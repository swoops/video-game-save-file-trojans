# Video Game Save File Trojans

This article is to inform gamers of the dangers of loading save files found on
the Internet. To illustrate, below is a video of a custom-written save file
virus being downloaded from a shady website, added to a game save directory and
then loaded in-game. As you'll see, it's not until the malicious save file is
loaded that the virus is recognizable as such. At that point the virus is
already running and can start to speak for itself. 

Watch video on [vimeo](https://vimeo.com/784655708)
or [catbox](https://files.catbox.moe/0uvmn2.webm)

#### Please watch the video before continuing.

## How to Stay Safe

Avoid use of any file that has an untrustworthy or unknown origin. This goes
for save files, game mods, games, PDFs, GIFs — everything. If the origin is
suspect, loading, executing or even downloading the file can be dangerous.

Hypothetically, if someone were to email you `I found out how to save
<<<spoiler>>>, save file attached as proof!` it could be awfully tempting to
try out that save file. Instead, do some investigating before you download or
open it.

## Regarding Ren'Py

While the above video uses the Ren'Py tutorial, this general attack is by no
means restricted to the tutorial, nor to Ren’Py. For example, the 
[Twilight Hack](https://www.wiibrew.org/wiki/Twilight_Hack) was discovered 
back in 2008 and can still be used to homebrew a wii. 
The virus shown in the above video could also run on games like 
"[Doki Doki Literature Club](https://store.steampowered.com/app/698780/Doki_Doki_Literature_Club/)",
[Ren'Py Chess](https://r3dhummingbird.itch.io/renpy-chess-game) or even
"[Shadow The Hedgehog](https://shibaya.itch.io/shadow-the-hedgehog)" (a dating
simulator). _All games built with Ren’Py are affected._ That said, save file sharing is
extremely rare with Ren’Py games.

For those unfamiliar, Ren'Py is a visual novel game development platform. Save
file sharing doesn't often make much sense as a feature of visual novels (who
would skip pages when reading a book?). And to be clear, I have nothing bad to
say about Ren'Py itself. It is free, open source and very easy to use. Combined
with a kind and active community, it's probably the easiest way to get into
game development. As a result, there are Ren'Py games for just about every
interest (https://itch.io/games/tag-renpy). (I really do mean any interest, so
some discretion is advised.)

## How It Works

Ren'Py uses a save system that automatically works for every game. Whether
writing a chess game or a dating simulator, the save system will be handled by
Ren'Py in the same way. To create a universal save system that is both flexible
enough for any game and simple enough for beginner developers to work with,
"arbitrary object serialization" is necessary. In Python, the language Ren'Py
uses, this means "Python pickles".

Python pickles can be dangerous. If you "unpickle" a malicious pickle, it's
possible to get a virus. This is the principle behind the virus shown in the
video above.

## The Fix

Let's imagine Ren'Py gives up on Python pickles and chooses instead to use JSON
as a "safe" alternative.

JSON can't serialize arbitrary Python objects. So the Ren'Py save system would
no longer "just work." Game developers would have to figure out how to save
custom Python objects themselves. How would they do that? Like any decent
developer, they'd start with a Google search. Google would then proffer
countless blog posts and video tutorials on how save systems in Python can
quickly and easily be implemented with Python pickles. This highest-ranking and
most often suggested solution is, of course, precisely what Ren’Py is currently
implementing automatically for developers.

I have also observed that many save files are susceptible to trivial injection
attacks. Switching from Python pickles to JSON won’t fix this, either. So at
least some games will necessarily continue to have a dangerous save system.
Since gamers would have no way of knowing which games are susceptible, the
safest course is to avoid save files of unknown origin in all cases.

In short, if Ren'Py switches to a "safe" save system: it still won't be safe in
practice; end users will still have to exercise the same caution; and game
development will be significantly more difficult. All of this to fix a
non-feature that is rarely ever used.

So instead, Ren'Py has decided to keep its current save system but add warnings
and [documentation](https://www.renpy.org/doc/html/security.html). A future
version of Ren'Py is likely to warn users when they attempt to load a save file
originating from a different computer.

## Note to Developers

The simplest way to implement a generic save system is with arbitrary object
serialization. Because of its simplicity, it seems to be the most recommended
method online for implementing save systems (in Python, at least). If you
choose to use this method, save files will likely have the same abilities as
your code. As seen in the video above, the save file can rewrite parts of the
game, change variables or execute system commands.

Regardless of the save system you choose, a kind warning to make end users
aware of any dangers that could arise from misusing your product is never
remiss.

## Malware in the Wild?

I downloaded 354 files from Virustotal that matched the format of a Ren'Py save
file. These files were examined for malware or suspicious behavior. No malware
was found, and nothing appeared obfuscated. This is not to say that no malware
of this format exists, but it’s unlikely based on this and other
considerations.

## Public Sample

If I was the one reading this article, I would be excited to try my hand at
making my own virus to ~~irritate~~ surprise my friends. I don't necessarily
recommend this behavior. While save files can be used to mod the game, it often
requires a certain level of expertise to do this without causing crashes. It is
usually a lot easier to make something malicious.

So inspired by the idea that "RickRoll did more for security than any awareness
class", I created a "Get Stickbugged" Ren'Py save file Trojan. In theory, this
will just move a video file into the user's game directory and then play it
over and over in an infinite loop. (While this file *shouldn't* cause any harm,
I take no responsibility if it does.)

This file does contain, and will print, the EICAR AV signature string, so it
should (theoretically) be flagged by antivirus. All the assembly in this file
was written by me and assembled with Radare2. It breaks at least a couple of
rules regarding how pickles are expected to work, so most pickle tools error
when reading it. It should work on most Ren'Py games, regardless of whether
Python 2 or Python 3 is used, and should work even if the game is being played
on the web.

## Further Research

I wrote a Python pickle assembler, disassembler and decompiler to help with
this research. The assembler and disassembler are available in 
[Radare2](https://github.com/radareorg/radare2/). I also created a pickle 
decompiler, which is available [here](https://github.com/swoops/pickle_decomp). 
The decompiler would probably be the best option for reversing the above sample, 
but it is currently missing oneopcode by design. Eventually I intend to add in 
this opcode and others.

I'm still researching more ideas regarding pickles that I haven't yet
published. I'm currently between employers, but assuming that I'm working with
a new company before publication, I intend to publish on my next employer's
website. Regardless, I will link to the article(s) from here.
