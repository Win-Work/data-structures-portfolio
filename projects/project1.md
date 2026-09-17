# NASCAR Stage Caution Pit Analysis

## Problem Definition

### Research Question

Does pitting during a stage-ending caution (vs. staying out to collect stage points) correlate with better final finishing position, and does this differ by track type?

### Context

  A NASCAR race is split up into 3 smaller "races" composed of stages. Each race will have 1 to 3 stages including the final stage and it depends on the length of the race. Usually, the first and second stages will be fairly short compared to the final stage which will span for around the entire second half of the race. After each stage, there is a short period of caution where the pace car comes out, and teams can choose whether to bring their cars into the pits to change tires and/or refuel the car.

  This call is usually judged by the team's predetermined strategy going into the race; however, teams often change strategies due to various reasons. One example could be a team skipping a stage pit to run a driver on a longer stint for end-race track position which risks running out of fuel or tire if not already adjusted for that. My research question is relevant by looking to find whether there is a correlation between the team choosing to pit their cars or not during the stage caution or are there more forces at work that skew answer than just choosing between the two. Hopefully my finding is able to guide strategists into making an easier decision on whether to pit their driver or not.

## Data Definitions

### Key Variables

- driver_name
- starting_position
- race_id (unique id given to each race for use in the library)
- track_type (short track, intermediate, superspeedway, road course)
- caution_type (described the reason for each caution and when, only used "competition")
- start_lap/end_lap (used to determine the caution window)
- lap
- driver_name

### Data Source

The data source I used is from a python package called "pynascar" (github.com/ab5525/pynascar). The package retrieves the race data NASCAR's internal data feeds (cf.nascar.com/cacher/). It is the same backend that runs NASCAR.com's live results and stat pages. The data source includes multiple datasets, but the main ones I used are "race.results" (final results, stage-by-stage results, caution flag logs, qualifying data) and "race.telemetry" (lap-by-lap timing, pit stop records, and flag/event logs). The scope of my project is pulling the from the 2026 NASCAR Cup Series (series_id 1) regular season races, race_ids 5593–5623 (30 races total), with race data (race name, track type) from pynascar's Schedule class.

## Data Cleaning

### Preperation

In order to understand the structure, I pulled the full 2026 schedule dataset:

<img width="2738" height="1642" alt="Screenshot 2026-09-16 193241" src="https://github.com/user-attachments/assets/aedcc703-8a7d-4a28-ad8d-c5f631afdccf" />

Then I got only the finished races to use for my data visualizations:

<img width="2730" height="1654" alt="image" src="https://github.com/user-attachments/assets/ce194502-7c87-43b3-88a2-d598cfbf48fb" />

### Cleaning

To find the variables and the datasets I needed, I went through the single race process of finding the specific per-race stats I need for the visualizations. The original dataset had the drivers who did not in starting_position 0, so I filtered it out all the drivers who did = 0. I then removed all of the columns that I did not need to calculate average finishing positions. 

<img width="2792" height="1452" alt="image" src="https://github.com/user-attachments/assets/a199ebe8-42f2-4dd1-a6d8-9501eb559051" />


I then pulled the Daytona 500 caution dataset to figure out what the stage caution was listed as a variable. I found it listed as "competition".

<img width="2712" height="740" alt="image" src="https://github.com/user-attachments/assets/c814127d-dfef-4ad4-aac1-8bec5a76ef3b" />

Here I am creating a new stage_caution variable and filtering it to only those listed "competition" since that is the stage causion. I am then constraining it to only the start lap and end lap of the caution and dropping every row besides lap and driver_name.

<img width="2748" height="1184" alt="image" src="https://github.com/user-attachments/assets/2f0da121-34b5-41a1-984b-6b677962eb0e" />

Lastly, before the visualizations, I do the same filtering and constrains as above but I print who is pitting or staying out for an easier way to see that data.

<img width="2798" height="836" alt="image" src="https://github.com/user-attachments/assets/f3b3ef6d-ba89-4d94-860a-c97fb0d1804b" />

## Visualizations

I created 2 charts using matplotlib:

<img width="1389" height="590" alt="image" src="https://github.com/user-attachments/assets/f7512db9-34e5-46a0-bcf7-894390d89ce1" />

