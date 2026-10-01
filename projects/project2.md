# IMSA GTDPRO Tire Degradation Analysis

## Problem and Dataset

### Research Question

How does tire age affect lap time in IMSA GTD Pro, and does a nonlinear model (gradient boosting) capture that effect better than a linear one (linear regression)?

### Dataset

The dataset used is from the lap-by-lap timing data from the IMSA WeatherTech SportsCar Championship, from the toby/imsa_data GitHub repo, which was cloned locally.
There is one race-laps CSV per race and it is organized by year and event spanning from 2021 to 2026. There were 42 files available to use and within each one there is data for 
Identity (car number, driver number and name, team, manufacturer, class), Timing (lap time, the three sector times, speed (KPH and top speed), elapsed time, time of day), and Race Status (lap number, flag status at the finish line (green, full-course yellow, finish), whether the car crossed the line in the pits, pit time).

## Context and Supporting Research

### Context

In IMSA endurance racing, teams have to decide how long to run each stint before pitting, and tire wear is a major part of that decision. This project uses lap-by-lap timing data from IMSA's GTD Pro class (GT3 based cars with all professional driver lineups) to see how lap time changes as the tires age
, and whether a nonlinear model describes that change better than a linear one.

### Supporting Research

Friedman, J. H. (2001). Greedy function approximation: A gradient boosting machine. The Annals of Statistics, 29(5), 1189–1232. https://doi.org/10.1214/aos/1013203451

Heilmeier, A., Thomaser, A., Graf, M., & Betz, J. (2020). Virtual strategy engineer: Using artificial neural networks for making race strategy decisions in circuit motorsport. Applied Sciences, 10(21), 7805. https://doi.org/10.3390/app10217805

Pedregosa, F., Varoquaux, G., Gramfort, A., Michel, V., Thirion, B., Grisel, O., Blondel, M., Prettenhofer, P., Weiss, R., Dubourg, V., Vanderplas, J., Passos, A., Cournapeau, D., Brucher, M., Perrot, M., & Duchesnay, E. (2011). Scikit-learn: Machine learning in Python. Journal of Machine Learning Research, 12, 2825–2830.

## Data

### Data Preparation and Feature Selection

<img width="2646" height="812" alt="image" src="https://github.com/user-attachments/assets/01799304-79f6-4439-867a-79b91dd67b1b" />

When importing the data, there was a duplicate semicolon delimited column that I used the function "load_clean_csv" to skip that column. I then added columns "year" and "event" from the parent files onto my dataframe.

<img width="2658" height="1308" alt="image" src="https://github.com/user-attachments/assets/187a56e7-0970-4200-a068-a3626c8782f9" />

On the original dataset, the time features were setup as strings. I created the function "to_seconds" to covert those stings to float seconds. I then dropped any lap without a useable time or number.
I use the describe to look at the spread of the lap time data and to see if there are any outliers that will skew the model. The max is showing a lap time over 1000 seconds which needs to get trimmed out.

<img width="2650" height="938" alt="image" src="https://github.com/user-attachments/assets/22116913-fa17-49df-bfdd-7a741f584307" />

Here I am getting rid of any laps that will create noise in my models. Those laps were any that were listed as full course caution or green at the finish line since they would not be laps at race pace. I also removed any lap where the car crossed the finish line in the pits and created a list for any out lap which were the "is_pit" lap shifted forward by 1. I then created "fcy_prev" and "fcy_next" using the same method.

<img width="2650" height="1394" alt="image" src="https://github.com/user-attachments/assets/a58e6633-57e5-4785-9a7e-01f5f1d24fe1" />

I create a new stint and tire age feature for the model. The stint is the number of laps completed between pits and tire age just about the same thing, but it tells how many laps the tires on the car have been ran for. I then put together all of the cleaned data into a new dataset.

## Model Development

### Scaling

<img width="2698" height="1428" alt="image" src="https://github.com/user-attachments/assets/476d8be6-f44a-444f-84e2-3ef9c3f93e31" />

Since each track has different lap times and lengths, a 5 second difference at a very short track does not mean the same as a 5 second difference at very long track. I turned the lap times into a ratio by dividing each lap by the track's median lap time. This makes a 1% slowdown at Detroit and a 1%
slowdown at Daytona equal. This helps because tire wear tends to cost the same share of lap time no matter the track. With keeping the lap times the raw seconds, the model would use its effort to try and learn which track each lap came from instead of what the tires are doing.

### Training

<img width="2700" height="1210" alt="image" src="https://github.com/user-attachments/assets/2a4eb433-779a-46e6-ad9e-cb30c2e5c0ab" />

For the model I chose to do an 80/20 split for training and testing. While this is a common split, it left me with around 15,000 laps for testing because the data set was so large.
The split is by stint and not single laps; that was to keep laps from same stint from being in both sets.

### Baseline Performance

<img width="1254" height="120" alt="image" src="https://github.com/user-attachments/assets/d27aba7e-1716-4278-bce2-a4a2e7d281f5" />

<img width="296" height="34" alt="image" src="https://github.com/user-attachments/assets/a85464f7-fd40-4859-9158-d8ca9bf7b811" />

