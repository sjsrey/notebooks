---
redirect_from:
  - "/model/spint/dispersion-test"
interact_link: content/model/spint/dispersion_test.ipynb
title: 'dispersion_test'
prev_page:
  url: /model/spint/validate_gravity
  title: 'validate_gravity'
next_page:
  url: /model/spint/glm_speed
  title: 'glm_speed'
comment: "***PROGRAMMATICALLY GENERATED, DO NOT EDIT. SEE ORIGINAL FILES IN /content***"
---



{:.input_area}
```python
import os
os.chdir('/Users/toshan/dev/pysal/pysal/contrib/spint')
from count_model import CountModel
import numpy as np
import pandas as pd
from gravity import Gravity, Production, Attraction, Doubly, BaseGravity
import statsmodels.formula.api as smf
from statsmodels.api import families
os.chdir('/Users/toshan/dev/pysal/pysal/contrib/glm')
from glm import GLM
from family import Poisson, QuasiPoisson

import pysal
import os
os.chdir('/Users/toshan/dev/pysal/pysal/contrib/spint')
from dispersion import alpha_disp, phi_disp


```




{:.input_area}
```python
rec = pd.read_csv('/Users/toshan/Documents/RecreationDemand.csv')
```




{:.input_area}
```python
rec.head()
```





<div markdown="0">
<div style="max-height:1000px;max-width:1500px;overflow:auto;">
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Unnamed: 0</th>
      <th>trips</th>
      <th>quality</th>
      <th>ski</th>
      <th>income</th>
      <th>userfee</th>
      <th>costC</th>
      <th>costS</th>
      <th>costH</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td> 1</td>
      <td> 0</td>
      <td> 0</td>
      <td> yes</td>
      <td> 4</td>
      <td> no</td>
      <td> 67.59</td>
      <td> 68.620</td>
      <td> 76.800</td>
    </tr>
    <tr>
      <th>1</th>
      <td> 2</td>
      <td> 0</td>
      <td> 0</td>
      <td>  no</td>
      <td> 9</td>
      <td> no</td>
      <td> 68.86</td>
      <td> 70.936</td>
      <td> 84.780</td>
    </tr>
    <tr>
      <th>2</th>
      <td> 3</td>
      <td> 0</td>
      <td> 0</td>
      <td> yes</td>
      <td> 5</td>
      <td> no</td>
      <td> 58.12</td>
      <td> 59.465</td>
      <td> 72.110</td>
    </tr>
    <tr>
      <th>3</th>
      <td> 4</td>
      <td> 0</td>
      <td> 0</td>
      <td>  no</td>
      <td> 2</td>
      <td> no</td>
      <td> 15.79</td>
      <td> 13.750</td>
      <td> 23.680</td>
    </tr>
    <tr>
      <th>4</th>
      <td> 5</td>
      <td> 0</td>
      <td> 0</td>
      <td> yes</td>
      <td> 3</td>
      <td> no</td>
      <td> 24.02</td>
      <td> 34.033</td>
      <td> 34.547</td>
    </tr>
  </tbody>
</table>
</div>
</div>





{:.input_area}
```python
y = rec['trips'].values.reshape((-1,1))
```




{:.input_area}
```python
X = rec[['quality', 'income', 'costC', 'costS', 'costH']].values
```




{:.input_area}
```python
test = CountModel(y, X, constant=False)
```




{:.input_area}
```python
glm_results = test.fit(framework='glm')
```




{:.input_area}
```python
phi_disp(glm_results)
```





{:.output_data_text}
```
array([  7.30811593e+00,   2.71035909e+00,   3.36051997e-03])
```





{:.input_area}
```python
alpha_disp(glm_results)
```





{:.output_data_text}
```
array([  6.30811593e+00,   2.71035909e+00,   3.36051997e-03])
```





{:.input_area}
```python
alpha_disp(glm_results, lambda x: x**2)
```





{:.output_data_text}
```
array([  1.55402055e+00,   3.38253708e+00,   3.59097912e-04])
```





{:.input_area}
```python
#Prepare some test data - columbus example
db = pysal.open(pysal.examples.get_path('columbus.dbf'),'r')
y = np.array(db.by_col("HOVAL"))
y = np.reshape(y, (49,1))
X = []
#X.append(np.ones(len(y)))
X.append(db.by_col("INC"))
X.append(db.by_col("CRIME"))
X = np.array(X).T

poisson_y = np.round(y).astype(int)
```




{:.input_area}
```python
test = CountModel(poisson_y, X)
```




{:.input_area}
```python
glm_results = test.fit(framework='glm')
```




{:.input_area}
```python
phi_disp(glm_results)
```





{:.output_data_text}
```
array([ 5.39968689,  2.3230411 ,  0.01008847])
```





{:.input_area}
```python
alpha_disp(glm_results)
```





{:.output_data_text}
```
array([ 4.39968689,  2.3230411 ,  0.01008847])
```





{:.input_area}
```python
alpha_disp(glm_results, lambda x:x**2)
```





{:.output_data_text}
```
array([ 0.10690133,  2.24709978,  0.01231683])
```





{:.input_area}
```python
model1 = GLM(y, X, constant=False, family=Poisson()).fit()
```


{:.output_stream}
```
<class 'family.Poisson'>
<class 'family.Poisson'>
<class 'family.Poisson'>

```



{:.input_area}
```python
model2 = GLM(y, X, constant=False, family=QuasiPoisson()).fit()
```


{:.output_stream}
```
<class 'family.QuasiPoisson'>
<class 'family.QuasiPoisson'>
<class 'family.QuasiPoisson'>

```



{:.input_area}
```python
print model1.scale
print model2.scale
```


{:.output_stream}
```
1.0
<class 'family.QuasiPoisson'>
7.02573401193

```
