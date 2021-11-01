# App for LearningWords
## Содержание
1. [Описание проекта](#description) 
2. [Используемые технологии](#technologies)
3. [Проектирование](#designing)
---
<a name="description"></a>  

## Описание
Десктопное *(позже, возможно, мобильное)* приложение для :book:изучения:book: новых слов как **русских**, так и **иностранных**

* Будет **неопределенное** количество курсов на выбор по изучению слов:
    * *русский*,
    * *английский*,
    * *собственный* (подробнее: [Свой курс](#ur-list))  
    (список будет расширяться).

    Можно будет выбрать **несколько** курсов.
* У курсов будет сложность:
    * базовый,
    * продвинутый,
    * профессиональный,
    * литературный  
    (Список, вероятно, будет расширяться).
* <a name='ur-list'></a> 
  Также можно будет создать свой курс слов, в котором будут слова, добавляемые пользователем.  
  Если человек желает добавить **собственное** слово, то будет проверяться правильность ввода. Пользователь может отключить проверку на это слово, но затем он **должен** будет дать ему определение. Иначе определение не будет найдено.
* Словам можно будет давать определение **вручную**. Иначе определение будет браться из интернета (будет использоваться определённый источник).
* Приложение для корректной работы должно быть в **автозагрузке** и работать в **фоновом режиме**.
* Слово и его значение будет отображаться в уведомлении. В том же уведомлении будут кнопки **«понял»** **«выучил»**. Первая закрывает уведомление, а вторая так же закрывает, но и удаляет это слово из **БД** слов для изучения.
* Будет поле для поиска определения слова, введённого пользователем

---
<a name="technologies"></a>

## Какие технологии будут использоваться
* PyQt5 — для создания тела приложения
* SQLite — для создания базы данных слов
* :link:[win10toast](https://github.com/jithurjacob/Windows-10-Toast-Notifications) / :link:[NotificationHub](https://docs.microsoft.com/ru-ru/azure/notification-hubs/notification-hubs-python-push-notification-tutorial) / :link:[Plyer](https://docs.microsoft.com/ru-ru/azure/notification-hubs/notification-hubs-python-push-notification-tutorial) — для настройки уведомлений 
* BeautifulSoup4 — для парсинга сайтов
* pyinstaller — для конвертации в *.exe*

---

<a name="designing"></a>
## Этапы разработки
### 1. Составить базу данных слов (для начала: английский и русский)  
  > Так как я не нашел уже готовую БД слов, я буду парсить сайт https://slovaronline.com/. И из полученных данных будет создаваться ДБ.  
  - [x] Определить пустую БД  
  - [x] Спарсить английские слова в *.csv*.   
  > * [Парсер](/Code/DB_parser.py) уже написан, осталось только менять переменную **URL** на нужную ссылку и не более, так как структура у сайта одинакова.  
  > * Файл *.csv* имеет вид ```word;value;example``` . ```word``` и ```value``` обязательно **НЕ**пустые, а ```example``` будет не всегда, так как сайт бывает не предоставляет примеры. 


  - [ ] Обработать файл *.csv* (убрать все лишнее) и добавить таблицу с английскими словами  
  > * Под обработкой я имею ввиду убрать лишние переносы строки: ```example``` бывает многострочный, из-за этого, его нужно нормализовать.
  - [ ] Спарсить русские слова в *.csv*  
  - [ ] Обработать файл *.csv* (убрать все лишнее) и добавить таблицу с русскими словами 
### 2. Создать интерфейс приложения
  > Предполагается, что будет 2 окна: **Главная** и **Настройки**
  - [x] **Главная**: на основной странице будут расположены **курсы** и **кнопка, вызывающая настройки**. **Курсы** представляют собой тоже некую кнопку, нажатием на которую активируется курс, а повторным деактивируется. На кнопке курса будет располагаться информация о нем: **название**, **сложность**, **описание**. ![Главное окно](/Interface/theIntendedInterfaceOfTheMainWindow.png)  
  (может быть чуть криво, тк я рисовал от руки :dizzy_face: )
  * На изображении показан предполагаемый интерфейс главного окна. Здесь выбранные курсы обведены <span style="color: orange;">**оранжевой**</span> рамкой, а деактивированные никак не выделяются.  
  Также имеется кнопка для открытия окна настроек. Она представлена в правом нижнем углу.
  - [ ] **Окно настроек**. Там будет располагаться поле, для изменения частоты отправки уведомлений (в часах). По-умолчанию там стоит 1 час.
  ![Окно настроек](/Interface/theIntendedInterfaceOfTheSettingWindow.png)
### 3. Добавить бэкэнд
  * На **Главном окне**  
    Необходима будет обработка нажатий на курсы. При выборе курса будет вызываться функция ```selectCourse```, а при деактивировании курса будет вызваться функция ```unselectCourse```.
    * Будет определена **БД текущего курса**, куда будут добавляться слова из выбранных курсов
    * Также будет создана **БД изученных слов**, куда будут добавляться изученные слова. Пополняться она будет при вызове ```learnWord```, которая будет описана в **4 этапе** проекта
    * ```selectCourse``` отвечает за добавление БД слов данного курса в **БД текущего курса**, откуда, используя ```random.choice``` ,будут браться слова. При вызове ```learnWord``` данное слово будет удаляться из БД текущего курса и, в свою очередь, добавляться в БД изученных слов
    * ```unselectCourse``` будет убирать из БД текущего курса слова этого курса. Но из БД изученных слов удаляться не будет
  * На **Окне Настроек**  
    Необходимо будет привязать к кнопке сохранения функцию, которая будет сохранять выбранные параметры настройки. И в соответствии с этим, работать
### 4. Настроить уведомления
  * Есть 2 варианта:
    1. Использовать уведомления Windows 
    2. Использовать **свои** всплывающие окна
  * Если выбирать первый пункт, то будет использоваться одна из [вышеперечисленных](#technologies) библиотек. В уведомлении будет отображаться информация вида: **слово** - ***значение***, и если поместиться, примеры употребления.   
  Под текстом будут располагаться 2 кнопки: **«Понял»** и **«Выучил»**
    * **Понял** будет равносильна закрытию окна уведомления. То есть она не будет ничего за собой влечь
    * **Выучил** будет вызывать функцию ```learnWord```: удалять из БД текущего курса данное слово и добавлять его в БД изученных слов
### 5. Скомпилировать в *.exe*
  * ```pip install pyinstaller```
  * Открываем консоль в той папке, где лежит нужный файл **.py** и пишем:  
    > ```pyinstaller my_soft.py```. Вместо my_soft.py подставляем свое название.  
  * Будут сгенерированы 2 папки, **build** и **dist**. build можно сразу удалять, в dist будет наш *.exe*.
  * Можно так же все в 1 файл паковать, параметром **-F**:  
    > ```pyinstaller -F my_soft.py```
  * Чтобы не появлялось консольное окошко во время запуска, добавляем **-w** или **--noconsole**
  * Добавляем иконку:  
    > ```-i=my_icon.ico``` *(или --icon=my_icon.ico)*
  * Название приложения
    > ```-n="my App"``` *(или --name=Name)*