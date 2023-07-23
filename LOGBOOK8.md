# Logbook 8

## Task 1

This first task sets up the environment, using `docker`.  At this point, we were able to use the 
**`mysql`** client to interact with the previously created `sqllab_users` dataset.
The final step was to print out the information available about the user **Alice**.
![](https://cdn.discordapp.com/attachments/579367639478960253/926534145545478164/unknown.png)

## Task 2

### Task 2.1
In order to login as administrator, we need to make sure that the given username is `admin`. Furthermore, we can skip the password check due to incorrect sanitization on the username.
The given input to complete the attack is **`admin;#`** in the username field.
As a result, we login to the administration area with information about all users.

![](https://media.discordapp.net/attachments/579367639478960253/926550724110860328/unknown.png?width=967&height=414)

### Task 2.2
The goal of this task is to repeat the attack from the **task 2.1**, but using the **curl** command. Therefore, we will need to run `curl "http://www.seed-server.com/unsafe_home.php?username=admin%27%3B%23&Password="`. The response will come in HTML format.

![](https://cdn.discordapp.com/attachments/579367639478960253/926550746961432666/unknown.png)


### Task 2.3

In this task, we would execute 2 different *`sql statements`* (separated by `;`). However, by analyzing the code from `unsage_home.php`, we can conclude that this attack will not be successful due to the use of the `query()` function. This method does not allow 2 queries to be run in the database.

## Task 3

### Task 3.1
In this task, we will modify the salary of the user **Alice**, in the *Edit profile* page. 
Since the fields are not correctly sanitized, we can use the payload `Alice', salary=50000;#` to change Alice's salary to 50000.
![](https://cdn.discordapp.com/attachments/579367639478960253/926558979847516180/unknown.png)


### Task 3.2

In this task, we willl change the salary of a user (Boby) while being logged in as Alice. The attack is pretty similar to the previous (*Task 3.1*). However, we need to specify that the **UPDATE** is set on the user Boby. Therefore, the payload used is:
`Boby,salary=1 WHERE name='Boby';#`

To check wether the attack was successful or not, we login as admin and look at the users informations.
![](https://cdn.discordapp.com/attachments/579367639478960253/926550886292009051/unknown.png)

As we can see, Boby's salary is set to 1.


# CTF 8

## Challenge 1

In this first challenge, we want to login as admin to the web server located at `http://ctf-fsi.fe.up.pt:5003`.
We know beforehand that the fields don't have correct sanitization. Therefore, we can use a `SQL Injection` attack.
 ![](https://cdn.discordapp.com/attachments/579367639478960253/926569019862044722/unknown.png)
 The payload used makes sure we login as admin and skips password verification.
![](https://cdn.discordapp.com/attachments/579367639478960253/926569293355843584/unknown.png)


## Challenge 2

In this second challenge, we were given a web application that pings hosts, using `php` **`system()`** function. With this in mind, we know that the `ping` command is being used in the ` system()` method. However, we can use the same call to execute multiple system calls (separated by `;`). Moreover, we can display the content of the `flag.txt` file when the ping results are displayed.
![](https://cdn.discordapp.com/attachments/579367639478960253/926571418341228634/unknown.png)

![](https://cdn.discordapp.com/attachments/579367639478960253/926571362498265208/unknown.png)

As we can see from the images above, the exploit works and we are able to display the flag on the output.