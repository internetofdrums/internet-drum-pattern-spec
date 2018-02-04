# Internet Drum Pattern Specification

This document specifies the format of simple drum patterns that can be sent
over the internet and interpreted by an intermediate. The intermediate,
typically a piece of software or hardware, should be able to translate this
pattern and pass it onto a receiving party that renders a musical pattern of
drum beats.

A typical example would be a device that receives these patterns and outputs a
MIDI signal, which can be sent to a drum computer that outputs the audible drum
pattern.

This specification deliberately specifies a very simple drum pattern, due to
the educational nature of the project it was born in (The Internet of Drums).

## Definition of a drum pattern

An internet drum pattern:

- Defines a rhythmic, loopable pattern to be played on a drum kit.
- Has a time signature of 4/4.
- Consists of 4 beats (pulses).
- Divides each beat into 4 equal parts (subdivisions), thus yielding a count of
  16 notes per instrument.
- Defines a length for each note, ranging from 0 (not played) to 127 (a quarter
  note, the logical maximum duration).
- Defines velocity information for each note, ranging from 0 (silent) to 127
  (loud).
- Contains information for exactly 16 instruments.
- Should contain velocity and note length information for every note of every
  instrument.

An internet drum pattern does not define tempo, drum sounds nor groove. They
can be defined by other modules down the interpreting chain, like a sequencer
or a drum machine.

## Data format

Given the constraints mentioned above, a drum pattern always has the same size:

- 16 instruments.
- 16 notes per instrument.
- Length information for each note.
- Velocity information for each note.

That gives us 16 × 16 × (1 + 1) = 512 data parts for every drum pattern.

## Data size

Since note lenth and velocity information are represented by whole numbers
ranging from 0 to 127, their information fits in one 8-bit byte. Given 512 data
parts per drum pattern and the constraint that every note's data, even if zero,
should be defined, we need exactly **512 bytes** for every drum pattern.

## Wire format

Drum patterns can be sent over the wire by simply sending all note data
sequentially, starting at the note length of note 1 of instrument 1, followed
by the note's velocity. Next, the note length of note 2 of instrument 1 is up,
followed by its velocity. After the velocity of the last note of instrument 1
comes the note length of note 1 of instrument 2. This continues until the
velocity of the 16th note of instrument 16 is processed.

For the more visually inclined: the following figure shows the data order for
each drum pattern.

```
+------------+-------------------+---+-------------------+
| Instrument | Note 1            | … | Note 16           |
+------------+-------------------+---+--------+----------+
| 1          | Length | Velocity | … | Length | Velocity |
+------------+--------+----------+---+--------+----------+
| 2          | Length | Velocity | … | Length | Velocity |
+------------+--------+----------+---+--------+----------+
| …          | …      | …        | … | …      | …        |
+------------+--------+----------+---+--------+----------+
| 16         | Length | Velocity | … | Length | Velocity |
+------------+--------+----------+---+--------+----------+
```

Receivers (interpreters) of drum patterns can use this definition to determine
incoming drum pattern data.

[1]: https://en.wikipedia.org/wiki/MIDI

