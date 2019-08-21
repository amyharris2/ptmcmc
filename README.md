# ptmcmc
 Stellar parameter and radial velocity estimation using Parallel Tempering MCMC

Description:

	This module uses Parallel Tempering Markov Chain Monte Carlo (ptmcmc) sampling in order to estimate 
	stellar parameters (Teff, logg, vsini) and radial/line-of-sight velocities from observed spectra. 
	This is achieved by comparing the observed spectrum with a set of synthetic spectra templates. The 
	templates are directly mapped onto the unnormalised spectrum, avoiding tricky continuum fitting. 


Files included:

	- ptemcee-1.0.0.zip file (77KB)
		- this is the 'ptemcee' python module that is used in ptmcmc.py for the mcmc sampling (see
		https://github.com/willvousden/ptemcee for details, although the version provided here is 
		older and I have slightly modified it). 

	- ptmcmc.py 
		- this is the ptmcmc module that computes stellar parameters of input spectra. Some inputs 
		are required, see below. The ptmcmc runs with the provided template set as default, but it 
		can process (crop, rotationally broaden, smooth and rebin) your own templates, and put the 
		processed templates into a grid for use with ptmcmc. See parameters.py. 

	- parameters.py
		- this is where the parameters for the ptmcmc module are stored. In the most basic case, 
		only the path to a list of spectra to be processed, and the path to a write directory, are 
		required. The rest of the parameters are suitable for the default case of analysing the CaT
		region of WEAVE-like LR spectra - tweak if you desire.

	- example folder (1.92MB)
		- targets folder including 6 fits files of required form for input, and a list of these.  
		- results folder, initially empty.
		
Additional file to be downloaded from http://star.herts.ac.uk/~amy/PTMCMC/templates/:

	- templates.zip (288MB)
		- 1134 templates smoothed to a FWHM ~ 1.3A and rebinned to 0.25A sampling, covering 
		6000 - 9000 A.
		- template_grid.npy is a table containing the flux data of all templates with 
		5000 =< Teff =< 18000, 2.5 =< logg =< 5, 0 =< vsini =< 300.



