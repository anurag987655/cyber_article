---
layout: default
title: "Classical Cipher Part-II"
date: 2026-02-06
categories: [updates]
---


This article will introduce readers with some more classical cipher. This article will introduce polyalphabetic cipher where cryptographic algorithm uses a vector key. 

---

<style>
    /* GLOBAL STYLES */
    body {
        font-family: -apple-system, Segoe UI, Helvetica, Arial, sans-serif;
        color: #24292e;
        max-width: 700px;
        margin: 40px auto;
        line-height: 1.5;
        padding: 20px;
        background-color: #f6f8fa;
    }
    
    /* Common section styles */
    .module-section {
        margin-bottom: 60px;
        padding: 20px;
        border: 1px solid #e1e4e8;
        border-radius: 8px;
        background-color: #ffffff;
        box-shadow: 0 1px 3px rgba(0,0,0,0.04);
    }
    
    .module-title {
        font-size: 1.25rem;
        font-weight: 600;
        margin-top: 0;
        padding-bottom: 10px;
        border-bottom: 2px solid #2ea44f;
        color: #1b1f23;
    }
    
    /* Vigenere specific */
    .decrypt-title { 
        border-bottom-color: #0366d6; 
    }
    
    /* Playfair specific */
    .playfair-title { 
        border-bottom-color: #f66a0a; 
    }
    
    .playfair-decrypt-title { 
        border-bottom-color: #6f42c1; 
    }
    
    label { 
        display: block; 
        font-size: 12px; 
        font-weight: bold; 
        text-transform: uppercase;
        color: #586069; 
        margin-bottom: 5px; 
        margin-top: 15px;
    }
    
    input {
        width: 100%;
        padding: 10px;
        border: 1px solid #d1d5da;
        border-radius: 4px;
        font-family: monospace;
        box-sizing: border-box;
        margin-bottom: 5px;
        font-size: 14px;
    }
    
    button {
        cursor: pointer;
        padding: 10px 20px;
        border-radius: 4px;
        border: none;
        font-weight: bold;
        color: white;
        margin-top: 10px;
        width: 100%;
        transition: opacity 0.2s;
    }
    
    button:hover {
        opacity: 0.9;
    }
    
    /* Vigenere buttons */
    .btn-enc { 
        background: #2ea44f; 
    }
    
    .btn-dec { 
        background: #0366d6; 
    }
    
    /* Playfair buttons */
    .btn-playfair-enc { 
        background: #f66a0a; 
    }
    
    .btn-playfair-dec { 
        background: #6f42c1; 
    }
    
    .result-display {
        margin-top: 20px;
        padding: 15px;
        background: #f6f8fa;
        border-radius: 4px;
        font-family: monospace;
        min-height: 24px;
        word-break: break-all;
        border: 1px solid #e1e4e8;
    }
    
    /* Playfair matrix styles */
    .matrix-container {
        display: grid;
        grid-template-columns: repeat(5, 40px);
        gap: 5px;
        margin: 15px 0;
        justify-content: center;
    }
    
    .cell {
        width: 40px;
        height: 40px;
        border: 1px solid #e1e4e8;
        display: flex;
        align-items: center;
        justify-content: center;
        background: #f6f8fa;
        font-family: monospace;
        font-weight: bold;
        border-radius: 3px;
    }
    
    .loading { 
        animation: pulse 0.8s infinite; 
        color: #6a737d; 
    }
    
    @keyframes pulse { 
        0%, 100% { opacity: 1; } 
        50% { opacity: 0.4; } 
    }
    
    /* Typography */
    h1, h2, h3 {
        margin-top: 24px;
        margin-bottom: 16px;
    }
    
    h2 {
        border-bottom: 1px solid #eaecef;
        padding-bottom: 8px;
    }
    
    code {
        background-color: rgba(27,31,35,0.05);
        border-radius: 3px;
        font-family: SFMono-Regular,Consolas,Menlo,monospace;
        padding: 0.2em 0.4em;
    }
    
    table {
        border-collapse: collapse;
        width: 100%;
        margin: 16px 0;
    }
    
    th, td {
        border: 1px solid #dfe2e5;
        padding: 8px 12px;
        text-align: left;
    }
    
    th {
        background-color: #f6f8fa;
    }
</style>

## The Vigenère cipher:

The Vigenère cipher is upgraded version of Caesar cipher that uses repeating keyword instead of single fixed keyword.

### Mathematical Formulation:

Let the plaintext (P), ciphertext (C), and key (K) symbols belong to

$$
\mathbb{Z}_{26} = \{0,1,2,\ldots,25\}.
$$

The key is a vector and is defined as

$$
K = (k_1, k_2, k_3, \ldots, k_m),
$$

where each $k_i \in \mathbb{Z}_{26}$.

The key is repeated cyclically to match the entire length of the plaintext.

Let the plaintext be

$$
P = (x_1, x_2, \ldots, x_n),
$$

where each $x_i \in \mathbb{Z}_{26}$.

---

The encryption function is

$$
E(x_i) = (x_i + k_i) \bmod 26 = y_i.
$$

---

The decryption function is

$$
D(y_i) = (y_i - k_i) \bmod 26 = x_i.
$$

---

### Example:

Let the plaintext be

$$
P = \text{APPLE}.
$$

So we already know as per fixed alphabet mapping APPLE gets mapped to following:

$$
P = (0, 15, 15, 11, 4).
$$

Let the key be

$$
K = \text{KEY}.
$$

Using the same mapping:

$$
K \mapsto (10, 4, 24).
$$

There are 5 values in plaintext. So we will repeat value on keyvector to make its length 5 by repeating.

$$
K = (10, 4, 24, 10, 4).
$$

---

Applying the encryption function:

| $x_i$ (Plain) | $k_i$ (Key) | $x_i + k_i$ | $\bmod 26$ | Cipher |
|--------------|-----------|-----------|-----------|--------|
| 0 (A) | 10 (K) | 10 | 10 | K |
| 15 (P) | 4 (E) | 19 | 19 | T |
| 15 (P) | 24 (Y) | 39 | 13 | N |
| 11 (L) | 10 (K) | 21 | 21 | V |
| 4 (E) | 4 (E) | 8 | 8 | I |

So the ciphertext is

$$
C = \text{KTNVI}.
$$

---

Applying the decryption function:

| $y_i$ (Cipher) | $k_i$ (Key) | $y_i - k_i$ | $\bmod 26$ | Plain |
|---------------|-----------|-----------|-----------|--------|
| 10 (K) | 10 (K) | 0 | 0 | A |
| 19 (T) | 4 (E) | 15 | 15 | P |
| 13 (N) | 24 (Y) | -11 | 15 | P |
| 21 (V) | 10 (K) | 11 | 11 | L |
| 8 (I) | 4 (E) | 4 | 4 | E |

Thus, the original plaintext is recovered:

$$
P = \text{APPLE}.
$$

### Interactive Vigenère cipher:
Try out Vigenère cipher for encryption and decryption:

<div class="module-section">
    <h2 class="module-title">1. Plaintext to Ciphertext</h2>
    <label>Encryption Key</label>
    <input type="text" id="enc_key" placeholder="Key...">
    <label>Plaintext Message</label>
    <input type="text" id="enc_input" placeholder="Message...">
    <button class="btn-enc" onclick="processVigenere('enc')">Execute Encryption</button>
    <div class="result-display" id="enc_res">Waiting for data...</div>
</div>

<div class="module-section">
    <h2 class="module-title decrypt-title">2. Ciphertext to Plaintext</h2>
    <label>Decryption Key</label>
    <input type="text" id="dec_key" placeholder="Key...">
    <label>Encrypted Ciphertext</label>
    <input type="text" id="dec_input" placeholder="Ciphertext...">
    <button class="btn-dec" onclick="processVigenere('dec')">Execute Decryption</button>
    <div class="result-display" id="dec_res">Waiting for data...</div>
</div>

<script>
    function vigenereLogic(text, key, isEncrypt) {
        if (!key || !text) return "ERROR: MISSING INPUT";
        
        let output = "";
        let keyIndex = 0;
        const cleanKey = key.toUpperCase().replace(/[^A-Z]/g, "");

        for (let i = 0; i < text.length; i++) {
            let char = text[i];
            
            if (char.match(/[a-z]/i)) {
                const isUpper = char === char.toUpperCase();
                const code = char.toUpperCase().charCodeAt(0) - 65;
                const k = cleanKey.charCodeAt(keyIndex % cleanKey.length) - 65;
                
                let shift = isEncrypt ? (code + k) % 26 : (code - k + 26) % 26;
                
                let resultChar = String.fromCharCode(shift + 65);
                output += isUpper ? resultChar : resultChar.toLowerCase();
                
                keyIndex++;
            } else {
                output += char;
            }
        }
        return output;
    }

    function processVigenere(mode) {
        const key = document.getElementById(mode + '_key').value;
        const input = document.getElementById(mode + '_input').value;
        const display = document.getElementById(mode + '_res');

        display.innerHTML = "<span class='loading'>Processing case-sensitive shift...</span>";

        setTimeout(() => {
            display.innerText = vigenereLogic(input, key, mode === 'enc');
        }, 600);
    }
