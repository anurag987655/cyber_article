---
layout: default
title: "An introduction to number theory for cryptography"
date: 2026-01-21
categories: [updates]
---



This article provides the reader a glimpse of number theory that is required for cryptography.  
So let us ponder into the world of number theory.

<style>
/* Unified CSS for all clocks */
.modular-clock-container {
    text-align: center;
    margin: 2rem auto;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    border: 2px solid #333;
    padding: 20px;
    border-radius: 15px;
    background: white;
    max-width: 500px;
    box-shadow: 0 4px 12px rgba(0,0,0,0.1);
}

.modular-clock-container h3 {
    margin-top: 0;
    color: #2c3e50;
    border-bottom: 2px solid #eee;
    padding-bottom: 10px;
}

.modular-clock-svg {
    display: block;
    margin: 0 auto;
    width: 100%;
    max-width: 300px;
    height: auto;
}

.modular-clock-controls {
    margin: 20px 0;
}

.modular-clock-btn {
    margin: 0.3rem;
    padding: 10px 20px;
    cursor: pointer;
    background: #3498db;
    color: white;
    border: none;
    border-radius: 5px;
    font-family: inherit;
    font-size: 14px;
    transition: background 0.3s;
}

.modular-clock-btn:hover {
    background: #2980b9;
}

.modular-clock-text {
    font-weight: bold;
    font-size: 1.2rem;
    margin-top: 15px;
    color: #2c3e50;
    padding: 10px;
    background: #f8f9fa;
    border-radius: 5px;
}

/* Special styles for different clock types */
.clock-rim {
    fill: none;
    stroke: #2c3e50;
    stroke-width: 2;
}

.clock-hand {
    stroke: #e74c3c;
    stroke-width: 3;
    stroke-linecap: round;
    transition: transform 0.6s cubic-bezier(0.4, 0, 0.2, 1);
}

.clock-dot {
    fill: #2c3e50;
}

.clock-label {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    font-size: 14px;
    fill: #2c3e50;
    text-anchor: middle;
}

.coprime-dot {
    fill: #27ae60;
}

.non-coprime-dot {
    fill: #bdc3c7;
}

.highlight-dot {
    fill: #3498db;
    r: 6;
}
</style>

---

## 1) Congruence

Congruence expresses **modular equality**.

We say,

$$
a \equiv b \pmod{n}
$$

when \( n \) divides \( a - b \).

We read it as *"a is congruent to b modulo n"*.  
It simply means that \( a \) and \( b \) differ by a multiple of \( n \).

### Examples

$$
17 \equiv 5 \pmod{12}
$$

This means:

$$
17 - 5 = 12
$$
is divisible by 12.

$$
15 \equiv 1 \pmod{7}
$$

This means:

$$
15 - 1 = 14
$$
is divisible by 7.

---

## Intuition for Congruence

Let us develop intuition for congruence using a **clock**.

### Example: 

$$
14 \equiv 2 \pmod{12}
$$

Imagine a clock that has labels from 0 to 11.  
<div class="modular-clock-container">
  <h3>Modular Rotation: 14 ≡ 2 (mod 12)</h3>
  <svg id="mod-clock" class="modular-clock-svg" viewBox="0 0 300 300">
    <circle class="clock-rim" cx="150" cy="150" r="120"/>
  </svg>
  <div class="modular-clock-controls">
    <button onclick="animateMod(14)" class="modular-clock-btn">Spin to 14</button>
    <button onclick="resetMod()" class="modular-clock-btn">Reset</button>
  </div>
  <p id="mod-text" class="modular-clock-text">Position: 0</p>
</div>

