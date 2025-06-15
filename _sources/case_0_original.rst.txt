
.. _case_0:

.. figure:: ./images/case0-1.png
    :scale: 50 %
    :height: 200 px
    :align: center
    :figclass: align-center


Course Introduction
===================

CESG599 - NHERI - An introduction to NHERI Simcenter tools and DesignSafe Resources
-----------------------------------------------------------------------------------

Authors:
--------
**Kendra Mutch, Erick Martinez, Jose Barreto, Chungen Tai, Morgan Sanger, Luis Guerrero, Daniel Acosta, and Pedro Arduino**

Course Description
------------------
In this course, we explore the fundamentals of SimCenter tools and the DesignSafe infrastructure.
The course adopts a self-directed approach, where students follow a well-established framework tailored 
for this format. Over the course of 10 weeks, we investigate the core concepts of SimCenter and DesignSafe, 
and examine four to five SimCenter tools, covering one every two weeks. Students are tasked with mastering 
the essentials of each tool and delivering presentations to the class. Additionally, they work through one 
or more practical examples for each tool, presenting their findings to their peers. Constructive feedback for 
each tool presentation is an integral part of the learning experience. DesignSafe and SimCenter personnel are 
invited to give Zoom presentations based on availability. At the end of the course, a small final project, 
with a topic of choice for each student or group of students, is required, providing an opportunity for 
deeper exploration and application of the learned concepts.

 
SimCenter provides next-generation computational modeling and simulation software tools, user support, 
and educational materials to the natural hazards engineering research community with the goal of advancing 
the user’s capability to simulate the impact of natural hazards on structures, lifelines, and communities.

DesignSafe is a comprehensive cyberinfrastructure that provides cloud-based tools to manage, analyze, understand, 
and publish critical data for research to understand the impacts of natural hazards. The capabilities within 
the DesignSafe infrastructure are available at no-cost to all researchers working in natural hazards.

 
Learning Objectives
-------------------

#. Develop a familiarity with Simcenter tools and DesignSafe cyberinfrastructure
#. Develop a familiarity with the breath of SimCenter tools.
#. Develop a suitable background for using HPC resources.
#. Introduce/revise concepts related to structural and geotechnical engineering including UQ concepts, FEM, PB, etc.
#. Provide a working knowledge for selecting, using, and interpreting tools for Structural and geotechnical design and analysis.
#. Introduce markup language to develop easy-to-read, easy-to-write documents in the sphinx python package.


Introduction
------------
This section should provide a brief executive summary, giving an overview of the project. It should be written last to ensure it accurately reflects the content of the entire report. Aim for a short paragraph that highlights the key points, objectives, and outcomes of the project.


Problem Description
-------------------
Clearly describe the problem being addressed. Use text and images to illustrate the issue, ensuring that even readers unfamiliar with the topic can understand the context and significance. This section should detail the background, relevance, and any previous attempts to solve the problem.


Solution Strategy
-----------------
Outline the strategy used to solve the problem. This should include a detailed explanation of the methods, processes, and any equations or theoretical frameworks involved. Be sure to make this section as comprehensive as necessary to fully explain the approach taken.

SimCenter Tool Used
-------------------
Provide a brief description of the SimCenter tool(s) used in the project. Explain its relevance and how it was applied. This section should be informative yet concise, offering enough detail to understand the tool's role and capabilities without overwhelming the reader.

Example of Application
----------------------
Give a concrete example of how the solution strategy was applied, using text and images. This helps to illustrate the practical application of the theoretical concepts and methods discussed earlier. Ensure the example is detailed enough to show the effectiveness of the solution.

Remarks
-------
Use this section for any additional comments or observations that do not fit into the other sections. This could include limitations, unexpected findings, future directions, or any acknowledgments.


EXAMPLELS of LISTS, FIGURES, TABLE, REFERENCE, etc
--------------------------------------------------

#. Open the Dr. Layer program. By default we get twelve layers. The top six layers are hardwired into the system with a velocity of specified as very fast. The bottom six layers are hardwired with a velocity of very slow.

#. Select all the layers to all have very slow values using the select all option.

