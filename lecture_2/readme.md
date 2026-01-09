# COMP3204 — Lecture 2 Notes: Image Formation & Frequency Space

## Lecture scope (what the slides cover)
- **What is inside an image?**
- **Restrictions on image formation**
  - spatial sampling (**resolution**)
  - brightness sampling (**quantisation / bit-depth**)
- Moving to a different representation: **Fourier / frequency domain**
- (Mentioned by name only later: DCT/DST, Hartley, many wavelet/transform families)

---

## 1) What is inside an image? (digital representation)
- **Image as a matrix**
  - A grayscale image can be treated as an **N × N matrix** of numbers (pixel intensities).

- **Pixel values and bit-depth**
  - In the slides, a pixel is an **8-bit unsigned integer**:
    - **8-bit** → there are $2^8 = 256$ possible values.
    - **unsigned** → only non-negative values.
    - intensity range: **0 to 255**.
  - Bits have different “weights” (importance in the number):
    - bit 0 is the **LSB** (least significant bit), bit 7 is the **MSB** (most significant bit).
    - A pixel value can be written as:
      $$
      \text{value}=\sum_{k=0}^{7} b_k 2^k,\quad b_k\in\{0,1\}
      $$
    - So MSB contributes large jumps (like 128), while LSB contributes tiny changes (1).

- **Bit-plane decomposition**
  - A **bit-plane** is the binary image formed by taking **one fixed bit position** from every pixel.
  - What the slide examples show:
    - **Higher-order bit-planes** (near the **MSB**) preserve most **recognisable structure**.
    - **Lower-order bit-planes** (near the **LSB**) often look **noise-like**.
    - Some middle planes can clearly show effects like **lighting variation**.
  - Exam takeaway: **most perceptual image content is carried by higher-order bits**.

---

## 2) Restrictions on image formation (sampling limitations)
- **Spatial sampling = resolution**
  - Sampling an image on an **N × N grid** sets how much spatial detail you can represent.
  - Slide examples (e.g. 64×64 vs 128×128 vs 256×256):
    - low N → blocky, detail lost
    - higher N → more detail preserved
  - Trade-off:
    - larger N increases **storage + compute**
    - motivates: **How do we choose an appropriate N?**

- **Brightness sampling = quantisation**
  - Brightness is discretised into a finite set of intensity values (here 0–255 with 8 bits).
  - Slide framing: **sampling affects both space and brightness** and **is not as simple as it appears**.

---

## 3) Frequency viewpoint: waves, frequency, and why Fourier matters
- **Waves**
  - A sine/cosine wave is a repeating pattern over a variable (often time).
  - For images, the idea generalises to **2D repeating patterns** across spatial coordinates.

- **Complex exponential form (compact sinusoid representation)**
  - Key identity used throughout Fourier analysis:
    $$
    e^{j\omega t}=\cos(\omega t)+j\sin(\omega t)
    $$
  - Interpretation (matching the slide diagram):
    - cosine → **real** component
    - sine → **imaginary** component
    - behaves like a rotating vector in the complex plane as $t$ changes.

- **Period and frequency**
  - If a pattern repeats every **T** (period), then the frequency is:
    $$
    \xi=\frac{1}{T}
    $$
  - High frequency → rapid changes/repetitions; low frequency → slow changes.

---

## 4) Fourier transform: time/space domain → frequency domain
- **Core idea (Fourier)**
  - A signal can be represented as a mixture (sum/integral) of sinusoids at different frequencies.

- **Fourier transform definition (as in slides)**
  - For a signal $p(t)$:
    $$
    F_p(\omega)=\int_{-\infty}^{\infty} p(t)\,e^{-j\omega t}\,dt
    $$
  - The exponential can be expanded as:
    $$
    e^{-j\omega t}=\cos(\omega t)-j\sin(\omega t)
    $$
  - Meaning: the transform measures how much cosine/sine content exists at each $\omega$, packaged as a complex number.

- **Angular frequency vs “cycles per unit time”**
  $$
  \omega = 2\pi \xi
  $$

- **Inverse Fourier transform (reconstruction)**
  $$
  p(t)=\frac{1}{2\pi}\int_{-\infty}^{\infty} F_p(\omega)\,e^{j\omega t}\,d\omega
  $$
  - Interpretation: rebuild the original by adding up all frequency components, weighted by their coefficients.

