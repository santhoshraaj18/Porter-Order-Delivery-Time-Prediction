

# ⏱️ Porter Order Delivery Time Prediction: Delivering Insights, On Time\!

This is a solo project developed as part of my Post Graduate Diploma by IIIT Bangalore through Upgrad.

-----

## 🚀 Project Overview

Ever wondered how food delivery apps like Porter estimate your delivery time so accurately? This project dives deep into that very question\! My goal was to build a robust **regression model to predict the delivery time for orders placed through Porter**, ensuring efficiency and improving the customer experience.

By optimizing delivery time predictions, Porter can enhance its operational efficiency, manage logistics more effectively, and set realistic customer expectations. This project uncovers the **key factors that truly influence delivery duration**, providing actionable insights for smarter decisions.

-----

## 📊 The Data Behind the Deliveries

The heart of this project is the `porter_data_1.csv` dataset, a rich collection of **175,777 order records across 14 insightful columns**. Each row tells a story about a unique order, from its origin to its destination.

Here’s a snapshot of the crucial information we're working with:

  * `market_id`: The unique ID of the market or region.
  * `created_at`: When the order was placed (the starting gun\!).
  * `actual_delivery_time`: When the order actually arrived (the finish line\!).
  * `store_primary_category`: What kind of restaurant it was (e.g., fast food, Indian).
  * `order_protocol`: How the order was placed (e.g., through the app, by phone).
  * `total_items`: How many delicious items were in the bag.
  * `subtotal`: The total bill amount.
  * `num_distinct_items`: How many *different* items were ordered.
  * `min_item_price` & `max_item_price`: Price range of items.
  * `total_onshift_dashers`: Number of available delivery heroes.
  * `total_busy_dashers`: How many heroes were already on a mission.
  * `total_outstanding_orders`: The current backlog of orders.
  * `distance`: The crucial journey from the restaurant to the customer.

-----

## ⚙️ Our Data Journey: From Raw Data to Predictions

This project followed a well-defined data pipeline, ensuring every step contributed to a powerful and accurate model:

1.  **Data Loading**: Getting our hands on the `porter_data_1.csv` was the first step, confirming its massive (175,777, 14) shape.
2.  **Data Preprocessing & Feature Engineering**: Transforming raw timestamps into meaningful durations and extracting new features like `order_hour` and `isWeekend` was vital. We engineered our target variable, `time_taken`, which showed an **average delivery time of \~46.20 minutes**. We also meticulously handled irrelevant columns and identified the most impactful features.
3.  **Exploratory Data Analysis (EDA)**: This phase was like detective work\! We uncovered feature distributions, relationships, and identified outliers. **`distance` emerged as the star, showing the highest positive correlation with delivery time (0.461)**. We also found that many features were skewed and handled outliers through **Winsorization**, ensuring our model wasn't swayed by extreme values.
4.  **Model Building**: The data was split (80% training, 20% testing), and numerical features were scaled. We built a **Linear Regression model**, a straightforward yet powerful algorithm for continuous predictions. To refine our model, we used **Recursive Feature Elimination (RFE)**, which confirmed that **11 key features** were optimal for prediction.
5.  **Model Inference & Results**: The moment of truth\! How well did our model perform?

-----

## 🎯 Model Performance: Hitting the Mark\!

Our Linear Regression model achieved impressive results on unseen data:

  * **R²: 0.8693** - This means our model explains nearly **87% of the variance in delivery times**\! A fantastic fit.
  * **Root Mean Squared Error (RMSE): 3.38 minutes** - On average, our predictions were off by only about **3.38 minutes**. That's incredibly precise\!
  * **Mean Absolute Error (MAE): 2.41 minutes** - The average absolute difference between predicted and actual delivery times was a mere **2.41 minutes**.

### Key Insights from Model Coefficients:

Unpacking the model's coefficients revealed the real-world impact of each factor:

  * **`distance`**: The undisputed champion\! A 1-unit increase in distance (e.g., a mile) leads to an estimated **12.83-minute increase** in delivery time. This makes perfect sense – longer distances mean longer drives.
  * **`total_outstanding_orders`**: Network congestion matters\! More pending orders significantly increase delivery time (a coefficient of **18.59 minutes**).
  * **`subtotal`**: Surprisingly, higher-value orders also saw a slight increase in delivery time (a coefficient of **2.34 minutes**).
  * **`total_items`**: Each additional item in an order added about **0.05 minutes** to delivery time.

-----

## 🌟 Key Findings & Takeaways

  * **Distance is King (or Queen)\!**: The most significant factor influencing delivery time is the geographical distance an order travels.
  * **Congestion Slows Things Down**: The number of outstanding orders and busy dashers directly impacts delivery duration, highlighting the importance of efficient resource allocation.
  * **A Robust & Reliable Model**: Our Linear Regression model is highly effective, explaining almost 87% of delivery time variance with minimal error.
  * **Data Quality is Paramount**: The success of this model underscores the critical role of thorough data preprocessing, intelligent feature engineering, and robust outlier handling.
  * **Consistency Breeds Confidence**: Consistent feature distributions and correlations across training and validation sets confirm our model's generalizability and reliability for predicting unseen orders.

-----

## 🛠️ Technical Arsenal

This project was built using a powerful stack of Python libraries:

  * **Python 3.x**
  * **pandas**: For data manipulation and analysis.
  * **numpy**: For numerical operations.
  * **matplotlib** & **seaborn**: For stunning data visualizations.
  * **scikit-learn**: The backbone for machine learning tasks (data splitting, scaling, Linear Regression, RFE, and performance metrics).
  * **statsmodels**: For in-depth statistical analysis and OLS modeling.
  * **scipy**: For statistical functions, including Winsorization and probability plots.

-----

## 🏃‍♀️ How to Run This Project

Ready to explore the code and replicate the analysis? Follow these simple steps:

1.  **Clone the Repository**:
    ```bash
    git clone https://github.com/your-username/porter-delivery-time-prediction.git
    cd porter-delivery-time-prediction
    ```
2.  **Unzip the Project Files**:
      * Locate `porter_project_files.zip` within the cloned repository.
      * Unzip its contents. This will extract:
          * `porter_data_1.csv` (the dataset)
          * `LR_Delivery_Time_Estimation_Starter_Santhosh_Raaj_G.ipynb` (the Jupyter Notebook)
          * `LR_Delivery_Time_Estimation_Starter_Santhosh_Raaj_G.pdf` (the comprehensive project report)
3.  **Set Up Your Environment**:
      * It's highly recommended to create a virtual environment:
        ```bash
        python -m venv venv
        source venv/bin/activate # On Windows: venv\Scripts\activate
        ```
      * Install all necessary libraries:
        ```bash
        pip install pandas numpy matplotlib seaborn scikit-learn statsmodels scipy
        ```
4.  **Launch the Notebook**:
      * Start Jupyter Lab or Jupyter Notebook:
        ```bash
        jupyter notebook
        ```
      * Open `LR_Delivery_Time_Estimation_Starter_Santhosh_Raaj_G.ipynb` and run all cells to see the complete data pipeline, analysis, and model results come to life\!

-----

## 📦 Repository Contents

  * `porter_project_files.zip`: Your all-in-one package containing:
      * `porter_data_1.csv`: The raw dataset.
      * `LR_Delivery_Time_Estimation_Starter_Santhosh_Raaj_G.ipynb`: The complete Jupyter Notebook with all code.
      * `LR_Delivery_Time_Estimation_Starter_Santhosh_Raaj_G.pdf`: The detailed project report.
  * `README.md`: You're reading it\!

-----
