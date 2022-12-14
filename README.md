# MonoRemote - удаленное управление контрольно-измерительными приборами
*Разрабатывается (первоначально) для устройств производства компании **Rohde & Schwarz***

### Цель проекта: 
#### Реализовать удаленное взаимодействие с контрольно-измерительными приборами (анализаторы спектра, осцилографы и т.п.) для проведения измерений и лабораторных работ студентами (кафедры ИУ* МГТУ им. Баумана)

### Как это работает *(простыми словами)*:
У многих приборов производства компании **Rohde & Schwarz** уже "из коробки" присутствует возможность удаленного управления, представлена она следующим образом:


**Прибор <---> WEB-интерфейс (http://`device-ip`/vnc.htm) <---> Пользователь**


#### Функционал взаимодействия с прибором условно можно поделить на 2 части:

![Image1](https://i.ibb.co/gm90QBM/2022-12-13-160609.png)


### 1. Передача изображения экрана пользователю

На приборе поднят VNC-сервер, который отвечает за передачу изображения пользователю

### 2. Получение информации о нажатии кнопок от пользователя

На приборе поднят PHP-сервер, который обрабатывает GET-запросы от пользователя с WEB-интерфейса и эмулирует нажатие соответствующей кнопки

Схематично:

![Image2](https://i.ibb.co/q0pt4P4/image.png)

Один IP-адрес - один прибор. Не очень удобно.

Поэтому была поставлена задача: Один IP-адрес - N приборов.
То есть возможность взаимодействия с N-ым количеством приборов в одной вкладке браузера.

### Реализация: 

![Image3](https://i.ibb.co/kBHD4Jx/image.png)


Для доступа к прибору из "любой точки мира" в цепочку добавляется промежуточный сервер (прокси сервер?). 

**Для передачи изображения:**

На нем поднят websocket-сервер с помощью утилиты [websockify](https://github.com/novnc/websockify), которая позволяет преобразовать траффик с VNC-сервера в Websocket-трафик (и наоборот), после чего на клиенте при помощи инструментов от [noVNC](https://github.com/novnc/noVNC) (они же разработали и **websockify**) можно подключаться к VNC-сессии (изображение отрисовывается на канвасе, которых на странице может быть несколько).

**Для отправки нажатия кнопок:**

На сервере поднят прокси-сервер на Node.js (Express), который проксирует запросы на нажатие от клиента на нужный прибор.

**После чего всё это объединяется в единый интерфейс, написанный на React (пока в разработке):**

![Image3](https://i.ibb.co/G5Dcsn9/image.png)

*(проект находится в стадии разработки)*