- **Equivalent form using $\xi$ (also shown in slides)**
  $$
  F_p(\xi)=\int_{-\infty}^{\infty} p(t)\,e^{-j2\pi\xi t}\,dt
  $$
  $$
  p(t)=\int_{-\infty}^{\infty} F_p(\xi)\,e^{j2\pi\xi t}\,d\xi
  $$
  - Same idea; the $2\pi$ moves into the exponent.

---

## 5) Worked example in slides: rectangular pulse → transform
- **Signal**
  - A rectangular pulse $p(t)$ with amplitude 1 over a finite window and 0 elsewhere.

- **How the integral is evaluated (the key reduction step)**
  - Start:
    $$
    F_p(\omega)=\int_{-\infty}^{\infty}p(t)e^{-j\omega t}\,dt
    $$
  - Because $p(t)=0$ outside the pulse, the integral collapses to the non-zero interval:
    $$
    F_p(\omega)=\int_{-T/2}^{T/2} e^{-j\omega t}\,dt
    $$
  - Integrate the exponential:
    $$
    \int e^{-j\omega t}\,dt=\frac{e^{-j\omega t}}{-j\omega}
    $$
  - Apply the limits:
    $$
    F_p(\omega)=\left[\frac{e^{-j\omega t}}{-j\omega}\right]_{-T/2}^{T/2}
    =
    \frac{e^{-j\omega(T/2)}-e^{j\omega(T/2)}}{-j\omega}
    $$
  - Use the identity $e^{-ja}-e^{ja}=-2j\sin(a)$:
    $$
    F_p(\omega)=\frac{2\sin(\omega T/2)}{\omega}
    $$
  - This is the “pulse ↔ sinc-like spectrum” behaviour the slides illustrate.

---

## 6) Reconstruction intuition (inverse transform visual)
- Slide idea:
  - Each $\omega$ contributes an oscillation (a sinusoidal component).
  - Adding more frequencies (integrating over a wider range of $\omega$) makes the reconstruction closer to the original pulse.

---

## 7) Complex Fourier output: magnitude and phase
- Fourier transform values are **complex**:
  $$
  F_p(\omega)=\mathrm{Re}(F_p(\omega))+j\,\mathrm{Im}(F_p(\omega))
  $$

- **Magnitude**
  - “How much” of that frequency exists:
    $$
    |F_p(\omega)|=\sqrt{\mathrm{Re}(F_p(\omega))^2+\mathrm{Im}(F_p(\omega))^2}
    $$

- **Phase**
  - “Alignment/shift” information of that frequency component:
    $$
    \arg(F_p(\omega))=\tan^{-1}\left(\frac{\mathrm{Im}(F_p(\omega))}{\mathrm{Re}(F_p(\omega))}\right)
    $$

---

## 8) Example: Fourier transform of a musical chord
- A chord is a mixture of multiple tones → multiple frequency components.
- The Fourier transform shows peaks/components corresponding to the tones/harmonics present.

---

## 9) Why phase matters (highly examinable slide result)
- Slide experiment:
  - take **magnitude** from one image + **phase** from another
  - inverse transform to reconstruct
- Observation:
  - reconstruction tends to look like the image that provided the **phase**.
- Exam takeaway:
  - **Magnitude** → “how much of each frequency”
  - **Phase** → **where/structure** information that strongly controls recognisable appearance

---

## 10) Fourier for reconstruction / coding (compression intuition)
- Slide pipeline:
  - transform → keep/encode a subset of coefficients → inverse transform
- Slide result:
  - even keeping a small fraction (e.g., **5%**) can produce a visually similar reconstruction
  - an “error image” shows what information is lost.

---

## 11) Slide summary points
- **Sampling isn’t as simple as it appears.**
- **Sampling affects space and brightness.**
- **Fourier helps us understand frequency.**
- **Fourier enables coding (compression) and more.**
- Next framing: Fourier will be used to understand sampling.

---

## 12) Other transforms mentioned (names only; not explained in slides)
- Discrete Cosine (Sine) Transform
- Discrete Hartley Transform
- Wavelet families/types (examples named): continuous, discrete, complex, stationary, dual, Haar, Daubechies, Morlet, Gabor, …
- Other named transform families: curvelets, shearlets, bandelets, contourlets, fresnelets, chirplets, noiselets, …

---

## 13) Wavelet transform (what the slide example shows)
- Slides show an example of a **2D discrete wavelet transform** used in **JPEG2000**.
- The output is a set of sub-images capturing information at different:
  - **scales** (coarse → fine)
  - **detail patterns** (different local change types)
- Key intuition consistent with the slide visual:
  - wavelets represent content that is both:
    - **spatially localised** (where it happens)
    - **scale-sensitive** (coarse structure vs fine detail)
