# LOGBOOK 6
### Task 1
In the first task, we needed to crash the crash the program by exploring a format string vulnerability in `format.c` file. The program allowed a user to control the format string passed to the **`printf()`** function. 
In order to crash the program, we could inject a payload with `"%n"` specifiers. This specifier loads the value with the number of characters pointed by `printf` before its occurrence. Since we are not specifying any digits before **`%n`**, it will look for all the elements in the stack, which will cause the program to crash.
![](https://media.discordapp.net/attachments/579367639478960253/914297375449821224/unknown.png)
![](https://media.discordapp.net/attachments/579367639478960253/914297441459769354/unknown.png)

## Task 2

This task was divided in 2 *subtasks*:
 - The first subtask, **task 2.A**, we had to use the `%x` specifier to print out the first few bytes of our input. In order to easily identify the right bytes on the stack leak, we wrote **`'AAAA'`** in the beggining of the payload. The remaining content was filled with `%x` to leak the memory.
 We started by printing the first 300 stack registers (from the `target` address).
![](https://cdn.discordapp.com/attachments/579367639478960253/914512666058436658/unknown.png)
 In this image, we can see that after a few registers, the stack repeats itself with the same bytes (`0x20`, `0x78`, `0x25`). These bytes are hexadecimal *ascii* codes for `%x`, which means that we found our payload in the stack!
 As expected, the first 4 bytes are on the beggining of the pattern (`0x41414141`), which is the hexadecimal bytes for **`'AAAA'`**. From here, all we needed to do was count the amount of registers between the `target` and the beggining of the payload by lowering the amount of `%x` in the string to be sent. This distance was equal to **64**.
 
 - In the **Task 2.B**, our goal was to print out the content of the secret message, whose adddress was known from the server's output. Since we already knew the location of the payload on the stack, all we needed to do was place the message's address (`0x080b4008`) on the beginning of the payload and consume the remaining content of the stack with `%x` until we reached the desired location. To get the content of the address and, therefore print the message, we placed a `%s` on the location we knew the address was going to be placed: 
 `content =  bytearray(b"%x  "  *  65  +  b"%s")`
 `content[0:4]  =  (0x080b4008).to_bytes(4,byteorder='little')`

## Task 3

### Task 3.A
The goal of this sub-task was to modify the **target** value. In order to do that, we applied the same logic that we applied in **Task2**: writing the address of the variable that we are working with (`0x080e5068`) and consuming the stack registers until we reach the place where we know the address will be put at. The only difference is that, this time, we actually want to change the content of the adress instead of reading it. To accomplish this we can use the `%n` specifier, that loads into the stack the number of variables printed by **`printf()`**:
`content =  bytearray(b"%x  "  *  65  +  b"%n")`
![](https://cdn.discordapp.com/attachments/579367639478960253/914557555123060797/unknown.png)

### Task 3.B
In this sub-task, we needed to modify the `target` value to `0x5000`. In order to accomplish this, we needed to consume the first 64 stack addresses and then use `%n` to write to the *target's* address (similarly to the **Task 3.A**). However, in order to set the value to 0x5000, we had to make sure that the value given by `%n` was the one desired. In order to do that, the number of characters printed by **`printf()`** was 20480. The first 64 stack registers resulted in 236 characters printed out. The remaining ones were taken care of with `b"%20244x%n"`.
![](https://media.discordapp.net/attachments/579367639478960253/914573375609909248/unknown.png)

# CTF 6

## Challenge 1

In this CTF challenge, we were given a `program` whose source code was available in the file `main.c` to analyse and explore possible vulnerabilities. The first step, as suggested in the instructions, was to run the **`checksec`** command to know the active protections for the program. 
![](https://media.discordapp.net/attachments/579367639478960253/914892116348514324/ctf6-checksec.png)
From this output, we can see that the program has *canary* protection, which makes it harder to execute buffer overflow attacks. By reading the source code, we could see that there is a **`printf()`** call where the format string is controlled by the user input: this means that we can use this **format string vulnerability** to print the content of the flag (since it is a global variable and the addresses are static).
To accomplish this task, we first should discover where the program would start printing some data from the stack (**memory leak**) and find where our input would be placed. To do that we passed as input `AAAA` followed by the maximum number of `%x`. The output of this call showed our input followed by `41414141` (which means that the input was placed in the beginning of the stack). 
With this in mind we were ready to lauch the attack: we put the address (`0x804c060`) of the flag's global variable in the input and then used `%s` to get its value.
![](https://media.discordapp.net/attachments/579367639478960253/914892115937468426/ctf6-c1.png?width=1335&height=676)

## Challenge 2

In this second challenge, we were given a program with format string vulnerability that could be used to modify the value of the **`key`** variable. This way, if we were able to change the value to `0xbeef`, we would open a *root* shell in the vulnerable server. 
In order to lauch this attack, we would pass as input to the server a dummy address (`0x01010101`) to be read by `%x`, followed by the key's address (`0x804c034`), where we want to write into and the correct specifier (`%48871x%n`).
![](https://media.discordapp.net/attachments/579367639478960253/914921167893180446/unknown.png?width=1335&height=676)
In this image, we can see the exploit used as well as the output from the server: a root shell used to get the content of the **`flag.txt`** file. 