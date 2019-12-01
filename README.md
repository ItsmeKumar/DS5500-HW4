
# DS 5500 Assignment 4


```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline
import re
```


```python
math_data = pd.read_csv('./math-achievement-sch-sy2015-16.csv', low_memory=False)
arts_data = pd.read_csv('./rla-achievement-sch-sy2015-16.csv', low_memory=False)
fiscal_data = pd.read_csv('./Sdf16_1a.txt', sep='\t', low_memory=False)
```

## Problem 1

For the districts you selected for budget cuts in HW 3 Problem 4, calculate and visualize the proportion of each districtâs total funding that will be lost.
Which districts will be affected by your budget cuts the most?


```python
condition = fiscal_data['TFEDREV']>0
cut_amount = int((fiscal_data.loc[condition, 'TFEDREV'].sum())*0.15)
print('15% of the U.S. federal budget currently being spent on funding school districts:', cut_amount)
```

    15% of the U.S. federal budget currently being spent on funding school districts: 8340411300



```python
condition = (fiscal_data['TOTALREV']>0) & (fiscal_data['TOTALEXP']>0)
cut_rev = fiscal_data.assign(DEBT = fiscal_data['TOTALEXP'] - fiscal_data['TOTALREV']).query('DEBT<0')
cut_rev['LEAID'] = cut_rev['LEAID'].astype(str)
cut_rev['DEBT'] = -cut_rev['DEBT']
cut_rev = cut_rev.assign(AVAILABLE_CUT = cut_rev[['DEBT','TFEDREV']].min(axis=1))
available_amount = cut_rev['AVAILABLE_CUT'].sum()
cut_rev['CUT'] = (cut_amount/available_amount)*cut_rev['AVAILABLE_CUT']
cut_rev['CUT'] = round(cut_rev['CUT'])
cut_rev = cut_rev.query('CUT>0')
cut_rev['PROPORTION'] = cut_rev['CUT']/cut_rev['TFEDREV']

print('Ranking of school districts by cut in descending order:')
cut_rev = cut_rev.sort_values('CUT', ascending=False)
cut_rev[['LEAID','CUT','PROPORTION']]
```

    Ranking of school districts by cut in descending order:

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>LEAID</th>
      <th>CUT</th>
      <th>PROPORTION</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1904</th>
      <td>0622710</td>
      <td>280561869.0</td>
      <td>0.257066</td>
    </tr>
    <tr>
      <th>3306</th>
      <td>1500030</td>
      <td>113901886.0</td>
      <td>0.436187</td>
    </tr>
    <tr>
      <th>15038</th>
      <td>4218990</td>
      <td>90139303.0</td>
      <td>0.332165</td>
    </tr>
    <tr>
      <th>9784</th>
      <td>3200060</td>
      <td>60848490.0</td>
      <td>0.219173</td>
    </tr>
    <tr>
      <th>3057</th>
      <td>1201440</td>
      <td>56389352.0</td>
      <td>0.256337</td>
    </tr>
    <tr>
      <th>6328</th>
      <td>2400480</td>
      <td>49165663.0</td>
      <td>0.436187</td>
    </tr>
    <tr>
      <th>6006</th>
      <td>2201170</td>
      <td>44611437.0</td>
      <td>0.436187</td>
    </tr>
    <tr>
      <th>7676</th>
      <td>2680995</td>
      <td>31849921.0</td>
      <td>0.436187</td>
    </tr>
    <tr>
      <th>5622</th>
      <td>2012990</td>
      <td>31376223.0</td>
      <td>0.424164</td>
    </tr>
    <tr>
      <th>3059</th>
      <td>1201500</td>
      <td>30676579.0</td>
      <td>0.163717</td>
    </tr>
    <tr>
      <th>15402</th>
      <td>4502310</td>
      <td>30562298.0</td>
      <td>0.419708</td>
    </tr>
    <tr>
      <th>6329</th>
      <td>2400510</td>
      <td>28893447.0</td>
      <td>0.199253</td>
    </tr>
    <tr>
      <th>3207</th>
      <td>1302550</td>
      <td>27456212.0</td>
      <td>0.192367</td>
    </tr>
    <tr>
      <th>2174</th>
      <td>0635310</td>
      <td>27252077.0</td>
      <td>0.328093</td>
    </tr>
    <tr>
      <th>3180</th>
      <td>1301740</td>
      <td>26772707.0</td>
      <td>0.219921</td>
    </tr>
    <tr>
      <th>11351</th>
      <td>3605850</td>
      <td>25488137.0</td>
      <td>0.199954</td>
    </tr>
    <tr>
      <th>2150</th>
      <td>0634320</td>
      <td>25404390.0</td>
      <td>0.167767</td>
    </tr>
    <tr>
      <th>3073</th>
      <td>1201920</td>
      <td>24586103.0</td>
      <td>0.371330</td>
    </tr>
    <tr>
      <th>2240</th>
      <td>0638010</td>
      <td>23912631.0</td>
      <td>0.436187</td>
    </tr>
    <tr>
      <th>6316</th>
      <td>2400120</td>
      <td>23599449.0</td>
      <td>0.230788</td>
    </tr>
    <tr>
      <th>16123</th>
      <td>4811680</td>
      <td>23024118.0</td>
      <td>0.224999</td>
    </tr>
    <tr>
      <th>6315</th>
      <td>2400090</td>
      <td>22410840.0</td>
      <td>0.139301</td>
    </tr>
    <tr>
      <th>2372</th>
      <td>0691030</td>
      <td>22403861.0</td>
      <td>0.287443</td>
    </tr>
    <tr>
      <th>2575</th>
      <td>0804800</td>
      <td>22224588.0</td>
      <td>0.436187</td>
    </tr>
    <tr>
      <th>7665</th>
      <td>2680880</td>
      <td>21884362.0</td>
      <td>0.436187</td>
    </tr>
    <tr>
      <th>6463</th>
      <td>2502790</td>
      <td>21321245.0</td>
      <td>0.276200</td>
    </tr>
    <tr>
      <th>3058</th>
      <td>1201470</td>
      <td>20721052.0</td>
      <td>0.284505</td>
    </tr>
    <tr>
      <th>3020</th>
      <td>1200330</td>
      <td>19990439.0</td>
      <td>0.371908</td>
    </tr>
    <tr>
      <th>15911</th>
      <td>4800211</td>
      <td>19737015.0</td>
      <td>0.436187</td>
    </tr>
    <tr>
      <th>1693</th>
      <td>0612330</td>
      <td>19698630.0</td>
      <td>0.337554</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>6940</th>
      <td>2600946</td>
      <td>436.0</td>
      <td>0.001166</td>
    </tr>
    <tr>
      <th>12381</th>
      <td>3805670</td>
      <td>436.0</td>
      <td>0.014065</td>
    </tr>
    <tr>
      <th>3384</th>
      <td>1600870</td>
      <td>436.0</td>
      <td>0.002224</td>
    </tr>
    <tr>
      <th>6970</th>
      <td>2600980</td>
      <td>436.0</td>
      <td>0.010900</td>
    </tr>
    <tr>
      <th>3600</th>
      <td>1700236</td>
      <td>436.0</td>
      <td>0.436000</td>
    </tr>
    <tr>
      <th>9019</th>
      <td>3000020</td>
      <td>436.0</td>
      <td>0.001163</td>
    </tr>
    <tr>
      <th>7007</th>
      <td>2601019</td>
      <td>436.0</td>
      <td>0.004688</td>
    </tr>
    <tr>
      <th>3602</th>
      <td>1700238</td>
      <td>436.0</td>
      <td>0.003230</td>
    </tr>
    <tr>
      <th>3609</th>
      <td>1700246</td>
      <td>436.0</td>
      <td>0.013212</td>
    </tr>
    <tr>
      <th>12932</th>
      <td>3901558</td>
      <td>436.0</td>
      <td>0.004275</td>
    </tr>
    <tr>
      <th>9000</th>
      <td>3000001</td>
      <td>436.0</td>
      <td>0.003726</td>
    </tr>
    <tr>
      <th>8801</th>
      <td>2922470</td>
      <td>436.0</td>
      <td>0.003230</td>
    </tr>
    <tr>
      <th>6142</th>
      <td>2308135</td>
      <td>436.0</td>
      <td>0.436000</td>
    </tr>
    <tr>
      <th>6155</th>
      <td>2309090</td>
      <td>436.0</td>
      <td>0.109000</td>
    </tr>
    <tr>
      <th>10462</th>
      <td>3408430</td>
      <td>436.0</td>
      <td>0.008720</td>
    </tr>
    <tr>
      <th>17486</th>
      <td>5009240</td>
      <td>436.0</td>
      <td>0.436000</td>
    </tr>
    <tr>
      <th>14043</th>
      <td>4019980</td>
      <td>436.0</td>
      <td>0.001530</td>
    </tr>
    <tr>
      <th>13962</th>
      <td>4014670</td>
      <td>436.0</td>
      <td>0.001468</td>
    </tr>
    <tr>
      <th>12503</th>
      <td>3880260</td>
      <td>436.0</td>
      <td>0.001309</td>
    </tr>
    <tr>
      <th>12495</th>
      <td>3880100</td>
      <td>436.0</td>
      <td>0.000655</td>
    </tr>
    <tr>
      <th>9433</th>
      <td>3024300</td>
      <td>436.0</td>
      <td>0.001563</td>
    </tr>
    <tr>
      <th>13750</th>
      <td>4000788</td>
      <td>436.0</td>
      <td>0.001751</td>
    </tr>
    <tr>
      <th>5833</th>
      <td>2200047</td>
      <td>436.0</td>
      <td>0.001074</td>
    </tr>
    <tr>
      <th>2017</th>
      <td>0627900</td>
      <td>436.0</td>
      <td>0.000741</td>
    </tr>
    <tr>
      <th>17829</th>
      <td>5300450</td>
      <td>436.0</td>
      <td>0.015571</td>
    </tr>
    <tr>
      <th>7973</th>
      <td>2700422</td>
      <td>436.0</td>
      <td>0.006812</td>
    </tr>
    <tr>
      <th>644</th>
      <td>0400844</td>
      <td>436.0</td>
      <td>0.012457</td>
    </tr>
    <tr>
      <th>6131</th>
      <td>2307200</td>
      <td>436.0</td>
      <td>0.436000</td>
    </tr>
    <tr>
      <th>4375</th>
      <td>1735940</td>
      <td>436.0</td>
      <td>0.000790</td>
    </tr>
    <tr>
      <th>12742</th>
      <td>3900568</td>
      <td>436.0</td>
      <td>0.003545</td>
    </tr>
  </tbody>
