---
title: "Meta-Analysis on Randomized Clinical Trials - Part 1"
author: ["Amir Golzan"]
date: 2025-05-16 09:00:00 -0800
categories: ["Meta_Analysis", "R"]
tags: ["meta_analysis", "R"] # tags always lowercase
---



## Meta-Analysis

Imagine you’re studying the effects of non-nutritive sweeteners on blood pressure. You’re curious about whether they eventually increase, decrease, or have no impact on blood pressure. You review various studies and find quite conflicting results. Some studies suggest that consuming non-nutritive sweeteners can increase blood pressure, while others show quite opposite results. Also, some studies indicate no effect on blood pressue from consuming non-nutritive sweeteners. This situation presents a challenge that you may address by conducting a meta-analysis. **Meta-analysis pools data from multiple studies to provide a more precise estimate based on existing evidence**. Seems quite intriguing, doesn’t it?

Before getting into meta-analysis itself, let's have a better understanding of **different types of studies**:

### Primary Studies
These types of studies are conducted on humans, animals, and so on. In technical terminology, they’re referred to as *original* studies.

### Secondary Studies
These types of studies are conducted on the findings of primary studies, including:

- **Narrative reviews**: They can be conducted with as few as two studies. they may not have a specific objective and might cover a broad topic, for example: Efficacy of vitamin E supplementation on routine diseases in women. Also, they may not include every study available within the topic area under review.
- **Systematic reviews**: They have a defined objective, for example: Efficacy of vitamin E supplementation on blood pressure in adult women. Also, all studies within the scope of the topic under review must be included.
- **Meta-analyses**: Similar in every respect to a systematic review, with the difference that *statistical analysis* is also performed. Meta-analysis is essentially a re-analysis of the findings from primary studies. In a meta-analysis, the quantitative data from primary studies (such as means, odds ratios, etc.) are combined into a single result (for example, one mean, one odds ratio, etc.) Therefore, drawing the final conclusion in a meta-analysis is easier. An example would be: Effect of vitamin E supplementation on CRP concentrations in adults: A meta-analysis

## Meta-analyses sit at the top of the evidence pyramid

So you may ask why meta-anlysis? In the biomedical sciences field, there’s a hierarchical evidence pyramid that outlines the significance of different studies conducted in this area. Interestingly, meta-analysis studies, which combine existing studies, are placed at the top of this pyramid, highlighting their importance.

![](images/explaining-the-fact-level.png)
Source: [The Logic of Science](https://thelogicofscience.com/2016/01/12/the-hierarchy-of-evidence-is-the-studys-design-robust/)

In the initial step, we should have a better understanding of different types of RCTs. We want to know how we can differentiate them and how we should treat each study’s data based on its type.

## Study Designs

- **Randomized Clinical Trial (RCT)**: Participants are randomly assigned to intervention and control groups.
- **Single-blind Trial**: Participants are unaware of the intervention details.
- **Double-blind Trial**: Both participants and researchers are blind to assignments.
- **Triple-blind Trial**: Participants, researchers, and analysts are blind to group assignments.
- **Semi-experimental (Quasi-experimental) Study**: Lacks randomization.
- **Open Trial**: No control group; only an intervention group.
- **Community Trial**: Interventions at the community or group level. For example, iron fortification of flour or vitamin D supplementation programs implemented across schools.

## Choosing a Topic for Meta-Analysis

To select a topic, we should consider:

- **Principle 1**) **Type of study**: Clinical trial vs. observational study. _We focus on clinical trials in this series._

- **Principle 2**) **Study design**: RCTs are preferred for fewer biases.

- **Principle 3**) **Type of intervention**: e.g., supplementation, drug therapy, dietary intervention, educational intervention, etc.

- **Principle 4**) **Target population**: Children vs. adults, patients vs. healthy individuals.

- **Principle 5**) **Dependent variables**: The dependent variables to be considered: inflammatory markers, lipid profile, weight, etc.

- **Principle 6**) **Intervention duration**: The minimum duration of intervention: one day, one week, two weeks, etc.

- **Principle 7**) **Number of studies**: To perform a meta-analysis, there must be at least three studies (in accordance with Principles 1–6) in the topic area under review. These three studies must provide the necessary data to carry out a meta-analysis on a single dependent variable. 

**Required data** for interventional meta-analysis:

- **Option 1:** The mean and standard deviation (SD) of the dependent variable before and after the intervention, reported separately for the intervention and control groups.

- **Option 2:** The mean change (i.e., difference between pre- and post-intervention) and its SD for the dependent variable, again separately for the intervention and control groups.

> **Note:** Some studies report SE (standard error), IQR (interquartile range), **95% CI** (confidence interval), or **range**. These can all be converted to SD for pooling.

- **Principle 8**) Is there an existing meta-analysis on our topic?

If a meta-analysis on our topic has already been published, we should usually drop the topic—since it no longer has publishable value—unless:

- The published meta-analysis contains **methodological flaws**.
- It is possible to **update** the meta-analysis due to **several new studies** published in that field since then.

---

Well, that seems like a lot for now! In **Part 2**, we will look at how we should prepare **our dataset for performing meta-analyses**.

**Stay tuned!**

---

**Reference:** For guidelines and detailed methodology, see the [Cochrane Handbook](https://training.cochrane.org/handbook) by Julian P. T. Higgins and Sally Green.
