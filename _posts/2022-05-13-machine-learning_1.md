---
layout : single
title : '머신러닝 공부 시작'
---

```python
from tensorflow.keras.models import Model
from tensorflow.keras.layers import Input, Dense
from tensorflow.keras.optimizers import Adam, SGD
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
from sklearn.preprocessing import OneHotEncoder
```


### 1. 선형 회귀
model을 선형으로 가정하고 푸는 regression 문제.

데이터 전처리
```python
x_data = np.array(df['YearsExperience'], dtype=np.float32)
y_data = np.array(df['Salary'], dtype=np.float32)

print(x_data.shape)
print(y_data.shape)
```
nparray의 32비트 float으로 정리

```python
x_data = x_data.reshape((-1, 1))
y_data = y_data.reshape((-1, 1))
```
모양도 정리.

```python
x_train, x_val, y_train, y_val = train_test_split(x_data, y_data, test_size=0.2, random_state=1234)
```
트레이닝셋과 테스트셋을 나눔. (train, validation, test 세가지로 나눌 경우는 어떻게?)

```python
from tensorflow.keras.optimizers import RMSprop
from tensorflow.keras.optimizers import SGD
model = Sequential([Dense(1)])

model.compile(loss='mean_absolute_error', optimizer=SGD(learning_rate=0.1))

model.fit(x_train, y_train, validation_data=(x_val, y_val), epochs=1000, verbose=0)
model.fit(x_train, y_train, validation_data=(x_val, y_val), epochs=10)
```
Sequential 을 이용한 Fully connected node? 생성.
compile시 사용할 loss, optimizer 설정. 

