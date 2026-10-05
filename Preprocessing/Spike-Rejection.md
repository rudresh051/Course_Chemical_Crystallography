# Spike Rejection

Yes. **Spike rejection** is much easier to understand once you separate a real XRD peak from an *accidental, very narrow intensity jump*.


## 1. What does an XRD pattern actually contain?

An XRD instrument measures intensity as a function of diffraction angle:

$$
I(2\theta)
$$

So you might have data like:

| $(2\theta$) | Intensity |
|---:|---:|
| 20.00° | 120 |
| 20.01° | 125 |
| 20.02° | 130 |
| 20.03° | 128 |
| 20.04° | 135 |

A real diffraction peak looks something like:

```text
Intensity
   │
   │                 /\
   │                /  \
   │              /      \
   │            /          \
   │___________/______________\________
                       2θ
```

The intensity changes **smoothly over multiple neighboring points**.

Why?

Because a diffraction peak is produced by the crystal structure and the instrument's finite resolution. It isn't normally just one isolated measurement.

---

# 2. What is a spike?

Now imagine the detector records something abnormal:

```text
Intensity
   │
   │                 │
   │                 │
   │                 │
   │______________ __│______________
                  2θ
```

Suppose the data is:

| $(2\theta$) | Intensity |
|---:|---:|
| 20.00° | 120 |
| 20.01° | 122 |
| 20.02° | **9500** ← |
| 20.03° | 124 |
| 20.04° | 126 |

That 9500-count point is suspicious.

It doesn't fit the surrounding pattern.

This is called a **spike**, **outlier**, or sometimes a **cosmic-ray spike / detector artifact**, depending on its origin.

---

# 3. Why can spikes happen?

The important thing is that **not every intensity value comes purely from diffraction from the sample**.

The detector can experience transient events or noise.

For example:

- electronic noise
- detector artifacts
- cosmic-ray events
- unstable detector response
- transient radiation events
- measurement glitches

These can produce an intensity value that is **much higher than its neighboring points**.

Think about taking a temperature measurement:

```text
30.1°C
30.2°C
30.3°C
85.0°C  ← suspicious
30.2°C
30.4°C
```

You wouldn't conclude:

> "The room suddenly became 85°C for exactly one measurement."

You'd suspect an erroneous measurement.

Spike rejection in XRD follows a similar idea.

---

# 4. The important difference: spike vs real diffraction peak

This is **the most important concept**.

Consider these two patterns.

### Real peak

```text
                 /\
                /  \
               /    \
              /      \
_____________/        \____________
```

Data might look like:

```text
100
120
160
230
350
500
350
230
160
120
100
```

There is a smooth progression.

---

### Spike

```text
                    │
                    │
                    │
____________________│________________
```

Data might look like:

```text
100
102
101
105
5000   ← spike
103
101
104
100
```

The difference is:

$$
\boxed{\text{Real peak = structured signal}}
$$

whereas

$$
\boxed{\text{Spike = isolated anomalous measurement}}
$$

---

# 5. Why do we need spike rejection?

Imagine you're doing peak detection.

Your software sees:

```text
Normal pattern:

             /\
            /  \
___________/    \___________


With spike:

             /\
            /  \
_____│_____/    \___________
     ↑
   spike
```

A peak-detection algorithm might think:

> "There is another peak here."

That could lead to incorrect:

- peak positions
- peak intensities
- phase identification
- indexing
- background estimation
- similarity calculations
- refinement results

So spike rejection attempts to remove **obviously non-physical isolated intensity events** before downstream analysis.

---

# 6. What does "rejection" actually mean?

This is an important implementation question.

Suppose your raw data is:

| $(2\theta$) | Intensity |
|---:|---:|
| 20.00 | 100 |
| 20.01 | 105 |
| 20.02 | **5000** |
| 20.03 | 108 |
| 20.04 | 110 |

A spike rejection algorithm might identify:

$$
I(20.02)=5000
$$

as an outlier.

But what do we replace it with?

One simple approach is interpolation.

For example:

$$
I_{\text{replacement}}
=
\frac{I_{\text{previous}}+I_{\text{next}}}{2}
$$

Therefore:

$$
I_{\text{replacement}}
=
\frac{105+108}{2}
=
106.5
$$

The processed data becomes:

| $(2\theta$) | Intensity |
|---:|---:|
| 20.00 | 100 |
| 20.01 | 105 |
| 20.02 | **106.5** |
| 20.03 | 108 |
| 20.04 | 110 |

