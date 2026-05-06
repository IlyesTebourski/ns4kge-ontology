# Competency Questions

## Use Case 1 — Literature Navigation

A researcher wants to explore the state of the art and understand which methods and experimental configurations exist in the literature.

- CQ1: Which datasets are most frequently used to evaluate negative sampling methods?
- CQ2: Which KGE models are most commonly associated with particular NS strategies?
- CQ3: Which evaluation metrics are typically reported?
- CQ4: Which combinations of dataset, KGE model, and NS method appear most frequently?
- CQ5: Which experimental parameters are consistently reported across papers?

---

## Use Case 2 — Gap Discovery

A researcher wants to assess the completeness and comparability of experimental descriptions, and identify missing evaluations.

- CQ6: Which combinations of NS methods and datasets are underrepresented in the literature?
- CQ7: Which articles do not fully describe their experimental configuration (optimizer, learning rate, loss function, hardware, NS ratio)?
- CQ8: Which experimental dimensions are most often missing across papers?
- CQ9: Which NS methods have been evaluated on a given dataset?
- CQ10 (variant): Which self-adversarial NS methods have never been evaluated on the most frequently used datasets?

---

## Use Case 3 — Experimental Blueprint

A researcher wants to identify the best or most common experimental configurations for a given task in order to design their own experiment.

- CQ10: Which configurations are most frequently used for link prediction tasks?
- CQ11: Which similar experimental configurations yield strongly diverging results across articles?
- CQ12: Which negative sampling method achieves the best performance for a given task and dataset?
