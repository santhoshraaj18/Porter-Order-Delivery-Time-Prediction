# Porter Order Delivery Time Prediction

## Project Overview

This project focuses on building a **regression model to accurately predict the delivery time for orders placed through Porter** [1]. The primary objectives include optimising operational efficiency, improving delivery time predictions, and understanding the key factors that influence delivery duration [1]. The insights gained from this model can help Porter make more informed decisions regarding logistics and customer expectations.

## Data

The dataset (`porter_data_1.csv`) contains **175,777 rows and 14 columns** [2], providing detailed information about orders. Key fields include:
*   `market_id`: Integer ID representing the market/region [3].
*   `created_at`: Timestamp when the order was placed [3].
*   `actual_delivery_time`: Timestamp when the order was delivered [3].
*   `store_primary_category`: Category of the restaurant (e.g., fast food) [4].
*   `order_protocol`: How the order was placed (e.g., via Porter app, call) [4].
*   `total_items`: Total items in the order [4].
*   `subtotal`: Final price of the order [4].
*   `total_onshift_dashers`: Delivery partners on duty [4].
*   `total_busy_dashers`: Delivery partners occupied with other orders [5].
*   `total_outstanding_orders`: Orders pending fulfillment [5].
*   `distance`: Total distance from the restaurant to the customer [5].

## Data Pipeline & Methodology

The project followed a structured data pipeline [3]:

1.  **Data Loading**: The `porter_data_1.csv` file was loaded into a Pandas DataFrame [2].

2.  **Data Preprocessing and Feature Engineering**:
    *   **Timestamp Conversion**: `created_at` and `actual_delivery_time` were converted from `object` to `datetime` data types [6, 7].
    *   **Categorical Conversion**: `market_id`, `store_primary_category`, and `order_protocol` were converted to `category` data types [8, 9].
    *   **Target Variable Creation**: A new `time_taken` column was engineered, representing the delivery duration in minutes, calculated as the difference between `actual_delivery_time` and `created_at` [10, 11]. The mean delivery time was approximately **46.20 minutes** [11].
    *   **Temporal Features**: `order_hour` and `isWeekend` (a boolean flag for Saturday/Sunday) were extracted from `created_at` to capture temporal patterns [12, 13].
    *   **Column Dropping**: Original timestamp columns (`created_at`, `actual_delivery_time`) were dropped, along with `store_primary_category` and `order_day` (after `isWeekend` was created) [13, 14]. `min_item_price` was later dropped due to weak correlation with the target variable (`time_taken`) [15].

3.  **Exploratory Data Analysis (EDA)**:
    *   **Feature Distributions**: Histograms and skewness analysis were performed on numerical features in the training set, revealing **skewness** in many features (e.g., `total_items`, `subtotal`, `total_outstanding_orders`) [16-18]. Categorical features showed varying unique values and some **class imbalance** (e.g., `market_id` 6.0000 with 0.36%, `order_protocol` 7.0000 with 0.01%) [19, 20].
    *   **Target Variable Distribution**: The `time_taken` target variable was analysed, showing a **positive skewness (0.758)** and kurtosis (0.593) [21, 22].
    *   **Relationships & Correlations**:
        *   **`distance` had the highest positive correlation with `time_taken` (0.461)** [23, 24]. This indicates that longer distances are strongly associated with longer delivery times [24].
        *   `subtotal` (0.413) and `total_outstanding_orders` (0.385) also showed strong positive correlations [23].
        *   A heatmap was used to visualise inter-feature correlations, revealing highly correlated pairs like `total_items` and `num_distinct_items`, and `total_onshift_dashers` and `total_busy_dashers` [25, 26].
    *   **Outlier Handling**: Outliers in numerical features and `time_taken` were identified using boxplots and the IQR method [27, 28]. These outliers were **handled by applying Winsorization at the 1st and 99th percentiles**, capping extreme values to reduce their influence while preserving data structure [29-31].
    *   **Validation Data EDA**: A crucial step involved performing EDA on the validation set (`X_test`, `y_test`) to ensure that feature distributions, skewness, and particularly **correlations with `time_taken` were consistent with the training set** [32-35]. The correlation values were indeed **very similar** (e.g., `distance` correlation was 0.4595 in training and 0.4554 in validation) [36], confirming the robustness of these relationships.

4.  **Model Building**:
    *   **Data Split**: The data was split into training (80%) and test (20%) sets using `train_test_split`, resulting in 140,621 training samples and 35,156 test samples [37-39].
    *   **Feature Scaling**: Numerical features were scaled using `StandardScaler` to bring them to a comparable range, aiding in coefficient comparison [40-42].
    *   **Linear Regression**: A **Linear Regression model** was built using both `statsmodels.OLS` and `scikit-learn.LinearRegression`, yielding consistent results [42, 43].
    *   **Feature Selection (RFE)**: **Recursive Feature Elimination (RFE)** was employed to select the most impactful features for the model [44, 45]. It determined that the **optimal number of features was 11** [46]. The selected features were: `order_protocol`, `total_items`, `subtotal`, `num_distinct_items`, `max_item_price`, `total_onshift_dashers`, `total_busy_dashers`, `total_outstanding_orders`, `distance`, `order_hour`, and `isWeekend` [47].