So we have essentially reconstructed the likely underlying signal.

---

# 7. But how does the software KNOW it's a spike?

This is where things become interesting.

You need a **spike-detection criterion**.

The simplest idea is:

> Compare a point with its neighboring points.

Suppose we have:

$$
I_{i-1}=105
$$

$$
I_i=5000
$$

$$
I_{i+1}=108
$$

The neighboring intensities are around 100, while the current point is 5000.

Therefore:

$$
I_i \gg I_{i-1}, I_{i+1}
$$

That is suspicious.

---

# 8. A simple mathematical approach

One possible spike criterion could be:

$$
I_i > \text{local baseline} + k\sigma
$$

where:

- $(I_i$) = current intensity
- local baseline = estimated intensity around the point
- $(\sigma$) = local variation/noise
- \(k\) = threshold multiplier

For example:

$$
I_i > \mu_{\text{local}} + 5\sigma
$$

might classify the point as an outlier.

This is conceptually similar to statistical outlier detection.

---

# 9. But there's a problem

Imagine a genuine XRD peak:

```text
100
110
150
250
500
900
500
250
150
110
100
```

The central point:

$$
I_i=900
$$

is much larger than its immediate neighbors.

A naive algorithm might say:

> "900 is an outlier!"

But that would be **wrong**.

It's a legitimate diffraction peak.

Therefore:

$$
\boxed{\text{High intensity alone does NOT mean spike}}
$$

This is critical for your QA understanding.

The algorithm needs to consider **shape and locality**, not simply intensity.

---

# 10. Width is one of the key clues

A genuine diffraction peak has a certain width.

For example:

```text
                  /\
                /    \
              /        \
____________/            \____________
```

It occupies several measurement points.

A spike might look like:

```text
                  │
                  │
                  │
__________________│__________________
```

It occupies essentially one or a few points.

So spike rejection often relies on detecting **rapid intensity changes over a very small angular width**.

---

# 11. Think in terms of derivatives

Here's a useful mathematical way to understand it.

Your XRD pattern is approximately:

$$
I=f(2\theta)
$$

For a smooth peak, intensity changes progressively.

The first derivative:

$$
\frac{dI}{d(2\theta)}
$$

changes relatively smoothly.

For a spike, the intensity can jump dramatically:

$$
100\rightarrow5000\rightarrow100
$$

So you get very large positive and negative changes over a tiny angular interval.

Conceptually:

```text
Normal peak:

      /
     /
____/____________
   gradual change


Spike:

____│____
    │
    │
```

The spike has a much sharper local variation.

---

# 12. Another intuitive method: median filtering

A common general signal-processing idea is the **median filter**.

Suppose you have three neighboring values:

$$
[105,\;5000,\;108]
$$

The median is:

$$
108
$$

So the extreme value 5000 can be identified as inconsistent with its neighborhood.

For a normal peak:

$$
[350,\;500,\;350]
$$

the median is:

$$
350
$$

But here's the subtlety: blindly applying a median filter can also distort real XRD peaks.

Therefore, a good XRD preprocessing algorithm should be designed so that **real diffraction features are preserved**.

---

# 13. Spike rejection is therefore a classification problem

You can think of every point as asking:

> "Am I part of the real diffraction signal or am I an anomalous measurement?"

Conceptually:

```text
                Intensity point
                       │
                       ▼
              Examine neighborhood
                       │
             ┌─────────┴─────────┐
             │                   │
       Consistent with      Extremely
       local pattern        anomalous
             │                   │
             ▼                   ▼
        REAL SIGNAL             SPIKE
             │                   │
             │                   ▼
             │              Reject /
             │              replace
             │
             ▼
        Keep unchanged
```

---

# 14. Why spike rejection comes before some other preprocessing

Consider this raw pattern:

```text
Raw

Intensity
   │
   │          /\       │
   │         /  \      │
   │        /    \     │
   │_______/______\____│______
                        ↑
                      spike
```

If you perform smoothing first:

```text
Raw → Smoothing
```

the spike can contaminate the surrounding points.

Instead, you might do:

```text
Raw
 │
 ▼
Spike detection/rejection
 │
 ▼
Clean pattern
 │
 ▼
Smoothing / other processing
 │
 ▼
Analysis
```

This is one reason spike removal can be a useful preprocessing step.

However, **the exact order depends on the FC Cubic/GSAS-II implementation**. You shouldn't assume that every XRD pipeline uses the same sequence.

---

# 15. Now connect it to your FC Cubic system

