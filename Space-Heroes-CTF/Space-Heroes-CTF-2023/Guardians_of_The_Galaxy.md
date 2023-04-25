# Description
Only those who know the password can unlock the power of the system. But be warned, the password is as elusive as the Guardians themselves - hidden in the depths of Xandar or floating in the vastness of Knowhere.

Are you ready to take on the challenge and prove yourself a true Guardian? Remember, as Groot would say, 'I am Groot' is not the password you are looking for.

MD5 (Guardians_of_the_galaxy.bin) = ef91510f54b416c1feb233a23e7b9114

![image](https://user-images.githubusercontent.com/101048320/234409335-b4a896a1-6788-489d-a9ff-47f3383b1601.png)

# Files 

- Guardians_of_the_galaxy.bin

# Solution 
## Recon

- I started by analyzing the file with `file Guardians_of_the_galaxy.bin` : 
```
┌─[not1cyyy@0x45] - [~/Desktop/space-heroes-CTF/2023/Guardians_of_the_galaxy_FINISHED] - [mar. avril 25, 22:39]
└─[$] <> file Guardians_of_the_galaxy.bin 
Guardians_of_the_galaxy.bin: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=5161cfec17ba15aa0cec13ebe94013b8f50d4123, for GNU/Linux 3.2.0, not stripped

```

- There are few stuff that interest us, the file is an ELF executable binary, dynamically linked and not stripped

- Opening the binary in ghidra, and since it's not stripped, we can look at the symbol tree and we see this: 

![image](https://user-images.githubusercontent.com/101048320/234410813-51d4b508-1951-4926-a07b-0acdd3fb4f74.png)

- As intuitive as it is, I went ahead and examined the `main()` function to see what it had : 
```c

undefined8 main(void)

{
  int iVar1;
  int iVar2;
  size_t sVar3;
  char *__s1;
  char *__s1_00;
  char *in_RCX;
  char *pcVar4;
  int extraout_EDX;
  int extraout_EDX_00;
  int extraout_EDX_01;
  int extraout_EDX_02;
  ulong extraout_RDX;
  ulong uVar5;
  undefined8 *puVar6;
  char *pcVar7;
  int in_R8D;
  int in_R9D;
  byte local_f8 [64];
  undefined8 local_b8;
  undefined8 local_b0;
  undefined2 local_a8;
  undefined8 local_98;
  undefined8 local_90;
  undefined2 local_88;
  byte local_7a [9];
  byte local_71 [9];
  byte local_68 [9];
  byte abStack_5f [9];
  byte abStack_56 [10];
  undefined4 local_4c;
  char *local_48;
  char *local_40;
  char *local_38;
  int local_30;
  int local_2c;
  int local_28;
  int local_24;
  int local_20;
  int local_1c;
  int local_18;
  int local_14;
  int local_10;
  int local_c;
  
  logo();
  local_38 = "\nIf you are a true Guardian you know the password!!\npassword >>> ";
  slow_print("\nIf you are a true Guardian you know the password!!\npassword >>> ");
  local_40 = "Welcome aboard Guardian, here is your reward for saving our galaxy flag :  ";
  local_48 = 
  "Well!!! you are not a True Guardian.\nAs I said earlier only a true Guardian knows the password!! !\n \nStar Lord will lead you to the exit!!!!!!!!"
  ;
  __isoc99_scanf(&DAT_00102f48,local_68);
  local_c = 0;
  local_10 = 0;
  local_14 = 9;
  local_18 = 0x12;
  local_1c = 0;
  local_20 = 0;
  local_24 = 0;
  sVar3 = strlen((char *)local_68);
  if (sVar3 != 0x1b) {
    slow_print(local_48);
    return 0;
  }
  for (local_10 = 0; local_10 < 9; local_10 = local_10 + 1) {
    local_71[local_c] = local_68[local_10];
    local_c = local_c + 1;
  }
  local_28 = 0;
  local_2c = 0;
  local_98 = 0;
  local_90 = 0;
  local_88 = 0;
  local_b8 = 0;
  local_b0 = 0;
  local_a8 = 0;
  for (local_14 = 9; local_14 < 0x12; local_14 = local_14 + 1) {
    local_7a[local_28] = local_68[local_14];
    local_28 = local_28 + 1;
  }
  puVar6 = &local_98;
  atox(local_7a);
  uVar5 = extraout_RDX;
  for (local_2c = 0; local_2c < 9; local_2c = local_2c + 1) {
    uVar5 = (ulong)*(byte *)((long)&local_98 + (long)local_2c);
    *(byte *)((long)&local_b8 + (long)local_2c) = *(byte *)((long)&local_98 + (long)local_2c);
  }
  local_30 = 0;
  local_4c = 0;
  for (local_18 = 0x12; local_18 < 0x1b; local_18 = local_18 + 1) {
    uVar5 = (ulong)local_68[local_18];
    local_f8[local_30] = local_68[local_18];
    local_30 = local_30 + 1;
  }
  r(local_71,puVar6,(int)uVar5,in_RCX,in_R8D,in_R9D);
  r(local_f8,puVar6,extraout_EDX,in_RCX,in_R8D,in_R9D);
  r(local_71,puVar6,extraout_EDX_00,in_RCX,in_R8D,in_R9D);
  iVar1 = strncmp(__s1,"od_pbw1gu",9);
  if (iVar1 == 0) {
    local_1c = 1;
  }
  pcVar4 = "5F31735F6E30745F74";
  pcVar7 = "5F31735F6E30745F74";
  iVar2 = strncmp((char *)&local_b8,"5F31735F6E30745F74",0x12);
  iVar1 = extraout_EDX_01;
  if (iVar2 != 0) {
    pcVar4 = "5F31735F6E30745F74";
    pcVar7 = "5F31735F6E30745F74";
    iVar2 = strncmp((char *)&local_98,"5F31735F6E30745F74",0x12);
    iVar1 = extraout_EDX_02;
    if (iVar2 != 0) goto LAB_00101864;
  }
  local_20 = 1;
LAB_00101864:
  r(local_f8,pcVar7,iVar1,pcVar4,in_R8D,in_R9D);
  iVar1 = strncmp(__s1_00,"d/[h-i-py",9);
  if (iVar1 == 0) {
    local_24 = 1;
  }
  if (((local_1c == 1) && (local_20 == 1)) && (local_24 == 1)) {
    slow_print(local_40);
    printf("%s",local_68);
  }
  else {
    slow_print(local_48);
  }
  return 0;
}

```

- The program reads user input and stores it in the `local_68` variable, then checks if the input is 27 characters long

- Then it proceeds to split the input into 3 equal parts (9 bytes each),  call the `r()` function 3 times with different parameters, and then checks each part against a specific string

- Looking at the `r()` function, we see this code : 
```c

void r(void *param_1,void *param_2,int param_3,char *param_4,int param_5,int param_6)

{
  size_t __size;
  void *pvVar1;
  int local_c;
  
  __size = strlen((char *)param_1);
  pvVar1 = malloc(__size);
  for (local_c = 0; local_c < 9; local_c = local_c + 1) {
    *(char *)((long)pvVar1 + (long)local_c) = *(char *)((long)param_1 + (long)local_c) + -4;
  }
  return;
}

```
- The function seems to take unnecessary arguments, but overall it loops over a string, rotates each character by 4 and then returns the string

- So to get the flag, we need to write the reverse function of `r()` to decrypt the first and last 9 bytes of the password, for the last part, we just need to convert that string to ascii

# Execution

- I wrote a C code that does the necessary calculations : 
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

void r(void *string, void *ptr, int num, char *str, int val1, int val2);
void decode(void *string, void *ptr, int num, char *str, int val1, int val2);

int main()
{
    char original_string[] = "d/[h-i-py";
    char encoded_string[9];

    strcpy(encoded_string, original_string);

    // Decode the encoded string using the `decode` function

    char original_string2[] = "od_pbw1gu";
    char encoded_string2[9];

    strcpy(encoded_string2, original_string2);

    // Decode the encoded string using the `decode` function
    decode(encoded_string2, NULL, 0, NULL, 0, 0);

    char hex_string[] = "5F31735F6E30745F74";
    unsigned char byte_string[10];
    int i;

    // Convert the hexadecimal string to a byte string
    for (i = 0; i < strlen(hex_string); i += 2)
    {
        sscanf(&hex_string[i], "%2hhx", &byte_string[i / 2]);
    }

    // convert the byte string to a string
    char *string = (char *)byte_string;

    printf(string);
    decode(encoded_string, NULL, 0, NULL, 0, 0);

    return 0;
}

void r(void *string, void *ptr, int num, char *str, int val1, int val2)
{
    size_t size;
    void *ptr1;
    int count;

    size = strlen((char *)string);
    ptr1 = malloc(size);
    for (count = 0; count < size; count = count + 1)
    {
        *(char *)((long)ptr1 + (long)count) = *(char *)((long)string + (long)count) + -4;
    }
    strcpy((char *)string, (char *)ptr1);
    free(ptr1);
    printf("Encoded string: %s\n", (char *)string);
    return;
}

void decode(void *string, void *ptr, int num, char *str, int val1, int val2)
{
    size_t size;
    void *ptr1;
    int count;

    size = strlen((char *)string);
    ptr1 = malloc(size);
    for (count = 0; count < size; count = count + 1)
    {
        *(char *)((long)ptr1 + (long)count) = *(char *)((long)string + (long)count) + 4;
    }

    // Print the decoded string
    printf((char *)ptr1);

    free(ptr1);
    return;
}

``` 

- Running the script, we get this output : 
```
┌─[not1cyyy@0x45] - [~/Desktop/space-heroes-CTF/2023/Guardians_of_the_galaxy_FINISHED] - [mar. avril 25, 22:39]
└─[$] <> ./a.out 
shctf{5ky_1s_n0t_th3_l1m1t}shctf{5ky
```

# Flag

shctf{5ky_1s_n0t_th3_l1m1t}
