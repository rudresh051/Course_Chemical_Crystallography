# Quality Check

This **Quality Check** is slightly different from the preprocessing operations we've discussed so far.

Zero-offset, spike rejection, Kα₂ stripping, smoothing, and background removal **modify the XRD data**.

Quality Check generally does something different:

> **It evaluates whether the XRD pattern is good enough to proceed to analysis, and identifies potential problems before/during preprocessing.**

Your screenshot shows four checks:

1. Signal-to-noise ratio
2. Mean count rate
3. Baseline flatness
4. Step size vs FWHM

Let's build each from first principles.

---

# 1. First: What makes an XRD pattern "good"?

Imagine two XRD measurements of the same material.

### Pattern A

```text
Intensity
   │
   │             /\
   │            /  \
   │           /    \
   │__________/______\____________
   │
   └────────────────────────────── 2θ
```

Nice, clear peaks.

### Pattern B

```text
Intensity
   │
   │       /\/\/\/\/\
   │    /\/          \/\/\
   │___/                \_/\/\__
   │
   └────────────────────────────── 2θ
```

The peaks may still exist, but they're buried in noise.

So before doing:

- peak detection
- phase identification
- indexing
- Le Bail/Pawley
- Rietveld refinement

we should ask:

> **Is this pattern of sufficient quality for the analysis we're about to perform?**

That's the purpose of Quality Check.

---

# 2. Start with the fundamental signal model

The same equation we've used before is useful:

$$
\boxed{
I_{\text{measured}} = I_{\text{signal}}
+
I_{\text{background}}
+
I_{\text{noise}}
}
$$

Quality Check tries to answer questions about all three.

For example:

```text
              XRD DATA
                  │
       ┌──────────┼──────────┐
       ▼          ▼          ▼
    Signal    Background    Noise
       │          │          │
       │          │          │
       └──────────┼──────────┘
                  ▼
             Is it usable?
```

Your screenshot represents four different ways of answering that question.

---

# 3. Check 1 — Signal-to-Noise Ratio

Your screenshot says:

> **Signal-to-noise ratio**  
> **S/N ≈ 38 (good)**

Let's start from the simplest idea.

Suppose your useful diffraction signal is:

$$
S
$$

and random noise is:

$$
N
$$

Then:

$$
\boxed{
SNR=\frac{S}{N}
}
$$

A higher SNR means:

> The actual diffraction signal is much stronger than the random fluctuations.

---

# 4. Simple example

Suppose a peak has a signal amplitude of:

$$
S=1000
$$

and the noise level is approximately:

$$
N=25
$$

Then:

$$
SNR=\frac{1000}{25}=40
$$

So:

$$
\boxed{SNR=40}
$$

That's consistent with the kind of value you're seeing in the screenshot:

$$
S/N\approx38
$$

which the UI considers **good**.

---

# 5. Why does SNR matter?

Imagine these two peaks.

### High SNR

```text
Intensity
   │
   │             /\
   │            /  \
   │           /    \
   │__________/______\_________
   │
```

You can confidently detect the peak.

### Low SNR

```text
Intensity
   │
   │       /\/\/\ /\
   │    /\/     \/  \/\/\
   │___/                \_/\/
   │
```

Now the software has difficulty determining:

- where the peak begins
- where its maximum is
- its exact position
- whether a small bump is a peak
- whether two peaks are actually separate

So:

$$
\boxed{
\text{Higher SNR} \rightarrow \text{more reliable peak information}
}
$$

---

# 6. But "SNR = 38" needs a caveat

There isn't one universal XRD definition of SNR.

For example, one implementation might calculate:

$$
SNR=
\frac{\text{peak height}}
{\text{standard deviation of noise}}
$$

Another might use:

$$
SNR=
\frac{\text{mean signal}}
{\text{noise standard deviation}}
$$

Another might use a robust estimate based on the background.

So for project, the important QA question is:

