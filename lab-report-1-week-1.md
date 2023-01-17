# Tutorial: How to Log into a Course-specific Account on ieng6
## Installing VS Code
- Dowload VS Code [here](https://code.visualstudio.com/). After installing VS Code, you should see a window like the one below: 
![Image](https://github.com/LaveryXu/cse15l-lab-reports/blob/main/VS%20Code.png)
- Press Ctrl+` to open a terminal in VS Code.
- Tips: if you are stuck in the terminal, press **Ctrl+C** to opt out of any ongoing execution.

## Remotely Connecting
- [Look for your CSE 15L course-specific account](https://sdacs.ucsd.edu/~icc/index.php) and note down your account name, it should be in such format: **cs15lwi23zz@ieng6.ucsd.edu**. Reset your account password as [instructed](https://docs.google.com/document/d/1hs7CyQeh-MdUfM9uv99i8tqfneos6Y8bDU0uhn1wqho/edit).
- In the terminal you opened in VS Code, use command **ssh cs15lwi23zz@ieng6.ucsd.edu** (replace cs15lwi23zz with your CSE 15L account name) to log into UCSD's remote server. 
- If you get prompted to type yes/no, type yes and hit enter.
- You should be prompted "Password:". Enter the password your reset previously. Your terminal should look like below: ![Image](https://github.com/LaveryXu/cse15l-lab-reports/blob/main/remote%20log%20in.png)

## Trying Some Commands
- Now that you have logged into the school's server, you are working with the server instead of your local computer. Commands entered in the terminal will be executed on the server while you are logged in. Here's some basic command lines you can try on the server:

  - cd ~
  - cd
  - ls -lat
  - ls -a
  - ls <directory> where <directory> is /home/linux/ieng6/cs15lfa22/cs15lfa22abc, where the abc is one of the other group members’ username
  - cp /home/linux/ieng6/cs15lfa22/public/hello.txt ~/
  - cat /home/linux/ieng6/cs15lfa22/public/hello.txt
- To log out of the remote server, use:
  - Ctrl+D
  - **exit** command
- Here's some examples of running commands on the remote server:![Image](https://github.com/LaveryXu/cse15l-lab-reports/blob/main/commands.png)

## Moving Files with scp
- Create a file on your computer called WhereAmI.java and put the following contents into it:
  ```
  class WhereAmI {
    public static void main(String[] args) {
      System.out.println(System.getProperty("os.name"));
      System.out.println(System.getProperty("user.name"));
      System.out.println(System.getProperty("user.home"));
      System.out.println(System.getProperty("user.dir"));
    }
  }
  ```
- In the terminal, use `javac WhereAmI.java` and `java WhereAmI` to compile and run this file.
- Then, in the terminal **from the directory where you made this file**, run this command (as usually, using your username):
  ```
  scp WhereAmI.java cs15lfa22it@ieng6.ucsd.edu:~/
  ```
- You should be prompted for a password just like when you log in with ssh. Enter your password.
- log into ieng6 with ssh again, and use ls. You should see the file there in your home directory! Now you can run the program on the ieng6 computer using the same javac and java commands from before.
- After compiling and running, your should see below in your terminal:![Image](https://github.com/LaveryXu/cse15l-lab-reports/blob/main/scp%20(1).png)
- Compare the what the terminal prints when you run the file on your local client and the remote server. What are the differences? What do these differences imply about what `getProperty()` does?
- What we see here is one solution for running code remotely – we can copy files to a remote server with scp, then log in and run them there.
- Before you go on, do the following:
   - Have someone on your team start a timer
   - Make a change to WhereAmI.java and save the file
   - Copy the file to the remote server
   - Log into the remote server and run the file
   - Stop the timer
- How long did it take you? (Not everyone has to do this, but someone should.) Assume you’d have to do this process 100 times over the course of a PA. How long would you spend copying and running the file?

## Setting an SSH Key
- Here's a more convenient solution for running code remotely.
- We will use ssh keys to skip entering our password everytime.
- The idea behind ssh keys is that a program, called ssh-keygen, creates a pair of files called the public key and private key. You copy the public key to a particular location on the server, and the private key in a particular location on the client. Then, the ssh command can use the pair of files in place of your password. This is a common setup step in lots of work environments that involve code on a server.
-Here’s what you should run to set this up:
```
# on client (your computer)
$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/joe/.ssh/id_rsa): /Users/joe/.ssh/id_rsa
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /Users/joe/.ssh/id_rsa.
Your public key has been saved in /Users/joe/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:jZaZH6fI8E2I1D35hnvGeBePQ4ELOf2Ge+G0XknoXp0 joe@Joes-Mac-mini.local
The key's randomart image is:
+---[RSA 3072]----+
|                 |
|       . . + .   |
|      . . B o .  |
|     . . B * +.. |
|      o S = *.B. |
|       = = O.*.*+|
|        + * *.BE+|
|           +.+.o |
|             ..  |
+----[SHA256]-----+
```
- When given the prompt Enter file in which to save the key (/Users/joe/.ssh/id_rsa): press enter again to specify the default path and take note of it. In this case, the default path is /Users/joe/.ssh/id_rsa.
- If you’re on Windows, follow the extra ssh-add steps [here](https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_keymanagement#user-key-generation).
- **NOTE: If you're on Windows and is entering a path with space(s) in the directory names, doule quote the entire path.**
- This created two new files on your system; the private key (in a file id_rsa) and the public key (in a file id_rsa.pub), stored in the .ssh directory on your computer.
- Now we need to copy the public (not the private) key to the .ssh directory of your user account on the server.
```
# on client
```
$ ssh cs15lfa22zz@ieng6.ucsd.edu
<Enter Password>
```

# now on server
```
$ mkdir .ssh
$ <logout>
```
  
# back on client
$ scp /Users/joe/.ssh/id_rsa.pub cs15lfa22@ieng6.ucsd.edu:~/.ssh/authorized_keys
# You use your username and the path you saw in the command above
```
- Once you do this, you should be able to ssh or scp from this client to the server without entering your password, as shown below:![Image](https://github.com/LaveryXu/cse15l-lab-reports/blob/main/SSH%20(1).png)
- Repeat the timing experiment of editing and running WhereAmI.java now that you don’t have to use a password. How much time is saved per run?
## Optimizing Remote Running
- Use what you’ve learned to come up with the most pleasant process you can for making a local edit to WhereAmI.java, then copying it to the remote server and running it.
- Tips:
  - You can write a command in quotes at the end of an ssh command to directly run it on the remote server, then exit. For example, this command will log in and list the home directory on the remote server:
```
$ ssh cs15lfa22@ieng6.ucsd.edu "ls"
```
- After executing the command above, you should see something like this:![Image](https://github.com/LaveryXu/cse15l-lab-reports/blob/main/Optimization%20(1).png)
- You can use semicolons to run multiple commands on the same line in most terminals. For example, try:
```
$ cp WhereAmI.java OtherMain.java; javac OtherMain.java; java WhereAmI
```
- You can use the up-arrow on your keyboard to recall the last command that was run
- First try using just what we learned in this lab, and document the best process you came up with. Try to get the total time for a run after editing and saving to under 10 total keystrokes/mouse clicks, including all typing. A “keystroke” is pressing one key on your keyboard. For example, pressing the up arrow counts as one keystroke, and typing “java” counts as 4.
- Using the up-arrow to recall previous commands, we can keep our keystroke under 10. Below is an example:![Image](https://github.com/LaveryXu/cse15l-lab-reports/blob/main/Optimization%20(2).png)
  - After altering WhereAmI.java in the client, use the up arrow to recall `scp WhereAmI.java cs15lfa22it@ieng6.ucsd.edu:~\` (1 keystroke).
  - Use the up arrow to recall `ssh cs15lfa22it@ieng6.ucsd.edu javac WhereAmI.java` and hit enter (2 keystrokes).
  - Then, use the up arrow again to recall `ssh cs15lfa22it@ieng6.ucsd.edu java WhereAmI` and press enter (2 keystrokes). Now we've completed editing a file in our client, update the changes to the server and then run the file.
- If you have more time, brainstorm other ideas or search for other ways you might easily run remote code.

## Wrapup
- Discuss with your team – do you have any open questions about things you saw that you don’t understand? Write them down in your notes document or ask your tutor. Even if they don’t know, writing them down means we can come back to them later!
- If you didn’t get everything to work, that’s OK! Keep trying and make sure your tutor knows if you’re totally stuck getting something set up; we’ll be posting some office hours soon where you can come to get unstuck as well.
