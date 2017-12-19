Sound Field Synthesis
=====================

.. image:: img/header.png
    :align: center
 
.. toctree::
    :maxdepth: 3
    :hidden:

    index


Sound field synthesis (SFS) includes all methods that try to generate a defined
sound field in an extended area that is surrounded by loudspeakers. This page
focuses on those methods that provide analytical solutions to the underlying
mathematical problem, namely |WFS|, |NFC-HOA|, and the |SDM|.

The Toolboxes provide you with the implementation of the underlying mathematics.
You can make numerical simulations of the resulting sound fields and can even
create binaural simulations of the same sound fields. This enables you to listen
to large loudspeaker arrays, even if you don’t have one in your laboratory or at
home. In addition, you can easily plug-in your own algorithms in order to test
or compare them.

The SFS Toolbox project is structured in the following three sub-projects.

Discussion of Theory:
    http://sfstoolbox.org (current page)

SFS Toolbox for Matlab/Octave:
    http://matlab.sfstoolbox.org

SFS Toolbox for Python:
    http://python.sfstoolbox.org

Most of the figures in this page are directly created by the SFS Toolbox for
Python. All of them display the corresponding code for creating them directly
before the actual figure. In order to recreate them, you have to execute the
following code first:

.. Common plotting settings
.. plot::
    :context: reset

    import numpy as np
    import matplotlib.pyplot as plt
    import sfs
    plt.rcParams['figure.figsize'] = 8, 4.5  # inch

The image at the top of the page is extracted from [ZotterSpors2013]_.  The
following presentation of the theory is based on Chap. 2 from [Wierstorf2014]_.

.. _sec-mathematical-definitions:

Mathematical Definitions
------------------------

.. _sec-coordinate_system:

Coordinate system
~~~~~~~~~~~~~~~~~

Figure shows the coordinate system that is used in the following
chapters. A vector :math:`\x` can be described by its position
:math:`(x,y,z)` in space or by its length, azimuth angle
:math:`\phi \in [0,2\pi[`, and elevation
:math:`\theta \in \left[-\frac{\pi}{2},\frac{\pi}{2}\right]`.
The azimuth is measured counterclockwise and elevation is positive for
positive :math:`z`-values.

.. _fig-coordinate-system:

.. figure:: img/coordinate_system.png
    :align: center

    Coordinate system used in this document. The vector :math:`x` can also be
    described by its length, its azimuth angle :math:`\phi`, and its elevation
    :math:`\theta`.

.. _sec-fourier-transform:

Fourier transformation
~~~~~~~~~~~~~~~~~~~~~~

Let :math:`s` be an absolute integrable function, :math:`t,\w` real
numbers, then the temporal Fourier transform is defined after [Bracewell2000]_
as

.. math::
    :label: fft

    S(\w) = \mathcal{F}\left\{s(t)\right\} =
        \int^{\infty}_{-\infty} s(t) \e{-\i\w t} \d t.

In the same way the inverse temporal Fourier transform is defined as

.. math::
    :label: ifft

    s(t) = \mathcal{F}^{-1}\left\{S(\w)\right\} =
        \frac{1}{2\pi} \int^{\infty}_{-\infty} S(\w)
        \e{\i\w t} \d\w.


.. ============================================================================


.. _sec-problem-statement:

Problem statement
-----------------

.. _fig-geometry:

.. figure:: img/geometry.png
    :align: center

    Illustration of the geometry used to discuss the physical fundamentals of
    sound field synthesis and the single-layer potential.

The problem of sound field synthesis can be formulated after as follows. Assume
a volume :math:`V \subset \mathbb{R}^n` which is free of any sources and sinks,
surrounded by a distribution of monopole sources on its surface :math:`\partial
V`. The pressure :math:`P(\x,\w)` at a point :math:`\x\in V` is then given
by the *single-layer potential* (compare p. 39 in [ColtonKress1998]_)

.. math::
    :label: single-layer

    P(\x,\w) = \oint_{\partial V} D(\x_0,\w) G(\x-\x_0,\w)
        \d A(\x_0),

where :math:`G(\x-\x_0,\w)` denotes the sound propagation of the source at
location :math:`\x_0 \in \partial V`, and :math:`D(\x_0,\w)` its weight,
usually referred to as *driving function*. The sources on the surface are called
*secondary sources* in sound field synthesis, analogue to the case of acoustical
scattering problems. The single-layer potential can be derived from the
Kirchhoff-Helmholtz integral [Williams1999]_. The challenge in sound field
synthesis is to solve the integral with respect to :math:`D(\x_0,\w)` for a
desired sound field :math:`P = S` in :math:`V`. It has unique solutions which
[ZotterSpors2013]_ explicitly showed for the spherical case and [Fazi2010]_
(Chap.4.3) for the planar case.

In the following the single-layer potential for different dimensions is
discussed. An approach to formulate the desired sound field :math:`S` is
described and finally it is shown how to derive the driving function
:math:`D`.


.. ============================================================================


.. _sec-nfchoa:

Solution for Special Geometries: NFC-HOA and SDM
------------------------------------------------

The integral equation :eq:`single-layer` states a Fredholm equation of first
kind with a Green’s function as kernel. This type of equation can be solved in a
straightforward manner for geometries that have a complete set of orthogonal
basis functions.  Then the involved functions are expanded into the basis
functions :math:`\psi_n` as [MorseFeshbach1981]_, p. (940)

.. math::
    :label: G_expansion

    G(\x-\x_0, \w) = \sum_{n} \tilde{G}_n(\w) \psi_n^*(\x_0) \psi_n(\x)

.. math::
    :label: D_expansion

    D(\x_0, \w) = \sum_n \tilde{D}_n(\w) \psi_n(\x_0)

.. math::
    :label: S_expansion

    S(\x, \w) = \sum_n \tilde{S}_n(\w) \psi_n(\x),

where :math:`\tilde{G}_n, \tilde{D}_n, \tilde{S}_n` denote the series expansion
coefficients, :math:`n \in \mathbb{Z}`, and \ :math:`\langle\psi_n,
\psi_{n'}\rangle = 0\,` for :math:`n \ne n'`.
If the underlying space is not compact the equations will involve an integration
instead of a summation

.. math::
    :label: G_expansion_non_compact

    G(\x-\x_0, \w) = \int \tilde{G}(\mu, \w) \psi^*(\mu, \x_0)
        \psi(\mu, \x) \d\mu

.. math::
    :label: D_expansion_non_compact

    D(\x_0, \w) = \int \tilde{D}(\mu, \w) \psi(\mu, \x_0) \d\mu

.. math::
    :label: S_expansion_non_compact

    S(\x, \w) = \int \tilde{S}(\mu, \w) \psi(\mu, \x) \d\mu,

where :math:`\d\mu` is the measure in the underlying space.
Introducing these equations into :eq:`single-layer` one gets

.. math::
    :label: D_HOA

    \tilde{D}_n(\w) =
        \frac{\tilde{S}_n(\w)}{\tilde{G}_n(\w)}.

This means that the Fredholm equation :eq:`single-layer` states a convolution.
For geometries where the required orthogonal basis functions exist, :eq:`D_HOA`
follows directly via the convolution theorem [ArfkenWeber2005]_, eq. (1013).
Due to the division of the desired sound field by the spectrum of the Green’s
function this kind of approach has been named |SDM| [AhrensSpors2010]_.  For
circular and spherical geometries the term |NFC-HOA| is more common due to the
corresponding basis functions. “Near-field compensated” highlights the usage of
point sources as secondary sources in contrast to Ambisonics and |HOA| that
assume plane waves as secondary sources.

The challenge is to find a set of basis functions for a given geometry.
In the following paragraphs three simple geometries and their widely
known sets of basis functions will be discussed.

.. _sec-spherical-geometries:

Spherical Geometries
~~~~~~~~~~~~~~~~~~~~

