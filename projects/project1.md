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
