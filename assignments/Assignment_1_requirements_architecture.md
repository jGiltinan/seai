# Assignment 1: Requirements and Architecture

(ENPM 808O AI-based Software Systems)

## Overview:

In this assignment, you will zoom out from the AI component and think about the requirements and the architecture of a larger AI-enabled system in a concrete scenario. You are working for a company that wants to integrate facial recognition into their dashcams, cameras that are mounted to the front of the windshield and often as video evidence in the event of an accident. The facial recognition feature is to find missing children when an amber alert is given. The company has various models of dashcams on the market and this assignment focuses on how a facial recognition model, produced by a contractor, will be integrated into the current system and the many considerations that will have to be made.

## Scenario

[Dashcams](https://en.wikipedia.org/wiki/Dashcam) are getting more popular and are broadly installed in many vehicles already. As a manufacturer of dashcams, you want to develop features that distinguish your dashcams from those of your competitors. As one project, you work with a non-profit organization on *child safety*: The project's goal is to use dashcam footage to locate children reported missing. However, instead of broadcasts, such as [Amber alerts](https://en.wikipedia.org/wiki/Amber_alert) your products will allow to search for those children in video recordings made by the dashcam.

Assume that you are contracting out the AI component that recognizes persons in images and video to a company with extensive experience in face recognition based on deep neural networks. The contractors can build on past tools and infrastructure (e.g., [Amazon Rekognition](https://aws.amazon.com/rekognition/)) but will customize one or multiple components to your needs, to the extend possible (e.g., deploying models offline would be an option). They will provide you with the infrastructure to train and serve person recognition models, which you can operate and update in-house.

In designing such system, there are many considerations, such as:
* Your dashcams do not have direct internet access, but they can communicate over USB, Bluetooth or Wifi with phones, cars, and wifi-hotspots.
* The dashcams may run on battery but are usually connected to the car's power supply. Their processing power differs from model to model.
* Searches are coordinated with the authorities and the non-profit organization. You suspect less strict requirements as for Amber alerts, but the legal details are not worked out yet. Searches are likely not very frequent in any given area. For Amber alerts, [official statistics](https://amberalert.gov/statistics.htm) report nearly 1 alert per day nationwide.
* Faster reports of sightings are more useful to the authorities.
* You suspect users may be worried about privacy and charges for data.
* You recently hear everywhere, including press and consultants, how exciting the future of [Edge computing](https://en.wikipedia.org/wiki/Edge_computing) rather than Cloud computing is going to be and wonder whether you should explore that. You wouldn't be opposed to thinking about partnering with other organizations to, say, install hardware in gas stations or drive throughs.

You are concerned at least about the following qualities:

1. Latency between reporting a child missing (with numerous pictures) to receiving potential matches from dashcam users
2. Number of false positives and false negatives
3. Ability to observe how well the system works in production
4. Users can be casual drivers, frequent drivers, commercial freight, and others, each of which may have different dashcam models and expectations of their user experience
5. Scalability and cost of running the infrastructure with many users across many states/countries
6. Operating costs for users
7. Difficulty of changing and updating the system to meet new requirements or incorporate better technology
8. Privacy
9. Development cost, technical complexity of the solution, maintainability

## Tasks

Think about requirements for such a system and how you would design it (no implementation needed for this assignment). What could go wrong and how could risks be mitigated? What would be the main components (AI or not), where might they be located? Consider alternative designs. You will focus on four aspects: goals, risks, system architecture, and evaluation strategy.

First, identify the goals for the new feature in the dashcam. Break down goals into *organizational objectives*, *leading indicators*, *user outcomes*, and *model properties* and provide corresponding measures you could use to assess how well you achieve the goals. Provide a brief description how goals relate to each other (e.g., “better model accuracy should help with higher user satisfaction”). Organizational objectives and leading indicators should be stated from the perspective of the company (not the partnering non-profits or authorities).  For user outcomes and model properties make clear to which users or models the goal refer; you may state different goals for different users. Your list of goals should be reasonably comprehensive and may include multiple goals at each level.

Second, propose an integrated system to accomplish the task. Suggest whether the AI component(s) for recognizing a person in an image should be deployed (a) on the dashcam, (b) on a phone, (c) in the cloud, or (d) some other configuration you describe (e.g., hybrid or edge). Explicitly discuss the tradeoffs involved in this discussion by comparing the suggested design with an alternative design according to the qualities  Draw a system context diagram of your proposed system.

Third, think about risks based on wrong predictions by the image recognition component and possible mitigation strategies. Specifically think about possible safeguards, experience design, and keeping humans in the loop. Identify and explain at least 3 relevant risks caused by wrong predictions and suggest mitigation strategies. Mitigation strategies will typically be at the system level, outside of the AI component itself. Briefly explain how each suggested mitigation strategy can (partially for fully) address the risk. Use fault tree analysis in your analysis. Describe how you constructed your diagram along with supporting evidence.

Fourth, design an evaluation strategy to use to analyze your proposed system.  

Define “constraints” to the dashcam design based on the research. For example, dahscam needs to have the minimum 4GB processing power, etc. You may want to do some Internet research about typical characteristics of various hardware and software components (e.g., storage capacity of dashcams, size of typical face recognition models, bandwidth of Bluetooth connections). You do not need to conduct precise measurements or estimate concrete values, but should inform your discussion with an understanding of the qualities in the context of the scenario (e.g., “solution A is better than solution B because of a bottleneck in Bluetooth bandwidth” or “privacy is better in solution C because customer data does not leave the device”).


## Deliverable

Submit a report as a single PDF file to Gradescope that covers the following topics in clearly labeled sections (ideally each section starts on a new page):

1. **Goals** (1 page max): Provide a list of organizational objectives, leading indicators, user outcomes, and model properties and corresponding measures. Briefly discuss how goals relate to each other.
2. **System Context Diagram** Provide a diagram of your proposed integrated system. Briefly discuss how each component connects, and an explanation (0.5 page max)
3. **Risks** (1.5 page max) Describe three relevant risks from wrong predictions and corresponding mitigation strategy. Explain how each mitigation strategy reduces the risk.
4. **Formal risk analysis** (1 page max): Provide a brief description of the risk analysis process selected and how it was systematically applied. Provide supporting evidence (e.g., diagrams, tables, screenshots) of the risk analysis performed and briefly discussed insights gained from applying the fault tree analysis method systematically.
5. **Evaluation strategy** (1 page max):  Describe how you would evaluate your 




4. **Analysis of deployment alternatives:** Briefly describe the fourth deployment architecture considered. Then for each of the 4 deployment options discuss the 8 qualities listed above. A tabular representation (one column per deployment option, one row per quality) may be suitable, but other clearly structured formats are possible. Rough estimates or relative ratings with a brief explanation are sufficient as long as they are grounded and realistic in the scenario.
5. **Recommendation and justification of deployment architecture** (1 page max): Recommend a deployment architecture and justify this recommendation in terms of the relative relevance of the qualities and the tradeoffs among quality attributes.



Graphics and tables do not count against the page limit.


## Grading

The assignment is worth 100 points. For full credit, we expect:
* [ ] 25 points: Goals listed and correctly grouped (10p). List of goals relates to scenario and reasonable complete (5p). For each goal a metric is clearly defined that could realistically be measured in the scenario (5p). The relationship of goals is described and plausible (5p).
* [ ] 10 points: System context diagram drawn (5p) and discussed (5p).
* [ ] 25 points: Three risks from wrong predictions are described that are relevant in the scenario (10p). For each risk a plausible mitigation strategy is suggested and explained (15p).
* [ ] 20 points: Fault tree analysis technique systematically applied to understand risks (10p). Supporting evidence provided (5p). Insights from applying the method discussed (5p).
* [ ] 20 points: Evaluation strategy defined. Criteria/requirements the system design will be evaluated against are given (5p) and justified (5p). Constraints on hardware (5p) and software (5p) given and explained.



* [ ] 15 points: Description of a fourth deployment architecture is included. For each of the 4 design alternatives all 8 quality attributes are analyzed plausibly.
* [ ] 15 points: Recommendation for a deployment decision provided and justified (10p). The justification clearly makes tradeoffs among the qualities and weighs the relative importance of the qualities (5p).

