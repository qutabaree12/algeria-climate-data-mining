# Exploratory Data Analysis of ERA5-Land Climate Data over Algeria (2015–2026)

## 1. Introduction and Objectives

The objective of this stage is to perform an Exploratory Data Analysis (EDA) of ERA5-Land monthly climate data covering Algeria from January 2015 to September 2026.

The purpose of this analysis is to understand the structure and quality of the dataset, investigate the statistical properties of the climate variables, identify temporal and spatial patterns, and examine relationships between meteorological parameters.

This exploratory stage is also intended to prepare the dataset for the subsequent Data Mining tasks, particularly the prediction of next-month 2-meter temperature classes and the identification of climate patterns through clustering.

The analysis focuses on twelve meteorological variables, including temperature, dewpoint temperature, radiation fluxes, evaporation, runoff, precipitation, wind components, and surface pressure.

## 2. Dataset Description and Initial Exploration

### 2.1 Data Loading

The dataset was loaded from a Parquet file using the Pandas library. The `read_parquet()` function was used to import the data into a DataFrame.

Google Drive was mounted in Google Colab to access the dataset.

### 2.2 Dataset Structure

Several initial operations were performed to understand the structure of the dataset:

- `DataFrame.shape` was used to determine the number of rows and columns.
- `DataFrame.info()` was used to inspect data types and non-null values.
- `nunique()` was used to identify the number of unique values in each column.
- The minimum and maximum timestamps were examined to determine the temporal coverage.
- The number of unique geographical cells and months was calculated.
- The number of available months was checked for each year.

The dataset contains **764,925 observations**, corresponding to **5,425 geographical grid cells observed over 141 months**, from January 2015 to September 2026.

The relationship between these dimensions is:

5,425 × 141 = 764,925 observations.

This confirms that the dataset forms a complete cell-month panel over the available period. However, 2026 is a partial year containing only nine months.

### 2.3 Geographic Grouping

To facilitate regional comparisons, the geographical cells were divided into three latitude-based groups:

- South: latitude between 0° and 26°.
- Centre: latitude between 26° and 33°.
- North: latitude between 33° and 45°.

The `pandas.cut()` function was used to assign each observation to a geographical group.

These groups are simplified latitude bands designed for exploratory analysis. They should not be considered official Algerian climatic regions.

## 3. Data Quality Assessment

Before performing statistical analysis, several checks were conducted to evaluate the quality and consistency of the dataset.

### 3.1 Missing Values

The number and percentage of missing values were calculated for every column using:

- `isna().sum()` to count missing values.
- `isna().mean() * 100` to calculate their percentage.

**Interpretation:**

The dataset contains no missing values in the analysed columns. Therefore, no missing-value imputation was required during this exploratory stage.

### 3.2 Duplicate Records

Two types of duplicate checks were performed:

1. Duplicate combinations of `cell_id` and `time`.
2. Completely duplicated rows.

The `duplicated()` function was used for both checks.

**Interpretation:**

No duplicate records were detected. Each geographical cell therefore has a unique observation for each month in the analysed period.

### 3.3 Zero Values

The number of zero values was calculated for all meteorological variables.

This check is particularly important for precipitation, runoff, and evaporation because zero or near-zero values can be physically meaningful.

**Interpretation:**

The runoff variable (`ro`) contains approximately 148,642 zero values, representing about 19.43% of the observations.

This does not indicate missing or invalid data. It reflects the fact that runoff can be absent or negligible during many months and geographical conditions.

The distribution of runoff must therefore be interpreted in the context of its physical characteristics rather than treating zero values as errors.

## 4. Descriptive Statistical Analysis

Descriptive statistics were calculated to summarize the central tendency, dispersion, and distributional characteristics of the twelve climate variables.

The following statistical measures were computed:

- **Mean:** the arithmetic average of the observations.
- **Median:** the middle value after sorting the observations.
- **Trimmed mean:** the mean calculated after removing a proportion of the smallest and largest observations.
- **Standard deviation:** measures the dispersion of observations around the mean.
- **Variance:** represents the squared dispersion.
- **Range:** the difference between the maximum and minimum values.
- **First and third quartiles (Q1 and Q3):** describe the lower and upper quarters of the distribution.
- **Interquartile range (IQR):** calculated as Q3 − Q1.
- **Skewness:** measures the asymmetry of a distribution.

