<!--
author: Kyle
version: 0.1.0
language: en
comment: A tiny computer you can watch, one instruction at a time.
-->

# A computer you can watch

*[From the Silicon Up](https://liascript.github.io/course/?https://raw.githubusercontent.com/LordParsley/from-the-silicon-up/main/course.md) - Little Machine, lesson 1*

Lesson 0 ended with the idea that makes computers universal: a programme is data, stored in the same memory the machine works on. That leaves a fair question. How do you write and save a valid programme as data?

You give the machine an **instruction set**: a small, fixed list of commands it knows how to respond to. Each command has a short name we can read, like `LDA` ("load") or `ADD`, and a number the machine actually stores, like `508` for "load mailbox 08". Write your programme as those numbers, save them in memory, and the machine can run them one at a time. Every number it meets is either a command from its list or a piece of data to work on.

Real computers work exactly this way. A CPU's instruction set is its whole vocabulary, the complete list of things it can do. Storing instructions as numbers, in the same memory as the data, is the stored-programme idea computers have used since the 1940s.

We will borrow the **Little Man Computer (LMC)**, a teaching machine with an instruction set of about ten commands. You will write programmes as data, load them into memory, and watch the machine obey them. No installation, no real assembly toolchain, and no need to memorise everything.

Allow about 30-40 minutes. The aim is to predict what happens, then watch it happen.

## Your workbench

<a href="https://www.peterhigginson.co.uk/LMC/" target="_blank" rel="noopener noreferrer">Open the LMC simulator in another tab</a>.

Keep this lesson and the simulator side by side if you have room. A laptop or desktop will be more comfortable than a phone.

For each exercise:

1. Copy the whole programme into the simulator's assembly editor.
2. Replace `???` with your answer. The simulator cannot assemble a question mark.
3. Click **ASSEMBLE INTO RAM**. This loads the instructions and the starting data.
4. Predict the result, then click **STEP** repeatedly. Watch the accumulator and program counter.
5. To start again, assemble the programme again. This also restores the `DAT` values.

The answer boxes below check your typed answer in the lesson. They do not read your simulator. Use the worked answers after you have had a go.

## Meet the machine

- **Memory** is a set of 100 numbered mailboxes, `00` to `99`. A mailbox holds a number. Instructions live in memory too.
- The **accumulator** is the CPU's working number. Most arithmetic happens here.
- The **program counter** tells the CPU which instruction to fetch next. Normally it moves to the next mailbox. A branch changes that path.

An instruction such as `LDA SEVEN` means "copy the value stored at SEVEN into the accumulator". It does **not** mean "load the address of SEVEN".

`SEVEN DAT 7` sets aside a mailbox containing `7`. `SEVEN` is a label: a name for that mailbox's address. `DAT` prepares memory; it is not an action for the CPU to run.

<!-- data-type="None" -->
| Instruction | Meaning |
| --- | --- |
| `LDA <BOX>` | Copy `<BOX>`'s value into the accumulator |
| `STA <BOX>` | Copy the accumulator's value into `<BOX>` |
| `ADD <BOX>` | Add `<BOX>`'s value to the accumulator |
| `SUB <BOX>` | Subtract `<BOX>`'s value from the accumulator |
| `OUT` | Send the accumulator's value to the output |
| `BRA <PLACE>` | Always continue at `<PLACE>` |
| `BRZ <PLACE>` | Continue at `<PLACE>` if the accumulator is zero; otherwise continue on the next line |
| `HLT` | Stop |

We will stay with small non-negative numbers, so overflow and negative-number conventions can wait.

## 1. Take a number out of memory

The mailbox named SEVEN already contains `7`. Complete one instruction so the programme outputs `7`.

```text
??? SEVEN
OUT
HLT
SEVEN DAT 7
```

Which instruction replaces `???`? Type just the three-letter instruction, in capitals.

[[LDA]]

Did you find yourself confused about why the `DAT` line sits at the end? Take a guess before reading on. Why does it go there?

[[ ]] `DAT` only works at the end of a programme. The assembler refuses it anywhere else.
[[ ]] It is just a style convention. The programme would run the same wherever the `DAT` lines sit.
[[X]] The machine starts at mailbox `00` and works downward. It cannot tell data from instructions, so data must sit where execution never reaches it: after `HLT`.
[[ ]] The mailboxes at the end of the programme are the ones with free space.

The machine starts at mailbox `00` and runs downward until it hits `HLT`. It cannot tell data from instructions - both are just numbers in mailboxes. If `SEVEN DAT 7` sat in the middle, the machine would march straight into it and try to run `7` as a command. Putting the `DAT` lines after `HLT` means the machine stops before it ever reaches them. Position is the only thing separating code from data.

What if we break the rule? The same programme, with the data moved to the start:

```text
SEVEN DAT 7
LDA SEVEN
OUT
HLT
```

It assembles without complaint. Predict what happens when you run it. What does the machine do on its first step?

[[ ]] It runs normally. The machine knows `SEVEN DAT 7` is data, not a command.
[[X]] It stops immediately and outputs nothing. It fetches the `7` in mailbox `00` and reads it as a command: `007`, which is `HLT`.
[[ ]] It outputs `7` anyway, then stops.

Run it in the simulator and watch. The programme halts on its very first step and the output stays empty. The machine fetched the number `007` from mailbox `00`, read the leading `0` as `HLT`, and stopped. It never reached your real instructions at all.

Before stepping: what should happen to the accumulator on the first step?

<details>
<summary>Worked answer</summary>

```text
LDA SEVEN
OUT
HLT
SEVEN DAT 7
```

`LDA SEVEN` copies `7` into the accumulator. `OUT` outputs it. `HLT` stops.

There are three instructions before the data, so SEVEN is at address `03`. The label saves us from having to write that address ourselves.

Notice that reading SEVEN did not empty it. It still contains `7`.

</details>

## 2. Add without changing the ingredients

The data is ready. Complete the arithmetic instruction to output `7`.

```text
LDA THREE
??? FOUR
OUT
HLT
THREE DAT 3
FOUR  DAT 4
```

Which instruction replaces `???`?

[[ADD]]

Predict the accumulator after the first instruction, and again after the second.

<details>
<summary>Worked answer</summary>

```text
LDA THREE
ADD FOUR
OUT
HLT
THREE DAT 3
FOUR  DAT 4
```

The accumulator goes from `3` to `7`. Output is `7`.

THREE still contains `3` and FOUR still contains `4`. `ADD` changes the accumulator, not the source mailbox.

</details>

## 3. Put a result back in memory

Make SAVED contain `7` by the time the machine stops. There is no output instruction this time: inspect memory instead.

```text
LDA SEVEN
??? SAVED
HLT
SEVEN DAT 7
SAVED DAT 0
```

Which instruction replaces `???`?

[[STA]]

<details>
<summary>Worked answer</summary>

```text
LDA SEVEN
STA SAVED
HLT
SEVEN DAT 7
SAVED DAT 0
```

`LDA` copies from memory into the accumulator. `STA` copies from the accumulator into memory.

SAVED is at address `04`. Watch it change from `0` to `7` when you step through `STA SAVED`. The accumulator remains `7`.

</details>

## 4. Fix a broken jump

This programme should output `7`, but it stops without outputting anything. Change only the target of `BRA`.

```text
      LDA SEVEN
      BRA STOP
SHOW  OUT
STOP  HLT
SEVEN DAT 7
```

What should replace `STOP` on the `BRA` line?

[[SHOW]]

<details>
<summary>Worked answer</summary>

```text
      LDA SEVEN
      BRA SHOW
SHOW  OUT
STOP  HLT
SEVEN DAT 7
```

The broken version jumped straight to `HLT`, skipping `OUT`.

The fixed version jumps to SHOW, outputs `7`, then continues to STOP. This is a `goto`: no condition, just a different next instruction.

Watch the program counter. Changing where execution goes can change the result even when the numbers stay the same.

</details>

## 5. Build a tiny "if"

We want: **if VALUE is zero, stop without output; otherwise output VALUE**.

```text
      LDA VALUE
      ??? STOP
      OUT
STOP  HLT
VALUE DAT 0
```

Which instruction replaces `???`?

[[BRZ]]

Try it first with `VALUE DAT 0`. Then change that to `VALUE DAT 5`, assemble again, and try it once more.

<details>
<summary>Worked answer</summary>

```text
      LDA VALUE
      BRZ STOP
      OUT
STOP  HLT
VALUE DAT 0
```

With `0`, the branch is taken: the machine skips `OUT` and stops.

With `5`, the branch is not taken: the machine continues to `OUT`, outputs `5`, then stops.

The machine has no high-level `if` statement here. We made the decision by testing a value and changing the next instruction.

</details>

## 6. A small loop (optional stretch)

This one is a little longer. Make it output `3`, `2`, `1`, then stop. Fill in the one missing instruction name.

```text
      LDA START
LOOP  OUT
      SUB ONE
      ??? DONE
      BRA LOOP
DONE  HLT
START DAT 3
ONE   DAT 1
```

Which instruction replaces `???`?

[[BRZ]]

Use STEP rather than RUN first. Say the accumulator's value aloud before each `OUT`.

<details>
<summary>Worked answer</summary>

```text
      LDA START
LOOP  OUT
      SUB ONE
      BRZ DONE
      BRA LOOP
DONE  HLT
START DAT 3
ONE   DAT 1
```

The accumulator starts at `3`.

<!-- data-type="None" -->
| Value at OUT | After SUB ONE | What happens next |
| --- | --- | --- |
| 3 | 2 | Not zero: BRA goes back to LOOP |
| 2 | 1 | Not zero: BRA goes back to LOOP |
| 1 | 0 | Zero: BRZ jumps to DONE |

Zero is never output: we stop before reaching `OUT` again.

The data mailbox START stays `3`. The accumulator is doing the counting. Because we only load START once, the count does not reset each time around the loop.

</details>

## What you have built

You have moved a value into the CPU, changed it, stored it, jumped to another instruction, made a decision, and repeated work.

The labels make the programme readable. Underneath, the machine follows addresses and numbers.

Before finishing, explain these in your own words:

- Why does `ADD FOUR` leave FOUR unchanged?
- What is the difference between `BRA` and `BRZ`?
- In the loop, why is `LDA START` outside LOOP?

Next time: accept input and choose between two different results.

[Back to the course home](https://liascript.github.io/course/?https://raw.githubusercontent.com/LordParsley/from-the-silicon-up/main/course.md)

## References

- [Peter Higginson's LMC simulator](https://www.peterhigginson.co.uk/LMC/). Its HELP button documents the instruction set and controls.
- [LiaScript documentation](https://github.com/LiaScript/docs).

