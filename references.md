Java Security API Algorithms (The "Cipher" Class Group)
**Common Pattern:** `getInstance` $\to$ `SecretKeySpec` $\to$ `init` $\to$ `doFinal`

| File | Algorithm | Key Req | Cipher String (Memorize This) | Critical Code Snippet / Logic |
| :--- | :--- | :--- | :--- | :--- |
| **AES** | AES | 16 chars | `"AES"` | `new SecretKeySpec(key.getBytes(), "AES")` |
| **DES** | DES | 8 chars | `"DES"` | `new SecretKeySpec(key.getBytes(), "DES")` |
| **RC4** | RC4 | Any | `"RC4"` | `new SecretKeySpec(key.getBytes(), "RC4")` |
| **CBC** | AES (CBC) | 16 chars | `"AES/CBC/PKCS5Padding"` | **IV Required:** `byte[] iv = new byte[16]; Arrays.fill(iv, (byte)0);`<br>`new IvParameterSpec(iv)` |
| **ECB** | AES (ECB) | 16 chars | `"AES/ECB/PKCS5Padding"` | Explicitly defines ECB mode. Same logic as AES otherwise. |
| **RSA** | RSA | Generated | `"RSA/ECB/PKCS1Padding"` | **Key Gen:** `KeyPairGenerator kpg = KeyPairGenerator.getInstance("RS");` <br> *(Note: Code has typo "RS", usually "RSA")*<br>`kpg.initialize(1024);` |



**Standard Flow for Lab:**
1.  `Scanner` input for text & key.
2.  `Cipher c = Cipher.getInstance("STRING");`
3.  `SecretKeySpec sk = new SecretKeySpec(key.getBytes(), "ALG");`
4.  **Encrypt:** `c.init(Cipher.ENCRYPT_MODE, sk);` $\to$ `byte[] enc = c.doFinal(text.getBytes());` $\to$ Print `Base64`.
5.  **Decrypt:** `c.init(Cipher.DECRYPT_MODE, sk);` $\to$ `byte[] dec = c.doFinal(enc);` $\to$ Print `new String(dec)`.

---

### 2. Classical Algorithms (Logic Based)
These require memorizing the specific logic loops.

| File | Algorithm | Key Logic to Remember |
| :--- | :--- | :--- |
| **Caesar** | Shift | **Formula:** `(c - 'A' + shift) % 26 + 'A'`<br>**Decrypt:** `(c - 'A' - shift + 26) % 26 + 'A'`<br>*Tip: Only handles Uppercase in your code.* |
| **Hill** | Matrix (2x2) | **Math:** $C_1 = (K_{00}P_1 + K_{01}P_2) \% 26$<br>**Inverse:** Calculate determinant `(ad-bc)%26`. If negative, `+26`. Find `modInverse`.<br>**Inverse Matrix:** Swap `a,d`, negate `b,c`. Multiply by `detInv`. |
| **Playfair** | 5x5 Grid | **Setup:** `replace("J", "I")`. Fill matrix with key, then A-Z.<br>**Rules:**<br>1. Same Row: Right (+1)<br>2. Same Col: Down (+1)<br>3. Rectangle: Swap columns (`[r1][c2]`, `[r2][c1]`)<br>**Decrypt:** Same but -1 (or +4). |
| **RailFence** | Zig-Zag | **Structure:** `StringBuilder[] r = new StringBuilder[k]`<br>**Movement:** `row += d`. If `row==0` or `row==k-1`, flip direction `d *= -1`.<br>**Decrypt:** Mark positions with `boolean[]`, fill marks, then read. |
| **RowCol** | Transposition | **Encrypt:** Write Row-wise, Read Col-wise.<br>**Decrypt:** Write Col-wise, Read Row-wise.<br>**Padding:** Fill with 'X' until `len == rows * cols`. |

---

### 3. Quick Constants & "Gotchas" Checklist

