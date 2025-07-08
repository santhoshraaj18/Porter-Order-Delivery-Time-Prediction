# Porter Order Delivery Time Prediction

## Project Overview

This project aims to develop a **regression model to accurately predict the delivery time for orders placed through Porter** [1]. The primary objectives include **optimising operational efficiency, improving delivery time predictions**, and understanding the **key factors that influence delivery duration** [1]. The insights gained from this model are crucial for Porter to make more informed decisions regarding logistics and customer expectations [1].

## Data

The dataset (`porter_data_1.csv`) contains **175,777 rows and 14 columns** [5], providing detailed information about orders placed through Porter [2]. Key fields include:

*   `market_id`: Integer ID representing the market/region where the restaurant is located [2].
*   `created_at`: Timestamp when the order was placed [2].
*   `actual_delivery_time`: Timestamp when the order was delivered [2].
*   `store_primary_category`: Category of the restaurant (e.g., fast food, dine-in) [3].
*   `order_protocol`: Integer representing how the order was placed (e.g., via Porter, call to restaurant, etc.) [3].
*   `total_items`: Total number of items in the order [3].
*   `subtotal`: Final price of the order [3].
*   `num_distinct_items`: Number of distinct items in the order [3].
*   `min_item_price`: Price of the cheapest item in the order [3, 7].
*   `max_item_price`: Price of the most expensive item in the order [3, 7].
*   `total_onshift_dashers`: Number of delivery partners on duty when the order was placed [3].
*   `total_busy_dashers`: Number of delivery partners already occupied with other orders [4].
*   `total_outstanding_orders`: Number of orders pending fulfillment at the time of the order [4].
*   `distance`: Total distance from the restaurant to the customer [4].

## Data Pipeline & Methodology

The project followed a structured data pipeline, involving five key steps: Data Loading, Data Preprocessing and Feature Engineering, Exploratory Data Analysis, Model Building, and Model Inference [2].

### 1. Data Loading

The `porter_data_1.csv` file was successfully loaded into a Pandas DataFrame, confirming its shape as (175,777, 14) [5].

### 2. Data Preprocessing and Feature Engineering

This phase focused on transforming raw data into a suitable format for modelling and creating new, more informative features [11].

*   **Timestamp Conversion**: `created_at` and `actual_delivery_time` columns were converted from `object` to `datetime` data types for accurate calculations [11, 12].
*   **Categorical Conversion**: `market_id`, `store_primary_category`, and `order_protocol` were converted to `category` data types to ensure proper handling of categorical variables [13, 14].
*   **Target Variable Creation**: A new `time_taken` column was engineered, representing the delivery duration in minutes, calculated as the difference between `actual_delivery_time` and `created_at`. The **mean delivery time was approximately 46.20 minutes**, with a minimum of 32 minutes and a maximum of 110 minutes [15, 16].
*   **Temporal Features**: `order_hour` (the hour at which the order was placed) and `isWeekend` (a boolean flag indicating if the order was on a Saturday or Sunday) were extracted from `created_at` to capture temporal patterns [17]. Approximately **34.6% of orders occurred on weekends** [18].
*   **Column Dropping**: Original timestamp columns (`created_at`, `actual_delivery_time`) were dropped after feature engineering [18]. `store_primary_category` and `order_day` were also dropped as they were deemed unnecessary for modelling [19]. `min_item_price` was later dropped due to its **weak correlation (less than 0.1)** with the target variable (`time_taken`) [49]. Subsequently, `market_id` was also dropped as it showed a weak correlation with the target [70]. The final number of features retained for modelling was 11 [71].

### 3. Exploratory Data Analysis (EDA)

EDA was performed on the training data to understand distributions, relationships, and identify issues like outliers [28]. A crucial step involved performing **EDA on the validation set (`X_test`, `y_test`) to ensure consistency** in feature distributions, skewness, and correlations with `time_taken` compared to the training set [54, 55].

