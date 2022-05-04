# List

read `.csv` files

```python
import os
import pandas as pd

DATA_PATH = 'data'
file_list = os.listdir(DATA_PATH)
csv_list = [pd.read_csv(os.path.join(DATA_PATH, file_name)) for file_name in filename_list if filename.endswith('.csv')]
```

# generate an array of numbers

```python
import numpy as np
np.linspace(0, 100, num=21)
np.linspace(0, 100, num=20, endpoint=False)
```

# datetime

```python
from datetime import datetime

start = datetime.strptime("20190902", "%Y%m%d")
end = datetime.strptime("20190928", "%Y%m%d")
date_obj = [start + timedelta(days=x) for x in range(0, (end-start).days)]
dates = [d.strftime("%Y%m%d") for d in date_obj]

datetime.utcfromtimestamp(1582503300000/1000).strftime('%Y-%m-%d %H:%M:%S')
```

# number formats

```python
'${:,.2f}'.format(114)
'${:,d}'.format(int(rev))
```