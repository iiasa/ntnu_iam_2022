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

Scenario implementation
-----------------------
-	SSP1 has another “energy mix” than SSP2 and how the carbon intensity develops. This is estimated for the Middle East and Africa in the appendix of Van Vuuren et al. 2017 (Energy, land-use and greenhouse gas emissions trajectories under a green growth paradigm). How do I go forward in transferring this to the various energy and emission parameters in the South Africa model? I don’t see how I could use a metric for estimating this as we did with the demand.
The main levers you have for adjusting carbon intensity of energy in an optimization model like MESSAGEix is changing the relative economics of different energy supply options and limiting their deployment. So basically changing the different costs (inv_cost, but also fix_cost and in some cases var_cost) for specific technologies as well as constraining their deployment (e.g., by using bound_activity_up) is what can be done in a straight forward manner. For example, making solar_pv_ppl and wind_ppl cheaper in SSP1 and in turn making the different coal power plants more expensive would be an entry point. You could also make extraction of fossil fuel (coal_extr, gas_extr, etc.) more expensive. Always important to look at all cost components is relevant (e.g., when reducing inv_cost, but keeping fix_cost fixed, at some point the fix_cost dominate and additional reduction of inv_cost does not help much anymore).
-	How to cope with the price (or economic) parameters is the next question. I haven’t found papers discussing this in detail yet. Do these need to be changed?
In addition to the technology costs (see above), you could also adjust the interestrate parameter which changes the relative of all technologies simultaneously. For example, lowering the interest rate works in favor of capital intensive technologies (e.g., renewables, nuclear) while increasing it works in favor of technologies with low investment but high operating costs (e.g., gas turbine power plant – at least in the past). Prices then emerge endogenously from the changes of cost assumptions.
-	You also mentioned that adding/removing technologies could be a necessary step in this assignment. The figure below illustrates the “big picture” of this, but again, I am struggling with how to implement this in the model.
You could add new options that might not be in the base model, e.g., wave energy, hydrogen technologies, or remove or limit the deployment of technologies (e.g., ban of CCS, no new nuclear). As a general rule, constraining existing technologies could lead to infeasibilities, but you can also limit building new capacities of technology (e.g., nuclear) via “bound_capacity_up” parameter for future years (see https://docs.messageix.org/en/stable/model/MESSAGE/parameter_def.html#bounds-on-capacity-and-activity for bounds).
