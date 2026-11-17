---
title: CLT - in english
---

# A proof of the Central Limit Theorem

In general: CLT explains why many distributions of data, approximates a normal
distribution (bell curve), as samplesize increases, independent of the shape
of the underlying distrubution.

The theorem states, at the distribution of the standardized average of a 
sample approaches the standard normal distribution.

Or, less precise, that for large samples their average is more or less normally
distributed around the true average of the population.


Overall: CLT explains why many distributions of data, tend towards a normal distribution (bell curve) when the sample size becomes large, regardless of the original distribution shape.

The theorem tell us, that the distribution of the standardized mean of a sample, approximates the standard normal distribution.

Or, less precisely, for large samples, their mean is more or less normally distributed around the true mean of the population.

## Moment generating functions

Let us begin by introducing the phenomenon "moment generating function".

We have a distribution, it could be the normal distribution.

It is defined by:

$$X(x) = \frac{1}{\sqrt{2\pi \sigma^2}} e^{-\frac{(x-\mu)^2}{2\sigma^2}}$$

Where $\mu$ is the mean, and $\sigma$ is the standard deviation (and $var^2$ is the variance).

We now define a moment generating function:


$$M(t) = E[e^{tX}]$$

X is the function defining the distribution. This is completely general. X could be the function for the normal distribution, it could be any other.

The exponential function in the definition, can be calculated on a calculator, or with the `exp` function in R.

Calculators, or R, do not simply calculate the exponential, it uses another expression to do the calculation. So behind the scene, it 
calculate a so-called Taylor series:
$$e^{tX} = 1 + tX + \frac{(tX)^2}{2!} + \frac{(tX)^3}{3!} + \cdots$$

The next term will be $\frac{(tX)^4}{4!}$. This series allow us to calculate the left hand side of the 
equation with the precision we want, we simply add more terms.

Under appropriate conditions, we are allowed to replace X with $E(X)$, where
$E(X) = \mu$, that is the mean, also called the expected value (this is where "E" come from).

If we do that, we get:


$$M(t) = 1 + tE[X] + \frac{t^2 E[X^2]}{2!} + \frac{t^3 E[X^3]}{3!} + \cdots$$


If we differentiate $M(t)$ with regard to t, we get:
$$M'(t) = E[X] + 2\frac{t E[X^2]}{2!} + 3\frac{t^2 E[X^3]}{3!} + \cdots$$

Inserting t = 0, give:

$$M'(0) = E[X]$$
That is the expectation value, or mean.

If we differentiate twice with regards to T, and insert t = 0, we get:


$$M''(0) = E[X^2]$$



This is the so-called "raw second moment" of our distribution function, and from it we can get the variance. It is a "raw" moment because we don't get the variance directly.

Variance is defined by:
$$Var(X) = E[(X - \mu)^2]$$

Or, the square of the difference between the values, and the mean of the values.



We can expand that to:

$$\text{Var}(X) = E[X^2] - (E[X])^2$$

In other words, the raw second moment, minus the raw first moment squared, give us the variance.

We can continue. The third momen, which we get by differentiating with reard to t three times,
and inserting t = 0, give us the "skewness". The fourth is "kurtosis", an expression of how fat the tails of a distribution is.

A central point is, that if all moments of two distribution functions are equal, the 
functions are equal.

When we in the following work with a moment generating function, the interesting questions
is if it ends up being the same as teh momentgenerting function for the normal distribution.

We will not derive it here, but it can be found with:

$$
N(xM \mu, \sigma^2) = \frac{1}{\sqrt{(2\pi\sigma^2)}}e^{-\frac{1}{2}(x-\mu)^2/\sigma^2} \\
M_z(t) = E(e^{zt}) = \int e^{zt} \frac{1}{\sqrt{2\pi}}e^{-\frac{1}{2}z^2/\sigma^2}\,dz \\
= e^{\frac{1}{2}t^2}
$$



## Onwards!

Havin established this, we can move onward.

The central limit theorem tell that when we have a population with defined mean and standard deviation. And 
take sufficiently many samples from that (and that the samples are also independent and with identical distribution),
the mean of these samples will be normally distributed. 
No matter the distribution of the population.

If we do not break these rulse, we can therefore regard a mean of a sample as coming from a 
normally distributed population. And because of that, we can use the well defined properties
of the normal distribution to do statistical tests, calculate confidence intervals etc.


## What are we trying to prove?

Let us take a number of random varaibles, $X_1 ... X_n$, that are independent and with identical distributions, with a
known mean $\mu$ and a constant, non-infinite variance $\sigma^2$.

We then define a random variable Z:

$$ Z = \frac{\overline{X} - \mu}{\sigma/\sqrt{n}}$$

With these definitions:

$$\mu = E(X_i)$$

The "true" mean of the population.

And:

$$\sigma^2 = Var(X_i) $$
The "true" variance of the population.

We do not know the true mean or variance, but we can calculate the mean of our sample:

$$\overline{X} = \frac{1}{n}\sum_{i=1}^nX_i$$


