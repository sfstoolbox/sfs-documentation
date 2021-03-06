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
    :nofigs:

    npw = 0, -1, 0  # direction of plane wave
    omega = 2 * np.pi * 1000  # frequency
    xref = 0, 0, 0  # 2.5D reference point
    array = sfs.array.circular(200, 1.5)
    grid = sfs.util.xyz_grid([-1.75, 1.75], [-1.75, 1.75], 0, spacing=0.02)
    d, selection, secondary_source = \
        sfs.fd.wfs.plane_25d(omega, array.x, array.n, npw, xref=xref)
    twin = sfs.tapering.tukey(selection, alpha=.3)
    p = sfs.fd.synthesize(d, twin, array, secondary_source, grid=grid)
    sfs.plot2d.amplitude(p, grid, xnorm=xref)
    sfs.plot2d.secondary_sources(array.x, array.n, grid=grid)

.. plot::
    :context:
    :include-source: false
    :nofigs:

    save_fig('wfs-25d-plane-wave')

.. _fig-wfs-25d-plane-wave:

.. figure:: wfs-25d-plane-wave.*
    :align: center

    Sound pressure for a monochromatic plane wave synthesized with 2.5D |WFS|
    :eq:`fd-wfs-point-25d`.  Parameters: :math:`\n_k =
    (0, -1, 0)`, :math:`\xref = (0, 0, 0)`, :math:`f = 1` kHz.

By inserting the source model of a plane
wave :eq:`fd-plane` into :eq:`fd-drivingfunction-wfs`
and :eq:`fd-drivingfunction-wfs-25d` it follows

.. math::
    :label: fd-wfs-plane

    D(\x_0,\w) = 2 w(\x_0) A(\w)
        \i\wc  \scalarprod{\n_k}{\n_{\x_0}}
        \e{-\i\wc  \scalarprod{\n_k}{\x_0}},

.. math::
    :label: fd-wfs-plane-25d

    D_\text{2.5D}(\x_0,\w) = 2 w(\x_0) A(\w)
        \sqrt{2\pi|\xref-x_0|}
        \sqrt{\i\wc } \scalarprod{\n_k}{\n_{\x_0}}
        \e{-\i\wc  \scalarprod{\n_k}{\x_0}}.

Transferred to the temporal domain via an inverse Fourier transform :eq:`ifft`,
it follows

.. math::
    :label: td-wfs-plane

    d(\x_0,t) = 2 a(t) * h(t) * w(\x_0) \scalarprod{\n_k}{\n_{\x_0}}
        \dirac{t - \frac{\scalarprod{\n_k}{\x_0}}{c}},

.. math::
    :label: td-wfs-plane-25d

    \begin{aligned}
        d_\text{2.5D}(\x_0,t) =& 2 a(t) * h_\text{2.5D}(t) * w(\x_0)
            \sqrt{2\pi|\xref-x_0|} \\
            &\cdot \scalarprod{\n_k}{\n_{\x_0}}
            \dirac{t - \frac{\scalarprod{\n_k}{\x_0}}{c}},
    \end{aligned}

where

.. math::
    :label: td-wfs-prefilter

    h(t) = \mathcal{F}^{-1}\left\{\i\wc \right\},

and

.. math::
    :label: td-wfs-prefilter-25d

    h_\text{2.5D}(t) = \mathcal{F}^{-1}\left\{
        \sqrt{\i\wc }\right\}

denote the so called pre-equalization filters in |WFS|.

The window function :math:`w(\x_0)` for a plane wave as source model can be
calculated after :cite:`Spors2008` as

.. math::
    :label: wfs-secondary-source-selection-plane

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
    :nofigs:

    xs = 0, 2.5, 0  # position of source
    omega = 2 * np.pi * 1000  # frequency
    xref = 0, 0, 0  # 2.5D reference point
    array = sfs.array.circular(200, 1.5)
    grid = sfs.util.xyz_grid([-1.75, 1.75], [-1.75, 1.75], 0, spacing=0.02)
    d, selection, secondary_source = \
        sfs.fd.wfs.point_25d(omega, array.x, array.n, xs, xref=xref)
    twin = sfs.tapering.tukey(selection, alpha=.3)
    p = sfs.fd.synthesize(d, twin, array, secondary_source, grid=grid)
    normalization = 4 * np.pi
    sfs.plot2d.amplitude(normalization * p, grid)
    sfs.plot2d.secondary_sources(array.x, array.n, grid=grid)

