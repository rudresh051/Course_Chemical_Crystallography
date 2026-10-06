# Background Removal

**Background removal** is one of the most fundamental XRD preprocessing operations, because before you can properly analyze diffraction peaks, you need to distinguish:

> **Intensity caused by the crystalline diffraction peaks** from **intensity that is present underneath them for other reasons.**

Your screenshot is especially interesting because Project is exposing several different mathematical models for estimating that background.

Let's build it from first principles.

---

# 1. What does an XRD instrument actually measure?

Start with the same basic quantity:

$$
I(2\theta)
$$

The instrument records intensity at each diffraction angle.

But the measured intensity isn't purely the crystalline diffraction signal.

A useful model is:

$$
\boxed{
I_{\text{measured}}(2\theta) =
I_{\text{Bragg}}(2\theta)
+
I_{\text{background}}(2\theta)
+
I_{\text{noise}}(2\theta)
}
$$

This equation is the foundation of background removal.

Where:

- $(I_{\text{Bragg}}$) = diffraction from crystalline structure
- $(I_{\text{background}}$) = unwanted/non-Bragg intensity
- $(I_{\text{noise}}$) = random measurement fluctuations

---

# 2. What does the background look like?

Imagine an ideal crystalline pattern:

```text
Intensity
   │
   │                 /\              /\
   │                /  \            /  \
   │       /\      /    \          /    \
   │      /  \    /      \        /      \
   │_____/____\__/________\______/________\____
   │
   └────────────────────────────────────────── 2θ
```

Now imagine the same sample has a background:

```text
Intensity
   │
   │                 /\              /\
   │                /  \            /  \
   │       /\      /    \          /    \
   │      /  \    /      \        /      \
   │_____/____\__/________\______/________\____
   │    ╲______________________________╱
   │           background
   └────────────────────────────────────────── 2θ
```

The detector sees **both**.

The background is essentially the broad, slowly varying intensity underneath the sharp diffraction features.

---

# 3. Where does this background come from?

This is the important first-principles question.

The X-ray beam interacts with more than just the ideal crystal planes responsible for Bragg diffraction.

Possible contributors include:

### 1. Air scattering

X-rays can scatter from material between the source, sample, and detector.

### 2. Sample holder / substrate

The holder itself can scatter X-rays.

### 3. Fluorescence

The incident X-rays can excite atoms in the sample.

Those atoms can subsequently emit characteristic X-rays.

This can produce a background contribution that may be substantial for some materials/source combinations.

### 4. Compton/incoherent scattering

Not all scattering is coherent Bragg diffraction.

### 5. Amorphous material

An amorphous component doesn't produce the same sharp Bragg peaks as a crystalline phase.

Instead, it can produce a broad diffuse scattering contribution.

### 6. Other instrumental contributions

Detector response, optics, source characteristics, etc., can also contribute.

So:

$$
\boxed{
\text{Background is real measured intensity, but not the Bragg peaks we want to analyze.}
}
$$

---

# 4. Why is background usually smooth?

This is the key observation that allows background removal to work.

Crystalline Bragg diffraction produces relatively localized peaks:

```text
              /\
             /  \
____________/    \____________
```

Background generally varies much more slowly:

```text
___________________
                  /
                 /
________________/
```

So conceptually:

$$
\boxed{
\text{Bragg signal} \rightarrow \text{localized / structured}
}
$$

while:

$$
\boxed{
\text{Background} \rightarrow \text{broad / slowly varying}
}
$$

This difference allows us to estimate the background mathematically.

---

# 5. A simple numerical example

Suppose the instrument measures:

| $(2\theta$) | Measured intensity |
|---:|---:|
| 20° | 120 |
| 21° | 130 |
| 22° | 150 |
| 23° | 500 |
| 24° | 900 |
| 25° | 600 |
| 26° | 170 |
| 27° | 150 |

Suppose the estimated background is:

| $(2\theta$) | Background |
|---:|---:|
| 20° | 100 |
| 21° | 105 |
| 22° | 110 |
| 23° | 115 |
| 24° | 120 |
| 25° | 125 |
| 26° | 130 |
| 27° | 135 |

Then background-corrected intensity is simply:

$$
\boxed{
I_{\text{corrected}} = I_{\text{measured}}
-
I_{\text{background}}
}
$$

For 24°:

$$
900-120=780
$$

So:

| $(2\theta$) | Measured | Background | Corrected |
|---:|---:|---:|---:|
| 20° | 120 | 100 | 20 |
| 21° | 130 | 105 | 25 |
| 22° | 150 | 110 | 40 |
| 23° | 500 | 115 | 385 |
| 24° | 900 | 120 | 780 |
| 25° | 600 | 125 | 475 |
| 26° | 170 | 130 | 40 |
| 27° | 150 | 135 | 15 |

That's the basic operation.

---

# 6. Think of background removal as separating two signals

Imagine the measured pattern is:

```text
             MEASURED XRD
                  │
                  │
          ┌───────┴────────┐
          │                │
          ▼                ▼
     Bragg peaks       Background
          │                │
          │                │
          └───────┬────────┘
                  │
                  ▼
             Detector
```

We want to mathematically estimate:

$$
B(2\theta)
$$

and then calculate:

$$
\boxed{
I_{\text{corrected}}(2\theta) =
I_{\text{measured}}(2\theta)-B(2\theta)
}
$$

The **hard part is not subtraction**.

The hard part is:

> **How do we estimate \(B(2\theta)\) without accidentally treating real diffraction peaks as background?**

That is exactly why your UI offers many different methods.

---

# 7. Look at your screenshot

Your Project UI offers:

1. Chebyshev — monomial power series
2. Chebyshev-1 — true Chebyshev, 1st kind
3. Cosine Fourier series
4. \(Q^2\) power series
5. \(Q^{-2}\) power series
6. Linear / inverse-Q / log-Q interpolation between nodes
7. Debye structureless (amorphous) term
8. Peaks-in-background (Bragg profiles)
9. Fixed background histogram
10. Auto-background — arPLS / iarPLS

These are **different ways of estimating \(B(2\theta)\)**.

They aren't ten different definitions of background.

They are ten different **models/algorithms for estimating the same underlying concept**.

---

# 8. Method 1 — Polynomial background

The simplest mathematical idea is:

> "The background is a smooth mathematical curve."

For example:

$$
B(x)=a_0+a_1x+a_2x^2+a_3x^3+\cdots
$$

where:

$$
x=2\theta
$$

You fit this smooth function to the observed pattern.

Conceptually:

```text
Measured pattern

     /\       /\          /\
    /  \     /  \        /  \
___/____\___/____\______/____\____
  ╲____________________________╱
       fitted background
```

Then:

$$
I_{\text{corrected}}=I_{\text{measured}}-B
$$

---

# 9. Why use Chebyshev polynomials?

Your first option says:

> **Chebyshev (monomial power series — despite the name)**

This wording is actually important.

There are two related but mathematically different ideas.

### Ordinary power series

$$
B(x)=a_0+a_1x+a_2x^2+a_3x^3+\cdots
$$

### True Chebyshev polynomial basis

For example:

$$
T_0(x)=1
$$

$$
T_1(x)=x
$$

$$
T_2(x)=2x^2-1
$$

etc.

Then:

$$
B(x)=c_0T_0(x)+c_1T_1(x)+c_2T_2(x)+\cdots
$$

Your UI explicitly distinguishes:

> **Chebyshev (monomial power series — despite the name)**

from:

> **Chebyshev-1 (true Chebyshev, 1st kind)**

So your implementation appears to expose two different mathematical bases despite the historical/name association.

For QA, I would definitely preserve that distinction in your documentation.

---

# 10. Why fit a polynomial at all?

Because background often changes gradually.

Imagine:

```text
Intensity
   │
   │     /\          /\
   │    /  \        /  \
   │___/____\______/____\____
   │  ╲____________________╱
   │
```

The smooth curve can approximate:

$$
B(2\theta)
$$

The sharper structures are then treated as diffraction peaks.

---

# 11. The problem with polynomial background

Suppose you have:

```text
       /\  /\ 
      /  \/  \
_____/        \________
```

If the polynomial is too flexible, it might start following the peaks.

Instead of:

```text
background:
____________________
```

you could accidentally get:

```text
background:
___/\___/\__________
```

Then you subtract part of the actual diffraction signal.

That's **overfitting**.

So:

$$
\boxed{
\text{Background model must be flexible enough to follow background,
but not flexible enough to follow Bragg peaks.}
}
$$

This is one of the central problems of background removal.

---

# 12. Method 2 — Cosine Fourier series

Your second type is:

> **Cosine Fourier series**

Instead of representing the background using powers of \(x\), we represent it using cosine functions:

$$
B(x) =
a_0
+
a_1\cos(x)
+
a_2\cos(2x)
+
a_3\cos(3x)
+\cdots
$$

More generally:

$$
B(x)=a_0+\sum_{n=1}^{N}a_n\cos(nx)
$$

The exact scaling/normalization depends on the implementation.

The concept is:

> Build a smooth background by combining smooth cosine waves.

Again:

```text
Measured
     ↓
Estimate smooth background
     ↓
Subtract
     ↓
Corrected pattern
```

---

# 13. Method 3 — \(Q^2\) and \(Q^{-2}\)

Now your screenshot gets more interesting.

You have:

> **Q² power series**

and

> **Q⁻² power series**

This requires understanding **Q**.

In diffraction, the scattering-vector magnitude is commonly defined as:

$$
\boxed{
Q=\frac{4\pi\sin\theta}{\lambda}
}
$$

where:

- \(\theta\) = Bragg angle
- \(\lambda\) = wavelength

Since the XRD plot normally uses \(2\theta\), remember:

$$
\theta=\frac{2\theta}{2}
$$

So \(Q\) is another way of describing the diffraction coordinate.

---

# 14. Why use Q instead of 2θ?

Because \(Q\) is directly connected to reciprocal-space/crystallographic descriptions.

Recall:

$$
d=\frac{\lambda}{2\sin\theta}
$$

Therefore:

$$
\boxed{
Q=\frac{2\pi}{d}
}
$$

This is a very important relationship.

So:

```text
2θ
 │
 ▼
θ
 │
 ▼
sinθ
 │
 ▼
Q
 │
 ▼
reciprocal-space coordinate
```

Some background models behave more naturally when represented in \(Q\) rather than directly in \(2\theta\).

---

# 15. What does a Q² power series mean?

Conceptually:

$$
B(Q)=a_0+a_1Q^2+a_2Q^4+a_3Q^6+\cdots
$$

Again, the exact implementation may use a particular parameterization, but the important idea is:

> Instead of fitting background as a function of \(2\theta\), fit it as a function of \(Q\), using powers related to \(Q^2\).

Similarly, a \(Q^{-2}\) model uses inverse powers:

$$
B(Q)=a_0+\frac{a_1}{Q^2}+\frac{a_2}{Q^4}+\cdots
$$

These can be useful because some scattering/background behavior has a more natural dependence on reciprocal-space variables.

---

# 16. Method 4 — interpolation between background nodes

Your screenshot says:

> **Linear / inverse-Q / log-Q interpolation between nodes**

This is a very intuitive method.

Instead of asking a polynomial to determine the entire background, you manually or algorithmically define points that are believed to represent background:

```text
Intensity
   │
   │         /\            /\
   │        /  \          /  \
   │   ●___/    \___●____/    \___●
   │
   └────────────────────────────── 2θ
       ↑           ↑          ↑
     node        node       node
```

The nodes represent:

> "At these locations, I believe the intensity belongs mainly to the background."

Then connect them.

### Linear interpolation

Simply draw straight lines:

```text
●────────────●────────────●
```

### Inverse-Q interpolation

Interpolate in a coordinate related to:

$$
\frac{1}{Q}
$$

### Log-Q interpolation

Interpolate using:

$$
\log Q
$$

The important concept is:

$$
\boxed{
\text{Choose background anchor points → construct smooth background between them}
}
$$

This can be powerful when the user knows where the true background lies.

---

# 17. Method 5 — Debye structureless / amorphous term

This one is conceptually different.

Your screenshot says:

> **Debye structureless (amorphous) term**

Suppose your sample contains an amorphous component.

A crystalline material produces sharp Bragg peaks:

