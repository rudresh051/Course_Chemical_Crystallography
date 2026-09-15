# A Brief Introduction to Rietveld Analysis of XRD Patterns

Video Link - https://www.youtube.com/watch?v=Y808itzb5hw&list=PLZO_dTWSnBCqITbd88ymG7pCol0GGdIvI&index=14

* **References**  
  * R.A Young, The Rietveld Method
  * Georg Will, Powder Diffraction. The Rietveld Method and the Two state method to determine and refine crystal structures from powder Diffraction Data


```txt
Many students do powder diffraction and this can be used to analyze the
powder diffraction data specially in terms of profile fitting, strain
calculations, structure distortions, structure refinement. 
So it is is a fantastic technique. 
But what I am going to do is it is basically a profile fitting technique, its data fitting technique. 
You have to have x-ray data. 

So first of all you have to have a data.
And you need to know something about the sample. 
You need to have some good starting guessing guesses.

```

Rietveld requires initially to have a good guess. If you have the polynomial you cannot start with a straight line.  

Dr. Hugo M. Rietveld -  Dedicated his life in Refinement and didn’t make money out it. Developed program and gave the things for free. He developed Mathematical code and gave it for free.  
So in 1983 it was called Rietveld refinement method instead of powder diffraction method

```txt
If the data looks like second order polynomial and you start fitting a
straight line, obviously you will get a bad fit. So Rietveld requires
you to have some smart guesses, which means you have x-ray data
you need to. When you fitted with a given structure, you need to
have a good guess.
Which means if the structure is FCC.
With different space groups you need to be close to FCC and then
vary the space groups. 
You cannot start with the BCC lattice and
choose different space groups, so guessing.
```

![alt text](image-2.png)

## Difficulties with Powder Diffraction

* Systematic overlapping of diffraction peaks due to symmetry conditions, for example in cubic space groups
* Accidental overlapping because of limited
experimental resolution
* Considerable background difficult to define with accuracy
* Non-random distribution of the crystallites in the specimen, generally known as preferred orientation.

> To overcome some of above difficulties Rietveld developed some methods


## What is Rietveld analysis?

* The Rietveld method refines user-selected parameters to minimize the difference between an experimental pattern (observed data) and a model based on the hypothesized crystal structure and instrumental parameters (calculated pattern)

This is where intelligence guess in the beginning is very important  
A good fit doesn’t mean the answer is right.

* **Full profile fitting**
* **Using crystallographic constraints**
  * Lattice parameters and space group to constrain peak positions
  * Crystal structure to constrain peak intensities

## Least Squares Method

* Curve Fitting

How many of you are aware?  
Let me briefly introduce it to you  

![alt text](image-3.png)

![alt text](image-4.png)

![alt text](image-5.png)

```txt
Let's say you have these data points OK 45 data points, maybe 10 data points. You
draw two lines.
This is blue, blue line and red line. How do you know which one is a good fit? 
Visually it looks as if the blue line is good, but why not red line? 
Do you? Can we develop a quantified method to tell that which fit is good and which fit is not good? 
And this is the purpose of least square fit? What it does is basically is...

It measures the difference between the observed value and the calculated value to a function and square it and takes the sum of all the data points in that
fashion and then sees that for which this error is minimum. 
This is basically the least square fitting. 
```

![alt text](image-6.png)

* The "best" line has minimum error between line and data points
* This is called the **least squares approach**, since we minimize the square of the error

> In above our function is F(x) = ax + b

![alt text](image-7.png)

## Example - Fitting a 2nd order polynomial

![alt text](image-8.png)

## What Rietveld can do?

![alt text](image-9.png)

> It's not a technique for structure determination. It is technique for structure refinement.
> Basically it is a modification of the starting guess
> If you start from wild guess you will not have refinement right.

## Requirements of Rietveld Method

* High quality experimental diffraction pattern
* A structure model that makes physical and chemical
sense
* Suitable peak and background functions

## Principle of Rietveld Refinement

![alt text](image-10.png)

## Goal

![alt text](image-11.png)


![alt text](image-12.png)

> And you need to tweak independently to look at the refinement


## Structure Factor

![alt text](image-13.png)

> Also you need to about these.


![alt text](image-14.png)

## Where to Get Crystal Structures

![alt text](image-15.png)

## Lorentz Polarization factor

![alt text](image-16.png)

## Modeling Preferred Orientation

![alt text](image-17.png)


## The March distribution function

![alt text](image-18.png)

## Additional Intensity factors

![alt text](image-19.png)

## Profile Shape function

![alt text](image-20.png)

> Little tail and narrow peak
> Extended tail


![alt text](image-21.png)

![alt text](image-22.png)

## Peak width

![alt text](image-23.png)

## Background

![alt text](image-24.png)

```txt
I mean this is all done by a program. It is very difficult. It must have been difficult to Rietveld to do it and that is why he wrote code to do it. So that you do not have to worry about the Mathematics part of it. You can just enter the parameters and get a good fit. And this is what most people do. They do not go around solving these equations. The equations are being just shown just to understand what exactly goes on in a program.
```

## Quality of Refinement

![alt text](image-25.png)

![alt text](image-26.png)

The goal is to minimize Rwp and hence chi square

## Why the Rietveld refinement is widely used?

![alt text](image-27.png)

> You will learn better after the demo


