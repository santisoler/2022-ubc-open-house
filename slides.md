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
* Former member of the
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

### Tesseroids

<div class="container" style="align-items: center;">

<div class="column">

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

### Tesseroids

<div class="container" style="align-items: center;">

<div class="column">

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

<!-- .slide: data-auto-animate -->

## Developed new method

- **Gravitational fields** of **variable density tesseroids**
- **Density**: continuous **function of depth**
- Open-source **Python** implementation

---

<!-- .slide: data-auto-animate -->

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

<!-- .slide: data-auto-animate -->

## THE SOLUTION

### Gradient-boosted equivalent sources

- Interpolate **very large datasets**
- Significant **reduction** in required **memory**
- Open-source **Python** implementation

---

<!-- .slide: data-auto-animate -->

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

# Open-source software development

---

<!-- .slide: data-background-color="#060629" data-background-image="images/fatiando-website.png" data-background-size="contain" -->

<div class="container r-stretch" style="flex-direction: column; justify-content: flex-end; align-items: end;">

[www.fatiando.org](https://www.fatiando.org)

</div>

---

<!-- .slide: data-auto-animate -->

## The libraries

---

<!-- .slide: class="slide-fatiando-libraries" data-auto-animate -->

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

# Who we are

<div class="container" style="flex-direction: row; align-items: center; justify-content: center; margin-bottom: 1em;">

<div class="mantainer">
<img src="https://www.github.com/leouieda.png" >
<a href="https://www.leouieda.com">Leonardo Uieda</a>
</div>

<div class="mantainer">
<img src="https://www.github.com/santisoler.png" >
<a href="https://www.santisoler.com">Santiago Soler</a>
</div>

</div>

<div class="contributors">

<img src="https://www.github.com/LL-Geo.png">
<img src="https://www.github.com/MGomezN.png">
<img src="https://www.github.com/aguspesce.png">
<img src="https://www.github.com/mdtanker.png">
<img src="https://www.github.com/birocoles.png">
<img src="https://www.github.com/nshea3.png">
<img src="https://www.github.com/Esteban82.png">
<img src="https://www.github.com/djhoese.png">
<img src="https://www.github.com/lheagy.png">
<img src="https://www.github.com/jessepisel.png">
<img src="https://www.github.com/SAskevold.png">
<img src="https://www.github.com/andersy005.png">
<img src="https://www.github.com/GenevieveBuckley.png">
<img src="https://www.github.com/lukegre.png">
<img src="https://www.github.com/mathause.png">
<img src="https://www.github.com/hmaarrfk.png">
<img src="https://www.github.com/horta.png">
<img src="https://www.github.com/hugovk.png">
<img src="https://www.github.com/dokempf.png">
<img src="https://www.github.com/Xarthisius.png">
<img src="https://www.github.com/jrleeman.png">
<img src="https://www.github.com/drammock.png">
<img src="https://www.github.com/remram44.png">
<img src="https://www.github.com/neutrinoceros.png">
<img src="https://www.github.com/danshapero.png">
<img src="https://www.github.com/matthewturk.png">
<img src="https://www.github.com/avalentino.png">
<img src="https://www.github.com/dabiged.png">

</div>


---

# Who is using Fatiando?

---

<!-- .slide: data-background-image="images/fatiando-papers.png" data-background-size="contain" data-background-repeat="no-repeat" -->

---

<!-- .slide: data-background-color="#eee" -->

<img src="images/fatiando-users-map.svg" style="width: 100vw">

---

<!-- .slide: data-background-color="#eee" -->

# Research and open-source software

<img src="images/software-to-research.svg" style="height: 30vh; margin: 3em 0;">

---

<!-- .slide: data-background-color="#eee" -->

# Research and open-source software

<img src="images/software-research-feedback.svg" style="height: 30vh; margin: 3em 0;">

---

<!-- .slide: data-background-color="#eee" -->

<img src="images/software-research-multi.svg" style="height: 100vh;">

---

<!-- .slide: class="slide-transition" data-background-color="#de6f2e" -->

# Current research

---

<!-- .slide: data-background-color="#eee" -->

## Carbon mineralization

<div class="container align-center">

<div class="column">
<p style="color: #40b93c; margin-bottom: 0">Serpentinization</p>
<img src="images/serpentinization.png" style="width: 90%; margin-top: 0">
<p style="color: #f16738; margin-bottom: 0">Carbonation</p>
<img src="images/carbonation.png" style="width: 90%; margin-top: 0">
</div>

<div class="column-2">

- **Serpentinized** rocks react with **CO2**
- Mineralize in **carbonated** rocks
- **Carbon sequestration** with **ultramafic rocks**

</div>
</div>


<div class="footnote">

Cutts et al. (2021). doi: [10.1029/2021GC009989](https://doi.org/10.1029/2021GC009989)

Mitchinson et al., (2020). ISBN: 978-0-88865-470-0

</div>

---

## Physical properties


<img src="images/density-suscept-plots.svg" style="height: 80vh;">

**Density** and **susceptibility** change with alteration


---

## *Research opportunity:*

## Geophysical inversion for assessing <br> carbonation potential


---

## Goals

<div class="container align-center">
<div class="column">

- **Gravity** + **magnetic** data
- 3D joint **inversions**
- Characterize **volume** and **depth** of rocks with **carbonation potential**

</div>
<div class="column">
<img src="images/3d_plot.png" style="height: 70vh;">
</div>
</div>

---

# Contact

<div>

<ul class="fa-ul">
<li><i class="fa-li fa fa-envelope"></i>

[ssoler@eoas.ubc.ca](mailto:ssoler@eoas.ubc.ca)

</li>
<li><i class="fa-li fa fa-globe-americas"></i>

[www.santisoler.com](https://www.santisoler.com)

</li>
<li><i class="fa-li fab fa-github"></i>

[@santisoler](https://www.github.com/santisoler)

</li>
<li><i class="fa-li fab fa-mastodon"></i>

[@santisoler@scicomm.xyz](https://scicomm.xyz/santisoler)

</li>
</ul>

</div>

---

<!-- .slide: class="slide-license" -->


<h1 style="margin: 1em 0;"> Muchas gracias | Thank you </h1>

**Slides:** [www.santisoler.com/2022-ubc-open-house](https://www.santisoler.com/2022-ubc-open-house)

<p class="license-icons">
<i class="fab fa-creative-commons"></i><i class="fab fa-creative-commons-by"></i>
</p>

These slides are available under a <br>
[Creative Commons Attribution 4.0 International License](https://creativecommons.org/licenses/by/4.0/)