This chart shows the average finishing position of drivers who pitted during the stage ending caution versus those who stayed out, across each race in the season. Drivers who pitted show a very consistent trend, usually finishing in a narrow spread regardless of the race. Drivers who stayed out show much higher volatility, sometimes finishing far better than the pitted group, sometimes far worse. This suggests that staying out during the stage caution is a higher-risk, higher-variance strategy compared to the predictability of pitting.

<img width="989" height="590" alt="image" src="https://github.com/user-attachments/assets/00ddc50d-04ab-4c74-b68b-0d54b84bba2b" />

This chart shows the difference between the average finishing positions for drivers who stayed out vs pitted at each track type: Short Track, Intermediate, Superspeedway, and Road Course. A negative value (in green) means pitting produced a better average finish; a positive value (in red) means staying out produced a better average finish. This reveals that the pit strategy is not consistent across each track type. At short track pitting during a stage caution shows a large advantage (about 12 places better on average). Although, staying out at the other 3 options shows a slight advantage (2.5 to 5 places better on average). This directly answers the "does this differ by track type" part of the research question. The data shows that track type does play a role in whether pitting or staying out is the better choice.

## Storytelling and Narrative

This project set out to answer whether pitting during the caution period following a stage end versus staying out affects a driver's final race position, and whether that effect differs by track type.

The first chart tracked finishing positions across all races of the sample. It showed a clear difference in consistency between the 2 strategies. Drivers who pitted during the stage caution finished in a tight, predictable spread in nearly every race. Drivers who stayed out were far more volatile, with average finishes ranging from strong to poor depending on the race. This suggests that pitting is the lower-risk choice, while staying out is a higher-variance strategy that can pay off big or backfire badly.

The second chart broke this pattern down by track type, and the story became more nuanced. Rather than one strategy being outright better, the advantage swapped depending on where the race was held: pitting showed a clear advantage at short tracks, while staying out showed a smaller advantage at intermediate tracks, superspeedways, and road courses. This shows that track type is a meaningful factor in which strategy tends to pay off. The answer to "should you pit or stay out" depends on where you're racing, not just whether you're racing.

## Limitations, Ethics, and Reflection

This dataset captures what strategy a driver chose and how they finished, but not why. It has no information on a driver's championship/playoff standing at the time of the race, team radio communications, real-time tire wear, fuel window constraints, or other in-race factors that likely influence a crew chief's pit-or-stay decision. Without these variables, the analysis can find patterns but cannot explain the reasoning behind the strategy calls.

What biases or collection gaps exist in the data:
- Sample imbalance by track type: The comparison between strategies is far more balanced at some track types than others. Short tracks had 215 "pitted" samples but only 6 "stayed out" samples; road courses showed the reverse imbalance (17 pitted vs. 94 stayed out). This means the short-track and road-course findings rest on very small samples for one group, and may be disproportionately influenced by a handful of drivers rather than reflecting a broad pattern.
- Selection bias in the pit/stay decision itself: The decision to pit or stay out is not random — drivers already running near the back of the field may be more likely to stay out since they have less to lose, which could make the "stayed out" group's results look worse than the strategy itself actually is. This limits how directly the results can be attributed to the strategy choice alone.
- Data source reliability: pynascar is a community-maintained, unofficial package built on reverse-engineered NASCAR data feeds.During data collection, 4 of 30 targeted races (5593, 5595, 5601, 5608) failed to return data due to server errors and had to be ignored, reducing the final sample to 27 races.
- Non-points events: The initial race ID range included at least one non-points exhibition event (the NASCAR All-Star Race) and a qualifying event (the Daytona Duel), which do not carry the same stakes or strategic incentives as points-paying races. I ended up keeping the results in the notebook due to wanting to keep the entire dataset I had together.

What would I explore next with more time or data?

With additional time, this analysis could be strengthened by:
-Adding driver playoff/points standing at the time of each race, to test whether risk for the "stay out" strategy actually correlates with championship position, rather than treating that as speculation.
-Controlling for starting position or running position at the time of the caution, to separate the effect of the strategy itself from the effect of who tends to choose each strategy.
-Expanding the dataset across multiple seasons to build more balanced samples for track types (like short tracks and road courses) that were underrepresented on one side of the comparison in this single-season sample.
-Investigating stage-by-stage effects separately (Stage 1 caution vs. Stage 2 caution) rather than combining all "Competition" cautions together, since strategic incentives may differ depending on how much of the race remains.

## Code and References
- 