*   **Imports:** Just memorize `import javax.crypto.*; import javax.crypto.spec.*; import java.util.*; import java.security.*;` (covers everything).
*   **Base64:** Always used for printing encrypted bytes: `Base64.getEncoder().encodeToString(bytes)`.
*   **AES Key:** Must be **exactly 16 characters**. If you type 15, it crashes.
*   **DES Key:** Must be **exactly 8 characters**.
*   **RSA Typo:** Your code uses `"RS"` in `getInstance`. If the lab compiler fails, change it to `"RSA"`.
*   **CBC IV:** Your code uses a **zero IV** (`Arrays.fill(iv, (byte)0)`). This is insecure in real life but standard for this specific lab code.
*   **Hill Padding:** If plaintext length is odd, append "X".

### 4. One-Liner Implementation Summaries

*   **AES/DES/RC4:** `Cipher.getInstance` $\to$ `init(ENCRYPT)` $\to$ `doFinal` $\to$ `Base64`.
*   **Caesar:** Loop chars, add shift, modulo 26.
*   **RailFence:** Bouncing ball index (`0, 1, 2, 1, 0...`).
*   **RowCol:** 2D Array `matrix[r][c]`. Nested loops `r` then `c` vs `c` then `r`.
*   **Playfair:** `pos(a)`, `pos(b)`. Check `r1==r2`, `c1==c2`, or else.

Based on analyzing all your cryptographic programs, here's a comprehensive table to help you remember each algorithm with all the constants, cipher classes, and key implementation details:

## 🔐 **Algorithm Memory Table - Quick Reference**

| **Algorithm** | **Cipher Class/Type** | **Key Constants** | **Import Packages** | **Key Memory Tricks** |
|---------------|----------------------|-------------------|-------------------|---------------------|
| **Caesar Cipher** | Manual implementation | `shift = 3-25`<br>`26` (alphabet size) | `java.util.Scanner` | **"ABC → DEF"** (shift by 3)<br>Formula: `(c-'A'+shift)%26+'A'` |
| **Playfair** | Manual 5×5 matrix | `5×5 = 25` letters<br>`J → I` replacement | `java.util.*` | **"5×5 = 25-1"** (no J)<br>Rules: Row→Right, Col→Down, Rect→Swap |
| **Hill Cipher** | Manual 2×2 matrix | `2×2` matrix<br>`mod 26`<br>Determinant ≠ 0 | `java.util.Scanner` | **"Matrix Math Mod 26"**<br>`det×inv ≡ 1 (mod 26)` |
| **Rail Fence** | Manual zigzag | `k` rails<br>`direction = ±1` | `java.util.*` | **"Train Tracks Zigzag"**<br>Direction flips at rails 0 and k-1 |
| **Row-Column** | Manual matrix | `rows × cols`<br>Pad with `'X'` | `java.util.Scanner` | **"Fill Rows, Read Columns"**<br>Matrix transpose concept |
| **DES** | `Cipher.getInstance("DES")` | Key: **8 characters**<br>Block: 64-bit | `javax.crypto.*`<br>`javax.crypto.spec.SecretKeySpec` | **"DES = 8"** (8-char key)<br>Legacy = Deprecated |
| **AES** | `Cipher.getInstance("AES")` | Key: **16 characters**<br>Block: 128-bit | `javax.crypto.*`<br>`javax.crypto.spec.SecretKeySpec` | **"AES = 16"** (16-char key)<br>Current standard |
| **AES-ECB** | `"AES/ECB/PKCS5Padding"` | Key: **16 characters**<br>No IV needed | `javax.crypto.*`<br>`javax.crypto.spec.*` | **"ECB = Bad"** (patterns visible)<br>Each block independent |
| **AES-CBC** | `"AES/CBC/PKCS5Padding"` | Key: **16 characters**<br>IV: **16 bytes** | `javax.crypto.*`<br>`javax.crypto.spec.*` | **"CBC = Chain"** (blocks chained)<br>`IvParameterSpec(ivBytes)` |
| **RC4** | `Cipher.getInstance("RC4")` | Variable key length<br>Stream cipher | `javax.crypto.*`<br>`javax.crypto.spec.SecretKeySpec` | **"RC4 = Stream"** (byte-by-byte)<br>Fast but deprecated |
| **RSA** | `"RSA/ECB/PKCS1Padding"` | **1024-bit** key pair<br>Public/Private | `java.security.*`<br>`javax.crypto.*` | **"RSA = Key Pair"**<br>`KeyPairGenerator.getInstance("RSA")` |


