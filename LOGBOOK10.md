# LOGBOOK 10
## Tasks

### Task 1
In this first task, the goal is to introduce a simple payload in the description field inside the profile page. There is a vulnerability in this page, since it allows for a user to **input html code**.
By editing the profile description to `<script>alert("XSS");</script>`, we can see the following output:
![](https://cdn.discordapp.com/attachments/579367639478960253/930917457588273223/unknown.png)

### Task 2 
The second task is similar to the first one. The goal is to display an user's cookie by visiting another user's profile. This can be achieved by changing the payload used in the first task to `<script>alert(document.cookie);</script>`
![](https://media.discordapp.net/attachments/579367639478960253/930917457835720734/unknown.png?width=1321&height=676)

### Task 3
In the third task, we want to display cookies that belong to other users of the website. In order to accompleish this task, we want to inject a XSS payload (in the description field), that forces other users to send a GET request to a specific location that the attacker is listening to. This request is made when the victim tries to load an image. 
The attacker gains control of the victim's cookie, as we can see with the log of the connections (via `netcat` command).
![](https://media.discordapp.net/attachments/579367639478960253/930917458234204210/unknown.png?width=1335&height=676)

### Task 4
In this task, we will write a XSS worm that makes people add a user (Samy) as a friend automatically after visiting his profile page. The template code was given by the lab's report. All we need to do is is find the correct URL that adds Samy as a friend. This can be accomplished with the `HTTP Header Live`  add-on. By clicking the **`Add friend`** button on Samy's profile while the add-on window is open, we can see the request.
With this information recently acquired, we need to set the `sendurl` variable with the `GET` request to be sent.
![](https://media.discordapp.net/attachments/579367639478960253/930917458552954910/unknown.png?width=1321&height=676)

**Question 1**: The lines 1 and 2 are parts of the request that contain duplicated information about the tokens.
**Question 2**: When we change to **text mode**, the injection is no longer possible. This happens due to the use html tags sanitizer, not allowing html code to execute.

# CTF 10

## Challenge 1 

In this challenge, we were given a simple web application with a html page that asks the user for an input. This input is then stored until an administrator reads the request and marks it as read. By exploring the platform, we noticed the **XSS vulnerability**. With this in mind, and knowing that the admin has access to the button that gives us the flag, we need to inject a payload that clicks that button:
`<script>document.querySelector("#giveflag").click()</script>`
After injecting the payload and waiting for a while, we managed to extract the flag `flag{a599e798522931388ba266db3d226db0}`.
![](![](https://cdn.discordapp.com/attachments/579367639478960253/931356429355466802/unknown.png))

  ## Challenge 2
In this second challenge, the idea is to run a **buffer overflow** attack on a service. We could easily identify this vulnerability due to the use of the `gets` function (that does't have any boundary limitations). However, the program has **PIE** (_Position Independent Executable_) enabled, which means that, as the name suggests, its code is placed somewhere in memory for execution regardless of its absolute address.
With this in mind we can start to build our payload. The first step is to know the offset between the buffer and the frame pointer (`$ebp`).
![](https://media.discordapp.net/attachments/579367639478960253/931372676935729214/Screenshot_from_2022-01-13_21-20-11.png?width=1321&height=676)

The distance between the buffer and the frame pointer is 104. Since the buffer's address is printed on the console, we need to fetch it to calculate the frame pointer address.
The next thing we need is a shellcode that opens a shell. 
Finally, we construct the payload overwriting the return address. 
![](https://media.discordapp.net/attachments/579367639478960253/931372676600197180/Screenshot_from_2022-01-13_21-16-57.png?width=1321&height=676)

By executing this script, we are able to open a shell on the server and log the output of the *flag.txt* file.
![](https://media.discordapp.net/attachments/579367639478960253/931372677229318144/Screenshot_from_2022-01-13_21-20-31.png?width=1321&height=676)