# data I/O

## txt

"w+" to create

```python
f = open("textfile.txt", "w+")
for i in range(10):
   f.write("This is line " + str(i) + "\r\n")
f.close()
```

"a" to append

```python
f = open("textfile.txt", "a")
for i in range(10):
   f.write("This is line " + str(i) + "\r\n")
f.close()
```

"r" to read

```python
f = open("textfile.txt", "r")

if f.mode == 'r':
   contents = f.read()
   print(contents)

if f.mode == 'r':
   fl = f.readlines()
   for x in fl:
       print(x)
```

## dataframe

```python
# create empty
res = pd.DataFrame()

# create from lists
l_dates = ['2022-01-01', '2022-01-02', '2022-01-03']
l_latency = [10, 20, 30]
df_latency = pd.DataFrame(zip(l_dates, l_latency),
                          columns=['date', 'latency'])
```

## csv

```python
# read csv
df = pd.read_csv(dir_path + "file.csv") # default sep=','
df = pd.read_csv(dir_path + "file.csv", sep='\t')

# write
df.to_csv('output_file.csv', index=False)

import os

pwd = '/home/jessieji/'
os.listdir(pwd)

# (a) read all csv files

# (a.1)
file_list = os.listdir(pwd)
csv_list = [pd.read_csv(os.path.join(pwd, file_name)) for file_name in filename_list if filename.endswith('.csv')]

# (a.2)
dir_path = os.getcwd()
df = pd.concat(map(pd.read_csv, glob.glob(os.path.join('', dir_path + '*.csv'))))

# (a.3)
path = r'/Users/project_folder/'
all_files = glob.glob(os.path.join(path, "*.csv"))
df_from_each_file = (pd.read_csv(f) for f in all_files)
df = pd.concat(df_from_each_file, ignore_index=True)


# (b) specify a list of CSV files to merge
# csv_files = [
#     'file1.csv',
#     'file2.csv'
# ]


# (c) specify the prefix of the CSV files
prefix = 'res_'
csv_files = glob.glob(pwd + prefix + '*.csv')

# initialized on empty DataFrame
merged_df = pd.DataFrame()
# loop through each CSV file and append it to the merged DataFrame
for file in csv_files:
    df = pd.read_csv(file)
    merged_df = pd.concat([merged_df, df])
```

## json

```python
import json
import pandas as pd
from pandas.io.json import json_normalize

df = pd.read_csv('ss.csv', sep='\t', index_col = False)

with open('ssResult.json') as f:
    d = json.load(f)

res = json_normalize(d)
res.rename(columns={'event.bucket_name':'bucket', 'event.id':'id', 'timestamp':'dt', 'event.sum_revenue':'revenue', 'event.sum_amount':'amount'}, inplace=True)
res.drop(['version'], axis=1, inplace=True)
res['dt'] = res.dt.str.slice(0,10)
res['dt'] = res.dt.str.replace('-','')

res.groupby('dt')[['id']].size()
```

read

```python
file_input = 'file.json'
with open(file_input) as f:
    d = json.load(f)
df = pd.json_normalize(data=d, record_path='result', meta='timestamp')
```

process data

```python
def parseJson(x):
    keys = list(json.loads(x).keys())
    if 'app' in keys:
        return 'app'
    elif 'web' in keys:
        return 'web'
    else:
        return 'unknown'

df['app_web_status'] = df['info'].apply(getAppWebStatus)

df['out1'] = df['in_arr'].apply(lambda x: x[0])
df['out1'] = df['in_arr'].apply(lambda x: ','.join(map(str, sorted(x[1]))))
```

display json data

```python
json.loads(df_raw['json'][0])
```



# unstructured data

```python
import pandas as pd
import re

filename = 'asr.txt'

with open(filename) as fn:
    ln = fn.readline()    # read each line
    cnt = 1               # Keep count of lines
    d = dict()
    output = pd.DataFrame()
    while ln:
        elm = re.findall(r'{(.+?)}', ln)
        for e in elm:
            d['id'] = cnt
            d['recognition'] = e.split(':')[1]
            d['score'] = e.split(':')[2]           
            output = output.append(d, ignore_index=True)
        ln = fn.readline()
        cnt += 1

output
```

# complex data

## web

```python
import urllib.request

webUrl = urllib.request.urlopen("http://www.google.com")
print("result code: " + str(webUrl.getcode()))
```

## html

```python
from html.parser import HTMLParser

class MyHTMLParser(HTMLParser):
    
    def handle_comment(self, data):
        print("Encountered comment: ", data)
        pos = self.getpos()
        print("\tAt line: ", pos[0], " position ", pos[1])
    
    def handle_starttag(self, tag, attrs):
        global metacount
        if tag == 'meta':
            metacount += 1
        
        print("Encountered tag: ", tag)
        pos = self.getpos()
        print("\tAt line: ", pos[0], " position ", pos[1])
        
        if attrs.__len__() > 0:
            print ("\tAttributes:")
            for a in attrs:
                print ("\t", a[0],"=",a[1])
        
    def handle_endtag(self, tag):
        print("Encountered tag: ", tag)
        pos = self.getpos()
        print("\tAt line: ", pos[0], " position ", pos[1])
        
    def handle_data(self, data):
        if (data.isspace()):
            return
        print("Encountered data: ", data)
        pos = self.getpos()
        print("\tAt line: ", pos[0], " position ", pos[1])


metacount = 0
parser = MyHTMLParser()
f = open("samplehtml.html")
if f.mode == 'r':
    contents = f.read()
    parser.feed(contents)
```

## xml

```python
import xml.dom.minidom

doc = xml.dom.minidom.parse("samplexml.xml")

print (doc.nodeName)
print (doc.firstChild.tagName)
```

parse tags out

```python
skills = doc.getElementsByTagName("skill")
print ("%d skills:" % skills.length)
for skill in skills:
    print (skill.getAttribute("name"))
```

create new tags

```python
newSkill = doc.createElement("skill")
newSkill.setAttribute("name", "jQuery")
doc.firstChild.appendChild(newSkill)

skills = doc.getElementsByTagName("skill")
print ("%d skills:" % skills.length)
for skill in skills:
    print (skill.getAttribute("name"))
```

# print dataframe
```python
lst_os = ['android', 'ios']

def gen_display_os(pv_res, lst_version_id, format_type):
   display(pv_res.style.format(dict(zip(lst_os, [format_type]*len(lst_os)))))



## print mini dataframe side by side
from IPython.display import display_html
from itertools import chain,cycle
def display_side_by_side(*args,titles=cycle([''])):
   html_str=''
   for df,title in zip(args, chain(titles,cycle(['</br>'])) ):
       html_str+='<th style="text-align:center"><td style="vertical-align:top">'
       html_str+=f'<th style="text-align: center;">{title}</th>'
       html_str+=df.to_html().replace('table','table style="display:inline"')
       html_str+='</td></th>'
   display_html(html_str,raw=True)
```
