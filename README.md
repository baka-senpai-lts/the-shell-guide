# The Shell Guide


## Contents
- [Preambula](#preambula)
- [So you wish to learn how to use your shell?](#so-you-wish-to-learn-how-to-use-your-shell)
- [Some basic commands you should know in any case](#some-basic-commands-you-should-know-in-any-case)
- [Some standard conventions](#some-standard-conventions)
- [Environment](#environment)
- [Quoting and escaping](#quoting-and-escaping)
- [Piping and redirecting](#piping-and-redirecting)
- [stdout, stderr and stdin](#stdout-stderr-and-stdin)
- [Scripting](#scripting)
  - [Now a bit on more complicated stuff](#now-a-bit-on-more-complicated-stuff)
    - [AND & OR](#and--or)
    - [if](#if)
    - [while](#while)
    - [for](#for)
    - [Functions and arguments](#functions-and-arguments)
- [Signals or HOW DO I EXIT](#signals-or-how-do-i-exit)
- [Jobs or how to not open 20 terminals for 20 tasks](#jobs-or-how-to-not-open-20-terminals-for-20-tasks)
- [Why it all doesn't make sense](#why-it-all-doesnt-make-sense)
- [And what do I do now?](#and-what-do-i-do-now)


## Preambula
Before reading this guide, remember the following:
- I expect you to be familiar with core UNIX concepts. I won't explain you how filesystem works
  or why programs have names.
- This isn't a regular sugarcoating session with oversimplifying basic concepts into saying
  "uh well, prompt always has username, host and current path", because let's be completely honest,
  *fuck it*.
- I won't be holding your hand through this guide, you are *highly encouraged* to do your own
  research to learn more useful stuff.
- This was never intended to be a complete guide on shell, I know for sure that there are many
  other themes that I didn't cover with it, but it gives you the ground to stay on later.
- I won't teach you commands, I will teach you how shell works so you can write your own.
- Never ask "How?", only ask "Why?"


## So you wish to learn how to use your shell?
First of all, forget whatever guides you were watching, these make no sense in most cases.
Now that you've cleaned up your mind, let me tell you the truth. It's all just text interpreted
by your shell into executing binary files with certain arguments and nothing more.

Let's take a look at the example:
```bash
ls -ali | grep 'Whatever'
```

Now one might ask, what all this means. Nothing essentially until you specify that it's bash or
any other *program* that uses the same syntax. But yes, this is indeed *bash* and here is what it all
means:
- `ls` - list files, part of coreutils.
- `-ali` - argument for `ls` to list all files (`-a`) in long format (`-l`) and print inodes (`-i`).
- `|` - feed next command with output of previous command, this is called a *pipe*.
- `grep` - find a pattern, might be anything, look `man grep` for details.
- `'Whatever'` - well, the pattern for `grep`.

And so what does it *really* mean? Bash just looks up in `$PATH`, which is an *environment variable*,
executables with the same names, giving them whatever you threw afterwards as arguments. You see?
No magic at all. If you want to see all the paths where `bash` or any other shell would look up
programs, just... Print it out, you should already be familiar with `echo`. And the full command will
be `echo "$PATH"`. And what do quotes mean? Just use whatever is inside as one argument. You see
where I'm pointing at? `"ls" "-ali"` is a *completely* valid command.

Also it's important to say, that you may as well call *functions* like this, they will be treated completely
the same. Look forward into *scripting* section and make a connection with your *\*rc* file.

Now you may ask "but how do I find what programs do and how to use them?". And here is a quick answer:
Essentially you have three ways:
- Use any help that comes in executable, *usually*, but not always, you can call `--help` or `-h` for
  shorthand to get help.
- Refer to manual or tldr pages if any are present, commands will be `man` or `tldr` respectively.
  Make sure that you have both on your system though. Or don't if you don't want them, I warned you.
- Refer to the online documentation for whatever you want to use.

So to sum up, in *any way* you *must* read the friendly manual *before* arguing on forums.


## Some basic commands you should know in any case
So there is a quick list for you, my impatient friend:
- `ls` - list files, defaults to current directory, which you can get by accessing `$PWD` or typing `pwd`,
  but you can set your own.
- `mv` - move from one location to another, will work with both directories and files.
- `cp` - copy, add `-r` if you are copying a directory, it means "recursive", so it will copy directory
  and everything inside. I hope you know what recursion means.
- `rm` - remove. May be replaced with `mv something /dev/null`, if you are desperate enough, but don't really
  try it until you want to remove that "something". If you need more information on this, take a look at `man rm`.
- `echo` - write. Literally, just write whatever you throw into it's arguments. For example, you can do
  `echo *` if you accidentally nuked your coreutils and still want to list contents of a directory.
- `cd` - change directory, you should already be familiar with file hierarchy in UNIX-like systems, so
  `cd ..` would take you up one level and `cd .` would do nothing. Also `cd` without arguments will
  bring you `$HOME` (remember how it's not our beloved `~`). Essentially just a glorified `$PWD` switcher
  with validation and some convenience. You should remember though that even `$PWD` isn't an actually
  trusted source of current working directory. And yeah, again, it's a file, you can find it in `/proc/self/cwd`.
  It is a *symlink* to current working directory. And technically nothing stops you from overwriting it...
- `cat` - concatenate and print file contents, just give it paths.
- `man` - read the friendly manual for whatever you ask it. For example, `man ls` will show you instructions
  on how to use `ls`. If you have any more questions, `man man`.

And... That's all the mandatory commands you must know. *Everything* else you will learn by yourself, just
remember to search on your own.


## Some standard conventions
First of all, we have an asterisk. Yup, that starry thing, that looks like `*`. Remember it, *usually* it means
wildcard. For example, `cat *` will concatenate all the files in current directory or `$PWD`. It's not limited
to this though, for example `*.jpg` will catch all the files ending with ".jpg" and `*/my_file.txt` will result
in path to every "my_file.txt" that lies in some directory. But important note, if the file or directory are
*hidden*, it won't. In that case use `.*` instead.

Now from last topic you might ask why the hell there is `.` in every directory. Well, because if there wasn't...
What would happen if there was a file called "ls" and you wanted to execute *it specifically*? One might guess
that typing a full path will do the job and would be correct, same as the person shouting `"$PWD/ls"`. But these
are a bit inconvenient, so we would just type `./ls`, just that simple, it only exists for you convenience as
many things out there.

Great, now once we have this stuff covered, let's see what is in history. Again, everything is a file and what is
your command history? Right, it's also a file, congratulations on succeeding with basic logic. It will *generally*
be something like "$HOME/.bash_history", but it *may* also not be there. Anyway, to access it, just type `history`,
it will work with *most* shells out there. If it doesn't, refer to your shell manual via `man` or any other documentation.
And in the same place you will find out how to configure it and get some more advanced usage, this will be your homework.


## Environment
So you saw me referring to some strange stuff with dollar sign before. These are variables. And yeah, there is
plenty of them, may not even try to remember all. Just remember this:
- If something starts with a "$" and it isn't in single quotes and isn't following the "\" symbol, it's a variable.
- To set some variable for your current shell session, use `export`, for example `export SOME_VARIABLE=some_value`
  will do exactly what it says and now for current session `$SOME_VARIABLE` will be `"some_value"`.
- If you want to take a look at what some variable contains, just use `echo $SOME_VARIABLE`.

This should be enough for you, but one last trick. In bash/zsh/sh and many other common shells you can set
variables for just one command like this:
```bash
MY_VARIABLE="something" ls
```

Here $MY_VARIABLE is set for just this one little ls and if it would read it (which it doesn't), it could affect
what it does. Remember that what program gets as environment variables has little to nothing to do with what you
can read inside of your shell. So following example won't work at all in *most* shells.:
```bash
MY_VARIABLE="something" echo "$MY_VARIABLE"
```

To make this actually work, you should use `export`:
```bash
export MY_VARIABLE="something"
echo "$MY_VARIABLE"
```

And now we are getting somewhere with this.


## Quoting and escaping
Imagine that you want to put two words as one argument, what will you? Keep in mind, that `command two words` will
mean that `two` and `words` are separate arguments, so to go around this we have three ways:
- Double quotes - simply put your argument in double quotes, you can also use variables and other stuff here, like
  "$SOME_VARIABLE" and so on.
- Single quotes - just put your argument in single quotes, it will be treated as one argument and you *cannot* put
  variables and anything else inside.
- Escaping - it's not about escaping to a fantasy world, it's about pure ignorance. When you are using `\` you are
  basically telling your shell to escape it. And it has two variations.
  - First it can make it interpret space or any other symbol literally without making `bash` or anything similar
    parse it.
  - Secondly, it can create completely new characters such as `\n` and `\t` for new line or tabulation accordingly.
    You should really read `man console_codes` if you want to learn more about it.


## Piping and redirecting
I won't cover it fully, because I'm too lazy to show you how to work with different output streams (stderr and
stdout to be exact), but I will show you some basic usage.

First of all, what is a pipe. Pipe is a simple blah-blah-blah, it just puts output of one command into input of other
command, that simple. So if you ever wanted to concatenate two files and filter only lines containing word "lol", you
can do `cat file1 file2 | grep "lol"` and here you go, congratulations, you've achieved your first completely useless
pipe, I am proud of you.

Now that we've convered pipe, let's talk about redirection. It just allows you to redirect output into files or
vice-versa. And you know that *everything* is a file, right? So redirecting from one stream to another stream is
also covered by this .And how do you use it? Just like this: `cat file > another_file`. This essentially just copies
contents of "file" into "another_file". And if you don't want to override existing file, use this: `cat file >> another_file`.
This will *append* stdout from your command to the file.
And if you want to do it vice-versa and put contents of a file into stdin for a program? We've got you covered,
there is `cat < file`. Literally the same thing, but swapped.


## stdout, stderr and stdin
I said that I'm too lazy for it, but fuck it. So there are exactly three main streams of data in UNIX-like environment.

First goes stdin under number of 0.
Second is stdout, it has number of 1.
And last one is stderr under 2.

What does it mean? Nothing until you are using it. Remember how I said everything is a file? Right, this too, you can
find them in `/proc/self/fd/` directory on Linux or many other UNIX-like OSes if you want to or even run something like
`echo "Hello World" > /proc/self/fd/1`. Not like anything would change, but you just redirected it *directly to stream*
of data. Also yes, you can indeed use some other processes' stream instead because there is no magical switch stopping
you from doing so. Also there is `/dev/fd` containing symlinks to same stuff. And yes, all these streams are just file
descriptors and nothing more. If you write to it, you are writing to stream, if you read from it, you are reading from
it, no magic under the hood and this is done by any program you are running.

That's all cool, but you may think that there must be a shortcut to do it and would be completely right. And it looks
like this: `ls -ali 1>&2`. Now let me explain. `>&` is used *specifically* to redirect from one stream to another.
So in this specific example all it does may be read as "redirect stdout to stderr". But if you want to, for example,
redirect stderr to file, you can use `2> file`. Pretty intuitive, right?


## Scripting
Now that you see that you can do basically anything with your shell it would be nice to put it in a file, wouldn't it?
And you can, just write down a list of commands in your shell and save it. But before you execute it, let me tell you
what shebang is. Might sound scary, but it all just comes down to executable that will interpret the text inside a file
and it's written like this: `#!/bin/bash`. And if you want python? Right, same thing `#!/bin/python`. But wait, python
isn't installed in `/bin` on your system? And that's why we don't use it like this in our scripts.

Most systems have `/usr/bin/env` utility, that gives you the right executable and is *usually* located exactly here.
It doesn't guarantee that it will run, but it boosts your chances of not fucking up on a new system with non-conventional
paths. To use it, simply call it and ask for a name, so for python it would be `/usr/bin/env python`. If right now you
execute this command in your shell, you will probably get into python shell... Or just error out because you have no
python installed, in which case I respect you. And for a simple script here is what it would look like:
```bash
#!/usr/bin/env bash

echo "Hello World"
```

You see how there is again no magic and just text that tells what to execute and how. Also before you save it into a file
and try to run it, remember that to execute file you need rights to, so pull of your `chmod +x random_script_from_internet.sh`.

Or just don't do it and run it directly using `bash another_random_script.sh`, both will work.
Another important thing for you. You probably saw how guides introduce you to files like `.bashrc`. And yes, these are
indeed scripts and yes, you can run any code here.


### Now a bit on more complicated stuff
Bash and other shell implemenations are full programming languages, just accept it. I won't cover it all, but I will show you most
important stuff that works *in general*. Most of it stands on returning codes, so we will cover it first.

Returning code is a value that program returns when it's finished. All that is important for you though is that when this value
isn't 0, you know something went wrong. To see this value for your last command, you can run `echo $?`.


#### AND & OR
Sometimes you want to execute a command only if previous command didn't return an error or the opposite, run only if there was an error.
But writing a whole if statement for this is a bit of overkill, so that's why we have `&&` and `||` operators. It's all dead simple,
`&&` means that next command will execute if previous one returned 0, `||` means that it will execute if previous one returned anything
other than 0.
And so `less myfile && echo "Opened a file successfully" || echo "An error occured"` will execute perfectly fine. If file exists, it will print out
"Opened a file successfully" and if it doesn't or there is some other error, it will echo "An error occured".


#### if
This one is incredibly simple, just few keywords: `if`, `then`, `elif`, `else` and `fi`.
What do these mean? Here is a quick breakdown:
- `if` - if this condition is true, do whatever stands after `then`.
- `then` - just accept that it exists, if you know C or any other language, interpret it as `{`.
- `else` - if condition[s] above are false, execute whatever is here.
- `elif` - else if, you should already understand this.
- `fi` - finish your beloved `if` statement.

An example for you:
```bash
if ls
then
    echo "Works as expected"
else
    echo "I'm genuinely curious how you made ls return anything other than 0"
fi
```


#### while
Again, straight up `while`, `do` and `done`. Works completely the same as if, but runs while command inside of `while`
returns zero. For an example make your own example, I believe in you. If you are stuck in your own personal hell made by `while`,
look into Signals or HOW DO I EXIT.


#### for
This one is actually interesting. It will iterate through whatever you ask it, the keywords are `for`, `do` and `done`.

Here is your example, not like with `while` loop:
```bash
for i in *
do
    echo "$i"
done
```
This piece of code will echo all the files in your directory.


#### Functions and arguments
There are many ways to write a function in `bash`, but I'll show you just one that I prefer. You can refer to other manuals
to choose yours.

Here is how you define a function:
```bash
function my_function {
    echo "Hello World"
}
```

Pretty easy, right? Right, now how do we address arguments we threw at it? Well, just use `$1` and `$2`, etc. as well as
`$@`. So here is a quick breakdown for you:
- `$0` - the actual first argument, the name of a function or script with which it was called.
- `$1` - second argument and the first thing that sane person would consider an argument, if you type `command argument1 argument2`,
  it will catch `argument1`.
- `$2` and everything after it accordingly - same as `$1`, but for the rest of arguments.
- `$@` - all arguments, as an array. If you want to put it all into a command, make sure to type it as `"$@"`, this way it will
  expand correctly without messing with quotes.
  
For an example write your own again, these are literally just variables. And remember that while it is suitable for functions,
it can also be used for scripts in the same way, so if you are writing `"$@"` outside of a function, you are referring to all
the arguments passed to your script. Otherwise a call like `command "arg 1" arg2 "arg 3"` will expand like `command arg 1 arg2 arg 3`
and you know where it leads us, we've now got 5 arguments instead of 3.


## Signals or HOW DO I EXIT
That's all great, but what will you do when you are running `tree /` and it takes half an hour, but you got bored
after 30 seconds of staring at it? Well, you can send signals to your programs and there are two main ways to do this.

First of all, you have the `kill` command. Don't be fooled by name, it doesn't just kill processes, but sends them a signal
to commit suicide, so it's all by mutual consent (well, unless it's SIGKILL, then it's a murder). But hey, signals aren't
meant to only kill and there are many other signals which you may see in `man 7 signal` or `kill -l`. Here are three most
common though:
- SIGTERM - tell process commit a suicide gracefully, recommended to use if you want to kill something, this gives it
  chance to react, tell kids to not miss it and clean up stuff after it.
- SIGINT - interrupt poor process, still allows it to end gracefully.
- SIGKILL - kill process immediately. And please don't until you are completely sure that it's the only way to go.

Secondly, there are key combinations these send signals to current active process in shell. I *highly recommend* you to investigate
further for your exact shell, as following *may not* work for you. These are just common variations.

First goes `^C`. Be not afraid, "C" is literally "C" on your keyboard and "^" is Ctrl, so just Ctrl-C to send a SIGINT, it will
effectively stop that annoying command that you ran for fun.
And second goes `^D`. It tells program that you are exiting. It doesn't send any signals at all, but it ends the stdin stream, which
is called EOF.
Why did I put it here then? Because otherwise you will keep terminating poor `cat` when all you had to do was end your own stream.

Also since SIGINT, SIGTERM and other stuff allows process to finish gracefully why wouldn't I teach you on how to catch these
signals? To use this in your bash script you will use `trap` command. For now it will be enough for you to know that as arguments
it takes a command and signal that you want to trap. For any more information see `man trap` or just google and while you are
doing this here is your example:
```bash
#!/usr/bin/env bash

trap 'echo "Caught SIGINT"' INT

sleep 9000
```


## Jobs or how to not open 20 terminals for 20 tasks
Let's be real for a second, no one likes a lot of windows sitting around for nothing. And neither do I. Therefore, `bash` and other
shells provide a way to run processes in background and manage them however you want. In total there are three main commands for this:
- `jobs` - lists all jobs you have.
- `fg` - runs whatever job you specify (or just the only one you have) in foreground. It means that you will be able to interact with it.
- `bg` - same as `fg`, but in background and you *cannot* interact with it.

But let's dive into some practice:
```bash
sleep 9000
```

And what now? Right, you are stuck here for 9000 seconds if you want this to finish. Well, luckily it doesn't mean that you are completely
out of control. There is a signal that "pauses" a process for you and it is called `SIGSTP`. You can either send it from kill or by
pressing ^Z (Ctrl-Z) to use it. And now what will happen? You will get a message that says that your process just got suspended. And now
you can run `jobs` to see how exactly it will look. In our case it will look like this:
```
[1]  + suspended  sleep 9000
```

Now what do we do? You can manipulate run this process however you want. For example, if you want to resume it in foreground, simply run
`fg %1` and it will continue executing. And if you want it in background, right, `bg %1`. If you have more jobs, just replace that 1 with
any jobspec you have (that number placed in square brackets). Congratulations, you can now run multiple jobs at once, wasn't that hard, right?
Also now you can do stuff like `fg %1 && shutdown` if suddenly compilation takes longer than expected and you don't want your PC to run for
an entire night.

Also there is a way to make your command run in background from the very start, just add `&` in the end like this: `sleep 9000 &`. You will
still be able to see it in `jobs` output, but now you won't even have to suspend it.


## Why it all doesn't make sense
You may find it useful, boring or whatever else, but let's be real. This guide essentially doesn't make any sense at all. And you
should know that. And I'm saying it because *EVERYTHING* is individual. Maybe you patched your bash to interpret `/` as your
home directory? Or you fucked around with file descriptors and now your stdin file is 1337? You get where I'm bringing you. You
must first understand *why* something works, not just how to do your thing. And once you get this right, there will be no such
thing as "impossible". Everything is possible if it complies with rules of nature. And it's *your* system and *your* rules.
Stay free as in freedom.


## And what do I do now?
Anything. Just remember that you can always google to find the right tool for your problem and read manual.
Once you get the hang of it, your workflow will probably move entirely to terminal.
