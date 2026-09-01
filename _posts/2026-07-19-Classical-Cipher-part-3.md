---
layout: default
title: "Classical Cipher Part-III"
date: 2026-07-19
categories: [updates]
---
This article will introduce readers with more classical cipher especially Hill Cipher and Transposition Cipher and the idea of one time pad

<style>
.math-table {
    width: 100%;
    border-collapse: collapse;
    margin: 20px 0;
    font-family: sans-serif;
    border: 1px solid #ddd;
}

.math-table th {
    background-color: #f2f2f2;
    padding: 12px 8px;
    text-align: center;
    border: 1px solid #ddd;
    font-weight: bold;
}

.math-table td {
    padding: 10px 8px;
    text-align: center;
    border: 1px solid #ddd;
}

.math-table tr:nth-child(even) {
    background-color: #f9f9f9;
}
</style>

---

## Hill Cipher: 

Hill cipher is polygraphic substitution cipher that encrypts the plaintext using matrix multiplication. 

### Mathematical formulation: 

Let the plaintext (P), ciphertext (C), and key (K) symbols belong to

$$
\mathbb{Z}_{26} = \{0,1,2,\ldots,25\}.
$$

Let P contains the message which has a length of n.

The key is a square matrix of order $n$ * $n$ which is invertible. It is defined as
$$
K = \begin{pmatrix}
k_{11} & k_{12} & \cdots & k_{1n} \\
k_{21} & k_{22} & \cdots & k_{2n} \\
\vdots & \vdots & \ddots & \vdots \\
k_{n1} & k_{n2} & \cdots & k_{nn}
\end{pmatrix},
$$

The entry $k_{ij} \in \mathbb{Z}_{26}$

The encryption is given by the transformation:

$$
C = K \cdot P \bmod 26
$$

The decryption is given by the inverse transformation:

$$
P = K^{-1} \cdot C \bmod 26
$$

**Note** : We can also encrypt message of any size by breaking it into the size of length $n$. The idea is demonstrated in following example.

### Example

Let us encrypt the plaintext **"HELP"** using a $$2 \times 2$$ key matrix.

First, We map each letter to its numeric value in $$\mathbb{Z}_{26}$$:

$$
H \mapsto 7,\; E \mapsto 4,\; L \mapsto 11,\; P \mapsto 15
$$

Choose the key matrix:

$$
K = \begin{pmatrix} 3 & 3 \\ 2 & 5 \end{pmatrix}
$$

Since $$\det(K) = (3\cdot5 - 3\cdot2) = 9$$ and $$\gcd(9, 26) = 1$$, the matrix is invertible modulo 26.

Divide the plaintext into blocks of size 2:

$$
p_1 = \begin{pmatrix} 7 \\ 4 \end{pmatrix},\quad
p_2 = \begin{pmatrix} 11 \\ 15 \end{pmatrix}
$$

**Encryption:**

<table class="math-table">
<thead>
<tr>
<th>Block</th>
<th>Plain Vector</th>
<th>$$K \cdot p$$</th>
<th>$$\bmod 26$$</th>
<th>Cipher</th>
</tr>
</thead>
<tbody>
<tr>
<td>1</td>
<td>(7, 4)</td>
<td>(33, 34)</td>
<td>(7, 8)</td>
<td>HI</td>
</tr>
<tr>
<td>2</td>
<td>(11, 15)</td>
<td>(78, 97)</td>
<td>(0, 19)</td>
<td>AT</td>
</tr>
</tbody>
</table>

So the ciphertext is **HIAT**.

**Decryption:**

The inverse of $$K$$ modulo 26 is:

$$
K^{-1} = \begin{pmatrix} 15 & 17 \\ 20 & 9 \end{pmatrix}
$$

<table class="math-table">
<thead>
<tr>
<th>Block</th>
<th>Cipher Vector</th>
<th>$$K^{-1} \cdot c$$</th>
<th>$$\bmod 26$$</th>
<th>Plain</th>
</tr>
</thead>
<tbody>
<tr>
<td>1</td>
<td>(7, 8)</td>
<td>(241, 212)</td>
<td>(7, 4)</td>
<td>HE</td>
</tr>
<tr>
<td>2</td>
<td>(0, 19)</td>
<td>(323, 171)</td>
<td>(11, 15)</td>
<td>LP</td>
</tr>
</tbody>
</table>