```text
             /\      /\
            /  \    /  \
___________/    \__/    \________
```

An amorphous material tends to produce broad diffuse scattering:

```text
             __________
           /            \
__________/              \________
```

That broad contribution can form part of what we call background.

So instead of simply saying:

> "background = arbitrary smooth curve"

we can say:

> "Some of this background comes from structureless/amorphous scattering."

A Debye-type model attempts to represent that physically.

This is more **physics-informed** than simply fitting an arbitrary polynomial.

---

# 18. Method 6 — Peaks-in-background

This is particularly interesting.

Your UI says:

> **Peaks-in-background (Bragg profiles)**

Instead of saying:

> "First find background, then peaks."

we can mathematically model:

$$
I_{\text{observed}} =
B(2\theta)
+
\sum_i P_i(2\theta)
$$

where:

- \(B\) = background
- \(P_i\) = diffraction peak profile

For example:

```text
Observed
   │
   │          /\             /\
   │         /  \           /  \
   │________/____\_________/____\____
             +              +
         background
```

The algorithm simultaneously tries to explain the pattern as:

$$
\boxed{
\text{background}+\text{Bragg peaks}
}
$$

This can be more sophisticated than simply drawing a smooth line underneath the peaks.

---

# 19. Method 7 — Fixed background histogram

Your screenshot says:

> **Fixed background histogram (point-by-point subtraction)**

This is conceptually very straightforward.

Suppose you already have a background file:

```text
2θ       Background
20.00      100
20.01      102
20.02      104
...
```

Then:

$$
I_{\text{corrected}}(x)
=
I_{\text{sample}}(x)
-
I_{\text{background}}(x)
$$

point by point.

This is useful when you have measured the background separately.

For example:

```text
Run 1:
empty sample holder
       ↓
background pattern


Run 2:
actual sample
       ↓
sample + background


subtract:
       ↓

sample diffraction signal
```

This is fundamentally different from estimating the background from the sample pattern itself.

---

# 20. Method 8 — arPLS / iarPLS

Your last option:

> **Auto-background — arPLS / iarPLS (pybaselines Whittaker, not rolling-ball)**

This is a more automated baseline-estimation approach.

The key idea is:

> Find a smooth baseline that lies underneath the signal while avoiding being pulled upward by strong peaks.

Think:

```text
Observed

              /\           /\
             /  \         /  \
            /    \       /    \
___________/      \_____/      \____
        ╲________________________╱
                 ↑
              baseline
```

A Whittaker/arPLS-style method uses a mathematical optimization balancing:

1. **Smoothness of the baseline**
2. **Agreement with points believed to represent background**

Conceptually, it minimizes something like:

$$
\boxed{
\text{data-fitting penalty}
+
\lambda\cdot\text{smoothness penalty}
}
$$

The exact arPLS formulation uses an asymmetric weighting strategy so that positive peaks have less influence on the estimated baseline.

That's the key insight.

---

# 21. Why "not rolling-ball" matters

Your UI explicitly says:

> **Whittaker, not rolling-ball**

That's worth documenting.

"Baseline removal" has several families of algorithms.

A rolling-ball algorithm thinks geometrically:

```text
                  peak
                   /\
              ____/  \____
             /
        ___/ 
       O
    conceptual
     ball
```

The baseline is estimated from a geometric rolling operation.

Your UI is explicitly saying that its Auto-background implementation instead uses the **Whittaker/arPLS family**, not rolling-ball.

So these shouldn't be treated as interchangeable algorithms.

---

# 22. The biggest challenge in background removal

Now we arrive at the most important scientific issue.

Suppose the pattern is:

```text
             weak peak
                /\
               /  \
_______________/    \_______________
```

How does the algorithm know whether this little bump is:

### A. A real diffraction peak?

or:

### B. Background variation?

It can't simply say:

> "Anything small is background."

because weak peaks are scientifically important.

Therefore:

$$
\boxed{
\text{Background estimation is fundamentally a signal-separation problem.}
}
$$

---

# 23. Think about weak peaks

Imagine:

```text
Intensity

Strong peak
             /\
            /  \
           /    \
__________/      \____

Weak peak

       /\
______/  \________________
```

