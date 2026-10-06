# Zero-Offset

### 1. Start with what an XRD instrument is trying to measure

In a powder XRD experiment, the instrument measures **intensity as a function of angle**:

$$
I(2\theta)
$$

For example, you might get:

| Measured $(2\theta$) | Intensity |
| -------------------: | --------: |
|               20.00° |       120 |
|               20.01° |       135 |
|               20.02° |       180 |
|               20.03° |       250 |
|                  ... |       ... |

A peak might occur at:

$$
2\theta = 20.02^\circ
$$

The important thing is that **the angle determines the physical information about the crystal**.

Using Bragg's law:

$$
n\lambda = 2d\sin\theta
$$

where:

* \(n\) = diffraction order
* $(\lambda$) = X-ray wavelength
* \(d\) = interplanar spacing
* $(\theta$) = Bragg angle
* $(2\theta$) = angle usually shown by the XRD instrument

So if the measured angle is wrong, the calculated \(d\)-spacing will also be wrong.

---

# 2. The ideal instrument

Imagine that the instrument is perfectly calibrated.

Suppose a reference material has a known diffraction peak at:

$$
2\theta_{\text{true}} = 30.000^\circ
$$

You measure it and get:

$$
2\theta_{\text{measured}} = 30.000^\circ
$$

Perfect.

But real instruments aren't perfect.

---

# 3. What is a zero offset?

Suppose the **actual** peak should be at:

$$
30.000^\circ
$$

but your instrument records:

$$
30.100^\circ
$$

There is a systematic angular error:

$$
\boxed{\Delta(2\theta)=+0.100^\circ}
$$

The instrument is effectively saying:

> "Everything is shifted 0.100° toward higher $(2\theta$)."

This systematic angular shift is called **zero offset** (or zero-point error).

So conceptually:

$$
\boxed{2\theta_{\text{measured}} = 2\theta_{\text{true}}+\text{zero offset}}
$$

Therefore:

$$
\boxed{2\theta_{\text{corrected}} = 2\theta_{\text{measured}}-\text{zero offset}}
$$

For our example:

$$
30.100^\circ-0.100^\circ = 30.000^\circ
$$

---

# 4. But why would the instrument have such an error?

This is the important part.

The XRD instrument has a mechanical geometry involving things such as:

* X-ray source
* sample
* incident beam
* detector
* goniometer
* rotation axes

Ideally, when the instrument says:

$$
2\theta=0^\circ
$$

the geometry should correspond exactly to the theoretical zero position.

But tiny mechanical/alignment errors can occur.

For example, imagine the detector is physically shifted by a tiny amount.

The instrument's encoder might believe:

```text
Detector position = 30.000°
```

while the actual physical geometry corresponds to:

```text
Detector position = 30.100°
```

That produces a systematic angular displacement of diffraction peaks.

The important characteristic is:

> **The error is systematic.**

It isn't random noise affecting one peak independently.

---

# 5. Why does this matter so much?

Because \(2\theta\) is used to calculate \(d\).

Remember:

$$
n\lambda=2d\sin\theta
$$

For first-order diffraction, \(n=1\):

$$
d=\frac{\lambda}{2\sin\theta}
$$

And:

$$
\theta=\frac{2\theta}{2}
$$

Let's use Cu Kα radiation:

$$
\lambda \approx 1.5406\text{ Å}
$$

Suppose the true peak is:

$$
2\theta=30^\circ
$$

Therefore:

$$
\theta=15^\circ
$$

and

$$
d=\frac{1.5406}{2\sin15^\circ}
$$

which gives approximately:

$$
d=2.977\text{ Å}
$$

Now suppose zero offset causes the instrument to report:

$$
2\theta=30.1^\circ
$$

Then:

$$
\theta=15.05^\circ
$$

and:

$$
d=\frac{1.5406}{2\sin15.05^\circ}
$$

which is approximately:

$$
d=2.967\text{ Å}
$$

So a **0.1° angular error** has produced an error in the calculated \(d\)-spacing.

That can subsequently affect:

* lattice parameters
* unit-cell dimensions
* phase identification
* peak matching
* refinement
* crystallite-size calculations
* strain calculations