The trimmed mean was calculated using `scipy.stats.trim_mean()` with a trimming proportion of 10%.

**Interpretation:**

The mean 2-meter temperature (`t2m`) is approximately 23.9°C, while its median is around 24.2°C. The relatively small difference between these values and the skewness close to zero indicate an almost symmetric overall distribution.

However, this does not mean that temperature follows a normal distribution. Its distribution is strongly influenced by seasonal variations, as demonstrated by the histogram analysis.

Other variables, particularly precipitation (`tp`), runoff (`ro`), latent heat flux (`slhf`), and evaporation (`e`), exhibit strong asymmetry. Their means and standard deviations can be influenced by relatively few large observations.

For these variables, the median and trimmed mean provide additional information about their typical values.

## 5. Distribution Analysis

Three complementary graphical techniques were used to investigate the distributions of the climate variables:

1. Histograms.
2. Boxplots.
3. Quantile-Quantile (Q-Q) plots.

### 5.1 Histograms

Histograms were generated using Matplotlib with 50 bins for each variable.

A histogram represents the frequency distribution of a variable and helps identify:

- Symmetry or asymmetry.
- Unimodal or multimodal distributions.
- Concentration of observations.
- Extreme values and long tails.

**Interpretation:**

The `t2m` histogram presents two noticeable peaks, approximately around 17°C and 34°C. These peaks are associated with colder and warmer periods of the year, particularly winter and summer.

This bimodal structure reflects the strong seasonal nature of temperature in Algeria.

The `d2m` distribution is more concentrated around lower values, while `slhf` and `e` exhibit strong asymmetry and negative values.

The `ro` and `tp` histograms show that most observations are concentrated near zero, with a relatively small number of large values. This indicates that precipitation and runoff events are not uniformly distributed across the observations.

### 5.2 Boxplots

Boxplots were generated using Seaborn on a random sample of up to 200,000 observations.

A boxplot summarizes a distribution using:

- The median.
- The first and third quartiles.
- The interquartile range.
- Whiskers.
- Potential outliers.

This technique allows distributions and extreme observations to be compared across variables.

**Interpretation:**

The boxplots confirm that `t2m` has a relatively wide distribution, while some other variables are concentrated within a narrower range.

The variables `slhf`, `e`, `ro`, and `tp` exhibit numerous points outside their whiskers. This is consistent with their highly skewed distributions.

These points should not automatically be considered errors. In climate data, extreme observations may represent genuine meteorological events, such as intense rainfall or strong evaporation.

### 5.3 Quantile-Quantile (Q-Q) Plots

Q-Q plots were generated using `scipy.stats.probplot()` to compare the empirical quantiles of each variable with the theoretical quantiles of a normal distribution.

If the observations approximately follow a normal distribution, the points should lie close to a straight reference line.

**Interpretation:**

The Q-Q plots reveal substantial deviations from normality for several variables.

The `t2m` plot exhibits a curved, S-shaped pattern, reflecting its seasonal and bimodal distribution.

The `ro` and `tp` plots show strong deviations in their upper tails, indicating the presence of highly skewed distributions and extreme observations.

The wind components (`u10` and `v10`) appear closer to the reference line than several other variables, although this visual assessment does not establish perfect normality.

Overall, these results show that the normality assumption cannot be made for all climate variables. This observation supports the use of statistical methods that are less sensitive to distributional assumptions, such as Spearman correlation.

## 6. Relationships Between Meteorological Variables

Scatter plots were used to investigate the relationships between `t2m` and the other meteorological variables.

A random sample of 20,000 observations was selected to make the visualizations more manageable.

Additional scatter plots were created for selected pairs of variables to investigate relationships that could not be fully understood through temperature-related plots alone.

### 6.1 Relationships with 2-Meter Temperature

The analysis focused on the relationships between `t2m` and:

- Dewpoint temperature (`d2m`).
- Solar radiation (`ssr`).
- Thermal radiation (`str`).
- Sensible heat flux (`sshf`).
- Latent heat flux (`slhf`).
- Evaporation (`e`).
- Runoff (`ro`).
- Precipitation (`tp`).
- Wind components (`u10`, `v10`).
- Surface pressure (`sp`).

