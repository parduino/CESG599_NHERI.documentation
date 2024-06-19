.. _case_5:

R2D - Liquefaction
==================

Author: Morgan Sanger
---------------------

Introduction
------------

This page describes basic concepts of geospatial liquefaction hazard modeling withing the umbrella of SimCenter tools. 


Problem Description
-------------------

Coseismic soil liquefaction is a phenomenon in which the strength and stiffness of a soil is reduced by earthquake shaking. Resilient communities and infrastructure networks, like lifelines or transportation systems, must be built to withstand and respond to hazards posed by coseismic soil liquefaction. Ideally, these predictions could be made:

* quickly, in near-real-time after an event; 

* at high resolution, consistent with the scale of individual assets; and 

* at map-scale, across the regional extent affected by large earthquakes. 


Common liquefaction models in practice require in-situ testing which cannot be continuously performed across large areas, thus presenting the need for “geospatial” liquefaction models. Prior tests of such models (e.g., :cite:`Zhu2017`) have shown both promising potential and severe shortcomings in predicting subsurface conditions with few geospatial predictors. There is a need to advance geospatial liquefaction modeling by integrating geotechnical data, liquefaction mechanics, artificial intelligence (AI), and many geospatial predictor variables to provide reliable regional liquefaction predictions for any earthquake event. When integrated with regional hazard assessment capabilities, geospatial liquefcation models will provide value throughout the life of infrastructure projects - from initial desk studies to refined project-specific hazard analyses - and will unlock insights beyond conventional practice, with opportunity to: 

* prescribe event-specific emergency response and evacuation routes immediately after an earthquake,

* evaluate network reliability and infrastructure network resiliency using structural databases or other asset inventories, and 

* understand the impacts of earthquake events of vulnerable communities using population demographic data. 

Solution Strategy
-----------------

The state-of-practice geospatial liquefaction model is the :cite:`RB2020` model (updated version of the :cite:`Zhu2017` model), which uses logistic regression to predict probability of liquefaction based on five (5) geospatial variables and trained on a database of liquefaction case histories.

In this problem, another modeling solution strategy is proposed, according to :cite:`Sanger2024`. The :cite:`Sanger2024` approach parses the problem into that which is empirical and best predicted by AI (the relationship between geospatial variables and subsurface traits) and that which is best predicted by mechanics (liquefaction response, conditioned on those traits). In this approach, the subsurface traits are characterized at point locations using available cone penetration testing (CPT) data. The liquefaction response at each CPT location is computed across a range of magnitude-scale peak ground accelerations (PGAM7.5) using state-of-practice liquefaction manifestation models (e.g., liquefaction potential index, LPI), thereby retaining the knowledge of liquefaction mechanics developed over the last 50+ years. The relationship between manifestation index and PGAM7.5 is represented as a functional form (Eqn 1) with two curve-fitting parameters: A and B (Fig 1). Therefore, the liquefaction response (i.e., A and B) at each CPT location becomes target variables of supervised learning AI models.  

