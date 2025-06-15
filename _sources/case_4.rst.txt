.. _case_4:

S\ :sup:`3` hark - Site Response 2
===================================

Author: Chungen Tai



Introduction
------------

This page shows basic concepts of one-dimensional nonlinear site response analysis by using various soil material models (ex: Elastic, PM4Sand.) and the lateral spreading.


Problem Description
-------------------

Site response analysis is commonly performed to analyze the propagation of seismic waves through soil. As shown in the below figure, one-dimensional response analyses, as a simplified method, assume that all boundaries are horizontal, and that the response of a soil deposit is predominately caused by SH-waves propagating vertically from the underlying bedrock. Ground surface response is usually the major output from these analyses, together with profile plots such as peak horizontal acceleration along the soil profile. When liquefiable soils are presenting, maximum shear strain and excess pore pressure ratio plots are also important.

.. figure:: ./images/case4_siteResponse.png
    :scale: 60 %
    :align: center
    :figclass: align-center

    Figure 1. Site Response Analysis


Solution Strategy
-----------------

OpenSees
~~~~~~~~~~~~~~~~~
OpenSees (Open System for Earthquake Engineering Simulation) is an open-source software framework used for simulating the response of structural and geotechnical systems to earthquakes and other loading conditions. It is widely used in research and practical applications for the analysis and design of structures subjected to dynamic loading. S3hark created a GUI to help user doing nonlinear site response analysis easily.


Soil model
~~~~~~~~~~~~~~~~~
PM4Sand Model
~~~~~~~~~~~~~~~~~
PM4Sand is a constitutive model used in geotechnical engineering to simulate the behavior of sand under various loading conditions, including seismic events. Developed by Boulanger and Ziotopoulou, this model captures the complex behaviors of sand such as cyclic mobility, dilatancy, and liquefaction. It's particularly useful in finite element analysis for evaluating the performance of soil-structure systems during earthquakes. PM4Sand is known for its ability to represent the stress-strain behavior of sandy soils accurately, aiding in the design and analysis of foundations, slopes, and embankments in seismic regions. The model parameters are grouped into two categories: a primary set of six parameters (three properties, two flags, and atmospheric pressure) that are most important for model calibration, and a secondary set of parameters that may be modified from their default values in special circumstances. The three primary input properties are the sand’s apparent relative density :math:`D_R`, the shear modulus coefficient :math:`G_o`, and the contraction rate parameter :math:`h_po`.
Apparent relative density (:math:`D_R`) can be calculated by correlation to penetration resistance.Commonly estimated based on CPT or SPT penetration resistances, such as the following relationships used by Idriss and Boulanger (2008):

.. math::

    D_R = \frac{(N_1)_{60}}{C_d}
    
Where

:math:`D_R` = Apparent Relative density (%)

:math:`C_d` = 46 (Idriss and Boulanger ,2008)


Shear modulus coefficient is the primary variable controlling the small strain shear modulus, G_max. Should be chosen to match estimated or measured shear wave velocities.

The shear modulus \( G_{max} \) can be described by the following equations:

.. math::
  
    G_{max} = \rho V_s^2

Usign following equation to calculate :math:`G_0`   

.. math::

    G_{max} = G_0 p_a \left(\frac{p}{p_a}\right)^{0.5}

Contraction rate parameter :math:`h_{po}` is one of primary variable that adjusts contraction rates and hence can be adjusted to obtain a target cyclic resistance ratio, as commonly estimated based on CPT or SPT penetration resistances and liquefaction correlations.

   


Elasticisotropic Model
~~~~~~~~~~~~~~~~~~~~~~~

In the context of geotechnical engineering and computational modeling, the "elastic model" refers to a mathematical representation of soil or rock behavior under loading conditions, where the material responds to stress with a proportional strain, following Hooke's Law. This model assumes that the material returns to its original shape once the load is removed, provided the stress is within the elastic limit.We can get the shear modulus from shear wave velocity, and then use the following equation convert to elastic modulus,

.. math::

    E= (2G)(1+\nu)