Installation: 
	
	1) Download or clone this repository and unpack.
	2) Download templates.zip from http://star.herts.ac.uk/~amy/PTMCMC/templates/ and unpack. Put the 
	templates/ folder into the unpacked repository from step 1.
	3) Ensure required software is installed, including the provided 'ptemcee' module (see below).
	4) ptmcmc.py can now be run directly from the terminal.
	
	Required software:

		- Python (tested and working on both 2.7 and 3.5)

		- Python modules:
			- numpy 		(tested and working using v1.17.0. Must be at least v1.14.0. 
						 Install before ptemcee installation)	
			- scipy 		(tested and working using v1.3.1)
			- matplotlib 		(tested and working using v3.0.3)
			- astropy 		(tested and working using v3.2.1)
			- tqdm 			(tested and working using v4.33.0)
			- PyAstronomy 		(tested and working using v0.13.0. Only required if you are 
						 processing your own templates)
			- The ptemcee module 
				- use provided version (the version available at 
				https://github.com/willvousden/ptemcee is currently not compatible)
				- unpack and install using 'python setup.py install' command


Usage:

	The inputs:
		The following are the minimum required inputs to be entered in parameters.py:
			- the path to a list of target spectra to be analysed. The list file should include
			paths to spectra.
				- target spectra should be fits files, similar to the OpR3 spectra files. The
				following columns are required:
					- 'wavelength'
					- 'final_spectra'
					- 'spectra_before_sky_subtraction'
					- 'Calibration_function'
					*NOTE: THE FLUX DATA SHOULD BE UNNORMALISED*
			- the path to the desired write directory 


	To run the example:
		- the default parameters for the ptmcmc.py module are those for the example.
		- ptmcmc.py must be run from within the ptmcmc-package directory in the case of the example.
		- once the results have been written, ptmcmc.py will not re-run unless the results are deleted.


	The outputs:
		The outputs from running ptmcmc.py are written to the specified write directory, and consist 
		of the following for each analysed spectrum:
			- <SPECTRUM>_result (in <WRITE_DIRECTORY>/results/)
				This is a text file containing the results for the analysed spectrum. The 
				columns are as follows:
				(1)  name: the analysed spectrum
				(2)  acceptance fraction: the mean fraction of proposed walker jumps that are accepted, should be between 0.2-0.5 for efficient sampling
				(3)  Teff: median value of marginalised posterior distribution for effective temperature (K)
				(4)  Teff_minus: 1-sigma negative uncertainty on Teff (K)*, i.e. Teff +/- Teff_plus / Teff_minus
				(5)  Teff_plus: 1-sigma positive uncertainty on Teff (K)**, i.e. Teff +/- Teff_plus / Teff_minus
				(6)  logg: median value of marginalised posterior distribution for surface gravity log(g)
				(7)  logg_minus: 1-sigma negative uncertainty on logg*, i.e. logg +/- logg_plus / logg_minus
				(8)  logg_plus: 1-sigma positive uncertainty on logg**, i.e. logg +/- logg_plus / logg_minus
				(9)  vsini: median value of marginalised posterior distribution for projected rotational velocity (km/s) 
				(10) vsini_minus: 1-sigma negative uncertainty on vsini (km/s)*, i.e. vsini +/- vsini_plus / vsini_minus
				(11) vsini_plus: 1-sigma positive uncertainty on vsini (km/s)**, i.e. vsini +/- vsini_plus / vsini_minus
				(12) RV: median value of marginalised posterior distribution for radial/line-of-sight velocity (km/s)
				(13) RV_minus: 1-sigma negative uncertainty on RV (km/s)*, i.e. RV +/- RV_plus / RV_minus
				(14) RV_plus: 1-sigma positive uncertainty on RV (km/s)**, i.e. RV +/- RV_plus / RV_minus
				(15) slope: median value of marginalised posterior distribution for slope of mapping function 
				(16) slope_minus: 1-sigma negative uncertainty on slope*, i.e. slope +/- slope_plus / slope_minus
				(17) slope_plus: 1-sigma positive uncertainty on slope**, i.e. slope +/- slope_plus / slope_minus
				(18) intercept: median value of marginalised posterior distribution for intercept of mapping function
				(19) intercept_minus: 1-sigma negative uncertainty on intercept*, i.e. intercept +/- intercept_plus / intercept_minus
				(20) intercept_plus: 1-sigma positive uncertainty on intercept**, i.e. intercept +/- intercept_plus / intercept_minus

				* calculated as the median less the 16th percentile of the marginalised posterior distribution
				** calculated as the 84th percentile less the median of the marginalised posterior distribution

				All <SPECTRUM>_result files can be easily concatenated to one big table using e.g. the following commands in <WRITE_DIRECTORY>/results/ 
				UNIX:
					'cat *_results > <BIG_TABLE_NAME>'
				WINDOWS:
					'type *_results > <BIG_TABLE_NAME>'

			- <SPECTRUM>_spectrum.png (in <WRITE_DIRECTORY>/plots/)
				This is a plot of the analysed region of the spectrum, along with its best-fit template match which has been mapped onto the spectrum.

			- <SPECTRUM>_walkers.png (in <WRITE_DIRECTORY>/plots/)
				This is a plot of the walker paths after the burn-in phase. They should converge as the step number advances. 

			- <SPECTRUM>_mapping_function.png (in <WRITE_DIRECTORY>/plots/)
				This is a plot of the mapping function used to project the template onto the spectrum, along with the spectrum divided by its best-fit template.

	Multiprocessing:
		The option of processing multiple spectra simultaneously is available via pooling (see
		https://docs.python.org/3.4/library/multiprocessing.html). If you run 32 processes, 32 
		spectra will be analysed simulatanously. When one is finished, a new spectra will begin
		to be analysed, until all spectra have been analysed. This is particularly useful on large
		cluster machines.

	Analysed wavelength region:
		The default parameters analyse a continuous wavelength region of 8470-8940A, covering the
		calcium triplet (CaT) region. The analysis of this region has proven successful for stellar
		parameter determination of BA stars using low-resolution (R~4000) spectra. It is possible to
		'exclude' portions of your wavelength region (see parameters.py), for example to remove a
		region that contains a diffuse interstellar band. This feature could be exploited in order to
		include far-away lines, for example to include the analysis of the H-alpha line alongside the
		analysis of the CaT region. However, you must consider whether a linear mapping function 
		remains appropriate when covering extensive wavelength regions (in the case of H-alpha + CaT,
		it is not appropriate). 

		In order to successfully include the analysis of multiple regions that span a large wavelength 
		range, the code must be altered. If you would like to analyse two separate regions, a separate 
		mapping function can be applied to each region, and the resulting likelihoods can be summed in
		order to get a total likelihood for the specific template. In this case, two extra free 
		parameters must be implemented, i.e. the slope and intercept of the mapping function for the 
		additional region. 

	Template choice:
		- The provided set:
			The templates that come with this package have been provided by Dr. Marwan Gebran. They
			were calculated using the approach of Gebran et al. (2016) and Palacios et al. (2010),
			using SYNSPEC48 (Hubeny and Lanz, 1992) to calculate the spectra based on ATLAS9 model
			atmospheres (Kurucz, 1992), which assume local thermodynamic equilibrium, plane parallel
			geometry, and radiative and hydrostatic equilibrium. 

			The template set provided have span the following parameter space:
			Teff: 5000-18000 K in steps of 500 K
			logg: 3.0-5.0 in steps of 0.5
			vsini: 0-300 km/s in steps of 50 km/s

			They were calculated assuming Solar abundances and they have a resolution of R=10000. 

			The templates provided have been cropped (6000-9000 A), rebinned (sampling=0.25 A) and 
			smoothed (sigma=0.48). The rebinning and smoothing are done in order to closely match the
			templates to typical WEAVE LR spectra in the CaT region.

		- To use your own templates:
			The option to use your own template set is available. This module can process (crop, rebin,
			smooth, rotationally broaden) your provided template set and put the processed flux data 
			into the required grid shape ready for ptmcmc sampling. It is also possible to provide your
			own templates that do not require processing/putting into grid. 

			Provided templates should be text files including wavelength and unnormalised flux/counts 
			data. No column titles should be present.

			The grid has shape (len(Teff), len(logg), len(vsini), len(wavelength)). The grid must be 
			complete and square i.e. there must be a template for each combination of parameters. 


			- Templates that require processing:

				If you want to process your own templates for use with the ptmcmc module, alter 
				the parameter file accordingly. The templates will be processed and written. The
				processed flux data will be put into a grid for use with ptmcmc sampling. Please
				ensure the PARAMETER BOUNDARIES in parameters.py reflects the template grid provided:
				the grid should be 100% full.

				The following options are available for processing:
					- Cropping: this crops the wavelength and flux columns such that 
					template_min_wav < wavelength < template_max_wav. 
					- Rebinning: this rebins your wavelength and flux data such that the binned
					fluxes are the mean value of the bins (see
					https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.binned_statistic.html),
					and the binned wavelength is the centre value of the bin. 
					- Smoothing: this broadens/smooths your flux data such that it is possible to
					match the resolution of the template to the observed spectra. The template is
					broadened with a gaussian kernel of width/standard deviation = sigma (see 
					https://www.hs.uni-hamburg.de/DE/Ins/Per/Czesla/PyA/PyA/pyaslDoc/aslDoc/broad.html).
					- Rotational broadening: this broadens your flux data in accordance with that
					from rotational broadening (see 
					https://www.hs.uni-hamburg.de/DE/Ins/Per/Czesla/PyA/PyA/pyaslDoc/aslDoc/rotBroad.html). 
					A linear limb-darkening coefficient of 0.6 is used.

				After templates are processed and put into grid form, the ptmcmc sampling will 
				automatically run using your provided templates. 

			- Templates that don't require processing

				If you have a set of templates that do not require processing, and you have a .npy 
				file (named 'template_grid.npy') with the gridded flux data, you can use these with
				the ptmcmc module by putting the grid file in the templates/ folder, along with (at
				least) one template. The template will be used for the wavelength data, so all used
				templates must have the same wavelength data, and the provided template must have the
				corresponding wavelength data for the gridded flux data. 