> **Exactly how is SNR calculated?**

You should eventually get the formula/algorithm from the developer/scientist.

The displayed threshold:

> `38 (good)`

is also **product-specific**, not a universal scientific law.

---

# 7. Check 2 — Mean count rate

Your screenshot says:

> **Mean count rate**  
> **12.4k counts (adequate)**

To understand this, first understand what a detector actually counts.

X-rays arrive at the detector.

The detector converts detected photons into counts.

Conceptually:

```text
X-rays
  ↓
Detector
  ↓
Photon detected
  ↓
1 count
```

If you detect:

$$
12,400
$$

counts during some measurement interval, that's the measured count total.

But strictly speaking, **count rate** means counts per unit time:

$$
\boxed{
R=\frac{\text{number of counts}}{\text{measurement time}}
}
$$

usually:

$$
\text{counts/sec}
$$

or:

$$
\text{cps}
$$

---

# 8. Why do counts matter?

XRD detection is fundamentally statistical.

A simplified model is that photon counting follows approximately **Poisson statistics**.

If you detect:

$$
N
$$

photons, the statistical uncertainty is approximately:

$$
\boxed{
\sigma\approx\sqrt{N}
}
$$

Therefore the relative statistical noise is approximately:

$$
\frac{\sigma}{N}
=
\frac{\sqrt N}{N}
=
\frac{1}{\sqrt N}
$$

This is extremely important.

---

# 9. More counts → less relative statistical noise

Suppose:

$$
N=100
$$

Then:

$$
\sigma=\sqrt{100}=10
$$

Relative uncertainty:

$$
\frac{10}{100}=10\%
$$

Now:

$$
N=10,000
$$

Then:

$$
\sigma=\sqrt{10,000}=100
$$

Relative uncertainty:

$$
\frac{100}{10,000}=1\%
$$

So:

$$
\boxed{
\text{More photons} \rightarrow \text{better counting statistics}
}
$$

This is one fundamental reason count rate matters.

---

# 10. But high count rate isn't automatically good

This is an important QA point.

You might think:

> "Higher count rate = better XRD."

Not necessarily.

If the detector becomes saturated or experiences dead-time/nonlinearity, excessively high rates can cause problems.

So there is generally a useful operating range:

```text
Too low             Good range            Too high
   │                    │                    │
   ▼                    ▼                    ▼
Poor statistics     Reliable counts     Saturation/
                                          nonlinearity
```

Therefore your UI's:

> **12.4k counts (adequate)**

should be understood as:

> "The measured intensity/count level is considered sufficient by the application's configured criterion."

Not:

> "12.4k is a universal scientifically correct threshold."

---

# 11. One terminology issue I'd flag in your UI

The screenshot says:

> **Mean count rate**
>
> `12.4k counts`

Strictly speaking, **count rate** should have a time unit, such as:

$$
12.4\text{ kcps}
$$

if it means 12,400 counts per second.

If the value is simply an average intensity/count value over the XRD data points, then calling it "mean count rate" may be misleading.

This is something I'd ask your developer/scientist:

> Is `12.4k counts` the mean intensity across data points, total counts normalized by acquisition time, or actual counts/sec?

That's a worthwhile QA clarification.

---

# 12. Check 3 — Baseline flatness

Your screenshot says:

> **Baseline flatness**  
> `slight slope high 2θ`

This relates directly to the **background removal** concept we just discussed.

Imagine an ideal baseline:

```text id="r4bcr5"
Intensity
   │
   │
   │________________________
   │
   └──────────────────────── 2θ
```

That's flat.

Now suppose:

```text id="u3f1ny"
Intensity
   │
   │                 /
   │              /
   │           /
   │_________/
   │
   └──────────────────────── 2θ
```

The baseline slopes upward.

That means the measured background is changing with angle.

---

# 13. Why does baseline shape matter?

Suppose you have:

```text id="cz1evg"
               peak
                /\
               /  \
______________/    \________
              ↑
         slowly rising
          background
```

