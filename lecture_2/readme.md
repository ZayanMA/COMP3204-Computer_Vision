# COMP3204 — Lecture 2 Notes: Image Formation & Frequency Space

## Lecture 2 scope
- Topics covered in the lecture slides:
  - **What is inside an image?**
  - **Restrictions on image formation** (resolution + brightness sampling)
  - Moving to a different representation: **Fourier / frequency domain**

---

## 1) What is inside an image? (digital image representation)
- **Image as a matrix**
  - A grayscale image can be treated as an **N × N matrix** of numbers (pixel intensities).

- **Pixel values and bit-depth**
  - In these slides, each pixel is an **8-bit unsigned integer**:
    - “8-bit” → stored using 8 binary digits (bits).
    - “unsigned” → only non-negative values.
  - Range is **[0, 255]** because \(2^8 = 256\) possible values.
  - Bits have **weights**:
    - bit 7 contributes \(2^7 = 128\), bit 6 contributes \(2^6 = 64\), …, bit 0 contributes \(2^0 = 1\).
  - Converting bits to a value:
    - If \(b_k \in \{0,1\}\) is bit \(k\), then  
      \[
      \text{value}=\sum_{k=0}^{7} b_k 2^k
      \]
    - bit 0 = **LSB**, bit 7 = **MSB**.

- **Bit-plane decomposition**
  - A **bit-plane** is the binary image formed by taking *one fixed bit position* from every pixel.
  - Slide observations:
    - **MSB (bit 7)** carries most of the *recognisable structure*.
    - **LSB (bit 0)** often looks *noise-like*.
    - A mid-plane (e.g., **bit 4**) can visibly capture **lighting variation** in the example.
  - Exam takeaway:
    - Higher-order bits dominate **overall appearance**; lower-order bits often capture **fine detail/noise**.

---

## 2) Restrictions on image formation
- **Resolution (spatial sampling)**
  - Sampling an image on an \(N \times N\) grid: \(N\) controls spatial detail.
  - Lower resolution (e.g., **64×64**) → blocky, detail lost.
  - Higher resolution (e.g., **128×128**, **256×256**) → more detail preserved.
  - Trade-off stated in slides:
    - Larger \(N\) increases storage + computation.
    - Motivating question: **How do we choose an appropriate value for \(N\)?**

- **Brightness sampling (intensity quantisation)**
  - Brightness is discretised by limiting pixel values to a finite set (here, 0–255 via 8 bits).

- **Slide framing point**
  - **Sampling is not as simple as it appears** and affects both:
    - **space** (resolution)
    - **brightness** (intensity levels / bit-depth)

---

## 3) Frequency viewpoint: waves, frequency, and why Fourier matters
- **Waves**
  - A sine/cosine wave is a repeating pattern over a variable (like time).
  - For images, we later generalise to **2D repeating patterns** over \(x\) and \(y\).

- **Complex exponential (compact sinusoid form)**
  - Key identity used in Fourier analysis:
    \[
    e^{j\omega t} = \cos(\omega t) + j\sin(\omega t)
    \]
  - Interpretation (as per slide diagram):
    - cosine = **real-axis** component
    - sine = **imaginary-axis** component
    - behaves like a rotating vector in the complex plane

- **Frequency and period**
  - If a pattern repeats every \(T\) (period), frequency is:
    \[
    \xi = \frac{1}{T}
    \]
  - High frequency = repeats quickly; low frequency = repeats slowly.

- **Frequency intuition (from slide images)**
  - Repeating textures/patterns → strong, structured frequency components.
  - Oriented patterns → frequency structure aligned with that orientation.

---

## 4) Fourier Transform (time/space domain → frequency domain)
- **Core idea (Joseph Fourier)**
  - Signals can be represented as sums of sine/cosine waves of different frequencies.

- **Fourier transform definition (as in slides)**
  - For a signal \(p(t)\):
    \[
    F_p(\omega) = \int_{-\infty}^{\infty} p(t)\,e^{-j\omega t}\,dt
    \]
  - Meaning:
    - \(F_p(\omega)\) tells you **how much** of angular frequency \(\omega\) exists in the signal.
  - Expand the exponential:
    \[
    e^{-j\omega t} = \cos(\omega t) - j\sin(\omega t)
    \]
    So the transform is effectively measuring cosine + sine content (packaged as a complex number).

- **Angular frequency vs ordinary frequency**
  - Relationship:
    \[
    \omega = 2\pi\xi
    \]

- **Inverse Fourier transform (reconstruct the original signal)**
  \[
  p(t) = \frac{1}{2\pi}\int_{-\infty}^{\infty} F_p(\omega)\,e^{j\omega t}\,d\omega
  \]
  - Interpretation:
    - You reconstruct \(p(t)\) by adding up **all frequency components**, weighted by \(F_p(\omega)\).

- **Equivalent “frequency \(\xi\)” form shown in slides**
  \[
  F_p(\xi) = \int_{-\infty}^{\infty} p(t)\,e^{-j2\pi\xi t}\,dt
  \]
  \[
  p(t) = \int_{-\infty}^{\infty} F_p(\xi)\,e^{j2\pi\xi t}\,d\xi
  \]
  - Same concept; the \(2\pi\) factor moves into the exponent.

