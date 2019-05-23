
# Discussion: Options for Embeddings

Embeddings are flexible and there are many options for using them in sports. We have discussed different options for embeddings (see Mike's summary in this directory). This page aims to provide a brief summary. There are usually two version of a model: instantaneous: apply embeddings only to a current match state, and historical: condition on the observation history.

| ![emb-yudong](emb-yudong.jpg) |
| ------------------------------------------------------------ |
| Yudong's skip-gram-style model for learning embeddings. A player embedding is trained to predict the player's next action given a history of states. |

## Available Statistical Information

Our basic idea are event logs of the form

![e1](https://latex.codecogs.com/gif.latex?<img src="/tex/445e7315d470dca99bccd6b36a859dfc.svg?invert_in_darkmode&sanitize=true" align=middle width=95.06772659999999pt height=22.831056599999986pt/>)

with integer time stamps *t*. The local features ![e2](https://latex.codecogs.com/gif.latex?<img src="/tex/87a619146a878ae60db8335debd064f0.svg?invert_in_darkmode&sanitize=true" align=middle width=14.942908199999989pt height=14.611878600000017pt/>) include things like game time, *x-y* location, score differential, manpower etc. At time *t* action ![e3](https://latex.codecogs.com/gif.latex?<img src="/tex/9789555e5d8fa5de21171cc40c86d2cd.svg?invert_in_darkmode&sanitize=true" align=middle width=13.65494624999999pt height=14.15524440000002pt/>) is taken by ![e4](https://latex.codecogs.com/gif.latex?<img src="/tex/4032915205acc929aafa62e0ee6a59dc.svg?invert_in_darkmode&sanitize=true" align=middle width=50.21431469999999pt height=22.831056599999986pt/>). 

Focusing on player *i*, the data for this player is given by

![e5](https://latex.codecogs.com/gif.latex?<img src="/tex/f26335e0888c67f975c37bb38f79c7ba.svg?invert_in_darkmode&sanitize=true" align=middle width=128.03670329999997pt height=22.831056599999986pt/>)

## Discriminative Embeddings: Predict States and Actions.

#### Current-Time Version

A player embedding can be used to predict the current state-features and action:

![e6](https://latex.codecogs.com/gif.latex?%24P%28%5Cmathbf%7Bx%7D_%7Bt%7D%2Ca_%7Bt%7D%7C%5Cmathit%7Bplayer%7D_t%3Di%29.%24)


This can be learned using [skip-gram techniques](https://bitbucket.org/sportlogiqteam/spg-sfu-waterloo-project/src/master/foundations/README.md) . It seems similar to [Michael's 'causal model'](https://bitbucket.org/sportlogiqteam/spg-sfu-waterloo-project/src/master/embeddings/VI%20Player%20Embeddings%20Summary.pdf). The equation can be decomposed into quantities of interest for sports: 

![e7](https://latex.codecogs.com/gif.latex?%24P%28%5Cmathbf%7Bx%7D_%7Bt%7D%2Ca_%7Bt%7D%7C%5Cmathit%7Bplayer%7D_t%3Di%29%20%3D%20P%28%5Cmathbf%7Bx%7D_%7Bt%7D%7C%20%5Cmathit%7Bplayer%7D_t%3Di%29%20%5Ctimes%20P%28a_t%7C%5Cmathbf%7Bx%7D_%7Bt%7D%2C%5Cmathit%7Bplayer%7D_%7Bt%7D%3Di%29%20%24)


The second term can be interpreted as the player's policy, and the first as the probability of finding player *i* in a specific location in state-feature space. Thus the first arguably captures a player's style or role. Thus it would make sense to 

1. cluster players according to an embedding for ![e8](https://latex.codecogs.com/gif.latex?%24P%28%5Cmathbf%7Bx%7D_%7Bt%7D%7C%20%5Cmathit%7Bplayer%7D_t%3Di%29%20%5Ctimes%20%24), and 
2. evaluate them according to their actions modelled by the policy distribution ![e9](https://latex.codecogs.com/gif.latex?%24P%28a_t%7C%5Cmathbf%7Bx%7D%7Bt%7D%2C%5Cmathit%7Bplayer%7D%7Bt%7D%3Di%29%24) 

See our [Sloan paper](http://www.sloansportsconference.com/wp-content/uploads/2017/02/1625.pdf) on comparing apples-to-apples.

#### Historical Version

A [marked point process model](https://bitbucket.org/sportlogiqteam/spg-sfu-waterloo-project/src/master/foundations/README.md) aims to predict the next observation given the history so far: 

![e10](https://latex.codecogs.com/gif.latex?%24P%28%5Cmathbf%7Bx%7D_%7Bt%7D%2Ca_%7Bt%7D%7C%5Cmathbf%7Bx%7D_%7B%3C%20t%7D%2Ca_%7B%3Ct%7D%29.%24)

Adding the information that player *i* acts at time *t*, we can consider a conditional model 

![e11](https://latex.codecogs.com/gif.latex?%24P%28%5Cmathbf%7Bx%7D_%7Bt%7D%2Ca_%7Bt%7D%7C%5Cmathbf%7Bx%7D_%7B%3C%20t%7D%2Ca_%7B%3Ct%7D%2C%20%5Cmathit%7Bplayer%7D_t%3Di%29.%24)

Reasons why this quantity is of interest include the following.

1. We have proven a theorem for our goal impact metric that says that the impact metric is equivalent to the expected value for a team that results when we replace the conditional probability for the average player with that for a specific player (i.e. replace the first equation with the second). 

2. As before, the equation can be decomposed into quantities of interest for sports: <img src="/tex/d0f319cc00f8fc80ab054f0e85fbedd6.svg?invert_in_darkmode&sanitize=true" align=middle width=229.105965pt height=24.65753399999998pt/>

   ![e12](https://latex.codecogs.com/gif.latex?%24P%28%5Cmathbf%7Bx%7D_%7Bt%7D%7C%5Cmathbf%7Bx%7D_%7B%3C%20t%7D%2Ca_%7B%3Ct%7D%2C%20%5Cmathit%7Bplayer%7D_t%3Di%29%20%5Ctimes%20P%28a_t%7C%5Cmathbf%7Bx%7D_%7B%5Cleq%20t%7D%2C%5Cmathit%7Bplayer%7D_%7Bt%7D%3Di%2Ca_%7B%3Ct%7D%29%24)

   The second term can be interpreted as the player's policy, and the first as the probability of finding player $i$ in a specific location in state-feature space, both as functions of the match history.
   
   

## Embedding Players + Encoding States/Actions

An elegant possibility is to build a generative model over states and actions, using a conditional VAE that conditions on an embedding for a player.  Formally, we would have a code <img src="/tex/f93ce33e511096ed626b4719d50f17d2.svg?invert_in_darkmode&sanitize=true" align=middle width=8.367621899999993pt height=14.15524440000002pt/> for states and actions, generated by a VAE that conditions on current player: <img src="/tex/99febe0bd37d950dd79ea7216d332878.svg?invert_in_darkmode&sanitize=true" align=middle width=162.02652674999996pt height=24.65753399999998pt/>

where the code *z* is generated by a conditional VAE model

![e13](https://latex.codecogs.com/gif.latex?%24q%28%5Cmathbf%7Bx%7D_%7Bt%7D%2Ca_%7Bt%7D%7Cplayer_t%20%3Di%29%24) . 

The historical version conditions also on the history of actions and states. This extends the variational auto-encoder model for point processes described  [here](https://www.borealisai.com/en/publications/variational-auto-encoder-model-stochastic-point-process/)  from CVPR 2019.

## Generate Players: Conditional VAE

A generative model defines a distribution over players. One possibility champoined by Galen is to use a conditional VAE (see [tutorial](https://arxiv.org/abs/1606.05908)) to model 

![e14](https://latex.codecogs.com/gif.latex?%24P%28%5Cmathit%7Bplayer%7D_t%3Di%7C%5Cmathbf%7Bx%7D_%7Bt%7D%2Ca_%7Bt%7D%29%24)

A conditional VAE produces an encoding ![e15](https://latex.codecogs.com/gif.latex?%24q%28%5Cmathit%7Bplayer_t%7D%3Di%7C%5Cmathbf%7Bx%7D_%7Bt%7D%2Ca_%7Bt%7D%29%24) which depends on the observed features. Therefore the *conditional VAE does not produce a single embedding for each player*. 

The history version is especially interesting if we include other players in the history: 

<img src="/tex/c70bd2c9c36b3cf5889effce877c147c.svg?invert_in_darkmode&sanitize=true" align=middle width=235.51711094999996pt height=24.65753399999998pt/>,

because then it captures interactions among players. For example the model would represent the information usually visualized in a *passing graph.*

## Generating Players, States, and Actions: Point Process Model

A variational auto-encoder  could produce a code <img src="/tex/a33a6206486276bf957212fceb1ecf46.svg?invert_in_darkmode&sanitize=true" align=middle width=12.61044509999999pt height=14.15524440000002pt/>,  that generates the next observation:

<img src="/tex/d333ebe6b6d67ae2d279ba6aaba89462.svg?invert_in_darkmode&sanitize=true" align=middle width=139.51039244999998pt height=24.65753399999998pt/>

Several models of this type assume independence of the modelled components like Mikes VI Model, e.g.

<img src="/tex/8a383da86090fdc416bfaa248cca5afe.svg?invert_in_darkmode&sanitize=true" align=middle width=357.34449465pt height=24.65753399999998pt/>

The encoding distribution provides a joint embedding of the three components <img src="/tex/2454b05504a8a5be66b35d6deae3958e.svg?invert_in_darkmode&sanitize=true" align=middle width=95.06772659999999pt height=22.831056599999986pt/> (see [general explanation](https://bitbucket.org/sportlogiqteam/spg-sfu-waterloo-project/src/master/embeddings/README.md)). Mike's model VI proposes decomposing the encoding distribution to get a component for players only:

<img src="/tex/b20307c1a7dc43d5cf0b33dcdf777557.svg?invert_in_darkmode&sanitize=true" align=middle width=357.820452pt height=24.65753399999998pt/>.

In the history version, the code can also depend on the previous history

![e18](https://latex.codecogs.com/gif.latex?%24%20Q%28z_t%7C%5Cmathbf%7Bx%7D%7Bt%7D%2Ca%7Bt%7D%2C%5Cmathit%7Bplayer%7Dt%2C%5Cmathbf%7Bx%7D%7B%5Cleq%20t%7D%2Ca_%7B%5Cleq%20t%7D%2C%29%24)

If we assume conditional independence and allow the code to depend on the history, we have an extension of the variational auto-encoder model for point processes described  [here](https://www.borealisai.com/en/publications/variational-auto-encoder-model-stochastic-point-process/)  from CVPR 2019. This would be a powerful and exciting extension of this CVPR paper.

## Questions

- How does Mike's model capture correlations between state and player?
- Is it really necessary to embed states and actions if all we want is player embeddings?



Hi <img src="/tex/8fdd9f20ad386c2350de4d0628adc450.svg?invert_in_darkmode&sanitize=true" align=middle width=68.42065394999999pt height=19.1781018pt/>.

<p align="center"><img src="/tex/eec5713a36c954c2630d087e912385e3.svg?invert_in_darkmode&sanitize=true" align=middle width=86.16804239999999pt height=15.572667pt/></p>

<p align="center"><img src="/tex/76bc6a0e4e6a17d9f86ad2f16e7fa4c1.svg?invert_in_darkmode&sanitize=true" align=middle width=118.06485734999998pt height=39.452455349999994pt/></p>`









