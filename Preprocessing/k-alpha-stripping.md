# K-alpha Stripping

**Kα stripping** is one of those XRD preprocessing operations that becomes much easier once you understand where the unwanted part of the pattern comes from.

The core idea is:

> **The X-ray source does not produce a perfectly single-wavelength beam. Kα radiation actually contains two closely spaced wavelengths, and Kα stripping attempts to remove the contribution of Kα₂ so that the pattern behaves more like a single Kα₁ wavelength pattern.**


# 1. Start with the X-rays coming from the source

Suppose your XRD instrument uses a copper X-ray source.

You might hear:

$$
\text{Cu K}\alpha
$$

and think:

> "The X-rays have one wavelength."

But that's not quite true.

Copper produces characteristic X-ray radiation with two important components:

$$
\boxed{\mathrm{K}\alpha_1}
$$

and

$$
\boxed{\mathrm{K}\alpha_2}
$$

Their wavelengths are very close, but not identical.

Approximately:

$$
\lambda_{\mathrm{K}\alpha_1}\approx1.5406\ \text{Å}
$$

$$
\lambda_{\mathrm{K}\alpha_2}\approx1.5444\ \text{Å}
$$

So the source is effectively producing:

```text
Cu X-ray source
      │
      ├──── Kα₁ → λ₁ ≈ 1.5406 Å
      │
      └──── Kα₂ → λ₂ ≈ 1.5444 Å
```

The important point:

$$
\boxed{\lambda_1\neq\lambda_2}
$$

---

# 2. Why do we have Kα₁ and Kα₂?

This comes from atomic physics.

A copper atom has electrons occupying different energy levels.

For our purpose, think of electron shells roughly like:

```text
Higher energy
     │
     │   L shell
     │  ─────────
     │
     │   K shell
     │  ─────────
     │
Lower energy
```

When an electron is knocked out of the inner K shell, an electron from a higher shell falls down to fill the vacancy.

That transition releases energy as an X-ray photon.

For example:

$$
L\rightarrow K
$$

produces **Kα radiation**.

But the L shell itself has slightly different energy states.

Therefore, there are two closely related transitions:

$$
L_3\rightarrow K
$$

and

$$
L_2\rightarrow K
$$

which produce:

$$
K\alpha_1
$$

and

$$
K\alpha_2
$$

respectively.

You don't need the atomic physics details to use Kα stripping, but this explains **why there are two wavelengths in the first place**.

---

# 3. Now connect this to Bragg's law

This is where it becomes important for XRD.

Remember:

$$
\boxed{n\lambda=2d\sin\theta}
$$

For a particular crystal plane \(d\), if you change \(\lambda\), the diffraction angle must change.

Rearranging:

$$
\sin\theta=\frac{n\lambda}{2d}
$$

Therefore:

$$
\lambda_1\neq\lambda_2
$$

means:

$$
\theta_1\neq\theta_2
$$

for the same \(d\).

This is the fundamental reason Kα₂ creates an additional feature in the XRD pattern.

---

# 4. Imagine a single crystal plane

Suppose a particular set of planes has:

$$
d=2.00\text{ Å}
$$

Using Cu Kα₁:

$$
\lambda_1=1.5406\text{ Å}
$$

For first-order diffraction:

$$
\lambda=2d\sin\theta
$$

Therefore:

$$
1.5406=2(2.00)\sin\theta_1
$$

So:

$$
\sin\theta_1=0.38515
$$

giving approximately:

$$
\theta_1=22.65^\circ
$$

Therefore:

$$
2\theta_1\approx45.30^\circ
$$

Now use Kα₂:

$$
\lambda_2=1.5444\text{ Å}
$$

Then:

$$
1.5444=4\sin\theta_2
$$

giving approximately:

$$
2\theta_2\approx45.42^\circ
$$

So we get two nearby diffraction positions:

```text
Intensity
   │
   │             Kα₁
   │              /\
   │             /  \
   │            /    \      Kα₂
   │           /      \      /\
   │__________/________\____/__\____
             45.30°    45.42°
```

---

# 5. This is the key insight

The crystal has **one set of planes**.

But because the X-ray source contains two closely spaced wavelengths, that same set of planes can diffract at two slightly different angles.

Therefore, what should conceptually be one diffraction peak can appear as:

$$
\boxed{\text{K}\alpha_1\text{ peak}+\text{K}\alpha_2\text{ contribution}}
$$

This produces **peak asymmetry or even visible peak splitting**, depending on resolution and angle.

---

# 6. Why is Kα₂ weaker?

Kα₁ and Kα₂ don't have equal intensities.

For many conventional sources, the approximate intensity relationship is:

$$
K\alpha_1:K\alpha_2\approx2:1
$$

So conceptually:

```text
Kα₁       Kα₂
  /\        /\
 /  \      /  \
/    \    /    \
```

Kα₁ is stronger.

The measured pattern therefore contains something like:

$$
\boxed{
I_{\text{measured}}
=
I_{\alpha_1}
+
I_{\alpha_2}
}
$$

More precisely, this relationship is a convolution/superposition of the responses produced by the two wavelengths, but the above is the important first-principles mental model.

---

# 7. What does this look like in a real XRD pattern?

Imagine a theoretical single-wavelength peak:

```text
Intensity
   │
   │             /\
   │            /  \
   │           /    \
   │__________/______\________
```

Now include Kα₂:

```text
Intensity
   │
   │             Kα₁
   │              /\
   │             /  \
   │            /    \     Kα₂
   │           /      \    /\
   │__________/________\__/__\____
```

If the two components are close enough and the instrument resolution isn't high enough to separate them, you may see something more like:

```text
Intensity
   │
   │             /\
   │            /  \_
   │           /     \__
   │__________/___________\_______
```

So Kα₂ can create an **asymmetric tail/shoulder** on diffraction peaks.

---

# 8. Why do we want to strip Kα₂?

Because many XRD analyses work more cleanly when the diffraction pattern is represented using **one known wavelength**, typically Kα₁.

Without stripping:

$$
\text{Measured pattern}
=
K\alpha_1+K\alpha_2
$$

After Kα₂ stripping:

$$
\boxed{
\text{Processed pattern}
\approx K\alpha_1
}
$$

The goal is therefore:

> **Remove the estimated Kα₂ contribution while preserving the Kα₁ signal.**

---

# 9. Very important: we're not removing a "second material peak"

Suppose you see:

```text
        /\_
       /  \__
______/______\____
```

You shouldn't immediately think:

> "There must be another phase."

The small shoulder could simply be the Kα₂ component of the same diffraction peak.

This matters enormously for:

- peak detection
- phase identification
- indexing
- peak fitting
- lattice parameter estimation
- Rietveld refinement

---

# 10. How does stripping actually work?

Now we get to the preprocessing algorithm.

Imagine the measured pattern is:

$$
I_{\text{measured}}(2\theta)
$$

Conceptually:

$$
I_{\text{measured}}
=
I_{\alpha_1}
+
I_{\alpha_2}
$$

If we can estimate:

$$
I_{\alpha_2}
$$

then:

$$
\boxed{
I_{\alpha_1}
\approx
I_{\text{measured}}
-
I_{\alpha_2}
}
$$

That's the fundamental mathematical idea behind stripping.

---

# 11. But how do we know where Kα₂ should be?

This is where Bragg's law comes back.

We know:

$$
\lambda_{\alpha_1}
$$

and

$$
\lambda_{\alpha_2}
$$

Therefore, for a given \(d\), we can calculate where the two components occur.

From:

$$
d=\frac{\lambda}{2\sin\theta}
$$

we can relate the Kα₁ and Kα₂ positions.

So the software can estimate:

> "If the Kα₁ peak is here, the corresponding Kα₂ contribution should occur slightly farther toward higher \(2\theta\)."

