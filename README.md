The Bottom-up Adaptive Macroeconomics under Extortion Racket Systems (BAMERS) model extends the BAM model @DelliGatti2011 (NetLogo code by @Platas-Lopez2020 in https://www.comses.net/codebases/9dacc220-8d7f-4038-b618-92bb9b1333f0/) by introducing extortionists in the system. In what follows, the model is described in detail following the ODD protocol.

Overview
========

Purpose
-------

BAMERS is designed to explore the effect of individual extortion activities in macroeconomic signals like GDP, inflation, unemployment rate, and Gini index.

Entities, state variables, and scales
-------------------------------------

-   Agents: Firms (also know as producers), workers (also know as consumers and households) and banks.

-   Environment: Originally the grid environment where the agents are situated was used exclusively for debug purposes, displaying information useful for validating the behavior of the system, e.g., the number of workers laboring in a firm, their presence when working and buying goods, etc. Beyond this, the BAMERS model uses neighborhood in this space when computing some decisions,   even when such relation has not geographical meaning, i.e., neighbors are not   assumed to be geographically closed.

-   State variables: Firms and workers, as originally defined in the   BAM model, are now extended with the variables shown in  next Table for registering the effect of extortion   activities. Unemployed workers can become extortionists and keep a list of   firms to extort and possibly punish. When captured, an extortionist will be in   jail a given number of periods (`time-in-jail`). Firms register if they are   being extorted and keep.

<center>
<table style="width: 400px; height: 50px;">
<tbody>
<tr class="odd">
<td style="text-align: right;"><strong>Firm</strong></td>
<td style="text-align: left;"></td>
<td style="text-align: right;"><strong>Worker</strong></td>
<td style="text-align: left;"></td>
</tr>
<tr class="even">
<td style="text-align: left;"><strong>Attribute</strong></td>
<td style="text-align: left;"><strong>Type</strong></td>
<td style="text-align: left;"><strong>Attribute</strong></td>
<td style="text-align: left;"><strong>Type</strong></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><code>being-extorted?</code></td>
<td style="text-align: left;">Bool</td>
<td style="text-align: left;"><code>extortionist?</code></td>
<td style="text-align: left;">Bool</td>
</tr>
<tr class="even">
<td style="text-align: left;"><code>amount-of-pizzo</code></td>
<td style="text-align: left;">Float</td>
<td style="text-align: left;"><code>firms-to-extort</code></td>
<td style="text-align: left;">AgSet</td>
</tr>
<tr class="odd">
<td style="text-align: left;"><code>amount-of-punish</code></td>
<td style="text-align: left;">Float</td>
<td style="text-align: left;"><code>firms-to-punish</code></td>
<td style="text-align: left;">AgSet</td>
</tr>
<tr class="even">
<td style="text-align: left;"></td>
<td style="text-align: left;"></td>
<td style="text-align: left;"><code>time-in-jail</code></td>
<td style="text-align: left;">Int</td>
</tr>
</tbody>
</table>
</center>

-   Scales: Time is represented in discrete periods, each step   representing a month.


Process overview and scheduling
-------------------------------

Extortion in  the BAMERS model occurs after closing the goods market and before firms pay loans and dividends, i.e., in between steps 5 and 6 of the BAM model schedule. The new steps added to the schedule are as follows:

<span>5.</span> A) Goods market opens. B) Good market closes.
&nbsp;&nbsp;<span>E1.</span> Unemployed poor workers decide whether to become extortionists or not.
&nbsp;&nbsp;<span>E2.</span> Extortionists look for firms to extort.
&nbsp;&nbsp;<span>E3.</span> Firms pay the pizzo or refuse to pay and denounce.
&nbsp;&nbsp;<span>E4.</span> Extortionists punish firms that refused to pay or, when captured, they     go to jail and lose their wealth.
&nbsp;&nbsp;<span>E5.</span> Prisoners that have served their time in prison are released as regular     unemployed workers.
<span>6.</span>  Firms will pay loans and dividends.


Design concepts
===============

### Basic Principles

Economic pressure and socioeconomic status induce a process of decision making on how to respond to basic needs, even considering the idea of becoming criminals. @Abrahamsen1949 [p.140] wrote about this:

> There are a few questions that are frequently asked in regard to our findings that family tension is the basic cause of criminal behavior.  The first has to do with economics. It is reasonable to assume,  intellectually speaking, that when one is without what is necessary  for subsistence and cannot get it, he will simply take it for himself  and his loved ones. This is instinctive, and it has to do with  self-preservation.

### Adaptation

Following the basic principle enunciated above, every time the goods market closes, unemployed workers belonging to the poorest quartile in the population decide as to whether they will become extortionists or not with a propensity *ϵ*. The propensity to become a criminal is orthogonal to any other variable, e.g., the probability of being imprisoned *λ*. This enables a controlled complete parameter exploration to evaluate the macroeconomic effect of extortion. When *ϵ* is equal to zero, our model corresponds to the baseline macroeconomic model without extortion, i.e., the BAM model.

### Sensing

Firms can observe a given number of firms to know if they are paying for pizzo or not.

### Interaction

Interaction happens when an extortionist finds a firm and asks for the payment of pizzo. Firms can in turn accept, or refuse to pay and denounce.

### Stochasticity

Processes exhibiting randomness include becoming an extortionist, being imprisoned, and trying to extort a firm. The search for potential firms to extort is also stochastic.

### Observation

Along simulation are observed:

-   Logarithm of real GDP.

-   Unemployment rate.

-   Annual inflation rate.


At end of simulation are computed:

-   Distribution of the size of firms.


Details
=======

Initialization
--------------

Next Table shows the default parameter initialization for the BAMERS model. Parameter values are influenced by the work of  [@elsenbroich2016; @Troitzsch2015a; @Nardin2016a; @Nardin2017]. Their use is fully described below, in the submodels section.

<center>
<table>
<thead>
<tr class="header">
<th style="text-align: left;"><strong>Parameter</strong></th>
<th style="text-align: left;"></th>
<th style="text-align: right;"><strong>Value</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;"><span class="math inline">&epsilon;</span></td>
<td style="text-align: left;">Propensity to become an extortionist</td>
<td style="text-align: right;">20%</td>
</tr>
<tr class="even">
<td style="text-align: left;"><span class="math inline">&lambda;</span></td>
<td style="text-align: left;">Probability of being imprisoned</td>
<td style="text-align: right;">30%</td>
</tr>
<tr class="odd">
<td style="text-align: left;"><span class="math inline">R<sub>t</sub></span></td>
<td style="text-align: left;">Rejection threshold</td>
<td style="text-align: right;">15%</td>
</tr>
<tr class="even">
<td style="text-align: left;"><span class="math inline">X</span></td>
<td style="text-align: left;">Number of attempts to extort a new firm</td>
<td style="text-align: right;">1</td>
</tr>
<tr class="odd">
<td style="text-align: left;"><span class="math inline">CF</span></td>
<td style="text-align: left;">Number of observable closest firms</td>
<td style="text-align: right;">3</td>
</tr>
<tr class="even">
<td style="text-align: left;"><span class="math inline">P<sub>i</sub></span></td>
<td style="text-align: left;">Proportion of net worth asked as pizzo</td>
<td style="text-align: right;">20%</td>
</tr>
<tr class="odd">
<td style="text-align: left;"><span class="math inline">P<sub>u</sub></span></td>
<td style="text-align: left;">Proportion of net worth taken as punish</td>
<td style="text-align: right;">30%</td>
</tr>
<tr class="even">
<td style="text-align: left;"><span class="math inline">C<sub>m</sub></span></td>
<td style="text-align: left;">Proportion of wealth as confiscated money</td>
<td style="text-align: right;">50%</td>
</tr>
<tr class="odd">
<td style="text-align: left;"><span class="math inline">T<sub>j</sub></span></td>
<td style="text-align: left;">Number of time periods in jail</td>
<td style="text-align: right;">6</td>
</tr>
</tbody>
</table>
</center>

Submodels
---------

1.  The number of extortionists in the system can change every time step. Each unemployed worker in the quartile with the lower savings become a criminal if the propensity to be an extortionist *ϵ* is greater than a randomly generated value with a uniform distribution between 0 and 100.

