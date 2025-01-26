The Operating System of this machine is Ubuntu 20.04.

Ubuntu is a fairly simple linux distribution, meant mostly for beginners. You can run mostly any operation using the interface as in any other computer. However, genomic data requires a RAM and storage resources not available in most desktop computers. You usually need to work in a server, where there is no interface. Hence, it is important to know how to work in a linux command-line environment.
This is a very basic tutorial, meant to know the most elemental functions. Just enough to work, don't be afraid. If you want to learn more, there are plenty of tutorials out there.

Open the "Terminal"

######################
## BASIC VOCABULARY ##
######################

- Directory: it's a folder

- Path: that's the route you follow if you want to find an specific file or folder. E.g. /home/tecnicasmoleculares/Escritorio/README_BasicLinux.txt
This tutorial, called "README_BasicLinux.txt" is in the folder "Escritorio", whitin "tecnicasmoleculares", within "home".

- HOME: this is your basic location. Everytime you open a terminal window you are here. This is your basic location. Everything you do will be inside this folder. The path to HOME is: /home/tecnicasmoleculares/
If you don't want to type that path, there are two symbols that you can use and mean the same: $HOME and ~


###################
## Moving around ##
###################

As I said, if you have just opened the terminal you will be HOME, but: what if I have been working, moving around folders and don't know where I am anymore?

> pwd

Print the path to your current location. Of course, we are noe HOME and the path is simply /home/tecnicasmoleculares/
This command will let you know where you are at all times.

But what if I want to move to another folder...

> cd

Move to another directory. This is how you change your location:

> cd Escritorio
> pwd

As you can see, you have moved from "tecnicasmoleculares" to "Escritorio", and the path has changed accordingly: /home/tecnicasmoleculares/Escritorio/

> cd Data
> pwd

Again, you have moved from "Escritorio" to "Data" and your new path is "/home/tecnicasmoleculares/Escritorio/Data)

As you can see, your path is growing because you are moving to a folder, within another folder, within... But sometimes you want to move back to a previous folder:

> cd ../
> pwd

The "../" symbol means "the previous folder in the path". In this case it is "Escritorio" as you see in the path: /home/tecnicasmoleculares/Escritorio
But what if I want to move to the HOME folder? There are 5 options, choose one:

> cd
> cd ../				# Relative path
> cd ~					# Absolute path
> cd $HOME				# Absolute path
> cd /home/tecnicasmoleculares	# Absolute path

Here we see the difference between an "absolute path" and a "relative path".
- Absolute path: you write the full path, from the first folder to the one you want to visit. It doesn't matter where you are, it always works.
- Relative path: this is how you get to the folder you want to visit from where you are. In this case just go one folder back, but it changes everytime. It depends on where you are.
Which one is better?


####################################
## Working with files and folders ##
####################################

We are now in our HOME directory. Let's move to the "Escritorio". What files can we find here?

> cd Escritorio
> ls

As you can see, there are two names in blue, and two in white. The blue names are the different folders stored here, while in white you see the files. In this case, .txt files.
Let's create a folder where to practice some new commands:

> mkdir Test_linux
> ls

You can see now there are three folders, inluding the one we have just created. Move in there

> cd Test_linux
> pwd
> ls

You see now yor new path is "/home/tecnicasmoleculares/Escritorio/Test_linux", which is an empty folder. Let's create a new file:

> touch Nice.txt
> ls

Ok, the new file is there. How do we edit it? There are several text editors in linux (emacs, vim, nano...). Here we'll use nano:

> nano Nice.txt

This command opens the file, and now you can write whatever you want.

> [Elevator music] Waiting until you write a couple of sentences...

Let's save this file:

> Windows users: Control X and follow the instructions
> Mac users: Right command X and follow the instructions

We have now edited and saved this files. What if we want to see the content of this file? There are several options:

> less Nice.txt	# It opens a "only read" version (q to exit)
> cat Nice.txt	# It prints the content of the file in the terminal
> head Nice.txt	# It prints only the 10 first lines
> tail Nice.txt	# It prints only the 10 last lines

Can we make a copy of this file? The estructure is easy: the first item is the file you want to copy, and the second the name of the new file

> ls
> cp Nice.txt Nice_2.txt
> ls

If you want to copy this file to a new folder, you need to write the path. Here, we will copy this file to "Escritorio"

> pwd
> cp Nice.txt /home/tecnicasmoleculares/Escritorio/Nice_3.tx
> ls
> ls ../	# Remember that "../" means the previous folder

I don't want "Nice_3.txt" in the "Escritorio", let's move it back to our "Test_linux/" folder

> pwd
> mv ../Nice_3.txt ./		# New symbol! "./" means "This folder"
> ls

Yeah it's cool, the three Nice files are here, but now the original "Nice.txt" is the last file, can I rename it as "Nice_1.txt"?

> ls
> mv Nice.txt ./Nice_1.txt
> ls

As you can see, it is as easy as "move it here, but with a different name"
It is silly to have three copies of the same file, let's remove "Nice_2.txt" and "Nice_3.txt". There are two alternatives:

> ls
> rm Nice_2.txt Nice_3.txt
> ls

Be careful when removing files. They don't go to the bin: you remove them forever.

And that was basically it. Let's move to the "Escritorio" folder, and remove there the "Test_linux" folder (that contains the "Nice_1.txt" file)

> cd ../
> ls
> rm Test_linux

Hmmmmmm, it didn't work. That's because "Test_linux" is a directory, not a file. To remove a directory you need to type "rm -r"

> ls
> rm -r Test_linux
> ls

Ok, we finished! Of course there are many other things you can do, but now everything you could do using the interface, you can do it in the command-line. That's cool.

If you want to learn more, you can follow this tutorial: http://swcarpentry.github.io/shell-novice/