5.  **Model Inference & Results**:
    *   **Performance Metrics**: The final model achieved strong performance on the test set [48]:
        *   **R²: 0.8693** (The model explains approximately 86.93% of the variance in delivery times).
        *   **Root Mean Squared Error (RMSE): 3.38 minutes** (Average magnitude of errors in minutes).
        *   **Mean Absolute Error (MAE): 2.41 minutes** (Average absolute difference between predictions and actuals).
    *   **Residual Analysis**: Residual plots showed generally random scatter around zero, which is good [49]. However, the **Q-Q plot indicated slight right skewness** in the residuals, suggesting the model might occasionally underestimate longer delivery times [50, 51].
    *   **Coefficient Analysis**: The analysis of unscaled coefficients revealed the real-world impact of features on delivery time [52, 53]:
        *   **`distance`**: Has the **largest absolute impact**, with an increase of 1 unit (e.g., 1 mile) in distance leading to an increase of approximately **12.83 minutes** in delivery time [54].
        *   **`total_outstanding_orders`**: An increase in pending orders significantly increases delivery time (coefficient of **18.59 minutes**) [54].
        *   **`subtotal`**: Higher-value orders are associated with a slight increase in delivery time (coefficient of **2.34 minutes**) [54].
        *   `total_items`: Adding 1 item to an order increases delivery time by about **0.05 minutes** [55].

## Key Findings & Insights

*   **Distance is the paramount factor**: The geographical distance an order travels is the most significant determinant of delivery time, naturally requiring more travel time [23, 24, 54].
*   **Network Congestion Impacts Delivery**: `total_outstanding_orders` also has a substantial positive impact, indicating that a higher volume of pending orders or busy delivery partners leads to increased delivery times, reflecting network congestion [23, 54].
*   **Robust Model Performance**: The Linear Regression model achieved a high R² of nearly 87%, demonstrating its strong ability to explain and predict variations in delivery times with low error rates (RMSE ~3.38 mins) [48].
*   **Data Quality and Preparation are Crucial**: Thorough data preprocessing, including type conversions, intelligent feature engineering (`time_taken`, `isWeekend`), and robust outlier handling (Winsorization), was fundamental to the model's success [6, 10, 12, 29].
*   **Consistency Across Data Subsets**: The consistency of feature distributions and correlations between training and validation sets assures that the model's learnings are generalisable and reliable for unseen orders [33, 36].

## Technical Stack

*   **Python 3.x**
*   **pandas** [5]
*   **numpy** [5]
*   **matplotlib** [5]
*   **seaborn** [5]
*   **scikit-learn** (for `train_test_split`, `StandardScaler`, `LinearRegression`, `RFE`, `mean_squared_error`, `r2_score`, `mean_absolute_error`, `median_absolute_error`) [37, 40, 45, 56]
*   **statsmodels** (for Ordinary Least Squares (OLS) model and detailed statistics) [40, 42]
*   **scipy** (for `scipy.stats.mstats.winsorize` and `scipy.stats.probplot`) [21, 29]

## How to Run This Project

To explore and replicate the analysis and model, follow these steps:

1.  **Clone the Repository**:
    ```bash
    git clone https://github.com/your-username/porter-delivery-time-prediction.git
    cd porter-delivery-time-prediction
    ```
2.  **Unzip the Data and Report**:
    *   Locate the `porter_project_files.zip` file in the cloned repository.
    *   Unzip its contents. This should extract:
        *   `porter_data_1.csv` (the dataset)
        *   `Delivery_Time_Estimation_Notebook.ipynb` (the Jupyter Notebook)
        *   `Delivery_Time_Estimation_Report.pdf` (the project report)
3.  **Set Up Environment**:
    *   It is recommended to create a virtual environment:
        ```bash
        python -m venv venv
        source venv/bin/activate  # On Windows: venv\Scripts\activate
        ```
    *   Install the required libraries (you can typically create a `requirements.txt` from the notebook's imports, but for this project, the core libraries are listed above):
        ```bash
        pip install pandas numpy matplotlib seaborn scikit-learn statsmodels scipy
        ```
4.  **Run the Notebook**:
    *   Start Jupyter Lab or Jupyter Notebook:
        ```bash
        jupyter notebook
        ```
    *   Open `Delivery_Time_Estimation_Notebook.ipynb` and run all cells to see the complete data pipeline, analysis, and model results.

## Repository Contents

*   `porter_project_files.zip`: A compressed archive containing:
    *   `porter_data_1.csv`: The raw dataset used for the project.
    *   `Delivery_Time_Estimation_Notebook.ipynb`: The Jupyter Notebook containing all the code for data loading, preprocessing, EDA, model building, and evaluation.
    *   `Delivery_Time_Estimation_Report.pdf`: A comprehensive report summarising the project's objectives, methodology, findings, and conclusions.
*   `README.md`: This file.