.. math::
    MI = \left\{ \begin{array}{ll}
    0, & \text{PGA}_{M7.5} < 0.1g \\
    \arctan(B \cdot (\text{PGA}_{M7.5} - \frac{A}{B})^2) \cdot 100, & \text{PGA}_{M7.5} \geq 0.1g
    \end{array} \right.

**Eqn 1.** Manifestation index as a function of A, B, and PGAM7.5. 


.. figure:: ./images/case5_manifestationcurve.png
    :scale: 65 %
    :align: center
    :figclass: align-center
    
    **Fig 1.** Example manifestation curve of LPI vs. PGAM7.5 for a single CPT.

The AI model is trained to predict liquefaction response using a set of 37 geospatial predictor variables that serve as proxies for liquefaction. These predictor variables include metrics of surface topography and roughness, distance to and elevation above water bodies, and information about geology, geomorphology, and hydrology. By applying the trained AI model to these predictor datasets, the A and B parameters are predicted geospatially at 100-meter resolution. A crucial step in this approach is geostatistically updating the AI predictions via regression kriging near field measurements. The effect is that predictions near known subsurface conditions have lower model uncertainty, while predictions farther away rely on the AI model and have higher uncertainty. This method effectively pre-computes liquefaction response for all possible ground motions based on AI-predicted subsurface conditions. These predictions are stored as mapped parameters, ready for use with specific earthquake data, such as a PGAM7.5 raster in R2D.

Model predictions were then tested against the leading geospatial model :cite:`RB2020` in three case-history events using receiver operating characteristic and area under the curve analyses (Fig 2). The :cite:`Sanger2024` AI model (before kriging) performed significantly better than :cite:`RB2020` and was further improved by kriging (Fig 3). 

.. figure:: ./images/case5_sanger2024-roc.png
    :scale: 100 %
    :align: center
    :figclass: align-center

    **Fig 2.** Receiver operator characteristic curves and area under the curve (AUC) analyses comparing :cite:`RB2020` (“R&B”), and the :cite:`Sanger2024` *before* regression kriging (“LPI”).

.. figure:: ./images/case5_zhu2017.png
    :scale: 100 %
    :align: center
    :figclass: align-center

    **(a)**

.. figure:: ./images/case5_sanger2024-ai.png
    :scale: 100 %
    :align: center
    :figclass: align-center

    **(b)**

.. figure:: ./images/case5_sanger2024-krig.png
    :scale: 100 %
    :align: center
    :figclass: align-center

    **(c)**

    **Fig 3.** Comparison between **(a)** Rashidian & Baise (2020), and this model **(b)** before and **(c)** after regression kriging for the Feb. 2011 M6.1 Christchurch event.


SimCenter Tool Used
-------------------

The presented problem can be solved using SimCenter's Regional Resilience Determination `R2D <https://simcenter.designsafe-ci.org/research-tools/r2dtool/>`_ Tool. A substantially complete description of the tool is provided in the `R2D Documentation <https://nheri-simcenter.github.io/R2D-Documentation/common/user_manual/about/R2D/about.html>`_. 

The updated :cite:`Zhu2017` model (:cite:`RB2020`) is implemented in the R2D tool (version 4.2.0), whereas the :cite:`Sanger2024` model is not yet implemented in the R2D tool. In this project, the :cite:`Sanger2024` model was implemented in the R2D tool using the `applications.py` file. 

The workflow for the :cite:`Zhu2017` model in the Earthquake Event Generator tool in R2D is as follows:

**1. Define Analysis Grid**: Define the analysis grid for the study area. Here, an area of downtown San Francisco is selected for the analysis. The grid is defined with a resolution of approximately 100 meters, the true model resolution of the :cite:`Sanger2024` model.

.. figure:: ./images/case5_EQGen1.png
    :width: 800px
    :align: center
    :figclass: align-center

.. raw:: html

    <br>
    
**2. Forecast Rupture Scenarios**: Large events (>M7) are forecasted for the study area to demonstrate the model performance under extreme conditions.

.. figure:: ./images/case5_EQGen2.png
    :width: 800px
    :align: center
    :figclass: align-center

.. raw:: html

    <br>
    
**3. Select Earthquake Event**: Select an earthquake event scenario. Here, the M8 N. San Andreas rupture event scenario is selected.

.. figure:: ./images/case5_EQGen3.png
    :width: 800px
    :align: center
    :figclass: align-center

.. raw:: html

    <br>
    
**4. Select Intensity Measures**: PGA and PGV are selected as the intensity measures for the analysis, both required for the :cite:`Zhu2017` model.

.. figure:: ./images/case5_EQGen4.png
    :width: 800px
    :align: center
    :figclass: align-center

.. raw:: html

    <br>

**5. Select Ground Failure Model**: Select the ground failure model. Here, the :cite:`Zhu2017` model is selected.

.. figure:: ./images/case5_EQGen5.png
    :width: 800px
    :align: center
    :figclass: align-center

.. raw:: html

    <br>

**6. Run Hazard Simulation**.

.. figure:: ./images/case5_EQGen6.png
    :width: 800px
    :align: center
    :figclass: align-center

.. raw:: html

    <br>
    
**7. View Results**

Example Application
-------------------

This example demonstrates the application of the :cite:`Zhu2017` liquefaction-induced ground failure model (really the :cite:`RB2020` model) currently implemented in R2D, and the implementation of the next-generation :cite:`Sanger2024` model described in the **Solution Strategy**. 

.. note::
    In R2D, ground failure models are considered intermediate results that are accessible only through the Earthquake Event Generator tool, and they cannot be executed within the damage and loss assessment tools. Future development in R2D should consider (1) implementing ground failure models with other earthquake hazard source options (e.g., Shakemap Earthquake Scenario), (2) extending the implementation of the ground failure modeling beyond California, and (3) incorporating ground failure models into the damage and loss assessment tools.

    
.. note::
    The implementation of the :cite:`Zhu2017` and :cite:`Sanger2024` models in the Earthquake Event Generator tool employs grid sampling of the geospatial parameters, which is not the most efficient method for large-scale applications. Future development in R2D should consider implementing the models as raster-based models for more efficient, higher resolution, spatially continuous predictions in accordance with the model development.


Zhu et al. (2017)
.................

**CODE**

The following code snippet shows the implementation of the :cite:`Zhu2017` model in the R2D tool using the `applications.py` file.

.. raw:: html

    <details>
    <summary><u><b>Click to expand the full ZhuEtal2017 code</u></b></summary>
    <pre><code>

.. code-block:: python
    :linenos:

    # Zhu et al. (2017) code
    -----------------------------------------------------------
    class ZhuEtal2017(Liquefaction):
        """
        A map-based procedure to quantify liquefaction at a given location using logistic models by Zhu et al. (2017). Two models are provided:

        1. For distance to coast < cutoff, **prob_liq** = f(**pgv**, **vs30**, **precip**, **dist_coast**, **dist_river**)
        2. For distance to coast >= cutoff, **prob_liq** = f(**pgv**, **vs30**, **precip**, **dist_coast**, **dist_river**, **gw_depth**)
        
        Parameters
        ----------
        From upstream PBEE:
        pgv: float, np.ndarray or list
            [cm/s] peak ground velocity
        mag: float, np.ndarray or list
            moment magnitude
        pga: float, np.ndarray or list
            [g] peak ground acceleration, only to check threshold where prob_liq(pga<0.1g)=0
        stations: list
            a list of dict containing the site infomation. Keys in the dict are 'ID',
            'lon', 'lat', 'vs30', 'z1pt0', 'z2pt5', 'vsInferred', 'rRup', 'rJB', 'rX'
            
        Geotechnical/geologic:
        vs30: float, np.ndarray or list
            [m/s] time-averaged shear wave velocity in the upper 30-meters
        precip: float, np.ndarray or list
            [mm] mean annual precipitation
        dist_coast: float, np.ndarray or list
            [km] distance to nearest coast
        dist_river: float, np.ndarray or list
            [km] distance to nearest river
        dist_water: float, np.ndarray or list
            [km] distance to nearest river, lake, or coast
        gw_depth: float, np.ndarray or list
            [m] groundwater table depth
            
        Fixed:
        # dist_water_cutoff: float, optional
        #     [km] distance to water cutoff for switching between global and coastal model, default = 20 km

        Returns
        -------
        prob_liq : float, np.ndarray
            probability for liquefaciton
        liq_susc_val : str, np.ndarray
            liquefaction susceptibility category value
        
        References
        ----------
        .. [1] Zhu, J., Baise, L.G., and Thompson, E.M., 2017, An Updated Geospatial Liquefaction Model for Global Application, Bulletin of the Seismological Society of America, vol. 107, no. 3, pp. 1365-1385.
        
        """
        def __init__(self, parameters, stations) -> None:
            self.stations = stations
            self.parameters = parameters
            self.dist_to_water = None #(km)
            self.dist_to_river = None #(km)
            self.dist_to_coast = None #(km)
            self.gw_depth = None #(m)
            self.precip = None # (mm)
            self.vs30 = None #(m/s)
            self.interpolate_spatial_parameters(parameters)

        def interpolate_spatial_parameters(self, parameters):
            # site coordinate in CRS 4326
            lat_station = [site['lat'] for site in self.stations]
            lon_station = [site['lon'] for site in self.stations]
            # dist_to_water 
            if parameters["DistWater"] == "Defined (\"distWater\") in Site File (.csv)":
                self.dist_to_water = np.array([site['distWater'] for site in self.stations])
            else:
                self.dist_to_water = sampleRaster(parameters["DistWater"], parameters["inputCRS"],\
                        lon_station, lat_station)
            # dist_to_river
            if parameters["DistRiver"] == "Defined (\"distRiver\") in Site File (.csv)":
                self.dist_to_river = np.array([site['distRiver'] for site in self.stations])
            else:
                self.dist_to_river = sampleRaster(parameters["DistRiver"], parameters["inputCRS"],\
                        lon_station, lat_station)
            # dist_to_coast
            if parameters["DistCoast"] == "Defined (\"distCoast\") in Site File (.csv)":
                self.dist_to_coast = np.array([site['distCoast'] for site in self.stations])
            else:
                self.dist_to_coast = sampleRaster(parameters["DistCoast"], parameters["inputCRS"],\
                        lon_station, lat_station)
            # gw_water
            if parameters["GwDepth"] == "Defined (\"gwDepth\") in Site File (.csv)":
                self.gw_depth = np.array([site['gwDepth'] for site in self.stations])
            else:
                self.gw_depth = sampleRaster(parameters["GwDepth"], parameters["inputCRS"],\
                        lon_station, lat_station)
            # precipitation 
            if parameters["Precipitation"] == "Defined (\"precipitation\") in Site File (.csv)":
                self.precip = np.array([site['precipitation'] for site in self.stations])
            else:
                self.precip = sampleRaster(parameters["Precipitation"], parameters["inputCRS"],\
                        lon_station, lat_station)
            self.vs30 = np.array([site['vs30'] for site in self.stations])
            print("Sampling finished")
        
        def run(self, ln_im_data, eq_data, im_list, output_keys, additional_output_keys):
            if ('PGA' in im_list) and ('PGV' in im_list):
                num_stations = len(self.stations)
                num_scenarios = len(eq_data)
                PGV_col_id = [i for i, x in enumerate(im_list) if x == 'PGV'][0]
                PGA_col_id = [i for i, x in enumerate(im_list) if x == 'PGA'][0]
                for scenario_id in range(num_scenarios):
                    num_rlzs = ln_im_data[scenario_id].shape[2]
                    im_data_scen = np.zeros([num_stations,\
                                        len(im_list)+len(output_keys), num_rlzs])
                    im_data_scen[:,0:len(im_list),:] = ln_im_data[scenario_id]
                    for rlz_id in range(num_rlzs):
                        pgv = np.exp(ln_im_data[scenario_id][:,PGV_col_id,rlz_id])
                        pga = np.exp(ln_im_data[scenario_id][:,PGA_col_id,rlz_id])
                        mag = float(eq_data[scenario_id][0])
                        model_output = self.model(pgv, pga, mag)
                        for i, key in enumerate(output_keys):
                            im_data_scen[:,len(im_list)+i,rlz_id] = model_output[key]
                    ln_im_data[scenario_id] = im_data_scen
                im_list = im_list + output_keys
                additional_output = dict()
                for key in additional_output_keys:
                    item = getattr(self, key, None)
                    if item is None:
                        warnings.warn(f"Additional output {key} is not avaliable in the liquefaction trigging model 'ZhuEtal2017'.")
                    else:
                        additional_output.update({key:item})
            else:
                sys.exit(f"At least one of 'PGA' and 'PGV' is missing in the selected intensity measures and the liquefaction trigging model 'ZhuEtal2017' can not be computed.")
                # print(f"At least one of 'PGA' and 'PGV' is missing in the selected intensity measures and the liquefaction trigging model 'ZhuEtal2017' can not be computed."\
                #       , file=sys.stderr)
                # sys.stderr.write("test")
                # sys.exit(-1)
            return ln_im_data, eq_data, im_list, additional_output
        
        def model(self, pgv, pga, mag):
            """Model"""
            # zero prob_liq
            zero_prob_liq = 1e-5 # decimal
            
            # distance cutoff for model
            model_transition = 20 # km

            # initialize arrays
            x_logistic = np.empty(pgv.shape)
            prob_liq = np.empty(pgv.shape)
            liq_susc_val = np.ones(pgv.shape)*-99
            liq_susc = np.empty(pgv.shape, dtype=int)
            
            # magnitude correction, from Baise & Rashidian (2020) and Allstadt et al. (2022)
            pgv_mag = pgv/(1+np.exp(-2*(mag-6)))
            pga_mag = pga/(10**2.24/mag**2.56)

            # find where dist_water <= cutoff for model of 20 km
            # coastal model
            ind_coastal = self.dist_to_water<=model_transition
            # global model
            # ind_global = list(set(list(range(pgv.shape[0]))).difference(set(ind_coastal)))
            ind_global = ~(self.dist_to_water<=model_transition)

            # set cap of precip to 1700 mm
            self.precip[self.precip>1700] = 1700

            # x = b0 + b1*var1 + ...
            # if len(ind_global) > 0:
            # liquefaction susceptbility value, disregard pgv term
            liq_susc_val[ind_global] = \
                8.801 + \
                -1.918   * np.log(self.vs30[ind_global]) + \
                5.408e-4 * self.precip[ind_global] + \
                -0.2054  * self.dist_to_water[ind_global] + \
                -0.0333  * self.gw_depth[ind_global]
            # liquefaction susceptbility value, disregard pgv term
            liq_susc_val[ind_coastal] = \
                12.435 + \
                -2.615   * np.log(self.vs30[ind_coastal]) + \
                5.556e-4 * self.precip[ind_coastal] + \
                -0.0287  * np.sqrt(self.dist_to_coast[ind_coastal]) + \
                0.0666   * self.dist_to_river[ind_coastal] + \
                -0.0369  * self.dist_to_river[ind_coastal]*np.sqrt(self.dist_to_coast[ind_coastal])
            # catch nan values
            liq_susc_val[np.isnan(liq_susc_val)] = -99.
            # x-term for logistic model = liq susc val + pgv term
            x_logistic[ind_global] = liq_susc_val[ind_global] + 0.334*np.log(pgv_mag[ind_global])
            # x-term for logistic model = liq susc val + pgv term
            x_logistic[ind_coastal] = liq_susc_val[ind_coastal] + 0.301*np.log(pgv_mag[ind_coastal])

            # probability of liquefaction
            prob_liq = 1/(1+np.exp(-x_logistic)) # decimal
            prob_liq = np.maximum(prob_liq,zero_prob_liq) # set prob to > "0" to avoid 0% in log

            # for pgv_mag < 3 cm/s, set prob to "0"
            prob_liq[pgv_mag<3] = zero_prob_liq
            # for pga_mag < 0.1 g, set prob to "0"
            prob_liq[pga_mag<0.1] = zero_prob_liq
            # for vs30 > 620 m/s, set prob to "0"
            prob_liq[self.vs30>620] = zero_prob_liq

            # calculate sigma_mu
            sigma_mu = (np.exp(0.25)-1) * prob_liq

            # determine liquefaction susceptibility category
            liq_susc[liq_susc_val>-1.15]  = liq_susc_enum['very_high'].value
            liq_susc[liq_susc_val<=-1.15] = liq_susc_enum['high'].value
            liq_susc[liq_susc_val<=-1.95] = liq_susc_enum['moderate'].value
            liq_susc[liq_susc_val<=-3.15] = liq_susc_enum['low'].value
            liq_susc[liq_susc_val<=-3.20] = liq_susc_enum['very_low'].value
            liq_susc[liq_susc_val<=-38.1] = liq_susc_enum['none'].value

            # liq_susc[prob_liq==zero_prob_liq] = 'none'
            
            return {"liq_prob":prob_liq, "liq_susc":liq_susc}


.. raw:: html

    </code></pre>
    </details>


.. raw:: html

    <br><br>



**RESULTS**

.. figure:: ./images/case5_zhu_pliq.png
    :scale: 75 %
    :align: center
    :figclass: align-center

    **Fig 4.** Results of the Zhu et al. (2017) model for probability of liquefaction given the selected M8 N. San Andreas rupture event scenario.



Sanger et al. (2024)
.....................

The workflow for the :cite:`Sanger2024` model follows the same steps as the :cite:`Zhu2017` model, with the exception of the referenced geospatial parameters. The :cite:`Sanger2024` model needs only the LPI A and LPI B parameters, implemented here within the DistWater and DistCoast parameters in the R2D tool because the author did not have access to the user-interface code for the R2D tool.

.. figure:: ./images/case5_EQGen10.png
    :width: 800px
    :align: center
    :figclass: align-center    


**CODE**

In this example, the :cite:`Sanger2024` model is implemented in the R2D tool using the `applications.py` file, overwriting the `ZhuEtal2017` class for quick integration with the R2D user-interface.

.. raw:: html

    <details>
    <summary><u><b>Click to expand the full Sanger2024 code</u></b></summary>
    <pre><code>

.. code-block:: python
    :linenos:

    # Sanger et al. (2024) code
    -----------------------------------------------------------
    class ZhuEtal2017(Liquefaction):
    """
    A map-based procedure to quantify liquefaction at a given location Sanger et al. (2024). 

    Parameters
    ----------
    From upstream PBEE:
    mag: float, np.ndarray or list
        moment magnitude
    pga: float, np.ndarray or list
        [g] peak ground acceleration, only to check threshold where prob_liq(pga<0.1g)=0
    stations: list
        a list of dict containing the site infomation. Keys in the dict are 'ID',
        'lon', 'lat', 'vs30', 'z1pt0', 'z2pt5', 'vsInferred', 'rRup', 'rJB', 'rX'
        
    Geotechnical:
    LPI A: float, np.ndarray or list
    LPI B: float, np.ndarray or list

    Returns
    -------
    prob_liq : float, np.ndarray
        probability for liquefaciton (surface manifestation)
    
    """
    def __init__(self, parameters, stations) -> None:
        self.stations = stations
        self.parameters = parameters
        self.LPI_A = None #
        self.LPI_B = None #
        self.interpolate_spatial_parameters(parameters)

    def interpolate_spatial_parameters(self, parameters):
        # site coordinate in CRS 4326
        lat_station = [site['lat'] for site in self.stations]
        lon_station = [site['lon'] for site in self.stations]
        # LPI_A
        self.LPI_A = sampleRaster(parameters["DistWater"], parameters["inputCRS"],\
                    lon_station, lat_station)
        # LPI_B
        self.LPI_B = sampleRaster(parameters["DistCoast"], parameters["inputCRS"],\
                    lon_station, lat_station)
        print("Sampling finished")
    
    def run(self, ln_im_data, eq_data, im_list, output_keys, additional_output_keys):
        if ('PGA' in im_list):
            num_stations = len(self.stations)
            num_scenarios = len(eq_data)
            PGA_col_id = [i for i, x in enumerate(im_list) if x == 'PGA'][0]
            for scenario_id in range(num_scenarios):
                num_rlzs = ln_im_data[scenario_id].shape[2]
                im_data_scen = np.zeros([num_stations,\
                                    len(im_list)+len(output_keys), num_rlzs])
                im_data_scen[:,0:len(im_list),:] = ln_im_data[scenario_id]
                for rlz_id in range(num_rlzs):
                    pga = np.exp(ln_im_data[scenario_id][:,PGA_col_id,rlz_id])
                    mag = float(eq_data[scenario_id][0])
                    model_output = self.model(pga, mag)
                    for i, key in enumerate(output_keys):
                        im_data_scen[:,len(im_list)+i,rlz_id] = model_output[key]
                ln_im_data[scenario_id] = im_data_scen
            im_list = im_list + output_keys
            additional_output = dict()
            for key in additional_output_keys:
                item = getattr(self, key, None)
                if item is None:
                    warnings.warn(f"Additional output {key} is not avaliable in the liquefaction trigging model 'SangerEtal2024'.")
                else:
                    additional_output.update({key:item})
        else:
            sys.exit(f"'PGA' is missing in the selected intensity measures and the liquefaction trigging model 'SangerEtal2024' can not be computed.")
        return ln_im_data, eq_data, im_list, additional_output
    
    def model(self, pga, mag):
        """Model"""
        # magnitude correction, according to MSF Correction (SAND) Function according to Idriss and Boulanger (2008) 
        MSF = 6.9*np.exp(-mag/4) - 0.058
        if MSF > 1.8:
            MSF = 1.8
        pga_mag = pga / MSF  

        # Geospatial LPI A and LPI B
        # Initialize an array for the calculated MI
        LPI = np.zeros_like(pga_mag)
    
        # Calculate MI for each element of the arrays
        mask_low = pga_mag < 0.1
        mask_high = pga_mag >= 0.1
        LPI[mask_low] = 0
        LPI[mask_high] = self.LPI_A[mask_high] * np.arctan(self.LPI_B[mask_high] * (pga_mag[mask_high] - self.LPI_A[mask_high] / self.LPI_B[mask_high]) ** 2) * 100
        
        from scipy.stats import norm

        # Probability of liquefaction (manifestation at the surface) according to Geyin et al. (2020) (minor manifestation)
        LPI_beta= 1.774
        LPI_theta= 4.095 
        prob_liq = norm.cdf(np.log(LPI/LPI_theta)/LPI_beta)
        
        return {"liq_prob":prob_liq, "liq_susc":LPI}

.. raw:: html

    </code></pre>
    </details>


.. raw:: html

    <br><br>


**RESULTS**

.. figure:: ./images/case5_sanger_lpi.png
    :scale: 75 %
    :align: center
    :figclass: align-center

    **Fig 5.**  Results of the Sanger et al. (2024) model for liquefaction potential index given the selected M8 N. San Andreas rupture event scenario.

.. figure:: ./images/case5_sanger_pliq.png
    :scale: 75 %
    :align: center
    :figclass: align-center

    **Fig 6.**  Results of the Sanger et al. (2024) model for probability of liquefaction given the selected M8 N. San Andreas rupture event scenario, using the Geyin and Maurer (2020) fragility function to map LPI to probability of surface manifestation (minor/all).


Remarks
-------

.. note::
    Preliminary visualization of the results can be accomplished in the R2D VIZ tab using Graduated Symbols. However, the author recommends exporting the results to a GIS software for more detailed visualization and analysis.
        .. figure:: ./images/case5_EQGen7.png
            :width: 600px
            :align: center
            :figclass: align-center


.. note::
    Note the division spacing issue that arises when the number of divisions is greater than 10. This has been alerted to the developers.

    .. figure:: ./images/case5_EQGen11.png
        :width: 600px
        :align: center
        :figclass: align-center