If your background algorithm is too aggressive:

```text
Strong peak
             /\
            /  \
___________/    \_________

Weak peak

___________________________
```

The weak peak disappears.

You've made a scientifically incorrect transformation.

---

# 24. Over-subtraction

Suppose:

$$
I_{\text{measured}}=100
$$

but the algorithm estimates:

$$
B=120
$$

Then:

$$
I_{\text{corrected}}=100-120=-20
$$

You now have:

$$
\boxed{I_{\text{corrected}}<0}
$$

Negative intensity isn't necessarily impossible as a mathematical residual after background subtraction, but for a conventional intensity dataset it is a strong indication that the background model may be inappropriate or that the data require special handling.

So this is an important QA case.

---

# 25. Under-subtraction

The opposite problem:

Measured:

$$
I=500
$$

True background:

$$
B=100
$$

but algorithm estimates:

$$
B=50
$$

Then:

$$
I_{\text{corrected}}=450
$$

instead of:

$$
400
$$

So background remains in the corrected pattern.

---

# 26. Overfitting vs underfitting

This is a great way to understand background algorithms.

### Underfitted background

```text
Observed:

       /\       /\
      /  \     /  \
_____/____\___/____\____

Estimated background:

________________________
```

It doesn't capture the real background variation.

### Overfitted background

```text
Observed:

       /\       /\
      /  \     /  \
_____/____\___/____\____

Estimated:

___/\____/\_____________
```

It follows actual peaks.

That is bad.

The ideal is:

```text
       /\       /\
      /  \     /  \
_____/____\___/____\____
    ╲__________________╱
```

A smooth curve underneath the actual diffraction features.

---

# 27. Why background removal is so important for downstream XRD

Consider peak intensity.

Before background removal:

$$
I_{\text{peak}}=
I_{\text{Bragg}}+I_{\text{background}}
$$

After:

$$
I_{\text{peak,corrected}}
\approx
I_{\text{Bragg}}
$$

This matters for:

- peak detection
- peak height
- integrated intensity
- peak fitting
- phase identification
- quantitative analysis
- Rietveld refinement
- signal-to-noise estimation

For example, if your background is 500 counts and your actual weak peak is 100 counts:

```text
Measured = 600
```

It is easy to miss that the actual diffraction signal is only:

$$
100
$$

Background removal makes that structure easier to analyze.

---

# 28. Important distinction: background removal vs smoothing

You've now learned smoothing, so compare them.

### Smoothing

Raw:

```text
100 103 98 105 101 106
```

tries to produce:

```text
101 101 102 103 103 104
```

It reduces **noise fluctuations**.

### Background removal

Raw:

```text
background + peak
```

tries to produce:

```text
peak only
```

So:

$$
\boxed{
\text{Smoothing → reduce noise}
}
$$

$$
\boxed{
\text{Background removal → remove baseline/background contribution}
}
$$

These are fundamentally different operations.

---

# 29. Connect it with the other preprocessing operations

You now have a very useful mental model:

```text
RAW XRD
   │
   ├── Zero-offset
   │       ↓
   │    Fix X-axis
   │
   ├── Spike rejection
   │       ↓
   │    Remove isolated artifacts
   │
   ├── Kα₂ stripping
   │       ↓
   │    Remove Kα₂ contribution
   │
   ├── Smoothing
   │       ↓
   │    Reduce random noise
   │
   └── Background removal
           ↓
        Remove baseline
   │
   ▼
CLEANER XRD PATTERN
   │
   ▼
Peak analysis / indexing / refinement
```

Although the **actual order in Project should be confirmed from the implementation**, because some operations interact with each other.

---

# 30. QA perspective for your Project feature

This feature is actually much more interesting to test than "select method → click Apply."

You should test the **scientific behavior of the background model**.

### Test 1 — Flat background

```text
Signal + constant background
```

Expected:

$$
B(x)=C
$$

After subtraction, baseline should approach zero.

---

### Test 2 — Sloping background

```text
        /\
       /  \
______/    \________
   /
  /
```

The algorithm should follow the gradual slope without following the peak.

---

### Test 3 — Curved background

Test a nonlinear baseline.