Postulatet fra sætningen er, at denne tilfældige variable Z, tilnærmer sig
standard normalfordelingen (med middelværdi 0, og standardafvigelse 1).

Det er det vi ønsker at bevise.

Bemærk at dette X ikke er det samme som ovenfor hvor vi arbejdede med moment
genererende funktioner.

Kan vi forstå dette som at X er vores stikprøver? Hm...

## The proof itself

Vi starter med at definere en ny tilfældig variabel Y. 

$$Y_i = \frac{X_i - \mu}{\sigma}$$

The expectation value and variance for each $Y_i$ is given by:

$$E(Y_i) = E\left(\frac{X_i - \mu}{\sigma}\right) = \frac{1}{\sigma}E(X_i - \mu) = \frac{1}{\sigma}(E(X_1)-\mu) = \frac{1}{n}(\mu - \mu) = 0$$

$$Var(Y_i) = Var\left(\frac{X_i - \mu}{\sigma}\right) = \frac{1}{\sigma^2}Var(X_i - \mu) = \frac{1}{\sigma^2}Var(X_i) = \frac{\sigma^2}{\sigma^2} = 1$$

Nu definerer vi endnu en tilfældig variabel, S, der er summen af alle $Y_i$:
$S = Y_1 + ... Y_n$. 

Så kan vi beregne forventingsværdien (gennemsnittet) og variansen af S:

$$E(S) = E\left(\sum_{i = 1}^{n}Y_i\right) = \sum_{i = 1}^{n}E(Y_i) = \sum_{i = 1}^{n}0 = 0$$

$$Var(S) = Var\left(\sum_{i = 1}^{n}Y_i\right) = \sum_{i = 1}^{n}Var(Y_i) = \sum_{i = 1}^{n}1 = n$$

And now the moment of thuth, pun intended. We define yet another random variable Z:

$$Z = \frac{S \sqrt{n}}{n} = \frac{S}{\sqrt{n}}$$

Og nu handler det så om at vise, at dette Z, som kommer fra vores stikprøver, 
er det samme Z som vi definerede ovenfor.

$$Z = \frac{S\sqrt{n}}{n} \\
= \frac{\sqrt{n}}{n}\sum_{i=1}^nY_i \\
= \frac{\sqrt{n}}{n}\sum_{i=1}^n \frac{X_i - \mu}{\sigma} \\
= \frac{\sqrt{n}}{n\sigma}\left[\left(\sum_{i=1}^n X_i \right) - n\sigma\right] \\
= \frac{\sqrt{n}}{\sigma}(\overline{X} - \mu) \\
= \frac{\overline{X}-\mu}{\sigma/\sqrt{n}}$$

Det betyder, at vi kender forventningsværdien og variansen af Z:

$$E(Z) = E\left(\frac{S\sqrt{n}}{n}\right) = \frac{\sqrt{n}}{n}E(S) = 0$$
og
$$Var(Z) = Var\left(\frac{S\sqrt{n}}{n}\right) = \frac{n}{n^2}Var(S) = \frac{n^2}{n^2} = 1$$

Nu vil vi gerne finde den momentgenerende funktion for $Y_i$. 

Der var et par ting vi fandt ud af ovenfor:

$$E(Y_i) = 0$$

$$E(Y_i^2) = Var(Y_i) - E(Y_i)^2 = 1 -0^2 = 1$$

$$M_{Y{_i}}(t) = 1 + \frac{t}{1!}E(Y_{i}) + \frac{t^2}{2!}E(Y_{i}^2) + \frac{t^3}{3!}E(Y_{i}^3) + ... \frac{t^n}{n!}E(Y_{i}^n) \\
= 1 + \frac{t^2}{2!} + \frac{t^3}{3!}E(Y_{i}^3) + ... \frac{t^n}{n!}E(Y_{i}^n)$$

Nu vil vi så gerne have en momentgenererende funktion for S. S var summen af
en række fordelinger Y: $S = Y_1 + ... Y_n$.

Her skal vi bruge en potensregneregel:

Hvis $S = X + Y$, hvor X og Y er uafhængige, så er den momentgenererende funktion 
for S:

$$M_S(t) = E\left[e^{t(X+Y)}\right] = E\left[e^{tX}e^{tY}\right]$$

Når vi så ser på den S vi gerne vil have, i stedet for eksemplet lige ovenfor,
får vi:

$$M_S(t) = \prod_{i=1}^n M_{Y_{i}}(t) \\
= \left[M_{Y_{i}}(t)\right]^n \\
= \left( 1+ \frac{t^2}{2!} + \frac{t^3}{3!}E(Y_i^3) + ... + \frac{t^n}{n!}E(Y_i^n)   \right)^n
$$

Og så hiver vi fat på den momentgenerende funktion for $Z = \frac{S}{\sqrt{n}}$:

$$M_z(t) = M_s\left(\frac{t}{\sqrt{n}}\right) = \\
\left( 1+ \frac{t^2}{2!(n^{½})^2} + \frac{t^3}{3!(n^{½})^3}E(Y_i^3) + ... + \frac{t^n}{n!(n^{½})^n}E(Y_i^n)   \right)^n
$$