Where
:math:`\nu` = Poisson’s ration (default is 0.3)




Lateral Spreading
~~~~~~~~~~~~~~~~~

Lateral spreading refers to the horizontal movement of soil, typically caused by liquefaction during an earthquake. When an earthquake occurs, seismic waves can cause loose, water-saturated, granular soils to temporarily lose their strength and behave like a liquid. This process is known as liquefaction. Once liquefaction occurs, the affected soil can flow or spread laterally, especially if there is a slope or free face (like a riverbank or retaining wall) nearby. Lateral spreading can cause significant ground displacement and can lead to damage of structures, utilities, and infrastructure. In this example, we will focus on the lateral movement cause by different slope angles.

.. figure:: ./images/case4_lateralspreading.png
    :scale: 60 %
    :align: center
    :figclass: align-center
    
    Figure 2. Lateral spreading




SimCenter Tool Used
-------------------

S\ :sup:`3` hark is the acronym of site-specific seismic hazard analysis and research kit. This tool focuses on simulating wave propagation along soil depth using finite element (FE) method. The intended audience for s3hark is researchers and practitioners interested in performing site-specific analysis of soil in response to earthquakes, and educators interested in teaching site response analysis in their classes. The tool provides a friendly interface for users to input and modify soil layers using tables, while the built soil profile and the FE mesh being visualized simultaneously. Results including acceleration, velocity, displacement, pore pressure, spectral acceleration, etc., are visualized for the soil profile and for each node as well, from which the user can comprehend the wave propagation and liquefaction status along the soil depth.

Features of S\ :sup:`3` hark include:

#. 2D and 3D elements for dynamic analysis of fluid saturated porous media

#. Advanced linear / nonlinear soil material models

#. Total stress / effective stress analysis

#. Bi-directional motions

#. Flat / slope free field analysis

#. Finite rigidity of the bedrock

Click on the icon of S\ :sup:`3` hark to open the application. Figure 3 illustrates the main window. It is split into the following areas:

.. figure:: ./images/case4_window.png
    :scale: 20 %
    :align: center
    :figclass: align-center

    Figure 3. S\ :sup:`3` hark HARK Main Window





#. Soil Column Graphic: The first graphic on the left of the panel shows a visualization of the soil column.

#. FE Mesh Graphic: The second graphic on the left shows the finite element mesh and profile plots. Selecting any of the tabs on the right inside this graphic (i.e, PGA, γmax,maxDisp, maxRu, maxRuPWP) will show various results from the simulation at the mesh points.

.. figure:: ./images/case4_plot.png
    :scale: 50 %
    :align: center
    :figclass: align-center

    Figure 4. S\ :sup:`3` hark HARK FE Mesh Graphic


#. Operations Area: The right side of this area shows some information (e.g., total height and number of soil layers), includes the Ground Water Table (GWT) input field, and plus and minus buttons. If the user presses the plus button, a layer is added below the selected layer. If the minus button is pressed the selected layer is removed. The GWT input field allows the user to specify the level of the ground water table. 

#. Soil Layer Table: This table is where the user provides the characteristics of the soil layer, such as layer thickness, density, Vs30, material type, and element size in the finite element mesh.

#. Tabbed Area: This area contains the three tabbed widgets described below.

    #. Configure Tab: This tab allows the user to specify the path to the OpenSees executable and to a ground motion file.

    #. Layer Properties Tab: This tab allows the user to enter additional material properties for the selected soil layer.

    #. Response Tab: Once the site response analysis has been performed, this tab provides information about element and nodal time varying response quantities.

.. figure:: ./images/case4_response.png
    :scale: 50 %
    :align: center
    :figclass: align-center

    Figure 5. S\ :sup:`3` hark HARK Response Tab


#. Analyze Button: This button shall be used to run the simulation locally. A progress bar will show the status of the analysis. This allows the user to review the ground motion predicted at the surface