If your analysis assumes a flat baseline:

```text id="n0j7k3"
_____________________________
```

then your peak intensities may be interpreted incorrectly.

You could have:

- incorrect peak intensities
- incorrect background estimation
- difficulty detecting weak peaks
- distorted refinement
- poor signal-to-noise estimation

---

# 14. Baseline flatness doesn't mean "background must literally be flat"

This is important.

Real XRD backgrounds don't necessarily have to be horizontal.

You might have:

```text id="f5m3s5"
background:
     /
    /
___/
```

and this could be perfectly legitimate.

So the Quality Check isn't necessarily saying:

> "Any slope is bad."

Instead, it may be saying:

> "The baseline has a significant slope according to our configured criterion."

Your screenshot:

> `slight slope high 2θ`

followed by:

> `Overall: ready for analysis (1 warning).`

is actually a good example.

The system is saying:

> "There is something worth knowing about the data, but it isn't severe enough to prevent analysis."

---

# 15. This introduces an important concept: warning vs failure

Your UI has:

```text id="7h8b1k"
✓ SNR — good
✓ Mean count rate — adequate
⚠ Baseline flatness — warning
✓ Step size vs FWHM — good

Overall:
ready for analysis (1 warning)
```

This is a **quality gate**, but apparently a soft gate.

Conceptually:

```text
GREEN
   ↓
Requirement satisfied


YELLOW
   ↓
Potential issue
but analysis allowed


RED
   ↓
Serious issue
analysis potentially blocked
```

That's a very useful design.

---

# 16. Check 4 — Step size vs FWHM

This is probably the most scientifically interesting check in your screenshot.

You have:

> **Step size vs FWHM**
>
> $$
> \Delta 2\theta=0.02^\circ
> $$
>
> `(Δ2θ = FWHM/5 ✓)`

Let's understand this from first principles.

---

# 17. What is step size?

When an XRD instrument scans an angular range, it doesn't measure every possible infinitely small angle.

It takes measurements at discrete positions.

For example:

```text id="7aq3v1"
20.00°
20.02°
20.04°
20.06°
20.08°
20.10°
...
```

The difference is:

$$
\boxed{
\Delta 2\theta=0.02^\circ
}
$$

This is the **step size**.

---

# 18. Imagine a continuous peak

The actual physical peak might look like:

```text id="22fkuc"
                  /\
                 /  \
                /    \
_______________/      \_______________
```

But the instrument samples it at discrete positions:

```text id="jow99b"
                  ●
               ●     ●
             ●         ●
___________●_____________●___________
```

If your step size is too large, you don't have enough points to describe the peak.

---

# 19. Example of a very large step size

Imagine a peak whose width is:

$$
FWHM=0.10^\circ
$$

but your scan step is:

$$
\Delta2\theta=0.10^\circ
$$

Then you have approximately **one point across the FWHM**.

That's terrible sampling.

The peak could appear as:

```text id="8k2bka"
        ●
        │
________│________
```

You can't properly determine its shape.

---

# 20. What is FWHM?

FWHM means:

$$
\boxed{\text{Full Width at Half Maximum}}
$$

Suppose a peak has maximum intensity:

$$
I_{\max}=1000
$$

Half maximum is:

$$
500
$$

Find the two angles where:

$$
I=500
$$

Suppose they're:

$$
30.00^\circ
$$

and:

$$
30.10^\circ
$$

Then:

$$
\boxed{
FWHM=30.10-30.00=0.10^\circ
}
$$

Graphically:

```text id="nd9lpm"
Intensity
   │
1000 ────────────●
   │            / \
   │           /   \
 500 ─────────●─────●──────
   │         /       \
   │        /         \
   │_______/___________\________
           ← 0.10° →
              FWHM
```

---

# 21. Why compare step size to FWHM?

Because we want enough measurement points to represent the peak.