<script>
  const modN = 12;
  const modSvg = document.getElementById("mod-clock");
  const modText = document.getElementById("mod-text");
  
  // Create labels
  for (let i = 0; i < modN; i++) {
    const angle = (2 * Math.PI * i) / modN - Math.PI / 2;
    const x = 150 + 100 * Math.cos(angle);
    const y = 150 + 100 * Math.sin(angle);
    const label = document.createElementNS("http://www.w3.org/2000/svg", "text");
    label.setAttribute("x", x);
    label.setAttribute("y", y + 5);
    label.setAttribute("class", "clock-label");
    label.textContent = i ;
    modSvg.appendChild(label);
  }

  // Create Hand
  const modHand = document.createElementNS("http://www.w3.org/2000/svg", "line");
  modHand.setAttribute("x1", "150");
  modHand.setAttribute("y1", "150");
  modHand.setAttribute("x2", "150");
  modHand.setAttribute("y2", "60");
  modHand.setAttribute("class", "clock-hand");
  modHand.style.transformOrigin = "150px 150px";
  modSvg.appendChild(modHand);

  function animateMod(val) {
    modHand.style.transform = `rotate(${(360/modN) * val}deg)`;
    modText.textContent = `14 mod 12 = ${val % modN} (One full lap + 2)`;
  }
  
  function resetMod() {
    modHand.style.transform = `rotate(0deg)`;
    modText.textContent = "Position: 0";
  }
</script>

Starting from 0 if you move 14 steps forward you will land at 2 in the clock.

Although 14 and 2 are different numbers, they represent the **same position** on the clock.

This allows us to think modulo arithmetic as movement on a **cycle**.

---

## Negative Numbers in Modulo

Consider:

$$
-1 \equiv 11 \pmod{12}
$$

<div class="modular-clock-container">
  <h3>Modular Clock</h3>
  <svg id="clock-neg" class="modular-clock-svg" viewBox="0 0 300 300"></svg>
  <div class="modular-clock-controls">
    <button id="btn-back" class="modular-clock-btn">-1 Step</button>
    <button id="btn-reset" class="modular-clock-btn">Reset</button>
  </div>
  <p id="clock-text" class="modular-clock-text">Position: 0</p>
</div>

<script>
  (function() {
    const n = 12;
    let currentRotation = 0;
    let logicalPos = 0;

    const svg = document.getElementById("clock-neg");
    const textDisp = document.getElementById("clock-text");
    const btnBack = document.getElementById("btn-back");
    const btnReset = document.getElementById("btn-reset");
    const center = 150;
    const radius = 110;

    // --- Drawing Logic ---
    const rim = document.createElementNS("http://www.w3.org/2000/svg", "circle");
    rim.setAttribute("cx", center); rim.setAttribute("cy", center);
    rim.setAttribute("r", radius + 15); rim.setAttribute("class", "clock-rim");
    svg.appendChild(rim);

    for (let i = 0; i < n; i++) {
      const angle = (2 * Math.PI * i) / n - Math.PI / 2;
      const x = center + radius * Math.cos(angle);
      const y = center + radius * Math.sin(angle);
      const dot = document.createElementNS("http://www.w3.org/2000/svg", "circle");
      dot.setAttribute("cx", x); dot.setAttribute("cy", y);
      dot.setAttribute("r", "4"); dot.setAttribute("class", "clock-dot");
      svg.appendChild(dot);
      const label = document.createElementNS("http://www.w3.org/2000/svg", "text");
      label.setAttribute("x", x);
      label.setAttribute("y", y + (i === 6 ? 25 : (i > 3 && i < 9 ? 20 : 15)));
      label.setAttribute("class", "clock-label");
      label.textContent = i;
      svg.appendChild(label);
    }

    const hand = document.createElementNS("http://www.w3.org/2000/svg", "line");
    hand.setAttribute("x1", center); hand.setAttribute("y1", center);
    hand.setAttribute("x2", center); hand.setAttribute("y2", center - radius);
    hand.setAttribute("class", "clock-hand");
    hand.style.transformOrigin = "150px 150px";
    svg.appendChild(hand);

    // --- Update Function ---
    function updateDisplay() {
      hand.style.transform = `rotate(${currentRotation}deg)`;
      let displayPos = logicalPos % n;
      if (displayPos < 0) displayPos += n;
      textDisp.textContent = "Position: " + displayPos;
    }

    // --- Event Listeners (Instead of onclick) ---
    // This is much more stable in Markdown environments
    btnBack.addEventListener("click", function() {
      currentRotation -= (360 / n);
      logicalPos -= 1;
      updateDisplay();
    });

    btnReset.addEventListener("click", function() {
      currentRotation = 0;
      logicalPos = 0;
      updateDisplay();
    });
    
    updateDisplay();
  })();
</script>

This means **one step backward from starting position (0)**, which is 11. 

Negative numbers simply move backward on the same cycle.

---

## An Algebraic Interpretation

Congruence can also be defined algebraically:

