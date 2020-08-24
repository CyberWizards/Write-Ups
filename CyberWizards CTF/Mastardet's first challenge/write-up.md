# mastardet's first challenge
 
#### Big thanks for [@mastardet](https://www.youtube.com/channel/UC66idcoWzeZ_AsTnYr3L37g/featured) for the creating the challenge, a great guy with a lot of experince and a good mentor.

#### Category: basic binary + basic linux
 
#### Download the challenge: [masterdet_challenge](https://drive.google.com/file/d/1hLfEf8c-VrA-j7meXcO4Lzi_u4Pk2BUP/view)
 
So let's begin.
Choose the file that fits your operating system.

Let's inspect the file.
![image](https://user-images.githubusercontent.com/69927215/90821653-eb640e00-e33b-11ea-878c-66ab52452d9e.png)

We can see that the file is executable so let's try to execute it.
So chmod +x challege_linux and run it!
![image](https://user-images.githubusercontent.com/69927215/90821787-21a18d80-e33c-11ea-822b-dc12d13b29e0.png)

We are getting a password prompt for root@cyberwizards
I tried throwing a few random passowords like: root, toor, password, etc. And it did not work.

Next I tried looking at the binaries as it's a binary challenge.

For that I used Binary.Ninja , an easy to use GUI.

![image](https://user-images.githubusercontent.com/69927215/90822121-a2608980-e33c-11ea-9ee9-206005b3ae81.png)

There are a lot of lines in it and it is almost impossible to get anything from that as it's written in golang.

I used a hint that masterdet gave us.
`HINT 1: try searching strings with the hostname of the machine, in the binary`

So let's run strings on the binary with the host name which is `cyberwizards`

![image](https://user-images.githubusercontent.com/69927215/90823013-f5870c00-e33d-11ea-91eb-97a5e9c94f44.png)

and there we have it! Let's connect.

![image](https://user-images.githubusercontent.com/69927215/90823135-28c99b00-e33e-11ea-9584-32f06f6a66b1.png)

So let's try and see what we have, oh cool the flag is right here lets cat it.
As you can see we can't cat it and it's a restricted shell, which means there are very few commands that work.

![image](https://user-images.githubusercontent.com/69927215/90823293-59a9d000-e33e-11ea-81ac-aa05604e3139.png)

After we run help we can see that we have few commands available.
Let's try running export.

![image](https://user-images.githubusercontent.com/69927215/90823422-8958d800-e33e-11ea-9f12-d1bb1cf8cc99.png)

We could try to use it.

![image](https://user-images.githubusercontent.com/69927215/90823669-ea80ab80-e33e-11ea-8bc0-48a6bf66c711.png)

Doesn't look like we can use it.

let's try running reboot, it might restart it and exit the restricted mode.

![image](https://user-images.githubusercontent.com/69927215/90823752-0dab5b00-e33f-11ea-8d82-346c527176c8.png)

A rabbit hole.

Let's try running the history command.

![image](https://user-images.githubusercontent.com/69927215/90823814-2451b200-e33f-11ea-8ea9-ede8025fb4bb.png)

Hmm.. I am sure I ran more commands.

Here we have our second hint `We are nothing without history, And linux is nothing without environment variables `

After googling I found that history uses an enviroment variable `HISTFILE`
So we can try setting the history variable to the flag.txt and it might show it to us!, Let's get into work.

tried to run HISTFILE=/root/flag.txt and did not get any error so it ran successfully!.
So let's run history!, same as before...

![image](https://user-images.githubusercontent.com/69927215/90824330-ef922a80-e33f-11ea-9d39-4f14360dcf94.png)

so after a bit more googling I found out that we need to refrash the hisotry by doing `hisotry -r`
And there it is!

![image](https://user-images.githubusercontent.com/69927215/90824529-3b44d400-e340-11ea-9db5-ca1cc59f24f1.png)

##### Big thanks again for [@mastardet](https://www.youtube.com/channel/UC66idcoWzeZ_AsTnYr3L37g/featured) for creating this challenge and you should definitely check out his youtube channel for more!
##### Thanks for reading! by -MazGan