**Interpretation:**

The strongest visible relationships with temperature involve `ssr`, `str`, and `sshf`.

Solar radiation (`ssr`) generally increases with temperature, whereas `str` and `sshf` show negative relationships with temperature in the analysed data.

However, the scatter plots remain relatively dispersed. This means that none of these variables can independently explain all temperature variations.

The precipitation-related variables (`tp` and `ro`) are concentrated near zero and do not exhibit a clear linear relationship with temperature.

The relationship between `d2m` and `t2m` is also influenced by seasonal effects. The observations form different groups corresponding approximately to colder and warmer periods.

Surface pressure (`sp`) suggests a geographical influence: high temperatures are more frequently observed in areas with higher surface pressure, while high-altitude regions tend to have lower temperatures.

### 6.2 Relationships Between Selected Climate Variables

Additional scatter plots were produced for ten selected variable pairs. Pearson and Spearman coefficients were displayed directly in the plots.

The objective was to investigate relationships such as:

- Solar radiation and thermal radiation.
- Solar radiation and sensible heat flux.
- Dewpoint temperature and precipitation.
- Precipitation and runoff.
- Precipitation and evaporation.
- Wind components and precipitation.

**Interpretation:**

A strong negative relationship was observed between `ssr` and `str`, as well as between `ssr` and `sshf`.

This indicates that these radiation and energy-related variables contain overlapping information.

The relationship between precipitation (`tp`) and runoff (`ro`) is particularly interesting. Runoff remains close to zero for many observations and increases more noticeably when precipitation becomes sufficiently high.

This suggests a threshold-like relationship rather than a simple linear relationship.

The relationship between `d2m` and `tp` also indicates that higher precipitation is generally associated with more humid atmospheric conditions.

For `tp` and `e`, the negative relationship must be interpreted while considering the sign convention of the evaporation variable. A negative numerical value does not necessarily represent a physically negative evaporation intensity.

These observations demonstrate that climate variables can interact through complex relationships that are not always adequately described by linear correlation coefficients.

## 7. Outlier Detection

Two statistical techniques were implemented to identify potential outliers:

1. The Interquartile Range (IQR) method.
2. The Z-score method.

### 7.1 IQR Method

The IQR method is based on the first and third quartiles:

IQR = Q3 − Q1

An observation is classified as a potential outlier if it falls outside the following interval:

Lower bound = Q1 − 1.5 × IQR

Upper bound = Q3 + 1.5 × IQR

The number and percentage of observations outside these limits were calculated for each variable.

### 7.2 Z-Score Method

The Z-score measures the distance between an observation and the variable's mean in units of standard deviations:

Z = (x − mean) / standard deviation

Observations with an absolute Z-score greater than 3 were considered potential outliers.

### 7.3 Comparison and Interpretation

The two methods produced different results.

The IQR method identified relatively few potential outliers for variables such as `t2m`, `ssr`, `str`, `d2m`, and the wind components.

However, it identified a much larger proportion of potential outliers for `slhf`, `e`, `ro`, and `tp`.

This is mainly explained by their strongly skewed distributions. In particular, the IQR of runoff is very small because many observations are close to zero. Consequently, even moderate non-zero values may be classified as outliers.

The Z-score method identified fewer extreme observations for these variables because their means and standard deviations are themselves influenced by large values.

**Conclusion:**

The presence of statistical outliers does not necessarily indicate incorrect climate measurements.

For this reason, the analysis does not automatically remove these observations. Extreme rainfall and runoff values may represent genuine climatic events.

Instead, the results suggest that robust preprocessing techniques should be considered during the modelling stage.

## 8. Temporal Analysis

Temporal analysis was performed to understand how the climate variables evolve over time and how their behaviour changes across the months and years.

### 8.1 Monthly Time-Series Analysis

For each month, the mean value of every climate variable was calculated across all geographical cells.

The resulting time series were plotted to examine temporal fluctuations and potential long-term patterns.

**Interpretation:**

The `t2m` time series displays a clear seasonal oscillation, with temperatures generally increasing during summer and decreasing during winter.

The radiation variables show a noticeable discontinuity between September 2022 and February 2024.

During this period, `ssr`, `str`, and `sshf` exhibit values that differ considerably from their usual behaviour.

