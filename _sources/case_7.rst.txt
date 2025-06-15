.. _case_7:

R2D - Hurricane Damage
======================

Author: Daniel Acosta Reyes
---------------------------

Introduction
------------

Hurricane Maria, a Category 5 storm, struck Puerto Rico on September 20, 2017, claiming over 3000 lives. 
This example assesses wind-induced damage for Vieques Island, Puerto Rico, using peak gust wind speed data from the National Institute of Standards and Technogology (NIST) and inventory of about 900 wood residential buildings. The inventory was created using a Python Notebook with BRAILS and formatted to a HAZUS-type damage assessment in R2D. The final R2D results include damage state and loss estimations.


Problem Description
-------------------

Hurricanes are powerful tropical storms producing strong winds and heavy rains. The Saffir-Simpson Wind Scale, which categorizes hurricanes from 1 to 5 based on sustained wind speeds, helps estimate potential property damage :cite:`Saffir1973`. Hurricanes in categories 3 and above are deemed major due to their significant destructive potential.

.. figure:: ./images/case7_DA1_saffirScale.png
   :width: 500
   :align: center
   :figclass: align-center

   **Fig. 1.** Saffir-Simpson Hurricane Scale. Soruce: TIGHE PA.
Hurricane Maria (Category 5) destroyed several homes and health facilities in Puerto Rico, including `the only healthcare center on Vieques Island <https://www.menendez.senate.gov/newsroom/press/sens-menendez-wicker-reintroduce-vieques-recovery-and-redevelopment-act>`_, exacerbating the island's health crisis after decades-long military use.
A recent study by Guerra-Velasquez :cite:`Guerra2022` highlithed that infrastructure weaknesses made recovery more challenging and emphasize the need for increased resiliency and preparedness for future disasters.

Solution Strategy
-----------------
The proposed solution gathers input data for a HAZUS-type damage and lossess analysis implemented in R2D. The dataset includes a baseline hazard, building inventory, and rulesets.

#. **Baseline Hazard:** The hazard definition for this analysis is a raster file containing `Hurricane Maria Wind-Field Model for Puerto Rico <https://catalog.data.gov/dataset/hurricane-maria-wind-field-model-for-puerto-rico>`_ in miles per hour (mph).
    
    .. figure:: ./images/case7_DA2_raster.png
        :width: 600
        :align: center
        :figclass: align-center

        **Fig. 2.** Raster visualization of Hurricane Maria Wind-Field Model.

#. **Building Inventory:** The building inventory for Puerto Rico is limited. However, using NHERI-SimCenter Building and Infrastructure Recognition using AI at Large-Scale `(BRAILS) <https://github.com/NHERI-SimCenter/BRAILS>`_ capabilities, it is possible to obtain building footprints and their associated attributes for a given location. Brails was implemented in a Jupyter Notebook to:

    * Obtain building footprints from open repositories (e.g., NSI)
    * Fetch Google Street Views using Google API
    * Import essential BRAILS modules such as NumberOfFloors, YearBuilt, and OccupancyClassifier 
    * Merge and create a Building Inventory Model (BIM) with required attributes for rulesets.

    A sample code to obtain bulding inventories is provided below. Make sure to ``!pip install brails`` before running.::

        ''' Import modules '''
        from brails.workflow.FootprintHandler import FootprintHandler
        from brails.workflow.NSIParser import NSIParser
        from brails.workflow.ImHandler import ImageHandler
        
        ''' Building parameters '''
        # Define query location:
        name_key = 'Vieques_2'
        location = Lajas, Puerto Rico
        # location = (lonmin, latmin, lonmax, latmax) # supports exten/bounding box format

        # Define footprint source:
        # fpSource included in BRAILS are i) OpenStreetMaps,
        # ii) Microsoft Global Building Footprints dataset, and iii) FEMA USA Structures.
        # The keywords for these sources are osm, ms, and usastr, respectively.
        footprint_source = 'osm'

        # Length units for the attributes (used when relevant):
        lengthunit = 'm' # Options are 'm' or 'ft'

        # File where the building inventory will be stored:
        outputfile = 'BuildingInventory.geojson'.format(name_key)

        ''' Initialize and Run BRAILS modules '''
        # Initialize FootprintHandler:
        fpHandler = FootprintHandler()

        # Run FootprintHandler to get the footprints for the entered location:
        fpHandler.fetch_footprint_data(location, fpSource=footprint_source,
                                    outputFile='{0}_Footprints.geojson'.format(name_key),
                                    lengthUnit=lengthunit)
        footprints = fpHandler.footprints.copy()

        # Initialize NSIParser:
        nsiParser = NSIParser()

        # Run NSIParser to merge the footprint data with NSI points:
        nsiParser.GetNSIData(footprints, outfile=outputfile, lengthUnit=lengthunit)

    .. note::
        Complete Jupyter notebook can be accessed in DesignSafe - Data Depot at ``PRJ-4604/Losses_Damage_R2D`` under the name `InventoryBRAILS-notebook.ipynb`.


    .. figure:: ./images/case7_DA3_footprints.png
        :width: 600
        :align: center
        :figclass: align-center

        **Fig. 3.** Sample of 932 Building Footprints in Vieques, Puerto Rico, and BRAILS NumberOfFloors module generator.


