# Assignment 2 Report — Extended Veterinary Dose Calculation Tutor (DRAFT)

> Fill in every **[TODO]** before submitting. Keep the final report to 3 double-spaced pages (screenshots do not count).

## 1. Package name on Tutorshop

**[TODO: package name]**

Files in the package:

| File | Purpose |
|---|---|
| `HTML/dose_a2.html` | Student interface for Assignment 2 (copy of the Assignment 1 interface, extended) |
| `brd/doseTutorA2.brd` | Concrete behavior graph for the original problem (26.4-lb dog, amoxicillin) |
| `MassProduction/doseTutorA2_template.brd` | Mass Production template (variables written as `%(name)%`) |
| `MassProduction/doseTutorA2_table.tsv` | Mass Production problems table |
| `FinalBRDs/*.brd` | Generated problems (one per column of the table) |

## 2. New tutor features

### a. The tutor does the calculations for the student

Every computed answer box works like a calculator: total pounds, total ounces, weight in kg, total dose (mg), volume (mL), and the drug concentration. The student can type the calculation itself (for example `181/35.2` or `5.14*6`). The tutor evaluates it, checks it, and puts the rounded number in the box.

Each of these steps has two parts:

1. **Input matcher (Formula Match, "boolean").** The formula evaluates the student's expression with `algEval(input)` and compares it with the correct value. For example, for weight in kg on Way A:
   `hasValue(algEval(input)) && hasValue(totalPounds) && abs(algEval(input) - (totalPounds / 2.2)) <= 0.0051 + 0.005 * abs(totalPounds / 2.2)`
2. **Input substitution.** After a correct entry, the box shows `fmtDecimal(algEval(input), 2)`, so `181/35.2` becomes `5.14`. The concentration box uses `fmtNormal(algEval(input))`, so `240/2` becomes `120`.

The given information (pounds, ounces, dose rate, concentration) is **not** copied for the student. Picking the right numbers out of a word problem is a skill a vet tech has to learn, especially when the weight is in pounds plus ounces or the label gives "240 mg in 2 mL".

### b. Use of formulas

- **Steps (all 10 answer steps use Formula Match):**
  - The given values accept any equivalent number (for example `26.40` for `26.4`), and anything that isn't a number is rejected.
  - Later steps use the student's own earlier answers as variables (`totalPounds`, `totalOunces`, `convertedWeight`, `totalDose`). So each step is judged against what the student entered before, not one fixed number.
  - `hasValue(...)` makes sure a step is only judged once the step it depends on has a value.
  - The tolerance accepts both "round each step to two decimals" and "keep full precision", but rejects wrong rounding (for example 0.25 mL when the answer is 0.257 mL).
- **Hints (4 hints use embedded formulas):** the last hint for kg (Way A and Way B), total dose, and volume uses `<%= ifThen(hasValue(x), ..., ...) %>`. It shows the student's own numbers, for example "Divide the total ounces by 35.2: type 181/35.2". If the earlier step isn't done yet, it says which step to do first.

### c. Added solution path

The weight-conversion step now has two solution paths:

- **Way A (pounds):** total pounds = lb + oz ÷ 16, then kg = total pounds ÷ 2.2
- **Way B (ounces):** total ounces = lb × 16 + oz, then kg = total ounces ÷ 35.2

Both paths lead to the same state in the behavior graph, so total dose, volume and Done are shared. The kg hint follows whichever way the student chose.

### d. Groups to constrain step order

| Group | Ordered? | Steps | Why |
|---|---|---|---|
| Given information | unordered | pounds, ounces, dose rate, concentration | The givens can be found in any order. |
| Calculations | ordered | weight conversion → total dose → volume | Each calculation needs the result of the one before it. |
| └ Weight conversion (nested) | ordered | Way A or Way B, then kg | The total weight has to exist before it can be converted. |

The top level is unordered, so a student can start calculating before filling in every given. Only the order that the math requires is enforced. Done is accepted only after every step is complete.

### e. Feedback from Assignment 1

These changes were made to the Assignment 1 tutor before starting Assignment 2:

- The default "Please enter X in the highlighted field" hints were replaced with formula-based hints (for example "kg = pounds ÷ 2.2").
- The given information is no longer pre-filled; students identify it from the problem, with question-style hints.
- The full problem statement is visible.
- A Done step with a success message was added.