Other variables, such as precipitation and runoff, display irregular fluctuations, which may correspond to changes in rainfall conditions.

### 8.2 Monthly Seasonal Climatology

The average value of each variable was calculated for every calendar month using the `groupby("month")` operation.

This makes it possible to compare the average behaviour of January, February, March, and the other months independently of the year.

**Interpretation:**

The temperature reaches its lowest average value in January, approximately 12°C, and its highest value in July, approximately 34.5°C.

Solar radiation reaches its maximum around June and its minimum in December.

Precipitation and runoff are generally higher during the cooler part of the year, particularly around March, and become very low during summer.

The dewpoint temperature follows a seasonal pattern, reaching higher values during late summer.

These observations confirm that seasonality is a major component of the climate data.

### 8.3 Monthly Temperature Boxplots

Boxplots of `t2m` were generated for each calendar month.

This technique makes it possible to compare the median temperature and its dispersion across the twelve months.

**Interpretation:**

The median temperature increases from approximately 12°C in January to approximately 35°C in July.

The temperature distribution within each month is considerably narrower than the overall temperature distribution.

This indicates that the month of the year explains a substantial part of the variability in temperature.

Some relatively low summer observations may correspond to high-altitude geographical cells, which remain cooler than surrounding desert areas.

### 8.4 Annual Means and Linear Trend Estimation

Annual averages were calculated using only complete years containing twelve months.

This avoids directly comparing complete years with the incomplete year 2026.

A linear regression was then fitted to the annual mean temperature using `scipy.stats.linregress()`.

The regression provides:

- The estimated annual slope.
- The p-value associated with the slope.
- An indication of whether the observed linear relationship is statistically significant under the regression assumptions.

**Results and Interpretation:**

The annual mean temperature increased from approximately 23.09°C in 2015 to approximately 24.58°C in 2024, before decreasing to approximately 24.02°C in 2025.

The estimated linear slope is approximately:

**+0.107°C per year**

The associated p-value is approximately 0.009.

Although this result indicates a statistically significant positive linear relationship under the fitted regression, it must be interpreted cautiously.

The analysis is based on only eleven complete annual observations. Furthermore, successive years may not be statistically independent, and the estimated trend is influenced by the relatively warm years near the end of the period.

Therefore, this result describes the observed 2015–2025 period and should not be interpreted as a definitive long-term climate trend.

## 9. Detection of an Anomalous Period in the Radiation Variables

An additional diagnostic analysis was conducted after observing unusual behaviour in `ssr`, `str`, and `sshf`.

The objective was to determine whether the observed changes represented normal seasonal variability or a potential inconsistency in the dataset.

### 9.1 Monthly Climatology Reference

The monthly mean values of the accumulated variables were calculated over Algeria.

The reference climatology was constructed using observations outside the years 2022, 2023, and 2024.

For each variable, the observed monthly value was divided by the corresponding monthly climatological average.

The resulting ratio is:

Observed-to-climatology ratio = Observed monthly value / Reference monthly climatology

A ratio close to 1 indicates that the observation is close to its reference climatology.

A ratio below 1 indicates a lower value than the reference, while a ratio above 1 indicates a higher value.

### 9.2 Identification of Suspicious Months

The analysis focused particularly on `ssr`, `str`, and `sshf`.

A month was classified as suspicious when all three variables simultaneously fell below 75% of their corresponding monthly climatological values.

This threshold was used as an exploratory diagnostic criterion.

**Interpretation:**

The analysis identified an uninterrupted period of 18 suspicious months, from September 2022 to February 2024.

During this period, the average observed-to-climatology ratios were approximately:

- `ssr`: 0.54.
- `str`: 0.49.
- `sshf`: 0.49.

These values indicate that the three variables were approximately half their reference levels.

The ratios were also relatively stable during this period, followed by a return towards normal values in March 2024.

This simultaneous and persistent deviation across several related variables raises concerns about a possible data acquisition, processing, or source inconsistency.

It should not be interpreted as a confirmed physical climate phenomenon without further verification against the original ERA5-Land source and its metadata.

The variables `slhf` and `e` also show lower values during this period, but their normal variability is larger, making the anomaly less conclusive.

For `tp` and `ro`, the analysis did not identify equally strong evidence of a systematic anomaly.

The temperature variable `t2m` does not exhibit the same discontinuity.

