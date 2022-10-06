# РАЗРАБОТКА СИСТЕМЫ МАШИННОГО ОБУЧЕНИЯ
Отчет по лабораторной работе #1 выполнила:
- Грошева Василиса

Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | * | 20 |

знак "*" - задание выполнено; знак "#" - задание не выполнено;

Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

Структура отчета

- Данные о работе: название работы, фио, группа, выполненные задания.
- Цель работы.
- Задание 1.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 2.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 3.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Выводы.
- ✨Magic ✨

## Цель работы
Познакомиться с программными средствами для создания системы машинного обучения и ее интеграции в Unity.

## Постановка задачи
В данной лабораторной работе мы создадим ML-агент и будем тренировать нейросеть, задача которой будет заключаться в управлении шаром. Задача шара заключается в том, чтобы оставаясь на плоскости находить кубик, смещающийся в заданном случайном диапазоне координат.
## Задание 1
Реализовать систему машинного обучения в связке Python - Google-Sheets – Unity.
- Создать новый пустой 3D проект на Unity
![изображение](https://user-images.githubusercontent.com/114161306/194391492-b1733c7a-39c5-4539-a6a1-6571256e0831.png)
- Скачать папку с ML-агентом
![изображение](https://user-images.githubusercontent.com/114161306/194391706-c91dcc22-a728-4664-96a4-1d59ee15b54d.png)

- Добавить ML-агент в проект
![изображение](https://user-images.githubusercontent.com/114161306/194392076-2459b487-f79e-4237-877c-f6b7bb651e07.png)

- Написать серию команд для создания и активации нового ML-агента, а также для скачивания необходимых библиотек в Anaconda Prompt

```py
(base) C:\windows\system32>create -n MlAgent python=3.6.13
```

```py
(base) C:\windows\system32>conda create -n MlAgent python=3.6.13
```

```py
(base) C:\windows\system32>conda activate MLAgent
```

```py
(MLAgent) C:\windows\system32>pip install torch~=1.7.1 -f https://download.pytorch.org/whl/torch_stable.html
```

```py
(MLAgent) C:\windows\system32>cd C:\Users\189\My project
```
- Создать на сцене плостость, куб и сферу, добавить к ним простой скрипт
![изображение](https://user-images.githubusercontent.com/114161306/194393232-6d7f3b4f-72fc-4fb6-8882-c73ae7871c38.png)

- Добавить код в скрипт-файл:
```py
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Unity.MLAgents;
using Unity.MLAgents.Sensors;
using Unity.MLAgents.Actuators;

public class RollerAgent : Agent
{
    Rigidbody rBody;
    // Start is called before the first frame update
    void Start()
    {
        rBody = GetComponent<Rigidbody>();
    }

    public Transform Target;
    public override void OnEpisodeBegin()
    {
        if (this.transform.localPosition.y < 0)
        {
            this.rBody.angularVelocity = Vector3.zero;
            this.rBody.velocity = Vector3.zero;
            this.transform.localPosition = new Vector3(0, 0.5f, 0);
        }

        Target.localPosition = new Vector3(Random.value * 8-4, 0.5f, Random.value * 8-4);
    }
    public override void CollectObservations(VectorSensor sensor)
    {
        sensor.AddObservation(Target.localPosition);
        sensor.AddObservation(this.transform.localPosition);
        sensor.AddObservation(rBody.velocity.x);
        sensor.AddObservation(rBody.velocity.z);
    }
    public float forceMultiplier = 10;
    public override void OnActionReceived(ActionBuffers actionBuffers)
    {
        Vector3 controlSignal = Vector3.zero;
        controlSignal.x = actionBuffers.ContinuousActions[0];
        controlSignal.z = actionBuffers.ContinuousActions[1];
        rBody.AddForce(controlSignal * forceMultiplier);

        float distanceToTarget = Vector3.Distance(this.transform.localPosition, Target.localPosition);

        if(distanceToTarget < 1.42f)
        {
            SetReward(1.0f);
            EndEpisode();
        }
        else if (this.transform.localPosition.y < 0)
        {
            EndEpisode();
        }
    }
}

```

- Добавить и настроить компоненты объекта "Шар"

![изображение](https://user-images.githubusercontent.com/114161306/194394406-39785563-862c-4614-8d1a-644497cc999d.png)

- В корень проекта добавить файл конфигурации нейронной сети

![изображение](https://user-images.githubusercontent.com/114161306/194394592-35ea9b3a-c857-4f84-8185-4baaa9f1bb71.png)

- Запустить ML-агента
```py
(MLAgent) C:\Users\189\My project>mlagents-learn rollerball_config.yaml --run-id=RollerBall --resume
```



## Задание 2
### Поставить unity для создания игровых сцен и научиться писать hello world (доп. задание на 20 баллов)
![image](https://user-images.githubusercontent.com/114161306/192729603-15934150-495a-4e0d-b259-5d35a4eeb1ff.png)
![image](https://user-images.githubusercontent.com/114161306/192729843-e847353c-e5fc-4ad2-8893-90b433cf922b.png)


## Задание 3
### Поработать с кодом, самостоятельно поразбираться с кодом на пайтоне (доп. задание на 20 баллов)
Ход работы:
- Произвести подготовку данных для работы с алгоритмом линейной регрессии. 10 видов данных были установлены случайным образом, и данные находились в линейной зависимости. Данные преобразуются в формат массива, чтобы их можно было вычислить напрямую при использовании умножения и сложения.

```py

In [ ]:
#Import the required modules, numpy for calculation, and Matplotlib for drawing
import numpy as np
import matplotlib.pyplot as plt
#This code is for jupyter Notebook only
%matplotlib inline

# define data, and change list to array
x = [3,21,22,34,54,34,55,67,89,99]
x = np.array(x)
y = [2,22,24,65,79,82,55,130,150,199]
y = np.array(y)

#Show the effect of a scatter plot
plt.scatter(x,y)

```

![image](https://user-images.githubusercontent.com/114161306/191946018-d0d7afe2-ce23-4e44-b003-cbf425161cd6.png)

- Определите связанные функции. Функция модели: определяет модель линейной регрессии wx+b. Функция потерь: функция потерь среднеквадратичной ошибки. Функция оптимизации: метод градиентного спуска для нахождения частных производных w и b.


```py
#The basic linear regression model is wx+ b, and since this is a two-dimensional space, the model is ax+ b
def model(a, b, x):
    return a*x + b
#Tahe most commonly used loss function of linear regression model is the loss function of mean variance difference
def loss_function(a, b, x, y):
    num = len(x)
    prediction=model(a,b,x)
    return (0.5/num) * (np.square(prediction-y)).sum()
#The optimization function mainly USES partial derivatives to update two parameters a and b
def optimize(a,b,x,y):
    num = len(x)
    prediction = model(a,b,x)
    #Update the values of A and B by finding the partial derivatives of the loss function on a and b
    da = (1.0/num) * ((prediction -y)*x).sum()
    db = (1.0/num) * ((prediction -y).sum())
    a = a - Lr*da
    b = b - Lr*db
    return a, b
#iterated function, return a and b
def iterate(a,b,x,y,times):
    for i in range(times):
        a,b = optimize(a,b,x,y)
    return a,b
    
```
- Начать итерацию: шаг 1 Инициализация и модель итеративной оптимизации
```py

    #Initialize parameters and display
a = np.random.rand(1)
print(a)
b = np.random.rand(1)
print(b)
Lr = 0.000001
#For the first iteration, the parameter values, losses, and visualization after the iteration are displayed
a,b = iterate(a,b,x,y,1)
prediction=model(a,b,x)
loss = loss_function(a, b, x, y)
print(a,b,loss)
plt.scatter(x,y)
plt.plot(x,prediction)

```

![image](https://user-images.githubusercontent.com/114161306/191947829-3d1dfb7c-4c36-441c-ab0b-2f09a0841465.png)

Шаг 2 На второй итерации отображаются значения параметров, значения
потерь и эффекты визуализации после итерации

```py
a,b = iterate(a,b,x,y,2)
prediction=model(a,b,x)
loss = loss_function(a, b, x, y)
print(a,b,loss)
plt.scatter(x,y)
plt.plot(x,prediction)

```
![image](https://user-images.githubusercontent.com/114161306/191947885-54e06c0a-058d-4043-97a6-a755aa54842c.png)

Шаг 3 Третья итерация показывает значения параметров, значения потерь и
визуализацию после итерации

```py
a,b = iterate(a,b,x,y,3)
prediction=model(a,b,x)
loss = loss_function(a, b, x, y)
print(a,b,loss)
plt.scatter(x,y)
plt.plot(x,prediction)

```
![image](https://user-images.githubusercontent.com/114161306/191947935-c20a4689-82dc-46e5-97e9-3d99e8840c5b.png)

Шаг 4 На четвертой итерации отображаются значения параметров, значения
потерь и эффекты визуализации

```py
a,b = iterate(a,b,x,y,4)
prediction=model(a,b,x)
loss = loss_function(a, b, x, y)
print(a,b,loss)
plt.scatter(x,y)
plt.plot(x,prediction)

```

![image](https://user-images.githubusercontent.com/114161306/191947979-30996e7a-f95c-442e-a72b-5f80d6f6271f.png)

Шаг 5 Пятая итерация показывает значение параметра, значение потерь и
эффект визуализации после итерации

```py
a,b = iterate(a,b,x,y,5)
prediction=model(a,b,x)
loss = loss_function(a, b, x, y)
print(a,b,loss)
plt.scatter(x,y)
plt.plot(x,prediction)

```

![image](https://user-images.githubusercontent.com/114161306/191948009-b0745879-a8d0-4b27-934c-bca76e48e38f.png)

Шаг 6 10000-я итерация, показывающая значения параметров, потери и
визуализацию после итерации

```py
a,b = iterate(a,b,x,y,10000)
prediction=model(a,b,x)
loss = loss_function(a, b, x, y)
print(a,b,loss)
plt.scatter(x,y)
plt.plot(x,prediction)

```

![image](https://user-images.githubusercontent.com/114161306/191948037-767978d6-dd02-440b-b164-263dc7e405b7.png)


## Задание 2
### 

- Перечисленные в этом туториале действия могут быть выполнены запуском на исполнение скрипт-файла, доступного [в репозитории](https://github.com/Den1sovDm1triy/hfss-scripting/blob/main/ScreatingSphereInAEDT.py).
- Для запуска скрипт-файла откройте Ansys Electronics Desktop. Перейдите во вкладку [Automation] - [Run Script] - [Выберите файл с именем ScreatingSphereInAEDT.py из репозитория].

```py

import ScriptEnv
ScriptEnv.Initialize("Ansoft.ElectronicsDesktop")
oDesktop.RestoreWindow()
oProject = oDesktop.NewProject()
oProject.Rename("C:/Users/denisov.dv/Documents/Ansoft/SphereDIffraction.aedt", True)
oProject.InsertDesign("HFSS", "HFSSDesign1", "HFSS Terminal Network", "")
oDesign = oProject.SetActiveDesign("HFSSDesign1")
oEditor = oDesign.SetActiveEditor("3D Modeler")
oEditor.CreateSphere(
	[
		"NAME:SphereParameters",
		"XCenter:="		, "0mm",
		"YCenter:="		, "0mm",
		"ZCenter:="		, "0mm",
		"Radius:="		, "1.0770329614269mm"
	], 
)

```
В ходе этой лабораторной работы я написала базовые программы hello World на Python и Unity, а также использовала код для запуска модели линейной регрессии, что (как нам потом объяснили) является базовой задачей в дата-анализе. И слава богу.

## Выводы
###

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