.. plot::
    :context:
    :include-source: false
    :nofigs:

    save_fig('wfs-25d-point-source')

.. _fig-wfs-25d-point-source:

.. figure:: wfs-25d-point-source.*
    :align: center

    Sound pressure for a monochromatic point source synthesized with 2.5D |WFS|
    :eq:`fd-wfs-point-25d`.  Parameters:
    :math:`\xs = (0, 2.5, 0)` m, :math:`\xref = (0, 0, 0)`, :math:`f = 1` kHz.

By inserting the source model for a point
source :eq:`fd-point` into :eq:`fd-drivingfunction-wfs`
it follows

.. math::
    :label: fd-wfs-point-woapprox

    D(\x_0,\w) =
        \frac{1}{2\pi} A(\w) w(\x_0) \i\wc
        \left(1 + \frac{1}{\i\wc|\x_0-\xs|} \right)
        \frac{\scalarprod{\x_0-\xs}{\n_{\x_0}}}{|\x_0-\xs|^2}
        \e{-\i\wc |\x_0-\xs|}.

Under the assumption of :math:`\wc |\x_0-\xs| \gg 1`,
:eq:`fd-wfs-point-woapprox` can be approximated by
:cite:`Schultz2016`, eq. (2.118)

.. math::
    :label: fd-wfs-point

    D(\x_0,\w) = \frac{1}{2\pi} A(\w) w(\x_0) \i\wc
        \frac{\scalarprod{\x_0-\xs}{\n_{\x_0}}}{|\x_0-\xs|^2}
        \e{-\i\wc |\x_0-\xs|}.

It has the advantage that its temporal domain version could again be implemented
as a simple weighting- and delaying-mechanism.

To reach at 2.5D for a point source, we will start in 3D and apply stationary
phase approximations instead of directly using
:eq:`fd-drivingfunction-wfs-25d` -- see discussion after :cite:`Schultz2016`,
(2.146). Under the assumption of :math:`\frac{\omega}{c} (|\x_0-\xs| +
|\x-\x_0|) \gg 1` it then follows :cite:`Schultz2016`, eq.  (2.137),
:cite:`Start1997`, eq. (3.10, 3.11)

.. math::
    :label: fd-wfs-point-25d

    \begin{aligned}
        D_\text{2.5D}(\x_0,\w) =&
            \frac{1}{\sqrt{2\pi}} A(\w) w(\x_0) \sqrt{\i\wc}
            \sqrt{\frac{|\xref-\x_0|}{|\xref-\x_0|+|\x_0-\xs|}} \\
            &\cdot \frac{\scalarprod{\x_0-\xs}{\n_{\x_0}}}
            {|\x_0-\xs|^{\frac{3}{2}}}
            \e{-\i\wc |\x_0-\xs|},
    \end{aligned}

whereby :math:`\xref` is a reference point at which the synthesis is correct.
A second stationary phase approximation can be applied to reach at
:cite:`Schultz2016`, eq. (2.131, 2.141), :cite:`Start1997`, eq. (3.16, 3.17)

.. math::
    :label: fd-wfs-point-25d-refline

    \begin{aligned}
        D_\text{2.5D}(\x_0,\w) =&
            \frac{1}{\sqrt{2\pi}} A(\w) w(\x_0) \sqrt{\i\wc}
            \sqrt{\frac{d_\text{ref}}{d_\text{ref}+d_\text{s}}} \\
            &\cdot \frac{\scalarprod{\x_0-\xs}{\n_{\x_0}}}
            {|\x_0-\xs|^{\frac{3}{2}}}
            \e{-\i\wc |\x_0-\xs|},
    \end{aligned}