$$
a \equiv b \pmod{n} \iff \exists k \in \mathbb{Z} \text{ such that } a = b + kn
$$

This means that \( a \) and \( b \) differ by a multiple of \( n \).

### Example

$$
23 \equiv 5 \pmod{9}
$$

Thus,

$$
23 = 5 + 2 \cdot 9
$$

The integer **2** represents the number of complete 9-hour cycles required to reach 23 on a 9-hour clock.

---

## Conclusion on Congruence

Congruence modulo \( n \) allows us to represent the **infinite set of integers** using only **\( n \) distinct values**.

---

## 2) Residue Classes

A **residue class** is the set of all integers that are congruent to a given number modulo \( n \).

It is denoted by \( [a]_n \) and defined as:

$$
[a]_n = \{ x \in \mathbb{Z} \mid x \equiv a \pmod{n} \}
$$

### Example (Modulo 5)

$$
[1]_5 = \{ \ldots, -9, -4, 1, 6, 11, \ldots \}
$$

This means:

$$
-4 \equiv 1 \equiv 6 \equiv 11 \pmod{5}
$$

The collection of all residue classes modulo \( n \) is denoted by:

$$
\mathbb{Z}/n\mathbb{Z} = \{ [0]_n, [1]_n, \ldots, [n-1]_n \}
$$

---

## 3) Arithmetic Operations Modulo \( n \)

### Addition

In modular arithmetic, we add numbers and reduce if necessary.

$$
3 + 4 = 7 \equiv 2 \pmod{5}
$$

**Clock intuition:**  
On a clock labeled \( 0,1,2,3,4 \), think of this as  starting at 3 and moving 4 steps forward.The hand will point at 2.

**note**
You can also think of this as starting at 4 and moving 3 steps forward.

<div class="modular-clock-container">
  <svg id="clock-mod5-fixed" class="modular-clock-svg" viewBox="0 0 300 300"></svg>
  <div class="modular-clock-controls">
    <button onclick="moveFourSteps()" class="modular-clock-btn">Move +4 Steps</button>
    <button onclick="resetMod5()" class="modular-clock-btn">Reset</button>
  </div>
  <p id="clock-text-5" class="modular-clock-text">Position: 3</p>
</div>

<script>
  (function() {
    const n = 5;
    const center = 150;
    const radius = 110;
    const svg = document.getElementById("clock-mod5-fixed");
    const textDisp = document.getElementById("clock-text-5");

    let totalDegrees = 0; 
    let currentPos = 3;

    // Draw Rim
    const rim = document.createElementNS("http://www.w3.org/2000/svg", "circle");
    rim.setAttribute("cx", center);
    rim.setAttribute("cy", center);
    rim.setAttribute("r", radius + 15);
    rim.setAttribute("class", "clock-rim");
    svg.appendChild(rim);

    // Draw Labels
    for (let i = 0; i < n; i++) {
      const angle = (2 * Math.PI * i) / n - Math.PI / 2;
      const x = center + radius * Math.cos(angle);
      const y = center + radius * Math.sin(angle);
      
      const dot = document.createElementNS("http://www.w3.org/2000/svg", "circle");
      dot.setAttribute("cx", x);
      dot.setAttribute("cy", y);
      dot.setAttribute("r", "4");
      dot.setAttribute("class", "clock-dot");
      svg.appendChild(dot);

      const label = document.createElementNS("http://www.w3.org/2000/svg", "text");
      label.setAttribute("x", x);
      label.setAttribute("y", y + 20);
      label.setAttribute("class", "clock-label");
      label.textContent = i;
      svg.appendChild(label);
    }

    // Create Hand (Initially pointing at 3)
    const hand = document.createElementNS("http://www.w3.org/2000/svg", "line");
    hand.setAttribute("x1", center);
    hand.setAttribute("y1", center);
    hand.setAttribute("x2", center);
    hand.setAttribute("y2", center - radius);
    hand.setAttribute("class", "clock-hand");
    hand.style.transformOrigin = "150px 150px";
    
    // Set initial position to 3
    totalDegrees = (360 / n) * 3;
    hand.style.transform = `rotate(${totalDegrees}deg)`;
    svg.appendChild(hand);

    window.moveFourSteps = function() {
      totalDegrees += (360 / n) * 4;
      currentPos = (currentPos + 4) % n;
      hand.style.transform = `rotate(${totalDegrees}deg)`;
      textDisp.textContent = "Position: " + currentPos;
    };

    window.resetMod5 = function() {
      currentPos = 3;
      totalDegrees = (360 / n) * 3;
      hand.style.transform = `rotate(${totalDegrees}deg)`;
      textDisp.textContent = "Position: 3";
    };
  })();
