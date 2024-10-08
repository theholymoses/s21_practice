Реализация утилит cat и grep

--------------------------------------------------------------------------------
I) cat

Флаги, которые необходимо реализовать:
-b - нумерация непустых строк
-n - нумерация всех строк
-s - вывод не более одной пустой строки подряд
-v - вывод непечатных символов (подробнее смотреть в мануале и гугле)
-t - аналогично -vT
-T - вывод tab как ^I
-e - аналогично -vE
-E - вывод переносов строки как $(по сути префикс переноса строки знаком доллара)

--------------------------------------------------------------------------------
II) grep

Флаги, которые необходимо реализовать:
-f - файл с регулярками 
-e - регулярка
-r - искать рекурсивно
-n - показывать номер строки с совпадением
-h - не выводить имя файла с совпадением
-i - не учитывать регистр
-v - инвертировать поиск
-c - выводить только кол-во совпавших строк для каждого из файлов
-l - выводить только названия файлов с совпадениями
-o - выводить только совпавший фрагмент строки, а не строку целиком
-s - не выводить ошибки о несуществующих или нечитаемых файлах

--------------------------------------------------------------------------------
- Более подробное описание флагов и самих утилит можно(и нужно) посмотреть в man:
  `man cat`
  `man grep`

- Парсинг флагов и опций коммандной строки либо реализовать самостоятельно, либо использовать хедер getopt.h
  Пример использования getopt.h можно посмотреть в мануале:
  `man getopt.3` или `man 3 getopt`
  Цифра 3 указывает на секцию мануала - в данном случае библиотечные функции.

- Реализованные программы должны вести себя идентично системным.
  a) Флаги могут быть переданы скопом (например `cat -bnsvetET`)
  б) Тестирование автоматизировать с помощью скрипта и тестовых данных.
     Запускаем системный cat с нужными флагами и инпут-файлам, выводим результат в файл.
     Запускаем самопальный cat с теми же флагами и инпут-файлами, выводим результат в другой файл.
     Сравниваем два результирующих файла утилитой `diff`, если есть отличия - думаем, что не так, исправляем.
  в) Разные флаги могут по-разному взаимодействовать. Например, флаги -b и -n для cat - взаимоисключающие, и один из них приоритетнее.
     Тестировать на системных утилитах.
  г) НЕ ОБЯЗАТЕЛЬНО: Не забыть про чтение из stdin (подробнее в man)

- Для grep пригодится библиотека регулярных выражений. Использовать pcre или regex.
  В системном grep выбор типа регулярки передаётся через специальный параметр. Если в своём используем pcre,
  то при тестировании системный grep вызывать с флагом -P. Если используем regex, то системный grep вызывать с флагом -E.
  Можно реализовать и то и другое.

- Длинные варианты флагов реализовывать не обязательно.
  Для примера, флаг программы cat, говорящий о том, что надо нумеровать все непустые строки, можно выразить тремя стилями:
  в UNIX стиле:  -b
  в BSD стиле:   b
  в GNU стиле:   --number-nonblank
  BSDшный стиль ныне почти нигде не используется, на память приходят только программы `ps` и `tar`, поддерживающие такой синтаксис.
  Системный cat поддерживает только UNIX и GNU. Реализовать достаточно флаги в стиле UNIX.

- Для операций чтения в идеале использовать системные вызовы read/write. Для открытия и закрытия файлов, соответственно open/close.
  Подробнее о них можно почитать в мануале:
  `man read.2`  или `man 2 read`
  `man write.2` или `man 2 write`
  и.т.д.
  Цифра 2 означает секцию мануала, в данном случае - системные вызовы.

  Также стоит разузнать какой размер буфера ввода/вывода будет наиболее оптимальным. На современной системе это скорее всего 4096 байт.

  Если на системных вызовах слишком сложно - можно использовать функции из stdio.h.
  `man stdio.h` <- посмотреть список всех функций
  `man fopen.3` <- посмотреть конкретно про функцию fopen

  Подробнее разузнать о том, чем отличаются системные вызовы и библиотечные функции можно из книги Advanced Programming in Unix Environment, главы 3 и 5.

- Флаги можно реализовать в виде отдельных битов одной int-переменной. Так можно попрактиковать работу с побитовыми операторами.
  Для побитовых операций следует использовать беззнаковые числа фиксированного размера.
  В хедере stdint.h есть нужные typedef, например uint8_t, uint16_t.

  Если хочется попроще, то можно использовать отдельную булевую переменную под каждый флаг. Хедер stdbool.h

- Рекомендуемая структура проекта:
  01_bash/
          cat/
              Makefile       <- скрипт сборки
              src/           <- исходный код
              out/
                  tests/     <- тестовый скрипт, тестовые данные
                  s21_cat    <- исполняемый файл

          grep/
              Makefile
              src/
              out/
                  tests/
                  s21_grep

- Сборку программ реализовать с помощью Makefile. Мануал по нему можно найти на gnu.org или погуглить. Достаточно прочитать введение.

- Рекомендуемые флаги для gcc:
  -std=c99 - использовать конкретный стандарт. Самый распространённый - c99, но можно использовать и другой.
  -Wall    - врубить основные предупреждения.
  -Wextra  - врубить ещё кое-какие предупреждения.
  -Wvla    - кидать предупреждение, если использован variable-length array.
             Отказ от использования vla - одна из распространённых практик, и делает задачу чуть интереснее.
  -Werror  - сделать предупреждения ошибками, прерывающими компиляцию.
  -O2      - уровень оптимизации 2. Иногда хреново написанный код ведёт себя нормально, но при этом ломается с включенной оптимизацией.
             Для этого и рекомендуется этот флаг - отсечь сомнительный код через баги.

  Подробнее о том, что делает определённый флаг можно почитать в мануале - `man gcc`.
  Страница мануала для компилятора большая, есть резон научиться использовать поиск по регулярке в man, чтобы сразу прыгнуть к нужному флагу.
  `man man` или гугл.

- Для поиска утечек можно использовать Valgrind.

- Для дебага можно использовать gdb. Но сперва его надо хотя бы поверхностно изучить, он не так прост. Гугл и gnu.org в помощь.

- Если есть проблемы с С, то можно почитать книгу The C programming language от Керингана и Ритчи. Гуглится как K&R.
  Книга достаточно короткая, и с референсом стандартной библиотеки.

