# TwitchMidi

An effort to make a twitch-chat reactive midi note generator for generative background music that doesn't suck (*too much*).

Inspired by instafluff's ComfyJazz (https://github.com/instafluff/ComfyJazz) but with midi note production rather than self-contained music generation.

## Usage

You can host this yourself, or just go to https://beavertank.github.io/TwitchMidi/ and use it from there.

Chrome will work out of the box, although it will prompt you on your first usage to give it access to MIDI devices. You'll need to grant this if you want to use it. 

Firefox will require a little tweaking: 
>go to **about:config**
>>find **dom.webmidi.enabled** and **dom.security.featurePolicy.header.enabled**<br>
>>make sure they're both set to "true"

If you changed either one you will need to completely restart Firefox for the changes to take effect.

For hardware (ie external) synths they should appear in the dropdown MIDI Device menu. Select the one you want, and the channel you want to use for Lead and/or Chord notes, and you're good to go. (a note: for the Chord notes to function properly whatever the data is going to needs to be polyphonic or paraphonic with the abililty to play 3 or 4 notes simultaneously)

For software (ie DAW or otherwise "in the box") synths you'll need to create a soft MIDI device that the program can send notes through and your DAW or other software can see as a controller of some kind. There are probably lots of ways to do this, but I have successfully used loopMIDI (https://www.tobias-erichsen.de/software/loopmidi.html) to do this without issue.

When you press Start Playing the note generation will begin. All settings (except the Twitch Channel) are dynamic and can be modified on the fly with instant effect on the next generation event; to change (or remove) the Twitch Channel the generator can't be running, so you either have to stop it - make the change - then start it or make the changes before starting.

The generator can be used without any Twitch integration by leaving that box blank and selecting the note triggers you want to use, or it can be used with actions based only on the Twitch channel chats by filling in the channel name and leaving all note triggers blank, or it can be used with a combination of Twitch channel chats and standard ongoing triggers by filling in both the channel name and selecting note triggers as well.

A note on Twitch channel names - use exactly what is found after the / in https://twitch.tv/[user_name] 

## How Does It Work?

The Lead MIDI channel will attempt to send a note in the key, mode, and octave selected every time it reaches a trigger event (either the selected beat(s) or on a message in the provided Twitch channel's chat). The odds of that note being sent is dependent on the trigger probability chosen. The note length will vary randomly between a beat and four beats and each subsequent note will be within the range (above and below) selected in the Max Interval slider. There is an extremely small chance that the note generation will instead trigger a five note triplet run up or down to add very occasional variability.

The Chord MIDI channel will attempt to send a new chord every 2 or 4 beats and that chord will contain the last note sent on the Lead MIDI channel to help tie things together musically. The chord itself will randomly be 3 or 4 notes (with the odds heavily favoring 3) and will be randomly chosen to be either a major triad, an inverted triad, or (much less frequently) a diminished chord.

Each note or chord will be randomly assigned a velocity value and a modwheel value which changes in a (hopefully) musical way. The velocity value has a low probability to change on each note generation event, and the modwheel has a similarly low probability to change on each note generation event. This mod wheel change is independent for the lead and the chord channels but the change event for both is tied to a note generation trigger on the lead channel. The mod wheel will strongly favor (with an 80% chance) to continue moving in the same direction, up or down, that it was previously moving in and will always go up from 0 or down from 127 so it can't get stuck at either end of the range.

The program will send a MIDI transport play signal on start playing, and a MIDI transport stop signal on stop playing, so the generation can be integrated with programmed loops or sequences as well.
