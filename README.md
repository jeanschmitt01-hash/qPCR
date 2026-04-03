# qPCR analysis

The purpose of this code is to automate qPCR analysis from the ViiA7 machine after a comparative Ct run using TaqMan reagents. For this analysis, both the reference gene (= housekeeping gene) and the target gene must be amplified in the same well. It is also required to set meaningful sample names in the machine, which allows the Python script to plot gene expression per condition (sample name).

This workflow is an adaptation of the LinRegPCR method (Untergasser et al., $2021)^{1}$ suitable for data obtained from the ViiA7 machine. Every modification from the initial method is mentioned in this document.

First of all, as the machine already provides baseline correction and Ct identification, these steps are not recalculated using the LinRegPCR method.

Here are the steps of calculation performed by the code:

- ### Calculating reaction efficiency and removing outliers:

First of all, the exponential phase of the amplification curve is identified based on the last point of the exponential phase, characterized by the second derivative maximum SDM $^{1}$. From that point, the four previous points are traced back to ensure at least three consecutive cycles in the exponential phase to calculate the PCR efficiency $^{1}$.

The fluorescence values of the exponential phase (the four points mentioned above) of the baseline-corrected amplification curve are log2-transformed in order to perform a linear regression. The slope × 100 corresponds to the reaction efficiency in percentage.

The user can set minimum and maximum efficiency thresholds to remove reactions for which the efficiency is not considered sufficient. Untergasser et al (2020)$^{1}$ use the Grubbs test to remove outlier reaction efficiencies, which is a technique used to remove only one outlier at a time and may not be optimal when several outliers are present.

- ### Calculating the efficiency-corrected target quantity $N_0$

First of all, an average PCR reaction efficiency per assay (= per GOI, not per well) is calculated in order to obtain a less variable mean PCR efficiency per $assay^{1}$.

For each well, the efficiency-corrected target quantity $N_0$ is calculated with the following formula $^{1}$:

$N_0 = N_q E^{-Cq}$   with E being the average reaction efficiency for the GOI.

Note: The well-known ΔΔCt method, in which data are processed using Ct values, is avoided because it may introduce bias during data processing, particularly when averaging Ct values. Ct values correspond to the log₂ of the initial quantity, and therefore the mean of log₂-transformed data is not equivalent to the log₂ of the mean of the non-transformed data $^{2}$.

For each well, the expression of the GOI is normalized by the expression of the reference gene (housekeeping):

$N_0 = N_0(GOI) / N_0(housekeeping)$

Finally, these data are plotted in a bar plot according to the sample name (experimental condition), and an Excel file is generated containing the following information:
- the data from the initial results sheet
- reaction efficiency (in %)
- real efficiency (multiplication factor of the cDNA quantity between two successive cycles)
- efficiency-corrected target quantity $N_0$
- normalized quantity with the housekeeping gene
- an additionnal sheet with the outliers


The initial LinRegPCR method, developed by Untergasser et al. (2021), is licensed under a Creative Commons Attribution 4.0 International License, which permits use, sharing, adaptation, distribution, and reproduction in any medium or format. To view a copy of this licence, visit:

http://creativecommons.org/licenses/by/4.0/

Note: This code is still in progress and requires further optimizations (like using vector functions instead of loops, implementing statistics test to the generated data...).   

## References 
1. Untergasser, A., Ruijter, J.M., Benes, V. et al. Web-based LinRegPCR: application for the visualization and analysis of (RT)-qPCR amplification and melting data. BMC Bioinformatics 22, 398 (2021). https://doi.org/10.1186/s12859-021-04306-1

2. Lixiang, F., Rongqian, Z., Zhang, K., & Yang, W. (2025). Comparison of the 2 -C T method and the 2 -ΔΔC T method for real-time qPCR data analysis. openRxiv. https://doi.org/10.1101/2025.07.16.665089