</script>

---

### Multiplication

Multiplication works similarly: multiply and reduce.

$$
2 \cdot 4 = 8 \equiv 3 \pmod{5}
$$

Modular multiplication is simply **repeated modular addition**.

---

## 4) Modular Inverse

An integer \( a \) has a **modular inverse** \( b \) modulo \( n \) if:

$$
a \cdot b \equiv 1 \pmod{n}
$$

### Does Every Number Have an Inverse?

No.

An integer \( a \) has a modular inverse modulo \( n \) **if and only if**:

$$
\gcd(a, n) = 1
$$

That is, \( a \) and \( n \) must be **relatively prime**.

---

## Intuition for Modular Inverse

Imagine a clock with \( n \) hours.  
An inverse of a exists if repeated jumps of size \( a \) can land on 1.

---

### Example 1: Inverse of 3 modulo 7

Clock: \( \{0,1,2,3,4,5,6\} \)

<div class="modular-clock-container">
  <svg id="clock-mod7" class="modular-clock-svg" viewBox="0 0 300 300"></svg>
  <div class="modular-clock-controls">
    <button id="jump-btn" onclick="startJumps()" class="modular-clock-btn">Start 5 Jumps (Step: 3)</button>
    <button onclick="resetMod7()" class="modular-clock-btn">Reset</button>
  </div>
  <p id="clock-text-7" class="modular-clock-text">Position: 0</p>
</div>

<script>
  (function() {
    const n = 7;
    const stepSize = 3;
    const totalJumps = 5;
    const center = 150;
    const radius = 110;
    
    const svg = document.getElementById("clock-mod7");
    const textDisp = document.getElementById("clock-text-7");
    const btn = document.getElementById("jump-btn");

    let currentRotation = 0;
    let currentPos = 0;
    let isRunning = false;

    // Draw Rim
    const rim = document.createElementNS("http://www.w3.org/2000/svg", "circle");
    rim.setAttribute("cx", center);
    rim.setAttribute("cy", center);
    rim.setAttribute("r", radius + 15);
    rim.setAttribute("class", "clock-rim");
    svg.appendChild(rim);

    // Draw Labels 0-6
    for (let i = 0; i < n; i++) {
      const angle = (2 * Math.PI * i) / n - Math.PI / 2;
      const x = center + radius * Math.cos(angle);
      const y = center + radius * Math.sin(angle);
      
      const dot = document.createElementNS("http://www.w3.org/2000/svg", "circle");
      dot.setAttribute("cx", x);
      dot.setAttribute("cy", y);
      dot.setAttribute("r", "4");
      dot.setAttribute("class", "clock-dot");
      svg.appendChild(dot);

      const label = document.createElementNS("http://www.w3.org/2000/svg", "text");
      label.setAttribute("x", x);
      label.setAttribute("y", y + 20);
      label.setAttribute("class", "clock-label");
      label.textContent = i;
      svg.appendChild(label);
    }

    // Create Hand
    const hand = document.createElementNS("http://www.w3.org/2000/svg", "line");
    hand.setAttribute("x1", center);
    hand.setAttribute("y1", center);
    hand.setAttribute("x2", center);
    hand.setAttribute("y2", center - radius);
    hand.setAttribute("class", "clock-hand");
    hand.style.transformOrigin = "150px 150px";
    svg.appendChild(hand);

    // Staggered Jump Logic
    window.startJumps = async function() {
      if (isRunning) return;
      isRunning = true;
      btn.disabled = true;

      for (let j = 1; j <= totalJumps; j++) {
        currentRotation += (360 / n) * stepSize;
        currentPos = (currentPos + stepSize) % n;
        
        hand.style.transform = `rotate(${currentRotation}deg)`;
        textDisp.textContent = "Position: " + currentPos;
        
        await new Promise(resolve => setTimeout(resolve, 800));
      }

      isRunning = false;
      btn.disabled = false;
    };

    window.resetMod7 = function() {
      currentRotation = 0;
      currentPos = 0;
      hand.style.transform = `rotate(0deg)`;
      textDisp.textContent = "Position: 0";
    };
  })();
</script>

Starting from 0 and jumping by 3:

- 3  
- 6  
- 2  
- 5  
- **1**

It takes **5 jumps** to land on 1.

Verification:

$$
3 \cdot 5 = 15 \equiv 1 \pmod{7}
$$

So the inverse of 3 modulo 7 is **5**.

---

### Example 2: Inverse of 2 modulo 8

Jumping by 2 on an 8-hour clock:

<div class="modular-clock-container">
  <svg id="clock-mod8" class="modular-clock-svg" viewBox="0 0 300 300"></svg>
  <div class="modular-clock-controls">
    <button id="loop-btn" onclick="startLoop()" class="modular-clock-btn">Start Infinite Loop</button>
    <button onclick="stopLoop()" class="modular-clock-btn">Stop</button>
    <button onclick="resetMod8()" class="modular-clock-btn">Reset</button>
  </div>
  <p id="clock-text-8" class="modular-clock-text">Position: 0</p>
  <p id="loop-status" style="color: #666; font-size: 0.9rem; text-align: center; margin-top: 10px;">
    The hand is skipping {1, 3, 5, 7}!
  </p>
</div>

<script>
  (function() {
    const n = 8;
    const stepSize = 2;
    const center = 150;
    const radius = 110;
    
    const svg = document.getElementById("clock-mod8");
    const textDisp = document.getElementById("clock-text-8");

    let currentRotation = 0;
    let currentPos = 0;
    let timer = null;

    // Draw Rim
    const rim = document.createElementNS("http://www.w3.org/2000/svg", "circle");
    rim.setAttribute("cx", center);
    rim.setAttribute("cy", center);
    rim.setAttribute("r", radius + 15);
    rim.setAttribute("class", "clock-rim");
    svg.appendChild(rim);

    // Draw Labels 0-7
    for (let i = 0; i < n; i++) {
      const angle = (2 * Math.PI * i) / n - Math.PI / 2;
      const x = center + radius * Math.cos(angle);
      const y = center + radius * Math.sin(angle);
      
      const dot = document.createElementNS("http://www.w3.org/2000/svg", "circle");
      dot.setAttribute("cx", x);
      dot.setAttribute("cy", y);
      dot.setAttribute("r", "5");
      
      const isReachable = (i % 2 === 0);
      dot.setAttribute("class", isReachable ? "clock-dot" : "non-coprime-dot");
      svg.appendChild(dot);

      const label = document.createElementNS("http://www.w3.org/2000/svg", "text");
      label.setAttribute("x", x);
      label.setAttribute("y", y + 20);
      label.setAttribute("class", "clock-label");
      label.style.fill = isReachable ? "#2c3e50" : "#95a5a6";
      label.textContent = i;
      svg.appendChild(label);
    }

    // Create Hand
    const hand = document.createElementNS("http://www.w3.org/2000/svg", "line");
    hand.setAttribute("x1", center);
    hand.setAttribute("y1", center);
    hand.setAttribute("x2", center);
    hand.setAttribute("y2", center - radius);
    hand.setAttribute("class", "clock-hand");
    hand.style.transformOrigin = "150px 150px";
    svg.appendChild(hand);

    window.startLoop = function() {
      if (timer) return;
      timer = setInterval(() => {
        currentRotation += (360 / n) * stepSize;
        currentPos = (currentPos + stepSize) % n;
        hand.style.transform = `rotate(${currentRotation}deg)`;
        textDisp.textContent = "Position: " + currentPos;
      }, 600);
    };

    window.stopLoop = function() {
      clearInterval(timer);
      timer = null;
    };

    window.resetMod8 = function() {
      stopLoop();
      currentRotation = 0;
      currentPos = 0;
      hand.style.transform = `rotate(0deg)`;
      textDisp.textContent = "Position: 0";
    };
  })();
</script>

$$
0 \to 2 \to 4 \to 6 \to 0 \to \dots
$$

The values repeat and never reach 1.  
Thus, **2 has no inverse modulo 8**.

---

## Euler's Totient Function

For a positive integer \( n \), Euler's totient function \( \varphi(n) \) is defined as:

$$
\varphi(n) = \text{number of integers } a \text{ such that } 1 \le a \le n \text{ and } \gcd(a,n)=1
$$

It simply counts the number of integers relatively prime to a positive integer $a$ which are less than or equal to $a$

### Examples

$$
\varphi(7) = 6 \quad \{1,2,3,4,5,6\}
$$

$$
\varphi(10) = 4 \quad \{1,3,7,9\}
$$

---

## Properties of Euler's Totient Function

1. If \( p \) is prime:

$$
\varphi(p) = p - 1
$$

2. If \( gcd(m,n) = 1 \):

$$
\varphi(mn) = \varphi(m)\varphi(n)
$$

3. For a prime power:

$$
\varphi(p^k) = p^k - p^{k-1} = p^k \left(1 - \frac{1}{p}\right)
$$

If:

$$
n = p_1^{k_1} p_2^{k_2} \cdots p_r^{k_r}
$$

then:

$$
\varphi(n) = n \left(1-\frac{1}{p_1}\right)\left(1-\frac{1}{p_2}\right)\cdots\left(1-\frac{1}{p_r}\right)
$$

---

## Euler's Theorem

If \( gcd(a,m) = 1 \), then:

$$
a^{\varphi(m)} \equiv 1 \pmod{m}
$$

### Example

$$
\varphi(15) = 8,\quad \gcd(2,15)=1
$$

$$
2^8 = 256 = 15 \cdot 17 + 1 \equiv 1 \pmod{15}
$$

---

## Clock Intuition for Euler's Theorem

For modulo 10, the numbers coprime to 10 are:

$$
\{1,3,7,9\}
$$

If we start at 1 and keep only coprime numbers on the clock and perform multiplicative jumping by any coprime step, after $\phi(12)=4$ jumps, we return to 1.

**note**:This holds true even if we start at any coprime number and take coprime multiplicative jump we are guarenteed to land at that initial coprime number position.

<div class="modular-clock-container">
  <h3>Modulo 10 Experiment</h3>
  <p style="text-align: center;">$\phi(10) = 4$. After 4 jumps, Coprimes hit 1.</p>
  
  <svg id="mod10-clock" class="modular-clock-svg" viewBox="0 0 300 300"></svg>

  <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin: 15px 0;">
    <div style="background: #e8f5e9; padding: 10px; border-radius: 8px;">
      <p style="margin: 0 0 5px 0; font-weight: bold; color: #27ae60;">Coprime (Success)</p>
      <button onclick="setA(3)" class="modular-clock-btn" style="width: 100%; margin: 2px 0;">3</button>
      <button onclick="setA(7)" class="modular-clock-btn" style="width: 100%; margin: 2px 0;">7</button>
      <button onclick="setA(9)" class="modular-clock-btn" style="width: 100%; margin: 2px 0;">9</button>
    </div>
    <div style="background: #ffebee; padding: 10px; border-radius: 8px;">
      <p style="margin: 0 0 5px 0; font-weight: bold; color: #e74c3c;">Not Coprime (Fail)</p>
      <button onclick="setA(2)" class="modular-clock-btn" style="width: 100%; margin: 2px 0; background: #95a5a6;">2</button>
      <button onclick="setA(4)" class="modular-clock-btn" style="width: 100%; margin: 2px 0; background: #95a5a6;">4</button>
      <button onclick="setA(5)" class="modular-clock-btn" style="width: 100%; margin: 2px 0; background: #95a5a6;">5</button>
    </div>
  </div>

  <button onclick="doJump()" class="modular-clock-btn" style="width: 100%; padding: 12px; font-size: 1.1rem;">
    Jump (×<span id="a-display">?</span>)
  </button>
  
  <p id="m10-status" class="modular-clock-text">Select a number</p>
  <button onclick="resetM10()" style="background:none; border:none; text-decoration:underline; cursor:pointer; color:#666; font-family: inherit;">
    Reset Clock
  </button>
</div>

