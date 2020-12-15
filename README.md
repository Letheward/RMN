# Readable Music Notation

RMN, *Readable Music Notation* for humans **and** computers.

Good for theoretical analysis and computing.

No sharp or flat, no typesetting, no dynamics, no grace note, no performance techniques.

## Syntax

Similiar to Lilypond with CSS and JSON flavor

### Basic

~~~
/*
Lyrics:
twinkle twinkle little star,
how I wonder where you are.
*/

piano {
    // two bars per line 
    40:4 40:4 47:4 47:4, 49:4 49:4 47:2,

    /* the other half */
    45:4 45:4 44:4 44:4, 42:4 42:4 40:2,
}
~~~

`/* */`

- comments in multiple lines and single line

`//`

- comments in a single line

`piano {}`

- an object named `piano`, musical content in `{}`

`40:4`

- `C4`, quarter note, `:` for declaring note duration 

`,`

- barline

### Pitch

- Conversion

    Pitch Class:

    ~~~
    0  1  2  3  4  5  6  7  8  9  10(t) 11(e)  
    C  C# D  D# E  F  F# G  G# A  A#    B
    C  Db D  Eb E  F  Gb G  Ab A  Bb    B
    ~~~

    Octaves:
    ~~~
    C4: 40
    Ab5: 58
    B3: 3E
    F#10: T6
    ~~~
    
    RMN use integer notation and Base 12 numbers. 

    Because of using integer notation, sharp or flat doesn't matter, like in MIDI.

    RMN **ONLY** use absolute pitch with `C0` = `00`, like Lilypond use `c'` for `C4` in absolute mode.

- Transpose

    Part written in A clarinet to C (concert pitch).

    `Eb4 F4 G4 Ab4` (instrument pitch) 
    
    to    
    `C4 D4 E4 F4` (concert pitch)

    `Eb4 F4 G4 Ab4` = `43 45 47 48` 
    
    `43 45 47 48` - `3` = `40 42 44 45`

    ~~~
    clarinet {
        40:4 42:4 44:4 45:4
    }
    ~~~

    RMN **ONLY** use concert pitch.

    Calculate first to convert instrument pitch to concert pitch, etc.

    This should be easy enough for computers and humans familiar with integer notation,

### Percussions

- single instrument: 

    ~~~
    kickdrum {
        hit:4 hit:4 hit:4 hit:4
    }
    ~~~
    
    Any string that don't conflict with pitch names and rest, etc. 
    
    `h` is recommended. 
    
    Other strings can be use to specify techniques, but the whole point is to avoid techniques.

- multiple instruments: 

    ~~~
    drumset [
        kick,
        snare,
        hat,
        ride,
    ] {
        kick:4 r:8 snare:8 kick:4 snare:4,
        hat:8 ride:8 kick:4 kick:2,
    }
    ~~~
    
    Use `[]` to define an array of strings first, only use these strings for names.

    `drumset` can still has pitched notes in `{}`, and the array can be use for many other things, more on that later.

### Rhythm

- Duration:

    ~~~
    guitar {
        30:8 32:8 33:8 35:8 32:6 2t:6 30:6,
    }
    ~~~

    after `:`, divide the wholenote by an `integer`.
    
    This is a syntax sugar to improve readability.

- Duration 2:

    ~~~
    guitar {
        30:8 32 33 35 32:6 2t 30,
    }
    ~~~

    Similiar to Lilypond, You can ignore duration declaring if it's the same as the previous note. 

    There is no default duration like `4` in Lilypond.
    
    If you want less confusion you can always declare them all.

- Duration (general):

    ~~~
    clap {
        h:1/8 h:2/17 h:1/4/5 h:3/1 h:3/8
    }
    ~~~

    by `/`, use fractions to express duration.
    
    `h:1/4/5` means wholenote divided by `4` then divided by `5`, equals to `h:20`.
    
    3 wholenotes must be written as `3/1`, writing `3` equals to `1/3`.

    `h:3/8` is a way to notate dotted rhythm.

- Rests:

    ~~~
    piano {
        r:1,
        r:1,
        r:1,
        r:1,
    }
    ~~~

    `r` for rests, duration declaring is the same as notes.

- Tie:

    ~~~
    flute { 
        47:4~ 47:4 40:2~,
        40:4~ 40:8 49:8~ 49:2, 
    }
    ~~~

    `~` for tie the note before to the next.

- Time Signature:

    ~~~
    electric_guitar {
        // time sig: 7/16
        24:16 r:16 24:16~ 24:16 34:16 24:16 r:16, 
    }
    ~~~

    End bar with `,` anywhere you like to create alternative meter.

    You can note the Time Signature in comments for clarity.

    It's possible to create "Irrational Meters" like `4/3`.

