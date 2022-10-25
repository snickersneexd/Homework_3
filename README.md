# Основы работы с Unity
Отчет по лабораторной работе #3 выполнил(а):
- Валиев Константин Дмитриевич 
- РИ300022
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

- Задание 1.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 2.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 3.
- Ссылка на реферат по теме.
- Выводы.
- ✨Magic ✨

## Цель работы
Создание интерактивного приложения и изучение принципов интеграции в него игровых сервисов.

## Задание 1
### Используя видео-материалы практических работ 1-5 повторить реализацию игровых механик:
1. Практическая работа «Реализация механизма ловли объектов».
2. Практическая работа «Реализация графического интерфейса с добавлением счетчика очков».

Ход работы:

1) Создаем новый скрипт "EnergyShield" для управления щитом.
```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class EnergyShield : MonoBehaviour
{
    void Update()
    {
        Vector3 mousePos2D = Input.mousePosition;
        mousePos2D.z = -Camera.main.transform.position.z;
        Vector3 mousePos3D = Camera.main.ScreenToWorldPoint(mousePos2D);
        Vector3 pos = this.transform.position;
        pos.x = mousePos3D.x;
        this.transform.position = pos;
    }
}
```

Теперь наш энергетический щит движется по направлению мыши.
![Alt text](Images/1.png?raw=true "Title")
![Alt text](Images/2.png?raw=true "Title")
![Alt text](Images/3.png?raw=true "Title")

2) Реализуем ловлю объектов "DragonEgg", для этого добавляем в скрипт новый метод.
```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class EnergyShield : MonoBehaviour
{
    void Update()
    {
        Vector3 mousePos2D = Input.mousePosition;
        mousePos2D.z = -Camera.main.transform.position.z;
        Vector3 mousePos3D = Camera.main.ScreenToWorldPoint(mousePos2D);
        Vector3 pos = this.transform.position;
        pos.x = mousePos3D.x;
        this.transform.position = pos;
    }

        private void OnCollisionEnter(Collision coll)
    {
        GameObject Collided = coll.gameObject;
        if (Collided.tag == "Dragon Egg")
        {
            Destroy(Collided);
        }
}
```

Теперь драконье яйцо пропадает при пересечении с энергетическим щитом.
![Alt text](Images/4.png?raw=true "Title")
![Alt text](Images/5.png?raw=true "Title")

3) Добавляем элемент Canvas и Text, редактируем их под Main Camera для лучшего вида.
![Alt text](Images/Canvas.png?raw=true "Title")

4) Создаем счётчик очков, для этого нужно добавить новый метод в скрипт "EnergyShield".
```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using TMPro;

public class EnergyShield : MonoBehaviour
{
    public TextMeshProUGUI scoreGT;

    void Start()
    {
        GameObject scoreGO = GameObject.Find("Score");
        scoreGT = scoreGO.GetComponent<TextMeshProUGUI>();
        scoreGT.text = "0";
    }

    void Update()
    {
        Vector3 mousePos2D = Input.mousePosition;
        mousePos2D.z = -Camera.main.transform.position.z;
        Vector3 mousePos3D = Camera.main.ScreenToWorldPoint(mousePos2D);
        Vector3 pos = this.transform.position;
        pos.x = mousePos3D.x;
        this.transform.position = pos;
    }

    private void OnCollisionEnter(Collision coll)
    {
        GameObject Collided = coll.gameObject;
        if (Collided.tag == "Dragon Egg")
        {
            Destroy(Collided);
        }
        int score = int.Parse(scoreGT.text);
        score += 1;
        scoreGT.text = score.ToString();
    }
}
```

Проверяем, что наш счётчик работает корректно.
![Alt text](Images/6.png?raw=true "Title")
![Alt text](Images/7.png?raw=true "Title")
![Alt text](Images/8.png?raw=true "Title")

## Задание 2
### Используя видео-материалы практических работ 1-5 повторить реализацию игровых механик:
3. Практическая работа «Уменьшение жизни. Добавление текстур».
4. Практическая работа «Структурирование исходных файлов в папке».

Ход работы:

1) В скрипте "DragonPicker" добавим условие при котором игра будет перезапускаться.
```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.SceneManagement;

public class DragonPicker : MonoBehaviour
{
    public GameObject energyShieldPrefab;
    public int numEnergyShield = 3;
    public float energyShieldBottomY = -6f;
    public float energyShieldRadius = 1.5f;

    public List<GameObject> shieldList;

    void Start()
    {
        shieldList = new List<GameObject>();
        for (int i = 1; i <= numEnergyShield; i++)
        {
            GameObject tShieldGo = Instantiate<GameObject>(energyShieldPrefab);
            tShieldGo.transform.position = new Vector3(0, energyShieldBottomY, 0);
            tShieldGo.transform.localScale = new Vector3(1*i, 1*i, 1*i);
            shieldList.Add(tShieldGo);
        }
    }

    public void DragonEggDestroyed()
    {
        GameObject[] tDragonEggArray = GameObject.FindGameObjectsWithTag("Dragon Egg");
        foreach (GameObject tGO in tDragonEggArray)
        {
            Destroy(tGO);
        }
        int shieldIndex = shieldList.Count - 1;
        GameObject tShieldGo = shieldList[shieldIndex];
        shieldList.RemoveAt(shieldIndex);
        Destroy(tShieldGo);
        if (shieldList.Count == 0)
        {
            SceneManager.LoadScene("_0Scene");
        }
    }
}
```

Радиус щита уменьшается с каждым промахом, если потерять все три щита - игра начнется сначала.
![Alt text](Images/9.png?raw=true "Title")
![Alt text](Images/10.png?raw=true "Title")
![Alt text](Images/11.png?raw=true "Title")

2) Добавим на сцену префаб горы и отредактируем его.
![Alt text](Images/12.png?raw=true "Title")

3) Также добавляем Skybox.
![Alt text](Images/13.png?raw=true "Title")

4) Выполним структурирование исходных файлов.
Для этого рассортируем все нужные элементы по папкам и удалим лишнее.
![Alt text](Images/14.png?raw=true "Title")

## Задание 3
### Используя видео-материалы практических работ 1-5 повторить реализацию игровых механик:
5. Практическая работа «Интеграция игровых сервисов в готовое приложение».

Ход работы:

1) Импортируем плагин от Яндекс.Игр для корректной инициализации Yandex.SDK
![Alt text](Images/15.png?raw=true "Title")

2) Добавляем на сцену элемент "YandexGame".

![Alt text](Images/16.png?raw=true "Title")

2) Архивируем билд нашей игры в zip-формат и загружаем в Яндекс.Консоль
![Alt text](Images/17.png?raw=true "Title")

3) После проверки нашего архива можно перейти на черновик и убедиться, что все работает корректно.
![Alt text](Images/18.jpg?raw=true "Title")

## Выводы

Во время выполнения данной лабораторной работы у меня получилось реализовать интеграцию интерфейса пользователя в разрабатываемое интерактивное приложение.
