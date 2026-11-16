# Score explanations

These notes match the heart-disease notebook. The worked example is the RBF support vector machine on the 61 held-out patients: 28 with disease and 33 without.

## One-hot encoding

`sex`, `cp`, `fbs`, `restecg`, `exang`, `slope`, and `thal` are category codes stored as integers. `thal` is the clearest case: 3 means normal, 6 means a fixed defect, and 7 means a reversible defect. Those numbers are labels. A 7 is not a larger amount of `thal` than a 3.

One-hot encoding replaces the single `thal` column with one column per level. A patient has a 1 in the column for their level and a 0 in the others:

| Patient's `thal` | `thal` is 3 | `thal` is 6 | `thal` is 7 |
|---|---:|---:|---:|
| 3 | 1 | 0 | 0 |
| 6 | 0 | 1 | 0 |
| 7 | 0 | 0 | 1 |

Age, cholesterol, heart rate, and `ca` stay ordinary numbers. `ca` is a count of vessels from 0 to 3.

## Search over C

`C` is a knob on logistic regression and the support vector machine. It controls how closely the model is allowed to follow the training patients.

- A small `C`, such as `0.01` or `0.1`, keeps the model simple and ignores small quirks in the training rows.
- A large `C`, such as `10`, lets the model follow the training patients more closely.

The search tried `0.01`, `0.1`, `1`, and `10` for logistic regression, and `0.1`, `1`, and `10` for the RBF support vector machine. Each value was scored by disease F1 on the training folds, and the winner was kept. Logistic regression kept `C = 0.1`. The RBF support vector machine kept `C = 10`.

## Gamma

`gamma` belongs to the RBF support vector machine. Each training patient pulls the decision boundary nearby, and `gamma` sets how far that pull reaches.

- A small `gamma`, such as `0.01`, reaches far, so the boundary stays smooth.
- A large `gamma`, such as `0.1`, reaches only the nearest patients, so the boundary can memorize individual people.

The search tried `"scale"`, `0.1`, and `0.01`. The winner was `0.01`. `"scale"` is scikit-learn's automatic default, based on the spread of the features.

## Precision, recall, accuracy, and F1

On the held-out set the RBF support vector machine found 23 of the 28 disease cases, missed 5, and raised 6 false alarms. It also labeled 27 healthy patients correctly.

**Precision** answers: of the people the model called "disease," how many really had it? It called 29 people diseased (23 true cases plus 6 false alarms), so precision is 23/29 = **0.79**. A low precision means many false alarms.

**Recall** answers: of the people who really had disease, how many did the model find? It found 23 of 28, so recall is 23/28 = **0.82**. A low recall means missed cases. For this problem, a missed case is the costlier error.

**Accuracy** answers: how many people, of either class, got the right label? The model was right about 23 diseased patients and 27 healthy patients, so accuracy is 50/61 = **0.82**. Accuracy mixes both classes into one number, so a model can look strong by mostly getting the larger group right.

**F1** combines precision and recall into one number. It is their harmonic mean:

```text
F1 = 2 * precision * recall / (precision + recall)
```

For this model, that is 2 * (0.79 * 0.82) / (0.79 + 0.82) = **0.81**. F1 stays low if either precision or recall is low. The disease search maximized disease F1 so that a high accuracy with many missed cases would not win.

## The classification report

The report prints precision, recall, and F1 once per class. For the same model:

```text
              precision    recall  f1-score   support

  no disease       0.84      0.82      0.83        33
     disease       0.79      0.82      0.81        28

    accuracy                           0.82        61
   macro avg       0.82      0.82      0.82        61
weighted avg       0.82      0.82      0.82        61
```

- **precision**, **recall**, and **f1-score** on a row are those three scores for that class alone. The disease row treats disease as the class being found. The no-disease row treats "healthy" as the class being found, which is why its precision and recall use the healthy patients as the target.
- **support** is a count, not a score. It is how many held-out patients truly belong to that row: 33 healthy, 28 with disease, 61 in total. Recall's denominator is the support. A disease recall of 0.82 means about 82% of those 28 patients.
- **accuracy** is the single overall fraction correct, 0.82 of all 61 patients. It has no precision or recall of its own, so those cells are blank.
- **macro avg** averages the per-class scores with each class weighted equally.
- **weighted avg** averages the per-class scores with each class weighted by its support, so the larger class counts more.