Useful for checking polynomial/Fourier/arPLS behavior.

---

### Test 4 — Strong peaks

Make sure the background doesn't rise underneath them:

```text
        /\ 
       /  \
______/    \____
       ↑
background should NOT
follow the peak
```

---

### Test 5 — Weak peaks

Very important.

Make sure a weak diffraction peak isn't treated as background.

---

### Test 6 — Multiple close peaks

```text
       /\ /\
      /  V  \
_____/       \____
```

Check that the background model doesn't pass through the valley between them incorrectly.

---

### Test 7 — Negative corrected intensities

Check what Project does when:

$$
I_{\text{measured}} < B
$$

Does it:

- retain negative values?
- clamp them to zero?
- show a warning?
- reject the result?

This should be explicitly defined.

---

### Test 8 — Fixed histogram

Provide an independently measured background.

Check:

$$
I_{\text{corrected}}(i)
=
I_{\text{sample}}(i)-I_{\text{background}}(i)
$$

point by point.

This is one of the easiest methods to verify numerically.

---

# 31. One particularly important test for your implementation

Since your UI has **multiple background algorithms**, take the **same XRD pattern** and run:

```text
Chebyshev
Chebyshev-1
Cosine Fourier
Q²
Q⁻²
Interpolation
Debye
Peaks-in-background
Fixed histogram
arPLS
```

Then compare:

- estimated background
- corrected intensity
- peak position
- peak height
- FWHM
- integrated intensity
- negative values
- weak peak preservation

You should **not expect all methods to produce identical results**.

That's not necessarily a bug.

They are different models.

The question is whether each method behaves according to its intended mathematical/physical model.

---

# 32. One thing I would clarify with your developer/scientist

Because your UI exposes:

> `Chebyshev (monomial power series — despite the name)`

and

> `Chebyshev-1 (true Chebyshev, 1st kind)`

I'd specifically ask:

> **What exact mathematical implementation and parameterization are we using for each background function, and what are the configurable parameters exposed to the user?**

Especially for:

- polynomial order
- Chebyshev order
- Fourier order
- Q transformation
- interpolation nodes
- Debye parameters
- Bragg profile model
- arPLS smoothing/regularization parameters

Without that, you can test the UI behavior, but you can't fully validate the **scientific correctness** of each algorithm.

---

# 33. The deepest mental model

Think about your raw XRD as three layers:

```text
                    RAW XRD
                       │
        ┌──────────────┼──────────────┐
        │              │              │
        ▼              ▼              ▼
     Bragg          Background       Noise
     peaks
        │              │              │
        │              │              │
        └──────────────┼──────────────┘
                       │
                       ▼
                   Detector
```

Background removal is trying to isolate:

$$
\boxed{I_{\text{Bragg}}}
$$

by estimating:

$$
\boxed{B(2\theta)}
$$

and performing:

$$
\boxed{
I_{\text{corrected}}
=
I_{\text{measured}}-B
}
$$

The **subtraction is easy**.

The scientifically difficult part is deciding:

$$
\boxed{\text{"What exactly is background?"}}
$$

That's why your Project screen has so many different algorithms.

---

## Your four preprocessing concepts so far

| Operation | What is wrong in the raw data? | What are we trying to preserve/correct? |
|---|---|---|
| **Zero-offset correction** | $(2\theta$) coordinate is systematically shifted | Correct peak positions |
| **Spike rejection** | Isolated abnormal intensity values | Preserve genuine signal, remove artifacts |
| **Kα₂ stripping** | Two closely spaced wavelengths contribute to the pattern | Approximate Kα₁-only pattern |
| **Smoothing** | Random/high-frequency fluctuations | Preserve peak shape while reducing noise |
| **Background removal** | Non-Bragg intensity underneath the pattern | Isolate the crystalline/Bragg signal |

The key sentence to remember for **background removal** is:

> **We are not removing "low intensity." We are estimating the intensity that would be present even without the Bragg diffraction peaks, and subtracting that estimated contribution.**

That distinction will become very important when you start studying **Rietveld refinement**, because background in a refinement isn't just a cosmetic baseline—the refinement explicitly models background as part of the calculated pattern.