- **What the Fourier transform “does” (conceptual slide)**
  - Transform: move to a representation where **frequency components are separated/identifiable**.
  - Inverse transform: **sum components** to reconstruct the original.

---

## 5) Worked example in slides: rectangular pulse → Fourier transform
- **Signal shown**
  - A **rectangular pulse** \(p(t)\) with amplitude \(A=1\) over a time window, 0 elsewhere.

- **How the integral is evaluated**
  - Start:
    \[
    F_p(\omega)=\int_{-\infty}^{\infty}p(t)e^{-j\omega t}dt
    \]
  - Pulse is non-zero only on its duration, so reduce limits:
    \[
    F_p(\omega)=\int_{-T/2}^{T/2} 1\cdot e^{-j\omega t}\,dt
    \]
  - Integrate:
    \[
    \int e^{-j\omega t}\,dt=\frac{e^{-j\omega t}}{-j\omega}
    \]
  - Apply limits:
    \[
    F_p(\omega)=\left[\frac{e^{-j\omega t}}{-j\omega}\right]_{-T/2}^{T/2}
    =
    \frac{e^{-j\omega(T/2)}-e^{j\omega(T/2)}}{-j\omega}
    \]
  - Use identity \(e^{-ja}-e^{ja}=-2j\sin(a)\):
    \[
    F_p(\omega)=\frac{2\sin(\omega T/2)}{\omega}
    \]
  - Key behaviour: pulse in time ↔ **sinc-like** spectrum in frequency.

---

## 6) Reconstructing a signal from its Fourier transform (inverse transform intuition)
- Slide idea:
  - Each frequency \(\omega\) contributes a sinusoidal component scaled by the Fourier coefficient at that \(\omega\).
  - Summing (integrating) over more frequencies improves reconstruction fidelity.
- Slide visual intuition:
  - Individual contributions for \(\omega = 1,2,3,4\) look like different oscillations.
  - Integrating across a range produces something close to the original pulse.

---

## 7) Complex output: magnitude and phase
- **Fourier transform values are complex**
  \[
  F_p(\omega)=\operatorname{Re}(F_p(\omega)) + j\,\operatorname{Im}(F_p(\omega))
  \]

- **Magnitude**
  \[
  |F_p(\omega)|=\sqrt{\operatorname{Re}(F_p(\omega))^2+\operatorname{Im}(F_p(\omega))^2}
  \]
  - Interprets as: **how much** of frequency \(\omega\) is present.

- **Phase**
  \[
  \arg(F_p(\omega))=\tan^{-1}\left(\frac{\operatorname{Im}(F_p(\omega))}{\operatorname{Re}(F_p(\omega))}\right)
  \]
  - Interprets as: **alignment/shift information** of frequency components.

---

## 8) Example: Fourier transform of a musical chord
- A chord is a mixture of multiple sinusoidal frequencies.
- Its Fourier transform shows multiple frequency components corresponding to the tones/harmonics present.

---

## 9) Why phase matters (examinable conceptual result)
- Slide demonstration:
  - Reconstruct images by combining:
    - magnitude from image A + phase from image B, then inverse transform.
- Observation from slides:
  - The result looks most like the image that contributed the **phase**.
- Exam takeaway:
  - **Magnitude** → “how much of each frequency”
  - **Phase** → strongly controls **spatial arrangement / recognisable structure**

---

## 10) Fourier for reconstruction / coding (compression intuition shown)
- Slide pipeline idea:
  - Transform image → keep/encode selected transform information → inverse transform to reconstruct.
- Shown result:
  - Keeping only a small fraction (e.g., **5%**) of transform components can still reconstruct an image that looks close to the original.
  - An “error image” visualises what was lost.

---

## 11) Main points so far (slide summary)
- **Sampling data is not as simple as it appears.**
- **Sampling affects space and brightness.**
- **Fourier helps us understand frequency.**
- **Fourier enables coding (compression) and more.**
- Next framing: Fourier will be used to understand sampling.

---

## 12) Other transforms mentioned (names only; not expanded in slides)
- **Discrete Cosine (Sine) Transform**
- **Discrete Hartley Transform**
- **Wavelet families/types** (examples listed): continuous, discrete, complex, stationary, dual, Haar, Daubechies, Morlet, Gabor, etc.
- Other named transform families: **curvelets, shearlets, bandelets, contourlets, fresnelets, chirplets, noiselets, …**

---

## 13) Wavelet transform (what the slide example is showing)
- Slide shows an example **2D discrete wavelet transform** used in **JPEG2000**:
  - Output is a collection of sub-images capturing information at different **scales** (coarse-to-fine) and **detail patterns**.
- Key intuition consistent with the slide visual:
  - Wavelets represent images in a way that is both:
    - **spatially localised** (where something happens), and
    - **scale-sensitive** (coarse structure vs fine detail)