#. On the top left hand corner of the menu box choose the plot box tool and apply a plot box at the top of the layers. Do the same at four arbitrary points along the soil layers. Note the height (:math:`H`) you place the plots.

    <insert image>

#. Push the time increment button for about 1 minute.

#. Obtain the angular frequency :math:`(2p/T)`, where :math:`T` is the period i.e. time it takes to complete one revolution.

#. Obtain the maximum displacements from the plots by clicking on the crest of the curves with your cursor.

.. math::
    TF = \frac{1}{\cos(\frac{wH}{v_s})}

    AF = \frac{1}{|\cos(\frac{wH}{v_s})|}


Where

:math:`w` = Angular frequency (2pf)

:math:`H` = distance between any two points in the layers under consideration.

:math:`V` = Velocity of wave travel within the soil layer.

:math:`TF` = Transfer function

:math:`AF` = Amplification function


Dr. Layer's operation is controlled via menu commands (with associated keyboard accelerators), manipulation tools, scaling buttons, the load tool bar, and time control buttons. The program displays the results of its calculations visually in the form of animated displacements, and also in the form of dynamically generated time history plots. There are also mechanisms for getting numerical values.

.. figure:: ./images/case1.png
    :scale: 30 %
    :align: center
    :figclass: align-center


SimCenter Tool Used
-------------------

blablabla

.. list-table:: Title
   :widths: 25 25 50
   :header-rows: 1

   * - Heading row 1, column 1
     - Heading row 1, column 2
     - Heading row 1, column 3
   * - Row 1, column 1
     -
     - Row 1, column 3
   * - Row 2, column 1
     - Row 2, column 2
     - Row 2, column 3

Time can be controlled using either the keyboard or the time control buttons:

* To run time **forward**: Press and hold the 'g' key or click and hold the time forward button: <insert icon>.

* To reset time to **zero**: Type the '0' key or click on the time reset button: <insert icon>.

* The current analysis time is **displayed** in the feedback pane at the bottom of the screen.

* The analysis time step size can be controlled via the Time Step menu (there are combinations of material properties and time steps that intentionally lead to unstable results, so beware).

* The display time step can be controlled via the Animation Speed menu. Internally, this command controls how many analysis time steps are computed between screen updates.


Example Application
-------------------

Dr. Layer's tool palette is illustrated below (Windows version: the Mac version is similar but grouped a bit differently):

<insert tool palette image>

* The **Arrow Tool** is used to select and manipulate objects.

* The **Panner** and **Camera Orbit Tools** are used to change the viewing point and camera orientation via clicking and dragging.

* The **Plot Box Tool** is used to create one of the various types of plot boxes: 

    * **Displacement Time History plots** are created by clicking on the relevant layer. The top node in the layer is used as the plotting target.

    * **Fast Fourier Transform (FFT) plots** of a displacement history can be created by clicking on the time history plot.

    * **Stress-strain plots** can be created by control-clicking (i.e., holding down the control key while clicking) on the desired layer.


 These controls are self-explanatory in regards to their functions. Note the following, however:

.. note::
    The scaling buttons will continue to scale as long as they are held down. It is not necessary to click multiple times to get this effect.


Remarks
-------

* To adjust the **plotting scales**, use the small expansion/contraction triangular buttons on the plot for the horizontal scale, and the plot scale buttons on the `Scale Button Toolbar <#scaling-buttons>`_ for the vertical scale. 

.. note::
    You will notice that all plots scale together. This is so that plots of a given type can be compared visually without any misleading differences in scale factors.

* To adjust the **horizontal offset** of a plot, click in the plot and drag horizontally to scroll back and forth.

.. note::
    In general, plots will automatically scroll as necessary as time is running. Once you have manually scrolled a plot, though, the automatic scrolling will cease until time is reset to zero.

* Plot boxes can be added or removed at any time, but they only accumulate data beginning from the time they are installed, with the exception of FFT plots, which always plot the according to the data accumulated in the target time history. FFT plots can use up to the first 1024 points in a time history.


.. warning:: 
    Plotting FFT's will slow down the animation speed significantly, especially as the length of the time histories increase.


.. bibliography:: references.bib