*   **Feature Distributions**: Histograms and skewness analysis were performed on numerical features [31]. Many numerical features (e.g., `total_items`, `subtotal`, `total_outstanding_orders`, `total_onshift_dashers`, `total_busy_dashers`) showed **skewness**, some being "Highly Skewed" or "Moderately Skewed" [31-33]. Categorical features displayed varying unique values and some **class imbalance**, particularly for `market_id` 6.0000 (0.36% representation) and `order_protocol` 7.0000 (0.01% representation) [35, 36].
*   **Target Variable Distribution**: The `time_taken` target variable was analysed, showing a **positive skewness (0.758)** and kurtosis (0.593), indicating it is not normally distributed [38, 40].
*   **Relationships & Correlations**:
    *   **`distance` had the highest positive correlation with `time_taken` (0.461)**, indicating that longer distances are strongly associated with longer delivery times [42, 100].
    *   `subtotal` (0.413) and `total_outstanding_orders` (0.385) also showed strong positive correlations with `time_taken` [43].
    *   A heatmap visualised inter-feature correlations, revealing highly correlated pairs such as `total_items` and `num_distinct_items`, and `total_onshift_dashers` and `total_busy_dashers` [46, 47].
    *   Analysis confirmed that **correlations with `time_taken` were very similar between the training and validation sets** (e.g., `distance` correlation was 0.4595 in training and 0.4554 in validation), confirming the robustness of these relationships [66, 67].
*   **Outlier Handling**: Outliers in numerical features and `time_taken` were identified using boxplots and the IQR method. For instance, `distance` had 264 outliers (0.2%), `total_items` had 6803 outliers (4.8%), `subtotal` had 6474 outliers (4.6%), and `time_taken` had 1379 outliers (1.0%) [51, 52, 101]. These outliers were **handled by applying Winsorization at the 1st and 99th percentiles** for columns like `distance`, `total_items`, `subtotal`, and `total_outstanding_orders`. This capping of extreme values reduces their influence while preserving data structure [52-54, 101]. For example, `distance` values were Winsorized to be between 4.44 and 41.84 [54, 101].

### 4. Model Building

*   **Data Split**: The data was split into training (80%) and test (20%) sets using `train_test_split`, resulting in **140,621 training samples and 35,156 test samples** [25, 99].
*   **Feature Scaling**: Numerical features were scaled using `StandardScaler` to bring them to a comparable range. While linear regression is agnostic to feature scaling, it aids in coefficient comparison [71, 72, 75].
*   **Linear Regression**: A **Linear Regression model** was built using both `statsmodels.OLS` and `scikit-learn.LinearRegression`, yielding consistent results [75, 76]. Linear regression predicts a continuous outcome (like delivery time) based on input features, assuming a linear relationship. It works by fitting a line that minimises the Mean Squared Error (MSE) [103].
*   **Feature Selection (RFE)**: **Recursive Feature Elimination (RFE)** was employed to select the most impactful features for the model [82, 83]. RFE recursively reduces features to find an optimal balance between performance and feature count [83]. It determined that the **optimal number of features was 11** [85]. The selected features were: `order_protocol`, `total_items`, `subtotal`, `num_distinct_items`, `max_item_price`, `total_onshift_dashers`, `total_busy_dashers`, `total_outstanding_orders`, `distance`, `order_hour`, and `isWeekend` [89].

### 5. Model Inference & Results

*   **Performance Metrics**: The final model achieved strong performance on the test set:
    *   **R²: 0.8693** (The model explains approximately 86.93% of the variance in delivery times) [89, 104].
    *   **Root Mean Squared Error (RMSE): 3.38 minutes** (Average magnitude of errors in minutes) [89].
    *   **Mean Absolute Error (MAE): 2.41 minutes** (Average absolute difference between predictions and actuals) [89].
*   **Residual Analysis**: Residual plots showed generally random scatter around zero, which is good. However, the **Q-Q plot indicated slight right skewness** in the residuals, suggesting the model might occasionally underestimate longer delivery times [90, 91, 107].
*   **Coefficient Analysis**: The analysis of unscaled coefficients revealed the real-world impact of features on delivery time [92, 93]:
    *   **`distance`**: Has the **largest absolute impact**, with an increase of 1 unit (e.g., mile, if units are miles) in distance leading to an increase of approximately **12.83 minutes** in delivery time [102].
    *   **`total_outstanding_orders`**: An increase in pending orders significantly increases delivery time (coefficient of **18.59 minutes**) [102].
    *   **`subtotal`**: Higher-value orders are associated with a slight increase in delivery time (coefficient of **2.34 minutes**) [102].
    *   `total_items`: Adding 1 item to an order increases delivery time by about **0.05 minutes** [96].