Example Application
-------------------
Treasure Island, situated atop sand fill strata overlaying Bay Mud within the San Francisco Bay, was subjected to seismic activity during the 1989 Loma Prieta Earthquake. Adjacent to Treasure Island lies Yerba Buena Island, characterized by its natural rock outcrop. Utilizing the site's soil profile and seismic data recorded on Yerba Buena Island, we endeavor to analyze the site response of Treasure Island. This entails computing parameters such as peak horizontal acceleration and peak horizontal displacement along the soil profile. Furthermore, we aim to investigate the influence of lateral spread in varying slopes on the site's response characteristics. 
For lateral spreading, we change the slope :math:`\alpha`  as 0, 2, 5 to see the influence of it. 


Analysis Processes
~~~~~~~~~~~~~~~~~~~~~~~~
There is the analysis process of site response analysis: 

#. Input the earthquake motions: Covent the earthquake motion record into '.json' file. Then, input the path into 'Configure Layer'.   

#. Input ths soil parameters: This is the most important step in site response analysis. The soil parameters are obtained from theory or investigation reports. Then, type all of the soil parameters into the 'Soil Layer Table'.   

#. Click "analysis" bottom: Click the button to run the analysis. The program will notify you when it is finished.

Earthquake motion
~~~~~~~~~~~~~~~~~
The motion recorded at Yerba Buena Island from the Loma Prieta earthquake is used in this sample. Figure 6 shows the acceleration, velocity, and displacement of this motion.

.. figure:: ./images/case4_YERBAISL2_Records.png
    :scale: 50 %
    :align: center
    :figclass: align-center

    Figure 6. Input ground motion (acceleration, velocity, and displacement)

Figure 7 illustrates the spetrum of this motion.

.. figure:: ./images/case4_YERBAISL2_RSpectra.png
    :scale: 50 %
    :align: center
    :figclass: align-center

    Figure 7. Input ground motion spetrum (acceleration, velocity, and displacement)


Soil condition
~~~~~~~~~~~~~~~~~

The following table shows the soil parameters in this site. Using PM4Sand to stimulate sand , and using Elasticisotropic to model clay.


.. list-table:: Table1. Soil Profile Parameters
   :widths: 20 20 20 20 20 20 20 20
   :header-rows: 1

   * - Layers
     - Thickness(m)
     - :math:`V_s (m/s)` 
     - :math:`\rho (kg/m^3)` 
     - :math:`G_o(kPa)`
     - :math:`E (kPa)` 
     - :math:`D_R` (%)
     - Model  
   * - SAND1
     - 15
     - 175.3
     - 2.01
     - 0.69
     - --
     - 0.33
     - PM4Sand
   * - CLAY1
     - 14
     - 198.8
     - 1.68
     - --
     - 172.8
     - --
     - Elasticisotropic
   * - SAND2
     - 12
     - 300
     - 2.08
     - 1.33
     - --
     - 0.77
     - PM4Sand     
   * - CLAY2
     - 32
     - 280
     - 2.08
     - --
     - 423.8
     - --
     - Elasticisotropic
   * - SAND3
     - 8
     - 380
     - 2.08
     - 1.46
     - --
     - 0.62
     - PM4Sand
   * - CLAY3
     - 8
     - 300
     - 2.08
     - --
     - 486.6
     - --
     - Elasticisotropic
   * - WRock
     - 5
     - 600
     - 2.16
     - --
     - 2022.8
     - --
     - Elasticisotropic 
   * - Rockbed
     - --
     - 1830
     - 2.24
     - --
     - 19526.7
     - --
     - Elasticisotropic     


Result
~~~~~~~~~~~~~~~~~~

The following plots show maximun displacement in different slope angles. 


Displacement
~~~~~~~~~~~~~~~~~

Figure 8 shows the result of maximun displacement, we can see that the displacement on the surface increases with increasing the slope. 


.. figure:: ./images/case4_d.png
    :scale: 50 %
    :align: center
    :figclass: align-center

    Figure 8. The maximum displacement in different slope


Figure 9 illustrates the relationships between slope and the displacement on the surface.


.. figure:: ./images/case4_dd.png
    :scale: 60 %
    :align: center
    :figclass: align-center

    Figure 9. The relationships between slope and the displacement on the surface
