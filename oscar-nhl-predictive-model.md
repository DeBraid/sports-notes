## Prediction Model Oscar

Posted on October 6, 2015, by [Micah Blake McCurdy](http://hockeyviz.com/txt/oscar/oscar.html).

My single-game prediction model for 2015-2016 is called Oscar. It replaces last year's model, called Pip, which I described at Silver Seven Sens. Where Pip used only recent 5v5 shots and home ice advantage, Oscar also uses individual goaltending terms, team scoring rate terms, and some special teams elements. The quick explanation of what is and is not included is first, the details follow.

What is included:
Recent 5v5 unblocked shot generation;
Recent 5v5 unblocked shot suppression;
Lifetime 5v5 goaltender save percentage, diluted;
Recent 5v4 shot generation;
Recent 4v5 shot suppression;
Recent team 5v5 goals per shot-on-goal ("shooting percentage");
and Home ice advantage.
What is not included.
Wins or losses;
Penalties drawn or taken;
Hits or fights;
Giveaways of takeaways;
or any individual or team award.
The only one of these features for which I have any plans of inclusion in a future model is penalties drawn and taken.

Features and Output
Given a single game, Oscar predicts the probability with which the home team will win the game based on recent measurements of the skill of the two teams. It does not predict the winner of games; that is, it is not designed for classification. It was trained on all regular-season games from 2008-2015.

Oscar has thirteen features, or inputs. These inputs are regularized, that is, each measurement is expressed as a number of standard deviations above or below the mean of that variable, as measured in the training data.

5v5 Shot measures
Measure	Model Coefficient (Beta)	Mean	Standard Deviation
5v5 Unblocked Shot Generation, Home Team	+0.075	40.48	3.92
5v5 Unblocked Shot Generation, Away Team	-0.111	40.59	4.02
5v5 Unblocked Shot Suppression, Home Team	-0.178	40.50	4.00
5v5 Unblocked Shot Suppression, Away Team	+0.106	40.50	4.09
In training, this was measured as the score-and-venue-adjusted unblocked shots per sixty minutes of 5v5 play during the previous 25 games, or as many games as were available early in the season. Considering larger samples of games weakened the fit and predictivity of the model, as did adjusting the shot numbers for strength-of-schedule.

The mean value over 2008-2015 for home unblocked shot generation is 40.5 per sixty minutes of 5v5 hockey, with a standard deviation of 4.1 per sixty minutes. A team whose recent results are 44.6 unblocked shots per sixty minutes is one standard deviation above average, and thus their home generation term is +0.075. This is positive, which means they are more likely to win the game under consideration, which makes sense -- they generate more offence than a typical team. The shot generation of the away team has the opposite sign, as we expect, and is larger in magnitude. That is, the ability of the away team to generate unblocked shots has a larger impact on the result of the game than the same ability in the home team. The ability of the home team to suppress unblocked shots is even more important, with the largest coefficient of the four.

For the away team, generation is more important. For the home team, suppression is more important. This suggests that the ability of the home team to set a defence and the away team to break it down (or fail to) is more important than the converse.

Pip, my previous model, had the above features, with broadly the same patterns. Oscar also has various other terms.

Diluted 5v5 Goaltending
Oscar has two goaltending terms, one for the home goalie and one for the away goalie.

Measure	Model Coefficient (Beta)	Mean	Standard Deviation
Lifetime Diluted 5v5 goals per shot-on-goal, Home team	-0.058	0.0782	0.0045
Lifetime Diluted 5v5 goals per shot-on-goal, Away team	+0.070	0.0786	0.0046
For a given goaltender, we calculate their lifetime 5v5 goals allowed per shot-on-goal and then we dilute it by artificially adding 1000 shots-on-goal, of which 82 are counted as goals, corresponding to the observed 5v5 goals per shot-on-goal allowed of 8.2%. This coarse estimation of goalie talent prevents Oscar from being thrown off by unusual results early in the careers of goaltenders. One thousand shots-on-goal is roughly the same order of magnitude as a single season of 5v5 work for a starting goalie. A helpful metaphor is to imagine trying to evaluate the quality of a vineyard from the quality of individual vintages they send out by mixing one bottle of each year's vintage with a bottle of known quaffably tolerable table wine. For new vineyards this will produce a taste close to the table wine, for established vineyards with a long track record their distinctive taste will show through.

For Oscar, we see that the quality of the away goalie is more important than that of the home goalie. Furthermore, having a goalie one standard deviation better than average has a smaller impact on winning than having one standard deviation better-than-average unblocked shot generation or suppression. The coefficient for the home term is negative and vice-versa because better goalies have lower numbers of goals per shot-on-goal.

Team scoring percentage
Oscar has two terms for capturing shooting talent.

Measure	Model Coefficient (Beta)	Mean	Standard Deviation
5v5 Goals per Shot-on-goal, Home team	+0.031	0.0425	0.0092
5v5 Goals per Shot-on-goal, Away team	-0.091	0.0424	0.0092
The shooting talent for the teams is measured over the past 25 games, like the generation and suppression terms. Where the goalie terms were very similar in magnitude, the shooting term for the away team is triple the magnitude of that of the home term. This confirms our finding above that the offensive talent of the away team is more important than that of the home team.

Special Teams Terms
Oscar has four terms for special teams:

Measure	Model Coefficient (Beta)	Mean	Standard Deviation
5v4 Shot Generation, Home Team	+0.057	95.08	12.81
5v4 Shot Generation, Away Team	-0.063	95.02	13.02
4v5 Shot Suppression, Home Team	-0.015	95.13	11.96
4v5 Shot Suppression, Away Team	+0.024	95.22	12.15
Here we have widened our view from unblocked shots to all shots; simply because they are more predictive; as above we measure the shots per sixty minutes over the last 25 games. Having a power-play which can generate shots at one standard deviation above league average has about as much impact as having a having a goaltender one standard deviation above average. A slavish adherence to the Akaike Information Criterion would not have permitted the penalty killing terms to survive, but I have decided to include them for reasons of symmetry and pedagogy.

Home-ice Advantage
Finally, Oscar includes a home-ice advantage term of 0.215; this corresponds to a historical advantage of 55%.

Training details
Oscar was trained on all regular season games from 2008-2015. My database includes the previous year also, but I decided to exclude it since all of the goalies in that year artificially appear to have nearly identical talent. I used a logistic regression, with regulation and overtime wins coded as having win probability 1, regulation and overtime losses as having win probability 0, and shootouts as having win probability 0.5 regardless of who won them.