---

# 12. And we know approximately how strong Kα₂ is

The algorithm can also use the known intensity ratio.

For example, conceptually:

$$
I_{\alpha_2}\approx\frac{1}{2}I_{\alpha_1}
$$

depending on the radiation/source model being used.

Therefore, if the software estimates:

```text
Kα₁ contribution = 1000 counts
```

it can estimate the corresponding Kα₂ contribution based on the appropriate source ratio.

It can then subtract that contribution from the measured pattern.

---

# 13. A simplified numerical example

Suppose around a particular peak we have:

```text
Measured:

2θ       Intensity
40.00       100
40.01       150
40.02       500
40.03       800
40.04       600
40.05       300
```

The algorithm determines that part of the high-angle side is caused by Kα₂.

Suppose its estimated Kα₂ contribution is:

```text
2θ       Kα₂ contribution
40.00       0
40.01       0
40.02       30
40.03       100
40.04       120
40.05       80
```

Then conceptually:

$$
I_{\text{corrected}}
=
I_{\text{measured}}
-
I_{\alpha_2}
$$

giving:

```text
2θ       Measured    Kα₂       Corrected
40.00       100        0          100
40.01       150        0          150
40.02       500       30          470
40.03       800      100          700
40.04       600      120          480
40.05       300       80          220
```

Again, this is a **simplified illustration** rather than a description of a specific software implementation.

---

# 14. Why doesn't Kα stripping simply shift peaks?

This is an important distinction from zero-offset correction.

### Zero-offset correction

Changes:

$$
2\theta
$$

Essentially:

$$
2\theta_{\text{corrected}}
=
2\theta_{\text{measured}}-\delta
$$

### Kα stripping

Primarily changes:

$$
I
$$

because you're removing an estimated wavelength component from the intensity signal.

Conceptually:

```text
Zero offset
────────────
Changes X-axis


Kα stripping
────────────
Changes Y-axis
```

This distinction is useful when you're testing Your Project.

---

# 15. Kα stripping vs Kα2 stripping

You may see terminology such as:

- Kα stripping
- Kα₂ stripping
- K-alpha-2 stripping
- Kα₂ removal

Usually, when people say **Kα stripping** in a preprocessing context, they mean removing the **Kα₂ component** while retaining Kα₁.

So:

$$
\boxed{\text{Kα stripping} \approx \text{Kα₂ removal}}
$$

The terminology can vary between software.

---

# 16. What happens if you don't strip Kα₂?

Suppose you want to fit a peak.

Without stripping:

```text
             Kα₁
              /\
             /  \_
            /     \__
___________/__________\____
                 ↑
               Kα₂
```

A peak-fitting algorithm may need to model the doublet explicitly.

With Kα₂ stripped:

```text
             Kα₁
              /\
             /  \
            /    \
___________/______\________
```

Now the peak is closer to a single-wavelength representation.

This can simplify certain downstream operations.

---

# 17. But there is an important modern-XRD nuance

You should **not automatically assume that Kα₂ stripping is always desirable**.

Modern analysis software can instead model the complete radiation profile mathematically.

For example, a refinement engine can know:

$$
\lambda_{\alpha_1}
$$

$$
\lambda_{\alpha_2}
$$

and their relative intensities and explicitly model both components during refinement.

In such a workflow:

```text
Raw pattern
     │
     ▼
Keep Kα₁ + Kα₂
     │
     ▼
Refinement model knows
about the doublet
     │
     ▼
Fit experimental pattern
```

Whereas a preprocessing workflow might do:

```text
Raw pattern
     │
     ▼
Kα₂ stripping
     │
     ▼
Approximate Kα₁-only pattern
     │
     ▼
Analysis
```

Therefore, **whether Your Project should strip Kα₂ before GSAS-II/FullProf analysis depends on how your pipeline configures the radiation model.**

This is something I would specifically clarify with your developers/scientist rather than assuming.

---

