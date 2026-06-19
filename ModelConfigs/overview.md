# Overview of Models

There are currently three models on the bench:

- FESOM
- NEMO
- ICON

The common configuration is called DBGYRE which is an idealized configuration to be used with different numerical ocean models. It was first introduced as a "*Double-Gyre*" configuration by [Levy et al. (2010)][Levy2010-cite] using the [NEMO][NEMO-www] ocean model. It was then adapted and refined for and implemented into [FESOM][FESOM-www] ocean model as "*toy_dbgyre*" by [Ekatarina Bagaeva et al. (2024)][Ekatarina-cite].

The *DBGYRE* configuration provided by this project have been modified in such a way that the resulting outcomes are as similar as possible between the three emodel branches *FESOM*, *NEMO* and ICON that are currently supported.


The general characteristics of *DBGYRE* are[^change]:

- Closed rectengular basin rotated by 45˚ of fixed size but of varying grid resolution
- Spherical coordinates
- Corners are located at (in clockwise direction starting at the northern most corner):
    - North:
    - East:
    - South:
    - West: 30.0N, ~~0.0W~~ 85W
- Linearised equation of state
- Coriolis $\beta$-plane
- Flat bottom
- Vertical mixing is based on Richardson number ([Pacanowski & Philander 1981 ![doi](assets/img/doi-grey-small.svg)](PP1981-cite))
- The lateral boundary condition set to *free-slip*
- ...

Details can be found in each model-specific document

- [FESOM](fesom.md)
- [NEMO](nemo.md)
- [ICON](icon.md)


[^change]: Might be object to experiment-specific changes.





[NEMO-www]: https://www.nemo-ocean.eu

[FESOM-www]: https://fesom.de


[Ekatarina-cite]: https://doi.org/10.1029/2023MS003972 "Bagaeva, E., Danilov, S., Oliver, M., & Juricke, S. (2024). Advancing eddy parameterizations: Dynamic energy backscatter and the role of subgrid energy advection and stochastic forcing. Journal of Advances in Modeling Earth Systems, 16, e2023MS003972. https://doi.org/10.1029/2023MS003972"

[Levy2010-cite]: https://doi.org/10.1016/j.ocemod.2010.04.001 "M. Lévy, P. Klein, A.-M. Tréguier, D. Iovino, G. Madec, S. Masson, K. Takahashi (2010). Modifications of gyre circulation by sub-mesoscale physics. Ocean Modelling, vol. 34, issue 1-2. https://doi.org/10.1016/j.ocemod.2010.04.001"

[PP1981-cite]: https://doi.org/10.1175/1520-0485(1981)011¡1443:POVMIN¿2.0.CO;2 "Pacanowski, R. C., & Philander, S. G. H. (1981). Parameterization of vertical mixing in numerical models of tropical oceans. Journal of Physical Oceanography, 11(11), 1443–1451. https://doi.org/10.1175/1520-0485(1981)011¡1443:POVMIN¿2.0.CO;2"