The original plaintext **HELP** is recovered.

### Interactive Hill Cipher:

<div class="module-section">
    <h2 class="module-title">1. Hill Encryption</h2>
    <label>Key Matrix (2x2)</label>
    <div style="margin: 5px 0;">
        <div style="display: flex; gap: 5px; margin-bottom: 4px;">
            <input type="number" id="hill_enc_a" value="3" min="0" max="25" class="cipher-key" style="width: 60px;">
            <input type="number" id="hill_enc_b" value="3" min="0" max="25" class="cipher-key" style="width: 60px;">
        </div>
        <div style="display: flex; gap: 5px;">
            <input type="number" id="hill_enc_c" value="2" min="0" max="25" class="cipher-key" style="width: 60px;">
            <input type="number" id="hill_enc_d" value="5" min="0" max="25" class="cipher-key" style="width: 60px;">
        </div>
    </div>
    <label>Plaintext (even length, letters only)</label>
    <input type="text" id="hill_enc_input" placeholder="e.g. HELP..." class="cipher-input">
    <button class="btn-enc" onclick="processHill('enc')">Encrypt</button>
    <div class="result-display" id="hill_enc_res">Waiting for data...</div>
</div>

<div class="module-section">
    <h2 class="module-title decrypt-title">2. Hill Decryption</h2>
    <label>Key Matrix (2x2) — must be invertible mod 26</label>
    <div style="margin: 5px 0;">
        <div style="display: flex; gap: 5px; margin-bottom: 4px;">
            <input type="number" id="hill_dec_a" value="3" min="0" max="25" class="cipher-key" style="width: 60px;">
            <input type="number" id="hill_dec_b" value="3" min="0" max="25" class="cipher-key" style="width: 60px;">
        </div>
        <div style="display: flex; gap: 5px;">
            <input type="number" id="hill_dec_c" value="2" min="0" max="25" class="cipher-key" style="width: 60px;">
            <input type="number" id="hill_dec_d" value="5" min="0" max="25" class="cipher-key" style="width: 60px;">
        </div>
    </div>
    <label>Ciphertext (even length, letters only)</label>
    <input type="text" id="hill_dec_input" placeholder="e.g. HIAT..." class="cipher-input">
    <button class="btn-dec" onclick="processHill('dec')">Decrypt</button>
    <div class="result-display" id="hill_dec_res">Waiting for data...</div>
</div>

<script>
    function modInv(n, m) {
        n = ((n % m) + m) % m;
        for (let i = 1; i < m; i++) if ((n * i) % m === 1) return i;
        return null;
    }

    function hillEncrypt(text, a, b, c, d) {
        let clean = text.toUpperCase().replace(/[^A-Z]/g, "");
        if (clean.length % 2 !== 0) clean += "X";
        let out = "";
        for (let i = 0; i < clean.length; i += 2) {
            let x = clean.charCodeAt(i) - 65;
            let y = clean.charCodeAt(i + 1) - 65;
            let r1 = (a * x + b * y) % 26;
            let r2 = (c * x + d * y) % 26;
            out += String.fromCharCode(r1 + 65) + String.fromCharCode(r2 + 65);
        }
        return out;
    }

    function hillDecrypt(text, a, b, c, d) {
        let det = (a * d - b * c) % 26;
        if (det < 0) det += 26;
        let detInv = modInv(det, 26);
        if (detInv === null) return "ERROR: Matrix is not invertible mod 26";
        let aInv = (detInv * d) % 26;
        let bInv = (-detInv * b) % 26;
        let cInv = (-detInv * c) % 26;
        let dInv = (detInv * a) % 26;
        if (aInv < 0) aInv += 26;
        if (bInv < 0) bInv += 26;
        if (cInv < 0) cInv += 26;
        if (dInv < 0) dInv += 26;
        let clean = text.toUpperCase().replace(/[^A-Z]/g, "");
        let out = "";
        for (let i = 0; i < clean.length; i += 2) {
            let x = clean.charCodeAt(i) - 65;
            let y = clean.charCodeAt(i + 1) - 65;
            let r1 = (aInv * x + bInv * y) % 26;
            let r2 = (cInv * x + dInv * y) % 26;
            out += String.fromCharCode(r1 + 65) + String.fromCharCode(r2 + 65);
        }
        return out;
    }

    function processHill(mode) {
        const a = parseInt(document.getElementById(`hill_${mode}_a`).value) || 0;
        const b = parseInt(document.getElementById(`hill_${mode}_b`).value) || 0;
        const c = parseInt(document.getElementById(`hill_${mode}_c`).value) || 0;
        const d = parseInt(document.getElementById(`hill_${mode}_d`).value) || 0;
        const input = document.getElementById(`hill_${mode}_input`).value;
        const display = document.getElementById(`hill_${mode}_res`);

        display.innerHTML = "<span class='loading'>Processing...</span>";

        setTimeout(() => {
            if (mode === 'enc') display.innerText = hillEncrypt(input, a, b, c, d);
            else display.innerText = hillDecrypt(input, a, b, c, d);
        }, 400);
    }
</script>

---

## Transposition Cipher: 
A transposition cipher doesnot change the character, instead the characters are simply permuted. 

### Mathematical Formulation:

Unlike substitution ciphers, a transposition cipher does not alter the characters themselves — it only rearranges their positions.

Let the plaintext be divided into blocks of fixed length $$n$$. The key is a permutation of positions:

$$
\pi = (\pi(1), \pi(2), \ldots, \pi(n))
$$

where $$\pi$$ is a bijection $$\pi : \{1, \ldots, n\} \to \{1, \ldots, n\}$$. The key $$\mathcal{K}$$ is the set of all such permutations, and the specific permutation $$\pi$$ chosen acts as the secret key.

Encryption:

$$
C = \pi(P)
$$

– the character at position $$i$$ in plaintext moves to position $$\pi(i)$$ in ciphertext.

Decryption:

$$
P = \pi^{-1}(C)
$$

---

### Example

Let the key (permutation) be:

$$
\pi = (3, 1, 4, 2)
$$

This means:
- position 1 → position 3
- position 2 → position 1  
- position 3 → position 4
- position 4 → position 2

Encrypt the plaintext **"HELP"** using this permutation.

Write the plaintext into a row:

$$
P = (H, E, L, P)
$$

Apply the permutation:

<table class="math-table">
<thead>
<tr>
<th>Position $$i$$</th>
<th>1</th>
<th>2</th>
<th>3</th>
<th>4</th>
</tr>
</thead>
<tbody>
<tr>
<td>Plaintext $$p_i$$</td>
<td>H</td>
<td>E</td>
<td>L</td>
<td>P</td>
</tr>
<tr>
<td>Moves to $$\pi(i)$$</td>
<td>3</td>
<td>1</td>
<td>4</td>
<td>2</td>
</tr>
</tbody>
</table>

Build ciphertext by reading each position $$j$$ and placing the letter that arrives there:

$$
c_1 \gets p_2 = E,\quad
c_2 \gets p_4 = P,\quad
c_3 \gets p_1 = H,\quad
c_4 \gets p_3 = L
$$

Ciphertext: **EPHL**

To decrypt, apply the inverse permutation:

$$
\pi^{-1} = (2, 4, 1, 3)
$$

<table class="math-table">
<thead>
<tr>
<th>Position $$j$$</th>
<th>1</th>
<th>2</th>
<th>3</th>
<th>4</th>
</tr>
</thead>
<tbody>
<tr>
<td>Ciphertext $$c_j$$</td>
<td>E</td>
<td>P</td>
<td>H</td>
<td>L</td>
</tr>
<tr>
<td>Comes from $$\pi^{-1}(j)$$</td>
<td>2</td>
<td>4</td>
<td>1</td>
<td>3</td>
</tr>
</tbody>
</table>

Recover plaintext by taking each $$c_j$$ back to its original position:

$$
p_1 \gets c_3 = H,\quad
p_2 \gets c_1 = E,\quad
p_3 \gets c_4 = L,\quad
p_4 \gets c_2 = P
$$

Plaintext: **HELP**

### Interactive Transposition Cipher:

<div class="module-section">
    <h2 class="module-title">1. Transposition Encryption</h2>
    <label>Permutation Key (comma-separated positions, e.g. 3,1,4,2)</label>
    <input type="text" id="trans_enc_key" value="3,1,4,2" placeholder="e.g. 3,1,4,2" class="cipher-input">
    <label>Plaintext</label>
    <input type="text" id="trans_enc_input" placeholder="e.g. HELP..." class="cipher-input">
    <button class="btn-enc" onclick="processTransposition('enc')">Encrypt</button>
    <div class="result-display" id="trans_enc_res">Waiting for data...</div>
