# СБОР, ОБРАБОТКА И ВИЗУАЛИЗАЦИЯ ТЕСТОВОГО
НАБОРА ДАННЫХ.
Отчет по лабораторной работе #2 выполнила:
- Грошева Василиса

Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | # | 60 |
| Задание 2 | # | 20 |
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

**BigDigital Team: Denisov | Fadeev | Panov**
