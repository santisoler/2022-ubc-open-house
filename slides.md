<!-- .slide: class="slide-title" -->

<img src="images/about.jpg" alt="" style="border-radius: 50%; width: 20%;">

# [Santiago Soler](https://www.santisoler.com)


<div class="affiliation">

<div>
<a href="https://www.ubc.ca">
<img src="images/logos/ubc.png" style="height: 100%;">
</a>
</div>

<div class="affiliation-items">

[*Geophysical Inversion Facility*](https://gif.eos.ubc.ca/)
<br>
[*Earth, Ocean and Atmospheric Sciences*](https://www.eoas.ubc.ca/)
<br>
[*University of British Columbia*](https://www.ubc.ca/)

</div>

</div>

---

# Background

<div class="container">

<div class="column">
<img src="images/about.jpg" style="margin-top: 5%; border-radius: 50%; width: 80%;">
</div>

<div class="col-3">
<div class="centered">

* Licenciate in Physics (UNR, Argentina)
* PhD in Geophysics (CONICET & UNSJ, Argentina)
* Postdoc Researcher at [UBC](https://www.ubc.ca)
* Member of the
  <img src="images/logos/compgeolab.svg" alt="" style="display: inline; height:
  1em; width: auto; margin: 0 0.2em;">
  [Computer-Oriented Geoscience Lab](https://www.compgeolab.org)
* Core developer of
  <img src="images/logos/fatiando.svg" alt="" style="display: inline; height:
  1em; width: auto; margin: 0 0.2em;">
  [Fatiando a Terra](https://www.fatiando.org)

</div>
</div>

</div>

---

<!-- .slide: class="slide-transition" data-background-color="#de6f2e" -->

# Past research

---

# Gravitational fields in <br> spherical coordinates

---

<div class="container" style="align-items: center;">

<div class="column">

### Tesseroids

<img src="images/tesseroid-definition.svg" style="height: 70vh; width: auto;">

a.k.a. spherical prisms

</div>

<div class="column">

Tesseroids' gravitational fields:

$$
    V(\mathbf{p}) = G \rho
        \iiint_V
        \frac{\kappa}{\left\lVert \mathbf{p} - \mathbf{q} \right\rVert}
        \text{d} r' \text{d} \lambda' \text{d} \phi'
$$

**no analytical solution**

</div>

</div>

---

<div class="container" style="align-items: center;">

<div class="column">

### Tesseroids

<img src="images/tesseroid-definition.svg" style="height: 70vh; width: auto;">

a.k.a. spherical prisms

</div>

<div class="column">

**Solution:**
<br>
Numerical approximations

$$
    V(\mathbf{p}) \cong
        G \rho
        A
        \sum\limits_{i=1}^{N_r}
        \sum\limits_{j=1}^{N_\lambda}
        \sum\limits_{k=1}^{N_\phi}
        W_{ijk}
        \frac{\kappa_{ik}}{\left\lVert \mathbf{p} - \mathbf{q}_{ijk} \right\rVert}
$$

</div>

</div>

---

## Constant density tesseroids

$$
    V(\mathbf{p}) = G {\color{#e4564a}{\rho}}
        \iiint_V
        \frac{\kappa}{\left\lVert \mathbf{p} - \mathbf{q} \right\rVert}
        \text{d} r' \text{d} \lambda' \text{d} \phi'
$$

---

## Variable density tesseroids

$$
    V(\mathbf{p}) = G
        \iiint_V
        {\color{#e4564a}{\rho(r')}}
        \frac{\kappa}{\left\lVert \mathbf{p} - \mathbf{q} \right\rVert}
        \text{d} r' \text{d} \lambda' \text{d} \phi'
$$

---

## Developed new method

- **Gravitational fields** of **variable density tesseroids**
- **Density**: continuous **function of depth**
- Open-source **Python** implementation

<pre style="width: 60vw; min-width: 40em;"><code data-line-numbers="*|4-7|9-11" data-trim data-noescape class="python hljs">
from numba import njit
import harmonica as hm

@njit
def linear_density(radius):
    origin, slope = ...
    return slope * radius  + origin

gravity = hm.tesseroid_gravity(
    coordinates, tesseroids, linear_density, field="g_z"
)
</code></pre>

---

<img src="images/soler2019.png" style="height: 100vh">

<div class="footnote">

Soler et al. (2019). doi: [10.1093/gji/ggz277](https://doi.org/10.1093/gji/ggz277)

Preprint: [10.31223/osf.io/3548g](https://doi.org/10.31223/osf.io/3548g)

</div>

---

# Gradient-boosted equivalent sources

---

<!-- .slide: data-background-color="#eee" data-auto-animate -->

## Equivalent sources

<img src="images/equivalent-sources.svg" style="height: 50vh; width: auto; margin-top: 3em;">

---

<!-- .slide: data-background-color="#eee" data-auto-animate -->

## Equivalent sources

<div class="container align-center">

<div class="column">
<img src="images/equivalent-sources.svg" style="height: 50vh; width: auto; margin-top: 3em;">
</div>

<div class="column">

- Always produce **harmonic fields**
- Consider the **observation heights**

</div>

</div>

---

## THE PROBLEM

### Require too much computational load

- 200.000 data points
- \+ 200.000 equivalent sources
- = **~300GB RAM**

---

## THE SOLUTION

### Gradient-boosted equivalent sources

- Interpolate **very large datasets**
- Significant **reduction** in required **memory**
- Open-source **Python** implementation

<pre style="height: 15em; width: 60vw; min-width: 40em;"><code data-line-numbers="*|4-6|7|9-12" data-trim data-noescape class="python hljs">
import verde as vd
import harmonica as hm

eqs = hm.EquivalentSourcesGB(
    depth=depth, damping=damping, window_size=window_size
)
eqs.fit(coordinates, data)

grid_coords = vd.grid_coordinates(
    region, spacing, extra_coords=height
)
grid = eqs.grid(grid_coords)
</code></pre>

---

## Gridding +1.7 million gravity data points

<div class="container align-center">
<div class="column">
<img src="images/australia-data.jpg" alt="">
</div>
<div class="column">
<img src="images/australia-data-zoom.jpg" alt="">
</div>
</div>

---

## Gridding +1.7 million gravity data points

<div class="container align-center">
<div class="column">
<img src="images/australia-grid.jpg" alt="">
</div>
<div class="column">
<img src="images/australia-grid-zoom.jpg" alt="">
</div>
</div>

---

<img src="images/soler2021.png" style="height: 100vh">

<div class="footnote">

Soler and Uieda (2021). doi: [10.1093/gji/ggab297](https://doi.org/10.1093/gji/ggab297)

Preprint: [10.31223/X58G7C](https://doi.org/10.31223/X58G7C)

</div>

---

<!-- .slide: class="slide-transition" data-background-color="#de6f2e" -->

# Open-source software devlopment

---

<!-- .slide: data-background-color="#0c052d" -->

<img src="images/fatiando-website.png" style="height: 100vh; margin: 0;">

[www.fatiando.org](https://www.fatiando.org)

---

<!-- .slide: class="slide-fatiando-libraries" -->

<!-- ========= -->
<!-- First row -->
<!-- ========= -->
<div class="container" style="justify-content: space-evenly;">

<!-- Verde -->
<div class="column">
<h1 style="display: flex; align-items: center; justify-content: center;">
  <img src="images/verde-logo.png" style="height: 1.5em; width: auto; margin-right: 0.5em;">
  Verde
</h1>


**Spatial** data processing and **interpolation**
with a **Machine Learning** flavour

<ul class="fa-ul">
<li><i class="fa-li fas fa-book fa-fw" title="Verde docs"></i>
   <a href="https://www.fatiando.org/verde">fatiando.org/verde</a>
</li>
<li><i class="fa-li fab fa-github fa-fw" title="Github repository"></i>
   <a href="https://github.com/fatiando/verde">fatiando/verde</a>
</li>
</ul>

</div>

<!-- Boule -->
<div class="column fragment">
<h1 style="display: flex; align-items: center; justify-content: center;">
  <img src="images/boule-logo.png" style="height: 1.5em; width: auto; margin-right: 0.5em;">
  Boule
</h1>

**Reference ellipsoids** and **normal gravity** calculations

<ul class="fa-ul">
<li><i class="fa-li fas fa-book fa-fw" title="Verde docs"></i>
   <a href="https://www.fatiando.org/boule">fatiando.org/boule</a>
</li>
<li><i class="fa-li fab fa-github fa-fw" title="Github repository"></i>
   <a href="https://github.com/fatiando/boule">fatiando/boule</a>
</li>
</ul>

</div>

</div> <!-- container div -->

<!-- ========== -->
<!-- Second row -->
<!-- ========== -->
<div class="container" style="justify-content: space-evenly; margin-top: 2em;">

<!-- Harmonica -->
<div class="column fragment">
<h1 style="display: flex; align-items: center; justify-content: center;">
  <img src="images/harmonica-logo.png" style="height: 1.5em; width: auto; margin-right: 0.5em;">
  Harmonica
</h1>

**Processing** and **modelling potential fields** data

<ul class="fa-ul">
<li><i class="fa-li fas fa-book fa-fw" title="Verde docs"></i>
   <a href="https://www.fatiando.org/harmonica">fatiando.org/harmonica</a>
</li>
<li><i class="fa-li fab fa-github fa-fw" title="Github repository"></i>
   <a href="https://github.com/fatiando/harmonica">fatiando/harmonica</a>
</li>
</ul>

</div>

<!-- Pooch -->
<div class="column fragment">
<h1 style="display: flex; align-items: center; justify-content: center;">
  <img src="images/pooch-logo.png" style="height: 1.5em; width: auto; margin-right: 0.5em;">
  Pooch
</h1>

A friend to **download** and **cache** your data

<ul class="fa-ul">
<li><i class="fa-li fas fa-book fa-fw" title="Verde docs"></i>
   <a href="https://www.fatiando.org/pooch">fatiando.org/pooch</a>
</li>
<li><i class="fa-li fab fa-github fa-fw" title="Github repository"></i>
   <a href="https://github.com/fatiando/pooch">fatiando/pooch</a>
</li>
</ul>

</div>

</div> <!-- container div -->

---

- Description of the project
- Libraries and their capabilities
- Fatiando in the wild

---

# Research and open-source software

---

<!-- .slide: class="slide-transition" -->

# Current research

---

## Carbon mineralization

---

## Physical properties

---

## Goals

- Grav+mag joint inversion to estimate depth and volume of ultramafic rocks
  with good carbonation potential

---

<!-- .slide: class="slide-title" -->

# Muchas gracias | Thank you

<p class="license-icons">
<i class="fab fa-creative-commons"></i><i class="fab fa-creative-commons-by"></i>
</p>

This presentation is available under a

[Creative Commons Attribution 4.0 International License](https://creativecommons.org/licenses/by/4.0/)

---

# Regular slide

## With subtitles

---

# Include math and equations

$$ E = mc^2 $$

---

## And code

```python
import numpy as np

x = np.linspace(0, 10, 11)
y = x ** 2
```

---

# Do you want columns?

<div class="container">

<div class="column">
<img src="images/about.jpg" style="margin-top: 5%; border-radius: 50%; width: 80%;">
</div>

<div class="col-2">
<div class="centered">

* Licenciado en Física (UNR)
* Estudiante de Doctorado en Geofísica (UNSJ)
* Becario Doctoral de CONICET
* Desarrollador de [Fatiando a Terra](https://www.fatiando.org)
* Miembro de [Computer-Oriented Geoscience Lab](https://www.compgeolab.org)

</div>
</div>

</div>

---

# You can add fade-in animations

<div class="container">

<div class="column fragment fade-in">

First element

</div>

<div class="column fragment fade-in">

Second element

</div>

</div>

---

## Even on lists

<ul>
<li class="fragment fade-in">First element</li>
<li class="fragment fade-in">Second element</li>
<li class="fragment fade-in">Third element</li>
</ul>

---

## Highlight current item on list

<ol>
<li class="fragment highlight-current-blue">First element</li>
<li class="fragment highlight-current-blue">Second element</li>
<li class="fragment highlight-current-blue">Third element</li>
</ol>

---

# You can put footnotes

<!-- Stretch to the end of the slide -->
<div class="r-stretch">
</div>

<div class="footnote">

https://www.blog.pythonlibrary.org/2019/04/11/python-used-to-take-photo-of-black-hole/

</div>

---

<!-- .slide: data-background-color="#eee" -->

## You can change the background color

---

## Add quotes

<blockquote>
This is a quote
</blockquote>

---

# Contacto

<div>

<ul class="fa-ul">
<li><i class="fa-li fa fa-envelope"></i>

[santiago.r.soler@gmail.com](mailto:santiago.r.soler@gmail.com)

</li>
<li><i class="fa-li fab fa-twitter"></i>

[@santirsoler](https://twitter.com/santirsoler)

</li>
<li><i class="fa-li fa fa-globe-americas"></i>

[www.santisoler.com](https://www.santisoler.com)

</li>
</ul>

</div>

---

<!-- .slide: class="slide-license" -->

<p class="license-icons">
<i class="fab fa-creative-commons"></i><i class="fab fa-creative-commons-by"></i>
</p>

El contenido de esta presentación está disponible bajo

[Creative Commons Attribution 4.0 International License](https://creativecommons.org/licenses/by/4.0/)

---

<!-- .slide: class="slide-title" -->

# Muchas gracias
