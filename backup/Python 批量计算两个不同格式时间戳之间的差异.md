```python
from datetime import datetime

# Define the timestamps as strings
timestamps = [
    ("20240805 09:51:14:177", "24-8-5  9:51:7"),
    ("20240805 09:55:31:281", "24-8-5  9:55:25"),
    ("20240805 10:01:31:451", "24-8-5  10:1:25"),
    ("20240805 10:01:32:289", "24-8-5  10:1:25"),
    ("20240805 10:10:31:368", "24-8-5  10:10:25"),
    ("20240805 10:10:32:310", "24-8-5  10:10:25"),
    ("20240805 10:16:31:441", "24-8-5  10:16:25"),
    ("20240805 10:25:31:462", "24-8-5  10:22:35"),
    ("20240805 10:25:32:368", "24-8-5  10:22:35"),
    ("20240805 10:28:21:457", "24-8-5  10:25:25"),
    ("20240805 10:31:21:541", "24-8-5  10:28:25"),
    ("20240805 10:31:22:504", "24-8-5  10:28:25"),
    ("20240805 10:43:21:562", "24-8-5  10:40:25"),
    ("20240805 10:43:22:439", "24-8-5  10:40:25"),
    ("20240805 10:49:21:690", "24-8-5  10:46:25")
]

# Parse and calculate differences
differences = []
for ts1, ts2 in timestamps:
    # Parse the timestamps
    dt1 = datetime.strptime(ts1, "%Y%m%d %H:%M:%S:%f")
    dt2 = datetime.strptime(ts2.replace(" ", ""), "%y-%m-%d%H:%M:%S")
    
    # Calculate the difference
    diff = dt1 - dt2
    
    # Store the difference
    differences.append(diff.total_seconds())

# Print the differences
for i, diff in enumerate(differences):
    print(f"Difference {i+1}: {diff} seconds")
```
