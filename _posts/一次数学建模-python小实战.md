---
title: 一次数学建模-python小实战
date: 2018-06-30 20:17:18
tags:
---
其实这只是本学期在 anu 数据科学入门课程最后一次作业的大题的详解。背景如下，我们要组建一只棒球队，给定了预算，所有球员的所有信息（包括，姓名，年龄，球队里的位置等基本信息以及每名球员的评分），要求在预算范围内组建一只总评分最高的球队，且每个位置有相应的数量要求。下面就是所有球员的一个缩影，
<!--more-->

|playerID  |POS |salary|OPW|
|:---------|:--:|:----:|:-:|
|abreubo01 |OF|9000000.0|122.452634|
|abreujo02 |1B|8666000.0|80.483563|

其中 C, 1B, 2B, 3B 和SS各要求有一个，OF有4个；最后一列OPW为该球员评分

## 解决方法

作为一名大学前两年在西工大呆过且禁受过五一数模节洗礼的人来说，这个问题可以很好地用数学建模的思想来解决。首先，又一个需要最大化的函数，又有若干约束，其中包括资金约束，以及各个位置人员数量的限制。这很符合线性规划的思想，可以建立如下模型。

其中，目标方程定义如下：

$$f(x_i) = max(\frac{1}{9} \sum_{i=1}^{length(k)} OPW(x_i)*x_i)$$

其中，

$$S=\\{ \\{ x: POS(x) = 1B\\},\\{ x: POS(x) = 2B\\},\\{ x: POS(x) = 3B\\}, \\{ x: POS(x) = C\\},\\{ x: POS(x) = OF\\}, \\{ x: POS(x) = SS\\} \\}$$

其中$POS(x)$为队员$x$所处位置，$length(k)$为每个子集$k$的长度，$OPW(x)$为队员$x$的评分

对于目标方程中每一个$x_i$的取值范围定义如下,

$$
x_i =
\begin{cases}
0, & \text{The player i is not selected}\\\\
1  & \text{The player i is selected}
\end{cases}
$$

Markdown 的数学公式里貌似不能加中文，所以用英文表示一下，应该能看懂哈。主体思想其实是线性规划中的 0-1 规划，所以取值范围为$0$ 或 $1$。

约束方程如下：

$$
\begin{cases}
\sum_{i=1}^{length(1B)}x_i=1 & \text{There should be one 1B} \\\\
\sum_{i=1}^{length(2B)}x_i=1 & \text{There should be one 2B} \\\\
\sum_{i=1}^{length(3B)}x_i=1 & \text{There should be one 3B} \\\\
\sum_{i=1}^{length(C)}x_i=1 & \text{There should be one C} \\\\
\sum_{i=1}^{length(OF)}x_i=4 & \text{There should be four OF} \\\\
\sum_{i=1}^{length(SS)}x_i=1 & \text{There should be one SS} \\\\
\sum_{k \in S} \sum_{i=1}^{length(k)}salary(x_i)*x_i \leq 25 & \text{The total salary is less than 25 million} \\\\
\end{cases}
$$

## 代码实现

根据以上数学基础，我们可以写出如下代码，其中用到了 scipy 这个库的 lingprog 做线性规划

```python
from scipy.optimize import linprog
players = playerLS.drop(columns=['minYear','maxYear','1B','2B','3B','HR','BB','nameFirst','nameLast'])
players['salary'] = players['salary']/1000000
players = players.sort_values('POS').reset_index().drop(columns=['index'])

# start and end indexes of 1B
start_1B = players[players['POS'] == '1B'].first_valid_index()
end_1B = players[players['POS'] == '1B'].last_valid_index()

# start and end indexes of 2B
start_2B = players[players['POS'] == '2B'].first_valid_index()
end_2B = players[players['POS'] == '2B'].last_valid_index()

# start and end indexes of 3B
start_3B = players[players['POS'] == '3B'].first_valid_index()
end_3B = players[players['POS'] == '3B'].last_valid_index()

# start and end indexes of C
start_C = players[players['POS'] == 'C'].first_valid_index()
end_C = players[players['POS'] == 'C'].last_valid_index()

# start and end indexes of OF
start_OF = players[players['POS'] == 'OF'].first_valid_index()
end_OF = players[players['POS'] == 'OF'].last_valid_index()

# start and end indexes of SS
start_SS = players[players['POS'] == 'SS'].first_valid_index()
end_SS = players[players['POS'] == 'SS'].last_valid_index()

#initialize coficients
cofficients = np.zeros_like(np.arange(len(players), dtype=float))
#initialize constraints
constraint_1B = np.zeros_like(np.arange(len(players), dtype=float))
constraint_2B = np.zeros_like(np.arange(len(players), dtype=float))
constraint_3B = np.zeros_like(np.arange(len(players), dtype=float))
constraint_C = np.zeros_like(np.arange(len(players), dtype=float))
constraint_OF = np.zeros_like(np.arange(len(players), dtype=float))
constraint_SS = np.zeros_like(np.arange(len(players), dtype=float))
constraint_salary = np.zeros_like(np.arange(len(players), dtype=float))
#value of constraints
constraints_value_eq = [1,1,1,1,4,1]
constraints_value_ub = [25]
#bounds -> for each x in the math model, the x is either 0 or 1
bounds = []

for i, row in players.iterrows():
    #for 1B
    if i >= start_1B and i <= end_1B:
        constraint_1B[i] = 1
    #for 2B
    if i >= start_2B and i <= end_2B:
        constraint_2B[i] = 1
    #for 3B
    if i >= start_3B and i <= end_3B:
        constraint_3B[i] = 1
    #for C
    if i >= start_C and i <= end_C:
        constraint_C[i] = 1
    #for OF
    if i >= start_OF and i <= end_OF:
        constraint_OF[i] = 1
    #for SS only 1 people
    if i >= start_SS and i <= end_SS:
        constraint_SS[i] = 1
    #for total salary
    constraint_salary[i] =row['salary']
    #for cofficients
    cofficients[i] = -(row['OPW'])
    # add new bounds
    bounds.append([0,1])
# This is our equility constraints - each position has its number of players
constraints_eq = [constraint_1B, constraint_2B, constraint_3B, constraint_C, constraint_OF, constraint_SS]
# This is our upper bounds constraints - salary <= 25 million
constraints_ub = [constraint_salary]
# let us do the linear programing
res = linprog(cofficients, A_ub=constraints_ub, b_ub=constraints_value_ub, A_eq=constraints_eq, b_eq=constraints_value_eq, bounds=bounds, options={"disp": True, 'maxiter': 20000000000})
results = res.x
team = {'playerID':[]}
salary = 0
total_opw = 0
# since the linear programming provided by scipy cannot do integer programing
# we need to get all element which is larger than 0.5
for i in range(0, len(results)):
    if results[i] > 0.5:
        salary += players.iloc[i]['salary']
        team['playerID'].append(players.iloc[i]['playerID'])
        salary += players.iloc[i]['salary']
        total_opw += players.iloc[i]['OPW']
team = pd.DataFrame(team)
team = pd.merge(team, playerLS, how='inner', on='playerID')
print('The average of OPW is', total_opw/9)
team
```
