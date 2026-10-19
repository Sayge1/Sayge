`import matplotlib.pyplot as plt`
`plt.show() # Вывод изображения`
`plt.title("Название графика") # Установка заголовка графика`
`plt.xlabel("Значения оси Х") # Установка заголовка оси X`
`plt.ylabel("Значения оси Y") # Установка заголовка оси Y`
`plt.grid(True) # Включаем сетку`
`plt.plot([1, 2, 3, 4], [5, 10, 8, 12]) # Построение графика`
`plt.xlim(1.5, 3.5) # Меняем границы вдоль осей`
`plt.ylim(-10, 20) # Меняем границы вдоль осей`

`fig, ax = plt.subplots(3, 3) # 3*3 графиков`
`plt.tight_layout() # нормальное отображение нескольких графиков`
`axes[i, j] # обращение к конкретному графику когда их несколько`

`plt.plot(x, x ** 2, label='x^2') # добавили название графика y=x^2`
`plt.plot(x, x ** 3, label='x^3') # добавили название графика`
`plt.plot(x, x ** 4, label='x^4') # добавили название графика`
`plt.legend() # отображаем легенду`

`plt.xticks([1, 2, 3, 4, 5]) # конкретные позиции делений x`
`plt.yticks([4, 6, 7, 8, 9]) # конкретные позиции делений y`
`plt.xticks([0, 1, 2, 3, 4], months) # вместо чисел будут подписи`

`plt.plot(sales) # Рисуем line plot по np.array`
`plt.plot(sales, years) # Рисуем line plot по 2 np.array`
`plt.plot(years, sales, linestyle='dashed', color='green')`
`linestyle="solid"/"dashed"/"dotted"`
`color="blue"/"yellow"/"red"...`

Гистограммы

`plt.hist(data, bins=7) # гистограмма по np.array`
`density=True # ось y показывает долю от 1, позволяет нормально смотреть соотношение например в 2 разных по размеру группах`
`alpha=0.5 # задаёт прозрачность`

Барчарт (зачастую используется для сравнения категориальных категорий)

`plt.bar(subjects, votes) # постройка барчарта`
`plt.bar(subjects, votes, color=colors, alpha=0.5, width=0.8) #кастомизация`

```python
# сгруппированный барчарт
votes_men = [10, 6, 3, 4, 15]
votes_women = [5, 2, 9, 2, 5]
x_indexes = np.arange(len(subjects)) # Создаём числовые индексы для категорий [0, 1, 2, 3, 4]
width = 0.35 # Задаём ширину столбца
plt.bar(x_indexes + width∕2, votes_men, width=width, label='Мужчины', alpha=0.5)
plt.bar(x_indexes - width∕2, votes_women, width=width, label='Женщины', alpha=0.5)
plt.xticks(x_indexes, subjects) # Подменяем числовые метки на названия предметов
```

```python
# stacked барчарт
plt.bar(subjects, votes_men, label='Мужчины', alpha=0.8)
plt.bar(subjects, votes_women, bottom=votes_men, label='Женщины', alpha=0.8)
```

Бокс плот

`plt.boxplot(data) # построение`
Оранжевая линия - медиана
Нижний край ящика - первый квартиль
Верхний край ящика - третий квартиль
Нижний ус - протянут до последнего значения не меньше Q1-1.5IQR
Верхний ус - протянут до последнего значения на больше Q3+1.5IQR
`plt.boxplot([data_group1, data_group2], labels=["Группа 1", "Группа 2"]) # рисуем два боксплота на одном графике`