</script>

---

## Playfair Cipher

The Playfair cipher is quite different from the ciphers we have seen so far.  
Instead of encrypting single letters, it encrypts **pairs of letters (digraphs)**.  
It uses a **5 × 5 matrix** of letters as the main structure for both encryption and decryption.

---

### How to Generate the 5 × 5 Matrix

1. Choose a **keyword** and remove all repeated letters from it.

2. Fill the matrix **row-wise** using the letters of the keyword first.

3. After the keyword is filled, insert the remaining letters of the alphabet
   (in alphabetical order) that do **not** already appear in the key.

4. Since the English alphabet has 26 letters and the grid has only 25 cells,  
   the letters **I and J are treated as the same letter** and share one cell.

   <br>

   **NOTE: You can use interactive demonstration below to generate the 5*5 matrix and practice to learn how it is done.**

---

### Preparing the Plaintext

Before encryption, the message must be prepared:

1. Split the plaintext into **pairs of letters**.

2. If a pair contains the **same letter twice**, insert **X** between them  
   and then continue pairing.

   Example:  
   BALLOON → BA LX LO ON

3. If the final pair contains only **one letter**, append **X** to complete it.

---

### Rules for Encryption

Let the two letters of a pair be located in the matrix.

1. **Same Row**  
   Replace each letter with the letter **to its right**  
   (wrap around to the beginning if needed).

2. **Same Column**  
   Replace each letter with the letter **below it**  
   (wrap around to the top if needed).

3. **Different Row and Column (Rectangle Rule)**  
   Replace each letter with the letter in the **same row**  
   but in the **column of the other letter**.

---

### Rules for Decryption

The rules are the reverse of encryption:

1. **Same Row**  
   Replace each letter with the letter **to its left**.

2. **Same Column**  
   Replace each letter with the letter **above it**.

3. **Rectangle Rule**  
   Use the same rectangle method as encryption  
   (rows stay the same, columns are swapped).

---

### Example:

Let the keyword be:
**APPLE** 

and the plaintext be : 
**I AM LEARNING CRYPTOGRAPHY.**

First remove repeated letters in keyspace and then construction 5 * 5 matrix :

| A | P | L | E | B |
|---|---|---|---|---|
| C | D | F | G | H |
| I/J | K | M | N | O |
| Q | R | S | T | U |
| V | W | X | Y | Z |

---

#### Preparing Plaintext :

Remove spaces from plaintext and convert to digraphs according to the rules from **Preparing the Message** . The plaintext becomes:

<br>

**IA ML EA RN IN GC RY PT OG RA PH YX**

<br>

---

#### Encrypt Each Pair

Apply the rule of encryption:

| Pair | Rule | Cipher | Logic |
|------|------|--------|-------------------|
| IA | Same Column | QC | I is below A. I moves down to Q, A moves down to C. |
| ML | Same Column | SF | M is below L. M moves down to S, L moves down to F. |
| EA | Same Row | BP | E moves right to B, A moves right to P. |
| RN | Rectangle | TK | R(4,2) and N(3,4). Corners: T and K. |
| IN | Same Row | KO | I moves right to K, N moves right to O. |
| GC | Same Row | HD | G moves right to H, C moves right to D. |
| RY | Rectangle | TW | R(4,2) and Y(5,4). Corners: T and W. |
| PT | Rectangle | ER | P(1,2) and T(4,4). Corners: E and R. |
| OG | Rectangle | NH | O(3,5) and G(2,4). Corners: N and H. |
| RA | Rectangle | QP | R(4,2) and A(1,1). Corners: Q and P. |
| PH | Rectangle | BD | P(1,2) and H(2,5). Corners: B and D. |
| YX | Same Row | ZY | Y moves right to Z, X moves right to Y. |

so finally the obtained ciphertext is **QCSFBPTKKOHDTWERNHQPBDZY**

<br>

---

**TRY OUT THE DECRYPTION!!**

---

### Interactive Playfair cipher:

Try out interactive Playfair cipher for encryption and decryption:

<div class="module-section">
    <h2 class="module-title playfair-title">1. Playfair Encryption</h2>
    <label>Keyword (Matrix Generation)</label>
    <input type="text" id="playfair_enc_key" placeholder="Enter key..." oninput="updatePlayfairMatrix('enc')">
    
    <div class="matrix-container" id="playfair_enc_matrix"></div>

    <label>Plaintext Message</label>
    <input type="text" id="playfair_enc_input" placeholder="Enter message...">
    
    <button class="btn-playfair-enc" onclick="processPlayfair('enc')">Execute Playfair Encryption ➔</button>
    <div class="result-display" id="playfair_enc_res">Waiting for data...</div>
</div>

<div class="module-section">
    <h2 class="module-title playfair-decrypt-title">2. Playfair Decryption</h2>
    <label>Keyword</label>
    <input type="text" id="playfair_dec_key" placeholder="Enter key..." oninput="updatePlayfairMatrix('dec')">
    
    <div class="matrix-container" id="playfair_dec_matrix"></div>

    <label>Ciphertext</label>
    <input type="text" id="playfair_dec_input" placeholder="Enter ciphertext...">
    
    <button class="btn-playfair-dec" onclick="processPlayfair('dec')">Execute Playfair Decryption ➔</button>
    <div class="result-display" id="playfair_dec_res">Waiting for data...</div>
</div>

<script>
    function getPlayfairMatrix(key) {
        key = key.toUpperCase().replace(/J/g, "I").replace(/[^A-Z]/g, "");
        let alphabet = "ABCDEFGHIKLMNOPQRSTUVWXYZ";
        let full = [...new Set((key + alphabet).split(""))].slice(0, 25);
        return full;
    }

    function updatePlayfairMatrix(mode) {
        const key = document.getElementById(`playfair_${mode}_key`).value;
        const matrix = getPlayfairMatrix(key);
        const container = document.getElementById(`playfair_${mode}_matrix`);
        container.innerHTML = matrix.map(char => `<div class="cell">${char}</div>`).join('');
    }

    function playfairLogic(text, key, isEncrypt) {
        const m = getPlayfairMatrix(key);
        let cleanText = text.toUpperCase().replace(/J/g, "I").replace(/[^A-Z]/g, "");

        let prepared = cleanText;
        if (isEncrypt) {
            prepared = "";
            for (let i = 0; i < cleanText.length; i += 2) {
                let a = cleanText[i];
                let b = cleanText[i + 1];
                if (!b) {
                    prepared += a + "X";
                } else if (a === b) {
                    prepared += a + "X";
                    i--;
                } else {
                    prepared += a + b;
                }
            }
        }

        let out = "";
        const move = isEncrypt ? 1 : -1;

        for (let i = 0; i < prepared.length; i += 2) {
            let a = prepared[i], b = prepared[i + 1];
            let i1 = m.indexOf(a), i2 = m.indexOf(b);
            let r1 = Math.floor(i1/5), c1 = i1%5;
            let r2 = Math.floor(i2/5), c2 = i2%5;

            if (r1 === r2) {
                out += m[r1 * 5 + (c1 + move + 5) % 5] + m[r2 * 5 + (c2 + move + 5) % 5];
            } else if (c1 === c2) {
                out += m[((r1 + move + 5) % 5) * 5 + c1] + m[((r2 + move + 5) % 5) * 5 + c2];
            } else {
                out += m[r1 * 5 + c2] + m[r2 * 5 + c1];
            }
        }

        if (!isEncrypt) {
            let healed = "";
            for (let i = 0; i < out.length; i++) {
                let current = out[i];
                let prev = out[i-1];
                let next = out[i+1];
                
                if (current === "X") {
                    if (prev && next && prev === next) continue;
                    if (i === out.length - 1) continue;
                }
                healed += current;
            }
            return healed;
        }

        return out;
    }

    function processPlayfair(mode) {
        const key = document.getElementById(`playfair_${mode}_key`).value;
        const input = document.getElementById(`playfair_${mode}_input`).value;
        const display = document.getElementById(`playfair_${mode}_res`);

        display.innerHTML = "<span class='loading'>Processing...</span>";

        setTimeout(() => {
            display.innerText = playfairLogic(input, key, mode === 'enc');
        }, 400);
    }

    // Initialize matrices
    updatePlayfairMatrix('enc');
    updatePlayfairMatrix('dec');
</script>

---

## Conclusion:

In this part, we explored some advanced classical ciphers, however, they remain vulnerable to cryptanalysis. In the upcoming article, we will look at more classical cipher and eventually identify vulnerability in classical cipher.

<br>

---

*End of article.*