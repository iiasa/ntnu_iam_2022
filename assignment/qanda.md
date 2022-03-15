Questions and Answers
=====================
This page summarizes questions and answers related to model and scenario development that emerged during the course.

MESSAGE
-------
- Where does the investment in MESSAGE come from (in a agent/flow perspective)?
  Investments are related to installing new capacities of technologies, basically by multiplying specific investment costs (inv_cost) with newly installed capacity (CAP_NEW).
- How can other emission types be introduced? How does it work with land occupation/conversion and water use?
  New types of emissions can be added to the set emission. If you want to constrain or tax the emission, it needs to be mapped to a member of the set emission_type. The parameter emission_scaling then maps the emission via a scaling factor to an emission_type which can be constrained via bound_emission or priced via tax_emission (see [documentation](https://docs.messageix.org/en/latest/model/MESSAGE/parameter_def.html#parameters-of-the-emission-section). This mechanism also allows aggregating multiple emissions using a conversion metric (e.g. GWP100).
- Do lifetimes + construction times + capacities always have to be defined for technologies?
  It is possible to define technologies without capacities. These technologies only have an activity variable and only operational parameters.
- What is mode M1?
  The set mode allows to define multiple operation modes of technologies. The names of the set members are arbitrary and can either be chosen to describe a specific operation mode or just number them. M1 just means operation mode 1.
- What is "grade"?
  Non-renewable resources (e.g., coal, oil, gas) can be distinguished in grades to construct cumulative supply curves.
- The abbreviation of "level" differs in the spreadsheet, e.g. "level_resource" and "lvl_temporal"
  level_resource and level_renewable are used to map commodities to exaustible and renewable resources, respectively (see [documentation](https://docs.messageix.org/en/latest/model/MESSAGE/sets_maps_def.html#category-types-and-mappings)). MESSAGEix allows to nested hierarchical spatial and temporal structures. The sets lvl_temporal and lvl_spatial are used to construct these hierarchies (see [documentation](https://docs.messageix.org/en/latest/model/MESSAGE/sets_maps_def.html#sets-in-the-messageix-implementation)).
- Would be nice to have an explanation of variables/parameters used in spreadsheet, e.g., what is i_spec or weight_trp (relation) or 90_rel (rating)
  i_spec is specific electricity demand in industry, weight_trp is an equation , and 90_rel is a rating bin relevant for modeling non-dispatchable technologies (e.g., wind, solar, see [documentation](https://docs.messageix.org/en/latest/model/MESSAGE/model_core.html#auxilary-variables-for-technology-activity-by-rating-bins)).
- Can the demand distinction of supply-and-use tables (intermediate+final) be maintained in MESSAGE or is that too deterministic, i.e. can there only be one aggregate demand (marked as useful)? And related to that: What exactly is demand_fixed in the auxiliary commodity_balance? Can all demand levels be set arbitrarily? If so, how, and what would be the implications?
  In principle, additional demands can be specified and existing demand can thus be split into different categories (e.g., intermediate and final supply-and-use table categories). A central question is how to incorporate relevant feedback effects for the intermediate demands in particular.
- In the South Africa Model, there is a technology called gas_ind but I was unable to find this in the technology description file.
  This seems to be a leftover which only has a couple of parameters set for the period 2010 for which the model is not run. So safe to ignore.
-	For tabs bound activity_lo and bound activity_up, some technologies have values until 2070 while some only have for 2020, what is the difference and what is the rational driving this?
  Values of these constraints in 2020 are related to base year calibration. Values going beyond 2020 are used to limit the deployment of technologies related to physical or other limits.
-	Concerning oil, we have loil, foil distinguish as technologies, but in the Data from IEA, there is also crude oil production, and other deratives like kerosene, etc. How does one distinguish this?
  The South Africa model includes three oil-based commodities, crudeoil, lightoil and fueloil. The latter aggregate different oil products rather than presenting them individually. Light oil-based commodities up to diesel are represented by lightoil, and heavier products such as heavy fuel oil, bitumen, etc. are represented by fueloil.

MACRO
-----
- How is GDP calculated/exogenously determined in MESSAGE? Does it relate to any other parameters/variables?
  GDP is calculated endogenously in when running MESSAGEix coupled with MACRO. GDP is the sum of consumption C and (economy-wide) investments I.
- What exactly is price_MESSAGE as given in the spreadsheet?
  price_MESSAGE is prices from a MESSAGEix scenario that MACRO is calibrated to. The equivalent for quantities is demand_MESSAGE.
- Why is cost_MESSAGE predetermined in the spreadsheet? Isn't it a model output?
  cost_MESSAGE is also a parameter used in the MACRO calibration and represents the total system costs of the MESSAGE scenario that MACRO is getting calibrated to.
- How is "sector" in MESSAGE defined? As all non-energy economic sectors?
  sector is not a MESSAGE set, but a MACRO set. sectors in MACRO get mapped to demands in MESSAGE.
- Not all parameters used in the spreadsheet are defined in the documentation: kgdp, kpvs, lakl, lotol, p_ref, prfconst, esub
  These parameters are specific to MACRO and as we skipped MACRO in the course. Their definition is part of the [MACRO GAMS code](https://github.com/iiasa/message_ix/blob/main/message_ix/model/MACRO/macro_data_load.gms) (see lines 53-97), but should be added to the web-based [MACRO documentation](https://docs.messageix.org/en/latest/model/MACRO/macro_core.html).
- How/why were MERtoPPP rates chosen?
  MERtoPPP ratios are also part of MACRO (which works in MER metric) and have been derived from SSP GDP projections.
- What does aeei mean ? And how are the values calculated?
  aeei stands for "autonomous energy efficiency improvement" and is a MACRO parameter that is used to align MACRO behavior to energy demands projected with MESSAGE. In a scenario development cycle, one would develop and energy scenario (typically a baseline) with MESSAGE and then calibrate aeei parameters so that MACRO is consistent with a set of demand and price projections from MESSAGE. Changes of energy prices in MESSAGE as a result of shocks (e.g., emission reduction target) then lead to a demand response in MACRO that is fed back to MESSAGE.
- Labor:
  - Where in the model is labour used and how? Which units are labour-related parameters given? $, h, or # of people?
    Labor enters the [production function](https://docs.messageix.org/en/latest/model/MACRO/macro_core.html#equation-new-production) together with capital and energy. In contrast to the other two factors, labor appears differently in the production function as it is chosen to be the numeraire. In other words, labor supply is exogenously set as a benchmark for the other two production factors via the parameter newlab which is defined as follows:
    newlab(y) = labor(y) - labor(y-1)*(1 - depr)**duration_period(y) with labor(y+1) = labor(y) * (1 + grow(y))**duration_period(y) where depr is the depreciation rate
    The relevant parameter is grow which can effectively be interpreted as labor productivity growth and, everything else (energy prices, demands, etc.) being equal, translates into GDP growth Therefore it is also referred to as potential GDP growth. In practice, MACRO is usually tuned to an exogenously specified GDP path and energy demand/price trajectory from MESSAGE by calibrating the aeei (= autonomous energy efficiency improvement) and grow parameters.
  - Can total labor supply be determined exogenously? (labor supply growth can...)
    As explained above, the parameter grow can be used to do so. In most practical applications of MACRO, the parameter is, however, used to calibrate MACRO to reproduce an exogenously specified GDP trajectory.
  - Is it possible that absolute labor related parameters are missing in the spreadsheet?
    As explained above, grow is the relevant parameter.
- Is there some more detailed, up-to-date documentation of the MACRO model available, e.g. in the form of a paper?
- How is capital depreciation represented? Simply via duration_period? What unit is capital formation given in? $, GWa, or other?
  Capital depreciation is governed by the [total capital equation](https://docs.messageix.org/en/latest/model/MACRO/macro_core.html#equation-total-capital) in MACRO.
- Could you explain how the elasticity of substitution works in MACRO? It appears that only a cross-market, generic, country-specific parameter (esub aka epsilon, included in rho) is chosen instead of distinguishing between capital, labour, energy separately. Does that mean that capital and labour as an aggregate can be substituted by energy, and vice versa? Can high and low skilled labour be substituted? How about introducing another substituent such as non-energy commodities?
  see presentation on 16 March.

Scenario implementation
-----------------------
-	SSP1 has another “energy mix” than SSP2 and how the carbon intensity develops. This is estimated for the Middle East and Africa in the appendix of Van Vuuren et al. 2017 (Energy, land-use and greenhouse gas emissions trajectories under a green growth paradigm). How do I go forward in transferring this to the various energy and emission parameters in the South Africa model? I don’t see how I could use a metric for estimating this as we did with the demand.
The main levers you have for adjusting carbon intensity of energy in an optimization model like MESSAGEix is changing the relative economics of different energy supply options and limiting their deployment. So basically changing the different costs (inv_cost, but also fix_cost and in some cases var_cost) for specific technologies as well as constraining their deployment (e.g., by using bound_activity_up) is what can be done in a straight forward manner. For example, making solar_pv_ppl and wind_ppl cheaper in SSP1 and in turn making the different coal power plants more expensive would be an entry point. You could also make extraction of fossil fuel (coal_extr, gas_extr, etc.) more expensive. Always important to look at all cost components is relevant (e.g., when reducing inv_cost, but keeping fix_cost fixed, at some point the fix_cost dominate and additional reduction of inv_cost does not help much anymore).
-	How to cope with the price (or economic) parameters is the next question. I haven’t found papers discussing this in detail yet. Do these need to be changed?
In addition to the technology costs (see above), you could also adjust the interestrate parameter which changes the relative of all technologies simultaneously. For example, lowering the interest rate works in favor of capital intensive technologies (e.g., renewables, nuclear) while increasing it works in favor of technologies with low investment but high operating costs (e.g., gas turbine power plant – at least in the past). Prices then emerge endogenously from the changes of cost assumptions.
-	You also mentioned that adding/removing technologies could be a necessary step in this assignment. The figure below illustrates the “big picture” of this, but again, I am struggling with how to implement this in the model.
You could add new options that might not be in the base model, e.g., wave energy, hydrogen technologies, or remove or limit the deployment of technologies (e.g., ban of CCS, no new nuclear). As a general rule, constraining existing technologies could lead to infeasibilities, but you can also limit building new capacities of technology (e.g., nuclear) via “bound_capacity_up” parameter for future years (see https://docs.messageix.org/en/stable/model/MESSAGE/parameter_def.html#bounds-on-capacity-and-activity for bounds).
