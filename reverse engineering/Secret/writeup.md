# Secret

## Challenge Description
> Trivalent works in a hackers cave. Can you get access?

* **Creator:** TrivalenT
* **Category:** Reverse Engineering
* **Files:** [s3cr3t.pyc]()

## Walkthrough

Alright, so we are given a **python bytecode** `(.pyc)` file. 
> Python bytecode is the intermediate file created by the python interpreter that so that it can be run by the **Python Virtual Information (PVM)**
> You can read more about this [here](https://opensource.com/article/18/4/introduction-python-bytecode)

So, we can *decompile* this file which is convert this file to a `.py` using a tool called **uncompyle6**.
> YOu can get this tool from [Pypi: uncompyle6](https://pypi.org/project/uncompyle6/)

Ok, so let's get the `.py` file to take a look at the source code.

[decompiled]()

I am going to use `nvim` to read the source. Inside the source, we mainly find **three functions**: `kraitrot`, `cobraverse` and `vipershift`. 

[functions]()

And then there is another interesting thing is the the **if-statement** and inside the **if-statement**, the **hex values** which are being compared to input we give after making it go through the functions mentioned above. And then the print statement below it says **"Congrats for the flag anyways"**.

[if-statement]()

Hmmm........ So the hex must be the flag, just in a different form, or you can say *encrypted form* so to get it, we need to reverse the three **functions**. Ok then mates, let's begin...

So, this is how I imagine it, think of the flag to be a door and these functions are the three locks on the door, we just need to open them one by one.
So here is the statement encrypting the flag:
```py
vipershift(cobraverse(kraitrot(s3cr3t, 15)))
```

Alright, so according to this, the first lock that we should be opening is the `vipershift()` one as it is the outermost function.
Ok then, let's take a look at the function line by line:
* `def vipershift(s):` --> Simply defines the function and takes `s` as an argument. Now let's dive into the function:
    * `g = ''` --> Creates an empty string and stores it in `g`
    * `for i in range(len(s)):` --> Starts a for loop and iterates from 0 to the length of the string. Therefore, we can say that the variable `i` iterates over the indexes of the characters in `s`. Now, let's take a look at what's going on inside the for loop
        * `a = hex(ord(s[i]) + 20)[2:]` --> This is the most interesting statement I would say in the function. We start from inside: 
            * so it first takes the current character it has using `s[i]` from `s`
            * then returns the **unicode point value** of the current character using the `ord()` function
            * adds `20` to the **unicode point value** of the character
            * and then uses the `hex()` function on that number to convert it to *hexadecimal*
            * and at last, removes the inital **'0x'** at the beginning of the hexadecimal number using `[2:]`

Alright, now we know what the function is doing and I am sure we all realize, that we just need to find a way to reverse `a = hex(ord(s[i]) + 20)[2:]` and that should itself lead us to reversing the function. So, to reverse the statement, we start backwards based on how the statement was working according to our analysis. And is the *psuedocode* for this:
1. Take a hex as a string input
2. Iterate over the string such that we are able to get all the hexadecimal numbers individually, that is, iterating over it in chunks of two
3. Take the hex number and convert it back to *decimal*
4. Subtract `20` from the *decimal*
5. And then convert this *decimal* we get back to its **Unicode character**. This can be done in python using the `chr()` function that does the complete opposite of the `ord()` function
6. Finally, we append it to a string

Ok, so let's follow these instructions and create our `reverse_vipershift()` function:
```py
def reverse_vipershift(string):
    decrypted = ''
    for i in range(0, len(string), 2):
        decrypted += chr(int('0x' + string[i] + string[i+1], 16) - 20)

    return decrypted
```

OK then, now let's run the `reverse_vipershift()` on the hex values we have and see what we get

```py
def reverse_vipershift(string):
    decrypted = ''
    for i in range(0, len(string), 2):
        decrypted += chr(int('0x' + string[i] + string[i+1], 16) - 20)

    return decrypted


enc_flag = '867387738d7c8284688f454745737c4488894784884491575a807a738787'
print("Running reverse_vipershift(enc_flag): " + reverse_vipershift(enc_flag))
```
[reverse_vipershift]()

Alright!! Although its still jibrish but we can still say that it has started to look like a flag now!

Well then, let's move towards opening the next lock which is the `cobraverse()` function. So without wasting time, let's start analyzing the function:
* `def cobraverse(s):` --> Simply defines the function and takes `s` as an argument. Now let's dive into the function:
    * `g = ''` --> Creates an empty string and stores it in `g`
    * `g += s[1::2]` --> So, it starts at the index of `1` and goes till the end of the `s` with a step of `2`. So what this does, in simple words, is take all the characters at the **odd indexes** in `s`  and adds it to `g`
    * `g += s[0::2]` --> This function does the same thing as the above statement, just instead of **odd** it takes **even indexes** in `s` and add it to `g`
    * `return g` --> Returns the value of `g`

So here is the pseudocode we will get that will help us reverse this function:
1. Take a string as an input
2. Divide the string into two equal parts
3. Store both the parts in separate variables
4. Iterate over both the first and second part simultaneously and add their characters to a new string variable in such a way that in each loop, the character of the first part comes before its corresponding second part.
> To achieve that simultaneous iteration, I am going to use the `zip()` function in python but if you wish, you can even simply do this only using the `range()` function

5. Return the new string variable we get after this loop

And so, on the basis of this code, we can create the below given script:
```py
def reverse_cobraverse(string):
    first = string[int(len(string)/2):]
    second = string[:int(len(string)/2)]
    decrypted = ''
    for f,s in zip(first,second):
        decrypted += f + s

    return decrypted
```

Now let's run this script and open this lock

```py
def reverse_vipershift(string):
    decrypted = ''
    for i in range(0, len(string), 2):
        decrypted += chr(int('0x' + string[i] + string[i+1], 16) - 20)

    return decrypted


def reverse_cobraverse(cipher):
    first = cipher[int(len(cipher)/2):]
    second = cipher[:int(len(cipher)/2)]
    initial = ''
    for f,s in zip(first,second):
        initial += f + s

    return initial


enc_flag = '867387738d7c8284688f454745737c4488894784884491575a807a738787'
print("Running reverse_cobraverse(reverse_vipershift(enc_flag)): " + reverse_cobraverse(reverse_vipershift(enc_flag)))
```
[reverse_cobraverse]()

Ok, now this is great!! Well, obviously, you can alreay see the flag right in front of you! But anyway, we should still move forward to open this last lock.

Well, I will be pretty brief about this function as it is a fairly simple one

###### Analysis
> This function is simply creating two parts of the string we input based on a given index and then interchanging their positions

So, here is the *pseudocode* for our `reverse_kraitrot()` function:
#### Psuedocode
> We simply take a string and the index where it wsa divided and then create two parts and then interchange the position of those two parts

And here is how our `reverse_kraitrot()` function will look like:
```py
def reverse_kraitrot(cipher, d):
    second = cipher[0:d]
    first = cipher[d:]
    return first + second
```

Ok mates, let's run break open this final lock!!!
```py
def reverse_vipershift(string):
    decrypted = ''
    for i in range(0, len(string), 2):
        decrypted += chr(int('0x' + string[i] + string[i+1], 16) - 20)

    return decrypted


def reverse_cobra(cipher):
    first = cipher[int(len(cipher)/2):]
    second = cipher[:int(len(cipher)/2)]
    initial = ''
    for f,s in zip(first,second):
        initial += f + s

    return initial


def reverse_kraitrot(cipher, d):
    second = cipher[0:d]
    first = cipher[d:]
    return first + second


enc_flag = '867387738d7c8284688f454745737c4488894784884491575a807a738787'
print("Running reverse_kraitrot(reverse_cobra(reverse_vipershift(enc_flag))): " + reverse_kraitrot(reverse_cobra(reverse_vipershift(enc_flag)), 15))
```
[reverse_kraitrot]()

And voila!!! There is our flag!!!

Really thank you for taking out time and reading the writeup :)