<script>
(function() {
  const n = 10;
  const svg = document.getElementById("mod10-clock");
  const status = document.getElementById("m10-status");
  const aDisp = document.getElementById("a-display");
  
  let currentPos = 1;
  let totalDeg = 36; // 1 o'clock in a 10-point clock (360/10)
  let currentA = null;
  let hand;

  function init() {
    // Clear SVG
    svg.innerHTML = '';
    
    // Draw rim
    const rim = document.createElementNS("http://www.w3.org/2000/svg", "circle");
    rim.setAttribute("cx", "150");
    rim.setAttribute("cy", "150");
    rim.setAttribute("r", "120");
    rim.setAttribute("class", "clock-rim");
    svg.appendChild(rim);
    
    // Draw numbers
    for (let i = 1; i <= 10; i++) {
      const val = i === 10 ? 0 : i;
      const angle = (i * 36 - 90) * (Math.PI / 180);
      const x = 150 + 100 * Math.cos(angle);
      const y = 150 + 100 * Math.sin(angle);
      
      const isCoprime = [1, 3, 7, 9].includes(val);
      const dot = document.createElementNS("http://www.w3.org/2000/svg", "circle");
      dot.setAttribute("cx", x);
      dot.setAttribute("cy", y);
      dot.setAttribute("r", isCoprime ? "6" : "3");
      dot.setAttribute("class", isCoprime ? "coprime-dot" : "non-coprime-dot");
      svg.appendChild(dot);

      const t = document.createElementNS("http://www.w3.org/2000/svg", "text");
      t.setAttribute("x", x);
      t.setAttribute("y", y + 20);
      t.setAttribute("class", "clock-label");
      t.textContent = val;
      svg.appendChild(t);
    }
    
    // Create hand
    hand = document.createElementNS("http://www.w3.org/2000/svg", "line");
    hand.setAttribute("x1", "150");
    hand.setAttribute("y1", "150");
    hand.setAttribute("x2", "150");
    hand.setAttribute("y2", "50");
    hand.setAttribute("class", "clock-hand");
    hand.style.transformOrigin = "150px 150px";
    svg.appendChild(hand);
    
    updateH();
  }

  function updateH() {
    hand.style.transform = `rotate(${totalDeg}deg)`;
  }

  window.setA = function(val) {
    currentA = val;
    aDisp.textContent = val;
    resetM10();
    status.textContent = "Ready to jump by " + val;
    status.style.color = "#2c3e50";
  };

  window.doJump = function() {
    if (!currentA) {
      alert("Select a multiplier first!");
      return;
    }
    const old = currentPos;
    currentPos = (currentPos * currentA) % n;
    
    // Calculate clockwise distance
    let diff = (currentPos === 0 ? 10 : currentPos) - (old === 0 ? 10 : old);
    if (diff <= 0) diff += 10;
    
    totalDeg += (diff * 36);
    updateH();
    status.textContent = `Result: ${currentPos}`;
    status.style.color = (currentPos === 1) ? "#27ae60" : ([1,3,7,9].includes(currentPos) ? "#2c3e50" : "#e74c3c");
  };

  window.resetM10 = function() {
    currentPos = 1;
    totalDeg = 36;
    updateH();
    status.textContent = currentA ? "Started at 1" : "Select a number";
    status.style.color = "#2c3e50";
  };

  init();
})();
</script>

---

## Fermat's Little Theorem

For a prime number \( p \) and \( gcd(a,p)=1 \):

$$
a^{p-1} \equiv 1 \pmod{p}
$$

This follows directly from Euler's theorem.

### Clock Intuition

Imagine a clock with \( p-1 \) hours.  
Starting at 1 and multiplicative jumping by \( a \), after exactly \( p-1 \) jumps, you return to 1.

<div class="modular-clock-container">
  <h3>Fermat's Proof ($3^6 \equiv 1 \pmod 7$)</h3>
  
  <svg id="fermat-fixed" class="modular-clock-svg" viewBox="0 0 300 300"></svg>

  <div class="modular-clock-controls">
    <button id="btn-auto" onclick="autoCycle()" class="modular-clock-btn">Run Full 6 Jumps (×3)</button>
    <div style="margin-top: 10px;">
      <button id="btn-jump" onclick="manualJump()" class="modular-clock-btn">Manual Jump (×3)</button>
      <button id="btn-reset" onclick="resetClock()" class="modular-clock-btn">Reset</button>
    </div>
  </div>

  <p id="label-pos" class="modular-clock-text">Position: 1</p>
  <p id="label-math" style="font-style: italic; color: #666; font-size: 0.9rem; height: 1.2rem; text-align: center;">
    Ready
  </p>
