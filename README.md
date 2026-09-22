# Predicting Mobility Demand from Urban Features

A shared bike that's ready when needed cuts demand for less climate-friendly transport. This tutorial models the relationship between bike usage and points of interest (POIs) to find good locations for new shared-bike stands.

**Original Authors:**

* Konstantin Klemmer, Microsoft Research & CCAI, [konstantin@climatechange.ai](mailto:konstantin@climatechange.ai)
* Shafat Rahman, CCAI, [shafatrahman@climatechange.ai](mailto:shafatrahman@climatechange.ai)
* Felix Wagner, TU Berlin & Mercator Research Institute on Global Commons and Climate Change, [wagner@mcc-berlin.net](mailto:wagner@mcc-berlin.net)
* Florian Nachtigall, TU Berlin & Mercator Research Institute on Global Commons and Climate Change, [nachtigall@tu-berlin.de](mailto:nachtigall@tu-berlin.de)
* Gabriela Yaulli Herrera, Cornell Tech, [cgy4@cornell.edu](mailto:cgy4@cornell.edu)

Originally presented at Climate Change AI Summer School 2023.

---

## About this fork

This is a fork of the original CCAI tutorial, completed as coursework ([@obonyo1](https://github.com/obonyo1))

### What I checked

The notebook fits three models on one task: predicting bike-trip-end density per H3 grid cell from nearby POI counts (medical, transportation, sports/recreation). At each stage I checked:

- **Generalization.** Using a train/validation/test split instead of in-sample R². The default XGBoost model had a clear gap between train and test R². A random hyperparameter search over `max_depth`, `learning_rate`, and `n_estimators` closed most of it — test R² moved from about 0.13 to 0.32.
- **Overfitting.** The XGBoost train-vs-validation RMSE plot showed overfitting starting past 10 trees. The GNN's loss curves showed the same pattern: validation loss rising again after early epochs, which is what early stopping catches.
- **Cost vs. accuracy.** CodeCarbon tracked training emissions for each model. Even at this small scale, the GNN cost more energy to train than the linear model for a small gain in R². That trade is worth naming, not assuming away.
- **Feature contribution.** SHAP values on the tuned XGBoost model showed sports/recreation and transportation POIs raising predicted trips, and medical POIs lowering them. This is a Shenzhen-specific pattern, not a general rule — see below.

### What I learned

The main lesson wasn't a modeling technique. It was that a model tuned on Shenzhen data does not transfer to another city just because the code does. Working through the notebook's reflection question for Nairobi surfaced three gaps:

1. Nairobi's main short-trip modes are boda boda and matatu, not docked bike-share. There's no local match for the "trip ends in this cell" data the model needs.
2. The POI data here comes from a formal mapping provider. It under-represents informal settlements like Kibera, where much real infrastructure isn't formally mapped. A model trained on it would understate demand in exactly those areas.
3. The POI-demand relationships found by SHAP reflect Shenzhen's land use and cycling habits. There's no reason they'd hold elsewhere.

What does carry over is the method: aggregate features on an H3 grid, compare a linear model against non-parametric models to test for nonlinearity, hold out a real test set, and track training cost alongside accuracy. Reusing this in another city would need local data, a locally meaningful trip-end definition, and field validation, not a direct transfer of the trained model.

---

## Access this tutorial

Run this notebook in Colab, for GPU access and easier dependency setup.

## Contribute to this tutorial

See [GitHub's fork-and-pull-request guide](https://docs.github.com/en/get-started/exploring-projects-on-github/contributing-to-a-project#about-forking) to open a pull request.

Pull requests are reviewed by the Climate Change AI Tutorials team for relevance, accuracy, and conciseness.

## Climate Change AI Tutorials

See the [tutorials page](https://www.climatechange.ai/tutorials?) on our website for the full list of tutorials on AI and climate change.

## License

MIT License.

## Cite

**Plain Text**

Klemmer, K., Rahman, S., Wagner, F., Nachtigall, F., & Herrera, G. Y. (2026). Predicting Mobility Demand from Urban Features [Tutorial]. In Climate Change AI Summer School. Climate Change AI. https://doi.org/10.5281/zenodo.11619223

**BibTeX**

```
@misc{klemmer2026predicting,
  title={Predicting Mobility Demand from Urban Features},
  author={Klemmer, Konstantin and Rahman, Shafat and Wagner, Felix and Nachtigall, Florian and Herrera, Gabriela Yaulli},
  year={2026},
  organization={Climate Change AI},
  type={Tutorial},
  doi={https://doi.org/10.5281/zenodo.11619223},
  booktitle={Climate Change AI Summer School},
  howpublished={\url{https://github.com/climatechange-ai-tutorials/mobility-demand}}
}
```
