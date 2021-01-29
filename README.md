# Средние значения отношений сигнал/шум спутников, полученные по протоколам UBX, BINR.

## Описаниеd
Скрипты реализованы в Python 3.9.1
Использовались библиотеки: numpy, tkinter, matplotlib

Основная задача состоит в сравнении работы нескольких спутниковых приемников.
В данном проекте собирается информация о SNR каждого видимого спутника, общего для всех приемников.

## Описание файлов
### UBX_mes_data.py и BINR_mes_data.py
Данные скрипты содержат функции, позволяющие собирать данные из сообщений, хранящихся в файлах, и обрабатывать полученные данные из шестнадцатиричного формата в необходимый для исследования в соответствии с типом сообщения. На данный момент есть возможность обрабатывать только сообщения RAWX для UBX протокола и F5 для BINR протокола.

### UBX_handler.py и BINR_handler.py
Работа данных скриптов заключается в чтении из файла, пока не найдется заголовок одного из нужных сообщений. После обнаружения вызывается соответствующая функция из UBX_mes_data.py или BINR_mes_data.py, возвращающая необработанные данные, которые затем обрабатываются соответствующей для считанного сообщения фкнункцией из того же скрипта UBX_mes_data.py или BINR_mes_data.py. При завершении работы возвращаются обработанные данные, а так же идентификатор считанного сообщения.

### SNR_col.py
Основной скрипт, при запуске которого необходимо выбрать папку, в которой хранятся собранные данные с приемников во время эксперимента.
В зависимости от расширения файла (.ubx или .bnr) вызывается соответствующая функция из скриптов UBX_handler.py или BINR_handler.py.
Данные собираются на интервале времени, общем для всех приемников, и для одновременнго видимых спутников.
Если значение SNR спутника оказалось ниже 30 дБ, то считается, что данные спутника не использовались при получении решения, и спутник не участвует в гистограмме.
Также, если спутник был видим менее 80% времени, то его данные тоже не учитываюся.
При окончании работы выводится гистограмма значений SNR спутников попадающих под условия.

## Пример гистограммы
[Гистограмма](Screenshots/hist_1.PNG "Гистограмма SNR для ровера и базы")

## Особенности и допущения работы.
### 1. Приемники формируют сообщения с одинаковой частотой
Основной скрипт SNR_col.py изначально находит в каком из файлов первое сообщение содержит наибольшую временную отметку.
Все данные, идущие до этой отметки игнорируются. После достижения времени из каждого файла в дальнейшем считывается по одному сообщению. Таким образом, считанные значения должны иметь одинаковые временные отметки, а для этого необходимо, чтобы частота формирования сообщений была одинаковой.

### 2. Данные в файлах должны быть непрерывны по времени.
Исходя из первого пункта, в скрипте не проверяется соответствие временных меток, поэтому временной разрыв между данными также не учитывается. 
Для получения корректного результата необходимо избегать таких случаев.

### 3. Все файлы с данными в папке должны содержать общий временной промежуток.
На данный момент не обрабатываетя ситуация данная ситуация. При возникновении такого случая программа уходит в вечный цикл.
