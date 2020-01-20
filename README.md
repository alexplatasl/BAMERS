# BAMERS

BAMERS extends the economic model BAM (https://github.com/alexplatasl/BAMmodel) introducing extortionists in the system. In what follows, the model is described in detail following the ODD protocol.

##Overview

###Purpose

BAMERS is designed to explore the effect of the agent's extortion activities in macroeconomic signals like GDP, inflation, unemployment rate, and Gini index.

### Entities, state variables, and scales

* Environment: Agents are situated in a grid environment. Following  Troitzsch (2015a) and Elsenbroich (2016), extortionists use the environment when searching for victims among their known firms, defining known firms as those in proximity; firms use the environment when deciding to accept or reject paying the pizzo, based on the behavior of their neighbors.
* State variables: Firms and workers in BAM are extended with the variables shown in next Table to reflect the extortion activities. As mentioned before, unemployed workers can become extortionists (``extortionist?``). In this case they keep a list of firms to be extorted and punished. When captured, an extortionist will be in jail given the number of periods (``time-in-jail``). Firms register if they are being extorted (``being-extorted?``). If they are, they keep register of the amount to pay as pizzo or punishment.

|         Firm         |       |       Worker        |       |
| :------------------: | :---: | :-----------------: | :---: |
|      Attribute       | Type  |      Attribute      | Type  |
| ``being-extorted?``  | Bool  |  ``extortionist?``  | Bool  |
| ``amount-of-pizzo``  | Float | ``firms-to-extort`` | AgSet |
| ``amount-of-punish`` | Float | ``firms-to-punish`` | AgSet |
|                      |       |  ``time-in-jail``   |  Int  |

###Process overview and scheduling

Extortion occurs before firms pay loan and dividends, step 6 in BAM's schedule (Platas, 2019). The schedule is as follows:


1.  Unemployed poor workers decide to become extortionists.
2.  Extortionists look for firms to extort.
3.  Firms pay pizzo or refuse to pay.
4.  Extortionists go to jail and lose their wealth or punish firms that refused to pay pizzo. 
5.  Prisoners that have served their time in prison are released as regular workers.


##Design concepts

####Basic Principles    

Economic pressure and socioeconomic status induce a process of decision making on how to respond to basic needs, even considering the idea of becoming criminals. About this Abrahamsen (1949) says:

> There are a few questions that are frequently asked in regard to our findings that family tension is the basic cause of criminal behavior. The first has to do with economics. It is reasonable to assume, intellectually speaking, that when one is without what is necessary for subsistence and cannot get it, he will simply take it for himself and his loved ones. This is instinctive, and it has to do with self-preservation.

####Adaptation

Following the basic principles enunciated above, every time the goods market closes, the unemployed workers belonging to the poorest quartile in population decide if becoming extortionists with a propensity $\epsilon$. The propensity to become a criminal is orthogonal to any other variable, e.g., the efficiency of the police. This enables a controlled complete parameter exploration to evaluate the macroeconomic effect of extortion. When $\epsilon$ is equal to zero, our model corresponds to the baseline macroeconomic model without extortion, i.e., the BAM model.  

####Sensing

Firms can observe the nearest firms to know if they are paying for pizzo or not.

####Interaction

The interaction is generated when an extortionist finds a firm to request the payment of pizzo, the firms can refuse to pay it.

####Stochasticity
Elements that have random shocks are:


* The decision to become an extortionist $\epsilon$.
* The probability that the extortionist is caught by the police $\lambda$.

##Details

###Initialization

Next Table shows the parameter initialization of the model. Values were adopted from Elsenbroich (2016), Troitzsch (2015a), Nardin (2016a) and Nardin (2017). 

|            | Parameter                                 | Value |
| ---------- | ----------------------------------------- | ----- |
| $\epsilon$ | Propensity to be an extortionist          | 20%   |
| $\lambda$  | Probability of being caught               | 30%   |
| $R_t$      | rejection threshold                       | 15%   |
| $X$        | Number of trials to extort a new firm     | 1     |
| $CF$       | Number of observable closest firms        | 3     |
| $Pi$       | Proportion of Net Worth A as pizzo        | 10%   |
| $Pu$       | Proportion of Net Worth A as punish       | 25%   |
| $C_m$      | Proportion of wealth as confiscated money | 50%   |
| $T_j$      | Amount of time periods in jail            | 6     |


###Submodels

1. The amount of extortionists changes every time step, each unemployed worker in the quartile with the lower savings $SA$ become a criminal if the propensity to be an extortionist $\epsilon$ is greater than a randomly generated value with a uniform distribution between 0 and 100.
2. An extortionist has $X$ trials to find a new victim among the firms that are not already extorted.
3. The extortionists have two possible strategies for searching victims, random and by geographic proximity.
4. In any of the strategies, if in his attempt $X$ the extortionist selects a company that has already been extorted by someone else who provides "protection", the worker loses that chance to extort.
5. The firm can decide if it allows extortion or if it refuses to pay.
6. The firm decides not to pay the pizzo when its rejection threshold ($R_t$) is greater than the expected risk (ER).
7. A threshold of 0\% represents that the firm at the slightest hint of extortion in the area will choose to pay the pizzo.
8. The expected risk is calculated as follows:

$$ ER = \frac{EF}{CF}$$

where, $CF$ represents a small observable number of Closest Firms (default 3) and the number of Firms Extorted in that small set are $EF$l. This cognitive mechanism is based on Elsenbroich (2016).

9. Firms that accept to pay the pizzo are extorted. The required amount of extortion will be a proportion $Pi$ of the net worth A (default) as in Troitzsch (2015a), Nardin (2016a), Nardin (2017) or a constant amount like Elsenbroich (2016).
10. Each time a company decides not to pay the pizzo, it generates the possibility that the extortionist is caught by the police with a probability $\lambda$.
11. If the extortionist is caught by the police, a certain amount of time periods $T_j$ is sent to jail. Additionally, wealth of the extortionist will be confiscated.
12. The confiscated money $C_m$ is sent to a fund to support victim firms.
13. If the extortionists were not captured by the police, they execute a punishment against the firms that refused to pay the pizzo. Again, the required amount of punishment $Pu$ will be a proportion of the net worth A.
14. Each company punished is eligible to receive a refund from the victim support fund.
15. Compensation can be given proportionally among all the firms punished, or with an amount equivalent to the punishment they received in the current period, following the principle "first to come, first to serve" until the funds are finished.
16. Extortionists who serve their sentence in prison, are again eligible in the labor market, in the following period.

----

# References

Abrahamsen, D. (1949). Family tension, basic cause of criminal behavior. Journal of Criminal Law and Criminol-
ogy (1931-1951), 40(3), 330–343.

Elsenbroich, C. & Badham, J. (2016). The extortion relationship: A computational analysis. Journal of Artificial
Societies and Social Simulation, 19. doi:10.18564/jasss.3223.

Nardin, L. G., Andrighetto, G., Conte, R., Székely, Á., Anzola, D., Elsenbroich, C., Lotzmann, U., Neumann, M.,
Punzo, V. & Troitzsch, K. G. (2016). Simulating protection rackets: a case study of the sicilian mafia. Au-
tonomous Agents and Multi-Agent Systems, 30(6), 1117–1147. doi:10.1007/s10458-016-9330-z.

Nardin, L. G., Székely, Á. & Andrighetto, G. (2017). GLODERS-S: a simulator for agent-based models of criminal
organisations. Trends in Organized Crime, 20(1), 85–99. doi:10.1007/s12117-016-9287-y.

Platas-López, A., Guerra-Hernández, A., Grimaldo, F., Paolucci, M. & Cecconi, F. (2019). Micro-foundations of
macroeconomic dynamics: the agent-based BAM model. In J. Sabater-Mir, V. Torra, I. Aguiló & M. González-
Hidalgo (Eds.), Artificial Intelligence Research and Development. Proceedings of the 22nd International Confer-
ence of the Catalan Association for Artificial Intelligence, Frontiers in Artificial Intelligence and Applications.
IOS Press. doi:https://doi.org/10.3233/FAIA190141