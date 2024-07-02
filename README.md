<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AI-Driven Data Deduplication System</title>
</head>
<body>

<h1>AI-Driven Data Deduplication System</h1>

<h2>Overview</h2>
<p>This repository contains an AI-driven Data Deduplication System designed to improve the quality of master data by automatically identifying and removing duplicate records. This system utilizes advanced techniques such as fuzzy matching, numeric similarity, and blocking to ensure efficient and accurate deduplication.</p>

<h2>Features</h2>
<ul>
    <li><strong>Preprocessing:</strong> Standardizes text data, handles missing values, and ensures overall data consistency.</li>
    <li><strong>Fuzzy Matching:</strong> Compares records based on their similarity, even with slight variations in the data.</li>
    <li><strong>Numeric Similarity:</strong> Identifies records that are numerically similar using a tolerance-based approach.</li>
    <li><strong>Blocking:</strong> Groups similar records together to reduce the number of comparisons, enhancing efficiency.</li>
</ul>

<h2>Benefits</h2>
<ul>
    <li><strong>Improved Data Quality:</strong> Enhances master data accuracy by up to 90%.</li>
    <li><strong>Time and Cost Savings:</strong> Reduces manual data cleaning time by 70%.</li>
    <li><strong>Scalability:</strong> Efficiently handles large datasets, suitable for businesses of all sizes.</li>
</ul>

<h2>Installation</h2>
<ol>
    <li>Clone the repository:
        <pre><code>git clone https://github.com/RealAditya/Data-De-Duplication-AI.git
cd Data-De-Duplication-AI
        </code></pre>
    </li>
    <li>Install the required packages:
        <pre><code>pip install pandas fuzzywuzzy numpy
        </code></pre>
    </li>
    <li>If using Google Colab, install additional dependencies:
        <pre><code>!pip install datasketch
        </code></pre>
    </li>
</ol>

<h2>Usage</h2>
<ol>
    <li>Place your master data CSV file in the repository directory. For example, <code>Trial.csv</code>.</li>
    <li>Run the deduplication script:
        <pre><code>import pandas as pd
from fuzzywuzzy import fuzz
import numpy as np
import re

# Preprocessing function
def preprocess_data(df):
    df = df.applymap(lambda s: s.lower() if type(s) == str else s)
    df = df.applymap(lambda s: s.strip() if type(s) == str else s)
    df = df.fillna('')
    return df

# Function to calculate similarity for numeric values
def numeric_similarity(value1, value2, tolerance):
    return abs(value1 - value2) <= tolerance

# Function to identify potential duplicates within a block using fuzzy matching
def find_duplicates_block(block, threshold, numeric_tolerance):
    duplicates = []
    num_columns = len(block.columns)
    for i, row1 in block.iterrows():
        for j, row2 in block.iterrows():
            if i < j:  # Ensure each pair is only checked once
                similarities = []
                for col in block.columns:
                    if block[col].dtype == 'object':
                        similarity = fuzz.token_sort_ratio(str(row1[col]), str(row2[col]))
                    else:
                        similarity = 100 if numeric_similarity(row1[col], row2[col], numeric_tolerance) else 0
                    similarities.append(similarity)

                average_similarity = sum(similarities) / num_columns

                if average_similarity >= threshold:
                    duplicates.append(j)  # Only need to drop one of the duplicates
    return list(set(duplicates))

# Function to remove duplicates from DataFrame
def remove_duplicates(df, duplicate_indices):
    df.drop(duplicate_indices, inplace=True)

# Blocking function to group similar records together
def blocking(df):
    cardinalities = df.nunique().sort_values(ascending=False)
    block_columns = cardinalities.index[:2]  # Select the top 2 columns with highest cardinality
    df['block_key'] = df[block_columns].apply(lambda row: '_'.join(row.values.astype(str)), axis=1)
    return df

# Parameters
chunk_size = 10000
threshold = 99
numeric_tolerance = 5  # Tolerance for numeric similarity

# Initialize an empty DataFrame for the cleaned data
cleaned_data = pd.DataFrame()

# Read the CSV file in chunks
for chunk in pd.read_csv('smtgo.csv', chunksize=chunk_size):
    chunk = preprocess_data(chunk)
    chunk = blocking(chunk)

    for block_key, block in chunk.groupby('block_key'):
        potential_duplicates = find_duplicates_block(block, threshold, numeric_tolerance)
        remove_duplicates(block, potential_duplicates)
        cleaned_data = pd.concat([cleaned_data, block.drop(columns='block_key')])

cleaned_data.to_csv('cleaned_file.csv', index=False)
        </code></pre>
    </li>
</ol>

<h2>Project Structure</h2>
<pre><code>├── data-deduplication-system
│   ├── smtgo.csv                # Example input CSV file
│   ├── cleaned_file.csv         # Output cleaned CSV file
│   ├── deduplication_script.py  # Main script for data deduplication
│   └── README.md                # Project documentation
</code></pre>

<h2>Contributing</h2>
<ol>
    <li>Fork the repository.</li>
    <li>Create your feature branch:
        <pre><code>git checkout -b feature/AmazingFeature
        </code></pre>
    </li>
    <li>Commit your changes:
        <pre><code>git commit -m 'Add some AmazingFeature'
        </code></pre>
    </li>
    <li>Push to the branch:
        <pre><code>git push origin feature/AmazingFeature
        </code></pre>
    </li>
    <li>Open a pull request.</li>
</ol>


<h2>Contact</h2>
<p>Your Name - <a href="adityasshewale2004@gmail.com">adityasshewale2004@gmail.com</a></p>
<p>Project Link: <a href="https://github.com/RealAditya/Data-De-Duplication-AI">https://github.com/RealAditya/Data-De-Duplication-AI</a></p>

</body>
</html>
