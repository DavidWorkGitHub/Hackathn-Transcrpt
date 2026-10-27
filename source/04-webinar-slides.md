# Opening webinar slides (Tech Arena Ireland - Challenge 2026 PDF, Gonzalo Fiz Pontiveros, Huawei IRC, 14 Oct 2026), text extract

 HUAWEI IRELAND TECH ARENA 2026
From user histories
to recommendations
Generative sequential recommendation under constraints
Gonzalo Fiz Pontiveros
AIRecS, Huawei Ireland Research Centre
Opening webinar · 14 October 2026
 Recommendation turns abundance into discovery
 01 / WHY RECOMMENDATION MATTERS
Which item is useful to this person, right now?
 A large catalogue
 Jazz mix Dance hits
 A listener A relevant
 Acoustic live Podcasts History, current intent recommendation
 and context Acoustic live
 Classical New releases
 Learn what matters Help someone discover it
Users Platforms Creators and sellers
Find useful content, products Make each interaction more Reach people who may value
and services. relevant. their work.
The same problem appears in shopping, video, news and many other digital services.
Tech Arena 2026 | Opening webinar 1/8
 A shared idea: predict what comes next
 02 / FROM LANGUAGE TO INTERACTION HISTORIES
Language
 On my way home
A listener’s interaction history
 Jazz mix Dance hits Acoustic set Next
 played skipped replayed interaction?
 T
 ∏︂
 pθ (x1 , . . . , xT ) = pθ (xt | x<t )
 t=1
 xt : a text token or an encoded interaction; x<t : the preceding sequence.
 Learn a conditional distribution from the history.
Tech Arena 2026 | Opening webinar 2/8
 LLMs motivate a scalable recipe for recommendation
 02 / WHY THIS DIRECTION IS EXCITING
 Scale and measure
 Represent events Learn from histories
 Data, context and
 Items, actions and context Conditional prediction
 model capacity
SASRec TIGER HSTU
2018 2023 2024
Attention over item histories Generate structured item Scale sequence models
for next-item prediction. identifiers from interaction adapted to recommendation
 histories. data.
 How far can the sequence modelling recipe take us in recommendation?
Tech Arena 2026 | Opening webinar 3/8
 What changes when the sequence records behaviour?
 03 / REPRESENTATION, TIME AND MEANING
 Typical text Interaction histories
Vocabulary A fixed vocabulary of reusable subword A changing catalogue of items,
 tokens including new items
Time Position within the text Order, elapsed time, sessions
Meaning Tokens in a linguistic context Views, clicks, skips and purchases carry
 different signals
Input/Output Short input sequences; long output Long input sequences; short output
Geometry sequences sequences
 The representation must preserve which event happened, and when.
Tech Arena 2026 | Opening webinar 4/8
 Recommendations help create tomorrow’s training data
 03 / OBSERVATION, FEEDBACK AND OBJECTIVES
 History
 Offer Response New data
 What we have
 What the user sees What the user does What we learn from
 observed
 • A logged click reflects both preference and exposure.
 • For an item that was never shown, the response is unknown.
 • Choosing recommendations changes the interactions we will observe.
 Predicting a response and choosing an action are connected problems.
Schnabel et al., Recommendations as Treatments (ICML 2016).
Tech Arena 2026 | Opening webinar 5/8
 Two phases: predict a response, then choose an offer
 04 / THE TECH ARENA CHALLENGE
 Phase 1 Predict clicks Phase 2 Compete and adapt
How likely is this user to click this Which item should your agent offer?
candidate?
 Given
Given A simulated marketplace and user histories.
Interaction history and candidate items.
 Build
Build A recommendation agent competing for user
A model of the user’s response. choice.
Submit Submit
A file of predicted click probabilities. Models for evaluation at round boundaries.
 Use what you learn about user behaviour to make better decisions.
Tech Arena 2026 | Opening webinar 6/8
 Phase 1: estimate the response to a candidate
 04 / FROM HISTORY AND CANDIDATE TO A CLICK PROBABILITY
Our listener’s history Illustrative predictions
 Jazz mix: played
 Candidate Click probability
 Dance hits: skipped
 Acoustic live 0.74
 Acoustic set: replayed Jazz classics 0.50
 Dance hits 0.12
 pθ (click | H, i)
H: observed history i: candidate item
 Learn from Score against
 Submit aligned predictions
 labelled examples hidden labels
Tech Arena 2026 | Opening webinar 7/8
 Phase 2: recommend, observe, improve
 04 / COMPETITION IN THE MARKETPLACE
 Your agent: Acoustic live
 Same listener Simulated user
 Agent A: Jazz classics
 Interaction history Responds to the offers
 Agent B: Dance hits
Four rounds of development and feedback
 Develop Release responses
 Submit Evaluate
 Initial data Learn for the
 Models collected Run on new data
 + feedback next round
 Start with a baseline. Improve quality and efficiency with each round.
Teams update their models between evaluation rounds.
Tech Arena 2026 | Opening webinar 8/8
 Selected reading
 REFERENCES / FOR PARTICIPANTS WHO WANT TO EXPLORE
 • Kang and McAuley (2018).
 Self-Attentive Sequential Recommendation.
 SASRec: attention over interaction histories.
 • Rajput et al. (2023).
 Recommender Systems with Generative Retrieval.
 TIGER: autoregressive generation of semantic item identifiers.
 • Zhai et al. (2024).
 Actions Speak Louder than Words: Trillion-Parameter Sequential
 Transducers for Generative Recommendations.
 HSTU: sequence modelling adapted to recommendation at scale.
 • Schnabel et al. (ICML 2016).
 Recommendations as Treatments:
 Debiasing Learning and Evaluation.
 Selection bias and propensity-weighted learning and evaluation.
Tech Arena 2026 | Opening webinar 8/8