2.  An extortionist has *X* attempts to find a new victim. If an extortionist selects a firm that is already being extorted, it is considered as a failed attempt.

3.  Extortionists choose their victims by searching randomly over all firms. 

4.  Firms can allow the extortion or refuse to pay. A firm refuses to pay the pizzo when the rejection threshold (*R*<sub>*t*</sub>) is greater than the expected risk (*ER*), which is calculated as follows:

    *ER = EF / CF*

    where, *CF* denotes number of observable firms (three by default), i.e., the closest ones; and *EF* denotes how many of them are being extorted. This cognitive mechanism is based on the work of @elsenbroich2016. *R*<sub>*t*</sub> = 0 makes firms to pay the pizzo at the slightest hint of extortion activity.

5.  Firms that accept to pay the pizzo are asked for a proportion of their net worth, set by default to *P*<sub>*i*</sub> = 20% as suggested by [@Troitzsch2015a; @Nardin2016a; @Nardin2017].

6.  Firms that refuse to pay the pizzo always denounce. The probability that a extortionist is imprisoned is set to *λ* = 30%. Imprisoned extortionists stay *T*<sub>*j*</sub> = 6 periods in jail. Additionally, a proportion *C*<sub>*m*</sub> = 50% of their wealth is confiscated for supporting the victims of extortion that had denounced. All the previous default parameter values were adopted from the findings by [@Troitzsch2015a; @Nardin2016a; @Nardin2017].

7.  Extortionists that are not imprisoned punish the firm that denounced them, if any. The amount of money taken as punishment is a proportion of the net worth of the firm set by default to *P*<sub>*u*</sub> = 30%.

8.  Firms who have denounced and being punished receive an equal proportion of the victim support fund raised from 50% of imprisoned extortionists wealth. 

9.  Extortionists who served their sentence become regular workers in the labor market, in the immediate following period.


References
========
Abrahamsen, D. (1949). Family tension, basic cause of criminal behavior. *Journal of Criminal Law and Criminology (1931-1951)*, *40*(3), 330--343 <http://www.jstor.org/stable/1138546>

Delli Gatti, D., Desiderio, S., Gaffeo, E., Cirillo, P. & Gallegati, M. (2011). *Macroeconomics from the bottom-up*, vol. 11. Springer. <http://dx.doi.org/10.1007/978-88-470-1971-3>

Elsenbroich, C. & Badham, J. (2016). The extortion relationship: A computational analysis. *Journal of Artificial Societies and Social Simulation*, *19*. <http://dx.doi.org/10.18564/jasss.3223>

Platas-López, A., Guerra-Hernández, A., Cruz-Ramı́rez, N., Quiroz-Castellanos, M., Grimaldo, F., Paolucci, M. & Cecconi, F. (2020). Towards an agent-based model for the analysis of macroeconomic signals. In *Intuitionistic and Type-2 Fuzzy Logic Enhancements in Neural and Optimization Algorithms: Theory and Applications*, (pp. 551–565). Springer International Publishing. <http://dx.doi.org/10.1007/978-3-030-35445-9_38>

Troitzsch, K. G. (2014). Distribution effects of extortion racket systems. In *Lecture Notes in Economics and Mathematical Systems*, (pp. 181--193). Springer International Publishing. <http://dx.doi.org/10.1007/978-3-319-09578-3_15>

Nardin, L. G., Andrighetto, G., Conte, R., Székely, Á., Anzola, D., Elsenbroich, C., Lotzmann, U., Neumann, M., Punzo, V. & Troitzsch, K. G. (2016). Simulating protection rackets: a case study of the sicilian mafia. *Autonomous Agents and Multi-Agent Systems*, *30*(6), 1117--1147. <http://dx.doi.org/10.1007/s10458-016-9330-z>

Nardin, L. G., Székely, Á. & Andrighetto, G. (2017). GLODERS-S: a simulator for agent-based models of criminal organisations. *Trends in Organized Crime*, *20*(1), 85--99. <http://dx.doi.org/10.1007/s12117-016-9287-y>
