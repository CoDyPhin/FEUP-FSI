# LOGBOOK4
## Tasks 

 ### Task 1
 To print all environment variables, we use: `env` or `printenv`
 However, if we want to print a specific environment variable, we can use `env | grep <var_name>` or `printenv <var_name>`.
 To set an environment variable, we can use `export <var_name> = <value>`
 To unset any environment variable, we can use `unset <var_name>`.
  
   The export command is a built-in utility of Linux Bash shell. It is used to ensure the environment variables and functions to be passed to child processes. It does not affect the existing environment variable. Environment variables are set when we open a new shell session.

### Task 2

The code provided in myprintenv.c prints all environment variables for the parent process or the child process. After compiling both versions of the program and using the `diff` command to analyse the difference between both outputs, we can conclude that the environment variables are inherited from the parent process to the child process and for that reason the outputs are the same.

### Task 3

The execution of `execve("/usr/bin/env", argv, NULL);` resulted in an empty output. When we pass the external variable `environ` to the `execve` call, the program outputs the environment variables (`env` command).
In conclusion, `execve` recieves 3 arguments: `filename, argv ` and  `envp`. Argv is an array of argument strings passed to the new program. By convention, the first of these strings should contain the filename associated with the file being executed. `envp` is an array of strings, conventionally of the form key=value, which are passed as environment to the new program.
When we call `execve` with envp = NULL, the output is empty as the process runs without environment variables. However, if we pass `environ` as an external variable. 1.  The variable `environ` points to an array of pointers to strings called the "environment".

### Task 4
 The provided program outputs the environment variables as expected, since the `system` function calls `execl`, which calls `execve` passing the environment variables array (`environ` from **task 3**).

### Task 5
The goal of this task was to understand the behaviour of Set-UID programs.  It is a security tool that permits users to run certain programs with escalated privileges.
The **Step 1** introduced the program to print out all  the environment variables. Then, in **Step 2** we changed the executable file's ownership and made it a Set-UID program, allowing us to execute it with level of access that matches the user who owns the file (root).
Finally, in **step 3**, we tried to modify the following environment variables: `PATH`, `LD_LIBRARY_PATH` and a new one created by us (`TEST`). When executing the Set-UID program to print the variables, we observed that the `PATH` and `TEST` values changed, but the `LD_LIBRARY_PATH`'s value didn't. This variable informs the operative system about the locations of the libraries needed by the applications.  In that way, a person could get your application to load a version of a shared library that contains malicious code! That’s one reason why setuid/setgid executables do neglect that variable!

### Task 6
The main goal of this task was to run a malicious code by compiling the provided code. This happens since the `system` function is calling the `ls` by the relative path. Bearing this in mind, this is a dangerous behaviour, as the user can easily change the `PATH` environment variable to a path with a malicious ls executable. Therefore, by giving the program root ownership and making it a Set-UID program, it would run the malicious `ls` executable (that we created for this task).


## CTF  Week 4
The goal of the CTF challenge was to get admin access to a wordpress server. To accomplish that, we used a CVE with a known exploit. 
The **first step** was to obtain information by navigating the website about the server was running with a plugin called `WooCommerce Booster Plugin` (version 5.4.3) .  With this new information, we used the website `exploit-db` to find a CVE related to this plugin. By searching for `WooCommerce 5.4.3`, we found a CVE that allows for **Authentication Bypass** in that same plugin version. Having completed these tasks, we discovered the first flag (`flag{CVE-2021-34646}`).
In the next phase, we needed to use the exploit available on `exploit-db` and explore the vulnerability. We downloaded the (Python) script and executed it with the url `http://ctf-fsi.fe.up.pt:5001/wp-admin/edit.php` as argument. As result, we were able to login into the website as an administrator. The second flag was located in a message sent by the admin. 



