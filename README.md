# **Network Analysis of Stock Price Data**

## **1. Data Collection and Processing**

In this project, historical stock price data was collected for multiple companies listed on the Indian stock market. The data was obtained using the Yahoo Finance API and includes daily values such as Open, High, Low, Close, and Volume for each stock over a one-year period.

### **Preprocessing Steps**

* The raw data was downloaded and stored as individual CSV files for each stock.
* Column inconsistencies and formatting issues were handled.
* Multi-level columns (if present) were flattened.
* Only relevant columns were retained: Date, Open, High, Low, Close, and Volume.
* The dataset was reversed to ensure chronological order (oldest to newest).
* Missing or malformed values were cleaned and converted into appropriate numeric formats.

This preprocessing ensured a clean and consistent dataset suitable for further analysis.

---

## **2. Creation of Log Return Feature**

To analyze stock price movements effectively, log returns were computed from closing prices.

### **Mathematical Definition**

The log return is defined as:

[
R_t = \log(P_t) - \log(P_{t-1})
]

where:

* ( P_t ) is the closing price at time ( t )
* ( P_{t-1} ) is the closing price at time ( t-1 )

### **Implementation in Code**

* The cleaned closing price column (`CLOSE`) was used.
* Log returns were calculated using:

  ```python
  df['RETURN_PRICE'] = np.log(df['CLOSE']) - np.log(df['CLOSE'].shift(1))
  ```
* Missing values introduced due to shifting were removed.

Log returns normalize the data and make it suitable for statistical and network analysis.

---

## **3. Correlation Analysis**

### **Mathematical Explanation**

The Pearson correlation coefficient between two variables ( X ) and ( Y ) is given by:

[
r_{XY} = \frac{\sum (X_i - \bar{X})(Y_i - \bar{Y})}{\sqrt{\sum (X_i - \bar{X})^2} \cdot \sqrt{\sum (Y_i - \bar{Y})^2}}
]

This measures the strength and direction of the linear relationship between two time series.

* ( r = 1 ): perfect positive correlation
* ( r = 0 ): no correlation
* ( r = -1 ): perfect negative correlation

---

### **Implementation in Code**

* A data matrix was constructed where:

  * Rows represent time (days)
  * Columns represent different stocks
  * Values represent log returns

* Correlation matrix computed using:

  ```python
  correlation = matrix.corr(method='pearson')
  ```

---

### **Thresholding and Network Construction**

* A threshold value (e.g., 0.25) was applied:

  * If correlation ≥ threshold → edge exists
  * Otherwise → no edge

* This converts the correlation matrix into an **adjacency matrix**, which is then used to construct a graph:

  * Nodes → stocks
  * Edges → significant correlations

Different thresholds affect the network:

* Low threshold → dense network
* High threshold → sparse network

---

## **4. Centrality Measures**

Centrality measures are used to identify important nodes (stocks) in the network.

---

### **4.1 Degree Centrality**

#### **Mathematical Definition**

[
C_D(i) = \frac{deg(i)}{N - 1}
]

* ( deg(i) ): number of edges connected to node ( i )
* ( N ): total number of nodes

#### **Interpretation**

Measures how many direct connections a stock has.

#### **Implementation**

```python
degree_centrality = nx.degree_centrality(G)
```

---

### **4.2 Closeness Centrality**

#### **Mathematical Definition**

[
C_C(i) = \frac{N - 1}{\sum d(i, j)}
]

* ( d(i, j) ): shortest path distance

#### **Interpretation**

Measures how close a node is to all other nodes in the network.

#### **Implementation**

```python
closeness_centrality = nx.closeness_centrality(G)
```

---

### **4.3 Betweenness Centrality**

#### **Mathematical Definition**

[
C_B(i) = \sum \frac{\sigma_{st}(i)}{\sigma_{st}}
]

* ( \sigma_{st} ): number of shortest paths from ( s ) to ( t )
* ( \sigma_{st}(i) ): paths passing through node ( i )

#### **Interpretation**

Measures how often a node acts as a bridge between other nodes.

#### **Implementation**

```python
betweenness_centrality = nx.betweenness_centrality(G)
```

---

### **4.4 Eigenvector Centrality**

#### **Mathematical Definition**

[
Ax = \lambda x
]

or

[
C_E(i) = \frac{1}{\lambda} \sum A_{ij} C_E(j)
]

#### **Interpretation**

Measures influence of a node based on the importance of its neighbors.

#### **Implementation**

```python
eigenvector_centrality = nx.eigenvector_centrality(G)
```

---

## **Effect of Threshold on Centrality**

The threshold used to construct the network significantly impacts centrality measures:

* **Lower Threshold (e.g., 0.25)**:

  * More edges
  * Higher degree values
  * Network appears dense

* **Higher Threshold (e.g., 0.5)**:

  * Fewer edges
  * More selective relationships
  * Network becomes sparse

Thus, centrality results depend on how strictly correlations are filtered.

---

## **Conclusion**

This project demonstrates how financial time series data can be transformed into a network representation using correlation analysis. By applying centrality measures, the structure of the market can be understood in terms of connectivity, influence, and information flow among stocks.
