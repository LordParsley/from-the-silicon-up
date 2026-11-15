<!--
author: Kyle
version: 0.2.0
language: en
comment: Adding machines, algorithms, and programmes, before any electronics.
-->

# A machine that adds

*[From the Silicon Up](https://liascript.github.io/course/?https://raw.githubusercontent.com/LordParsley/from-the-silicon-up/main/course.md) - Before the Silicon, lesson 0*

Before we meet a real computer, it is worth asking a stranger question: could you build an adding machine with no electricity in it at all?

People were building calculators long before electricity. In 1642, Blaise Pascal built one from gears and dials to help with his father's tax work. Water has even had a go: in 1949 the economist Bill Phillips built the MONIAC, a computer that modelled a whole national economy with water flowing through tanks and pipes. But water computes with levels, not digits.

The clearest little adding machine uses something humbler: marbles, wood, and gravity. It really exists - people build them as workshop projects - and its one moving part works exactly like the switches inside a real chip.

This lesson has no simulator and nothing to install. Allow about 25-30 minutes. The three ideas here (adding with carries, algorithms, and programmes) are the foundation for everything that follows.

## The marble adding machine

Picture a row of four wooden rockers, like tiny see-saws, side by side. Each rocker tips to the left or the right.

A rocker tipped to the right means `1`. Tipped to the left means `0`.

Each rocker has a value. Reading left to right, they are worth 8, 4, 2 and 1. The number the machine stores is the sum of the values of the rockers showing `1`.

This is not just a thought experiment. Here is the real thing: a marble adding machine built from plywood by Matthias Wandel.

[![The real binary marble adding machine, built from plywood by Matthias Wandel](https://woodgears.ca/marbleadd/marble_adder.jpg)](https://woodgears.ca/marbleadd/)

*Photo: [Matthias Wandel, woodgears.ca](https://woodgears.ca/marbleadd/). His machine has six rockers and counts up to 63; ours has four, to keep things small. It is genuinely worth watching in action:*

!?[Matthias Wandel demonstrates his wooden binary marble adding machine](https://www.youtube.com/watch?v=GcDshWmhF4A)

Our machine, storing the number 5 (only the rockers worth 4 and 1 are tipped to 1, and 4 + 1 = 5):

![Four rocker columns in a row, worth 8, 4, 2 and 1. The rockers worth 4 and 1 are tipped right, each holding a marble; the others are tipped left. Together they store the number 5. A fresh marble is dropping toward the worth-1 column.](marble-machine.svg)

**Try it.** The four boxes below are our rockers, drawn as the digit each one stores. Click a box to tip its rocker: the digit flips between 0 and 1, and the stored number on the far right updates. The marble button drops one marble into the rightmost column (add 1) - watch what happens when a rocker already shows `1`.

<div id="wam" style="margin:1em 0;"><span style="display:inline-block; text-align:center; vertical-align:bottom; margin-right:12px;"><span style="display:block; font-size:0.85em; opacity:0.7;">worth 8</span><button class="wam-lock" data-value="8" data-full="0" style="width:76px; height:56px; font-size:1.3em; border:2px solid #94a3b8; border-radius:8px; background:#e5e7eb; color:#374151; cursor:pointer;">0</button></span><span style="display:inline-block; text-align:center; vertical-align:bottom; margin-right:12px;"><span style="display:block; font-size:0.85em; opacity:0.7;">worth 4</span><button class="wam-lock" data-value="4" data-full="0" style="width:76px; height:56px; font-size:1.3em; border:2px solid #94a3b8; border-radius:8px; background:#e5e7eb; color:#374151; cursor:pointer;">0</button></span><span style="display:inline-block; text-align:center; vertical-align:bottom; margin-right:12px;"><span style="display:block; font-size:0.85em; opacity:0.7;">worth 2</span><button class="wam-lock" data-value="2" data-full="0" style="width:76px; height:56px; font-size:1.3em; border:2px solid #94a3b8; border-radius:8px; background:#e5e7eb; color:#374151; cursor:pointer;">0</button></span><span style="display:inline-block; text-align:center; vertical-align:bottom; margin-right:12px;"><span style="display:block; font-size:0.85em; opacity:0.7;">worth 1</span><button class="wam-lock" data-value="1" data-full="0" style="width:76px; height:56px; font-size:1.3em; border:2px solid #94a3b8; border-radius:8px; background:#e5e7eb; color:#374151; cursor:pointer;">0</button></span><span style="display:inline-block; vertical-align:bottom; font-size:1.6em; margin-right:12px; padding-bottom:6px;">= <span id="wam-total">0</span></span><button id="wam-add" style="vertical-align:bottom; height:56px; padding:0 14px; border:2px solid #1d4ed8; border-radius:8px; background:#eff6ff; color:#1d4ed8; cursor:pointer;">drop a marble (add 1)</button></div>

<script>
(function () {
  var root = document.getElementById('wam');
  if (!root || root.dataset.wired) return;
  root.dataset.wired = '1';
  var locks = Array.prototype.slice.call(root.querySelectorAll('.wam-lock'));
  var total = document.getElementById('wam-total');
  function paint(b) {
    var full = b.dataset.full === '1';
    b.textContent = full ? '1' : '0';
    b.style.background = full ? '#2563eb' : '#e5e7eb';
    b.style.color = full ? '#ffffff' : '#374151';
    b.style.borderColor = full ? '#1d4ed8' : '#94a3b8';
  }
  function update() {
    var t = 0;
    locks.forEach(function (b) { if (b.dataset.full === '1') t += parseInt(b.dataset.value, 10); });
    total.textContent = t;
  }
  locks.forEach(function (b) {
    b.addEventListener('click', function () {
      b.dataset.full = (b.dataset.full === '1') ? '0' : '1';
      paint(b); update();
    });
    paint(b);
  });
  document.getElementById('wam-add').addEventListener('click', function () {
    var byValue = locks.slice().sort(function (a, b) { return parseInt(a.dataset.value, 10) - parseInt(b.dataset.value, 10); });
    for (var i = 0; i < byValue.length; i++) {
      var b = byValue[i];
      if (b.dataset.full === '1') { b.dataset.full = '0'; paint(b); }
      else { b.dataset.full = '1'; paint(b); break; }
    }
    update();
  });
  update();
})();
</script>

Here is the machine storing the number 5. Only the rockers worth 4 and 1 show `1`, and 4 + 1 = 5:

<!-- data-type="None" -->
| worth 8 | worth 4 | worth 2 | worth 1 | number stored |
| --- | --- | --- | --- | --- |
| 0 | 1 | 0 | 1 | 5 |

And here are six different numbers, one per row:

<!-- data-type="None" -->
| Number stored | worth 8 | worth 4 | worth 2 | worth 1 |
| --- | --- | --- | --- | --- |
| 0 | 0 | 0 | 0 | 0 |
| 1 | 0 | 0 | 0 | 1 |
| 2 | 0 | 0 | 1 | 0 |
| 3 | 0 | 0 | 1 | 1 |
| 4 | 0 | 1 | 0 | 0 |
| 5 | 0 | 1 | 0 | 1 |

Reading a row's rockers as digits gives a pattern like `0101`, which means 5. This way of writing numbers is called **binary**, and it is exactly how computers store numbers, using tiny switches instead of rockers.

## 1. Read the machine

The rockers show, left to right: `1`, `0`, `1`, `0`.

What number is stored?

[[10]]

<details>
<summary>Worked answer</summary>

The rockers worth 8 and 2 show `1`. The others contribute nothing.

8 + 2 = 10.

With four rockers, the largest number we can store is `1111` = 8 + 4 + 2 + 1 = 15.

</details>

## One rule for adding

To **add 1**, drop a marble into the rightmost column (the one worth 1), and follow this rule:

> When a marble lands on a rocker: if the rocker shows `0`, it tips to `1` and catches the marble, and you stop. If the rocker shows `1`, it tips back to `0` and the marble rolls on to the next column to the left, which follows the same rule.

Watch it add 1 to 3 (`0011`):

- The marble lands on the 1-rocker. It shows `1`, so it tips to `0` and the marble rolls left to the 2-rocker.
- The 2-rocker shows `1`, so it tips to `0` and the marble rolls left to the 4-rocker.
- The 4-rocker shows `0`, so it tips to `1` and catches the marble. Stop.

The pattern is now `0100`, which is 4. We just computed 3 + 1 = 4.

That moment when a rocker tips back to `0` and sends the marble one column left is called a **carry**. You already know carries from school arithmetic: 7 + 5 makes 12, so you write down 2 and carry the 1. Our rockers carry whenever they pass 1, because a rocker can only hold 0 or 1.

**Watch it count.** This counter ticks the same rule. Each click of the button drops one marble into the machine: the rockers show the count in binary, the number on the right in ordinary decimal. Start at 0, keep ticking, and watch the carries ripple.

<div id="mmc" style="margin:1em 0;"><span class="mmc-bit" data-value="8" data-full="0" style="display:inline-block; width:64px; height:56px; line-height:52px; text-align:center; vertical-align:bottom; margin-right:12px; font-size:1.3em; border:2px solid #94a3b8; border-radius:8px; background:#e5e7eb; color:#374151;">0</span><span class="mmc-bit" data-value="4" data-full="0" style="display:inline-block; width:64px; height:56px; line-height:52px; text-align:center; vertical-align:bottom; margin-right:12px; font-size:1.3em; border:2px solid #94a3b8; border-radius:8px; background:#e5e7eb; color:#374151;">0</span><span class="mmc-bit" data-value="2" data-full="0" style="display:inline-block; width:64px; height:56px; line-height:52px; text-align:center; vertical-align:bottom; margin-right:12px; font-size:1.3em; border:2px solid #94a3b8; border-radius:8px; background:#e5e7eb; color:#374151;">0</span><span class="mmc-bit" data-value="1" data-full="0" style="display:inline-block; width:64px; height:56px; line-height:52px; text-align:center; vertical-align:bottom; margin-right:12px; font-size:1.3em; border:2px solid #94a3b8; border-radius:8px; background:#e5e7eb; color:#374151;">0</span><span style="display:inline-block; vertical-align:bottom; font-size:1.6em; margin-right:12px; padding-bottom:6px;">= <span id="mmc-total">0</span></span><button id="mmc-add" style="vertical-align:bottom; height:56px; padding:0 14px; border:2px solid #1d4ed8; border-radius:8px; background:#eff6ff; color:#1d4ed8; cursor:pointer;">drop a marble (add 1)</button><span id="mmc-note" style="display:inline-block; vertical-align:bottom; margin-left:12px; padding-bottom:12px; font-style:italic; color:#b45309;"></span></div>

<script>
(function () {
  var root = document.getElementById('mmc');
  if (!root || root.dataset.wired) return;
  root.dataset.wired = '1';
  var bits = Array.prototype.slice.call(root.querySelectorAll('.mmc-bit'));
  var total = document.getElementById('mmc-total');
  var note = document.getElementById('mmc-note');
  function paint(b) {
    var full = b.dataset.full === '1';
    b.textContent = full ? '1' : '0';
    b.style.background = full ? '#2563eb' : '#e5e7eb';
    b.style.color = full ? '#ffffff' : '#374151';
    b.style.borderColor = full ? '#1d4ed8' : '#94a3b8';
  }
  function update() {
    var t = 0;
    bits.forEach(function (b) { if (b.dataset.full === '1') t += parseInt(b.dataset.value, 10); });
    total.textContent = t;
  }
  document.getElementById('mmc-add').addEventListener('click', function () {
    var byValue = bits.slice().sort(function (a, b) { return parseInt(a.dataset.value, 10) - parseInt(b.dataset.value, 10); });
    var wrapped = true;
    for (var i = 0; i < byValue.length; i++) {
      var b = byValue[i];
      if (b.dataset.full === '1') { b.dataset.full = '0'; paint(b); }
      else { b.dataset.full = '1'; paint(b); wrapped = false; break; }
    }
    note.textContent = wrapped ? 'overflow! The marble fell out of the machine, and the count wraps back to 0.' : '';
    update();
  });
  update();
})();
</script>

Tick past 15 and every rocker flips back to `0`: there is no fifth rocker to catch the marble, so it falls clean out of the machine. That part is not invented for the lesson - on Wandel's real machine, adding 1 to a full register leaves every rocker at 0 and the runaway marble drops out of the machine entirely. Computers do this too: a number that outgrows the space allotted to it wraps around. It is called **overflow**.

## 2. Run the machine

The rockers show `0111` (that is 7). Follow the rule and add 1.

What is the new pattern? Type four digits.

[[1000]]

<details>
<summary>Worked answer</summary>

- The 1-rocker shows `1`: it tips to `0`, the marble rolls left.
- The 2-rocker shows `1`: it tips to `0`, the marble rolls left.
- The 4-rocker shows `1`: it tips to `0`, the marble rolls left.
- The 8-rocker shows `0`: it tips to `1` and catches the marble. Stop.

The pattern `0111` becomes `1000`: the number 8. The marble travelled through three rockers before finding one showing `0`.

</details>

Adding bigger numbers, subtracting, even multiplying, can all be built from this one idea with a few more rules. Real processors do exactly this, with electricity standing in for marbles.

## The rule is an algorithm

Notice what kind of thing our adding rule is:

- It is a **finite** list of steps.
- Every step is **completely mechanical**: "if the rocker shows `1`, tip it to `0` and send the marble left" requires no judgement, no cleverness, no guesses.
- It **always works**, for any starting pattern.

A set of steps like that is called an **algorithm**: a recipe so precise that a machine, or a person following it thoughtlessly, gets the right answer every time. Long division is an algorithm. A cake recipe mostly is one. "Season to taste" is not, because it asks for judgement.

In the 1930s, the mathematician Alan Turing asked a daring question: is there a single kind of machine that could follow *any* algorithm? His answer, the **Turing machine**, is a hypothetical device that reads and writes symbols on a tape by following rules exactly as simple as our marble rule. Turing showed this humble machine can compute anything that any algorithm can compute. Every computer ever built, from the marble adder to your phone, is one of these machines wearing different clothes. That claim is called the **Church-Turing thesis**, and nothing has ever broken it.

## 3. Spot the algorithm

Which of these is an algorithm?

[[X]] To add 1: drop a marble into the rightmost column. When it lands on a rocker showing `0`, the rocker tips to `1` and catches it; on a `1`, the rocker tips to `0` and the marble rolls left.
[[ ]] Keep dropping marbles around the machine until the answer feels right.
[[ ]] Ask the machine politely to think of a bigger number.

<details>
<summary>Worked answer</summary>

Only the first one. It has fixed steps, needs no judgement, and always finishes.

"Until it feels right" and "think of a number" both sneak in judgement or magic. An algorithm is exactly the part of problem-solving that needs neither. That is why it can be handed to a machine.

</details>

## Some problems have no recipe

If any algorithm can be followed by a machine, it is tempting to believe every question has an algorithm. Turing proved otherwise.

The most famous example: the **halting problem**. Programmes sometimes get stuck in loops that never end. It would be wonderful to have a "loop detector": a programme that reads any other programme and always answers correctly, "this one stops" or "this one runs forever".

Turing proved in 1936 that no such programme can exist. Not that we have not invented it yet, but that it is impossible, the way a square circle is impossible. Any machine you could ever build will either answer wrongly on some programmes or spin forever without answering.

So problems split in two:

- **Computable**: an algorithm exists. Adding numbers, sorting names, finding the shortest route between two towns.
- **Non-computable**: no algorithm exists, and none ever will. Telling, in general, whether an arbitrary programme halts is the classic case.

So there are questions with definite answers that no machine, however fast or large, can answer in general.

## 4. Computable or not?

You are given any map and two towns. Is there an algorithm that always finds the shortest route between them?

[[X]] Yes: try the routes in a fixed order and keep the shortest. Slow, maybe, but mechanical.
[[ ]] No: every map is different, so no single recipe can cover them all.

And the loop detector: a programme that reads any programme and always says whether it halts?

[[ ]] Yes: a fast enough computer could check every case.
[[X]] No: Turing proved no algorithm can do this for every possible programme.

<details>
<summary>Worked answer</summary>

Shortest route: computable. One fixed recipe (list every route, measure each, keep the shortest) works on every map. Being slow does not make a problem non-computable.

The loop detector: non-computable. Speed is not the issue. Even infinitely fast hardware cannot fix the problem Turing found, because the contradiction is in the question itself.

</details>

## The programme is data too

Our marble machine had its rule drilled into whoever operated it. But we could write the rule down: "when a marble lands on a rocker, if it shows `1` then tip it to `0` and send the marble left, else tip it to `1` and catch it". Written down, the rule is just symbols on paper: **data**.

Now imagine a machine that reads its rules from the same place it keeps its numbers. Feed it the adding rules and it adds. Feed it different rules and it sorts names, or plays music, without changing a single rocker or rail. That is a **universal machine**, and it is what a computer is.

A music box works this way: the tune is not built into the box, it is a pattern of bumps on a metal cylinder, and swapping cylinders swaps the song. Early looms wove patterns from punched cards. A modern computer stores its programmes in memory, as numbers, right next to the numbers they work on.

This is why coding is the way it is:

- **A programme is a file.** Instructions are stored as data, so they can be saved, copied, shared and edited like any document.
- **Programmes can work on programmes.** The tools that turn the code you write into running machine instructions (compilers and interpreters) are themselves programmes that read programmes as their input.
- **One machine, every job.** Because the programme lives in memory, the same computer can be a calculator, a text editor or a game just by loading different data. You never rebuild the hardware.

Whenever you hear that "code is data", this is the idea underneath.

## 5. Where does the programme live?

In a modern computer, where are the running programme's instructions stored?

[[ ]] In a special part of the machine that only holds instructions.
[[X]] In the same memory as the data the programme works on.
[[ ]] Nowhere: the instructions are wired into the circuits.

<details>
<summary>Worked answer</summary>

In the same memory as the data. Instructions and data are both just numbers in memory, which is what makes the computer universal: loading a different programme is loading different numbers.

Wiring the instructions into the machine was how some very early computers worked. Reprogramming them meant moving cables by hand. Storing the programme in memory instead, the "stored-programme computer", is the idea every modern machine is built on.

</details>

## What you have built

- A working mental model of a mechanical adder: rockers that tip between `0` and `1`, carrying by passing the marble left. Binary is just tipped-and-untipped written down.
- A definition of an **algorithm**: finite, mechanical, always right.
- A boundary: some problems are **non-computable**, proven so, not just waiting for faster hardware.
- The trick that makes computers universal: the **programme is data**, stored in the same memory it works on.

Before finishing, explain these in your own words:

- Why does adding 1 to `0111` tip every rocker?
- What makes "season to taste" fail as an algorithm?
- Why can the same computer run a game and a spreadsheet without new hardware?

Next: [A computer you can watch](https://liascript.github.io/course/?https://raw.githubusercontent.com/LordParsley/from-the-silicon-up/main/little-machine/lesson-01.md), where we meet a real (tiny) machine that stores both its instructions and its data in memory, and we make it compute.

[Back to the course home](https://liascript.github.io/course/?https://raw.githubusercontent.com/LordParsley/from-the-silicon-up/main/course.md)

## References

- [Matthias Wandel's binary marble adding machine](https://woodgears.ca/marbleadd/) - the real wooden machine this lesson is modelled on, with plans and a video.
- [The Halting Problem, Computerphile](https://www.youtube.com/watch?v=macM_MtS_w4) - a friendly video proof of why the loop detector cannot exist.
- [Turing's 1936 paper, "On Computable Numbers"](https://www.cs.virginia.edu/~robins/Turing_Paper_1936.pdf) - where the universal machine first appears, for the curious.
- [Pascal's calculator](https://en.wikipedia.org/wiki/Pascal%27s_calculator) - the 1642 gear-driven adding machine.
- [The MONIAC](https://en.wikipedia.org/wiki/Phillips_Machine) - the 1949 water-powered computer.