For all of the models, they were scored with MAE (mean absolute error) which calculates the average difference between predicted and actual lap time ratio.
The baseline test is the simplest model used to predict lap time from the training set. For my baseline MAE I got .712% and any improvement beyond that shows how much better the features help in consistently predicting the laptimes.

### Model Development and Comparison

I trained two models to predict lap time as a ratio of the car's median lap in that race. linear regression gives one number for how much the lap time changes with tire age. Gradient Boosting is the flexible model. It can follow a curve and interactions between features. That matters if tire wear is not constant over a stint.

### Model Evaluation

Both models use the same inputs: tire age, lap number, manufacturer, and event. The numeric features were scaled, and categorical features were one-hot coded, so both models see the same data. Boosting used max depth 4, learning rate 0.05, and 300 iterations.

<img width="768" height="160" alt="image" src="https://github.com/user-attachments/assets/65e9295e-4407-4486-812e-cddb2e702c1e" />

### Model Interpretation

Both models beat the baseline, boosting by about 6% and linear by about 2%. The linear model estimates that tire age cost about .01% of lap time per lap. That is roughly .3% over a 30-lap stint. Boosing's advantage suggests that the effect is not linear.

<Figure size 800x500 with 1 Axes><img width="703" height="468" alt="image" src="https://github.com/user-attachments/assets/e0f9a19e-3829-4023-8798-313d25f692c6" />


This chart shows how the two models think lap times change as tires age. For each tire age from 1 to 45, every lap in the test set is treated as if it were at that age, and the model's predictions are averaged. Each line is then shifted to start at zero at lap 1.

The Linear (blue) shows a straight line rising to about +.4% by lap 45. It can only show one slope, about .01% per lap.

Boosting (orange) drops to about -.58% by lap 6, then climbs to about -.30% by lap 26, then stays flat, with a small rise after lap 33.

Laps 1 to 6: lap times improve, probably from tire warm-up and fuel burn.
Laps 6 to 26: lap times slow by about 0.27%, which is the wear signal.
After lap 26: flat, with no sharp fall-off.

## Reflection and Transparency

### Ethics and Limitations

The data has no tire compound, tire set, fuel load, or weather, so tire age (laps since the last pit stop) is only a proxy for tire wear. It assumes every pit stop included a tire change, which the data doesn't confirm, and it overstates wear when a stint includes cautions. Fuel burn and tire wear also overlap, since a car gets lighter as its tires wear, so the measured slowdown (about 0.3% over a typical stint) probably understates the tire effect alone. Tire age explains under 10% of the variation in lap time, so most of what determines a lap (driver, traffic, track conditions, mistakes) isn't captured. The data comes from a community repository of IMSA timing data.

Cleaning choices shape the result. Only green-flag laps were kept, so wear under caution isn't shown. Laps more than 7% slower than the event median were removed, and that could include laps where a tire had failed, which biases the analysis against finding a sharp fall-off. Long stints are mostly run by cars whose tires are holding up, so the late part of the degradation curve reflects survivors and looks better than it would for an average car. The data covers only professional GTD Pro lineups, so the results may not apply to amateur drivers or other classes. Manufacturer and car effects also reflect team quality and series balance-of-performance rules, not just the cars themselves. The data spans 2021 to 2026, during which regulations and tire supply may have changed.

This model isn't safety-critical, but wrong predictions could still cost a team. If it understates wear, a team might run a stint too long and lose time on old tires. If it overstates wear, a team might pit early and give up track position. Because the model explains little of the variation, its predictions for an individual lap are uncertain, and treating them as precise would be overconfident. Using manufacturer coefficients to rank cars could also unfairly credit or blame a brand for effects that come from its team or the rules.

This model isn't ready for strategy decisions. A team would need tire compound and set information, fuel load, traffic, and weather, and would have to test it on a full season it hasn't seen. It's better suited to describing average behavior across the field, and any use for decisions should be one input to human strategists, not a replacement. Because it's trained only on past pro racing, it should be rechecked whenever tires or regulations change.

### Code and AI Transparency

[Project 2 Notebook](projects/Project2.ipynb)

Anthropic. (2026). Claude [Large language model]. https://claude.ai

Hunter, J. D. (2007). Matplotlib: A 2D graphics environment. Computing in Science & Engineering, 9(3), 90–95. https://doi.org/10.1109/MCSE.2007.55

IMSA. (2025, March 13). IMSA reveals 2026 WeatherTech Championship, Michelin Pilot Challenge schedules. https://www.imsa.com/news/2025/03/13/imsa-reveals-2026-weathertech-championship-michelin-pilot-challenge-schedules/

McKinney, W. (2010). Data structures for statistical computing in Python. In S. van der Walt & J. Millman (Eds.), Proceedings of the 9th Python in Science Conference (pp. 56–61). https://doi.org/10.25080/Majora-92bf1922-00a

Pedregosa, F., Varoquaux, G., Gramfort, A., Michel, V., Thirion, B., Grisel, O., Blondel, M., Prettenhofer, P., Weiss, R., Dubourg, V., Vanderplas, J., Passos, A., Cournapeau, D., Brucher, M., Perrot, M., & Duchesnay, E. (2011). Scikit-learn: Machine learning in Python. Journal of Machine Learning Research, 12, 2825–2830.

tobi. ([year of last update]). imsa_data [Data set]. GitHub. https://github.com/tobi/imsa_data