For your project, imagine the user uploads:

```text
sample.xy
```

with:

```text
2θ       Intensity
10.00    125
10.01    128
10.02    130
10.03    127
10.04    8500   ← suspicious
10.05    129
10.06    131
```

The preprocessing engine might produce:

```text
2θ       Raw       Processed
10.00    125       125
10.01    128       128
10.02    130       130
10.03    127       127
10.04    8500      ~128
10.05    129       129
10.06    131       131
```

The key point is:

$$
\boxed{\text{same }2\theta,\quad\text{corrected intensity}}
$$

Unlike zero-offset correction.

Compare the two:

| Operation | What primarily changes? |
|---|---|
| Zero-offset correction | $(2\theta$) |
| Spike rejection | Intensity |
| Background removal | Intensity |
| Smoothing | Intensity |
| Kα2 stripping | Intensity / pattern shape |

This distinction is **very useful when you're testing the preprocessing UI**.

---

# 16. A very important QA problem: don't remove real peaks

This is probably the biggest risk in spike rejection.

Suppose the sample genuinely has a very sharp peak:

```text
                    /\
                   /  \
__________________/    \________________
```

If the algorithm is too aggressive:

```text
                    │
                    │
____________________│___________________
```

you have accidentally deleted scientifically meaningful information.

So spike rejection should satisfy:

$$
\boxed{\text{Remove artifacts while preserving legitimate diffraction peaks}}
$$

That's the fundamental quality criterion.

---

# 17. Good test cases for FC Cubic

Since you're testing the preprocessing feature, I'd create at least these conceptual cases.

### Case 1 — No spikes

```text
Normal XRD pattern
```

Expected:

> Pattern remains essentially unchanged.

---

### Case 2 — Single obvious spike

```text
100
102
105
5000
103
101
100
```

Expected:

> Spike detected and removed/replaced according to the configured algorithm.

---

### Case 3 — Multiple spikes

```text
100
5000
105
103
7000
104
102
```

Expected:

> Both artifacts handled.

---

### Case 4 — Genuine narrow peak

This is extremely important.

Give it a legitimate diffraction peak that is relatively sharp.

Expected:

> **Peak must not be removed as a spike.**

---

### Case 5 — Broad legitimate peak

Expected:

> Peak shape preserved.

---

### Case 6 — Noise without obvious spikes

Expected:

> Spike rejection should not suddenly destroy the entire noisy pattern.

---

### Case 7 — Spike near a genuine peak

This is probably one of your most valuable tests.

```text
             real peak
                /\
               /  \
              /  │ \
_____________/   │  \________
                  ↑
                spike
```

Expected:

> The artifact is removed while the genuine peak remains.

---

# 18. One more important concept: spike rejection ≠ smoothing

These two can look similar but are conceptually different.

### Spike rejection

Question:

> "Is this point an abnormal measurement?"

Then remove/reconstruct that point.

### Smoothing

Question:

> "Can I reduce high-frequency noise while preserving the underlying signal?"

So:

```text
Spike rejection
       ↓
Remove isolated anomalies


Smoothing
       ↓
Reduce general small fluctuations
```

You shouldn't think of spike rejection as simply "strong smoothing."

---

# 19. The mental model I want you to keep

Think of your XRD pattern as:

$$
\boxed{
\text{Observed pattern}
=
\text{True diffraction signal}
+
\text{noise}
+
\text{artifacts}
}
$$

A spike is an **artifact**:

$$
\boxed{
I_{\text{observed}}
=
I_{\text{true}}
+
I_{\text{spike}}
}
$$

for a very localized measurement point.

Spike rejection attempts to estimate:

$$
\boxed{I_{\text{true}}}
$$

by recognizing that the anomalous point doesn't fit the surrounding diffraction signal.

---

## And compare this with zero-offset correction

This distinction should now be very clear:

```text
             XRD RAW DATA
                  │
       ┌──────────┴──────────┐
       │                     │
       ▼                     ▼
  Zero-offset             Spike
   problem                problem
       │                     │
       ▼                     ▼
 Wrong 2θ coordinate     Wrong intensity
       │                     │
       ▼                     ▼
 Correct 2θ             Correct/reconstruct
       │                  intensity
       └──────────┬──────────┘
                  ▼
            Clean pattern
                  │
                  ▼
              Analysis
```

So if **zero-offset correction** asks:

> "Is this peak located at the correct angle?"

**Spike rejection** asks:

> "Is this intensity value actually part of the diffraction pattern?"
