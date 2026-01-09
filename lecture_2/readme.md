# COMP3204 — Lecture 2 Notes: Image Formation & Frequency Space

## Lecture 2 scope (Image Formation + Frequency Space)

- Slide topics:
  - What is inside an image?
  - Restrictions on image formation (resolution + brightness sampling)
  - Moving to a different representation: **Fourier / frequency domain**

---

## 1) What is inside an image? (digital image representation)

- **Image as a matrix**
  - A (grayscale) image can be treated as an **$N \times N$ matrix** of numbers (pixel intensities).

- **Pixel values and bit-depth**
  - In these slides, each pixel is an **8-bit unsigned integer**:
    - **8-bit** means stored using 8 binary digits (bits).
    - **unsigned** means only non-negative values.
  - Range is **$[0, 255]$** because $2^8 = 256$ possible values.
  - Bits are indexed from **bit 0 (LSB)** up to **bit 7 (MSB)**.
  - Each bit has a weight:
    - bit 7 contributes $2^7 = 128$, bit 6 contributes $2^6 = 64$, …, bit 0 contributes $2^0 = 1$.
  - Value of an 8-bit pixel can be written as:

$$
\displaystyle
\text{value}=\sum_{k=0}^{7} b_k 2^k,\quad b_k\in\{0,1\}
$$

- **Bit-plane decomposition**
  - A **bit-plane** is the binary image formed by taking *one specific bit position* from every pixel (e.g., “all bit-3 values across the image”).
  - Key observations from the slide examples:
    - **MSB (bit 7)** carries most of the *recognisable structure*.
    - **LSB (bit 0)** often looks noise-like (rapidly changing, low visual significance).
    - In the shown example, **bit 4** noticeably captures *lighting variation*.
  - Exam takeaway:
    - Higher-order bits dominate *overall appearance*; lower-order bits often capture fine detail/noise.

---

## 2) Restrictions on image formation

- **Resolution (spatial sampling)**
  - Changing $N$ changes how much spatial detail you can represent:
    - Low resolution (e.g., **$64 \times 64$**) loses information and appears “blocky”.
    - Higher resolution (e.g., **$128 \times 128$, $256 \times 256$**) preserves more detail.
  - Trade-off stated in slides:
    - More points ($N \times N$) **increases storage** and processing cost.
    - This motivates: **How do we choose an appropriate value for $N$?**

- **Brightness sampling (intensity quantisation)**
  - Brightness is also “sampled” by limiting pixel values to a finite set (here, $0$–$255$ via 8 bits).

- **Slide framing (important)**
  - **Sampling isn’t as simple as it appears** and affects both:
    - **space** (resolution)
    - **brightness** (bit-depth / intensity levels)

---

## 3) Frequency viewpoint: waves, frequency, and why Fourier matters

- **Recall: waves**
  - A sine/cosine wave is a repeating pattern over a variable (like time).
  - In images, we later use **2D waves** varying along **$x$ and $y$** directions.

- **Complex exponential (compact way to represent sinusoids)**
  - Key identity used in Fourier analysis:

$$
\displaystyle
e^{j\omega t} = \cos(\omega t) + j\sin(\omega t)
$$

  - Interpretation (what the diagram is showing):
    - $\cos(\omega t)$ is the **real-axis** component.
    - $\sin(\omega t)$ is the **imaginary-axis** component.
    - As $t$ changes, the complex number behaves like a rotating vector in the complex plane.

- **Frequency and period**
  - If a pattern repeats every $T$ seconds (**period**), then its **frequency** is:

$$
\displaystyle
\xi = \frac{1}{T}
$$

  - High frequency = repeats quickly; low frequency = repeats slowly.

- **Frequency intuition for images (what the slide images illustrate)**
  - Repeating textures/patterns produce strong, structured components in the frequency representation.
  - Oriented patterns tend to produce frequency structure aligned with that orientation.

---

## 4) The Fourier Transform (time/space domain → frequency domain)

- **Core idea (Joseph Fourier)**
  - A signal can be represented as a sum of sine/cosine waves of different frequencies.

- **Fourier transform definition (as in slides)**
  - For a time-varying signal $p(t)$, the Fourier transform is:

$$
\displaystyle
F_p(\omega) = \int_{-\infty}^{\infty} p(t)\,e^{-j\omega t}\,dt
$$

  - Meaning: $F_p(\omega)$ tells you “how much” of angular frequency $\omega$ exists in the signal.
  - Exponential expansion used in slides:

$$
\displaystyle
e^{-j\omega t} = \cos(\omega t) - j\sin(\omega t)
$$

- **Angular frequency vs ordinary frequency**
  - Relationship between angular frequency $\omega$ and frequency $\xi$:

$$
\displaystyle
\omega = 2\pi\xi
$$

- **Inverse Fourier transform (go back to the original signal)**
  - Reconstruction formula:

$$
\displaystyle
p(t) = \frac{1}{2\pi}\int_{-\infty}^{\infty} F_p(\omega)\,e^{j\omega t}\,d\omega
$$

  - Interpretation: reconstruct $p(t)$ by adding up **all frequency components**, weighted by $F_p(\omega)$.

- **Equivalent “frequency $\xi$” form shown in slides**
  - Same idea, written using $\xi$ instead of $\omega$:

