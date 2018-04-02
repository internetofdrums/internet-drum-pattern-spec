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
- Defines velocity information for each note, ranging from 0 (silent) to 127
  (loud).
- Contains information for exactly 12 instruments.
- Should contain velocity information for every note of every
  instrument.

An internet drum pattern does not define tempo, drum sounds nor groove. They
can be defined by other modules down the interpreting chain, like a sequencer
or a drum machine.

## Data format

Given the constraints mentioned above, a drum pattern always has the same size:

- 12 instruments.
- 16 notes per instrument.
- Velocity information for each note.

That gives us 12 × 16 = 192 data parts for every drum pattern.

## Data size

Since velocity information is represented by whole numbers
ranging from 0 to 127, their information fits in one octet (8-bit byte). Given
192 data parts per drum pattern and the constraint that every note's data, even
if zero, should be defined, we need exactly **192 octets** for every drum
pattern.

## Wire format

Drum patterns can be sent over the wire by simply sending all note data
sequentially, starting at the velocity of note 1 of instrument 1. Next, the
velocity of note 2 of instrument 1 is up. After the velocity of the last note
of instrument 1 comes the velocity of note 1 of instrument 2. This continues
until the velocity of the 16th note of instrument 12 is processed.

For the more visually inclined: the following figure shows the data order for
each drum pattern.

```text
+------------+----------+---+----------+
| Instrument | Note 1   | … | Note 16  |
+------------+----------+---+----------+
| 1          | Velocity | … | Velocity |
+------------+----------+---+----------+
| 2          | Velocity | … | Velocity |
+------------+----------+---+----------+
| …          | …        | … | …        |
+------------+----------+---+----------+
| 12         | Velocity | … | Velocity |
+------------+----------+---+----------+
```

Receivers (interpreters) of drum patterns can use this definition to determine
incoming drum pattern data.

### Encoding

To prevent modification of data during transport over the internet, it is
probably wise to encode the data before sending it, and have the receiver
decode it again. The [standard][2] [base-64 encoding][3] should suffice for
this purpose.

[1]: https://en.wikipedia.org/wiki/MIDI
[2]: https://tools.ietf.org/html/rfc4648
[3]: https://en.wikipedia.org/wiki/Base64