**Conclusion:**

The period from September 2022 to February 2024 should be investigated before using the affected radiation variables in subsequent modelling.

This notebook identifies and diagnoses the suspicious period; it does not yet perform a definitive correction of the original observations.

## 10. Spatial Analysis

Spatial analysis was performed to investigate the geographical distribution of climate variables across Algeria.

### 10.1 Geographical Mapping

The average value of each climate variable was calculated for every geographical cell over the entire available period.

The resulting averages were visualized using scatter plots based on longitude and latitude.

The colour of each point represents the average value of the corresponding variable.

The following variables were mapped:

- `t2m`
- `d2m`
- `tp`
- `ssr`
- `str`
- `sp`

**Interpretation:**

The temperature map reveals a clear geographical structure.

The highest average temperatures are generally observed in the central and western Sahara, while lower temperatures are found in the northern part of the country and in high-altitude regions such as the Hoggar massif.

The surface pressure map also reflects geographical elevation. High-altitude areas exhibit lower surface pressure than low-altitude regions.

Precipitation and dewpoint temperature are more concentrated in the northern part of Algeria, particularly along the coastal areas and mountainous regions.

The precipitation map shows that most southern areas receive considerably less rainfall than northern regions.

The thermal radiation variable `str` also presents geographical differences, with less negative values generally observed in the more humid northern areas.

The spatial distribution of solar radiation is more complex and does not follow a simple north-to-south gradient.

These maps demonstrate that geographical location and elevation are important factors in explaining the distribution of climate variables.

### 10.2 Regional Statistical Comparison

The three latitude-based groups defined during preprocessing were compared using their mean values.

A regional aggregation was performed for `t2m`, `d2m`, `tp`, `ssr`, `str`, and `sp`.

Boxplots were also generated for temperature, precipitation, and solar radiation to compare their distributions across the three groups.

**Interpretation:**

The average temperature is approximately:

- South: 26.2°C.
- Centre: 24.2°C.
- North: 18.2°C.

This confirms the strong temperature contrast between the northern and southern parts of Algeria.

Precipitation is also much higher in the North, with an average of approximately 0.74 mm/day, compared with approximately 0.04 mm/day in the South.

The dewpoint temperature increases towards the North, indicating generally more humid conditions.

The boxplots show that temperature distributions overlap between the three groups. This suggests that seasonality remains important and that geographical grouping alone cannot fully explain temperature variation.

The regional analysis therefore provides useful information about geographical climate patterns that may be relevant to future clustering.

## 11. Correlation Analysis

Correlation analysis was conducted to quantify the relationships between the twelve meteorological variables.

Two correlation methods were used:

1. Pearson correlation.
2. Spearman rank correlation.

To ensure a fair comparison, both correlation matrices were calculated using the same random sample of 200,000 observations, selected with a fixed random state.

### 11.1 Pearson Correlation

Pearson correlation measures the strength and direction of a linear relationship between two numerical variables.

Its coefficient ranges from -1 to +1:

- A value close to +1 indicates a strong positive linear relationship.
- A value close to -1 indicates a strong negative linear relationship.
- A value close to 0 indicates a weak linear relationship.

### 11.2 Spearman Correlation

Spearman correlation is based on the ranks of observations rather than their original numerical values.

It measures the strength and direction of a monotonic relationship, including relationships that may not be linear.

This method is particularly useful when variables contain skewed distributions or when the relationship between them is not well represented by a straight line.

### 11.3 Correlation Matrices and Interpretation

Both matrices were visualized using Seaborn heatmaps.

The results reveal several important relationships.

**Relationship with temperature:**

The strongest correlations involving `t2m` are observed with:

- `ssr`: approximately +0.61.
- `str`: approximately -0.59 using Pearson and -0.66 using Spearman.
- `sshf`: approximately -0.57 using Pearson and -0.61 using Spearman.

These results indicate that radiation and energy-related variables are associated with temperature variations.

However, none of the individual correlations is sufficiently strong to explain temperature on its own.

**Multicollinearity between radiation variables:**

The variables `ssr`, `str`, and `sshf` exhibit strong correlations with one another.

For example, the Pearson correlation between `ssr` and `sshf` is approximately -0.93.

This indicates substantial redundancy between these variables.

