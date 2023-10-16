# Прогнозирование спроса товаров для “Лента”
Разработка алгоритма прогноза спроса на 14 дней для товаров собственного производства 

## Команда №1 "In Flames"
Проект DS разработан командой:

- **Амина Аджиева**
- **Олеся Круглякова**
- **Сергей Бредис**

## Содержание репозитория
- [lenta-hackathon-demand-forecasting.ipynb](https://github.com/aminaadzhieva/lenta-hackathon-demand-forecasting/blob/develop/lenta-hackathon-demand-forecasting%20(8).ipynb) - EDA, обучение модели, предсказание 
- [submission.csv](https://github.com/aminaadzhieva/lenta-hackathon-demand-forecasting/blob/develop/sales_submission.csv) - файл с предсказаниями
- `lenta`:
  - `ml` - [скрипт предсказания](https://github.com/aminaadzhieva/lenta-hackathon-demand-forecasting/blob/develop/lenta/ml/model.py), скрипт переобучения модели, [обученная модель](https://github.com/aminaadzhieva/lenta-hackathon-demand-forecasting/blob/develop/lenta/ml/lgbm_model.pkl)
  - `backend` - [скрипт для запуска модели](https://github.com/aminaadzhieva/lenta-hackathon-demand-forecasting/blob/develop/lenta/backend/app.py)

## Описание решения
- Выбрана модель градиентного бустинга LightGBM
- Дополнителльно сгенерированы признаки:
    - календарные: день недели, число месяца, номер недели
    - лаговые признаки 1-30 дней
    - скользящее среднее 7 предыдущих дней
    - кластеризация по характеристикам магазина и товаров
- Обучение, валидация и выбор лучшего набора гиперпараметров проводится на кросс-валидации: подбор гиперпараметров на фолде проводится на valid-выборке, оценка лучшей модели на фолде на test-выборке. Далее выбирается модель лучших моделей на каждом фолде.
- Предсказание спроса обученной моделью делается последовательно на каждый следующий день с промежуточным перерасчётом лаговых признаков (учитывается предсказанное значение спроса в предыдущий день).

Для оценки модели использовалась метрика качества  **WAPE**, посчитанный на уровне магазин, товар, день (гранулярность ТК-SKU-День).  
Полученный результат: 0,46

## Инструкция по запуску на локальной машине.

Клонировать репозиторий

```git clone git@github.com:aminaadzhieva/lenta-hackathon-demand-forecasting.git```

Установить виртуальное окружение

```python3.11 -m venv venv```

Запустить виртуальное окружение

```source venv/bin/activate```

Установить все зависимости

```pip install -r requirements.txt```

Запустить сервер

```uvicorn lenta.backend.app:app```

[Запустить систему предсказания](http://127.0.0.1:8000/api/v1/forecast/custom_response_post/)


[Проверить данные в админке](http://31.129.109.228/admin/)

## Взаимодействие 
- Скрипт предсказания запускается каждую ночь, генерирует предсказания для всех заданных комбинаций Магазин-Товар и записывает результат в базу данных. Пользователь взаимодействует с данными из базы данных.

  