</table>
<p>11324 rows Ã 3 columns</p>
</div>




```python
cut_rev['PROPORTION'].hist()
plt.title('Proportion of total cut for selected schools');
```


![png](https://github.com/ItsmeKumar/DS5500-HW4/blob/master/plots/output_7_0.png)



```python
plt.barh(cut_rev['NAME'][0:10], cut_rev['CUT'][0:10])
plt.xlabel("Cut Amount $")
plt.title("Top 10 school disticts taking highest cut");
```


![png](https://github.com/ItsmeKumar/DS5500-HW4/blob/master/plots/output_8_0.png)


## Problem 2

A common problem with purely data-driven solutions is that they can inadvertently perpetuate hidden pre-existing biases in the data, and further disadvantage groups that are already disadvantaged.
Calculate the proportion of enrolled students by race for each district, then visualize the distributions of these for districts that received budget cuts versus districts that did not receive budget cuts.
Comment on whether the the distributions appear to be the same or different. Did your selection include any hidden biases, or manage to avoid them?


```python
demographics = pd.read_csv('./ccd_lea_052_1516_w_1a_011717.csv', low_memory=False)
demographics = demographics.query('TOTAL>=0')
demographics = demographics.rename(columns={'AM':'American Indian/Alaska Native','AS':'Asian','HI':'Hispanic', 'BL':'Black',
                            'WH':'White','HP':'Hawaiian Native / Pacific Islander', 'TR':'Two or More Races'})
```


```python
demographics = (
    demographics[['American Indian/Alaska Native','Asian','Hispanic','Black','White','Hawaiian Native / Pacific Islander','Two or More Races']]
    .div(demographics.TOTAL, axis=0)
    .assign(LEAID = demographics['LEAID'])
)
demographics['LEAID'] = demographics['LEAID'].astype(str)
```


```python
budg_cut = demographics[demographics['LEAID'].isin(cut_rev['LEAID'])].drop('LEAID', axis=1).mean().reset_index().rename(columns = {0:'Budget Cut'})
no_budg_cut = demographics[~demographics['LEAID'].isin(cut_rev['LEAID'])].drop('LEAID', axis=1).mean().reset_index().rename(columns = {0:'No Budget Cut'})
race_dist = pd.merge(budg_cut, no_budg_cut, on = 'index', how='inner').rename(columns={'index':'Race'})
```


```python
race_dist
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Race</th>
      <th>Budget Cut</th>
      <th>No Budget Cut</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>American Indian/Alaska Native</td>
      <td>0.021995</td>
      <td>0.030712</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Asian</td>
      <td>0.018803</td>
      <td>0.026263</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Hispanic</td>
      <td>0.122228</td>
      <td>0.199912</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Black</td>
      <td>0.105066</td>
      <td>0.142116</td>
    </tr>
    <tr>
      <th>4</th>
      <td>White</td>
      <td>0.701833</td>
      <td>0.566853</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Hawaiian Native / Pacific Islander</td>
      <td>0.001092</td>
      <td>0.001833</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Two or More Races</td>
      <td>0.028778</td>
      <td>0.032018</td>
    </tr>
  </tbody>
</table>
</div>




```python
race_dist.plot(x="Race", y=["Budget Cut", "No Budget Cut"], kind="bar", figsize=(15,7))
plt.xticks(rotation=15);
```


![png](https://github.com/ItsmeKumar/DS5500-HW4/blob/master/plots/output_15_0.png)


The distributions do have differences. White students are 23% higher in proportion in the schools that were selected for budget cuts compared to the proportion in no budget cuts. Students of all the other races have an opposite trend. The model do have some biasess included.

## Problem 3


```python
demographics = pd.read_csv('./ccd_lea_052_1516_w_1a_011717.csv', low_memory=False)
demographics['LEAID'] = demographics['LEAID'].astype(str)
disability = pd.read_csv('./ccd_lea_002089_1516_w_1a_011717.csv', low_memory=False)
disability['LEAID'] = disability['LEAID'].astype(str)
```


```python
disability = pd.merge(disability, demographics[['LEAID', 'TOTAL']], on='LEAID', how='inner').query('TOTAL>0 and SPECED>0')
disability['Proportion'] = disability['SPECED']/disability['TOTAL']
```


```python
disb_prop = pd.DataFrame(data={'Budget Cut': [None], 'No Budget Cut': [None]})
```


```python
disb_prop.loc[0] =  ([
    disability.loc[disability['LEAID'].isin(cut_rev['LEAID']), 'Proportion'].mean(), 
    disability.loc[~disability['LEAID'].isin(cut_rev['LEAID']), 'Proportion'].mean()
])
disb_prop = disb_prop.T.reset_index()
```


```python
bins = np.linspace(0, 1, 100)
plt.hist(disability.loc[disability['LEAID'].isin(cut_rev['LEAID']), 'Proportion'], bins, alpha=0.5, label='Budget Cut')
plt.hist(disability.loc[~disability['LEAID'].isin(cut_rev['LEAID']), 'Proportion'], bins, alpha=0.5, label='No Budget Cut')
plt.legend(loc='upper right')
plt.title('Proportion of disabled students')
plt.show();
```


![png](https://github.com/ItsmeKumar/DS5500-HW4/blob/master/plots/output_22_0.png)



```python
plt.bar(disb_prop['index'],disb_prop[0])
plt.title('Proportion of disabled students');
```


![png](https://github.com/ItsmeKumar/DS5500-HW4/blob/master/plots/output_23_0.png)


The budget cut is slightly higher in the school districts with low proportion of disabled students. But, the distributions still look similar and the hidden bias is not very significant.

## Problem 4

I chose to critique Tian Sang's HW3 solution for this problem. Link - https://github.com/stiangithub/HW3

Tian's solution is to cut 15% budget from all school districts. Instead of cutting same amount from each school district, Tian suggested to cut equal proportion of funding.

Although this method seems to be unbiased, I think there is an important thing that should have also been taken into account. For schools that have expenditure greater than revenue, cutting more revenue could push them into debts. May be cutting equal proportion of funding by selecting schools that are self sustainable could solve this problem.

## Problem 5

I chose to summarize and comment on Map Reduce and Hadoop lecture by Prof. Jan Vitek. A breif overview of the history of Map Reduce and Hadoop was informative. The lecture provided a good understanding of implementation of map and reduce sub routines which was followed up by some interesting examples. I got a better understanding of the basics of how parallelization is done is big data systems to efficiently process the information. Overall, the lecture has a very nice explanation of development and working of Map Reduce and Hadoop which I find would be very helpful to dig deep into domain. 