The spherical harmonic functions constitute a basis for a spherical secondary
source distribution in :math:`{\mathbb{R}}^3` and can be defined as
[GumerovDuraiswami2004]_, eq. (12.153) [#F1]_

.. math::
    :label: spherical-harmonics

    \begin{gathered}
        Y_n^m(\theta,\phi) = (-1)^m \sqrt{\frac{(2n+1)(n-|m|)!}{4\pi(n+|m|)!}}
        P_n^{|m|}(\sin\theta) \e{\i m\phi} \; \\
        n = 0,1,2,... \;\;\;\;\;\; m = -n,...,n
    \end{gathered}

where :math:`P_n^{|m|}` are the associated Legendre functions. Note that
this function may also be defined in a slightly different way, omitting
the :math:`(-1)^m` factor, see for example [Williams1999]_, eq. (6.20).

The complex conjugate of :math:`Y_n^m` is given by negating the degree
:math:`m` as

.. math::
    :label: spherical-harmonics-complex-conjugate

    Y_n^m(\theta,\phi)^* = Y_n^{-m}(\theta,\phi).

For a spherical secondary source distribution with a radius of :math:`R_0` the
sound field can be calculated by a convolution along the surface. The driving
function is then given by a simple division as [Ahrens2012]_, eq. (3.21) [#F2]_

.. math::
    :label: D_spherical

    \begin{gathered}
        D_\text{spherical}(\theta_0,\phi_0,\w) = \\
        \frac{1}{R_0^{\,2}}
        \sum_{n=0}^\infty \sum_{m=-n}^n \sqrt{\frac{2n+1}{4\pi}}
        \frac{\breve{S}_n^m(\theta_\text{s},\phi_\text{s},r_\text{s},\w)}
        {\breve{G}_n^0(\frac{\pi}{2},0,\w)} Y_n^m(\theta_0,\phi_0),
    \end{gathered}

where :math:`\breve{S}_n^m` denote the spherical expansion coefficients of the
source model, :math:`\theta_\text{s}`, :math:`\phi_\text{s}`, and
:math:`r_\text{s}` its directional dependency, and :math:`\breve{G}_n^0` the
spherical expansion coefficients of a secondary monopole source located at
the north pole of the sphere :math:`\x_0 = (\frac{\pi}{2},0,R_0)`. For a point
source this is given as [SchultzSpors2014]_, eq. (25)

.. math::
    :label: G_spherical

    \breve{G}_n^0(\tfrac{\pi}{2},0,\w) =
        -\i\wc \sqrt{\frac{2n+1}{4\pi}}
        \hankel{2}{n}{\wc R_0},

where :math:`\hankel{2}{n}{}` describes the spherical Hankel function of
:math:`n`-th order and second kind.

.. _sec-circular-geometries:

Circular Geometries
~~~~~~~~~~~~~~~~~~~

The following functions build a basis in :math:`\mathbb{R}^2` for a circular
secondary source distribution [Williams1999]_

.. math::
    :label: circular-harmonics

    \Phi_m(\phi) = \e{\i m\phi}.

The complex conjugate of :math:`\Phi_m` is given by negating the degree
:math:`m` as

.. math::
    :label: circular-harmonics-complex-conjugate

    \Phi_m(\phi)^* = \Phi_{-m}(\phi).

For a circular secondary source distribution with a radius of :math:`R_0` the
driving function can be calculated by a convolution along the surface of the
circle as explicitly shown by [AhrensSpors2009a]_ and is then given as

.. math::
    :label: D_circular

    D_\text{circular}(\phi_0,\w) =
        \frac{1}{2\pi R_0} \sum_{m=-\infty}^\infty
        \frac{\breve{S}_m(\phi_\text{s},r_\text{s},\w)}
        {\breve{G}_m(0,\w)} \, \Phi_m(\phi_0),

where :math:`\breve{S}_m` denotes the circular expansion coefficients for the
source model, :math:`\phi_\text{s}`, and :math:`r_\text{s}` its directional
dependency, and :math:`\breve{G}_m` the circular expansion coefficients for a
secondary monopole source. For a line source located at :math:`\x_0 = (0,R_0)`
this is given as

.. math::
    :label: G_circular

    \breve{G}_m(0,\w) = -\frac{\i}{4}
        \Hankel{2}{m}{\wc R_0},

where :math:`\Hankel{2}{m}{}` describes the Hankel function of :math:`m`-th
order and second kind.

.. _sec-planar-goemetries:

Planar Geometries
~~~~~~~~~~~~~~~~~

The basis functions for a planar secondary source distribution located
on the :math:`xz`-plane in :math:`\mathbb{R}^3` are given as

.. math::
    :label: planar-harmonics

    \Lambda(k_x,k_z,x,z) = \e{-\i(k_x x + k_z z)},

where :math:`k_x`, :math:`k_z` are entries in the wave vector :math:`\k` with
:math:`k^2 = (\wc )^2`. The complex conjugate is given by negating
:math:`k_x` and :math:`k_z` as

.. math::
    :label: planar-harmonics-complex-conjugate

    \Lambda(k_x,k_z,x,z)^* = \Lambda(-k_x,-k_z,x,z).

For an infinitely long secondary source distribution located on the
:math:`xz`-plane the driving function can be calculated by a two-dimensional
convolution along the plane as [Ahrens2012]_, eq. (3.65)

.. math::
    :label: D_planar

    D_\text{planar}(x_0,y_0,\w) = \frac{1}{4{\pi}^2} \iint_{-\infty}^\infty
       \frac{\breve{S}(k_x,y_\text{s},k_z,\w)}{\breve{G}(k_x,0,k_z,\w)}
       \Lambda(k_x,x_0,k_z,z_0) \d k_x \d k_z,

where :math:`\breve{S}` denotes the planar expansion coefficients for the source
model, :math:`y_\text{s}` its positional dependency, and :math:`\breve{G}` the
planar expansion coefficients of a secondary point source with
[SchultzSpors2014]_, eq. (49)

.. math::
    :label: G_planar

    \breve{G}(k_x,0,k_z,\w) = -\frac{\i}{2}
        \frac{1}{\sqrt{(\wc )^2-k_x^2-k_z^2}},

for :math:`(\wc )^2 > (k_x^2+k_z^2)`.

For the planar and the following linear geometries the Fredholm equation is
solved for a non compact space :math:`V`, which leads to an infinite and
non-denumerable number of basis functions as opposed to the denumerable case for
compact spaces [SchultzSpors2014]_.

.. _sec-linear_geometries:

Linear Geometries
~~~~~~~~~~~~~~~~~

The basis functions for a linear secondary source distribution located on the
:math:`x`-axis are given as

.. math::
    :label: linear-harmonics

    \chi(k_x,x) = \e{-\i k_x x}.

The complex conjugate is given by negating :math:`k_x` as

.. math::
    :label: linear-harmonics-complex-conjugate

    \chi(k_x,x)^* = \chi(-k_x,x).

For an infinitely long secondary source distribution located on the
:math:`x`-axis the driving function for :math:`{\mathbb{R}}^2` can be calculated
by a convolution along this axis as [Ahrens2012]_, eq. (3.73)

.. math::
    :label: D_linear

    D_\text{linear}(x_0,\w) = \frac{1}{2\pi} \int_{-\infty}^\infty
        \frac{\breve{S}(k_x,y_\text{s},\w)}{\breve{G}(k_x,0,\w)}
        \chi(k_x,x_0) \d k_x,

where :math:`\breve{S}` denotes the linear expansion coefficients for the source
model, :math:`y_\text{s}`, :math:`z_\text{s}` its positional dependency, and
:math:`\breve{G}` the linear expansion coefficients of a secondary line source
with

.. math::
    :label: G_linear

    \breve{G}(k_x,0,\w) = -\frac{\i}{2}
        \frac{1}{\sqrt{(\wc )^2-k_x^2}},

for :math:`0<|k_x|<|\wc |\,`.


.. ============================================================================


.. _sec-wfs:

High Frequency Approximation: WFS
---------------------------------

The single-layer potential :eq:`single-layer` satisfies the homogeneous
Helmholtz equation both in the interior and exterior regions :math:`V` and
:math:`V^* {\mathrel{\!\mathop:}=}{\mathbb{R}}^n \setminus (V \cup \partial V)\,`.
If :math:`D(\x_0,\w)` is continuous, the pressure :math:`P(\x,\w)` is
continuous when approaching the surface :math:`\partial V` from the inside and
outside. Due to the presence of the secondary sources at the surface
:math:`\partial V`, the gradient of :math:`P(\x,\w)` is discontinuous when
approaching the surface.  The strength of the secondary sources is then given by
the differences of the gradients approaching :math:`\partial V` from both sides
as [FaziNelson2013]_

.. math::
    :label: D_gradient

    D(\x_0,\w) = \partial_\n P(\x_0,\w) +
        \partial_{-\n} P(\x_0,\w),

where :math:`\partial_\n{\mathrel{\mathop:}=}\scalarprod{\nabla}{\n}` is
the directional gradient in direction :math:`\n` – see :numref:`fig-geometry`.
Due to the symmetry of the problem the solution for an infinite planar boundary
:math:`\partial V` is given as

.. math::
    :label: D_wfs

    D(\x_0,\w) = -2 \partial_\n S(\x_0,\w),

where the pressure in the outside region is the mirrored interior pressure given
by the source model :math:`S(\x,\w)` for :math:`\x\in V`. The integral
equation resulting from introducing :eq:`D_wfs` into :eq:`single-layer` for a
planar boundary :math:`\partial V` is known as *Rayleigh’s first integral
equation*. This solution is identical to the explicit solution for planar
geometries :eq:`D_planar` in :math:`{\mathbb{R}}^3` and for linear
geometries :eq:`D_linear` in :math:`{\mathbb{R}}^2`.

A solution of :eq:`D_gradient` for arbitrary boundaries can be found by applying
the *Kirchhoff* or *physical optics approximation* [ColtonKress1983]_, p. 53–54.
In acoustics this is also known as *determining the visible elements* for the
high frequency boundary element method [Herrin2003]_.  Here, it is assumed that
a bent surface can be approximated by a set of small planar surfaces for
which :eq:`D_wfs` holds locally.  In general, this will be the case if the wave
length is much smaller than the size of a planar surface patch and the position
of the listener is far away from the secondary sources. [#F3]_ Additionally, only
one part of the surface is active: the area that is illuminated from the
incident field of the source model.

The outlined approximation can be formulated by introducing a window function
:math:`w(\x_0)` for the selection of the active secondary sources
into :eq:`D_wfs` as

.. math::
    :label: P_wfs

    P(\x,\w) \approx \oint_{\partial V} \!\!  G(\x|\x_0,\w) \,
        \underbrace{-2 w(\x_0) \partial_\n S(\x_0,\w)}_{D(\x_0,\w)}
        \d A(\x_0).

In the SFS Toolbox we assume convex secondary source distributions, which
allows to formulate the window function by a scalar product with the normal
vector of the secondary source distribution.  In general, also non-convex
secondary source distributions can be used with |WFS| – compare the appendix in
[LaxFeshbach1947]_ and [#F4]_.

One of the advantages of the applied approximation is that due to its local
character the solution of the driving function :eq:`D_wfs` does not depend on
the geometry of the secondary sources. This dependency applies to the direct
solutions presented in :ref:`sec-nfchoa`.


.. ============================================================================


.. _sec-dimensionality:

Sound Field Dimensionality
--------------------------

The single-layer potential :eq:`single-layer` is valid for all :math:`V \subset
{\mathbb{R}}^n`.  Consequentially, for practical applications a two-dimensional
(2D) as well as a three-dimensional (3D) synthesis is possible. Two-dimensional
is not referring to a synthesis in a plane only, but describes a setup that is
independent of one dimension. For example, an infinite cylinder is independent
of the dimension along its axis. The same is true for secondary source
distributions in 2D synthesis.  They exhibit line source characteristics and are
aligned in parallel to the independent dimension. Typical arrangements of such
secondary sources are a circular or a linear setup.

The characteristics of the secondary sources limit the set of possible sources
which can be synthesized. For example, when using a 2D secondary source setup it
is not possible to synthesize the amplitude decay of a point source.

For a 3D synthesis the involved secondary sources depend on all dimensions and
exhibit point source characteristics. In this scenario classical secondary
sources setups would be a sphere or a plane.

.. _sec-25d-synthesis:

2.5D Synthesis
~~~~~~~~~~~~~~

.. _fig-sound-field-dimensionality:

.. figure:: img/sound_field_dimensionality.png
    :align: center

    Sound pressure in decibel for secondary source distributions with different
    dimensionality all driven by the same signals. The sound pressure is color
    coded, lighter color corresponds to lower pressure. In the 3D case a planar
    distribution of point sources is applied, in the 2.5D case a linear
    distribution of point sources, and in the 2D case a linear distribution of
    line sources.

In practice, the most common setups of secondary sources are 2D setups,
employing cabinet loudspeakers. A cabinet loudspeaker does not show the
characteristics of a line source, but of a point source. This dimensionality
mismatch prevents perfect synthesis within the desired plane. The combination of
a 2D secondary source setup with secondary sources that exhibit 3D
characteristics has led to naming such configurations *2.5D synthesis*
[Start1997]_. Such scenarios are associated with a wrong amplitude decay due to
the inherent mismatch of secondary sources as is highlighted in
:numref:`fig-sound-field-dimensionality`. In general, the amplitude is only
correct at a given reference point :math:`\xref`.

For a circular secondary source distribution with point source characteristic
the 2.5D driving function can be derived by introducing expansion coefficients
for the spherical case into the driving function :eq:`D_circular`. The equation
is than solved for :math:`\theta = 0{^\circ}` and :math:`r_\text{ref} = 0`. This
results in a 2.5D driving function given as [Ahrens2012]_, eq. (3.49)

.. math::
    :label: D_circular_25D

    D_{\text{circular},\text{2.5D}}(\phi_0,\w) = \frac{1}{2\pi R_0}
        \sum_{m=-\infty}^\infty \frac{\breve{S}_{|m|}^m
        (\frac{\pi}{2},\phi_\text{s},r_\text{s},\w)}{\breve{G}_{|m|}^m
       (\frac{\pi}{2},0,\w)} \Phi_m(\phi_0).

For a linear secondary source distribution with point source characteristics the
2.5D driving function is derived by introducing the linear expansion
coefficients for a monopole source :eq:`point-source-linear-coefficients` into
the driving function :eq:`D_linear` and solving the equation for :math:`y =
y_\text{ref}` and :math:`z = 0`. This results in a 2.5D driving function given
as [Ahrens2012]_, eq. (3.77)

.. math::
    :label: D_linear_25D

    D_{\text{linear},\text{2.5D}}(x_0,\w) = \frac{1}{2\pi}
        \int_{-\infty}^\infty \frac{\breve{S}(k_x,y_\text{ref},0,\w)}
        {\breve{G}(k_x,y_\text{ref},0,\w)} \chi(k_x,x_0) \d k_x.

A driving function for the 2.5D situation in the context of |WFS| and arbitrary
2D geometries of the secondary source distribution can be achieved by applying
the far-field approximation  :math:`\Hankel{2}{0}{\zeta} \approx
\sqrt{\frac{2\i}{\pi\zeta}} \e{-\i\zeta}` for
:math:`\zeta \gg 1` to the 2D Green’s function [Williams1999]_, eq. (4.23).
Using this the following relationship between the 2D and 3D Green’s functions
can be established.

.. math::
    :label: 25D_approximation

    \begin{gathered}
        \underbrace{-\frac{\i}{4} \;
            \Hankel{2}{0}{\wc |\x-\x_0|}}_{G_\text{2D}(\x-\x_0,\w)}
        \approx
        \sqrt{2\pi\frac{c}{\i\w} |\x-\x_0|} \;
        \underbrace{
            \frac{1}{4 \pi}
            \frac{\e{-\i\wc |\x-\x_0|}}
            {|\x-\x_0|}}_{G_\text{3D}(\x-\x_0,\w)},
    \end{gathered}

where :math:`\Hankel{2}{0}{}` denotes the Hankel function of second kind and
zeroth order. Inserting this approximation into the single-layer potential for
the 2D case results in

.. math::
    :label: single-layer_25D

    P(\x,\w) = \oint_S \sqrt{2\pi\frac{c}{\i\w}
        |\x-\x_0|} \; D(\x_0,\w) G_\text{3D}(\x-\x0,\w) \d A(\x_0).

If the amplitude correction is further restricted to one reference point
:math:`\xref`, 2.5D the driving function for |WFS| can be formulated as

.. math::
    :label: D25D_wfs

    D_\text{2.5D}(\x_0,\w) = \underbrace{\sqrt{2\pi|\xref-\x_0|}}_{g_0}
        \sqrt{\frac{c}{\i\w}} \, D(\x_0,\w),

where :math:`g_0` is independent of :math:`\x`.


.. ============================================================================


.. _model-based-rendering:

Model-Based Rendering
---------------------

Knowing the pressure field of the desired source :math:`S(\x,\w)` is
required in order to derive the driving signal for the secondary source
distribution. It can either be measured, i.e. recorded, or modeled. While the
former is known as *data-based rendering*, the latter is known as *model-based
rendering*.  For data-based rendering, the problem of how to capture a complete
sound field still has to be solved. [Avni2013]_ et al. discuss some influences
of the recording limitations on the perception of the reproduced sound field.
This document will consider only model-based rendering.

Frequently applied models in model-based rendering are plane waves, point
sources, or sources with a prescribed complex directivity. In the following the
models used within the SFS Toolbox are presented.

.. _sec-plane-wave:

Plane Wave
~~~~~~~~~~

.. plot::
    :context: close-figs

    nk = sfs.util.direction_vector(np.radians(45))  # direction of plane wave
    xs = 0, 0, 0  # center of plane wave
    omega = 2 * np.pi * 800  # frequency
    grid = sfs.util.xyz_grid([-1.75, 1.75], [-1.75, 1.75], 0, spacing=0.02)
    p = sfs.mono.source.plane(omega, xs, nk, grid)
    sfs.plot.soundfield(p, grid);

.. _fig-plane-wave:

.. figure:: img/placeholder.png
    :align: center

    Sound pressure for a monochromatic plane wave :eq:`S.pw` going into the direction
    :math:`(1, 1, 0)`. Parameters: :math:`f = 800` Hz.

The source model for a plane wave is given as [Williams1999]_, eq. (2.24) [#F5]_

.. math::
    :label: S.pw

    S(\x,\w) = A(\w) \e{-\i\wc \scalarprod{\n_k}{\x}},

where :math:`A(\w)` denotes the frequency spectrum of the source and
:math:`\n_k` a unit vector pointing into the direction of the plane wave.

Transformed in the temporal domain this becomes

.. math::
    :label: s.pw

    s(\x,t) = a(t) * \dirac{t -\frac{\scalarprod{\n_k}{\x}}{c}},

where :math:`a(t)` is the Fourier transformation of the frequency spectrum
:math:`A(\w)`.

The expansion coefficients for spherical basis functions are given as
[Ahrens2012]_, eq. (2.38)

.. math::
    :label: plane-wave-spherical-coefficients

    \breve{S}_n^m(\theta_k,\phi_k,\w) = 4\pi\i^{-n}
        Y_n^{-m}(\theta_k,\phi_k),

where :math:`(\phi_k,\theta_k)` is the radiating direction of the plane wave.

In a similar manner the expansion coefficients for circular basis functions are
given as

.. math::
    :label: plane-wave-circular-coefficients

    \breve{S}_m(\phi_\text{s},\w) = \i^{-n}
        \Phi_{-m}(\phi_\text{s}).

The expansion coefficients for linear basis functions are given as after
[Ahrens2012]_, eq. (C.5)

.. math::
    :label: plane-wave-linear-coefficients

    \breve{S}(k_x,y,\w) = 2\pi\dirac{k_x-k_{x,\text{s}}}
        \chi(k_{y,\text{s}},y),

where :math:`(k_{x,\text{s}},k_{y,\text{s}})` points into the radiating
direction of the plane wave.

.. _sec-point-source:

Point Source
~~~~~~~~~~~~

.. plot::
    :context: close-figs

    xs = 0, 0, 0  # position of source
    omega = 2 * np.pi * 800  # frequency
    grid = sfs.util.xyz_grid([-1.75, 1.75], [-1.75, 1.75], 0, spacing=0.02)
    p = sfs.mono.source.point(omega, xs, [], grid)
    normalization = 4 * np.pi
    sfs.plot.soundfield(normalization * p, grid);

.. _fig-point-source:

.. figure:: img/placeholder.png
    :align: center

    Sound pressure for a monochromatic point source :eq:`S.ps` placed at :math:`(0, 0, 0)`.
    Parameters: :math:`f = 800` Hz.

The source model for a point source is given by the three dimensional Green’s
function as [Williams1999]_, eq. (6.73)

.. math::
    :label: S.ps

    S(\x,\w) = A(\w) \frac{1}{4\pi} \frac{\e{-\i
        \wc |\x-\xs|}}{|\x-\xs|},

where :math:`\xs` describes the position of the point source.

Transformed to the temporal domain this becomes

.. math::
    :label: s.ps

    s(\x,t) = a(t) * \frac{1}{4\pi} \frac{1}{|\x-\xs|}
        \dirac{t - \frac{|\x-\xs|}{c}}.

The expansion coefficients for spherical basis functions are given
as [Ahrens2012]_, eq. (2.37)

.. math::
    :label: point-source-spherical-coefficients

    \breve{S}_n^m(\theta_\text{s},\phi_\text{s},r_\text{s},\w) =
        -\i\wc
        \hankel{2}{n}{\wc r_\text{s}}
        Y_n^{-m}(\theta_\text{s},\phi_\text{s}),

where :math:`(\phi_\text{s},\theta_\text{s},r_\text{s})` describes the position
of the point source.

The expansion coefficients for linear basis functions are given as
[Ahrens2012]_, eq. (C.10)

.. math::
    :label: point-source-linear-coefficients

    \breve{S}(k_x,y,\w) =
        -\frac{\i}{4}
        \Hankel{2}{0}{\sqrt{(\tfrac{\w}{c})^2-k_x^2} \; |y-y_\text{s}|}
        \chi(-k_x,x_\text{s}),

for :math:`|k_x|<|\wc |` and with :math:`(x_\text{s},y_\text{s})`
describing the position of the point source.

.. _sec-dipole-point-source:

Dipole Point Source
~~~~~~~~~~~~~~~~~~~

.. plot::
    :context: close-figs

    xs = 0, 0, 0  # position of source
    ns = sfs.util.direction_vector(0)  # direction of source
    omega = 2 * np.pi * 800  # frequency
    grid = sfs.util.xyz_grid([-1.75, 1.75], [-1.75, 1.75], 0, spacing=0.02)
    p = sfs.mono.source.point_dipole(omega, xs, ns, grid)
    sfs.plot.soundfield(p, grid);

.. _fig-dipole-point-source:

.. figure:: img/placeholder.png
    :align: center

    Sound pressure for a monochromatic dipole point source :eq:`S.dps` placed at
    :math:`(0, 0, 0)` and pointing towards :math:`(1, 0, 0)`.  Parameters:
    :math:`f = 800` Hz.

The source model for a three dimensional dipole source is given by the
directional derivative of the three dimensional Green’s function with respect to
:math:`{\n_\text{s}}` defining the orientation of the dipole source.

.. math::
    :label: S.dps

    \begin{aligned}
        S(\x,\w) &= A(\w) \frac{1}{4\pi}
            \scalarprod{\nabla_{\xs} \frac{\e{-\i
            \wc |\x-\xs|}}{|\x-\xs|}}{\n_\text{s}} \\
        &=
            A(\w) \frac{1}{4\pi}
            \left( \frac{1}{|\x-\xs|} + \i\wc \right)
            \frac{\scalarprod{\x-\xs}{\n_\text{s}}}{|\x-\xs|^2}
            \e{-\i\wc |\x-\xs|}.
    \end{aligned}

Transformed to the temporal domain this becomes

.. math::
    :label: s.dps

    s(\x,t) = a(t) *
        \left( \frac{1}{|\x-\xs|} + {\mathcal{F}^{-1}\left\{
        \frac{\i\w}{c} \right\}} \right) *
        \frac{\scalarprod{\x-\xs}{\n_\text{s}}}{4\pi|\x-\xs|^2}
        \dirac{t - \frac{|\x-\xs|}{c}}.

.. _sec-line-source:

Line Source
~~~~~~~~~~~

.. plot::
    :context: close-figs

    xs = 0, 0, 0  # position of source
    omega = 2 * np.pi * 800  # frequency
    grid = sfs.util.xyz_grid([-1.75, 1.75], [-1.75, 1.75], 0, spacing=0.02)
    p = sfs.mono.source.line(omega, xs, None, grid)
    normalization = np.sqrt(8 * np.pi * omega / sfs.defs.c) * np.exp(1j * np.pi / 4)
    sfs.plot.soundfield(normalization * p, grid);

.. _fig-line-source:

.. figure:: img/placeholder.png
    :align: center

    Sound pressure for a monochromatic line source :eq:`S.ls` placed at :math:`(0, 0, 0)`.
    Parameters: :math:`f = 800` Hz.

The source model for a line source is given by the two dimensional Green’s
function as [Williams1999]_, eq. (8.47)

.. math::
    :label: S.ls

    S(\x,\w) = -A(\w) \frac{\i}{4} \Hankel{2}{0}{\wc |\x-\xs|}.

Applying the large argument approximation of the Hankel function
[Williams1999]_, eq. (4.23) and transformed to the temporal domain this becomes

.. math::
    :label: s.ls

    s(\x,t) = a(t) * \mathcal{F}^{-1}\left\{\sqrt{
        \frac{c}{\i\w}}\right\} * \sqrt{\frac{1}{8\pi}}
        \frac{1}{\sqrt{|\x-\xs|}}
        \dirac{t - \frac{|\x-\xs|}{c}}.

The expansion coefficients for spherical basis functions are given
as [Hahn2015]_, eq. (15)

.. math::
    :label: line-source-spherical-coefficients

    \breve{S}_n^m(\phi_\text{s},r_\text{s},\w) =
        -\pi \i^{m-n+1}
        \Hankel{2}{m}{\wc r_\text{s}}
        Y_n^{-m}(0,\phi_\text{s}).

The expansion coefficients for circular basis functions are given as

.. math::
    :label: line-source-circular-coefficients

    \breve{S}_m(\phi_\text{s},r_\text{s},\w) = -\frac{\i}{4}
        \Hankel{2}{m}{\wc r_\text{s}}
        \Phi_{-m}(\phi_\text{s}).

The expansion coefficients for linear basis functions are given as

.. math::
    :label: line-source-linear-coefficients

    \breve{S}(k_x,y_\text{s},\w) = -\frac{\i}{2}
        \frac{1}{\sqrt{(\wc )^2-k_x^2}}
        \chi(k_y,y_\text{s}).


.. ============================================================================

.. _sec-driving-functions-nfchoa-sdm:

Driving functions for NFC-HOA and SDM
-------------------------------------

In the following, driving functions for |NFC-HOA| and |SDM| are derived
for spherical, circular, and linear secondary source distributions. Among the
possible combinations of methods and secondary sources not all are meaningful.
Hence, only the relevant ones will be presented. The same holds for the
introduced source models of plane waves, point sources, line sources and focused
sources. [AhrensSpors2010]_ in addition have considered |SDM|
driving functions for planar secondary source distributions.

For |NFC-HOA|, temporal-domain implementations for the
2.5D cases are available for a plane wave and a point source as source models. The
derivation of the implementation is not explicitly shown here, but is described
in [Spors2011]_.

.. _sec-driving-functions-nfchoa-sdm-plane-wave:

Plane Wave
~~~~~~~~~~

.. plot::
    :context: close-figs

    nk = 0, -1, 0  # direction of plane wave
    omega = 2 * np.pi * 1000  # frequency
    R0 = 1.5  # radius of secondary sources
    x0, n0, a0 = sfs.array.circular(200, R0)
    grid = sfs.util.xyz_grid([-1.75, 1.75], [-1.75, 1.75], 0, spacing=0.02)
    d = sfs.mono.drivingfunction.nfchoa_25d_plane(omega, x0, R0, nk)
    p = sfs.mono.synthesized.generic(omega, x0, n0, d * a0 , grid,
        source=sfs.mono.source.point)
    normalization = 0.05
    sfs.plot.soundfield(normalization * p, grid);
    sfs.plot.secondarysource_2d(x0, n0, grid)

.. _fig-nfchoa-25d-plane-wave:

.. figure:: img/placeholder.png
    :align: center

    Sound pressure for a monochromatic plane wave synthesized with 2.5D
    |NFC-HOA| :eq:`D.nfchoa.pw.2.5D`.  Parameters: :math:`\n_k = (0, -1, 0)`,
    :math:`\xref = (0, 0, 0)`, :math:`f = 1` kHz.

For a spherical secondary source distribution with radius :math:`R_0` the
spherical expansion coefficients of a plane
wave :eq:`plane-wave-spherical-coefficients` and of the Green’s function for a
point source :eq:`G_spherical` are inserted into :eq:`D_spherical` and yield
[SchultzSpors2014]_, eq. (A3)

.. math::
    :label: D.nfchoa.pw.3D

    D_\text{spherical}(\theta_0,\phi_0,\w) = -A(\w)
        \frac{4\pi}{R_0^{\,2}} \sum_{n=0}^\infty \sum_{m=-n}^n
        \frac{\i^{-n} Y_n^{-m}(\theta_k,\phi_k)}
        {\i\wc \hankel{2}{n}{\wc R_0}}
        Y_n^m(\theta_0,\phi_0).

For a circular secondary source distribution with radius :math:`R_0` the
circular expansion coefficients of a plane
wave :eq:`plane-wave-circular-coefficients` and of the Green’s function for a
line source :eq:`G_circular` are inserted into :eq:`D_circular` and yield
[AhrensSpors2009a]_, eq. (16)

.. math::
    :label: D.nfchoa.pw.2D

    D_\text{circular}(\phi_0,\w) = -A(\w) \frac{2\i}{\pi R_0}
        \sum_{m=-\infty}^\infty \frac{\i^{-m}\Phi_{-m}(\phi_k)}
        {\Hankel{2}{m}{\wc R_0}} \Phi_m(\phi_0).

For a circular secondary source distribution with radius :math:`R_0` and point
source as Green’s function the 2.5D driving function is given by inserting the
spherical expansion coefficients for a plane
wave :eq:`plane-wave-spherical-coefficients` and a point
source :eq:`point-source-spherical-coefficients` into :eq:`D_circular_25D` as

.. math::
    :label: D.nfchoa.pw.2.5D

    D_{\text{circular},\,\text{2.5D}}(\phi_0,\w) = -A(\w)
        \frac{2}{R_0} \sum_{m=-\infty}^\infty
        \frac{\i^{-|m|} \Phi_{-m}(\phi_k)}
        {\i\wc \hankel{2}{|m|}{\wc R_0}} \Phi_m(\phi_0).

For an infinite linear secondary source distribution located on the
:math:`x`-axis the 2.5D driving function is given by inserting the linear
expansion coefficients for a point source as Green’s
function :eq:`point-source-linear-coefficients` and a plane
wave :eq:`plane-wave-linear-coefficients` into :eq:`D_linear_25D` and exploiting
the fact that :math:`(\wc )^2 - k_{x_\text{s}}` is constant.
Assuming :math:`0 \le |k_{x_\text{s}}| \le |\wc |` this results in
[AhrensSpors2010]_, eq. (17)

.. math::
    :label: D.sdm.pw.2.5D

    D_{\text{linear},\,\text{2.5D}}(x_0,\w) = A(\w)
        \frac{4\i\chi(k_y,y_\text{ref})}
        {\Hankel{2}{0}{k_y y_\text{ref}}} \chi(k_x,x_0).

Transferred to the temporal domain this results in [AhrensSpors2010]_, eq. (18)

.. math::
    :label: d.sdm.pw.2.5D

    d_{\text{linear},\,\text{2.5D}}(x_0,t) = h(t) *
        a\left(t-\frac{x_0}{c}\sin\phi_k-\frac{y_\text{ref}}{c}\sin\phi_k\right),

where :math:`\phi_k` denotes the azimuth direction of the plane wave and

.. math::
    :label: h.sdm

    h(t) = {\mathcal{F}^{-1}\left\{\frac{4\i}
        {\Hankel{2}{0}{k_y y_\text{ref}}}\right\}}.

The advantage of this result is that it can be implemented by a simple weighting
and delaying of the signal, plus one convolution with :math:`h(t)`. The same
holds for the driving functions of |WFS| as presented in the next section.

.. _sec-driving-functions-nfchoa-sdm-point-source:

Point Source
~~~~~~~~~~~~

.. plot::
    :context: close-figs

    xs = 0, 2.5, 0  # position of source
    omega = 2 * np.pi * 1000  # frequency
    R0 = 1.5  # radius of secondary sources
    x0, n0, a0 = sfs.array.circular(200, R0)
    grid = sfs.util.xyz_grid([-1.75, 1.75], [-1.75, 1.75], 0, spacing=0.02)
    d = sfs.mono.drivingfunction.nfchoa_25d_point(omega, x0, R0, xs)
    p = sfs.mono.synthesized.generic(omega, x0, n0, d * a0 , grid,
        source=sfs.mono.source.point)
    normalization = 20
    sfs.plot.soundfield(normalization * p, grid);
    sfs.plot.secondarysource_2d(x0, n0, grid)

.. _fig-nfchoa-25d-point-source:

.. figure:: img/placeholder.png
    :align: center

    Sound pressure for a monochromatic point source synthesized with 2.5D
    |NFC-HOA| :eq:`D.nfchoa.ps.2.5D`.  Parameters: :math:`\xs = (0, 2.5, 0)` m,
    :math:`\xref = (0, 0, 0)`, :math:`f = 1` kHz.

For a spherical secondary source distribution with radius :math:`R_0` the
spherical coefficients of a point
source :eq:`point-source-spherical-coefficients` and of the Green’s
function :eq:`G_spherical` are inserted into :eq:`D_spherical` and yield
[Ahrens2012]_, eq. (5.7) [#F2]_

.. math::
    :label: D.nfchoa.ps.3D

    D_\text{spherical}(\theta_0,\phi_0,\w) =
        A(\w) \frac{1}{R_0^{\,2}} \sum_{n=0}^\infty \sum_{m=-n}^n
        \frac{\hankel{2}{n}{\wc r_\text{s}}
        Y_n^{-m}(\theta_\text{s},\phi_\text{s})}
        {\hankel{2}{n}{\wc R_0}} Y_n^m (\theta_0,\phi_0).

For a circular secondary source distribution with radius :math:`R_0` and point
source as secondary sources the 2.5D driving function is given by inserting the
spherical coefficients :eq:`point-source-spherical-coefficients`
and :eq:`G_spherical` into :eq:`D_circular_25D`. This results in [Ahrens2012]_,
eq. (5.8)

.. math::
    :label: D.nfchoa.ps.2.5D

    D_{\text{circular},\,\text{2.5D}}(\phi_0,\w) =
        A(\w) \frac{1}{2\pi R_0} \sum_{m=-\infty}^{\infty}
        \frac{\hankel{2}{|m|}{\wc r_\text{s}}
        \Phi_{-m}(\phi_\text{s})}
        {\hankel{2}{|m|}{\wc R_0}} \Phi_m(\phi_0).

For an infinite linear secondary source distribution located on the
:math:`x`-axis and point sources as secondary sources the 2.5D driving function
for a point source is given by inserting the corresponding linear expansion
coefficients :eq:`point-source-linear-coefficients` and :eq:`G_linear`
into :eq:`D_linear_25D`.  Assuming :math:`0 \le |k_x| < |\wc |` this
results in [Ahrens2012]_, eq. (4.53)

.. math::
    :label: D.sdm.ps.2.5D

    D_{\text{linear},\,\text{2.5D}}(x_0,\w) =
        A(\w) \int_{-\infty}^\infty \frac{
        \Hankel{2}{0}{\sqrt{(\wc )^2-k_x^2} \;
        (y_\text{ref}-y_\text{s})} \chi(-k_x,x_\text{s})}
        {\Hankel{2}{0}{\sqrt{(\wc )^2-k_x^2} \;
        y_\text{ref}}} \chi(k_x,x_0) \d k_x.

.. _sec-driving-functions-nfchoa-sdm-line-source:

Line Source
~~~~~~~~~~~

For a spherical secondary source distribution with radius :math:`R_0` the spherical
coefficients of a line source :eq:`line-source-spherical-coefficients` and of
the Green's function :eq:`G_spherical` are inserted into :eq:`D_spherical` and
yield [Hahn2015]_, eq. (20)

.. math::
    :label: D.nfchoa.ls.3D

    D_{\text{spherical}}(\theta_0,\phi_0,\w) = A(\w) \frac{1}{2 R_0^2}
        \sum_{n=0}^{\infty} \sum_{m=-n}^{n}
        \frac{\i^{m-n} \Hankel{2}{m}{\wc r_\text{s}}
        Y_n^{-m}(0,\phi_\text{s})}
        {\wc \hankel{2}{n}{\wc R_0}}
        Y_n^m(\theta_0,\phi_0).

For a circular secondary source distribution with radius :math:`R_0` and line
sources as secondary sources the driving function is given by inserting the
circular coefficients :eq:`line-source-circular-coefficients`
and :eq:`G_circular` into :eq:`D_circular` as

.. math::
    :label: D.nfchoa.ls.2D

    D_{\text{circular}}(\phi_0,\w) = A(\w) \frac{1}{2\pi R_0}
        \sum_{m=-\infty}^{\infty}
        \frac{\Hankel{2}{m}{\wc r_\text{s}}
        \Phi_{-m}(\phi_\text{s})} {\Hankel{2}{m}{\wc R_0}}
        \Phi_m(\phi_0).

For a circular secondary source distribution with radius :math:`R_0` and point
sources as secondary sources the 2.5D driving function is given by inserting the
spherical coefficients :eq:`line-source-spherical-coefficients`
and :eq:`G_spherical` into :eq:`D_circular_25D` as [Hahn2015]_, eq. (23)

.. math::
    :label: D.nfchoa.ls.2.5D

    D_{\text{circular},\,\text{2.5D}}(\phi_0,\w) =
        A(\w) \frac{1}{2 R_0} \sum_{m=-\infty}^{\infty}
        \frac{\i^{m-|m|} \Hankel{2}{m}{\wc r_\text{s}}
        \Phi_{-m}(\phi_\text{s})}
        {\wc \hankel{2}{|m|}{\wc R_0}}
        \Phi_m(\phi_0).

For an infinite linear secondary source distribution located on the
:math:`x`-axis and line sources as secondary sources the driving function is
given by inserting the linear coefficients :eq:`line-source-linear-coefficients`
and :eq:`G_linear` into :eq:`D_linear` as

.. math::
    :label: D.sdm.ls.2D

    D_\text{linear}(x_0,\w) = A(\w) \frac{1}{2\pi}
        \int_{-\infty}^\infty \chi(k_y,y_s) \chi(k_x,x_0) \d k_x.

.. _sec-driving-functions-nfchoa-sdm-focused-source:

Focused Source
~~~~~~~~~~~~~~

Focused sources mimic point or line sources that are located inside the audience
area. For the single-layer potential the assumption is that the audience area is
free from sources and sinks. However, a focused source is neither of them. It
represents a sound field that converges towards a focal point and diverges
afterwards. This can be achieved by reversing the driving function of a point or
line source in time which is known as time reversal focusing [Yon2003]_.

Nonetheless, the single-layer potential should not be solved for focused sources
without any approximation. In the near field of a source, evanescent waves
appear for spatial frequencies :math:`k_x > |\wc |` [Williams1999]_,
eq. (24). They decay exponentially with the distance from the source.  An exact
solution for a focused source is supposed to include these evanescent waves
around the focal point. That is only possible by applying very large amplitudes
to the secondary sources, compare Fig. 2a in [SporsAhrens2010]_. Since the
evanescent waves decay rapidly and are hence not influencing the perception,
they can easily be omitted. For corresponding driving functions for focused
sources without the evanescent part of the sound field see [SporsAhrens2010]_
for |SDM| and [AhrensSpors2009b]_ for |NFC-HOA|.

In the SFS Toolbox only focused sources in |WFS| are considered at the moment.


.. _sec-driving-functions-wfs:

Driving functions for WFS
-------------------------

In the following, the driving functions for |WFS| in the frequency and temporal
domain for selected source models are presented. The temporal domain functions
consist of a filtering of the source signal and a weighting and delaying of the
individual secondary source signals. This property allows for a very efficient
implementation of |WFS| driving functions in the temporal domain. It is one of the
main advantages of |WFS| in comparison to most of the |NFC-HOA|, |SDM| solutions
discussed above.

.. _sec-driving-functions-wfs-plane-wave:

Plane Wave
~~~~~~~~~~

.. plot::
    :context: close-figs

    nk = 0, -1, 0  # direction of plane wave
    omega = 2 * np.pi * 1000  # frequency
    xref = 0, 0, 0  # 2.5D reference point
    x0, n0, a0 = sfs.array.circular(200, 1.5)
    grid = sfs.util.xyz_grid([-1.75, 1.75], [-1.75, 1.75], 0, spacing=0.02)
    d = sfs.mono.drivingfunction.wfs_25d_plane(omega, x0, n0, nk, xref)
    a = sfs.mono.drivingfunction.source_selection_plane(n0, nk)
    twin = sfs.tapering.tukey(a,.3)
    p = sfs.mono.synthesized.generic(omega, x0, n0, d * twin * a0 , grid,
        source=sfs.mono.source.point)
    normalization = 0.5
    sfs.plot.soundfield(normalization * p, grid);
    sfs.plot.secondarysource_2d(x0, n0, grid)

.. _fig-wfs-25d-plane-wave:

.. figure:: img/placeholder.png
    :align: center

    Sound pressure for a monochromatic plane wave synthesized with 2.5D
    |WFS| :eq:`D.wfs.ps.2.5D`.  Parameters: :math:`\n_k = (0, -1, 0)`,
    :math:`\xref = (0, 0, 0)`, :math:`f = 1` kHz.

By inserting the source model of a plane wave :eq:`S.pw` into :eq:`D_wfs`
and :eq:`D25D_wfs` it follows

.. math::
    :label: D.wfs.pw

    D(\x_0,\w) = 2 w(\x_0) A(\w)
        \i\wc  \scalarprod{\n_k}{\n_{\x_0}}
        \e{-\i\wc  \scalarprod{\n_k}{\x_0}},

.. math::
    :label: D.wfs.pw.2.5D

    D_\text{2.5D}(\x_0,\w) = 2 w(\x_0) A(\w)
        \sqrt{2\pi|\xref-x_0|}
        \sqrt{\i\wc } \scalarprod{\n_k}{\n_{\x_0}}
        \e{-\i\wc  \scalarprod{\n_k}{\x_0}}.

Transferred to the temporal domain via an inverse Fourier transform :eq:`ifft`,
it follows

.. math::
    :label: d.wfs.pw

    d(\x_0,t) = 2 a(t) * h(t) * w(\x_0) \scalarprod{\n_k}{\n_{\x_0}}
        \dirac{t - \frac{\scalarprod{\n_k}{\x_0}}{c}},

.. math::
    :label: d.wfs.pw.2.5D

    d_\text{2.5D}(\x_0,t) = 2 a(t) * h_\text{2.5D}(t) * w(\x_0)
        \sqrt{2\pi|\xref-x_0|} \scalarprod{\n_k}{\n_{\x_0}}
        \dirac{t - \frac{\scalarprod{\n_k}{\x_0}}{c}},

where

.. math::
    :label: h.wfs

    h(t) = \mathcal{F}^{-1}\left\{\i\wc \right\},

and

.. math::
    :label: h.wfs.2.5D

    h_\text{2.5D}(t) = \mathcal{F}^{-1}\left\{
        \sqrt{\i\wc }\right\}

denote the so called pre-equalization filters in |WFS|.

The window function :math:`w(\x_0)` for a plane wave as source model can be
calculated after [Spors2008]_

.. math::
    :label: wfs.pw.selection

    w(\x_0) = 
        \begin{cases}
            1 & \scalarprod{\n_k}{\n_{\x_0}} > 0 \\
            0 & \text{else}
        \end{cases}

.. _sec-driving-functions-wfs-point-source:

Point Source
~~~~~~~~~~~~

.. plot::
    :context: close-figs

    xs = 0, 2.5, 0  # position of source
    omega = 2 * np.pi * 1000  # frequency
    xref = 0, 0, 0  # 2.5D reference point
    x0, n0, a0 = sfs.array.circular(200, 1.5)
    grid = sfs.util.xyz_grid([-1.75, 1.75], [-1.75, 1.75], 0, spacing=0.02)
    d = sfs.mono.drivingfunction.wfs_25d_point(omega, x0, n0, xs, xref)
    a = sfs.mono.drivingfunction.source_selection_point(n0, x0, xs)
    twin = sfs.tapering.tukey(a,.3)
    p = sfs.mono.synthesized.generic(omega, x0, n0, d * twin * a0 , grid,
        source=sfs.mono.source.point)
    normalization = 1.3
    sfs.plot.soundfield(normalization * p, grid);
    sfs.plot.secondarysource_2d(x0, n0, grid)

.. _fig-wfs-25d-point-source:

.. figure:: img/placeholder.png
    :align: center

    Sound pressure for a monochromatic point source synthesized with 2.5D
    |WFS| :eq:`D.wfs.ps.2.5D`.  Parameters: :math:`\xs = (0, 2.5, 0)` m,
    :math:`\xref = (0, 0, 0)`, :math:`f = 1` kHz.

By inserting the source model for a point source :eq:`S.ps` into :eq:`D_wfs`
it follows

.. math::
    :label: D.wfs.ps.woapprox

    D(\x_0,\w) =
        \frac{1}{2\pi} A(\w) w(\x_0) \i\wc
        \left(1 + \frac{1}{\i\wc|\x_0-\xs|} \right)
        \frac{\scalarprod{\x_0-\xs}{\n_{\x_0}}}{|\x_0-\xs|^2}
        \e{-\i\wc |\x_0-\xs|}.

Under the assumption of :math:`\wc |\x_0-\xs| \gg 1`,
:eq:`D.wfs.ps.woapprox` can be approximated by [Schultz2016]_, eq. (2.118)

.. math::
    :label: D.wfs.ps

    D(\x_0,\w) = \frac{1}{2\pi} A(\w) w(\x_0) \i\wc
        \frac{\scalarprod{\x_0-\xs}{\n_{\x_0}}}{|\x_0-\xs|^2}
        \e{-\i\wc |\x_0-\xs|}.

It has the advantage that its temporal domain version could again be implemented
as a simple weighting- and delaying-mechanism.

To reach at 2.5D for a point source, we will start in 3D and apply stationary
phase approximations instead of directly using :eq:`D25D_wfs` -- see discussion
after [Schultz2016]_, (2.146). Under the assumption of :math:`\frac{\omega}{c}
(|\x_0-\xs| + |\x-\x_0|) \gg 1` it then follows [Schultz2016]_, eq. (2.137),
[Start1997]_, eq. (3.10, 3.11)

.. math::
    :label: D.wfs.ps.2.5D

    D_\text{2.5D}(\x_0,\w) =
        \frac{1}{\sqrt{2\pi}} A(\w) w(\x_0) \sqrt{\i\wc}
        \sqrt{\frac{|\xref-\x_0|}{|\xref-\x_0|+|\x_0-\xs|}}
        \frac{\scalarprod{\x_0-\xs}{\n_{\x_0}}}{|\x_0-\xs|^{\frac{3}{2}}}
        \e{-\i\wc |\x_0-\xs|},

whereby :math:`\xref` is a reference point at which the synthesis is correct.
A second stationary phase approximation can be applied to reach at
[Schultz2016]_, eq. (2.131, 2.141), [Start1997]_, eq. (3.16, 3.17)

.. math::
    :label: D.wfs.ps.2.5D.refline

    D_\text{2.5D}(\x_0,\w) =
        \frac{1}{\sqrt{2\pi}} A(\w) w(\x_0) \sqrt{\i\wc}
        \sqrt{\frac{d_\text{ref}}{d_\text{ref}+d_\text{s}}}
        \frac{\scalarprod{\x_0-\xs}{\n_{\x_0}}}{|\x_0-\xs|^{\frac{3}{2}}}
        \e{-\i\wc |\x_0-\xs|},

which is the traditional formulation of a point source in |WFS| as given by eq.
(2.27) in [Verheijen1997]_ [#F6]_. Now :math:`d_\text{ref}` is the distance of a
line parallel to the secondary source distribution and :math:`d_\text{s}` the
shortest possible distance from the point source to the linear secondary source
distribution.

The default |WFS| driving functions for a point source in the SFS Toolbox are
:eq:`D.wfs.ps` and :eq:`D.wfs.ps.2.5D`.  Transferring both to the temporal
domain via an inverse Fourier transform :eq:`ifft` it follows

.. math::
    :label: d.wfs.ps

    d(\x_0,t) = \frac{1}{2{\pi}} a(t) * h(t) * w(\x_0)
        \frac{\scalarprod{\x_0-\xs}{\n_{\x_0}}}{|\x_0-\xs|^2}
        \dirac{t-\frac{|\x_0-\xs|}{c}},

.. math::
    :label: d.wfs.ps.2.5D

    \begin{aligned}
        d_\text{2.5D}(\x_0,t) =&
            \frac{1}{\sqrt{2\pi}} a(t) * h_\text{2.5D}(t) * w(\x_0)
            \sqrt{\frac{|\xref-\x_0|}{|\x_0-\xs|+|\xref-\x_0|}} \\
            &\cdot \frac{\scalarprod{\x_0-\xs}{\n_{\x_0}}}{|\x_0-\xs|^{\frac{3}{2}}}
            \dirac{t-\frac{|\x_0-\xs|}{c}}, \\
    \end{aligned}

.. math::
    :label: d.wfs.ps.2.5D.refline

    d_\text{2.5D}(\x_0,t) =
        \frac{1}{\sqrt{2\pi}} a(t) * h_\text{2.5D}(t) * w(\x_0)
        \sqrt{\frac{d_\text{ref}}{d_\text{ref}+d_\text{s}}}
        \cdot \frac{\scalarprod{\x_0-\xs}{\n_{\x_0}}}{|\x_0-\xs|^{\frac{3}{2}}}
        \dirac{t-\frac{|\x_0-\xs|}{c}}.

The window function :math:`w(\x_0)` for a point source as source model can be
calculated after [Spors2008]_ as

.. math::
    :label: wfs.ps.selection

    w(\x_0) = 
        \begin{cases}
            1 & \scalarprod{\x_0-\xs}{\n_{\x_0}} > 0 \\
            0 & \text{else}
        \end{cases}

.. _sec-driving-functions-wfs-line-source:

Line Source
~~~~~~~~~~~

.. plot::
    :context: close-figs

    xs = 0, 2.5, 0  # position of source
    omega = 2 * np.pi * 1000  # frequency
    x0, n0, a0 = sfs.array.circular(200, 1.5)
    grid = sfs.util.xyz_grid([-1.75, 1.75], [-1.75, 1.75], 0, spacing=0.02)
    d = sfs.mono.drivingfunction.wfs_2d_line(omega, x0, n0, xs)
    a = sfs.mono.drivingfunction.source_selection_line(n0, x0, xs)
    twin = sfs.tapering.tukey(a,.3)
    p = sfs.mono.synthesized.generic(omega, x0, n0, d * twin * a0 , grid,
        source=sfs.mono.source.point)
    normalization = 7
    sfs.plot.soundfield(normalization * p, grid);
    sfs.plot.secondarysource_2d(x0, n0, grid)

.. _fig-wfs-25d-line-source:

.. figure:: img/placeholder.png
    :align: center

    Sound pressure for a monochromatic line source synthesized with 2D
    |WFS| :eq:`D.wfs.ls`.  Parameters: :math:`\xs = (0, 2.5, 0)` m,
    :math:`\xref = (0, 0, 0)`, :math:`f = 1` kHz.

For a line source its orientation :math:`\n_\text{s}` has an influence on the synthesized sound field as well.
Let :math:`|\vec{v}|` be the distance between :math:`\x_0` and the line source with

.. math::
    :label: v.ls

    \vec{v} = \x_0-\xs - \scalarprod{\x_0-\xs}{\n_\text{s}} \n_\text{s},

where :math:`|\n_\text{s}| = 1`. For a 2D or 2.5D secondary source setup and
a line source orientation perpendicular to the plane where the
secondary sources are located this automatically simplifies to :math:`\vec{v} =
\x_0 - \xs`.

By inserting the source model for a line source :eq:`S.ls` into :eq:`D_wfs`
and :eq:`D25D_wfs` and calculating the derivate of the Hankel function after eq.
(9.1.20) in [AbramowitzStegun1972]_ it follows

.. math::
    :label: D.wfs.ls

    D(\x_0,\w) = -\frac{1}{2}A(\w) w(\x_0) \i\wc
        \frac{\scalarprod{\vec{v}}{\n_{\x_0}}}{|\vec{v}|}
        \Hankel{2}{1}{\wc |\vec{v}|},

.. math::
    :label: D.wfs.ls.2.5D

    D_\text{2.5D}(\x_0,\w) =
        -\frac{1}{2}g_0 A(\w) w(\x_0) \sqrt{\i\wc}
        \frac{\scalarprod{\vec{v}}{\n_{\x_0}}}{|\vec{v}|}
        \Hankel{2}{1}{\wc |\vec{v}|}.


Applying :math:`\Hankel{2}{1}{\zeta} \approx -\sqrt{\frac{2}{\pi\i}\zeta}
\e{-\i\zeta}` for :math:`z\gg1` after [Williams1999]_, eq. (4.23) and
transferred to the temporal domain via an inverse Fourier transform :eq:`ifft`
it follows

.. math::
    :label: d.wfs.ls

    d(\x_0,t) = \sqrt{\frac{1}{2\pi}} a(t) * h(t) * w(\x0)
        \frac{\scalarprod{\vec{v}}{\n_{\x_0}}}{|\vec{v}|^{\frac{3}{2}}}
        \dirac{t-\frac{|\vec{v}|}{c}},

.. math::
    :label: d.wfs.ls.2.5D

    d_\text{2.5D}(\x_0,t) =
        g_0 \sqrt{\frac{1}{2\pi}} a(t) *
        {\mathcal{F}^{-1}\left\{\sqrt{\frac{c}
        {\i\w}}\right\}} * w(\x0)
        \frac{\scalarprod{\vec{v}}{\n_{\x_0}}}{|\vec{v}|^{\frac{3}{2}}}
        \dirac{t-\frac{|\vec{v}|}{c}},

The window function :math:`w(\x_0)` for a line source as source model can be
calculated after [Spors2008]_ as

.. math::
    :label: wfs.ls.selection

    w(\x_0) = 
        \begin{cases}
            1 & \scalarprod{\vec{v}}{\n_{\x_0}} > 0 \\
            0 & \text{else}
        \end{cases}

.. _sec-driving-functions-wfs-focused-source:

Focused Source
~~~~~~~~~~~~~~

.. plot::
    :context: close-figs

    xs = 0, 0.5, 0  # position of source
    ns = 0, -1, 0  # direction of source
    omega = 2 * np.pi * 1000  # frequency
    xref = 0, 0, 0  # 2.5D reference point
    x0, n0, a0 = sfs.array.circular(200, 1.5)
    grid = sfs.util.xyz_grid([-1.75, 1.75], [-1.75, 1.75], 0, spacing=0.02)
    d = sfs.mono.drivingfunction.wfs_25d_focused(omega, x0, n0, xs, xref)
    a = sfs.mono.drivingfunction.source_selection_focused(ns, x0, xs)
    twin = sfs.tapering.tukey(a,.3)
    p = sfs.mono.synthesized.generic(omega, x0, n0, d * twin * a0 , grid,
        source=sfs.mono.source.point)
    normalization = 1
    sfs.plot.soundfield(normalization * p, grid);
    sfs.plot.secondarysource_2d(x0, n0, grid)

.. _fig-wfs-25d-focused-source:

.. figure:: img/placeholder.png
    :align: center

    Sound pressure for a monochromatic focused source synthesized with 2.5D
    |WFS| :eq:`D.wfs.fs.2.5D`.  Parameters: :math:`\xs = (0, 0.5, 0)` m,
    :math:`\n_\text{s} = (0, -1, 0)`, :math:`\xref = (0, 0, 0)`, :math:`f = 1`
    kHz.

As mentioned before, focused sources exhibit a field that converges in a focal
point inside the audience area. After passing the focal point, the field becomes
a diverging one as can be seen in :numref:`fig-wfs-25d-focused-source`. In order
to choose the active secondary sources, especially for circular or spherical
geometries, the focused source also needs a direction :math:`\n_\text{s}`.

The driving function for a focused source is given by the time-reversed
versions of the driving function for a point source :eq:`d.wfs.ps` and
:eq:`d.wfs.ps.2.5D` as

.. math::
    :label: D.wfs.fs

    D(\x_0,\w) = \frac{1}{2\pi} A(\w) w(\x_0) \i\wc
        \frac{\scalarprod{\x_0-\xs}{\n_{\x_0}}}{|\x_0-\xs|^2}
        \e{\i\wc |\x_0-\xs|}.

The 2.5D driving functions are given by the time-reversed version of
:eq:`d.wfs.ps.2.5D` for a reference point [Verheijen1997]_, eq. (A.14)

.. math::
    :label: D.wfs.fs.2.5D

    D_\text{2.5D}(\x_0,\w) =
        \frac{1}{\sqrt{2\pi}} A(\w) w(\x_0) \sqrt{\i\wc}
        \sqrt{\frac{|\xref-\x_0|}{| |\x_0-\xs|-|\xref-\x_0| |}}
        \frac{\scalarprod{\x_0-\xs}{\n_{\x_0}}}{|\x_0-\xs|^{\frac{3}{2}}}
        \e{\i\wc |\x_0-\xs|},

and the time reversed version of :eq:`d.wfs.ps.2.5D.refline` for a reference
line, compare [Start1997]_, eq. (3.16)

.. math::
    :label: D.wfs.fs.2.5D.refline

    D_\text{2.5D}(\x_0,\w) =
        \frac{1}{\sqrt{2\pi}} A(\w) w(\x_0) \sqrt{\i\wc}
        \sqrt{\frac{d_\text{ref}}{d_\text{ref}-d_\text{s}}}
        \frac{\scalarprod{\x_0-\xs}{\n_{\x_0}}}{|\x_0-\xs|^{\frac{3}{2}}}
        \e{\i\wc |\x_0-\xs|},

where :math:`d_\text{ref}` is the distance of a line parallel to the secondary
source distribution and :math:`d_\text{s}` the shortest possible distance from
the focused source to the linear secondary source distribution.

Transferred to the temporal domain via an inverse Fourier transform :eq:`ifft` it
follows

.. math::
    :label: d.wfs.fs

    d(\x_0,t) = \frac{1}{2{\pi}} a(t) * h(t) * w(\x_0)
        \frac{\scalarprod{\x_0-\xs}{\n_{\x_0}}}{|\x_0-\xs|^2}
        \dirac{t+\frac{|\x_0-\xs|}{c}},

.. math::
    :label: d.wfs.fs.2.5D

    \begin{aligned}
        d_\text{2.5D}(\x_0,t) =&
            \frac{1}{\sqrt{2\pi}} a(t) * h_\text{2.5D}(t) * w(\x_0)
            \sqrt{\frac{|\xref-\x_0|}{|\x_0-\xs|+|\xref-\x_0|}} \\
            &\cdot \frac{\scalarprod{\x_0-\xs}{\n_{\x_0}}}{|\x_0-\xs|^{\frac{3}{2}}}
            \dirac{t+\frac{|\x_0-\xs|}{c}}, \\
    \end{aligned}

.. math::
    :label: d.wfs.fs.2.5D.refline

    d_\text{2.5D}(\x_0,t) =
        \frac{1}{\sqrt{2\pi}} a(t) * h_\text{2.5D}(t) * w(\x_0)
        \sqrt{\frac{d_\text{ref}}{d_\text{ref}-d_\text{s}}}
        \cdot \frac{\scalarprod{\x_0-\xs}{\n_{\x_0}}}{|\x_0-\xs|^{\frac{3}{2}}}
        \dirac{t+\frac{|\x_0-\xs|}{c}}.

In this document a focused source always refers to the time-reversed version of a
point source, but a focused line source can be defined in the same way starting
from :eq:`D.wfs.ls`

.. math::
    :label: D.wfs.fs.ls

    D(\x_0,\w) = -\frac{1}{2}A(\w) w(\x_0) \i\wc 
        \frac{\scalarprod{\x_0-\xs}{\n_{\x_0}}}{|\x_0-\xs|}
        \Hankel{1}{1}{\wc |\x_0-\xs|}.

Transferred to the temporal domain via an inverse Fourier transform :eq:`ifft`
it follows

.. math::
    :label: d.wfs.fs.ls

    d(\x_0,t) = \sqrt{\frac{1}{2\pi}} a(t) * h(t) * w(\x0)
        \frac{\scalarprod{\x_0-\xs}{\n_{\x_0}}}{|\x_0-\xs|^{\frac{3}{2}}}
        \dirac{t+\frac{|\x_0-\xs|}{c}}.

The window function :math:`w(\x_0)` for a focused source can be calculated as

.. math::
    :label: wfs.fs.selection

    w(\x_0) = 
        \begin{cases}
            1 & \scalarprod{\n_\text{s}}{\xs-\x_0} > 0 \\
            0 & \text{else}
        \end{cases}

.. _sec-driving-functions-local-sfs:

Driving functions for LSFS
--------------------------

The reproduction accuracy of |WFS| is limited due to practical aspects.  For the
audible frequency range the desired sound field can not be synthesized
aliasing-free over an extended listening area, which is surrounded by a discrete
ensemble of individually driven loudspeakers.  However, it is suitable for
certain applications to increase reproduction accuracy inside a smaller (local)
listening region while stronger artifacts outside are permitted. This approach
is termed |LSFS| in general.

The implemented Local Wave Field Synthesis method utilizes focused sources as a
distribution of virtual loudspeakers which are placed more densely around the
local listening area. These virtual loudspeakers can be driven by conventional
SFS techniques, like e.g. |WFS| or |NFC-HOA|. The results are similar to
band-limited |NFC-HOA|, with the difference that the form and position of the
enhanced area can freely be chosen within the listening area.

The set of focused sources is treated as a virtual loudspeaker distribution and
their positions :math:`{\x_\text{fs}}` are subsumed under
:math:`\mathcal{X}_{\mathrm{fs}}`. Therefore, each focused source is driven
individually by :math:`D_\text{l}({\x_\text{fs}}, \w)`, which in principle
can be any driving function for real loudspeakers mentioned in previous
sections. At the moment however, only |WFS| and |NFC-HOA| driving functions are
supported. The resulting driving function for a loudspeaker located at
:math:`\x_0` reads

.. math::
    :label: D.localwfs

    D(\x_0,\w) = \sum_{{\x_\text{fs}}\in \mathcal{X}_{\mathrm{fs}}}
        D_{\mathrm l}({\x_\text{fs}}, \w)
        D_{\mathrm{fs}}(\x_0,{\x_\text{fs}},\w),

which is superposition of the driving function
:math:`D_{\mathrm{fs}}(\x_0,{\x_\text{fs}},\w)` reproducing a single focused
source at :math:`{\x_\text{fs}}` weighted by :math:`D_\text{l}({\x_\text{fs}},
\w)`.  Former is derived by replacing :math:`\xs` with
:math:`{\x_\text{fs}}` in the |WFS| driving functions and for focused sources.
This yields

.. math::
    :label: D.localwfs.fs

    D_{\mathrm{fs}}(\x_0,{\x_\text{fs}},\w) =
        \frac{1}{2\pi} A(\w) w(\x_0) \i\wc 
        \frac{\scalarprod{\x_0-\x_\text{fs}}{\n_{\x_0}}}
        {|\x_0-{\x_\text{fs}}|^{\frac{3}{2}}}
        \e{\i\wc |\x_0-{\x_\text{fs}}|}

and

.. math::
    :label: D.localwfs.fs.2.5D

    D_{\mathrm{fs,2.5D}}(\x_0,{\x_\text{fs}},\w) = 
       \frac{g_0}{2\pi} A(\w) w(\x_0) \sqrt{\i\wc }
       \frac{\scalarprod{\x_0-\xs}{\n_{\x_0}}}{|\x_0-\xs|^{\frac{3}{2}}}
       \e{\i\wc |\x_0-\xs|}

for the 2.5D case. For the temporal domain, inverse Fourier transform yields the
driving signals

.. math::
    :label: d.localwfs

    d(\x_0,t) = \sum_{{\x_\text{fs}}\in \mathcal{X}_{\mathrm{fs}}} 
        d_{\mathrm l}({\x_\text{fs}}, t) * 
        d_{\mathrm{fs}}(\x_0,{\x_\text{fs}}, t),

while :math:`d_{\mathrm{fs}}(\x_0,{\x_\text{fs}}, t)` is derived analogously to
from or . At the moment :math:`d_{\mathrm l}({\x_\text{fs}}, t)` does only
support driving functions from |WFS|.


.. _sec-footnotes:

Footnotes
---------

.. [#F1]
    Note that :math:`\sin\theta` is used here instead of :math:`\cos\theta` due
    to the use of another coordinate system, compare Figure 2.1 from Gumerov and
    Duraiswami and :numref:`fig-coordinate-system`.

.. [#F2]
    Note the :math:`\frac{1}{2\pi}` term is wrong in [Ahrens2012]_, eq. (3.21)
    and eq. (5.7) and omitted here, compare the `errata
    <http://www.soundfieldsynthesis.org/errata/>`_ and [SchultzSpors2014]_, eq.
    (24).

.. [#F3]
    Compare the assumptions made before (15) in [SporsZotter2013]_, which lead
    to the derivation of the same window function in a more explicit way.

.. [#F4]
    The solution mentioned by [LaxFeshbach1947]_ assumes that the listener is
    far away from the radiator and that the radiator is a physical source not a
    notional one as the secondary sources. In this case the selection criterion
    has to be chosen more carefully, incorporating the exact position of the
    listener and the virtual source. See also the `related discussion
    <https://github.com/sfstoolbox/sfs-documentation/issues/8>`_.

.. [#F5]
    Note that Williams defines the Fourier transform with transposed signs as
    :math:`F(\w) = \int f(t) \e{\i\w t}`. This leads also to changed signs in
    his definitions of the Green’s functions and field expansions.

.. [#F6]
    Whereby :math:`r` corresponds to :math:`|\x_0-\xs|` and :math:`\cos\varphi`
    to :math:`\frac{\scalarprod{\x_0-\xs}{\n_{\x_0}}}{|\x_0-\xs|}`.


.. _sec-references:

References
----------

.. [AbramowitzStegun1972]
    Abramowitz, M. and Stegun, I. A.,
    *Handbook of Mathematical Functions*.
    (National Bureau of Standards, Washington, 1972).

.. [Ahrens2012]
    Ahrens, J.,
    *Analytic Methods of Sound Field Synthesis*.
    (Springer, New York, 2012).

.. [AhrensSpors2009a]
    Ahrens, J. and Spors, S.,
    “On the Secondary Source Type Mismatch in Wave Field Synthesis Employing Circular Distributions of Loudspeakers,”
    in *127th Conv. Audio Eng. Soc.*, New York, NY (2009), preprint 7952,
    `url <http://www.aes.org/e-lib/browse.cfm?elib=15146>`__.

.. [AhrensSpors2009b]
    Ahrens, J. and Spors, S.,
    “Spatial encoding and decoding of focused virtual sound sources,”
    in *1st Ambisonics S.*, Graz, Austria (2009),
    `url <http://www.deutsche-telekom-laboratories.de/~sporssas/.../AhrensSpors_AmbiSym09.pdf>`__.

.. [AhrensSpors2010]
    Ahrens, J. and Spors, S.,
    “Sound Field Reproduction Using Planar and Linear Arrays of Loudspeakers,”
    IEEE Trans. Audio Speech Lang. Processing **18**, 2038–2050 (2010),
    `url <http://dx.doi.org/10.1109/TASL.2010.2041106>`__.

.. [ArfkenWeber2005]
    Arfken, G. B. and Weber, H. J.,
    *Mathematical Methods for Physicists*.
    (Elsevier, Amsterdam, 2015).

.. [Avni2013]
    Avni, A., Ahrens, J., Geier, M., Spors, S., Wierstorf, H., and Rafaely, B.,
    “Spatial perception of sound fields recorded by spherical microphone arrays with varying spatial resolution,”
    J. Acoust. Soc. Am. **133**, 2711–2721 (2013),
    `url <http://dx.doi.org/10.1121/1.4795780>`__.

.. [Bracewell2000]
    Bracewell, R. N.,
    *The Fourier Transform and its Applications*.
    (McGraw Hill, Boston, 2000).

.. [ColtonKress1983]
    Colton, D. and Kress, R.,
    *Integral Equation Methods in Scattering Theory*.
    (Wiley, New York, 2000).

.. [ColtonKress1998]
    Colton, D. and Kress, R.,
    *Inverse Acoustic and Electromagnetic Scattering Theory*,
    (Springer, New York, 1998).

.. [Fazi2010]
    Fazi, F. M.,
    *Sound Field Reproduction*,
    Ph.D. dissertation, University of Southampton, Southampton, UK, 2010,
    `url <http://eprints.soton.ac.uk/id/eprint/158639>`__.

.. [FaziNelson2013]
    Fazi, F. M. and Nelson, P. A.,
    “Sound field reproduction as an equivalent acoustical scattering problem,”
    J. Acoust. Soc. Am. **134**, 3721–3729 (2013),
    `url <http://dx.doi.org/10.1121/1.4824343>`__.

.. [GumerovDuraiswami2004]
    Gumerov, N. A. and Duraiswami, R.,
    *Fast Multipole Methods for the Helmholtz Equation in Three Dimensions*.
    (Elsevier, Amsterdam, 2004).

.. [Hahn2015]
    Hahn, N. and Spors S.,
    “Sound Field Synthesis of Virtual Cylindrical Waves Using Circular and Spherical Loudspeaker Arrays,”
    in *138th Conv. Audio Eng. Soc.*, Warsaw, Poland (2015), preprint 9324,
    `url <http://www.aes.org/e-lib/browse.cfm?elib=17748>`__.

.. [Herrin2003]
    Herrin, D. W., Martinus, F., Wu, T. W., and Seybert, A. F.,
    “A New Look at the High Frequency Boundary Element and Rayleigh Integral Approximations,” SAE Technical Paper,
    doi:10.4271/2003-01-1451 (2003),
    `url <http://dx.doi.org/10.4271/2003-01-1451>`__.

.. [LaxFeshbach1947]
    Lax, M. and Feshbach, H.,
    “On the Radiation Problem at High Frequencies,”
    J. Acoust. Soc. Am. **19**, 682–690 (1947),
    `url <http://dx.doi.org/10.1121/1.1916538>`__.

.. [MorseFeshbach1981]
    Morse, P. M. and Feshbach, H.,
    *Methods of Theoretical Physics*.
    (Feshbach Publishing, Minneapolis, 1981).

.. [SchultzSpors2014]
    Schultz, F. and Spors, S.,
    “Comparing Approaches to the Spherical and Planar Single Layer Potentials for Interior Sound Field Synthesis,”
    Acta Acust. **100**, 900–911 (2014),
    `url <http://dx.doi.org/10.3813/AAA.918769>`__.

.. [Schultz2016]
    Schultz, F.,
    *Sound Field Synthesis for Line Source Array Applications in Large-Scale Sound Reinforcement*,
    doctoral thesis, Universität Rostock, Rostock, Germany, 2016,
    `url <http://rosdok.uni-rostock.de/resolve/urn/urn:nbn:de:gbv:28-diss2016-0078-1>`__.

.. [SporsAhrens2010]
    Spors, S. and Ahrens, J.,
    “Reproduction of Focused Sources by the Spectral Division Method,”
    in *4th IEEE ISCCSP*, Limassol, Cyprus (2010), doi:10.1109/ISCCSP.2010.5463335,
    `url <http://dx.doi.org/10.1109/ISCCSP.2010.5463335>`__.

.. [SporsZotter2013]
    Spors, S. and Zotter, F.,
    “Spatial Sound Synthesis with Loudspeakers,”
    in *Cutting Edge in Spatial Audio, EAA Winter School*, Merano, Italy (2013), pp. 32–37,
    `url <https://fedora.kug.ac.at/fedora/get/o:6537/bdef:Content/get>`__.

.. [Spors2011]
    Spors, S., Kuscher, V., and Ahrens, J.,
    “Efficient realization of model-based rendering for 2.5-dimensional near-field compensated higher order Ambisonics,”
    in *IEEE WASPAA*, New Paltz, NY (2011), pp. 61–64,
    `url <http://dx.doi.org/10.1109/ASPAA.2011.6082325>`__.

.. [Spors2008]
    Spors, S., Rabenstein, R., and Ahrens, J.,
    “The Theory of Wave Field Synthesis Revisited,”
    in *124th Conv. Audio Eng. Soc.*, Amsterdam, Netherlands (2008), preprint 7358,
    `url <http://www.aes.org/e-lib/browse.cfm?elib=14488>`__.

.. [Start1997]
    Start, E. W.,
    *Direct Sound Enhancement by Wave Field Synthesis*,
    Ph.D. dissertation, Technische Universiteit Delft, Delft, Netherlands, 1997.

.. [Verheijen1997]
    Verheijen, E.,
    *Sound Reproduction by Wave Field Synthesis*,
    Ph.D. dissertation, Technische Universiteit Delft, Delft, Netherlands, 1997.

.. [Wierstorf2014]
    Wierstorf, H.,
    *Perceptual Assessment of Sound Field Synthesis*,
    Ph.D. dissertation, Technische Universität Berlin, Berlin, Germany, 2014,
    `url <http://dx.doi.org/10.14279/depositonce-4310>`__.

.. [Williams1999]
    Williams, E. G.,
    *Fourier Acoustics*.
    (Academic Press, San Diego, 1999).

.. [Yon2003]
    Yon, S., Tanter, M., and Fink, M.,
    “Sound focusing in rooms: The time-reversal approach,”
    J. Acoust. Soc. Am. **113**, 1533–1243 (2003),
    `url <http://dx.doi.org/10.1121/1.1543587>`__.

.. [ZotterSpors2013]
    Zotter, F. and Spors, S.,
    “Is sound field control determined at all frequencies? How is it related to numerical acoustics?”
    in *52th Int. Conf. Audio Eng. Soc.*, Guildford, UK (2013), preprint 1.3,
    `url <http://www.aes.org/e-lib/browse.cfm?elib=16921>`__.


.. vim: filetype=rst spell:
