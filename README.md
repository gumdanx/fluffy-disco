# WhatsApp Chat to Excel Converter for Tree Surveys

## Overview
This project presents a tool for converting WhatsApp chat data into structured Excel files, specifically tailored for tree surveys. It focuses on organizing data about tree species, their height, and trunk measurements.

## How to Use
1. **Create a WhatsApp Group:** For example, a group named "Medidas".
2. **Mark Each Segment:** Use identifiers like P1, P2, etc., to denote different segments.
3. **Record Data:** Enter the name of the tree species, its measurements, and height.
4. **Export Chat:**
   - Open chat options (three dots).
   - Click on "More".
   - Select "Export chat".
   - Choose to export without media.
   - Share the exported chat file back to the same chat.
   - Download and upload the file to this platform (see instructions below).

5. **Prepare the Data:**
   - Copy this page to your drive for editing.
   - Delete the example file 'WhatsApp Chat with Medidas.txt' from the folder on the left.
   - Upload your exported chat conversation.

6. **Run the Code:**
   - Modify the file name in the first line of the code to match the name of your uploaded file.
   - Execute the script by pressing Ctrl + F9.

7. **Download the Processed File:**
   - Refresh the page.
   - Download the 'medidas_segmentadas.xlsx' file.

## Processing Steps
- The script reads the exported WhatsApp chat.
- It identifies and segregates data based on predefined patterns (like P1, P2, etc.).
- Tree species, measurements, and heights are extracted and organized.
- Irrelevant messages (like "deleted" or "apagou") are filtered out.
- Data is structured into a DataFrame, with columns for species, height, number of measurements, and individual measurements.
- Each segment (e.g., P1, P2) is saved as a separate CSV file and as a sheet in an Excel file.

## Example Code Snippet
```python
# Import necessary libraries
import re
import pandas as pd
from collections import defaultdict

# Function to process the content of the chat file
def process_content(content):
    data_dict = defaultdict(list)
    current_segment = None
    current_species = None
    current_height = None
    measures = []

    # Regular expressions to identify segments, species, measurements, and heights
    segment_pattern = re.compile(r"\d{2}/\d{2}/\d{2}, \d{2}:\d{2} - .+: P\d+")
    species_pattern = re.compile(r"^\d{2}/\d{2}/\d{2}, \d{2}:\d{2} - .+: (.+)$")
    measure_pattern = re.compile(r"^(\d+(?:,\d+)?)\n$")
    height_pattern = re.compile(r"^(\d+m)\n$")
    
    # Process each line of the chat
    for line in content:
        if segment_pattern.match(line):
            if current_segment and current_species:
                data_dict[current_segment].append([current_species, current_height, len(measures)] + measures)
            current_segment = line.strip().split()[-1]
            current_species = None
            current_height = None
            measures = []
        elif species_pattern.match(line):
            if current_species:
                data_dict[current_segment].append([current_species, current_height, len(measures)] + measures)
            current_species = species_pattern.match(line).group(1)
            measures = []
        elif height_pattern.match(line):
            current_height = height_pattern.match(line).group(1)
        elif measure_pattern.match(line):
            measures.append(measure_pattern.match(line).group(1))

    if current_species:
        data_dict[current_segment].append([current_species, current_height, len(measures)] + measures)

    return data_dict

# Example: Reading and processing the chat file
file_path = 'WhatsApp Chat with Medidas.txt'
with open(file_path, 'r') as file:
    content = file.readlines()

data_dict = process_content(content)

# Creating DataFrames and exporting to CSV and XLSX files
# ...
```

## Output
The tool outputs CSV files for each segment and an Excel file with each segment as a separate sheet, providing a structured format for tree survey data extracted from WhatsApp chats.