### Multiple Voices

- Write them as multiple objects:

    ~~~
    piano_voice1 {
        40:4 44:4 47:4 4e:4
    }

    piano_voice2 {
        44:4 47:4 4e:4 52:4
    }

    piano_voice3 {
        47:4 4e:4 52:4 56:4
    }
    ~~~

    RMN doesn't care if these are in one staff or not. 
    
    `piano_voice1` is just a string, you can use anything you like.

    It's then possible to notate a frakenstein instrument (mounting a flute on a guitar and with pitchrange overlapping). Just choose the right object name for each part.

    You can also notate guitars with 6 objects (each for a guitar string) this way.

- Write them in one object:

    ~~~
    piano {
        <40 44 47>:4
        <44 47 4e>:4
        <47 4e 52>:4
        <4e 52 56>:4,
    }
    ~~~

    `<>` for groups. Notes (pitched and percussions) in a group must share the same duration.

### Objects, Events, Arrays, Groups and Elements

> Note: this part is kinda messy now, maybe it won't stay in future syntax standards.

A notation of a frakentar instument playing a song.

> See `src/` for a hardcore version.

~~~
frakentar [
    perc [
        scratch,
        knock,
    ],
    seq [
        lick {
            30:8 32 33 35 32:4 2t:8 30~,
            30:4 r r:2,
        },
    ],
    chord [
        CMaj7 <40 44 47 4e>,
        AbMaj7 <38 40 43 47>,
        Dm7 <42 45 49 50>,
    ],
    part [
        A {
            {perc.scratch:8 perc.knock:3/16,}*5
            seq.lick*3
        },
        B {  
            chord.CMaj7:2 chord.AbMaj7,
            chord.Dm7:4 chord.CMaj7 chord.AbMaj7:2,
        },
    ],
] {
    part.A part.A part.B part.A
    part.B part.B part.A part.A
}
~~~

- Data Structure:

    ~~~
    frakentar [] {}
    ~~~

    `frakentar` is an object. An object is often an instrument or a voice.
    
    `[]` for a meta-array describing percussions, chords, sequences, song parts, etc.

    `{}` for a meta-event: All the events `frakentar` do.

    ~~~
    perc [
        scratch,
        knock,
    ],
    ~~~

    `perc` is a element in meta-array and also an array.
    
    `scratch` and `knock` are elements in `perc`.

    `,` for dividing `perc` with other elements.

    ~~~
    seq [
        lick {
            30:8 32 33 35 32:4 2t:8 30~,
            30:4 r r:2,
        },
    ],
    ~~~

    `seq` is a element in meta-array and also an array.
    
    `lick` is a element and an event containing the lick with `{}`.

    `lick` is not an object. 
    
    There cannot be objects in an object, unlike in OO languages.

    ~~~
    chord [
        CMaj7 <40 44 47 4e>,
        AbMaj7 <38 40 43 47>,
        Dm7 <42 45 49 50>,
    ],
    ~~~

    `chord` is a element and an array.

    `CMaj7`, `AbMaj7` and `Dm7` are elements in `chord`, and also groups.

    `CMaj7` is a group containing pitch elements: `40`, `44`, `47`, `4e` with `<>`.

    ~~~
    part [
        A {},
        B {},
    ],
    ~~~

    `part` is a element and an array.

    `A` and `B` are elements in `part`.

    ~~~
    A {
        {perc.scratch:8 perc.knock:3/16,}*5
        seq.lick*3
    },
    ~~~

    `A` is an event.

    `{perc.scratch:8 perc.knock:3/16,}` is a element in event `A`,
    
    also an event containing percussion element `perc.scratch:8` and `perc.knock:3/16` and **barline** `,`.

    `perc.scratch` is a way to call element `scratch` in array `perc`.

    `*5` means repeats `{perc.scratch:8 perc.knock:3/16,}` 5 times.

    `seq.lick` is a way to call element `lick` in `seq`.

    `lick` is also an event containing 2 bars of music and **barlines**.

    ~~~
    {
    part.A part.A part.B part.A
    part.B part.B part.A part.A
    }
    ~~~

    The meta-event of object `frakentar`.

    `part.A` is a way to call event `A` in array `part`.

## File Format

MIME: `text/plain`

Filename Extention: `.rmn`

## Reference

Lilypond: 

- readable, but use traditional system and have too much typesetting related function

MusicXML: 

- not very readable, also use traditional system

MIDI: 

- kinda readable, but not good for mind model, not plain text