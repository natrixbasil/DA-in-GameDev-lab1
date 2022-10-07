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

- Сделать 3, 9, 27 копий модели «Плоскость-Сфера-Куб», запустить
симуляцию сцены и наблюдайть за результатом обучения модели.

![изображение](https://user-images.githubusercontent.com/114161306/194498949-52cbb29b-191d-43c7-9930-d99207c72f19.png)

- Проверить работу модели
- 
![изображение](https://user-images.githubusercontent.com/114161306/194499988-e3366e3d-5a86-4ff0-82f0-7fdeeeb0d268.png)

## Задание 2
### Подробно опишите каждую строку файла конфигурации нейронной сети, доступного в папке с файлами проекта по ссылке. Самостоятельно найдите информацию о компонентах Decision Requester, Behavior Parameters, добавленных сфере.

## Задание 3
### Доработайте сцену и обучите ML-Agent таким образом, чтобы шар перемещался между двумя кубами разного цвета. Кубы должны, как и впервом задании, случайно изменять кооринаты на плоскости. В выводах к работе дайте развернутый ответ, что такое игровой баланс и как системы машинного обучения могут быть использованы для того, чтобы его скорректировать.

## Выводы


## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