---

# 6. How do we know what the zero offset is?

This is where a **standard/reference material** comes in.

We use a material whose diffraction peak positions are already known very accurately.

For example, suppose we use a reference material with a known peak:

$$
2\theta_{\text{known}}=30.000^\circ
$$

We run it through the instrument.

The instrument gives:

$$
2\theta_{\text{observed}}=30.120^\circ
$$

Then:

$$
\text{zero offset}
=
2\theta_{\text{observed}}
-
2\theta_{\text{known}}
$$

Therefore:

$$
\boxed{\text{zero offset}=+0.120^\circ}
$$

Now we know that the instrument is consistently reporting angles approximately \(0.120^\circ\) too high.

---

# 7. Correction of an actual sample

Now imagine your real sample produces a peak at:

$$
2\theta_{\text{measured}}=40.250^\circ
$$

If the determined zero offset is:

$$
+0.120^\circ
$$

then:

$$
2\theta_{\text{corrected}} = 40.250-0.120
$$

$$
\boxed{2\theta_{\text{corrected}}=40.130^\circ}
$$

So preprocessing effectively changes the XRD pattern from:

```text
Raw pattern

Intensity
   |
   |          /\
   |         /  \
   |________/    \________
             40.25°
                 ↑
            measured
```

to:

```text
Corrected pattern

Intensity
   |
   |        /\
   |       /  \
   |______/    \________
           40.13°
              ↑
           corrected
```

The **intensity values don't need to change simply because of zero-offset correction**.

The primary correction is to the **2θ coordinate**.

---

# 8. Why is it called "zero" offset?

This name can initially be confusing.

It doesn't mean:

> "The offset is zero."

It means an error in the instrument's **zero angular reference**.

Think about a weighing scale.

Suppose an empty scale shows:

$$
0.2\text{ kg}
$$

instead of:

$$
0\text{ kg}
$$

You have a **zero-point error**.

If you put a 5 kg object on it, it might show:

$$
5.2\text{ kg}
$$

You subtract the zero error:

$$
5.2-0.2=5.0
$$

XRD zero-offset correction is conceptually similar.

Instead of:

```text
weight = true weight + scale zero error
```

you have:

$$
\boxed{2\theta_{\text{measured}} = 2\theta_{\text{true}} + \text{angular zero error}}
$$

---

# 9. The subtle part: zero offset vs sample displacement

This is particularly important for your **FC Cubic preprocessing pipeline**.

Not every shift in XRD peak position is necessarily a zero-offset error.

There are several possible causes.

### A. Zero offset

Instrument's angular reference is wrong.

```text
True position       30.00°
Instrument reports  30.10°

Error = +0.10°
```

This is an instrument-related angular calibration issue.

---

### B. Sample displacement

The sample itself may not be positioned exactly at the intended sample height/location.

For example:

```text
Ideal geometry

Source ─────── Sample ─────── Detector
                  ↑
             correct height
```

but:

```text
Actual

Source ──────── Sample
                   ↑
                displaced
```

This changes the diffraction geometry and can shift peaks.

The resulting effect can sometimes resemble a zero shift, but physically it is a different source of error.

---

### C. Wavelength error

This is another completely different issue.

Bragg's law is:

$$
n\lambda=2d\sin\theta
$$

If \(\lambda\) is wrong, your calculated \(d\) can be wrong even if the angular measurement is perfect.

So:

```text
Wrong 2θ → angular calibration problem
Wrong λ  → wavelength/calibration problem
Wrong sample position → geometric displacement problem
```

These can interact during an actual refinement/calibration workflow.

---

# 10. Why correct the pattern before analysis?

Imagine your database contains the theoretical/reference peak:

$$
2\theta=30.00^\circ
$$

Your uploaded experimental pattern contains:

$$
2\theta=30.12^\circ
$$

A computer comparing the two patterns might see:

```text
Reference:

        ▲
        |
--------|----------------
       30.00


Experimental:

           ▲
           |
-----------|--------------
          30.12
```

It could conclude:

> "These peaks don't line up perfectly."

Even though the underlying material may actually be the same.

After zero-offset correction:

```text
Reference:

        ▲
        |
--------|----------------
       30.00


Corrected experimental:

        ▲
        |
--------|----------------
       30.00
```

Now they align.

This is why preprocessing matters for things such as:

* phase identification
* peak matching
* pattern similarity
* recipe recommendation
* Rietveld refinement
* comparison against reference patterns

---

# 11. What does the correction actually look like mathematically?

The simplest model is:

$$
\boxed{x_{\text{corrected}}=x_{\text{measured}}-\delta}
$$

where:

* \(x\) = $(2\theta$)
* $(\delta$) = zero offset

So:

$$
\boxed{
2\theta_{\text{corrected}} = 2\theta_{\text{measured}} -
\delta}
$$

For example:

| Measured $(2\theta$) | Zero offset | Corrected $(2\theta$) |
| -------------------: | ----------: | --------------------: |
|               20.15° |      +0.10° |                20.05° |
|               30.10° |      +0.10° |                30.00° |
|               40.35° |      +0.10° |                40.25° |
|               50.10° |      +0.10° |                50.00° |

Notice something important:

**Every peak is shifted by the same angular amount.**

That's the simple zero-offset model.

---

# 12. Now connect this to your FC Cubic preprocessing

Think of your XRD pipeline roughly like:

```text
Uploaded XRD pattern
        │
        ▼
   File parsing
        │
        ▼
Metadata extraction
        │
        ▼
   Preprocessing
        │
        ├── Background correction
        │
        ├── Kα handling
        │
        ├── Zero-offset correction
        │
        ├── Smoothing / filtering
        │
        └── Other corrections
        │
        ▼
Processed pattern
        │
        ▼
Peak detection / matching
        │
        ▼
Phase identification / refinement
```

Zero-offset correction is therefore **not about changing the material** or "fixing" the peaks themselves.

It is correcting the **coordinate system in which the peaks were measured**.

That's the key mental model.

---

## 13. One very important distinction for testing

As a QA engineer, I'd think about zero-offset correction in terms of **input → transformation → expected output**.

Suppose:

```text
Input:
2θ = [20.10, 30.10, 40.10]

Offset:
+0.10°

Expected:
2θ = [20.00, 30.00, 40.00]
```

You should test:

### Positive offset

$$
\delta=+0.10^\circ
$$

Expected:

$$
2\theta_c=2\theta_m-0.10
$$

### Negative offset

$$
\delta=-0.10^\circ
$$

Then:

$$
2\theta_c=2\theta_m+0.10
$$

### Zero offset

$$
\delta=0
$$

Expected:

$$
2\theta_c=2\theta_m
$$

### Boundary cases

For example:

* very small offset
* large offset
* offset with many decimal places
* negative offset
* missing offset
* invalid offset
* offset outside allowed range

And critically:

> **The intensity values should remain associated with the correct corrected angle.**

If you have:

```text
Before

2θ       Intensity
20.10       500
20.20       800
20.30       300
```

after a +0.10° correction:

```text
2θ       Intensity
20.00       500
20.10       800
20.20       300
```

The intensity isn't independently modified by the zero-offset operation.

---

# 14. The deepest mental model

You can remember zero-offset correction with this picture:

```text
             REAL WORLD
                 │
                 │
          Crystal diffracts
                 │
                 ▼
          TRUE 2θ POSITION
                 │
                 │
        XRD instrument
        introduces offset
                 │
                 ▼
       MEASURED 2θ POSITION
                 │
                 │
       Zero-offset correction
                 │
                 ▼
       CORRECTED 2θ POSITION
                 │
                 ▼
       Bragg's Law / analysis
```

So the fundamental problem is:

$$
\boxed{\text{Measured angle} \neq \text{true angle}}
$$

because of a systematic angular offset.

And preprocessing attempts to recover:

$$
\boxed{\text{true angle} \approx \text{measured angle} - \text{estimated offset}}
$$

Once you understand **that**, the rest of zero-offset correction—standards, calibration, \(d\)-spacing, peak matching, and QA test cases—becomes much easier.
