### Williamson–Hall plot in XRD: From first principles

Let's build this from the ground up, starting with what an X-ray diffraction (XRD) pattern actually measures, why peaks become broad, and how a Williamson–Hall plot helps us separate two causes of that broadening.

Since you're learning XRD analysis for the project pipeline, I'll connect the physics to the actual diffraction pattern, the calculations a software tool performs, and how you can test the feature as a QA engineer.

### 1\. First, what happens when X-rays hit a crystal?

![PPT - Diffraction: Electron and X-ray PowerPoint Presentation, free download - ID:6255084](https://images.openai.com/static-rsc-4/-pLAYFdnmjEFcP3GcmcR5rnvdGx0zyJK1E_-RnI4PWmr7NHddWiLwljiascxmD07FjRXQ5RQbl2R5SPEhOHNkMC_dOQ17bi3gay32LDKAb5UkSnQ8AzN6qWf_1xs6qwu5NNrxGTco76S_pyXhUhkAib6Advg584LS5odSSZogYm1PQ3UaQFMs-C6ySu3yq-f?purpose=fullsize)

![Checking your browser - reCAPTCHA](https://images.openai.com/static-rsc-4/47R3NpWgcQ8_Hd4w7_2dGDkUef4hbJsnDjA8_8jjNxwPoafHOKYsogYocPcAzuutacyk7dgGIvYPVb7kqObSBtR8ePW1ZdW64D7BUgNyNYNRMhn_nQt41RbxfwdM-o7HPJwbmB7GaraVQod8_Z-4X9L9DR19A_puHmdPqziJ_QGVwKn1OmLK1-YBTER1L-tN?purpose=fullsize)

![X-ray diffraction (XRD)](https://images.openai.com/static-rsc-4/MULpyI-3t0Z4H2_NKd8QGJPReHZbgwuO2kFQf6kJ3VtnDP8u0ccEDi86NLTgRnv8cwjAefaxcIU3Dp_U2_jgPGOw4uirWF03V9I09JhRfajC5qVKwifBSF35g5w8QPG6G7Ihm764euKIN2E8HENkjKWpamt-BUz4NBGAwxLVP1Cqj8q_1Oj0KXyDiNA8AACZ?purpose=fullsize)

A crystal is made of atoms arranged in a repeating three-dimensional structure. You can imagine it as a stack of regularly spaced atomic planes.

When X-rays hit the crystal, the atoms scatter the X-rays. At certain angles, the scattered waves reinforce one another, producing strong diffraction peaks.

The condition for constructive interference is described by Bragg's law:

n\\lambda=2d\\sin\\theta

Where:

* = diffraction order, usually taken as 1 for ordinary powder diffraction analysis.

* = X-ray wavelength.

* = spacing between the relevant crystal planes.

* = Bragg angle, half of the measured angle.

A powder XRD pattern plots intensity against . Each peak corresponds to diffraction from a family of crystal planes, identified by Miller indices such as , , or , depending on the crystal structure.

But here's the important question:

Why are diffraction peaks broad instead of infinitely narrow?

That question leads us to the Williamson–Hall method.

### 2\. Why do XRD peaks broaden?

Imagine a perfect crystal with an ideal, infinitely repeating lattice. In an idealized diffraction model, its Bragg reflection would be extremely narrow.

Real materials are not perfect. Two important features cause additional peak broadening:

![PPT - X-ray Scattering from Thin Films PowerPoint Presentation, free download - ID:6604967](https://images.openai.com/static-rsc-4/0bRkavRZ5Dy-cRrf1zeSJGdYLznXUsrTyB3xcGsILtCs1avsf78uskh4XMYoKCWcnM_590KNkKTogT3Sdzp7NzLBs9WLIQuKr1vbP_Gs-XhUTeI0ZXW7CaemAm_4mlxy07uaM0m2J2o4Df3A9os6vj0VqlGr_K9KHGt9S5iZpBqXc8tVX8L97VCw-__1o7FF?purpose=fullsize)

Cause 1: Small crystallite size

A crystal that is only a few nanometres across has fewer repeating atomic planes contributing to diffraction. The limited size causes the diffraction peak to spread over a wider angular range.

Smaller crystallites → broader peaks.

![Thin Films | Thin Films and Interfaces | PSI](https://images.openai.com/static-rsc-4/Nwd-76YNe6D9TbyFtZ745jLSuTK5kYnvaVaOLzx5pn1bGEcU9rxDHXOTBmFYlX1TRpudEFlxJBFbmfwHVdTlVXsdkKlo5gZfqpZ9RBw-aE-ozrZsQIyWnxVUqCMQ4Ka87wEGUEf3kH90mP9lk4hcLbtXfvSjYL_-VRk9lmKdu2qlRiRt0jGwe-bAhoOjaJdg?purpose=fullsize)

Cause 2: Microstrain

Atomic plane spacings are not always identical throughout a material. Defects, dislocations, compositional variation and local stresses can produce small variations in lattice spacing.

These variations cause diffraction to spread across nearby angles.

Greater microstrain → broader peaks.

There are other causes too, including the instrument's own resolution, overlapping peaks, and faults in the crystal structure. The basic Williamson–Hall method focuses on separating the contributions of crystallite size and microstrain.

One distinction matters: a crystallite is a coherently diffracting domain, not necessarily the same thing as a whole particle. A particle may contain several crystallites.

### 3\. Understand the Scherrer equation first

Before Williamson–Hall, scientists used the Scherrer equation to estimate crystallite size from peak broadening.

D=\\frac{K\\lambda}{\\beta\\cos\\theta}

Where:

* = average coherent crystallite size.

* = shape factor, often approximately .

* = X-ray wavelength.

* = sample-related peak width, in radians of .

* = Bragg angle.

Let's understand the physical meaning rather than memorizing the equation.

A smaller crystallite contains fewer repeating lattice planes. The diffraction peak spreads out more, so increases.

Since is inversely proportional to :

\\boxed{\\text{Smaller peak width contribution}
\\Rightarrow \\text{larger crystallite size}}

The Scherrer equation is useful, but it has a limitation: it attributes the relevant peak broadening to finite crystallite size. If microstrain also broadens the peak, the size estimate can be misleading.

This is the problem Williamson–Hall addresses.

### 4\. Derive the Williamson–Hall equation

The key idea is that size and strain influence diffraction peaks differently as the diffraction angle changes.

### Step A: Express the size contribution

The Scherrer equation can be rearranged to express peak broadening due to size:

\\beta_{\\text{size}}=\\frac{K\\lambda}{D\\cos\\theta}

For a given crystallite size, the broadening depends on .

### Step B: Express the strain contribution

Microstrain means that the lattice spacing varies slightly across different regions of the sample.

Start with Bragg's law:

\\lambda=2d\\sin\\theta

For a small change in spacing, differentiate:

0=2\\sin\\theta\\,\\Delta d
+2d\\cos\\theta\\,\\Delta\\theta

Rearranging gives the magnitude relationship:

\\left|\\Delta\\theta\\right|
\\approx \\frac{|\\Delta d|}{d}\\tan\\theta

Define microstrain as a relative variation in lattice spacing:

\\varepsilon\\sim\\frac{\\Delta d}{d}

Under the conventional Williamson–Hall broadening model, the strain-related contribution to peak width is represented as:

\\beta_{\\text{strain}}=4\\varepsilon\\tan\\theta

The factor of 4 follows the conventional definition of the strain-broadening width used in this model; the exact broadening coefficient depends on the line-profile and strain-distribution assumptions.

Notice the difference:

* Size broadening varies as .

* Strain broadening varies as .

So their relative contributions change with diffraction angle.

### Step C: Combine both contributions

The conventional Williamson–Hall model assumes that the contributions can be approximated as additive:

\\beta=\\beta_{\\text{size}}+\\beta_{\\text{strain}}

Substitute the two equations:

\\beta=\\frac{K\\lambda}{D\\cos\\theta}
+4\\varepsilon\\tan\\theta

Multiply both sides by :

\\boxed{
\\beta\\cos\\theta=
\\frac{K\\lambda}{D}
+4\\varepsilon\\sin\\theta
}

This is the central Williamson–Hall equation.

It has the form of a straight line:

y=c+mx

if we define:

y=\\beta\\cos\\thetax=4\\sin\\theta

Therefore:

\\boxed{y=\\frac{K\\lambda}{D}+\\varepsilon x}

We have transformed a peak-broadening problem into a straight-line problem.

That is the central insight: use multiple diffraction peaks, observe how their widths vary with angle, and use the fitted line to estimate crystallite size and microstrain.

### 5\. What does the Williamson–Hall plot look like?

![Superior Conductivity of Transparent ZnO/MoS2 Composite Films for Optoelectronic and Solar Cell Applications | MDPI](https://images.openai.com/static-rsc-4/meriavrbnSoTiU_wc6SC-MT3FfmDmTj0Iy_RqkdqdyPf3a85ijRTlZOuL6p0j0xbnSqshCsRECzdmlwAdO1ndC5Jr1BWeA0LTeVJwFdL3O3YnJacgTGhgHfBrOBr3Hy9vgXjWkVXaaCP7mLdEJ0piiXiyk5mKhmvxN2Iz4resLvFnBRbqIn6HVTv-B-QCJ8f?purpose=fullsize)

A conventional Williamson–Hall plot uses:

* X-axis:

* Y-axis:

Each point represents one diffraction peak, not one individual X-ray measurement.

The fitted line provides two parameters.

Slope = microstrain,

A steeper positive slope indicates greater estimated microstrain under the model's assumptions.

Y-intercept =

Calculate crystallite size using:

\\boxed{D=\\frac{K\\lambda}{c}}

where is the fitted y-intercept.

One convention is important: if you plot against instead of , the slope is , so you must divide the slope by 4 to obtain the strain. Both conventions are valid when used consistently.

### 6\. Work through a numerical example

Suppose you have collected an XRD pattern using Cu Kα radiation, with wavelength approximately .

You identify four diffraction peaks, fit their profiles, and obtain the following hypothetical measurements. The widths have already been corrected for instrumental broadening.

| Peak |     | FWHM (radians) |
| ---- | --- | -------------- |
| 1    | 15° | 0.0030         |
| 2    | 20° | 0.0038         |
| 3    | 25° | 0.0046         |
| 4    | 30° | 0.0055         |

These values are illustrative, not measurements from a real sample.

### Step 1: Calculate the X and Y values

For every peak, calculate:

x=4\\sin\\theta,\\qquad y=\\beta\\cos\\theta

For peak 1:

x=4\\sin(15^\\circ)\\approx0.1035y=0.0030\\cos(15^\\circ)\\approx0.002898

Repeat this for the remaining peaks.

### Step 2: Fit a straight line

Fit the equation:

y=mx+c

Suppose the resulting fit gives:

* Slope

* Intercept

These are hypothetical fit parameters to demonstrate the calculation.

### Step 3: Calculate microstrain

Because the X-axis is :

\\varepsilon=m=0.004

Microstrain is dimensionless. This result corresponds to a strain of when expressed as a percentage.

### Step 4: Calculate crystallite size

Using , , and :

D=\\frac{0.9(0.15406)}{0.001}\\boxed{D\\approx138.7\\ \\text{nm}}

This is an illustrative calculation, not a physically validated result from the table above. In actual analysis, the intercept and slope must come from the same measured data, and the line fit must be assessed for reliability.

### 7\. How does the analysis work in actual XRD software?

1\. Load the XRD pattern

XY, DAT, CSV, TXT or another supported pattern format

2\. Detect and fit diffraction peaks

Determine peak positions and FWHM values

3\. Correct instrumental broadening

Use an appropriate instrument-profile correction method

4\. Calculate transformed values

Compute and for each peak

5\. Fit the Williamson–Hall line

Estimate the slope, intercept, fit quality and uncertainties

6\. Report the results

Crystallite size, microstrain, plot and relevant assumptions

This is a useful reference workflow for a Williamson–Hall feature in FC Cubic. It describes the analytical process, not a claim that the application currently implements every step.

### 8\. Important details that can change the result

### Instrumental broadening

The measured peak width includes both sample-related and instrument-related effects. The instrument contribution should be accounted for before extracting sample size and strain.

For example, if both instrument and sample profiles are Gaussian, a common approximation is:

\\beta_{\\text{sample}}^2
=\\beta_{\\text{observed}}^2-\\beta_{\\text{instrument}}^2

For ideal Lorentzian profiles, widths are instead subtracted directly. Real profiles may be Voigt-like or pseudo-Voigt, requiring a more appropriate correction.

QA implication: Check which profile correction model the software uses. Do not assume that subtracting FWHM values directly is always correct.

### Angular units

The peak width must be in radians when used in the conventional equations. If peak fitting returns FWHM in degrees, convert it:

\\beta_{\\text{rad}}=\\beta_{\\text{deg}}\\frac{\\pi}{180}

The angle is half the reported peak position . Accidentally using instead of changes the result.

### Assumptions and limitations

Classical Williamson–Hall analysis assumes that size and strain broadening can be separated using its simplified line-broadening model. It works best when the material's broadening is approximately isotropic and the selected peaks are reliably fitted.

If different crystallographic directions have different sizes or strains, the points may deviate systematically from a straight line. Peak overlap, incorrect phase identification, anisotropy and too few reliable peaks can also produce misleading estimates. A high alone does not prove that the physical assumptions are correct.

### 9\. Test cases you should consider as a QA engineer

For a Williamson–Hall feature, I would start with these cases:

| Test scenario                                  | Expected result                                        |
| ---------------------------------------------- | ------------------------------------------------------ |
| Valid pattern with several well-resolved peaks | Plot and parameter estimates are generated             |
| FWHM reported in degrees                       | Correct conversion to radians occurs                   |
| Peak positions stored as                       | Software correctly uses                                |
| Instrument correction data provided            | Appropriate correction is applied                      |
| Zero or negative fitted peak width             | Invalid measurement is rejected or flagged             |
| Too few valid peaks                            | Analysis is blocked or marked insufficient             |
| Peaks overlap heavily                          | Unreliable fits are flagged where detectable           |
| Line fit has a near-zero or negative intercept | Size estimate is flagged as invalid or unreliable      |
| Strongly nonlinear peak distribution           | Poor fit or model limitations are reported             |
| Reanalysis with identical input                | Results remain reproducible within numerical tolerance |

Also verify that the report clearly distinguishes crystallite size from particle size, and reports strain as a dimensionless quantity rather than confusing it with a percentage.

### 10\. Check your understanding

Suppose two samples have comparable crystal structures and measurement conditions. Sample A has a larger Williamson–Hall y-intercept than Sample B. What does the model predict?

Sample A has larger crystallites.

Sample A has smaller crystallites.

Sample A necessarily has greater microstrain.

The takeaway: The Scherrer equation estimates crystallite size from peak broadening. Williamson–Hall goes one step further by analyzing how multiple peaks broaden at different angles, allowing a model-based separation of crystallite-size broadening and microstrain broadening.

For deeper study, the [NPTEL lecture on Williamson–Hall plots](https://archive.nptel.ac.in/content/storage2/courses/115103030/module2/lec5/2.html?utm_source=chatgpt.com) is a useful next reference.