</div>

<script>
(function() {
  const p = 7;
  const a = 3;
  const svg = document.getElementById("fermat-fixed");
  const posDisp = document.getElementById("label-pos");
  const mathDisp = document.getElementById("label-math");
  
  const cx = 150, cy = 150, radius = 90;
  let currentPos = 1;
  let totalRotation = 360 / p; // Hand starts at 1
  let isRunning = false;
  let hand;

  // Initial Draw
  function setup() {
    // Clear SVG
    svg.innerHTML = '';
    
    // Draw rim
    const rim = document.createElementNS("http://www.w3.org/2000/svg", "circle");
    rim.setAttribute("cx", cx);
    rim.setAttribute("cy", cy);
    rim.setAttribute("r", radius + 15);
    rim.setAttribute("class", "clock-rim");
    svg.appendChild(rim);
    
    // Draw numbers
    for (let i = 0; i < p; i++) {
      const angle = (i * (360 / p) - 90) * (Math.PI / 180);
      const x = cx + radius * Math.cos(angle);
      const y = cy + radius * Math.sin(angle);

      const dot = document.createElementNS("http://www.w3.org/2000/svg", "circle");
      dot.setAttribute("cx", x);
      dot.setAttribute("cy", y);
      dot.setAttribute("r", i === 1 ? "8" : "4");
      dot.setAttribute("class", i === 1 ? "highlight-dot" : (i === 0 ? "non-coprime-dot" : "clock-dot"));
      svg.appendChild(dot);

      const text = document.createElementNS("http://www.w3.org/2000/svg", "text");
      text.setAttribute("x", x);
      text.setAttribute("y", y + (i === 0 ? -15 : 25));
      text.setAttribute("class", "clock-label");
      text.textContent = i;
      svg.appendChild(text);
    }

    // Create hand
    hand = document.createElementNS("http://www.w3.org/2000/svg", "line");
    hand.setAttribute("x1", cx);
    hand.setAttribute("y1", cy);
    hand.setAttribute("x2", cx);
    hand.setAttribute("y2", cy - radius);
    hand.setAttribute("class", "clock-hand");
    hand.style.transformOrigin = "150px 150px";
    hand.style.transform = `rotate(${totalRotation}deg)`;
    svg.appendChild(hand);
  }

  // The Logic
  window.manualJump = function() {
    if (isRunning) return;
    performJump();
  };

  function performJump() {
    const oldPos = currentPos;
    currentPos = (currentPos * a) % p;
    
    // Logic for distance: how many numbers forward on the clock face?
    let steps = currentPos - oldPos;
    if (steps <= 0) steps += p;

    totalRotation += (steps * (360 / p));
    hand.style.transform = `rotate(${totalRotation}deg)`;
    
    posDisp.textContent = "Position: " + currentPos;
    mathDisp.textContent = `${oldPos} × 3 ≡ ${currentPos} (mod 7)`;
    
    if (currentPos === 1) {
      posDisp.style.color = "#27ae60";
    } else {
      posDisp.style.color = "#3498db";
    }
  }

  window.autoCycle = async function() {
    if (isRunning) return;
    isRunning = true;
    const btn = document.getElementById("btn-auto");
    btn.disabled = true;
    
    // Internal reset without unlocking
    currentPos = 1;
    totalRotation = 360 / p;
    hand.style.transform = `rotate(${totalRotation}deg)`;
    posDisp.textContent = "Position: 1";
    posDisp.style.color = "#3498db";
    mathDisp.textContent = "Starting...";
    
    await new Promise(r => setTimeout(r, 600));

    for (let i = 1; i <= 6; i++) {
      performJump();
      await new Promise(r => setTimeout(r, 1000));
    }
    
    mathDisp.textContent = `6 Jumps Complete: 3^6 ≡ 1`;
    isRunning = false;
    btn.disabled = false;
  };

  window.resetClock = function() {
    if (isRunning) return;
    currentPos = 1;
    totalRotation = 360 / p;
    hand.style.transform = `rotate(${totalRotation}deg)`;
    posDisp.textContent = "Position: 1";
    posDisp.style.color = "#3498db";
    mathDisp.textContent = "Ready";
  };

  setup();
})();
</script>

---

*End of article.*