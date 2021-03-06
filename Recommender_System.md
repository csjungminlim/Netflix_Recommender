
# Netflix Recommender System

추천 시스템(recommender system)이란 사용자(user)가 상품(item)에 대해 어떻게 평가하는지 예측하는 시스템의 일종이다. Netflix에서 제공한 netflix-prize-data의 경우 17770개의 영화에 대한 고객 2649429명 평점 정보가 저장되어있다. 이 정보를 가지고 사용자가 아직 평가하지 않은 영화에 대한 평점을 예측하는 것이 이번 프로젝트의 목표이다.

# Surprise 패키지

이번 프로젝트에서는 파이썬의 Surprise 패키지를 사용하여 추천 시스템을 구현해 본다. Surprise 패키지는 Nicola Hug가 만든 오픈 소스 추천 시스템 패키로 문서화가 잘 되어 있다.

- https://github.com/NicolasHug/Surprise

# 데이터

넷플릭스에서 제공하는 netflix-prize-data를 사용한다.
- https://www.kaggle.com/netflix-inc/netflix-prize-data


### Netflix-prize-data INFO

CustomerID,Rating,Date
    - MovieIDs range from 1 to 17770 sequentially.
    - CustomerIDs range from 1 to 2649429, with gaps. There are 480189 users.
    - Ratings are on a five star (integral) scale from 1 to 5.
    - Dates have the format YYYY-MM-DD.


```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import sys
import surprise
```

## 데이터 전처리

1. netflix-prize-data의 combined_data_1.txt를 오픈
2. surprise 패키지에 사용하기 위한 custom_data_file 생성


```python
data_file = open("/Users/limjungmin/Netflix_Recommender/netflix-prize-data/combined_data_1.txt")
# combined_data_1.txt에는 4499개의 movieID가 저장되어있음.

custom_data_file = open("/Users/limjungmin/Netflix_Recommender/u.data", 'w')
```

3. 원본데이터의 경우
    
    movieID:
    userID, Rank, Date 의 형태로 저장되어있다.
   
   
4. Surprise 패키지를 사용하기위해 dataset을 custom 해줘야하는데

    userID; movieID; Rank 의 형태로 파일을 수정해야한다.
   
    
   


```python
cnt = 0
for line in data_file:

    if ":" in line:
        movieID = line.split(":")[0]
        #print(movieID)
        cnt+=1
    else :
        
        info = line.split(",")

        userID = info[0]
        rating = info[1]
        date = info[2].split('\n')[0]

    str = userID + ";" + movieID + ";" + rating + "\r\n"
    custom_data_file.write(str)
    
    if cnt > 50 : break
    
print("Done")
```

    Done


## Use a custom dataset
참고: https://surprise.readthedocs.io/en/latest/getting_started.html#use-a-custom-dataset


```python
reader = surprise.Reader(line_format='user item rating', sep=';')
```


```python
data = surprise.Dataset.load_from_file('/Users/limjungmin/Netflix_Recommender/u.data', reader=reader)
```


```python
df = pd.DataFrame(data.raw_ratings, columns=["user", "item", "rate", "id"])
del df["id"]
```

- 포맷 변경후 dataframe으로 변환하여 저장 후 출력 (테스트를 위해 50개의 영화를 대상으로만 진행)