Your screenshot uses:

$$
\boxed{
\Delta2\theta \leq \frac{FWHM}{5}
}
$$

Suppose:

$$
FWHM=0.10^\circ
$$

Then:

$$
\frac{FWHM}{5}
=
0.02^\circ
$$

Your step size is:

$$
\Delta2\theta=0.02^\circ
$$

Therefore:

$$
0.02^\circ\leq0.02^\circ
$$

So the check passes.

---

# 22. Why "FWHM/5"?

Because then you have approximately:

$$
\frac{FWHM}{\Delta2\theta}
=
5
$$

measurement intervals across the FWHM.

Conceptually:

```text id="e7x5o1"
               peak
                 /\
                /  \
               /    \
              /      \
_____________/________\____________
             | | | | |
             ↑ ↑ ↑ ↑ ↑
             5-ish sampling
             intervals
```

That gives the software enough samples to characterize the peak.

But again:

$$
\boxed{
FWHM/5\text{ is a useful criterion, not a universal law of XRD.}
}
$$

The appropriate sampling depends on what analysis you're doing and the resolution you need.

---

# 23. What happens if the step size is too large?

Suppose:

$$
FWHM=0.10^\circ
$$

but:

$$
\Delta2\theta=0.05^\circ
$$

Then:

$$
\frac{FWHM}{\Delta2\theta}=2
$$

Only around two intervals span the FWHM.

The peak may look:

```text id="5pjh68"
             ●
          ●     ●
________●_________●________
```

You have poor characterization of the peak.

You may get less reliable:

- peak position
- FWHM
- peak intensity
- peak shape
- peak fitting
- phase identification

---

# 24. Now understand all four checks together

Your screenshot is essentially asking:

### Question 1 — SNR

> **Can I distinguish the diffraction signal from random noise?**

$$
SNR=\frac{Signal}{Noise}
$$

---

### Question 2 — Mean count rate

> **Did the detector collect enough photons/counts to have reasonable counting statistics?**

Approximately:

$$
\sigma\sim\sqrt N
$$

---

### Question 3 — Baseline flatness

> **Is there a problematic trend in the underlying baseline/background?**

---

### Question 4 — Step size vs FWHM

> **Did we sample the diffraction peaks finely enough to characterize them?**

$$
\Delta2\theta\leq\frac{FWHM}{5}
$$

Together:

```text id="cgtp2x"
                 XRD PATTERN
                      │
       ┌──────────────┼───────────────┐
       │              │               │
       ▼              ▼               ▼
   Signal quality  Detector       Background
       │           statistics       │
       │              │              │
       ▼              ▼              ▼
      SNR        Count rate     Baseline flatness
       │              │              │
       └──────────────┼──────────────┘
                      │
                      ▼
                 Sampling quality
                      │
                      ▼
                Step size/FWHM
                      │
                      ▼
              QUALITY DECISION
```

---

# 25. One important correction to your terminology

You said:

> "Quality check ... is part of preprocessing."

I'd make a small distinction in your **project documentation**:

### Preprocessing

Actually **transforms the data**:

- zero-offset correction
- spike rejection
- smoothing
- background removal
- Kα₂ stripping

### Quality Check

**Evaluates the data and/or processed data**:

- SNR
- count statistics
- baseline condition
- angular sampling

So I would architecturally think of it as:

```text
              Raw XRD
                 │
                 ▼
        ┌──────────────────┐
        │  Quality Check   │
        │  Initial QC      │
        └────────┬─────────┘
                 │
                 ▼
            Preprocessing
                 │
                 ▼
        ┌──────────────────┐
        │  Quality Check   │
        │  Post-processing │
        └────────┬─────────┘
                 │
                 ▼
              Analysis
```

Whether project actually performs QC before, after, or both is an implementation decision.

---

# 26. This is especially important for your question from earlier

You had asked:

> **If quality check fails, do we allow users to proceed to analysis?**