**[TODO: add or adjust anything specific from the instructor's feedback]**

## 3. Tutor testing (two users who are not domain experts)

**[TODO after testing — do not fill in from imagination]**

- Participant 1 (background): what went well / what didn't
- Participant 2 (background): what went well / what didn't

### Redesign ideas

**[TODO: based on your observations]**

## 4. Short answers

- a. Most difficult part: **[TODO]**
- b. Time spent: **[TODO]**
- c. Experience with CTAT's LLM-based Mass Production: **[TODO]**
- d. Likes / dislikes / suggestions for CTAT: **[TODO]**

---

## Appendix A — Problems

All problems below were made with Mass Production from one template (`MassProduction/doseTutorA2_template.brd`) and one table (`MassProduction/doseTutorA2_table.tsv`). Answers use 2-decimal rounding at each step; the tutor also accepts full-precision answers.

| File | Source | Problem | Answers |
|---|---|---|---|
| `p0_dog_amoxicillin.brd` | Original (Assignment 1) | 26.4-lb dog, amoxicillin 10 mg/kg, 50 mg/mL | 26.40 lb / 422.40 oz → 12.00 kg → 120.00 mg → 2.40 mL |
| `p1_ferret_telazol.brd` | Assignment list, Problem 1 | Ferret 1 lb 10 oz, Telazol 22 mg/kg, 100 mg/mL | 1.63 lb / 26 oz → 0.74 kg → 16.28 mg → 0.16 mL |
| `p2_cat_zeke_selamectin.brd` | Assignment list, Problem 2 | Zeke 11 lb 5 oz, selamectin 6 mg/kg, 240 mg / 2 mL | 11.31 lb / 181 oz → 5.14 kg → 30.84 mg → 0.26 mL |
| `p3_cat_skinny_famotidine.brd` | Assignment list, Problem 3 | Skinny Cat 6 lb 6 oz, famotidine 0.5 mg/kg, 200 mg / 20 mL | 6.38 lb / 102 oz → 2.90 kg → 1.45 mg → 0.15 mL |
| `p4_dog_fluffy_prednisolone.brd` | Assignment list, Problem 4 | Fluffy 26 lb 7 3/4 oz, prednisolone 1 mg/kg, 5 mg/cc | 26.48 lb / 423.75 oz → 12.04 kg → 12.04 mg → 2.41 mL |
| `p5_kittens_metronidazole.brd` | Assignment list, Problem 5 | Kitten 1 lb 9 oz, metronidazole 10 mg/kg, 80 mg/mL | 1.56 lb / 25 oz → 0.71 kg → 7.10 mg → 0.09 mL |
| `p6_cat_feral_doxycycline.brd` | Assignment list, Problem 6 | Feral cat 7 lb, doxycycline 10 mg/kg, 50 mg/mL | 7.00 lb / 112 oz → 3.18 kg → 31.80 mg → 0.64 mL |
| `p7_cat_banksy_convenia.brd` | Assignment list, Problem 7 | Banksy 8 1/2 lb, Convenia 8 mg/kg, 800 mg / 10 mL | 8.50 lb / 136 oz → 3.86 kg → 30.88 mg → 0.39 mL |
| **[TODO]** | Generated with CTAT's "Prompt LLM" tab | | |

## Appendix B — Suggested prompt for CTAT's "Prompt LLM" tab

Paste this into the customizable part of the prompt and save it in the package:

> Generate one new veterinary dose calculation problem for a common scenario at a small-animal vet clinic (dog or cat). Use a drug that is actually used for that species, at a realistic mg/kg dose. Give the animal's weight in pounds and ounces, and give the drug concentration as it appears on a real label (mg per mL, or mg in a stated number of mL). Fill every variable in the table. `weightLb`, `weightOz`, `doseRate`, and `concentration` (mg per mL, after dividing if the label gives mg per several mL) must be plain numbers. Compute the answer rows with 2-decimal rounding at every step: `totalPoundsAnswer` = lb + oz/16, `totalOuncesAnswer` = lb × 16 + oz, `weightKgAnswer` = `totalPoundsAnswer` / 2.2, `totalDoseAnswer` = `weightKgAnswer` × `doseRate`, `volumeAnswer` = `totalDoseAnswer` / `concentration`. `concentrationHint` should tell the student how to get mg per mL from the label. Do not use tabs or double quotes inside any value.

Before using the LLM's problem, check that:

- The drug is suitable for the species, and the mg/kg dose and the weight are realistic (check with a web search or a second LLM).
- Every answer row matches the formulas above (recompute by hand).
- No cell is blank (use `NA` if needed) and the first row is a new `.brd` filename.