which is the traditional formulation of a point source in |WFS| as given by eq.
(2.27) in :cite:`Verheijen1997` [#F1]_. Now :math:`d_\text{ref}` is the distance
of a line parallel to the secondary source distribution and :math:`d_\text{s}`
the shortest possible distance from the point source to the linear secondary
source distribution.

The default |WFS| driving functions for a point source in the SFS Toolbox are
:eq:`fd-wfs-point` and
:eq:`fd-wfs-point-25d`.  Transferring both to the
temporal domain via an inverse Fourier transform :eq:`ifft` it follows

.. math::
    :label: td-wfs-point

    d(\x_0,t) = \frac{1}{2{\pi}} a(t) * h(t) * w(\x_0)
        \frac{\scalarprod{\x_0-\xs}{\n_{\x_0}}}{|\x_0-\xs|^2}
        \dirac{t-\frac{|\x_0-\xs|}{c}},

.. math::
    :label: td-wfs-point-25d

    \begin{aligned}
        d_\text{2.5D}(\x_0,t) =&
            \frac{1}{\sqrt{2\pi}} a(t) * h_\text{2.5D}(t) * w(\x_0)
            \sqrt{\frac{|\xref-\x_0|}{|\x_0-\xs|+|\xref-\x_0|}} \\
            &\cdot \frac{\scalarprod{\x_0-\xs}{\n_{\x_0}}}
            {|\x_0-\xs|^{\frac{3}{2}}}
            \dirac{t-\frac{|\x_0-\xs|}{c}}, \\
    \end{aligned}

.. math::
    :label: td-wfs-point-25d-refline

    \begin{aligned}
    d_\text{2.5D}(\x_0,t) =&
        \frac{1}{\sqrt{2\pi}} a(t) * h_\text{2.5D}(t) * w(\x_0)
        \sqrt{\frac{d_\text{ref}}{d_\text{ref}+d_\text{s}}} \\
        &\cdot \frac{\scalarprod{\x_0-\xs}{\n_{\x_0}}}
        {|\x_0-\xs|^{\frac{3}{2}}}
        \dirac{t-\frac{|\x_0-\xs|}{c}}.
    \end{aligned}

The window function :math:`w(\x_0)` for a point source as source model can be
calculated after :cite:`Spors2008` as

.. math::
    :label: wfs-secondary-source-selection-point

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
    :nofigs:

    xs = 0, 2.5, 0  # position of source
    omega = 2 * np.pi * 1000  # frequency
    array = sfs.array.circular(200, 1.5)
    grid = sfs.util.xyz_grid([-1.75, 1.75], [-1.75, 1.75], 0, spacing=0.02)
    d, selection, secondary_source = \
        sfs.fd.wfs.line_2d(omega, array.x, array.n, xs)
    twin = sfs.tapering.tukey(selection, alpha=.3)
    p = sfs.fd.synthesize(d, twin, array, secondary_source, grid=grid)
    normalization = (np.sqrt(8 * np.pi * omega / sfs.default.c)
                     * np.exp(1j * np.pi / 4))
    sfs.plot2d.amplitude(normalization * p, grid)
    sfs.plot2d.secondary_sources(array.x, array.n, grid=grid)

.. plot::
    :context:
    :include-source: false
    :nofigs:

    save_fig('wfs-25d-line-source')

.. _fig-wfs-25d-line-source:

.. figure:: wfs-25d-line-source.*
    :align: center

    Sound pressure for a monochromatic line source synthesized with 2D |WFS|
    :eq:`fd-wfs-line`.  Parameters:
    :math:`\xs = (0, 2.5, 0)` m, :math:`\xref = (0, 0, 0)`, :math:`f = 1` kHz.

For a line source its orientation :math:`\n_\text{s}` has an influence on the
synthesized sound field as well.  Let :math:`|\vec{v}|` be the distance between
:math:`\x_0` and the line source with

.. math::
    :label: v-line-source

    \vec{v} = \x_0-\xs - \scalarprod{\x_0-\xs}{\n_\text{s}} \n_\text{s},

where :math:`|\n_\text{s}| = 1`. For a 2D or 2.5D secondary source setup and
a line source orientation perpendicular to the plane where the
secondary sources are located this automatically simplifies to :math:`\vec{v} =
\x_0 - \xs`.

By inserting the source model for a line source :eq:`fd-line`
into :eq:`fd-drivingfunction-wfs` and :eq:`fd-drivingfunction-wfs-25d` and
calculating the derivate of the Hankel function after
`<http://dlmf.nist.gov/10.6.E6>`_ it follows

.. math::
    :label: fd-wfs-line

    D(\x_0,\w) = -\frac{1}{2}A(\w) w(\x_0) \i\wc
        \frac{\scalarprod{\vec{v}}{\n_{\x_0}}}{|\vec{v}|}
        \Hankel{2}{1}{\wc |\vec{v}|},

.. math::
    :label: fd-wfs-line-25d

    D_\text{2.5D}(\x_0,\w) =
        -\frac{1}{2}g_0 A(\w) w(\x_0) \sqrt{\i\wc}
        \frac{\scalarprod{\vec{v}}{\n_{\x_0}}}{|\vec{v}|}
        \Hankel{2}{1}{\wc |\vec{v}|}.


Applying :math:`\Hankel{2}{1}{\zeta} \approx -\sqrt{\frac{2}{\pi\i}\zeta}
\e{-\i\zeta}` for :math:`z\gg1` after :cite:`Williams1999`, eq. (4.23) and
transferred to the temporal domain via an inverse Fourier transform :eq:`ifft`
it follows

.. math::
    :label: td-wfs-line

    d(\x_0,t) = \sqrt{\frac{1}{2\pi}} a(t) * h(t) * w(\x0)
        \frac{\scalarprod{\vec{v}}{\n_{\x_0}}}{|\vec{v}|^{\frac{3}{2}}}
        \dirac{t-\frac{|\vec{v}|}{c}},

.. math::
    :label: td-wfs-line-25d

    d_\text{2.5D}(\x_0,t) =
        g_0 \sqrt{\frac{1}{2\pi}} a(t) *
        {\mathcal{F}^{-1}\left\{\sqrt{\frac{c}
        {\i\w}}\right\}} * w(\x0)
        \frac{\scalarprod{\vec{v}}{\n_{\x_0}}}{|\vec{v}|^{\frac{3}{2}}}
        \dirac{t-\frac{|\vec{v}|}{c}},

The window function :math:`w(\x_0)` for a line source as source model can be
calculated after :cite:`Spors2008` as

.. math::
    :label: wfs-secondary-source-selection-line

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
    :nofigs:

    xs = 0, 0.5, 0  # position of source
    ns = 0, -1, 0  # direction of source
    omega = 2 * np.pi * 1000  # frequency
    xref = 0, 0, 0  # 2.5D reference point
    array= sfs.array.circular(200, 1.5)
    grid = sfs.util.xyz_grid([-1.75, 1.75], [-1.75, 1.75], 0, spacing=0.02)
    d, selection, secondary_source = \
        sfs.fd.wfs.focused_25d(omega, array.x, array.n, xs, ns, xref=xref)
    twin = sfs.tapering.tukey(selection, alpha=.3)
    p = sfs.fd.synthesize(d, twin, array, secondary_source, grid=grid)
    sfs.plot2d.amplitude(p, grid)
    sfs.plot2d.secondary_sources(array.x, array.n, grid=grid)

.. plot::
    :context:
    :include-source: false
    :nofigs:

    save_fig('wfs-25d-focused-source')

.. _fig-wfs-25d-focused-source:

.. figure:: wfs-25d-focused-source.*
    :align: center

    Sound pressure for a monochromatic focused source synthesized with 2.5D
    |WFS| :eq:`fd-wfs-focused-25d`.  Parameters:
    :math:`\xs = (0, 0.5, 0)` m, :math:`\n_\text{s} = (0, -1, 0)`, :math:`\xref
    = (0, 0, 0)`, :math:`f = 1` kHz.

As mentioned before, focused sources exhibit a field that converges in a focal
point inside the audience area. After passing the focal point, the field becomes
a diverging one as can be seen in :numref:`fig-wfs-25d-focused-source`. In order
to choose the active secondary sources, especially for circular or spherical
geometries, the focused source also needs a direction :math:`\n_\text{s}`.

The driving function for a focused source is given by the td-reversed
versions of the driving function for a point source
:eq:`td-wfs-point` and
:eq:`td-wfs-point-25d` as

.. math::
    :label: fd-wfs-focused-3d

    D(\x_0,\w) = \frac{1}{2\pi} A(\w) w(\x_0) \i\wc
        \frac{\scalarprod{\x_0-\xs}{\n_{\x_0}}}{|\x_0-\xs|^2}
        \e{\i\wc |\x_0-\xs|}.

The 2.5D driving functions are given by the td-reversed version of
:eq:`td-wfs-point-25d` for a reference point after
:cite:`Verheijen1997`, eq. (A.14) as

.. math::
    :label: fd-wfs-focused-25d

    \begin{aligned}
        D_\text{2.5D}(\x_0,\w) =&
            \frac{1}{\sqrt{2\pi}} A(\w) w(\x_0) \sqrt{\i\wc}
            \sqrt{\frac{|\xref-\x_0|}{||\x_0-\xs|-|\xref-\x_0||}} \\
            &\cdot \frac{\scalarprod{\x_0-\xs}{\n_{\x_0}}}
                        {|\x_0-\xs|^{\frac{3}{2}}}
            \e{\i\wc |\x_0-\xs|},
    \end{aligned}

and the time reversed version of
:eq:`td-wfs-point-25d-refline` for a reference line,
compare :cite:`Start1997`, eq. (3.16)

.. math::
    :label: fd-wfs-focused-25d-refline

    \begin{aligned}
        D_\text{2.5D}(\x_0,\w) =&
            \frac{1}{\sqrt{2\pi}} A(\w) w(\x_0) \sqrt{\i\wc}
            \sqrt{\frac{d_\text{ref}}{d_\text{ref}-d_\text{s}}} \\
            &\cdot \frac{\scalarprod{\x_0-\xs}{\n_{\x_0}}}
                        {|\x_0-\xs|^{\frac{3}{2}}}
            \e{\i\wc |\x_0-\xs|},
    \end{aligned}

where :math:`d_\text{ref}` is the distance of a line parallel to the secondary
source distribution and :math:`d_\text{s}` the shortest possible distance from
the focused source to the linear secondary source distribution.

Transferred to the temporal domain via an inverse Fourier transform :eq:`ifft`
it follows

.. math::
    :label: td-wfs-focused-3d

    d(\x_0,t) = \frac{1}{2{\pi}} a(t) * h(t) * w(\x_0)
        \frac{\scalarprod{\x_0-\xs}{\n_{\x_0}}}{|\x_0-\xs|^2}
        \dirac{t+\frac{|\x_0-\xs|}{c}},

.. math::
    :label: td-wfs-focused-25d

    \begin{aligned}
        d_\text{2.5D}(\x_0,t) =&
            \frac{1}{\sqrt{2\pi}} a(t) * h_\text{2.5D}(t) * w(\x_0)
            \sqrt{\frac{|\xref-\x_0|}{||\x_0-\xs|-|\xref-\x_0||}} \\
            &\cdot \frac{\scalarprod{\x_0-\xs}{\n_{\x_0}}}
                        {|\x_0-\xs|^{\frac{3}{2}}}
            \dirac{t+\frac{|\x_0-\xs|}{c}}, \\
    \end{aligned}

.. math::
    :label: td-wfs-focused-25d-refline

    \begin{aligned}
        d_\text{2.5D}(\x_0,t) =&
            \frac{1}{\sqrt{2\pi}} a(t) * h_\text{2.5D}(t) * w(\x_0)
            \sqrt{\frac{d_\text{ref}}{d_\text{ref}-d_\text{s}}} \\
            &\cdot \frac{\scalarprod{\x_0-\xs}{\n_{\x_0}}}
                        {|\x_0-\xs|^{\frac{3}{2}}}
            \dirac{t+\frac{|\x_0-\xs|}{c}}.
    \end{aligned}

In this document a focused source always refers to the td-reversed version of a
point source, but a focused line source can be defined in the same way starting
from :eq:`fd-wfs-line`

.. math::
    :label: fd-wfs-focused-2d

    D(\x_0,\w) = -\frac{1}{2}A(\w) w(\x_0) \i\wc 
        \frac{\scalarprod{\x_0-\xs}{\n_{\x_0}}}{|\x_0-\xs|}
        \Hankel{1}{1}{\wc |\x_0-\xs|}.

Transferred to the temporal domain via an inverse Fourier transform :eq:`ifft`
it follows

.. math::
    :label: td-wfs-focused-2d

    d(\x_0,t) = \sqrt{\frac{1}{2\pi}} a(t) * h(t) * w(\x0)
        \frac{\scalarprod{\x_0-\xs}{\n_{\x_0}}}{|\x_0-\xs|^{\frac{3}{2}}}
        \dirac{t+\frac{|\x_0-\xs|}{c}}.

The window function :math:`w(\x_0)` for a focused source can be calculated as

.. math::
    :label: wfs-secondary-source-selection-focused

    w(\x_0) = 
        \begin{cases}
            1 & \scalarprod{\n_\text{s}}{\xs-\x_0} > 0 \\
            0 & \text{else}
        \end{cases}


.. [#F1]
    Whereby :math:`r` corresponds to :math:`|\x_0-\xs|` and :math:`\cos\varphi`
    to :math:`\frac{\scalarprod{\x_0-\xs}{\n_{\x_0}}}{|\x_0-\xs|}`.

.. vim: filetype=rst spell:
