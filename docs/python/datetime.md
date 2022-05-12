# datetime

```python
from datetime import date, time, datetime, timedelta
```

## date object

```python
today = date.today()
today.year
today.month
today.day
today.weekday()        # 4
days[today.weekday()]  # Fri
```

## time difference

```python
start_time = time.time()
end_time = time.time()
print end_time - start_time
```

## time formatting

```python
now = datetime.now()

print(now.strftime("The current year is %Y"))
print(now.strftime("%a, %d, %B, %Y"))

print(now.strftime("Locale date and time: %c"))
print(now.strftime("Locale date: %x"))
print(now.strftime("Locale time: %X"))

print(now.strftime("Current time: %I:%M:%S %p"))
print(now.strftime("24-hour time: %H:%M"))
```

## timedelta

```python
timedelta(days=365, hours=5, minutes=1)

print("today is: " + str(now))
print("one year from now it will be: " + str(now + timedelta(days=365)))
print("In 2 days and 3 weeks, it will be: " +
     str(now + timedelta(days=2, weeks=3)))

t = datetime.now() - timedelta(weeks=1)
s = t.strftime("%A %B %d, %Y")
print("One week ago, it was: " + s)

print("Tomorrow will be", days[(today.weekday() + 1) % 7])
```

compute hours

```python
def compute_hours(curHour_str, interval_int):

    curHour_obj = datetime.strptime(curHour_str, '%Y%m%d%H')
    end_hour_obj = curHour_obj + timedelta(hours=interval_int)
    end_hour_str = end_hour_obj.strftime('%Y%m%d%H')
    
    return end_hour_str
```

countdown

```python
nyd = date(today.year, 1, 1)

if nyd < today:
    print("New Year's day already went by %d days ago"  % (today-nyd).days)
    nyd = nyd.replace(year = today.year + 1)

time_to_nyd = nyd - today
print("It's just", time_to_nyd.days, "days until New Year's Day")
```

## get a list of dates

```python
start = datetime.strptime("20220101", "%Y%m%d")
end = datetime.strptime("20220201", "%Y%m%d")
date_obj = [start + timedelta(days=x) for x in range(0, (end-start).days)]
dates = [d.strftime("%Y%m%d") for d in date_obj]
```

## convert UTC timestamp

```python
datetime.utcfromtimestamp(1582503300000/1000).strftime('%Y-%m-%d %H:%M:%S')
```

# calendar

```python
import calendar
```

## plain text calendar

```python
c = calendar.TextCalendar(calendar.MONDAY)
st = c.formatmonth(2021, 3, 0, 0)
print(st)
```

## HTML formatted calendar

```python
hc = calendar.HTMLCalendar(calendar.MONDAY)
ht = hc.formatmonth(2021, 3)
print(ht)
```

## loop over month

```python
for name in calendar.month_name:
    print(name)
```

## loop over weekday

```python
for day in calendar.day_name:
    print(day)
```

## calculate days based on a rule

i.e. 1st Friday of each month

```python
print("1st Friday of each month will be on: ")
for m in range(1, 13):
    cal = calendar.monthcalendar(2022, m)
    weekone = cal[0]
    weektwo = cal[1]
    
    if weekone[calendar.FRIDAY] != 0:
        meetday = weekone[calendar.FRIDAY]
    else:
        meetday = weektwo[calendar.FRIDAY]
    
    print("%10s %d" % (calendar.month_name[m], meetday))
```

