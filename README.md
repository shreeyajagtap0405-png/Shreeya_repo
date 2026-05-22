# Expected CTC Project
# Shreeya Jagtap

# Step 1: Mount Google Drive
from google.colab import drive
drive.mount('/content/drive')

# Step 2: Import libraries pandas and os
import pandas as pd
import os

# Step 3: Load dataset from Google Drive
folder_path = "/content/drive/My Drive/ColabData"

file_name = None
for f in os.listdir(folder_path):
    if f.endswith(".csv") or f.endswith(".xlsx"):
        file_name = f
        break

if file_name is None:
    raise Exception("No Excel or CSV file found")

file_path = folder_path + "/" + file_name

if file_name.endswith(".csv"):
    data = pd.read_csv(file_path)
else:
    data = pd.read_excel(file_path)

print("\nDataset Loaded Successfully!")
print(data.head())

# Step 4: Handle missing values
data = data.fillna(data.mode().iloc[0])

# Step 5: Identify target column (Expected CTC)
target_column = data.columns[-1]
feature_columns = data.columns[:-1]

print("\nInput Parameters Required:")
for col in feature_columns:
    print("-", col)

# Step 6: Taking user input for all columns
user_input = {}

print("\nEnter Candidate Details:")
for col in feature_columns:
    value = input(f"Enter {col}: ")
    user_input[col] = value

# Convert user input to DataFrame
user_df = pd.DataFrame([user_input])

# Step 7: Convert data types to match dataset
for col in feature_columns:
    try:
        user_df[col] = user_df[col].astype(data[col].dtype)
    except:
        pass

# Step 8: Find similar profiles (exact match)
filtered_data = data.copy()

for col in feature_columns:
    filtered_data = filtered_data[filtered_data[col] == user_df[col].iloc[0]]

# Step 9: If no exact match, relax filtering (ignore one column at a time)
if filtered_data.empty:
    filtered_data = data.copy()
    for col in feature_columns[:-1]:
        filtered_data = filtered_data[filtered_data[col] == user_df[col].iloc[0]]
        if not filtered_data.empty:
            break

# Step 10: Calculate Expected CTC
if filtered_data.empty:
    print("\nNo similar profile found.")
    print("Using overall average Expected CTC.")
    expected_ctc = data[target_column].mean()
else:
    expected_ctc = filtered_data[target_column].mean()

# Step 11: Display result
print("\n------------------------------------")
print("Predicted Expected CTC:", int(expected_ctc))
print("Based on", len(filtered_data), "similar employee profiles")
print("------------------------------------")

