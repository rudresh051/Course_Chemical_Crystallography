# Smoothing

For your project implementation is exposing **three smoothing methods**:

1. **Savitzky–Golay (quadratic polynomial)**
2. **Bromba–Ziegler recursive integer filter**
3. **Raw-data smoothing (repeatable N-step)**

Let's understand **smoothing itself first**, then these three methods.

---

# 1. Start from the raw XRD measurement

An XRD instrument gives you a sequence of measurements:

$$
I(2\theta)
$$

For example:

| \(2\theta\) | Intensity |
|---:|---:|
| 20.00 | 100 |
| 20.01 | 105 |
| 20.02 | 98 |
| 20.03 | 110 |
| 20.04 | 103 |
| 20.05 | 108 |
| 20.06 | 104 |

Ideally, the intensity should represent the **true diffraction signal**.

But a real measurement contains random fluctuations.

A useful first-principles model is:

$$
\boxed{
I_{\text{measured}}
=
I_{\text{true}}
+
I_{\text{noise}}
}
$$

So if the true signal is:

```text
Intensity
   │
   │             /\
   │            /  \
   │           /    \
   │__________/______\________
```

the measured signal might look like:

```text
Intensity
   │
   │            /\/\
   │          /\/  \_/\
   │       _/          \_
   │______/_______________\____
```

The **overall diffraction structure is there**, but small fluctuations are riding on top of it.

---

# 2. What is smoothing trying to do?

Smoothing tries to estimate:

$$
\boxed{I_{\text{true}}}
$$

from:

$$
I_{\text{measured}}
$$

In other words:

```text
RAW DATA
   │
   │  noise
   ▼
████████████████
   │
   │ Smoothing
   ▼
───────────────
cleaner signal
```

The important word is **estimate**.

Smoothing does **not** magically recover the exact original signal.

It applies a mathematical filter that assumes:

> "The underlying XRD signal should vary more smoothly than the random noise."

---

# 3. Why should XRD peaks be smooth?

Think about what creates a diffraction peak.

A crystal has a particular set of lattice planes.

When Bragg's condition is satisfied:

$$
n\lambda=2d\sin\theta
$$

you get enhanced diffraction intensity.

But the instrument doesn't measure an infinitely sharp mathematical point.

Real peaks have finite width because of things such as:

- instrument resolution
- crystallite size
- microstrain
- wavelength distribution
- sample characteristics
- instrumental broadening

So a real peak usually has a continuous shape:

$$
I=f(2\theta)
$$

rather than:

```text
100 → 100 → 1000 → 100 → 100
```

Therefore, random point-to-point fluctuations can often be treated as noise.

---

# 4. Think about smoothing as "looking around the point"

Suppose we have:

```text
Intensity

100
105
98
110   ← current point
103
108
104
```

A smoothing algorithm says:

> "Instead of trusting this one measurement completely, let's look at neighboring measurements and estimate what the underlying value should be."

This is the fundamental idea behind almost every smoothing technique.

---

# 5. The simplest possible smoothing: moving average

Before Savitzky–Golay, understand the simplest filter.

Suppose we use three points:

$$
[100,\;105,\;98]
$$

The average is:

$$
\frac{100+105+98}{3}=101
$$

So instead of using:

$$
I_i=105
$$

we use:

$$
I_i^{smooth}=101
$$

Then move one position:

```text
100   105   98
      ↑
    smooth

      105   98   110
             ↑
           smooth
```

This is called a **moving average**.

Conceptually:

$$
\boxed{
I_i^{smooth}
=
\frac{
I_{i-1}+I_i+I_{i+1}
}{3}
}
$$

---

# 6. Why does this reduce noise?

Imagine random noise:

```text
+5
-3
+7
-4
+2
-6
```

When you average neighboring measurements, positive and negative fluctuations partially cancel.

So:

$$
\boxed{\text{smoothing reduces high-frequency variation}}
$$

This is essentially what a low-pass filter does.

---

# 7. But moving averages have a serious XRD problem

Consider a sharp diffraction peak:

```text
                 /\
                /  \
_______________/    \_______________
```

If you average neighboring points too aggressively, you can get:

```text
               ______
              /      \
_____________/        \____________
```

