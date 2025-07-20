#
 
S
E
T
 
A
S
S
I
G
N
M
E
N
T



```python
import math
import pandas as pd
```

```python
omega={1,2,3,4,5,6,7,8,9,10,11,12}
a ={2,4,6,8,10}
b ={3,8,11,12}
c = {2,3,6,8,9,11}

```

```python
f'Type a: {type(a)}, a: {a}'
```

```
"Type a: <class 'set'>, a: {2, 4, 6, 8, 10}"
```

```python
f'Type b: {type(b)}, b: {b}'
```

```
"Type b: <class 'set'>, b: {8, 3, 11, 12}"
```

```python
f'Type c: {type(c)}, c: {c}'
```

```
"Type c: <class 'set'>, c: {2, 3, 6, 8, 9, 11}"
```

```python
f'Type omega: {type(omega)}, omega: {omega}'
```

```
"Type omega: <class 'set'>, omega: {1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12}"
```

```python
# question 1 : $ A \cap B$ - intersection
ans = a&b
print(ans)
```

```
{8}

```

```python
# question 2 : $ A \cup C$ -union 
ans = a|c
print(ans)
```

```
{2, 3, 4, 6, 8, 9, 10, 11}

```

```python
# absolute A
omega-a
```

```
{1, 3, 5, 7, 9, 11, 12}
```

```python
#The absolute complement of B
omega-b
```

```
{1, 2, 4, 5, 6, 7, 9, 10}
```

```python
# complement of A union B
omega -(a|b)
```

```
{1, 5, 7, 9}
```

```python
#The elements that are in set B but not in set C
b&(omega-c)
```

```
{12}
```

```python
#A\B
a-b
```

```
{2, 4, 6, 10}
```

```python
#c\(b\a)
c-(b-a)
```

```
{2, 6, 8, 9}
```

```python
# (C∩A)∪(C∖B)
(c&a)|(c-b)
```

```
{2, 6, 8, 9}
```

#
 
∣


A


∪


B


∪


C


∣
=
∣


A


∣


+


∣


B


∣


+


∣


C


∣


−


∣


A


∩


B


∣


−


∣


A


∩


C


∣


−


∣


B


∩


C


∣


+


∣


A


∩


B


∩


C


∣

```python
a|b|c
max=len(a|b|c)
print(max)
```

```
9

```

```python
Total = len(a) + len(b) + len(c)-len(a&b)-len(a&c)-len(b&c) + len(a&b&c)
print(Total)
```

```
9

```

```python
print(max==Total)
```

```
True

```

```python
Nina = set(["Cat","Dog","Rabbit","Donkey","Parrot", "Goldfish"])
Mary = set(["Dog","Chinchilla","Horse", "Chicken"])
Eve = set(["Rabbit", "Turtle", "Goldfish"])
```

```python
Eve.remove("Turtle")
print(Eve)
```

```
{'Goldfish', 'Rabbit'}

```

```python
Nina.update(Mary)
print(Nina)
```

```
{'Dog', 'Rabbit', 'Cat', 'Chicken', 'Chinchilla', 'Goldfish', 'Horse', 'Parrot', 'Donkey'}

```

```python
Mary.clear()
print(Mary)
```

```
set()

```

```python
species = len(Nina)
print(species)
```

```
9

```

```python
Nina.difference_update(Eve)
print(Nina)
len(Nina)
```

```
{'Dog', 'Cat', 'Chicken', 'Chinchilla', 'Horse', 'Parrot', 'Donkey'}

```

```
7
```

#
#
 
O
p
t
i
o
n
a
l
 
a
s
s
i
g
n
m
e
n
t

```python
# Load Europe and EU
europe = pd.read_excel('Europe_and_EU.xlsx', sheet_name = 'Europe') 
eu = pd.read_excel('Europe_and_EU.xlsx', sheet_name = 'EU')
eu.head()
```

```
   Rank         Country  2017 population  % of pop.  \
0     1        Germany          82800000      16.18   
1     2          France         67210459      13.10   
2     3  United Kingdom         65808573      12.86   
3     4          Italy          60589445      11.84   
4     5          Spain          46528966       9.09   

   Average relative annual growth  Average absolute annual growth  \
0                            0.76                          628876   
1                            0.40                          265557   
2                            0.65                          428793   
3                           -0.13                          -76001   
4                            0.19                           89037   

   Official figure Date of last figure                     Source  
0         82576900          2017-03-31          Official estimate  
1         67174000          2018-01-01  Monthly official estimate  
2         65648100          2017-06-30          Official estimate  
3         60494000          2018-01-01  Monthly official estimate  
4         46549045          2017-07-01          Official estimate  
```

```python
# Clean country names by stripping whitespace and handling special cases
europe['Country'] = europe['Country'].str.strip()
eu['Country'] = eu['Country'].str.strip()
```

```python
# Preview the data
print("Europe dataset preview:")
print(europe.head(3))
print("\nEU dataset preview:")
print(eu.head(3))

```

```
Europe dataset preview:
   Rank  Country  Population  % of population  \
0   1.0   Russia   143964709            17.15   
1   2.0  Germany    82521653             9.80   
2   3.0   Turkey    80810000             9.60   

   Average relative annual growth (%)  Average absolute annual growth  \
0                                0.19                          294285   
1                                1.20                          600000   
2                                1.34                         1035000   

  Estimated doubling time (Years)  Official figure (where available)  \
0                             368                          146839993   
1                              90                           82800000   
2                              52                           77695904   

   Date of last figure Regional grouping             Source  
0  2017-01-01 00:00:00             EAEU                 [1]  
1  2016-12-31 00:00:00               EU   Official estimate  
2  2016-12-31 00:00:00               NaN                [2]  

EU dataset preview:
   Rank         Country  2017 population  % of pop.  \
0     1         Germany         82800000      16.18   
1     2          France         67210459      13.10   
2     3  United Kingdom         65808573      12.86   

   Average relative annual growth  Average absolute annual growth  \
0                            0.76                          628876   
1                            0.40                          265557   
2                            0.65                          428793   

   Official figure Date of last figure                     Source  
0         82576900          2017-03-31          Official estimate  
1         67174000          2018-01-01  Monthly official estimate  
2         65648100          2017-06-30          Official estimate  

```

```python
# Get sets of country names
europe_countries = set(europe['Country'])
eu_countries = set(eu['Country'])

# Check if EU countries are a subset of European countries
print("\nIs EU a subset of Europe?", eu_countries.issubset(europe_countries))

# Find countries in Europe but not in EU
non_eu_countries = europe_countries - eu_countries

# Display the result
print("\nEuropean countries not in the EU (2018):")
print(sorted(non_eu_countries))
```

```

Is EU a subset of Europe? True

European countries not in the EU (2018):
['Albania', 'Andorra', 'Armenia', 'Azerbaijan', 'Belarus', 'Bosnia and Herzegovina', 'Faroe Islands (Denmark)', 'Georgia', 'Gibraltar (UK)', 'Guernsey (UK)', 'Iceland', 'Isle of Man (UK)', 'Jersey (UK)', 'Kosovo', 'Liechtenstein', 'Macedonia', 'Moldova', 'Monaco', 'Montenegro', 'Norway', 'Russia', 'San Marino', 'Serbia', 'Svalbard (Norway)', 'Switzerland', 'Turkey', 'Ukraine', 'Vatican City', 'Åland Islands (Finland)']

```