## Key Findings & Insights

*   **Distance is the paramount factor**: The geographical distance an order travels is the most significant determinant of delivery time, naturally requiring more travel time [42, 100, 102].
*   **Network Congestion Impacts Delivery**: `total_outstanding_orders` also has a substantial positive impact, indicating that a higher volume of pending orders or busy delivery partners leads to increased delivery times, reflecting network congestion [102].
*   **Robust Model Performance**: The Linear Regression model achieved a high R² of nearly 87%, demonstrating its strong ability to explain and predict variations in delivery times with low error rates (RMSE ~3.38 mins) [89].
*   **Data Quality and Preparation are Crucial**: Thorough data preprocessing, including type conversions, intelligent feature engineering (`time_taken`, `isWeekend`), and robust outlier handling (Winsorization), was fundamental to the model's success [11, 13, 15, 17, 52, 101].
*   **Consistency Across Data Subsets**: The consistency of feature distributions and correlations between training and validation sets assures that the model's learnings are generalisable and reliable for unseen orders [56, 57, 59-67].

## Technical Stack

*   **Python 3.x**
*   **pandas** [4]
*   **numpy** [4]
*   **matplotlib** [4]
*   **seaborn** [4]
*   **scikit-learn** (for `train_test_split`, `StandardScaler`, `LinearRegression`, `RFE`, `mean_squared_error`, `r2_score`, `mean_absolute_error`, `median_absolute_error`, `make_pipeline`, `cross_val_score`) [24, 71, 79, 83, 84, 87]
*   **statsmodels** (for Ordinary Least Squares (OLS) model and detailed statistics) [71, 75, 76]
*   **scipy** (for `scipy.stats.mstats.winsorize` and `scipy.stats.probplot`, `scipy.stats.shapiro`) [38, 52, 91]

## How to Run This Project

To explore and replicate the analysis and model, follow these steps:

1.  **Clone the Repository**:
    ```bash
    git clone https://github.com/your-username/porter-delivery-time-prediction.git
    cd porter-delivery-time-prediction
    ```
2.  **Unzip the Data and Report**:
    *   Locate the `porter_project_files.zip` file in the cloned repository (this zip file contains all necessary components).
    *   Unzip its contents. This should extract:
        *   `porter_data_1.csv` (the dataset) [5]
        *   `LR_Delivery_Time_Estimation_Starter_Santhosh_Raaj_G.ipynb` (the Jupyter Notebook)
        *   `LR_Delivery_Time_Estimation_Starter_Santhosh_Raaj_G.pdf` (the project report)
3.  **Set Up Environment**:
    *   It is recommended to create a virtual environment:
        ```bash
        python -m venv venv
        source venv/bin/activate  # On Windows: venv\Scripts\activate
        ```
    *   Install the required libraries:
        ```bash
        pip install pandas numpy matplotlib seaborn scikit-learn statsmodels scipy
        ```
4.  **Run the Notebook**:
    *   Start Jupyter Lab or Jupyter Notebook:
        ```bash
        jupyter notebook
        ```
    *   Open `LR_Delivery_Time_Estimation_Starter_Santhosh_Raaj_G.ipynb` and run all cells to see the complete data pipeline, analysis, and model results.

## Repository Contents

*   `porter_project_files.zip`: A compressed archive containing:
    *   `porter_data_1.csv`: The raw dataset used for the project [5].
    *   `LR_Delivery_Time_Estimation_Starter_Santhosh_Raaj_G.ipynb`: The Jupyter Notebook containing all the code for data loading, preprocessing, EDA, model building, and evaluation.
    *   `LR_Delivery_Time_Estimation_Starter_Santhosh_Raaj_G.pdf`: A comprehensive report summarising the project's objectives, methodology, findings, and conclusions [1].
*   `README.md`: This file.
