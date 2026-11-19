## Модуль 5. Раздел 4. Решение авторских задач

---

> В этом разделе собраны **авторские задачи** по классическим шифрам. Для решения на Python достаточно стандартной библиотеки.

Рекомендуемое время на решение

- **Задача №1** — до 25 минут.
- **Задача №2** — до 15 минут.

---

## №1. Авторская задача. Цезарь с пропущенной буквой (уровень **Сложно**)

### Условие

> В архиве нашли флаг, зашифрованный модифицированным шифром Цезаря. Буквам `A`–`Z` присвоили номера от 0 до 25. При обработке очередной буквы с номером `x` программа сначала вычисляет номер зашифрованной буквы `c`, затем обновляет текущий сдвиг `s`.
>
> ```text
> c = (x + s) mod 26
> s = (s + x + d) mod 26
> ```
>
> Начальный сдвиг и постоянный шаг `d` — целые числа от 0 до 25.
>
> После шифрования потерялась одна буква между первым и последним символами. Сохранилась строка `DQMVHMVWWCSU`.
>
> Известны первые пять букв исходного флага — `MENAD`. Контрольная сумма всего флага равна `980`. Чтобы её вычислить, умножьте номер каждой буквы исходного флага на её позицию и сложите результаты. Позиции считаются с единицы.
>
> Укажите исходный флаг, начальный сдвиг, шаг `d`, пропущенную букву и её позицию в полном шифротексте.

> [!Подсказка]
> Как выразить следующий сдвиг через только что полученную букву шифротекста и шаг `d`? Первые две буквы флага помогут вычислить параметры, а следующие три — проверить расшифровку.

> [!Решение]
> Из двух формул следует, что следующий сдвиг равен `(c + d) mod 26`. Пусть `c1` и `c2` — номера первых двух букв очередного кандидата на полный шифротекст. Первая буква флага `M` имеет номер 12, поэтому начальный сдвиг равен `(c1 − 12) mod 26`. Вторая буква `E` имеет номер 4, откуда `d = (c2 − c1 − 4) mod 26`.
>
> Переберём каждую позицию между соседними буквами сохранённой строки и вставим туда по очереди все 26 букв. Для каждого кандидата вычислим шаг и расшифруем строку. Если она начинается с `MENAD`, проверим контрольную сумму.
>
> ```python
> from string import ascii_uppercase as alphabet
>
> received = "DQMVHMVWWCSU"
> known_prefix = [alphabet.index(letter) for letter in "MENAD"]
> first, second = known_prefix[:2]
> expected_checksum = 980
>
> for index in range(1, len(received)):
>     for missing in alphabet:
>         restored = received[:index] + missing + received[index:]
>         codes = [alphabet.index(letter) for letter in restored]
>         initial_shift = (codes[0] - first) % 26
>         step = (codes[1] - codes[0] - second) % 26
>
>         plaintext = [first]
>         for previous, current in zip(codes, codes[1:]):
>             plaintext.append((current - previous - step) % 26)
>
>         if plaintext[:len(known_prefix)] != known_prefix:
>             continue
>
>         checksum = sum(
>             position * value for position, value in enumerate(plaintext, start=1)
>         )
>         if checksum == expected_checksum:
>             flag = "".join(alphabet[value] for value in plaintext)
>             print(flag, initial_shift, step, missing, index + 1)
> ```
>
> Только один вариант подходит по известному началу и контрольной сумме. После вставки `N` на позицию 10 получается полный шифротекст `DQMVHMVWWNCSU`.

> [!Ответ]
> Флаг — `MENADWASRIGHT`. Начальный сдвиг — `17`, шаг `d` — `9`. Пропущена буква `N` на позиции `10`.

---

## №2. Авторская задача. Две записки, один ключ (уровень **Средне**)

### Условие

> Организаторы квеста зашифровали две записки шифром Виженера. Буквы `A`–`Z` пронумеровали от 0 до 25. К номеру каждой буквы текста прибавляли номер соответствующей буквы ключа по модулю 26. Ключ длиной от 4 до 8 букв повторяли циклически. Пробелы переносили в шифротекст, а ключ продвигали только после букв.
>
> Первую записку шифровали с первой буквы ключа. Для второй использовали следующую букву ключа после той, которой зашифровали последнюю букву первой записки.
>
> | Материал | Текст |
> | --- | --- |
> | Первая записка | `KEEP THIS NOTE FOR THE TEAM` |
> | Шифротекст первой записки | `YVGW BKWJ PVBH TFT APH HVCT` |
> | Шифротекст второй записки | `BKS DCW QV IEFLZ WVV UAIJS` |
>
> Восстановите ключ и расшифруйте вторую записку. В ответе сохраните пробелы.

> [!Подсказка]
> Вычтите номер каждой буквы первой записки из номера соответствующей буквы её шифротекста по модулю 26. Найдите длину повторяющегося ключа и посчитайте, сколько его букв уже использовали к началу второй записки.

> [!Решение]
> Первая открытая буква `K` имеет номер 10, а соответствующая ей буква шифротекста `Y` — номер 24. Разность 14 соответствует букве ключа `O`. Повторяем вычисление для всех букв первой записки и получаем последовательность `ORCHIDORCHIDORCHIDORCH`. Из длин от 4 до 8 подходит только 6. Ключ — `ORCHID`.
>
> В первой записке 22 буквы. Остаток от деления 22 на 6 равен 4, поэтому для первой буквы второй записки нужно взять пятую букву ключа — `I`. Дальше вычитаем очередной сдвиг по модулю 26 и продвигаем ключ после каждой буквы.
>
> ```python
> from string import ascii_uppercase as alphabet
>
> numbers = {letter: index for index, letter in enumerate(alphabet)}
> first_note = "KEEP THIS NOTE FOR THE TEAM"
> first_ciphertext = "YVGW BKWJ PVBH TFT APH HVCT"
> second_ciphertext = "BKS DCW QV IEFLZ WVV UAIJS"
>
> shifts = [
>     (numbers[encrypted] - numbers[original]) % 26
>     for original, encrypted in zip(first_note, first_ciphertext)
>     if original != " "
> ]
> possible_lengths = [
>     length for length in range(4, 9)
>     if all(shift == shifts[i % length] for i, shift in enumerate(shifts))
> ]
> length, = possible_lengths
> key = shifts[:length]
>
> position = len(shifts)
> plaintext = []
> for letter in second_ciphertext:
>     if letter == " ":
>         plaintext.append(letter)
>         continue
>     shift = key[position % length]
>     plaintext.append(alphabet[(numbers[letter] - shift) % 26])
>     position += 1
>
> print("".join(alphabet[shift] for shift in key))
> print("".join(plaintext))
> ```

> [!Ответ]
> Ключ — `ORCHID`. Вторая записка — `THE MAP IS UNDER THE STAGE`.