</div>

<div class="module-section">
    <h2 class="module-title decrypt-title">2. Transposition Decryption</h2>
    <label>Permutation Key (comma-separated)</label>
    <input type="text" id="trans_dec_key" value="3,1,4,2" placeholder="e.g. 3,1,4,2" class="cipher-input">
    <label>Ciphertext</label>
    <input type="text" id="trans_dec_input" placeholder="e.g. EPHL..." class="cipher-input">
    <button class="btn-dec" onclick="processTransposition('dec')">Decrypt</button>
    <div class="result-display" id="trans_dec_res">Waiting for data...</div>
</div>

<script>
    function transpositionLogic(text, permStr, isEncrypt) {
        let clean = text.replace(/\s/g, "");
        if (!clean) return "ERROR: No input";
        let perm = permStr.split(",").map(Number);
        if (perm.some(isNaN)) return "ERROR: Invalid permutation";
        let n = perm.length;
        let min = Math.min(...perm), max = Math.max(...perm);
        if (min !== 1 || max !== n || new Set(perm).size !== n) return "ERROR: Permutation must be 1..n with no repeats";
        if (isEncrypt) {
            let padLen = Math.ceil(clean.length / n) * n;
            while (clean.length < padLen) clean += "X";
            let out = "";
            for (let start = 0; start < clean.length; start += n) {
                let block = clean.slice(start, start + n);
                let outArr = new Array(n);
                for (let i = 0; i < n; i++) outArr[perm[i] - 1] = block[i];
                out += outArr.join('');
            }
            return out;
        } else {
            let out = "";
            for (let start = 0; start < clean.length; start += n) {
                let block = clean.slice(start, start + n);
                for (let i = 0; i < n; i++) out += block[perm[i] - 1];
            }
            return out.replace(/X+$/, "");
        }
    }

    function processTransposition(mode) {
        const key = document.getElementById(`trans_${mode}_key`).value;
        const input = document.getElementById(`trans_${mode}_input`).value;
        const display = document.getElementById(`trans_${mode}_res`);

        display.innerHTML = "<span class='loading'>Processing...</span>";

        setTimeout(() => {
            display.innerText = transpositionLogic(input, key, mode === 'enc');
        }, 400);
    }
</script>

---

## One Time Pad: 

The OTP is only cipher which provides the perfect security. It uses a random key that should be: 
- long as the plaintext
- Truely random
- Never reuse

### Mathematical Formulation:

Let the plaintext and key be vectors of length $$n$$:

$$
P = (p_1, p_2, \ldots, p_n),\qquad
K = (k_1, k_2, \ldots, k_n)
$$

where each $$p_i, k_i \in \mathbb{Z}_{26}$$.

The encryption is given by

$$
c_i = p_i + k_i \bmod 26
$$

The decryption is given by : 

$$
p_i = c_i - k_i \bmod 26
$$

---

### Example:

Let us encrypt the plaintext **"HELP"** using a one-time pad.

First, map letters to numbers:

$$
H \mapsto 7,\; E \mapsto 4,\; L \mapsto 11,\; P \mapsto 15
$$

Choose a random key of the same length:

$$
K = (8, 21, 5, 14)
$$

**Encryption:**

<table class="math-table">
<thead>
<tr>
<th>Plain $$p_i$$</th>
<th>Key $$k_i$$</th>
<th>$$p_i + k_i$$</th>
<th>$$\bmod 26$$</th>
<th>Cipher $$c_i$$</th>
</tr>
</thead>
<tbody>
<tr>
<td>H (7)</td>
<td>8</td>
<td>15</td>
<td>15</td>
<td>P</td>
</tr>
<tr>
<td>E (4)</td>
<td>21</td>
<td>25</td>
<td>25</td>
<td>Z</td>
</tr>
<tr>
<td>L (11)</td>
<td>5</td>
<td>16</td>
<td>16</td>
<td>Q</td>
</tr>
<tr>
<td>P (15)</td>
<td>14</td>
<td>29</td>
<td>3</td>
<td>D</td>
</tr>
</tbody>
</table>

