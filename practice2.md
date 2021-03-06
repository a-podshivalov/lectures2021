# Практическое занятие 1: GPIO и xtimer

## Необходимая документация

Описание нужных модулей RIOT можно посмотреть в официальной документации:

Управление GPIO: https://doc.riot-os.org/group__drivers__periph__gpio.html

Таймеры: https://doc.riot-os.org/group__sys__xtimer.html

Принципиальные схемы элементов "конструктора" из аудитории 234 выложены здесь: https://github.com/unwireddevices/umdk-boards

## Задания

Цифры после номеров заданий обозначают сложность в "баллах", баллы за работу на практических занятиях суммируются. Всего будет три "пакета" заданий; общая сумма баллов существенно больше 10 (максимальное количество баллов за практические занятия), поэтому если чувствуете уверенность - попробуйте начать с более сложных, это как минимум будет более интересно. Все задания делать необязательно.

1. (1) Проверьте, в каких областях памяти создаются глобальные и локальные переменные у разных процессов, обработчиков прерываний и т. п., как влияют на расположение переменных модификаторы `const` и `static`. Для этого создайте несколько таких переменных, а потом выведите в `printf` их адреса, например, так (заодно можно выводить адреса функций):
```
static const int i;
printf("Static const local to main() is at %p\n", &i);
```

2. (1) Лампочка с выключателем - кнопка SAFE на плате отладчика подключена к выводу `GPIO_PIN(PORT_A, 11)` микроконтроллера. Напишите программу, которая по нажатию кнопки включала и выключала бы светодиод:
    1. с использованием периодического опроса кнопок в цикле;
    2. обрабатывая нажатие кнопки в прерывании с обработкой "дребезга" путем отключения прерываний;
    3. светодиод должен не просто включаться, а мигать с заданной частотой, длинное нажатие кнопки переключает частоту миганий.

3. (1) Фирма ST Microelectronics заявляет, что в стоящем на наших платах микроконтроллере (STM32L151) имеется high-performance ARM core, и даже приводит циферки, которые он набирает в бенчмарках. Проверьте это, скомпилировав любой из популярных бенчмарков, как приложение под RIOT. Сравните это с результатами того же бенчмарка на своем компьютере.
    1. Coremark (93 Coremark). Не вносите изменения в файлы самого бенчмарка, за исключением `core_portme.c` и `core_portme.h`.
    2. Dhrystone (33 DMIPS). Учтите, что Dhrystone необходимо собирать с отключенными оптимизациями компилятора, а сам код бенчмарка написан в очень устаревшем стиле, в современных компиляторах приводящем к генерации массы предупреждений (warning). Изучите Makefile-ы операционной системы, посмотрите, как можно подавить эти предупреждения или отключить их. Кроме того, понадобится включить печать float-ов в `printf()`, добавив в флаги компоновщика (линкера) опцию `-u _printf_float`.

4. (2) Цифровой замок - подключите плату UMDK-4BTN с четырьмя кнопками, напишите программу, которая при нажатии кнопок в правильном порядке ("наборе кода") зажигала бы светодиод на плате, а при повторном наборе - гасила бы его. Добавьте вывод в консоль сообщений о неправильных попытках ввода кода и тому подобных ситуациях. Логика работы - по вашему усмотрению. Посмотреть, каким выводам микроконтроллера соответствуют штыревые выводы на плате, можно все в том же файле `boards/unwd-range-l1-r3/board.h`.

5. (2) Светофор с кнопкой - подключите к микроконтроллеру несколько светодиодов (в лаборатории есть макетные платы и разнообразное дополнительное оборудование), переключайте их по заданной программе; при нажатии кнопки проверьте, сколько осталось до очередного включения "зеленого света" на нужном направлении, и если это время больше нужного - запланируйте новое переключение; если же зеленый свет и так скоро включится - то ничего не делайте (как на переходе между станцией метро "Строгино" и зданием МИЭМа).

6. (2) В драйвере датчиков DNT11/DHT21/DHT22 (`drivers/dht`; эти датчики есть в 234 аудитории) отсутствует обработка многих ошибок (например, при отсутствии или неготовности датчика функция `dht_read` уходит в бесконечный цикл, в системе с кооперативной многозадачностью так делать нельзя). Исправьте драйвер до приемлемого вида.

7. (3; без операционной системы) Добавьте в реализацию USB-UART на отладочной плате UMDK-RF обработку команды `USB_CDC_REQ_SEND_BREAK` (при этом на выводе TX на заданное время устанавливается логический 0). Условие приема задания - подключение и прошивка микроконтроллера TI CC3200 через его UART-загрузчик (для его запуска требуется корректная реализация этой команды). В этом проекте отсутствует операционная система, используется библиотека libopencm3.