$$
\displaystyle
F_p(\xi) = \int_{-\infty}^{\infty} p(t)\,e^{-j2\pi\xi t}\,dt
$$

$$
\displaystyle
p(t) = \int_{-\infty}^{\infty} F_p(\xi)\,e^{j2\pi\xi t}\,d\xi
$$

- **What the Fourier transform “does” (conceptual slide point)**
  - Transform separates a signal into frequency components.
  - Inverse transform recombines frequency components to reconstruct the original.

---

## 5) Worked example in slides: rectangular pulse → Fourier transform

- **Signal shown**
  - A **rectangular pulse** $p(t)$ of amplitude $A=1$ over a time window, and $0$ elsewhere.

- **How the integral is evaluated (the key slide steps)**
  - Start from:

$$
\displaystyle
F_p(\omega)=\int_{-\infty}^{\infty}p(t)e^{-j\omega t}\,dt
$$

  - Because the pulse is zero outside its duration, reduce the limits to where $p(t)=1$:

$$
\displaystyle
F_p(\omega)=\int_{-T/2}^{T/2} e^{-j\omega t}\,dt
$$

  - Integrate the exponential:

$$
\displaystyle
\int e^{-j\omega t}\,dt=\frac{e^{-j\omega t}}{-j\omega}
$$

  - Apply the limits:

$$
\displaystyle
F_p(\omega)=\left[\frac{e^{-j\omega t}}{-j\omega}\right]_{-T/2}^{T/2}
=
\frac{e^{-j\omega(T/2)}-e^{j\omega(T/2)}}{-j\omega}
$$

  - Use $e^{-ja}-e^{ja}=-2j\sin(a)$ to simplify:

$$
\displaystyle
F_p(\omega)=\frac{2\sin(\omega T/2)}{\omega}
$$

  - This is the “pulse ↔ sinc-like spectrum” behaviour the slides are illustrating.

---

## 6) Reconstructing a signal from its Fourier transform (inverse transform intuition)

- Slide idea:
  - Each frequency $\omega$ contributes an oscillatory component.
  - Integrating (summing) over a range of $\omega$ reconstructs the signal.
- What the reconstruction figure demonstrates:
  - Individual contributions for $\omega = 1,2,3,4$ look like different oscillations.
  - Adding more components makes the reconstructed signal look closer to the original pulse.

---

## 7) Complex output: magnitude and phase

- **Fourier transform values are complex**
  - Write:

$$
\displaystyle
F_p(\omega)=\operatorname{Re}(F_p(\omega)) + j\,\operatorname{Im}(F_p(\omega))
$$

- **Magnitude**
  - “How much” of frequency $\omega$ is present:

$$
\displaystyle
|F_p(\omega)|=\sqrt{\operatorname{Re}(F_p(\omega))^2+\operatorname{Im}(F_p(\omega))^2}
$$

- **Phase**
  - Encodes alignment/shift information of components:


$$
\displaystyle
\arg(F_p(\omega))=\tan^{-1}\left(\frac{\operatorname{Im}(F_p(\omega))}{\operatorname{Re}(F_p(\omega))}\right)
$$

- Pulse example note from slides:
  - Magnitude and phase plots are shown for the pulse (phase often appears at $0$ or $2\pi$ in the shown case).

---

## 8) Example: Fourier transform of a musical chord

- A chord is a mixture of multiple sinusoidal frequencies.
- Its Fourier transform shows multiple frequency components corresponding to different tones/harmonics.

---

## 9) Why phase matters (highly examinable conceptual result)

- Slide demonstration:
  - Reconstruct images by combining:
    - magnitude from image A + phase from image B, then inverse transform.
- Result:
  - The output tends to resemble the image that contributed the **phase** (structure is strongly phase-driven in this example).
- Exam takeaway:
  - **Magnitude** = “how much of each frequency”.
  - **Phase** = “how components align in space/time” (crucial for recognisable structure).

---

## 10) Fourier for reconstruction / coding (compression intuition shown)

- Slide pipeline idea:
  - Transform image → keep/encode selected transform information → inverse transform to reconstruct.
- Shown result:
  - Keeping only **5% of transform components** can still reconstruct an image that looks close to the original, with an “error image” showing what was lost.

---

## 11) Main points (slide summary)

- **Sampling data is not as simple as it appears.**
- **Sampling affects space and brightness.**
- **Fourier helps us understand frequency.**
- **Fourier enables coding (compression) and more.**
- Next framing from slides: Fourier will be used to understand sampling.

---

## 12) Other transforms mentioned (names only; not expanded in slides)

- **Discrete Cosine (Sine) Transform**
- **Discrete Hartley Transform**
- **Wavelet families/types** (examples listed): continuous, discrete, complex, stationary, dual, Haar, Daubechies, Morlet, Gabor, etc.
- Other named transform families: **curvelets, shearlets, bandelets, contourlets, fresnelets, chirplets, noiselets, …**

---

## 13) Wavelet transform (what the slide example is showing)

- Slide shows an example **2D discrete wavelet transform used in JPEG2000**:
  - Output is a collection of sub-images capturing information at different **scales** (coarse-to-fine) and **detail patterns**.
- Key intuition consistent with the slide’s visual:
  - Wavelets represent images using components that are:
    - **spatially localised** (where something happens), and
    - **scale-sensitive** (coarse structure vs fine detail).