#. **HAZUS Rulesets:** `HAZUS <https://www.fema.gov/flood-maps/products-tools/hazus>`_ is a tool for risk modeling methodology that is implemented in R2D. To conduct damage and loss assessments, the software uses rulesets that parses building attributes such as occupancy, year built, roof height, and others. For this example, the HAZUS rulesets were obtained and modified from `E8 - Hurricane Wind Example <https://nheri-simcenter.github.io/R2D-Documentation/common/user_manual/examples/desktop/E8HurricaneWind/README.html>`_ in R2D documentation.


SimCenter Tool Used
-------------------
This example uses the *Regional Resilience Determination Tool* (**R2D**) for wind damage and loss estimations in a residential area in Vieques, Puerto Rico, in order to assess the impact of wind forces and improve infrastructure resilience for future events.

Example Application
-------------------
+-----------------+------------------------------------------------------------------------------------------+
| Download files  | `Download <https://github.com/dacost2/E17HurricaneMariaPR/archive/refs/heads/main.zip>`_ |
+-----------------+------------------------------------------------------------------------------------------+

Download example **E17 - Hurricane Maria PR** files and open R2D. In the **File** tab, **Open** the "input.json" file within the example.

    .. figure:: ./images/case7_DA4_E1.png
        :width: 400
        :align: center
        :figclass: align-center

        **Fig. 4.** R2D inut data.

.. note::
    You will notice that all the inputs are autopopulated. Make sure to follow the next steps to verify they are in the correct units and using the right file references.


#. In the **GI** panel, set the **Analysis Name**, **Units**, **Asset Layers**, and **Output Settings**.

    .. figure:: ./images/case7_DA5_E2.png
        :width: 400
        :align: center
        :figclass: align-center

        **Fig. 5.** R2D **GI** Input and Output Settings.

#. For the **HAZ** Hazard Selection, select "Raster Defined Hazard" for an **Event Type**: "Hurricane". The **Intensity Measures of Raster** is "Peak Gust Wind Speed - PWS" in mph. 

    .. figure:: ./images/case7_DA6_E3.png
        :width: 400
        :align: center
        :figclass: align-center

        **Fig. 6.** R2D **HAZ** Hazard Definition.

#. In the **ASD** panel, make sure the **Regional Building Inventory** is set to "GIS File to AIM". Select the **Assets to Analyze** as intervals (e.g., 1-100) or go back to panel **VIZ** to manually select your assets by i) 'click' on the inventory layer; ii) push "Select" button (then drag selection in the map); iii) push "Add Assets" button.

    .. figure:: ./images/case7_DA7_E4.png
        :width: 400
        :align: center
        :figclass: align-center

        **Fig. 7 (a).** R2D **ASD** Assets Selection by Intervals.

    .. figure:: ./images/case7_DA8_E5.png
        :width: 400
        :align: center
        :figclass: align-center

        **Fig. 7 (b).** R2D **VIZ** Assets Selection in the Map.

#. The **HTA** panel requires a "Site Specified" calculation

    .. figure:: ./images/case7_DA9_E6.png
        :width: 400
        :align: center
        :figclass: align-center

        **Fig. 8.** R2D **HTA** Building Mapping.

#. This example does not need a Building Modeling. Therefore, **MOD** set to "none".

    .. figure:: ./images/case7_DA10_E7.png
        :width: 400
        :align: center
        :figclass: align-center

        **Fig. 9.** R2D **MOD** Building Model.