# 18. QA perspective for Your Project

Now let's translate the science into testing.

Suppose the UI has:

> **Kα₂ Stripping: ON/OFF**

You should verify at least:

### Case 1 — Kα₂ stripping OFF

The pattern should remain unchanged by this operation.

```text
Input → No Kα₂ processing → Output
```

### Case 2 — Kα₂ stripping ON

The Kα₂ contribution should be reduced/removed according to the configured algorithm.

```text
Input
  ↓
Kα₂ estimation
  ↓
Kα₂ removal
  ↓
Processed pattern
```

### Case 3 — Verify peak positions

Kα stripping shouldn't behave like zero-offset correction.

You should verify that it isn't unexpectedly shifting the entire \(2\theta\) axis.

### Case 4 — Verify intensities

The intensity profile should change primarily where Kα₂ contributes.

### Case 5 — High-angle peaks

The Kα₁/Kα₂ separation becomes more noticeable at higher angles, so high-\(2\theta\) peaks are particularly useful for validation.

### Case 6 — Multiple peaks

Make sure stripping doesn't introduce artificial negative intensities or distort neighboring peaks.

### Case 7 — Already Kα₁-only data

If the input data has already been stripped or collected with a monochromatic/appropriate source configuration, applying another Kα₂ stripping operation could potentially distort the pattern.

That should be explicitly handled by the product design.

---

# 19. One thing I would add to your Your Project test strategy

For this feature, don't only compare screenshots of the graph.

Compare the **actual numerical data**.

For example:

```text
                BEFORE             AFTER

2θ              40.00              40.00
Intensity       1000                980

2θ              40.02              40.02
Intensity       900                 820

2θ              40.04              700                 610
```

You want to establish:

$$
\boxed{\Delta I = I_{\text{before}}-I_{\text{after}}}
$$

and understand whether the change is consistent with the expected Kα₂ contribution.

This is especially important because visually, a Kα₂-stripped pattern can look "cleaner" while still being numerically wrong.

---

# 20. The complete mental model

Put everything together:

```text
                 X-RAY TUBE
                     │
                     ▼
             Characteristic X-rays
                     │
             ┌───────┴────────┐
             │                │
           Kα₁              Kα₂
        λ ≈ 1.5406 Å      λ ≈ 1.5444 Å
             │                │
             └───────┬────────┘
                     │
                     ▼
                  SAMPLE
                     │
             Bragg diffraction
                     │
                     ▼
                DETECTOR
                     │
                     ▼
             Measured XRD pattern
                     │
                     │
             Kα₂ stripping
                     │
                     ▼
       Estimate Kα₂ contribution
                     │
                     ▼
      subtract Kα₂ contribution
                     │
                     ▼
        Approximate Kα₁ pattern
                     │
                     ▼
            Further analysis
```

And the single equation that captures the idea is:

$$
\boxed{
I_{\text{measured}}
\approx
I_{\mathrm{K}\alpha_1}
+
I_{\mathrm{K}\alpha_2}
}
$$

so Kα₂ stripping tries to obtain:

$$
\boxed{
I_{\mathrm{K}\alpha_1}
\approx
I_{\text{measured}}
-
I_{\mathrm{K}\alpha_2}
}
$$

### The three preprocessing operations you've now learned

| Preprocessing | First-principles problem | Main thing affected |
|---|---|---|
| **Zero-offset correction** | Instrument's angular zero is wrong | \(2\theta\) |
| **Spike rejection** | Isolated detector/measurement artifacts | Intensity |
| **Kα₂ stripping** | Source contains two close wavelengths | Intensity / peak shape |

The deepest way to think about Kα₂ stripping is:

> **You're not correcting a bad crystal or moving a peak. You're separating two X-ray wavelengths that were mixed together in the measured diffraction pattern.**

And that distinction becomes especially important for your Your Project pipeline because **GSAS-II/FullProf may be able to model the Kα doublet directly rather than requiring the pattern to be stripped first**.