Such redundancy may affect distance-based algorithms such as K-Nearest Neighbors and SVM, because several features may represent overlapping information.

**Relationship between precipitation and runoff:**

The relationship between `tp` and `ro` is particularly strong under Spearman correlation, with a coefficient close to 0.98 in the scatter-plot analysis.

However, the Pearson coefficient is considerably lower.

This difference suggests a strong monotonic but non-linear relationship, consistent with the threshold-like pattern observed in the scatter plot.

**Relationship between evaporation and other variables:**

The variables `slhf` and `e` exhibit an almost perfect correlation.

This suggests that they contain highly similar information in the analysed dataset.

Their negative correlations with precipitation must be interpreted in light of the variables' sign conventions.

**Conclusion:**

Comparing Pearson and Spearman correlations demonstrates that some relationships are approximately linear, while others are monotonic but non-linear.

The results also reveal substantial redundancy among several climate variables.

These observations will be useful when selecting, transforming, and interpreting features during the modelling stage.

## 12. Target Variable Construction and Temperature Classification

The project requires predicting the class of 2-meter temperature rather than its continuous numerical value.

For this reason, the continuous `t2m` variable was divided into three classes using equal-width intervals.

### 12.1 Equal-Width Discretization

The minimum and maximum values of `t2m` were identified.

The `numpy.linspace()` function was then used to divide the temperature range into three intervals of equal width.

The `pandas.cut()` function assigned each observation to one of the following classes:

- Cold.
- Moderate.
- Hot.

The resulting thresholds are approximately:

- Cold: below 13.86°C.
- Moderate: from 13.86°C to 27.44°C.
- Hot: above 27.44°C.

### 12.2 Class Distribution

The distribution of the three classes was calculated globally and visualized according to month and year.

The approximate global proportions are:

- Cold: 15.9%.
- Moderate: 43.5%.
- Hot: 40.6%.

**Interpretation:**

The classes are not equally represented in the dataset.

The Cold class is less frequent than the other two classes. This class imbalance should be considered during model evaluation.

For this reason, accuracy alone may not provide a complete assessment of classification performance. Metrics such as macro F1-score and recall for each class should also be considered.

The monthly class distribution reveals a strong seasonal pattern.

January contains a large proportion of Cold observations, whereas July is dominated by the Hot class.

The annual distribution is relatively stable across complete years.

However, the 2026 distribution must be interpreted cautiously because only nine months are available.

**Important methodological consideration:**

The class thresholds in this notebook were calculated using the complete dataset for exploratory purposes.

For the final predictive modelling stage, the thresholds should be calculated using the training set only. Otherwise, information from the test period would influence the target definition, resulting in data leakage.

## 13. Baseline Analysis: Persistence and Seasonal Climatology

Before implementing machine-learning algorithms, simple reference approaches were evaluated.

The objective was to understand how predictable the next month's temperature is using basic information.

### 13.1 Construction of the Next-Month Target

The dataset was first sorted by geographical cell and time.

The `groupby()` and `shift(-1)` operations were used to associate each observation with the temperature of the following month for the same geographical cell.

The resulting target variable is:

`t2m_next`

This operation establishes the temporal prediction structure at the cell level.

### 13.2 Persistence Baseline

The persistence approach assumes that the next month's temperature will be equal to the current month's temperature.

In other words:

Predicted temperature at month t+1 = Observed temperature at month t.

The correlation and R² score were calculated to evaluate this simple reference.

**Interpretation:**

The correlation between the current and following month's temperature is approximately 0.868.

However, the persistence baseline achieves an R² score of approximately 0.733.

This indicates that temperature is temporally correlated, but month-to-month changes remain important.

### 13.3 Seasonal Climatology Baseline

A second baseline was constructed using the average historical temperature for each geographical cell and calendar month.

This approach considers the typical seasonal temperature of a particular geographical location.

For example, it estimates the expected temperature for a given cell in January using its historical January observations.

**Interpretation:**

The climatology baseline achieves an R² score of approximately 0.98, indicating that geographical location and season explain a very large proportion of the variation in next-month temperature in this retrospective evaluation.

However, this is an optimistic reference because the climatology was calculated using all available years, including observations that could belong to a future test period.

It also uses the geographical cell identity, which is not among the specified input features in the project description.

