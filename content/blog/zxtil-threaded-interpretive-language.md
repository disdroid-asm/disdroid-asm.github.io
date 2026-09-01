+++
date = '2026-09-01T14:30:41+01:00'
title = 'ZXTil: A Threaded Interpretive Language for the ZX Spectrum'
tags = ['zx-spectrum','sinclair','forth','til','programming','languages']
categories = ['ZX Spectrum','FORTH','Z80']
+++

## "Threaded Interpretive Languages" by R. G. Loeliger

Back in the 1980s I discovered a book on the shelf of my local library - "Threaded Interpretive Languages" by R. G. Loeliger
(PDF available here on [_archive.org_](https://ia902805.us.archive.org/27/items/R.G.LoeligerThreadedInterpretiveLanguagesTheirDesignAndImplementationByteBooks1981/R.%20G.%20Loeliger-Threaded%20Interpretive%20Languages_%20Their%20Design%20and%20Implementation-Byte%20Books%20%281981%29.pdf)).

!["Threaded Interpretive Languages" by R. G. Loeliger](images/til.png)


The book describes how to implement the basics of an indirect threaded _FORTH_ interpreter, and also contains instructions on how
to implement compilation of executable files, 'block' editors, and compilation from storage.

## FORH on the ZX Spectrum

I was fascinated, since I had already experimented with _FORTH_ using [_White Lightning_ by Oasis Software](https://spectrumcomputing.co.uk/entry/8967/ZX-Spectrum/White_Lightning), but I never had time to try it out.

![White Lightning by Oasis Software](images/wl.png)

Since I've now got my platform set up for ZX Spectrum coding, I decided to have a bash at importing the code.
The published code is intended as an example of how such a language might be implemented, but it largely works out
of the box. I found a few bugs and typos, but these were easy to find and fix.

## TIL Implementation on the Spectrum

The main issue that I encountered at first is that the code relies on the use of the `iy` Z80 register, which is used by 
the ROM on the ZX Spectrum and isn't available for use by programs that use ROM routines or interface with _BASIC_.
Therefore I had to import display and keyboard routines from my games library, and adapt these for text input.
The intiial version I created has a bug somewhere which means that symbol shift isn't working, so I can't test 
the defining words containing symbols such as ':' and '.'. Setting breakpoints in the keyboard routines is tiresome because the 
emulator pauses and this locks the keyboard.

However the outer interpreter loop runs fine. The next step is to get compilation working, immediate mode, etc.

## Project page

The project page is [_here_](/projects/zxtil) and the repository is [_here_](https://github.com/disdroid-asm/zxtil).