We then take the natural logarithm of $M_Z(t)$:

$$
\ln(M_Z(t)) = n\ln \left(1 + \frac{t^2}{2!n} + \frac{t^3}{3!n^{3/2}}E(Y_i^3) + ... + \frac{t^n}{n!n^{n/2}}E(Y_i^n) \right)
$$

This brings me to the part of Analysis 3 that I really hated. We have to guess a solution...


In the same way a Taylor-series exist for $e^x$, there is a Taylor-series for $\ln(1+x)$:

$$
\ln(1+x) = x - \frac{x^2}{2} + \frac{x^3}{3} - \frac{x^4}{4} + \frac{x^5}{5} - \frac{x^6}{6} + ... \\
= \sum_{n=1}^{\infty}\frac{(-1)^{n+1}}{n}x^n
$$

If we insert:
$$
x = 1 + \frac{t^2}{2!n} + \frac{t^3}{3!n^{3/2}}E(Y_i^3) + ... + \frac{t^n}{n!n^{n/2}}E(Y_i^n)
$$

We get: 
$$
\ln(M_Z(t)) = n\ln(1+x) =\\
n\sum_{n=1}^{\infty}\frac{(-1)^{n+1}}{n}x^n = \\
n\left( \sum_{k=1}^{\infty}\frac{(-1)^{k+1}}{k} \left( \frac{t^2}{2!n} + \frac{t^3}{3!n^{3/2}}E(Y_i^3) + ... + \frac{t^n}{n!n^{n/2}}E(Y_i^n) \right)^k\right)
$$

We can split this expression (this is another idea you have to "get").

Insert k = 1 and:

$$
\ln(M_Z(t)) = n\left(\left( \frac{t^2}{2!n} + \frac{t^3}{3!n^{3/2}}E(Y_i^3) + ... + \frac{t^n}{n!n^{n/2}}E(Y_i^n) \right) + 
\sum_{k=2}^{\infty}\frac{(-1)^{k+1}}{k} \left( \frac{t^2}{2!n} + \frac{t^3}{3!n^{3/2}}E(Y_i^3) + ... + \frac{t^n}{n!n^{n/2}}E(Y_i^n) \right)^k\right)
$$

We multiply n into the paranthesis and reduce the first sum:

$$
\ln(M_Z(t)) = \left( \frac{nt^2}{2!n} + \frac{nt^3}{3!n^{3/2}}E(Y_i^3) + ... + \frac{nt^n}{n!n^{n/2}}E(Y_i^n) \right) + 
n\sum_{k=2}^{\infty}\frac{(-1)^{k+1}}{k} \left( \frac{t^2}{2!n} + \frac{t^3}{3!n^{3/2}}E(Y_i^3) + ... + \frac{t^n}{n!n^{n/2}}E(Y_i^n) \right)^k \\
= \left( \frac{t^2}{2!} + \frac{t^3}{3!n^{1/2}}E(Y_i^3) + ... + \frac{t^n}{n!n^{(n-2)/2}}E(Y_i^n) \right) + 
n\sum_{k=2}^{\infty}\frac{(-1)^{k+1}}{k} \left( \frac{t^2}{2!n} + \frac{t^3}{3!n^{3/2}}E(Y_i^3) + ... + \frac{t^n}{n!n^{n/2}}E(Y_i^n) \right)^k
$$

n er jo antallet af fordelinger fra start. Og hele pointen er jo at undersøge
hvad der sker når det antal stiger og nærmer sig uendelig.

$$
\lim_{n \rightarrow \infty}\ln(M_Z(t)) = \lim_{n \rightarrow \infty} \left( \frac{t^2}{2!} + \frac{t^3}{3!n^{1/2}}E(Y_i^3) + ... + \frac{t^n}{n!n^{(n-2)/2}}E(Y_i^n) \right) + 
n\sum_{k=2}^{\infty}\frac{(-1)^{k+1}}{k} \left( \frac{t^2}{2!n} + \frac{t^3}{3!n^{3/2}}E(Y_i^3) + ... + \frac{t^n}{n!n^{n/2}}E(Y_i^n) \right)^k
$$
Hvad sker der når n går mod uendelig? I alle brøker, bortset fra den 
første $\frac{t^2}{2!}$, er der en nævner hvor n optræder med en positiv 
eksponent. Den nævner går derfor mod uendelig, alle brøkerne går derfor mod 0.
Og det eneste der er tilbage er derfor:

$$ 
\lim_{n \rightarrow \infty}\ln(M_Z(t)) \\
= \frac{t^2}{2!} \\
= \frac{t^2}{2}
$$

Exponentiating to get rid of the logarithm give us:

$$
M_Z(t) = e^{\ln(M_Z(t))} = e^{t^2/2}, n \rightarrow \infty
$$

This is identical to the moment generating function of the (standardized) normal distribution.

QED!