Ciphertext: **PZQD**

**Decryption:**

<table class="math-table">
<thead>
<tr>
<th>Cipher $$c_i$$</th>
<th>Key $$k_i$$</th>
<th>$$c_i - k_i$$</th>
<th>$$\bmod 26$$</th>
<th>Plain $$p_i$$</th>
</tr>
</thead>
<tbody>
<tr>
<td>P (15)</td>
<td>8</td>
<td>7</td>
<td>7</td>
<td>H</td>
</tr>
<tr>
<td>Z (25)</td>
<td>21</td>
<td>4</td>
<td>4</td>
<td>E</td>
</tr>
<tr>
<td>Q (16)</td>
<td>5</td>
<td>11</td>
<td>11</td>
<td>L</td>
</tr>
<tr>
<td>D (3)</td>
<td>14</td>
<td>-11</td>
<td>15</td>
<td>P</td>
</tr>
</tbody>
</table>

Original plaintext **HELP** is recovered.

### Interactive One Time Pad

<div class="module-section">
    <h2 class="module-title">1. OTP Encryption</h2>
    <label>Plaintext</label>
    <input type="text" id="otp_enc_input" placeholder="e.g. HELP..." class="cipher-input">
    <label>Key (same length as plaintext, letters only)</label>
    <div style="display: flex; gap: 5px;">
        <input type="text" id="otp_enc_key" placeholder="e.g. MYPAD..." class="cipher-input" style="flex: 1;">
        <button onclick="generateOTPKey('enc')" style="padding: 8px 12px; cursor: pointer; background: #1565c0; color: white; border: none; border-radius: 4px; white-space: nowrap;">Random Key</button>
    </div>
    <button class="btn-enc" onclick="processOTP('enc')">Encrypt</button>
    <div class="result-display" id="otp_enc_res">Waiting for data...</div>
</div>

<div class="module-section">
    <h2 class="module-title decrypt-title">2. OTP Decryption</h2>
    <label>Ciphertext</label>
    <input type="text" id="otp_dec_input" placeholder="e.g. PZQD..." class="cipher-input">
    <label>Key</label>
    <div style="display: flex; gap: 5px;">
        <input type="text" id="otp_dec_key" placeholder="e.g. MYPAD..." class="cipher-input" style="flex: 1;">
        <button onclick="generateOTPKey('dec')" style="padding: 8px 12px; cursor: pointer; background: #1565c0; color: white; border: none; border-radius: 4px; white-space: nowrap;">Random Key</button>
    </div>
    <button class="btn-dec" onclick="processOTP('dec')">Decrypt</button>
    <div class="result-display" id="otp_dec_res">Waiting for data...</div>
</div>

<script>
    function otpLogic(text, key, isEncrypt) {
        let cleanText = text.toUpperCase().replace(/[^A-Z]/g, "");
        let cleanKey = key.toUpperCase().replace(/[^A-Z]/g, "");
        if (!cleanText) return "ERROR: No input";
        if (cleanKey.length < cleanText.length) return "ERROR: Key must be at least as long as the plaintext";
        let out = "";
        for (let i = 0; i < cleanText.length; i++) {
            let p = cleanText.charCodeAt(i) - 65;
            let k = cleanKey.charCodeAt(i) - 65;
            let r = isEncrypt ? (p + k) % 26 : (p - k + 26) % 26;
            out += String.fromCharCode(r + 65);
        }
        return out;
    }

    function generateOTPKey(mode) {
        const input = document.getElementById(`otp_${mode}_input`).value;
        const keyField = document.getElementById(`otp_${mode}_key`);
        let len = input.replace(/[^a-zA-Z]/g, "").length || 4;
        let key = "";
        for (let i = 0; i < len; i++) key += String.fromCharCode(65 + Math.floor(Math.random() * 26));
        keyField.value = key;
    }

    function processOTP(mode) {
        const input = document.getElementById(`otp_${mode}_input`).value;
        const key = document.getElementById(`otp_${mode}_key`).value;
        const display = document.getElementById(`otp_${mode}_res`);

        display.innerHTML = "<span class='loading'>Processing...</span>";

        setTimeout(() => {
            display.innerText = otpLogic(input, key, mode === 'enc');
        }, 400);
    }
</script>

---
