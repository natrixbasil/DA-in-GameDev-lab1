# СБОР, ОБРАБОТКА И ВИЗУАЛИЗАЦИЯ ТЕСТОВОГО НАБОРА ДАННЫХ.
Отчет по лабораторной работе #2 выполнила:
- Грошева Василиса

Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | # | 20 |

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

## Цель работы
Познакомиться с программными средствами для организции передачи данных между инструментами google, Python и Unity

## Задание 1
### Реализовать совместную работу и передачу данных в связке Python - Google-Sheets – Unity.

- В облачном сервисе google console подключить API для работы с google
sheets и google drive.

![image](https://user-images.githubusercontent.com/114161306/193556342-6ca66832-e8f4-4c6e-8a94-9bf4369fd958.png)

- Реализовать запись данных из скрипта на python в google-таблицу. Данные описывают изменение темпа инфляции на протяжении 11 отсчётных периодов, с учётом стоимости игрового объекта в каждый период.

```py

import gspread
import numpy as np
gc = gspread.service_account(filename = 'unitydatascience-364407-1e8e0d2ba7b6.json')
sh = gc.open('UnitySheets')
price = np.random.randint(2000, 10000, 11)
mon = list(range(1, 11))
i = 0
while i <= len(mon):
    i += 1
    if i ==0:
        continue
    else:
        tempInf = ((price[i-1] - price[i-2])/price[i-2])*100
        tempInf = str(tempInf)
        tempInf = tempInf.replace('.', ',')
        sh.sheet1.update(('A' + str(i)), str(i))
        sh.sheet1.update(('B' + str(i)), str(price[i-1]))
        sh.sheet1.update(('C' + str(i)), str(tempInf))
        print(tempInf)
	
```

![image](https://user-images.githubusercontent.com/114161306/193556711-8247b972-d744-48b3-ad77-5e64ce88a4e3.png)

- Создать новый проект на Unity, который будет получать данные из google- таблицы, в которую были записаны данные в предыдущем пункте.

![image](https://user-images.githubusercontent.com/114161306/193556831-6a6d1bfc-7876-439a-9781-2c7f1e922c39.png)

- Написать функционал на Unity, в котором будет воспризводиться аудиофайл в зависимости от значения данных из таблицы.

```py
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;

public class NewBehaviourScript : MonoBehaviour
{
    public AudioClip goodSpeak;
    public AudioClip NormalSpeak;
    public AudioClip BadSpeak;
    private AudioSource selectAudio;
    private Dictionary<string, float> dataSet = new Dictionary<string, float>();
    private bool statusStart = false;
    private int i = 1;


    // Start is called before the first frame update
    void Start()
    {
        StartCoroutine(GoogleSheets());
    }

    // Update is called once per frame
    void Update()
    {
        if (dataSet["Mon_" + i.ToString()] <= 10 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioGood());
            Debug.Log(dataSet["Mon_" + i.ToString()]);

        }

        if (dataSet["Mon_" + i.ToString()] > 10 & dataSet["Mon_" + i.ToString()] < 100 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioNormal());
            Debug.Log(dataSet["Mon_" + i.ToString()]);

        }

        if (dataSet["Mon_" + i.ToString()] >= 100 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["Mon_" + i.ToString()]);

        }
    }
    IEnumerator GoogleSheets()
    {
        UnityWebRequest currentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/1BdbYueYifmwDBXVr4h3hncpUCdqAm6DEIZ3IRE9Dlxs/values/Лист1?key=AIzaSyByJWeuTixcyKA6zx3eoru4HqQqvf9ptpY");
        yield return currentResp.SendWebRequest();
        string rawResp = currentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            var parseJson = JSON.Parse(itemRawJson.ToString());
            var selectRow = parseJson[0].AsStringList;
            dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[2]));

        }
    }
    
    IEnumerator PlaySelectAudioGood()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = goodSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }

    IEnumerator PlaySelectAudioNormal()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = NormalSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }

    IEnumerator PlaySelectAudioBad()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = BadSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(4);
        statusStart = false;
        i++;
    }
}

```

![image](https://user-images.githubusercontent.com/114161306/193557272-9ed61d6c-92b1-4ddf-ad7c-cfc5e6d9b24a.png)


## Задание 2
Реализовать запись в Google-таблицу набора данных, полученных с помощью линейной регрессии из лабораторной работы № 1

```py
import gspread
import numpy as np
x = [3,21,22,34,54,34,55,67,89,99]
x = np.array(x)
y = [2,22,24,65,79,82,55,130,150,199]
y = np.array(y)

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

#Initialize parameters and display
a = np.random.rand(1)
print(a)
b = np.random.rand(1)
print(b)
Lr = 0.000001


a,b = iterate(a,b,x,y,5)
prediction=model(a,b,x)
loss = loss_function(a, b, x, y)
print(a,b,loss)

gc = gspread.service_account(filename = 'unitydatascience-364407-0c39f71b8174.json')
sh = gc.open_by_url('https://docs.google.com/spreadsheets/d/1oGfAHKsDBnTtgq7IYbe_dx7XzxREH-QNCT3Kk66ZAMo/edit#gid=0')
worksheet = sh.get_worksheet(0)

newa = str(a)
newa = newa.replace('.', ',')
newb = str(b)
newb = newb.replace('.', ',')
newloss = str(loss)
newloss = newloss.replace('.', ',')
worksheet.update('A1', str(newa))
worksheet.update('B1', str(newb))
worksheet.update('C1', str(newloss))
```
![image](https://user-images.githubusercontent.com/114161306/193571174-b6ecff5b-ca97-4d19-aba4-72b10e4be1c9.png)

## Задание 3


**BigDigital Team: Denisov | Fadeev | Panov**