#. The Building Analysis Method **ANA** will be "IMasEDP".

    .. figure:: ./images/case7_DA11_E8.png
        :width: 400
        :align: center
        :figclass: align-center

        **Fig. 10.** R2D **ANA** Building Analysis Method.

#. **Damage & Loss Apllication** is found in the **DL** panel. Here you select "Pelicun3" with **Damage and Losss Method** "HAZUS MH MU". The Auto-population script referring to the specified ruleset is "auto_HU_PR.py" file.

    .. figure:: ./images/case7_DA12_E9.png
        :width: 400
        :align: center
        :figclass: align-center

        **Fig. 11.** R2D **DL** Damage & Loss Application.

#. **RUN** the analysis. For the selection of 100 buildings, it should run in your local machine. For full inventory analysis, push **RUN at DesignSafe** button.
    
    .. warning:: 
        For complete inventory: run in DesignDafe, Stampede2 - 20 min|96 Skylake (SKX) cores | 2 nodes with 48 processors per node | 280 buildings per task

    .. note::
        **SP**, **UQ**, and **RV** panels are not used in this example.
    
Results
-------

Results will be retrieved from the **RUN** panel and manipulated in the visualization **VIZ** panel for mapping options.

    .. figure:: ./images/case7_DA13_E10.png
        :width: 400
        :align: center
        :figclass: align-center

        **Fig. 12.** R2D **RES** Regional Results Summary.
        
The assessment gives two types of results:

a) **MostLikelyCriticalDamageState**: The Damage State uses a scale from 0 to 4 that correspons to a qualitative damage description.

.. list-table:: Damage State for Residential Buildings
   :widths: 25 50
   :header-rows: 1

   * - Damage State
     - Qualitative Damage Description
   * - 0
     - *No Damage or Very Minor Damage* - No visible damage from outside
   * - 1
     - *Minor Damage* - One broken window and moderate roof cover loss
   * - 2
     - *Moderate Damage* - Major roof cover damage 
   * - 3
     - *Sever Damage* - Major window damage and roof cover loss
   * - 4
     - *Destruction* - Complete roof failure and/or failure of wall figremjobpane

*Ref.:* FEMA HAZUS Hurricane Technical Manual `4.2.3 <https://www.fema.gov/sites/default/files/documents/fema_hazus-hurricane-technical-manual-4.2.3_0.pdf>`_

b) **mean_RepairCost_loss_ratio**: The repair cost loss ratio is computed as a ratio of an estimated repair cost and wind-induced damage. The standard deviation of this variable is also provided as *std_RepairCost_loss_ratio*. This ratio goes on a scale from 0 to 1 and correlates with the *Damage State* (DS) variable. A typical breakdown corresponds to:
    
    * DS0 - Loss ratio 0%
    * DS1 - Loss ratio 2%
    * DS2 - Loss ratio 10%
    * DS3 - Loss ratio 50%
    * DS4 - Loss ratio 100%

The results suggest that -of the 100 buildings assessed- most would suffer `Severe Damage` to `Destruction` given the inventory information.

.. note::
    **Results Visualization**: R2D offers QGIS capabilities to visualize regional trends and produce mapping products. *Fig. 13* shows an example of a map product using the QGIS module to create *HeatMaps* of the **MostLikelyCriticalDamageState** variable and the spatial distribution of the **mean_RepairCost_loss_ratio** for different intervals. Then, using the "New Layout" option you can create maps with legends, title, and other elements.
    See `QGIS Documentation <https://docs.qgis.org/3.34/en/docs/user_manual/>`_ for more.

.. figure:: ./images/case7_DA14_Results.png
    :width: 700
    :align: center
    :figclass: align-center

    **Fig. 13.** Mapping Visualization of Results Using QGIS in R2D.

Remarks
-------

* Hurricanes are increasingly happening with more intensity and force due to climate change.
* In locations such as Vieques, Puerto Rico, a comprehensive building inventory to assess huricane impact to infrastructure assets is limited. Assessing hazard exposure and consequences are key to increase resilience.
* R2D possess vast capabilities to adress this challenge, allowing researches to input hazard data and construct building inventories with BRAILS tools.
* This example provides strategies for creating building inventories in locations where data is scarce and implementing them in a format that could be used in R2D for regional analysis.
