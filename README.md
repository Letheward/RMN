# Readable Music Notation

RMN, *Readable Music Notation* for humans **and** computers.

Good for theoretical analysis.

No sharp or flat, no typesetting, no dynamics, no grace note, no performance techniques.

## Syntax

Similiar to Lilypond with CSS flavor

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

- a part named `piano`, content in `{}`

`40:4`

- `C4`, quarter note, `:` for declaring note duration 

`,`

- barline

### Pitch

RMN use integer notation and Base 12 numbers. 

Because of using integer notation, sharp or flat doesn't matter, like in MIDI.

RMN Only use absolute pitch with `C0` = `00`, like Lilypond use `c'` for `C4`.

~~~
0  1  2  3  4  5  6  7  8  9  10(t) 11(e)  
C  C# D  D# E  F  F# G  G# A  A#    B
C  Db D  Eb E  F  Gb G  Ab A  Bb    B
~~~

~~~
C4: 40
Ab5: 58
B3: 3E
F#10: T6
~~~

### Percussions or any weird instruments

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
    
    Define an array of strings first, only use these strings for names.

### Rhythm

- Duration:

    ~~~
    guitar {
        30:8 32:8 33:8 35:8 32:6 2t:6 30:6,
    }
    ~~~

    after `:`, divide the wholenote by an `integer`.
    
    This is a syntax sugar to improve readability.

- Duration (advance):

    ~~~
    clap {
        h:1/8 h:2/17 h:1/4/5 h:3/1
    }
    ~~~

    by `/`, use fractions to express duration.
    
    `40:1/4/5` means divided by `4` then divided by `5`, equals to `40:20`.
    
    3 wholenotes must be written as `3/1`, writing `3` equals to `1/3`.

- Rest:

    ~~~
    piano {
        r:1,
        r:1,
        r:1,
        r:1,
    }
    ~~~

    `r` for rest, rhythm is the same as notes.

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

    It's possible to create 'Irrational Meter' like `4/3`.


## File Format

MIME: `text/plain`

Filename Extention: `.rmn`

## Reference

Lilypond: 

- readable, but use traditional system and have too much typesetting related function

musicxml: 

- not very readable, also use traditional system

midi: 

- kinda readable, but not good for mind model, not plain text