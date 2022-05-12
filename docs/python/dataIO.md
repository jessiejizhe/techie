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

## csv

read files ending with `.csv`

```python
import os
import pandas as pd

DATA_PATH = 'data'
file_list = os.listdir(DATA_PATH)
csv_list = [pd.read_csv(os.path.join(DATA_PATH, file_name)) for file_name in filename_list if filename.endswith('.csv')]
```

## json

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