Consequently, this result should be considered an exploratory reference rather than a directly comparable predictive model.

### 13.4 Anomaly Persistence

To investigate the temporal relationship beyond the seasonal cycle, temperature anomalies were calculated.

An anomaly is the difference between an observed temperature and its corresponding cell-month climatological average.

The correlation between consecutive anomalies is approximately 0.158.

This is much lower than the correlation between raw temperatures.

This result suggests that much of the apparent temporal predictability of temperature is associated with the recurring seasonal cycle rather than strong month-to-month persistence in the anomalies.

### 13.5 Classification Baselines

The three temperature classes were also evaluated using simple classification references:

- Majority-class prediction.
- Persistence-based prediction.
- Climatology-based prediction.

Their approximate accuracies are:

- Majority class: 43.7%.
- Persistence: 71.8%.
- Climatology: 93.1%.

These results provide useful reference points for evaluating future classification algorithms.

Nevertheless, the climatology baseline is optimistic for the reasons discussed above and should not be interpreted as a fair test-set benchmark.

## 14. Preliminary Spatial Trend Analysis for Clustering

As an initial preparation for the unsupervised learning stage, temporal slopes were calculated for `t2m` and `str` at each geographical cell.

Only complete years were included in this analysis, corresponding to 2015–2025.

### 14.1 Linear Slope Calculation

For each geographical cell, the annual average of `t2m` and `str` was calculated.

A linear slope was then estimated using the annual observations.

The slope represents the average annual change in the corresponding variable over the analysed period.

This procedure produces two additional features for every geographical cell:

- `pente_t2m`
- `pente_str`

The resulting slopes were mapped using geographical coordinates.

### 14.2 Interpretation of Temperature Slopes

The temperature slope is positive for all geographical cells in the dataset.

The average slope is approximately +0.107°C per year.

However, the spatial distribution is not uniform.

Higher estimated slopes are observed in some northern areas, while lower slopes appear in parts of the south-east, including the Hoggar and Tassili regions.

This indicates that the observed temperature evolution differs geographically.

These features may therefore provide useful information for identifying spatial climate patterns during the future clustering stage.

However, the estimates are based on only eleven complete annual observations and may partly reflect interannual variability rather than a persistent long-term trend.

### 14.3 Interpretation of Thermal Radiation Slopes

The estimated `str` slopes are positive across the geographical cells.

However, these results must not be interpreted as evidence of a genuine climate trend.

The calculation includes the suspicious period identified between September 2022 and February 2024.

The abnormal observations strongly influence the estimated slopes.

Therefore, the `str` slope feature must be recalculated after the underlying data issue has been investigated and appropriately addressed.

## 15. Overall Conclusions and Future Work

The exploratory analysis provides several important findings about the climate dataset over Algeria.

First, the dataset is structurally complete, with no missing values or duplicate cell-month records.

Second, the statistical analysis reveals substantial differences between meteorological variables. Temperature exhibits a strong seasonal structure, while precipitation, runoff, and evaporation-related variables display highly skewed distributions.

Third, the temporal analysis confirms the importance of seasonality and identifies a suspicious period affecting several radiation variables between September 2022 and February 2024.

Fourth, spatial analysis reveals a clear geographical climate structure, particularly the contrast between the humid North and the drier, warmer South.

Fifth, correlation analysis identifies strong relationships and redundancy among radiation variables, as well as non-linear or non-proportional relationships involving precipitation and runoff.

Finally, the preliminary target construction and baseline evaluation demonstrate that seasonal information is essential for predicting temperature classes.

The results provide a foundation for the next stages of the project.

The main tasks that remain include:

1. Investigating and addressing the suspicious period affecting `ssr`, `str`, and `sshf`.
2. Completing feature engineering for supervised learning, including the construction of the next-month target and a chronological train-test split.
3. Implementing and comparing KNN, SVM, Random Forest, Voting, and AdaBoost models.
4. Preparing annual feature sets for unsupervised learning.
5. Implementing K-Means, DBSCAN, AGNES, and AutoEncoder-KMeans separately for each year.
6. Evaluating the clustering results and analysing how geographical climate patterns evolve over time.

Overall, this EDA provides a statistical and geographical understanding of the dataset and identifies important preprocessing considerations before applying machine-learning algorithms.
