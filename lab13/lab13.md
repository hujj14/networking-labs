# Лабораторная работа № 13

![топология сети](Network.png)

---

## Информация о текущей топологии

### Сетевые устройства

| Имя сетевого устройства | IP адреса интерфейсов | Маска подсети | Что настроено                           |
| ----------------------- | --------------------- | ------------- | --------------------------------------- |
| Switch0                 | ---                   | ---           | Базовая коммутация, access-порты для PC |

### Компьютеры

| Имя устройства | IP адрес     | Маска подсети | Default Gateway | Порт на Switch0 | Что настроено   |
| -------------- | ------------ | ------------- | --------------- | --------------- | --------------- |
| PC6            | 192.168.1.10 | 255.255.255.0 | ---             | Fa0/1           | IP, subnet mask |
| PC8            | 192.168.1.20 | 255.255.255.0 | ---             | Fa0/2           | IP, subnet mask |
| PC7            | 192.168.1.30 | 255.255.255.0 | ---             | Fa0/3           | IP, subnet mask |

## Задание

1. Проверить связь между всеми компьютерами с помощью команды `ping`.
2. Посмотреть MAC Address Table на коммутаторе Switch0.
3. Очистить динамическую MAC Address Table.
4. Снова выполнить `ping` между компьютерами и проверить, что коммутатор заново изучил MAC-адреса.
5. Настроить Port Security на порту `Fa0/1`.
6. Ограничить количество разрешённых MAC-адресов на `Fa0/1` до одного.
7. Настроить sticky MAC на `Fa0/1`, чтобы коммутатор сам запомнил MAC-адрес PC0.
8. Проверить список secure MAC-адресов.
9. Настроить Port Security на портах `Fa0/2` и `Fa0/3`.
10. Вызвать нарушение Port Security: отключить PC0 от `Fa0/1` и подключить к этому порту другой компьютер.
11. Проверить состояние порта после нарушения.
12. Восстановить порт из состояния `err-disabled`.

## Контрольные вопросы

1. Что такое MAC Address Table?
**Tаблица в которой хранится номер интерфейса и напротив него MAC адрес устройства**
2. Для чего коммутатору нужна MAC Address Table?
**Для того чтобы комутатор смог пересылать кадр с одного на другой опираясь на эту таблицу**
3. Как switch изучает MAC-адреса?
Dynamic MAC:
        Это когда компьютер отпраляет другому кадр и Src MAC записывается в MAC TABLE
Static MAC:
        Это когда администратор вручную самостоятельно прописал MAC адрес
Secure MAC:
        Режим sticky когда комутатор сам опрашивает устройство о получении mac address
4. Что такое Source MAC Address и Destination MAC Address?
**Source MAC Address - это mac address источника, а Destination MAC Address - это mac address получателя.**
5. Что произойдёт, если switch не знает Destination MAC Address?
**Switch посылает всем комьютерам кадр с определенным mac address, если комьютер ответил ДА, то switch сохраняет его mac address и номер интерфейса в свою MAC Table.**
6. Что такое flooding?
Когда MAC address Table переполняется адресами.
7. Чем dynamic MAC-адрес отличается от static MAC-адреса?
**Dynamic Mac сам записывает Src Mac в MAC Table, а static MAC - это когда админ сам ввод MAC adress.**
8. Что такое secure MAC-адрес?
**Это когда ограничено поделючение других mac adress на интерфейсе.**
9. Что делает команда `show mac address-table`?
**Показывает всю MAC table.**
10. Что делает команда `clear mac address-table dynamic`?
**Очищает только динамические MAC.**
11. Что такое Port Security?
**Это функция для безопастности switch.**
12. На каких портах обычно настраивают Port Security?
Если нужно ограничить кол-во MAC address.
13. Почему Port Security обычно настраивают на trunk-портах?
**Чтобы нельзя было подключить свой комутатор с подслушиванием трафика и что бы комутатор работал только с 1 mac address.**
14. Что делает команда `switchport port-security maximum 1`?
Она ограничивает максимальное кол-во mac address до 1 на интерфейсе.
15. Что такое sticky MAC?
**Switch сам выучит MAC адрес и добавит его в running-config.**
16. Чем sticky MAC удобнее ручного указания MAC-адреса?
**Быстрее**
17. Что произойдёт, если к защищённому порту подключить другое устройство?
**Либо тарфик дропается, либо трафик откидывается.**
18. Какие есть режимы нарушения Port Security?
**shutdown, restrict и protect.**
19. Чем отличаются режимы `shutdown`, `restrict` и `protect`?
**Интерфейс отключатеся. Трафик дропается и все логируется. Трафик дропается, но ничего не логируется.**
20. Что такое состояние `err-disabled`?
Это режим работы интерфейса при shutdown.
21. Как восстановить порт из состояния `err-disabled`?
команда "no shutdown"
22. Как проверить, какие MAC-адреса были изучены Port Security?
команда "show interfaces status err-disabled"
23. Почему Port Security может защитить от подключения чужого устройства?
**Потому что switch на котором стоит Port Security может пропустить 1 mac address который он знает, а у чужого устройства другой mac address и он его не пропустит.**
24. Как Port Security помогает против MAC flooding attack?
Поможет режим shutdown и Protect.

# Решение
1) пинг проходит
2) Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----

   1    0001.6483.0413    DYNAMIC     Fa0/2
   1    000d.bd37.98b1    DYNAMIC     Fa0/1
   1    00e0.f9e6.5905    DYNAMIC     Fa0/3

3) Команда что бы очистить динамичские mac address
```
clear mac address-table dynamic
```
4) После пинга, 1 mac address удалилися.
5) Настройка Port-security
```
int fa0/1
switchport mode access
switchport port-security
```
6) Ограничить до 1 Mac adress:
```
int fa0/1
switchport mode access
switchport port-security
switchport port-security maximum 1
```
7) Настройка sticky:
```
 int fa0/1
switchport mode access
switchport port-security
switchport port-security maximum 1
switchport port-security mac-address sticky
```
8) Просмотр списков secure mac-adress:
```
show port-security adress
```
9) Полная настройка port-security
```
 int fa0/1
switchport mode access
switchport port-security
switchport port-security maximum 1
switchport port-security mac-address sticky
switchport port-security violation protect
``` 
10) Отключил компютер PC0 и подключил другой
11) SW0 не конектиться с PC3, так как у него другой mac-address. Интерфейс лег
12) Решение `err-disabled`:
Обратно переключили на PC0
```
no shutdown
```
