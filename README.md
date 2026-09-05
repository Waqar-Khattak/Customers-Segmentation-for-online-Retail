# Customer Segmentation Analysis

This project uses transactional retail data to identify meaningful customer groups and support targeted marketing decisions. It combines data cleaning, exploratory analysis, RFM analysis, engineered customer behavior features, dimensionality reduction, and three unsupervised clustering techniques.

## Project Files

| File | Description |
| --- | --- |
| `Customer_Segmentation Notebook.ipynb` | Complete interactive analysis with charts, tables, model comparisons, and recommendations |
| `customer_segmentation.py` | Python version of the analysis pipeline |
| `Online Retail.xlsx`

## Objectives

- Understand sales and customer purchasing behavior.
- Build customer-level Recency, Frequency, and Monetary features.
- Add behavioral features such as average order value, quantity, product diversity, and weekend preference.
- Compare K-means, hierarchical, and DBSCAN clustering.
- Select the strongest clustering approach using validation metrics.
- Translate customer clusters into actionable marketing segments.

## Dataset

The analysis uses the **Online Retail** dataset, which contains retail transactions from 1 December 2010 through 9 December 2011.

Raw dataset summary:

- Transactions: **541,909**
- Invoices: **25,900**
- Customers: **4,372**
- Products: **4,070**
- Columns: `InvoiceNo`, `StockCode`, `Description`, `Quantity`, `InvoiceDate`, `UnitPrice`, `CustomerID`, and `Country`

The dataset is not stored in this project. Download `Online Retail.xlsx` from the UCI Machine Learning Repository or another authorized source before running the analysis.

## Methodology

### 1. Data cleaning

The preprocessing pipeline:

- Converts `InvoiceDate` to a datetime value.
- Restricts the analysis to the stated dataset period.
- Removes transactions without a `CustomerID`.
- Converts customer IDs to integers.
- Removes cancellations and returns by keeping positive quantities.
- Removes invalid transactions with non-positive unit prices.
- Calculates transaction revenue with:

  `TotalPrice = Quantity * UnitPrice`

- Derives day, month, year, weekday, quarter, and weekend indicators.

### 2. Customer features

The final customer-level feature table contains **23 features for 4,337 customers**.

Core RFM features:

- **Recency:** days since the customer's latest purchase.
- **Frequency:** number of distinct invoices.
- **Monetary:** total customer spending.

Additional behavioral features include:

- Total and average quantity purchased.
- Average, minimum, and maximum unit price.
- Average and variation in transaction value.
- Average quantity per transaction.
- Number of unique products.
- Product diversity.
- Weekday and weekend purchasing behavior.

### 3. Clustering preparation

Seven features were selected for segmentation:

- `Recency`
- `Frequency`
- `Monetary`
- `TotalPrice_mean`
- `Quantity_mean`
- `ProductDiversity`
- `Weekend_mean`

Highly skewed variables were log-transformed with `log1p`, then all selected features were standardized with `StandardScaler`.

### 4. PCA

PCA was used to visualize customer groups and understand the structure of the standardized feature space. The first three principal components explain **72.06%** of the total variance:

| Component | Explained variance |
| --- | ---: |
| PC1 | 35.72% |
| PC2 | 21.96% |
| PC3 | 14.38% |
| **Total** | **72.06%** |

### 5. Clustering algorithms

The project compares:

- **K-means:** the number of clusters is selected with the elbow method, with silhouette scores used as a fallback.
- **Hierarchical clustering:** Ward linkage and silhouette scores are used to select the number of clusters.
- **DBSCAN:** several `eps` values are evaluated using silhouette score and the proportion of noise points.

## Results

### Model comparison

K-means produced the strongest overall result according to the silhouette score and was selected for the final segmentation.

| Algorithm | Clusters | Silhouette score | Calinski-Harabasz score | Noise points |
| --- | ---: | ---: | ---: | ---: |
| **K-means** | **5** | **0.3056** | **1,458.36** | 0 |
| Hierarchical | 4 | 0.2739 | 1,259.01 | 0 |
| DBSCAN | 9 | -0.0187 | 19.56 | 456 |

The K-means result has the best separation among the tested approaches. DBSCAN identified many noise points and had a negative silhouette score, so it was not selected for the final customer segmentation.

### Final K-means segments

The final K-means segmentation contains five groups:

| Segment | Customers | Share of customers | Share of revenue | Average recency | Average frequency | Average spending |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| Champions (High Order Value) | 1,376 | 31.73% | 79.72% | 24.1 days | 9.3 orders | £5,055.90 |
| Lost Customers | 2,049 | 47.24% | 13.60% | 132.5 days | 1.7 orders | £579.16 |
| Loyal Customers (Diverse Buyers) | 453 | 10.45% | 3.62% | 97.3 days | 2.5 orders | £697.07 |
| Lost Customers (Diverse Buyers) | 458 | 10.56% | 2.18% | 111.5 days | 2.1 orders | £415.88 |
| Former Big Spenders (High Order Value) (Focused Buyers) | 1 | 0.02% | 0.88% | 325.0 days | 1.0 order | £77,183.60 |

### Key findings

1. **Champions are the most commercially important group.** They represent 31.73% of customers but contribute 79.72% of recorded revenue.
2. **Lost Customers are the largest group.** Nearly half of customers have high recency values and low purchase frequency, making retention and reactivation important priorities.
3. **The single Former Big Spender is an extreme high-value outlier.** This customer should be reviewed individually rather than treated as a representative segment.
4. **K-means provides the most useful structure for this dataset.** Its positive silhouette score is higher than the hierarchical result, while DBSCAN produces weak separation and substantial noise.

## Business Recommendations

### Champions

- Create a VIP program with exclusive benefits and early product access.
- Use personalized offers and loyalty rewards.
- Request feedback to support product and service development.
- Promote premium products and high-value bundles.

### Loyal Customers

- Use tiered rewards to encourage repeat purchasing.
- Offer cross-sell and bundle opportunities.
- Recommend products based on purchase history.
- Highlight new arrivals and the breadth of the catalog.

### Lost Customers

- Run reactivation campaigns with relevant discounts.
- Send personalized “we miss you” messages.
- Offer a welcome-back promotion.
- Analyze feedback and unsubscribe inactive customers who do not respond.

### Lost Customers with Diverse Buying Behavior

- Combine win-back messaging with product discovery campaigns.
- Recommend complementary products and curated collections.
- Highlight new categories and product variety.

### Former Big Spenders

- Assign individual account attention.
- Use a high-value win-back offer and premium service.
- Investigate why purchasing stopped.
- Recommend complementary products based on the customer's historical purchase.

### Overall actions

- Connect the segments to a CRM system.
- Automate segment-specific email journeys.
- Recalculate segments regularly as customer behavior changes.
- Track conversion, repeat purchase rate, revenue, and campaign response by segment.

## Installation

Python 3.9 or later is recommended. Install the required packages with:

```bash
pip install pandas numpy matplotlib seaborn scikit-learn scipy yellowbrick openpyxl jupyter
```

## Running the Notebook

1. Download `Online Retail.xlsx`.
2. Place it in the project directory, or update the input path in the notebook.
3. Open `Customer_Segmentation Notebook.ipynb` in Jupyter or VS Code.
4. Run the cells from top to bottom.

The original notebook uses the Google Colab path `/content/Online Retail.xlsx`. For a local run, change the loading line to:

```python
df = pd.read_excel('Online Retail.xlsx')
```

## Running the Python Script

The script currently expects the Colab input path. Update this line in `customer_segmentation.py` before running locally:

```python
df = pd.read_excel('Online Retail.xlsx')
```

Then run:

```bash
python customer_segmentation.py
```

The script displays exploratory charts, PCA plots, cluster diagnostics, cluster profiles, the final segment distribution, and business recommendations. It does not currently export the final segmentation to a CSV file.

## Interpretation Notes and Limitations

- The results are based on historical purchasing behavior and should be refreshed with newer transactions before being used operationally.
- Segment names are assigned using median comparisons of recency, frequency, monetary value, order value, and product diversity; they are descriptive labels rather than supervised predictions.
- Customer segmentation is sensitive to feature selection, transformations, scaling, random seeds, and clustering parameters.
- Revenue concentration is highly uneven, so marketing decisions should consider both segment size and financial contribution.
- The one-customer Former Big Spender group is an outlier and should not be generalized to a broad customer population.
- The notebook contains saved outputs from the analysis, but the notebook summary reports that cells are not currently marked as executed in the active kernel. Re-run the notebook to reproduce the results in a new environment.

## Technology Stack

- Python
- pandas and NumPy
- Matplotlib and Seaborn
- scikit-learn
- SciPy
- Yellowbrick
- Jupyter Notebook / Google Colab