The peak becomes:

- lower
- wider
- less sharp

You have altered the actual diffraction information.

This is a fundamental tradeoff:

$$
\boxed{
\text{Noise reduction}
\quad\leftrightarrow\quad
\text{Peak preservation}
}
$$

This is why XRD smoothing isn't simply:

> "Average everything."

Research specifically examining XRD smoothing shows that smoothing can distort peak width, height, shape and other peak parameters; peak-width/FWHM distortion can be particularly important. [Wiley Online Library](https://onlinelibrary.wiley.com/doi/abs/10.1107/S0021889800006932?utm_source=chatgpt.com)

---

# 8. This brings us to Savitzky–Golay

Your screenshot shows:

> **Savitzky–Golay (quadratic polynomial)**

This is a very important method for XRD.

Instead of saying:

> "I'll average these points."

Savitzky–Golay says:

> **"I'll fit a small polynomial through the neighboring points and use that polynomial to estimate the center point."**

For example, consider:

```text
      •
   •     •
 •         •
      ↑
    center
```

The algorithm takes a window around the center:

$$
I_{i-k},\ldots,I_i,\ldots,I_{i+k}
$$

and fits a polynomial.

For your screenshot, the polynomial is quadratic:

$$
\boxed{
y=a+bx+cx^2
}
$$

The fitted polynomial is then used to calculate the smoothed value at the center.

---

# 9. Why is that better for XRD?

Consider this peak:

```text
                  /\
                /    \
              /        \
____________/            \____________
```

A moving average sees:

> "Let's average these points."

Savitzky–Golay sees:

> "These neighboring points form a curved structure. Let me fit a local quadratic curve."

So it can preserve the **shape of the peak** better than a simple moving average.

This is one reason Savitzky–Golay is widely used for spectral data and is specifically useful when you want smoothing without destroying important signal features. [OriginLab Documentation](https://docs.originlab.com/originc/ref/ocmath_savitsky_golay/?utm_source=chatgpt.com)

---

# 10. What does "quadratic polynomial" mean in your UI?

Your screenshot says:

> Savitzky–Golay **(quadratic polynomial)**

Quadratic means:

$$
\boxed{y=a+bx+cx^2}
$$

The algorithm doesn't fit one giant quadratic curve to the entire XRD pattern.

That's important.

It repeatedly fits a **small local window**.

For example:

```text
Entire XRD pattern

─────────────────────────────────────────────
     ↑
   local
   window
```

Then:

```text
               local window
             ┌─────────────┐
             │ • • • • • • │
             └─────────────┘
                     ↑
                   point
```

It moves this window across the entire dataset.

---

# 11. What is the "window"?

Suppose you have:

```text
2θ:
20.00
20.01
20.02
20.03
20.04
20.05
20.06
```

A 5-point window might be:

```text
20.01  20.02  20.03  20.04  20.05
                  ↑
               center
```

Fit a polynomial to those five points.

Then move:

```text
20.02  20.03  20.04  20.05  20.06
                  ↑
               center
```

and repeat.

This is why it is called a **moving-window filter**.

---

# 12. The window size matters enormously

Suppose your XRD peak is only about 7 data points wide.

If you use:

### Small window

```text
        /\
       /  \
______/    \______
```

the peak is mostly preserved.

### Huge window

```text
        ______
       /      \
______/        \______
```

the peak can become flattened/broadened.

So:

$$
\boxed{\text{window size is a critical smoothing parameter}}
$$

This is one of the first things I'd want to know about the Your project implementation.

---

# 13. Another important parameter: polynomial order

You have:

> quadratic polynomial

which means:

$$
\text{order}=2
$$

You could theoretically have:

$$
\text{linear} \rightarrow y=a+bx
$$

$$
\text{quadratic} \rightarrow y=a+bx+cx^2
$$

$$
\text{cubic} \rightarrow y=a+bx+cx^2+dx^3
$$

etc.

A higher-order polynomial can follow more complicated local shapes, but that doesn't automatically mean it is better.

The choice of:

$$
\boxed{\text{window size + polynomial order}}
$$

determines how aggressively the signal is smoothed and how well features are preserved.

---

# 14. Now look at your second method

Your screenshot shows:

> **Bromba–Ziegler recursive integer filter**

This is conceptually different from Savitzky–Golay.

The important word is:

$$
\boxed{\text{recursive}}
$$

A recursive filter uses previously processed values as part of calculating subsequent values.

In contrast, a conventional non-recursive filter processes the local neighborhood directly.

The Bromba–Ziegler approach is documented as a recursive implementation for spectral smoothing, in contrast to the traditional nonrecursive Savitzky–Golay approach. [ScienceDirect](https://www.sciencedirect.com/science/article/pii/016974398980084X?utm_source=chatgpt.com)

You can think conceptually:

```text
Raw point 1
     ↓
Filtered point 1
     ↓
Raw point 2 + previous filtered information
     ↓
Filtered point 2
     ↓
Raw point 3 + previous filtered information
     ↓
Filtered point 3
```

rather than:

```text
Take independent local window
        ↓
Calculate smoothed center
        ↓
Move window
```

The exact recurrence and parameters should come from the implementation/documentation your developers are using; I would **not assume the Your project version is mathematically identical to every published Bromba–Ziegler implementation**.

---

# 15. What does "integer filter" suggest?

It means the filter can be implemented using integer-valued coefficients/arithmetic in its filtering scheme.

That's useful computationally because such filters can be efficient.

But from your perspective as QA, the more important distinction is:

```text
Savitzky–Golay
→ local polynomial fitting


Bromba–Ziegler
→ recursive digital filtering
```

Both are trying to accomplish the same high-level goal:

$$
\boxed{\text{reduce noise while preserving the underlying signal}}
$$

but they do it differently.

---

# 16. Your third option: Raw-data smoothing

Your screenshot shows:

> **Raw-data smoothing (repeatable N-step) – Match!**

This sounds like a much more straightforward filtering operation.

But there is an important thing here:

**"Raw-data smoothing" is not a universally defined mathematical algorithm in the same way that Savitzky–Golay is.**

So for Your project, you need to know exactly what **"N-step"** means in your implementation.

For example, it could mean:

```text
Raw
 ↓
smoothing pass 1
 ↓
smoothing pass 2
 ↓
...
 ↓
N passes
```

Hence:

$$
S_N(x)=F^N(x)
$$

where \(F\) is the smoothing operation and \(N\) is the number of repetitions.

If that's what your developers mean, then:

### N = 1

```text
Raw → Smooth
```

### N = 2

```text
Raw → Smooth → Smooth again
```

### N = 5

```text
Raw
 ↓
Smooth
 ↓
Smooth
 ↓
Smooth
 ↓
Smooth
 ↓
Smooth
```

The more times you apply it, the more aggressively the pattern may be smoothed.

But again, **verify the exact Your project implementation** before treating that as the specification.

---

# 17. Smoothing is NOT spike rejection

This distinction is very important because you've just learned spike rejection.

Suppose your pattern is:

```text
             real peak
                /\
               /  \
______/\/\/\__/    \____
      ↑
     noise
```

### Spike rejection

Looks for something like:

```text
100
102
101
5000  ← isolated abnormal point
103
101
```

Question:

> "Is this point an artifact?"

### Smoothing

Looks at:

```text
100
105
98
110
103
108
```

Question:

> "Can I estimate the underlying smooth signal despite these small fluctuations?"

So:

$$
\boxed{\text{Spike rejection = artifact removal}}
$$

while:

$$
\boxed{\text{Smoothing = noise reduction}}
$$

They can complement each other, but they are not the same operation.

---

# 18. Smoothing also isn't background subtraction

This is another useful distinction.

Imagine:

```text
                 peak
                  /\
                 /  \
________________/    \________________
   rising background
```

The baseline underneath the peak is background.

### Background removal asks:

> "What part of this intensity is background rather than diffraction?"

### Smoothing asks:

> "Can I reduce small fluctuations in the measured signal?"

So:

| Operation | Main question |
|---|---|
| Spike rejection | Is this point an artifact? |
| Smoothing | Is this fluctuation noise? |
| Background removal | What intensity comes from background? |
| Zero-offset correction | Is the \(2\theta\) coordinate shifted? |
| Kα₂ stripping | Can we remove the Kα₂ contribution? |

---

# 19. The most important danger: over-smoothing

This is probably the most important concept for your QA work.

Suppose your raw pattern is:

```text
                  /\
                 /  \
                /    \
_______________/      \_______________
```

A reasonable smoothing:

```text
                  /\
                 /  \
________________/    \________________
```

Fine.

But aggressive smoothing:

```text
                ______
_______________/      \_______________
```

Now you've changed the scientific information.

You may have altered:

- peak height
- peak width
- peak shape
- sometimes apparent peak position
- small/closely spaced peaks
- shoulders

Studies of smoothing specifically on XRD peaks show that smoothing can cause flattening and broadening, and that FWHM can be significantly affected. [Wiley Online Library](https://onlinelibrary.wiley.com/doi/abs/10.1107/S0021889800006932?utm_source=chatgpt.com)

So:

$$
\boxed{
\text{More smoothing} \neq \text{better XRD data}
}
$$

---

# 20. Think about two peaks close together

This is an excellent example.

Raw:

```text
Intensity

             /\  /\
            /  \/  \
___________/        \___________
```

There are **two peaks**.

Now apply aggressive smoothing:

```text
              ______
_____________/      \____________
```

You may have effectively turned:

$$
\boxed{\text{two peaks}}
$$

into:

$$
\boxed{\text{one broad peak}}
$$

That can be disastrous for:

- phase identification
- peak indexing
- phase quantification
- peak fitting

This is why smoothing must be conservative.

---

# 21. Why smoothing can actually help analysis

Now the positive side.

Suppose the raw pattern is:

```text
        noise everywhere
       /\/\/\/\/\/\/\/\/\
      /                  \
_____/                    \____
```

Peak detection may struggle.

After reasonable smoothing:

```text
             /\
            /  \
___________/    \___________
```

The underlying structure becomes easier to detect.

So smoothing can improve:

- peak detection
- visualization
- signal-to-noise ratio
- locating approximate peak positions
- identifying weak features

But there is a critical caveat:

> **A smoothed pattern should not automatically replace the raw pattern for quantitative refinement.**

For quantitative work, you need to know exactly what your analysis engine expects and whether preprocessing changes the data model.

---

# 22. One useful mathematical perspective: low-pass filtering

Noise often contains rapid point-to-point changes.

For example:

```text
100 → 102 → 99 → 104 → 98 → 103
```

That's high-frequency variation.

The underlying XRD signal changes more slowly:

```text
100 → 110 → 130 → 160 → 200 → 250
```

Smoothing acts approximately like a **low-pass filter**:

```text
Measured signal
      │
      ├── slow variations → KEEP
      │
      └── rapid variations → REDUCE
```

That's the deeper signal-processing interpretation.

But remember:

> A real narrow XRD peak also contains relatively high-frequency information.

Therefore, if the filter is too aggressive, it can mistake legitimate diffraction structure for noise.

That's the fundamental smoothing tradeoff.

---

# 23. Let's connect this directly to your Your project UI

Your screenshot:

```text
Smoothing
────────────────────────────────

Method

[ Savitzky–Golay (quadratic polynomial)
  Bromba–Ziegler recursive integer filter
  Raw-data smoothing (repeatable N-step)
]
```

I would mentally model the feature as:

```text
                  RAW XRD
                     │
                     ▼
             ┌───────────────┐
             │   Smoothing   │
             └───────┬───────┘
                     │
          ┌──────────┼──────────┐
          │          │          │
          ▼          ▼          ▼
       S-G       B-Z filter   Raw-data
     quadratic   recursive     N-step
          │          │          │
          └──────────┼──────────┘
                     ▼
              Smoothed XRD
                     │
                     ▼
           Further preprocessing
                     │
                     ▼
                  Analysis
```

---

# 24. What I'd test as QA

For this feature, don't only check:

> "Does the graph look smoother?"

That's subjective.

You should test **scientific behavior**.

### Test 1 — Noisy pattern

Input:

```text
real signal + random noise
```

Expected:

> Noise reduced while major peaks remain recognizable.

---

### Test 2 — Strong peak

Check:

- peak position
- peak height
- FWHM
- shape

before vs after.

---

### Test 3 — Two closely spaced peaks

This is especially important.

```text
    /\ /\
   /  V  \
__/       \__
```

Expected:

> Two peaks should remain distinguishable at an appropriate smoothing setting.

---

### Test 4 — Weak peak beside strong peak

```text
             /\
            /  \
      /\   /    \
_____/  \_/      \____
```

Aggressive smoothing might eliminate the weak peak.

---

### Test 5 — Spike + noise

This tests interaction between:

```text
Spike rejection
       ↓
Smoothing
```

and verifies that smoothing isn't being used as an accidental spike-removal mechanism.

---

### Test 6 — Repeated smoothing

For your:

> **Raw-data smoothing (repeatable N-step)**

test:

$$
N=0
$$

$$
N=1
$$

$$
N=2
$$

$$
N=5
$$

and verify that the behavior is deterministic.

If the operation is intended to be repeatable:

$$
F(F(x)) \neq F(x)
$$

in general, but:

$$
F(F(x))
$$

should be exactly reproducible when applied twice with the same settings.

---

# 25. One test I'd consider especially valuable

Take a known synthetic peak:

$$
I(x)=A\exp\left[-\frac{(x-x_0)^2}{2\sigma^2}\right]
$$

You know exactly:

- peak position \(x_0\)
- maximum intensity \(A\)
- width \(\sigma\)

Then add noise:

$$
I_{\text{measured}}(x) =
I(x)+\epsilon(x)
$$

Run smoothing.

Now measure:

| Parameter | Before | After |
|---|---:|---:|
| Peak position | \(x_0\) | ? |
| Peak height | \(A+\text{noise}\) | ? |
| FWHM | known | ? |
| Integrated area | known | ? |

This gives you an **objective way of measuring whether Your project's smoothing implementation is distorting the peak**.

That's much stronger than visually saying "it looks good."

---

# 26. One subtle point about your screenshot: "SHADOW (documented)"

I would **not treat "SHADOW (documented)" as part of the mathematical name of the smoothing algorithm**.

It appears to be a label indicating that the method is currently a documented/shadowed implementation or reference method in your application.

Similarly:

> `Raw-data smoothing (repeatable N-step) – Match!`

looks like an implementation/status annotation rather than a standard scientific name.

For your test documentation, I'd separate:

**Scientific algorithm**

from:

**Your project implementation/status label.**

That's important because otherwise you may accidentally write a product-specific label as if it were a standard XRD concept.

---

# 27. The whole concept in one picture

Think of the XRD signal as:

```text
             WHAT WE WANT
                  │
                  ▼
         True diffraction signal
                  │
                  +
                  │
             unwanted noise
                  │
                  ▼
             RAW XRD DATA
                  │
                  │
             SMOOTHING
                  │
                  ▼
       Estimated underlying signal
                  │
                  ▼
       Peak detection / analysis
```

And the central mathematical idea is:

$$
\boxed{I_{\text{measured}}(2\theta) = I_{\text{signal}}(2\theta) + I_{\text{noise}}(2\theta)}
$$

Smoothing attempts to estimate:

$$
\boxed{I_{\text{signal}}(2\theta)}
$$

without destroying the scientifically meaningful diffraction peaks.

---

## The key distinction to remember

You've now covered four preprocessing concepts. I would keep this mental table:

| Operation | Problem being corrected | Main effect |
|---|---|---|
| **Zero-offset** | Angular zero/calibration error | Shifts \(2\theta\) |
| **Spike rejection** | Isolated anomalous measurements | Removes/reconstructs isolated intensity points |
| **Kα₂ stripping** | Mixed Kα₁/Kα₂ radiation | Changes peak intensity/shape |
| **Smoothing** | Random/high-frequency noise | Reduces fluctuations in intensity |

And for **smoothing specifically**, remember:

$$
\boxed{
\text{The goal is NOT to make the graph look pretty.}
}
$$

The goal is:

$$
\boxed{
\text{reduce noise}
\quad+\quad
\text{preserve scientifically meaningful diffraction features}
}
$$

That's why **window size, polynomial order, number of passes, and the resulting peak distortion** are much more important QA concerns than simply checking whether the plotted curve looks smoother.