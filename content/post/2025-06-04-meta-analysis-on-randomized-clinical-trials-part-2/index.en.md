---
title: "Meta-Analysis on Randomized Clinical Trials - Part 2"
author: ["Amir Golzan"]
date: 2025-06-04 09:00:00 -0800
categories: ["Meta_Analysis", "R"]
tags: ["meta_analysis", "r"] # tags always lowercase
params:
  math: true
---



## Meta-Analysis

So far, we have understood what meta-analysis is, why it is used, and in which cases we should perform it. In this part, we will learn how to *extract* data from published manuscripts and *prepare* them for meta-analysis.

### What would be the best tool for saving our extracted data?
Our suggestion is **Excel** because it is easy to work with and facilitates any necessary conversions (e.g., units or dispersions across different studies). In our Excel file, each *row*  will be allocated to a study from which we are extracting data.

### What information should we extract? 
The answer depends on your main meta-analysis question. In GENERAL, the following information should be extracted from each study:

- **First Author's name**: It should be entered in the following format: e.g., Jackson et al. (2025).

- **Publication date**: This should be the date of publication, not *acceptance*.

- **Number of individuals in both control and intervention groups**: This refers to the number of individuals included in the *final* analysis of the study. It is usually reported in the first demographic table or in the study's flowchart.

- **The mean of the dependent variable BEFORE and AFTER the study in both groups**: In some studies, the mean change (After minus Before) in the dependent variable is reported, so it should be extracted directly. 

> **Note**: The **mean change** is the final parameter used in meta-analysis. In our Excel file, we should have four columns for the mean of the dependent variable in control and intervention groups (before/after), four columns for the standard deviation (SD) of the dependent variable in control and intervention groups (before/after). You should also include four additional columns for studies reporting the mean change directly (two for the mean change of control and intervention groups and two for SDs in both groups).

- **The SD for each dependent variable should be extracted**: In cases where other types of dispersion (SE, IQR, 95% CI, or range) are reported, they should be extracted and can be converted to SD as follows:

  - **Converting from SE:**  \( SD = SE \times \sqrt{n} \), where \( n \) is the sample size
  
  - **Converting from IQR:** \( SD = \dfrac{\text{upper quartile} - \text{lower quartile}}{1.35} \)
  
  - **Converting from 95% Confidence Interval (CI):**
  
      \( SD = \dfrac{\text{upper CI} - \text{lower CI}}{3.92} \times \sqrt{n} \), where \( n \) is the sample size
  
  - **Converting from Range:** \( SD = \dfrac{\text{maximum} - \text{minimum}}{4} \)


>  **Reference for conversions:** For guidelines and detailed methodology, see the [Cochrane Handbook for Systematic Reviews of Interventions](https://dariososafoula.wordpress.com/wp-content/uploads/2017/01/cochrane-handbook-for-systematic-reviews-of-interventions-2019-1.pdf) by Higgins JP et al. You can also refer to [Estimating the sample mean and standard deviation from the sample size, median, range and/or interquartile range](https://link.springer.com/article/10.1186/1471-2288-14-135) by Wan et al.


In clinical studies, some studies have more than *one* intervention group. You may ask, if a study has several intervention groups with different types of interventions instead of a single intervention group, what should be done? The solution is to consider each intervention group as a **separate** study. In other words, each group occupies its *own row* in the data-extraction Excel sheet. For example, if a study has three intervention groups, we create three rows in the Excel sheet.

> It should be noted that studies are included in the meta-analysis ONLY IF the difference between the intervention and control groups is **solely** the intervention of interest.

  - **The unit of the measured dependent variable.** We must convert all studies to the **same** unit. If you encounter different reported units for the same dependent variable across studies, select the most common unit and convert the others to it. Ensure that when you perform conversions, you *also* convert the *SD*.

  - **The dosage of the intervention.** This is important for dose-response analysis if we wish to perform it. It can also be helpful for subgroup analysis. If the unit of reported doses varies between studies, ensure you convert them to a uniform unit across studies.

  - **The duration of the intervention.** This is also important for dose-response analysis if we wish to perform it.

> This is the necessary information needed to conduct a meta-analysis. If you wish to perform subgroup analysis, you may also want to extract the following information:
> - **1) Study design**: Randomized, single-, or double-blinded.
>
> - **2) Country or location where the study was conducted**: Usually found in the first line of the methodology section.
>
> - **3) Gender of participants**: Usually available in the first demographic table
 
  Note: Sometimes statistical analyses in a study are performed separately by **gender**. In such cases, we create separate rows in Excel for men and women. Although this rarely occurs in interventional studies and is more common in observational studies, when it does happen, two rows are created for the same study in Excel. In other words, one study is treated as *two* studies.
  
>
> - **4) Type of intervention**: In the intervention and control groups (e.g., placebo, capsule, different food or dietary regimen, injection). These are usually reported in the intervention or study-design paragraph.
>
> - **5) Any co-intervention alongside the main intervention**: Sometimes the main intervention is accompanied by a weight-loss program, an exercise regimen, another supplement, etc. In other words, *both* the intervention and control groups receive the co-intervention (e.g., weight-loss program, exercise regimen, another supplement), and the only difference between the two groups is the administration of the primary intervention. This is usually reported in the intervention or study-design paragraph. We should create a column titled “co-intervention” in the Excel sheet for this purpose.
>
> - **6) Mean age in intervention and control groups (mean ± SD)**: Usually available in the first demographic table.
>
> - **7) Health status of study participants**: The study may be conducted on healthy individuals or individuals with a specific disease. This is usually reported in the study title or the first paragraph of the Methods section.
>
> - **8) Method of assessing the dependent variable**: By what method was the dependent variable assessed? Create a column named “outcome assessment.” For example: By what method was the CRP level assessed in this study?
>
> - **9) Matching or adjustment**: The term "matching" refers to variables that have been equalized between the intervention and control groups. For example, matching based on age means individuals are divided into the two groups so there is no difference in age between them. This is usually reported at the beginning of the Methods or Results section, or you can locate it by searching for the words “match” or “stratified.” In interventional studies, differences between the intervention and control groups can be accounted for in the statistical analysis through adjustment. This is typically reported in a table footnote or in the statistical analysis section.

  *Note*: One of the most important adjustments in clinical trials is controlling for baseline values of the outcome variables.
  
>
> - **10) Compliance (adherence to the intervention)**: The higher the adherence to the intervention in a study, the more valid its findings will be. Compliance may be reported as a percentage (e.g., 70% of the capsules were taken) or simply noted as high, moderate, or low. It is usually discussed in the first paragraph of the Results section, or you can locate it by searching for the words “compliance” or “adherence.”
>
> - **11) Location where measurements were taken**

## Quality of the Included Studies

The final topic to discuss in this part is whether the included studies have the **necessary quality** to perform a meta-analysis. To what extent do the included studeis exhibit methodological errors? This assessment provides an overall view of the quality of the included studies. Previously, the Jadad tool was used for this purpose, but currently, the **Cochrane tool** is used. This tool is one of the most valid tools for assessing quality. It evaluates the quality of a study using seven factors, which assess the biases that may occur in a study and thereby evaluate its overall quality. We will not go into details of this tool as it is beyond the scope of this series, but you can find more information in the [Cochrane Handbook](https://training.cochrane.org/handbook) by Julian P. T. Higgins and Sally Green.

---

That’s it for this part! In **Part 3**, we will dive into the analysis of the collected data.

**Stay tuned!**

---
