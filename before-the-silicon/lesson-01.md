<!--
author: Kyle
version: 0.1.0
language: en
comment: Adding machines, algorithms, and programmes, before any electronics.
-->

# A machine that adds

*[From the Silicon Up](https://liascript.github.io/course/?https://raw.githubusercontent.com/LordParsley/from-the-silicon-up/main/course.md) - Before the Silicon, lesson 1*

Before we meet a real computer, it is worth asking a stranger question: could you build an adding machine out of water?

People were building calculators long before electricity. In 1642, Blaise Pascal built one from gears and dials to help with his father's tax work. Gears are fiddly, so we will use something easier to picture: canal locks, the stepped water chambers that lift boats uphill.

This lesson has no simulator and nothing to install. Allow about 25-30 minutes. The three ideas here (adding with carries, algorithms, and programmes) are the foundation for everything that follows.

## The water adding machine

Picture a row of four canal locks, side by side. Each lock is either **full** of water or **empty**. That is the whole memory of our machine.

A full lock means `1`. An empty lock means `0`.

Each lock has a value. Reading left to right, they are worth 8, 4, 2 and 1. The number the machine stores is the sum of the values of its full locks.

Our machine, storing the number 5 (only the locks worth 4 and 1 are full, and 4 + 1 = 5):

![Four canal locks in a row, worth 8, 4, 2 and 1. The locks worth 4 and 1 are full of water; the others are empty. Together they store the number 5.](locks.svg)

**Try it.** Click a lock to fill or empty it, and watch the stored number on the far right. The bucket button pours one bucket into the rightmost lock (add 1) - watch what happens when a lock is already full.

<div id="wam" style="margin:1em 0;"><span style="display:inline-block; text-align:center; vertical-align:bottom; margin-right:12px;"><span style="display:block; font-size:0.85em; opacity:0.7;">worth 8</span><button class="wam-lock" data-value="8" data-full="0" style="width:76px; height:56px; border:2px solid #94a3b8; border-radius:8px; background:#e5e7eb; color:#374151; cursor:pointer;">empty</button></span><span style="display:inline-block; text-align:center; vertical-align:bottom; margin-right:12px;"><span style="display:block; font-size:0.85em; opacity:0.7;">worth 4</span><button class="wam-lock" data-value="4" data-full="0" style="width:76px; height:56px; border:2px solid #94a3b8; border-radius:8px; background:#e5e7eb; color:#374151; cursor:pointer;">empty</button></span><span style="display:inline-block; text-align:center; vertical-align:bottom; margin-right:12px;"><span style="display:block; font-size:0.85em; opacity:0.7;">worth 2</span><button class="wam-lock" data-value="2" data-full="0" style="width:76px; height:56px; border:2px solid #94a3b8; border-radius:8px; background:#e5e7eb; color:#374151; cursor:pointer;">empty</button></span><span style="display:inline-block; text-align:center; vertical-align:bottom; margin-right:12px;"><span style="display:block; font-size:0.85em; opacity:0.7;">worth 1</span><button class="wam-lock" data-value="1" data-full="0" style="width:76px; height:56px; border:2px solid #94a3b8; border-radius:8px; background:#e5e7eb; color:#374151; cursor:pointer;">empty</button></span><span style="display:inline-block; vertical-align:bottom; font-size:1.6em; margin-right:12px; padding-bottom:6px;">= <span id="wam-total">0</span></span><button id="wam-add" style="vertical-align:bottom; height:56px; padding:0 14px; border:2px solid #1d4ed8; border-radius:8px; background:#eff6ff; color:#1d4ed8; cursor:pointer;">pour in a bucket (add 1)</button></div>


<script>
(function () {
  var root = document.getElementById('wam');
  if (!root || root.dataset.wired) return;
  root.dataset.wired = '1';
  var locks = Array.prototype.slice.call(root.querySelectorAll('.wam-lock'));
  var total = document.getElementById('wam-total');
  function paint(b) {
    var full = b.dataset.full === '1';
    b.textContent = full ? 'full' : 'empty';
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

Here is the machine storing the number 5. Only the locks worth 4 and 1 are full, and 4 + 1 = 5:

| worth 8 | worth 4 | worth 2 | worth 1 | number stored |
| --- | --- | --- | --- | --- |
| empty | full | empty | full | 5 |

And here are six different numbers side by side. Read **down a column** to see the four locks for that number:

| Lock | Storing 0 | Storing 1 | Storing 2 | Storing 3 | Storing 4 | Storing 5 |
| --- | --- | --- | --- | --- | --- | --- |
| worth 8 | empty | empty | empty | empty | empty | empty |
| worth 4 | empty | empty | empty | empty | full | full |
| worth 2 | empty | empty | full | full | empty | empty |
| worth 1 | empty | full | empty | full | empty | full |

Writing only the fulls and empties as `1`s and `0`s, the last column is `0101`, which means 5. This way of writing numbers is called **binary**, and it is exactly how computers store numbers, using tiny switches instead of water.

With four locks, the largest number we can store is `1111` = 8 + 4 + 2 + 1 = 15. Add one more bucket and every lock empties again: the water spills out of the last lock and the machine starts back at 0. Real machines do this too; it is called **overflow**.

## 1. Read the machine

The locks show: full, empty, full, empty. In digits, that is `1010`.

What number is stored?

[[10]]

<details>
<summary>Worked answer</summary>

Full locks are worth 8 and 2. The empty ones contribute nothing.

8 + 2 = 10.

With four locks, the largest number we can store is `1111` = 8 + 4 + 2 + 1 = 15.

</details>

## One rule for adding

Here is the whole trick. To **add 1**, pour a bucket of water into the rightmost lock (the one worth 1), and follow this rule:

> When water arrives at a lock: if the lock is empty, it fills, and you stop. If the lock is full, it overflows: the lock empties, and the water flows into the next lock to the left, which follows the same rule.

Watch it add 1 to 3 (`0011`):

- A bucket arrives at the 1-lock. It is full, so it empties, and the water flows left to the 2-lock.
- The 2-lock is full, so it empties, and the water flows left to the 4-lock.
- The 4-lock is empty, so it fills. Stop.

The pattern is now `0100`, which is 4. We just computed 3 + 1 = 4.

That moment when a full lock empties and pushes water into the next lock is called a **carry**. You already know carries from school arithmetic: 7 + 5 makes 12, so you write down 2 and carry the 1. Our locks carry whenever they pass 1, because a lock can only hold 0 or 1.

## 2. Run the machine

The locks show `0111` (that is 7). Follow the rule and add 1.

What is the new pattern? Type four digits.

[[1000]]

<details>
<summary>Worked answer</summary>

- The 1-lock is full: it empties, water flows left.
- The 2-lock is full: it empties, water flows left.
- The 4-lock is full: it empties, water flows left.
- The 8-lock is empty: it fills. Stop.

The pattern `0111` becomes `1000`: the number 8. The carry rippled through three locks before finding an empty one, like a wave climbing the staircase of locks.

</details>

Adding bigger numbers, subtracting, even multiplying, can all be built from this one idea with a few more rules. Real processors do exactly this, with electricity standing in for water.

## The rule is an algorithm

Notice what kind of thing our adding rule is:

- It is a **finite** list of steps.
- Every step is **completely mechanical**: "if full, empty it and pour left" requires no judgement, no cleverness, no guesses.
- It **always works**, for any starting pattern.

A set of steps like that is called an **algorithm**: a recipe so precise that a machine, or a person following it thoughtlessly, gets the right answer every time. Long division is an algorithm. A cake recipe mostly is one. "Season to taste" is not, because it asks for judgement.

In the 1930s, the mathematician Alan Turing asked a daring question: is there a single kind of machine that could follow *any* algorithm? His answer, the **Turing machine**, is a hypothetical device that reads and writes symbols on a tape by following rules exactly as simple as our lock rule. Turing showed this humble machine can compute anything that any algorithm can compute. Every computer ever built, from the water adder to your phone, is one of these machines wearing different clothes. That claim is called the **Church-Turing thesis**, and nothing has ever broken it.

## 3. Spot the algorithm

Which of these is an algorithm?

[[X]] To add 1: pour water into the rightmost lock. If it is full, empty it and pour into the next lock left; if it is empty, fill it and stop.
[[ ]] Keep pouring water around until the answer feels right.
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

This is a surprising and powerful fact: there are questions with definite answers that no machine, however fast or large, can answer in general.

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

One more idea, and it might be the deepest.

Our water machine had its rule drilled into whoever operated it. But we could write the rule down: "when water arrives, if full then empty and pour left, else fill". Written down, the rule is just symbols on paper: **data**.

Now imagine a machine that reads its rules from the same place it keeps its numbers. Feed it the adding rules and it adds. Feed it different rules and it sorts names, or plays music, without changing a single lock or wire. That is a **universal machine**, and it is what a computer is.

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

- A working mental model of a mechanical adder: locks that are full or empty, carrying by overflow. Binary is just full-and-empty written down.
- A definition of an **algorithm**: finite, mechanical, always right.
- A boundary: some problems are **non-computable**, proven so, not just waiting for faster hardware.
- The trick that makes computers universal: the **programme is data**, stored in the same memory it works on.

Before finishing, explain these in your own words:

- Why does adding 1 to `0111` change every lock?
- What makes "season to taste" fail as an algorithm?
- Why can the same computer run a game and a spreadsheet without new hardware?

Next: [A computer you can watch](https://liascript.github.io/course/?https://raw.githubusercontent.com/LordParsley/from-the-silicon-up/main/little-machine/lesson-01.md), where we meet a real (tiny) machine that stores both its instructions and its data in memory, and we make it compute.

[Back to the course home](https://liascript.github.io/course/?https://raw.githubusercontent.com/LordParsley/from-the-silicon-up/main/course.md)

## References

- [The Halting Problem, Computerphile](https://www.youtube.com/watch?v=macM_MtS_w4) - a friendly video proof of why the loop detector cannot exist.
- [Turing's 1936 paper, "On Computable Numbers"](https://www.cs.virginia.edu/~robins/Turing_Paper_1936.pdf) - where the universal machine first appears, for the curious.
- [Pascal's calculator](https://en.wikipedia.org/wiki/Pascal%27s_calculator) - the 1642 gear-driven adding machine.
