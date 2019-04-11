## Introduction

In this lab, you are going to attempt to work through the classic hacker
e-zine article, "Smashing the Stack for Fun and Profit" which is
available here:
<https://cs.maryvillecollege.edu/wiki/Smashing_the_Stack>

This material is just a tad out of date, and so I have set up an
outdated version of RedHat Linux for you to play with.  

As we stated in class, you could use this new found ability to exploit
buggy code for good or evil. It is my sincere hope that you find the
middle ground and find some sort of general enjoyment out of this as
breaking software can be a lot of fun. Barring that, at least find
someone you can mess with. Pranks are fun too\!

## Gaining Access to 1998
The real trick to making all the things in the article work is to use
an older linux system.  With some adaptations, these things can be
done on modern systems, however those modifications have proven to be
a little bit too difficult to pull off in a short amount of time.  TO
that end, we will be using a version of linux from the decade of the
article's publication.  To access this server, do the following:

1. If you have not already done so, signup for a shell account at
   <https://cs.maryvillecollege.edu/signup/> You can also use your SMC
   account if you prefer.
2. Using ssh/smc, login to cs.maryvillecollege.edu.  In SMC you will
   want to open a terminal.
3. To access the redhat virtual machine, type the following:

   telnet localhost 2323

Your username is the same as the first part of your maryvillecollege
email address in all lowercase letters.  For instance, my username is
robert.lowe.  Your password is changeme.  When you log in, please
change your password by typing:

  passwd

The redhat system has a more limited selection of text editors than
the main system. You will have to use either vi, emacs, joe, or jed.
You may want to do a little websearch to work out how to use these
editors.


## Read Along\!
For this lab, I want you to read the article and try your hand
implementing these attacks.

As you come to each example, type in the code that is provided in the
the wiki page. (You will probably want to keep it open and handy.) To
get the most benefit, type the code, don't copy and paste it. 

Just leave your code in place on the redhat machine and I will grade
it from there.

## Further Reading

If you enjoyed this lab, why not check out some of the sources I
"irresponsibly" referenced in the wiki page with the article? Even
better, why not find some others. (For instance, the hak5 youtube
channel or website <http://hak5.org>).

Messing around with deep low level stuff can be a lot of fun. It's only
illegal if you do it to someone else's system without their permission.
Doing this will make you a stronger programmer, and during the
coming apocalypse, it will help you become a formidable opponent to
those who would do you harm.

Ok, so maybe it will just make you a stronger coder, but a man can
dream\!
