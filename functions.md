# Функции
## Python
### Работа с текстом
#### Функция, которая может проверить, есть ли символ или строка check_value между between_value_left_str и between_value_right_str.
```
# Функция, которая может проверить, есть ли символ или строка check_value между between_value_left_str и between_value_right_str. Если символа между символами нет или тип данных не проходит для проверки, вернет False. Необходима библиотека regex

def check_if_value_between_values(string, check_value_str, between_value_left_str, between_value_right_str):
    # Проверка на то, что string строка
    if isinstance(string, str):
        # Создание маски для проверки
        pattern = f'{between_value_left_str}.*?{check_value_str}.*?{between_value_right_str}'
        # Проверка на то, что в строке есть маска
        match = re.search(pattern, string)
        return bool(match)
    else:
        return False
```

### Работа с Pandas
#### Функция для смены типа данных в столбце в числовой тип, с возвращением индексов строк, вызвавщих ошибку.
```
# Функция для смены типа данных в столбце в числовой тип, с возвращением индексов строк, вызвавщих ошибку. Если в столбце нет NaN значений и ошибок, возвращает два датафрейма: с измененным типом данных и series размером с dataframe где нет ни одного значения False. 
# Если в столбце есть NaN значения или значения, которые нельзя преобразовать в целочисленный тип, возвращает 
# 1) dataframe со всеми возможно преобразоваными значениями в столбце, оставляя NaN и значения, которые нельзя преобразовать в целочисленный тип
# 2) series, в котором напротив строки с не NaN значения, которое не вышло преобразовать стоит False
# При type = 'int' преобразует колонку в целочисленый тип данных, при других значениях возвращает датафрейм где обрабатываемая колонка принимает числовой тип данных, по умолчанию float
# Функционал присвоения типа данных int проверен на примере и работает верно

def convert_column_to_number_safely(data, *columns, can_be_changed_show_number=10, type='float'):
    for column in columns:
        # Список не NaN значений, которые можно преобразовать в целочисленный тип
        can_be_changed = data[column].apply(convertable_to_numeric)

        # Список не NaN значений, которые нельзя преобразовать в целочисленный тип
        cant_be_changed = data[column].notna() & ~can_be_changed

        # Смена типа данных на числовой в строках без NaN
        data.loc[can_be_changed, column] = pd.to_numeric(data.loc[can_be_changed, column], errors='raise')

        # Смена типа данных на целочисленный в строках без NaN, если type = 'int'
        if type == 'int':
            data.loc[can_be_changed & data[column].notna(), column] = data.loc[
                can_be_changed & data[column].notna(), column].astype('int')

        # Вывод строк количества строк содержащих NaN значения, 
        print(f'Количество строк в столбце {column} содержащих NaN значения = {data[column].isna().sum()}')

        # Вывод строк, которые нельзя преобразовать в целочисленный тип
        if cant_be_changed.sum() > 0:
            print(
                f'Эти значения нельзя преобразовать в целочисленный тип, их количество = {cant_be_changed.sum()}, показано {can_be_changed_show_number} из них')
            print(data.loc[cant_be_changed, column].head(can_be_changed_show_number))

        # Смена типа данных всего столбца
        if cant_be_changed.sum() == 0:
            if type == 'int' and data[column].isna().sum() == 0:
                data = data[column].astype('int')
            else:
                data[column] = pd.to_numeric(data[column], errors='coerce')

    return data, cant_be_changed
```
