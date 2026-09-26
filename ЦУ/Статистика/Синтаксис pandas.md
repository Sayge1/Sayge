**`pd.Series(data, index, name)`** - список
**`pd.DataFrame(data_dict, index)`** - таблица
**`df.dtypes`** - выведет типы данных столбцов
**`pd.read_csv(filepath, sep, encoding='utf-8',index_col="id")`** - чтение из файла
**`df.to_csv(path_or_buf, sep, index=True,encoding='utf-8')`** - сохранение датафрейма в файл
**`df.head(n)`** - просмотр первых n строк
**`df.shape`** - сколько строк и столбцов в датафрейме (col,str)
**`df.columns`** - выведет имена столбцов
**`df.index`** - выведет метки строк
**`df.describe()`** - выводит подробные данные по столбцам (квартили, среднее и т.д.)
**`df.loc[row_labels, col_labels]`** - выводит выбранные через `1:2` или по названиям столбцы/строки
**`df.iloc[row_positions, col_positions]`** - выводит столбцы/строки по числовым значениям индексов
**`df['col'] cond value`** - булева маска 
**`df['col1'] cond df['col2']`** - булева маска
**`df[mask]`** - вывод таблицы по маске
**`df['col'].isin(values_list)`** - булева маска проверяющая входит ли элемент в values_list
**`& | ~`** - булевы операторы которые могут участвовать в булевых масках
**`df['col'].unique()`** - выводит уникальные значения столбца
**`df['col'].value_counts()`** - выводит сколько раз встречалось каждое значение в столбце
**`df['new_col'] = df['col1'] oper df['col2']`** - арифм. операции между датафреймами
**`df.drop(index, columns, inplace=False)`** - удаление чего-то в датафрейме
**`df.rename(index, columns, inplace=False)`** - переименовывает колонки, нужно передать словари
**`df.sort_values(by, ascending=True, inplace=False)`** - сортировка
**`df.reset_index(drop=False, inplace=False)`** - удалить индекс
**`df.set_index(key, drop=False, inplace=False)`** - заменить индекс
**`series.to_frame(name)`** - переводит series в датафрейм
**`df.agg({'col': funcs, ...})`** - применение сразу нескольких базовых формул к датафрейму
**`df.astype(type)`** - приводит к указанному типу
**`pd.to_datetime(series, format)`** - строки в даты
**`df.isna()`** - проверяет на na
**`df.dropna()`** - удаляет строки содержащие na
**`df['col'].fillna(value)`** - заполняет na значениями