Now we can frame that much better.

Not every QC issue means:

$$
\boxed{\text{STOP}}
$$

There should ideally be severity.

For example:

| QC result | Interpretation | Possible action |
|---|---|---|
| SNR good | Strong signal | Continue |
| SNR warning | Weak/noisy signal | Continue with warning |
| SNR fail | Extremely poor signal | Consider blocking |
| Count rate adequate | Good counting statistics | Continue |
| Count rate low | Measurement may be insufficient | Warning/reacquisition |
| Baseline slight slope | Background issue | Warning / preprocess |
| Baseline severe | May affect analysis | Warning or block |
| Step/FWHM good | Adequate sampling | Continue |
| Step/FWHM poor | Undersampled peaks | Potentially block/reacquire |

That gives you a much more scientifically defensible **quality-gate strategy** than simply "any warning = fail."

---

# 27. A very useful QA test: deliberately create bad datasets

Instead of testing only a good XRD file, create/reference datasets representing:

### Dataset A — Excellent

```text
High SNR
Adequate counts
Flat baseline
Fine step size
```

Expected:

> 🟢 Ready for analysis

### Dataset B — Noisy

```text
Low SNR
```

Expected:

> 🟡 Warning or 🔴 fail depending on threshold

### Dataset C — Low counts

Expected:

> Appropriate count-rate warning/failure.

### Dataset D — Sloping baseline

Expected:

> Baseline warning.

### Dataset E — Coarse angular step

Example:

$$
\Delta2\theta > FWHM/5
$$

Expected:

> Step-size warning/failure.

### Dataset F — Multiple problems

For example:

```text
Low SNR
+
sloping baseline
+
large step
```

Expected:

> Overall result should correctly aggregate the individual failures/warnings.

---

# 28. One thing I'd specifically verify in your screenshot

The UI says:

> **Overall: ready for analysis (1 warning).**

This implies some kind of decision matrix:

$$
\text{QC results}
\rightarrow
\text{overall status}
$$

You should ask the developer:

> **What are the exact thresholds for Good / Warning / Fail for each QC metric, and how does the overall status get calculated?**

For example:

```text
SNR >= 30       → Good
20–30           → Warning
<20             → Fail
```

I'm **not saying these are the correct thresholds**—they are just an example.

The actual project thresholds need to be defined by the product/scientific specification.

---

# 29. The deepest mental model

Think of Quality Check as an **XRD health check**.

Before trusting your analysis, you're asking:

```text
                    XRD DATA
                       │
                       ▼
        ┌───────────────────────────┐
        │ "Can I trust this data?"  │
        └─────────────┬─────────────┘
                      │
       ┌──────────────┼──────────────┐
       ▼              ▼              ▼
    Signal         Detector       Baseline
     clear?        collected      reasonable?
                    enough?
       │              │              │
       ▼              ▼              ▼
      SNR         Count rate      Flatness
       │              │              │
       └──────────────┼──────────────┘
                      ▼
                 Sampling okay?
                      │
                      ▼
                 Step vs FWHM
                      │
                      ▼
               ┌──────────────┐
               │ Overall QC   │
               └──────┬───────┘
                      ▼
             Ready for analysis?
```

So the key distinction is:

> **Preprocessing tries to improve/correct the XRD data. Quality Check tries to determine whether the data are sufficiently trustworthy for the intended analysis.**

And your screenshot's four metrics cover four different dimensions:

$$
\boxed{
\text{SNR} \rightarrow \text{signal quality}
}
$$

$$
\boxed{
\text{Count rate} \rightarrow \text{counting statistics}
}
$$

$$
\boxed{
\text{Baseline flatness} \rightarrow \text{background condition}
}
$$

$$
\boxed{
\text{Step/FWHM} \rightarrow \text{angular sampling adequacy}
}
$$

That is a very good foundation for understanding why project has a **Quality Check** stage before allowing the user to move into actual XRD analysis.