Assignment
==========

- Option 1: Implement new energy or material commodity supply chains to the MESSAGEix South Africa model
- Option 2: Calibrate the MESSAGEix South Africa model to alternative Share Socio-economic Pathways other than SSP2
- Option 3: Calibrate the MESSAGEix South Africa model to another country based on

Option 1
--------

Option 2
--------

Option 3
--------
The assignment consists of developing a country model based on the South Africa model, develop SSP2 baseline scenario
for that country, implement a couple of relevant short-term energy policies (e.g., renewable capacity target, efficiency
target, but no explicit climate policy) and two climate targets consistent with RCPs 2.6 and 4.5. Based on the resulting 
scenario set (baseline, current polices, RCP 4.5, RCP2.6) write a "half paper" that focuses on implementation and results
of the analysis. Document everything and commit code, paper and data to repository.

- Modeling work based on South Africa
  - Recalibration of model to different country (base year activity, base year capacity, renewable and fossil resources, techno-economic parameters, see [resources](../resources/) for relevant data sets)
  - Add additional technologies (as needed for base year calibration or relevant for future scenarios)
  - Add additional commodity or emission (e.g., water, materials, LCA indicators)
- Develop scenarios
  - SSP2 (baseline)
  - implement 2-3 short-term energy policies (see [Climate Policy Database](http://climatepolicydatabase.org/))
  - RCPs: 4.5/2.6
- Commit code and data (incl. documentation)
- Write a “half-paper” focusing on describing what has been done and results, including a reflection of realism of results and limitations of model (literature review, framing and motivation not needed)

Timeline
--------
- Final assignment of tasks: 23 February 2022
- Submission of half paper, code, documentation and data (via pull request): 31 March 2022

Additional information
----------------------
- The most important model parameters to adjust/review as part of the recalibration are:
  - historical_new_capacity: Average annual capacity additions for historical periods prior to the first modeling period (i.e. 1955 - 2015 in five year steps).
  Note that the period names always refer to the last year of a period, i.e. 2015 denotes the period 2011-2015.
  - historical activity: Activity for historical periods. This is important for the last historical period (2015) in particular as a basis for
  [dynamic market penetration constraints](https://message.iiasa.ac.at/en/stable/model/MESSAGE/model_core.html#dynamic-constraints-on-market-penetration).
  - bound_activity_up/lo: Upper/lower constraints on annual activity of technologies.
  - bound_new_capacity_up/lo: Upper/lower constraints on average annual capacity additions of technologies.
  - bound_total_capacity_up/lo: Upper/lower constraints on total installed capacity of technologies.
  - growth_activity_up/lo: Upper/lower dynamic market penetration constraints activity growth rate.
  - initial_activity_up/lo: Upper/lower dynamic market penetration constraints initial annual activity addition.
  - growth_new_capacity_up/lo: Upper/lower dynamic market penetration constraints new capacity addition growth rate.
  - initial_new_capacity_up/lo: Upper/lower dynamic market penetration constraints initial annual capacity addition.
  - renewable_potential: Resource categories of renewable energy sources.
  - renewable_capacity_factor: Corresponding resource quality (capcity factor) of renewable energy sources.
  - resource_volume: Volume of exhaustible resources.
  - demand: Useful energy demand.