```python
df
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>user</th>
      <th>item</th>
      <th>rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>                                              ...</td>
      <td>4488</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1136431</td>
      <td>4488</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1183532</td>
      <td>4488</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>29551</td>
      <td>4488</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>848132</td>
      <td>4488</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>1707646</td>
      <td>4488</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>538012</td>
      <td>4488</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>921585</td>
      <td>4488</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>1290993</td>
      <td>4488</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>2466472</td>
      <td>4488</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>537632</td>
      <td>4488</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>11</th>
      <td>2519213</td>
      <td>4488</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>12</th>
      <td>1130752</td>
      <td>4488</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>13</th>
      <td>1373504</td>
      <td>4488</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>14</th>
      <td>2073563</td>
      <td>4488</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>15</th>
      <td>1954618</td>
      <td>4488</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>16</th>
      <td>575714</td>
      <td>4488</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>17</th>
      <td>1122282</td>
      <td>4488</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>18</th>
      <td>21777</td>
      <td>4488</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>19</th>
      <td>2648873</td>
      <td>4488</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>20</th>
      <td>954567</td>
      <td>4488</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>21</th>
      <td>1328707</td>
      <td>4488</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>22</th>
      <td>803824</td>
      <td>4488</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>23</th>
      <td>521315</td>
      <td>4488</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>24</th>
      <td>1537569</td>
      <td>4488</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>25</th>
      <td>1628478</td>
      <td>4488</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>26</th>
      <td>1211591</td>
      <td>4488</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>27</th>
      <td>1155967</td>
      <td>4488</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>28</th>
      <td>1480209</td>
      <td>4488</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>29</th>
      <td>437239</td>
      <td>4488</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>43439</th>
      <td>1836014</td>
      <td>4499</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>43440</th>
      <td>1960927</td>
      <td>4499</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>43441</th>
      <td>2385226</td>
      <td>4499</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>43442</th>
      <td>1997469</td>
      <td>4499</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>43443</th>
      <td>811530</td>
      <td>4499</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>43444</th>
      <td>1083336</td>
      <td>4499</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>43445</th>
      <td>1309223</td>
      <td>4499</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>43446</th>
      <td>604335</td>
      <td>4499</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>43447</th>
      <td>307404</td>
      <td>4499</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>43448</th>
      <td>1334851</td>
      <td>4499</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>43449</th>
      <td>1061120</td>
      <td>4499</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>43450</th>
      <td>1852040</td>
      <td>4499</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>43451</th>
      <td>268846</td>
      <td>4499</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>43452</th>
      <td>2368103</td>
      <td>4499</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>43453</th>
      <td>529787</td>
      <td>4499</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>43454</th>
      <td>441248</td>
      <td>4499</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>43455</th>
      <td>2092745</td>
      <td>4499</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>43456</th>
      <td>555962</td>
      <td>4499</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>43457</th>
      <td>303969</td>
      <td>4499</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>43458</th>
      <td>654591</td>
      <td>4499</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>43459</th>
      <td>272857</td>
      <td>4499</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>43460</th>
      <td>185372</td>
      <td>4499</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>43461</th>
      <td>2219917</td>
      <td>4499</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>43462</th>
      <td>1796454</td>
      <td>4499</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>43463</th>
      <td>2562830</td>
      <td>4499</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>43464</th>
      <td>2591364</td>
      <td>4499</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>43465</th>
      <td>1791000</td>
      <td>4499</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>43466</th>
      <td>512536</td>
      <td>4499</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>43467</th>
      <td>988963</td>
      <td>4499</td>
      <td>3.0</td>
    </tr>
    <tr>
      <th>43468</th>
      <td>1704416</td>
      <td>4499</td>
      <td>3.0</td>
    </tr>
  </tbody>
</table>
<p>43469 rows × 3 columns</p>
</div>



## 평가 

- Surprise 패키지의 cross_validate, KFold를 통해 훈련셋, 테스트셋을 나누어 예측값과 실제값의 비교를 간편히 진행할 수 있다.
- 훈련에 사용한 알고리즘은 netflix prize에서 높은 성능을 보였던 SVD를 사용.


```python
from surprise.model_selection import cross_validate
from surprise.model_selection import KFold
from surprise import SVD
from surprise import accuracy
```


```python
kf = KFold(n_splits=3)
```


```python
algo = SVD()
```

# 결과
- cross_validate 사용


```python
cross_validate(algo, data, measures=['RMSE', 'MAE'], cv=5, verbose=True)
```

    Evaluating RMSE, MAE of algorithm SVD on 5 split(s).
    
                      Fold 1  Fold 2  Fold 3  Fold 4  Fold 5  Mean    Std     
    RMSE (testset)    1.0543  1.0468  1.0464  1.0494  1.0276  1.0449  0.0091  
    MAE (testset)     0.8551  0.8439  0.8511  0.8460  0.8313  0.8455  0.0081  
    Fit time          2.92    2.75    2.85    2.96    3.09    2.91    0.11    
    Test time         1.61    0.08    0.06    0.07    0.07    0.38    0.62    





    {'fit_time': (2.9174020290374756,
      2.753688097000122,
      2.848954916000366,
      2.9645349979400635,
      3.0852890014648438),
     'test_mae': array([0.85509719, 0.8439233 , 0.85108494, 0.84597611, 0.8312547 ]),
     'test_rmse': array([1.0542821 , 1.04677041, 1.0463717 , 1.04939036, 1.02764535]),
     'test_time': (1.6091828346252441,
      0.07680201530456543,
      0.0644388198852539,
      0.06969308853149414,
      0.06683707237243652)}



- KFold 기법 사용


```python
for trainset, testset in kf.split(data):
    
    # train and test algorithm.
    algo.fit(trainset)
    predictions = algo.test(testset)

    # Compute and print Root Mean Squared Error
    accuracy.rmse(predictions, verbose=True)
```

    RMSE: 1.0490
    RMSE: 1.0451
    RMSE: 1.0401


## 보완해야 할 사항

- 전체 데이터 전처리에는 시간소요가 많이 걸리지 않아 기존 병훈이의 데이터 전처리에 비해 월등히 빠름
- 하지만, 현재 movieID 50개를 기준으로만 테스트해본 이유는 전체데이터를 대상으로 할 경우 알고리즘 학습에 많은 시간 소요
- 훈련셋과 테스트셋을 시각화 하여 결측치를 어떤 값으로 예측했지는 좀 더 명확하게 판단하기 위한 모듈 보완
- 더 많은 알고리즘으로 테스트 필요
