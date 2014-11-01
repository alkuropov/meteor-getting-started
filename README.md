# Meteor. Разрабатываем TODO List.

В данном уроке я не хочу обсуждать, почему метеор убийца веба, тем более я так не считаю, но определенную симпатию к этому фреймворку имею. Поэтому хочу показать с чего можно начать при разработке приложения на нем, какие есть пакеты и вообще что есть такое этот метеор.

Сразу хочу сказать, что у меня нет большого опыта в разработке веб приложений. Я занимаюсь этим всего около двух лет, а с метеором знаком вообще лишь пару месяцев.

Также хочу предупредить, что в данном уроке будут использоваться
следующие технологии для непосредственного написания примера:

* `jade` - html препроцессор;
* `less` - css препроцессор;
* `coffeescript` - язык программирования, компилируемый в javascript.

Небольшая затравка в виде скриншота полученного в ходе урока

![result](https://raw.githubusercontent.com/ovcharik/meteor-getting-started/master/images/result.png)

И кому все еще интересно, добро пожаловать под кат.

+-------------+
| Продолжение |
+-------------+

## Установка Meteor

Сам метеор базируется на `nodejs` и `mongodb`, так же у метеора нет поддержки windows, но они обещают скорым временем это исправить.

Поэтому первым делом устанавливаем [nodejs](http://nodejs.org/download/) и [mongodb](http://www.mongodb.org/downloads).

Следующим шагом необходимо установить метеор. Он не лежит в npm репозитории, поэтому не нужно торопиться и командовать `npm install -g meteor`, в данном случае лишь загрузиться его старая версия, для правильно установки необходимо выполнить в консоли:

    $ curl https://install.meteor.com/ | sh

## Создание проекта

После установки метеора можно сходу командовать

    $ meteor create 'todo-list'
    todo-list: created.

    To run your new app:
       cd todo-list
       meteor
    $ cd todo-list
    $ meteor
    [[[[[ ~/dev/meteor-getting-started/todo-list ]]]]]

    => Started proxy.
    => Started MongoDB.
    => Started your app.

    => App running at: http://localhost:3000/

Данный вывод означает, что все прошло хорошо, и наш хелловорлд можно проверить в браузере.

![helloworld](https://raw.githubusercontent.com/ovcharik/meteor-getting-started/master/images/helloworld.png)


Теперь, после проверки работоспособности нового проекта, файлы, находящиеся в корне проекта, можно удалить, нам они не особо интересны. Также можно заметить что была создана директория `.meteor`, там хранится различная служебная информация о проекте и даже автоматически сгенерируемый `.gitignore`, кстати для ручного управления пакетами можно изменять файл `packages`, но консольные утилиты тоже достаточно удобны.

Если у вас такой же результат как и у меня, значит минимальное окружение для разработки метеор проекта готово, если же что-то пошло не так - проверьте корректность установки `nodejs`, `mongodb` и `meteor`, например, у меня на компьютере сейчас следующая конфигурация:

>    $ node -v
>    v0.10.33
>    $ mongod --version
>    db version v2.4.12
>    $ meteor --version
>    Meteor 1.0

На данном этапе мы закончим с формальностями и приступим к разработке нашего туду листа. Для удобства рекомендую открыть новую вкладку консоли, так как перезапускать наше метеор приложение больше не потребуется, но будем использовать консольный интерфейс фреймворка для установки пакетов.

## Пакеты

Тут я опять же не хочу обсуждать почему в метеоре используется свой пакетный менеджер и почему они любят так велосипедить, к уроку это никакого отношения не имеет.

Установка пакетов производится командой

    $ meteor add <package-name>

Как я писал выше, приложение будем разрабатывать на `less`, `jade` и `coffeescript`, а значит самое время установить их. Все пакеты, используемые в уроке, и кучу других можно найти на сайте [Atmosphere](https://atmospherejs.com/). Собственно названия пакетов:

* `less`, `coffeescript` - это официальные пакеты, поэтому не содержат имя автора;
* `mquandalle:jade` - а вот это не официальный пакет, поэтому название состоит из двух составляющих, но выполнен он хорошо, и никаких проблем при его использовании у меня не возникало.

По ходу разработки мы добавим еще несколько популярных пакетов, и я постараюсь описать назначения каждого. Кстати `jquery` и `underscore` уже включены в метеор, как и множество других пакетов, полный список установленных пакетов, можно посмотреть в файле `./.meteor/versions`, в новосозданном проекте.

## Структура приложения

Теперь, по-моему, самое время разобраться с тем как метеор подключает файлы в проект, и какие способы регуляции этого существуют. Здесь нам не потребуется писать конфигурационные файлы для `grant` или `gulp`, метеор уже позаботился об этом. Для скафолдинга существует [проект](https://github.com/Pent/generator-meteor) для Yeoman, но мне приятнее все создавать в ручную. В предыдущем проекте я использовал следующую структуру папок:

    todo-list/           - корень проекта
    ├── client           - тут будут чисто клиентские файлы
    │   ├── components   - компоненты приложения будут состоять из шаблона 
    │   │                  и скрипта, реализующего его логику
    │   ├── config       - файлы конфигурации
    │   ├── layouts      - базовые шаблоны, не имеющие никакой логики
    │   ├── lib          - различные скрипты, которые могут понадобится на
    │   │                  клиенте
    │   ├── routes       - клиентский роутинг
    │   └── styles       - стили
    ├── collections      - здесь будем хранить модели
    ├── lib              - скрипты, которые могут понадобится везде
    ├── public           - статика, картинки, robots.txt и все такое
    ├── server           - файлы для серверной части приложения
    │   ├── methods      - тут будут серверные методы, типа реста,
    │   │                  только удобнее
    │   ├── publications - расшаривание данных из коллекций
    │   ├── routes       - серверный роутинг, собственно можно будет
    │   │                  контролировать http запросы
    │   └── startup      - инициализация сервера

Но в метеоре нет никаких ограничений на именование папок и файлов, так что можете придумать, любую, удобную для вас структуру. Главное следует помнить о некоторых нюансах:

* все файлы из папки `public` в корне проекта будут доступны пользователям по ссылки из браузера;
* все файлы из папки `server` в корне, доступны только серверной части приложения;
* все файлы из папки `client` в корне, доступны только клиентской части приложения;
* все остальное, что находится в корне, доступно в любой среде;
* файлы в проект подключаются автоматически, по следующим правилам:
  - загрузка начинается с поддиректорий, и первой всегда обрабатывается директория с именем `lib`, далее все папки и файлы загружаются в алфавитном порядке;
  - файлы начинающиеся с `main.` загружаются последними.

Например рассмотрим, как будет загружаться наш проект в браузере: первым делом будут загружены файлы из директории `lib` в корне проекта, далее будет обрабатываться папка `client`, в ней также первым делом загрузятся файлы из `lib`, а потом в алфавитном порядке: `components` -> `config` -> ... -> `styles`. И уже после файлы из папки `collections`. Файлы из папок `public` и `server`, не будут загружены в браузер, но, например, скрипты, хранящиеся в папке `public`, можно подключить через тег `script`, как это мы привыкли делать в других проектах, однако разработчики фреймворка не рекомендуют подобный подход.

Также для регуляции среды выполнения в общих файлах можно воспользоваться следующими конструкциями:

```coffeescript
if Meteor.isClient
  # Код, выполняемый только в браузере
if Meteor.isServer
  # Код, выполняемый только на сервере
```

И для регулирования времени выполнения скриптов мы можем использовать метод `Meteor.startup(<func>)`, в браузере это аналог функции `$` из библиотеки `jQuery`, а на сервере, код в данной функции выполнится сразу же после загрузки всех скриптов. [Подробнее об этих переменных и методах](https://docs.meteor.com/#/full/core).

## Базовый шаблон приложения

Для верстки я буду использовать Bootstrap, да знаю, что он всем приелся, но верстальщик из меня никакой, а с бутстрапом я более менее знаком.

Для этого устанавливаем пакет `mizzao:bootstrap-3`, он самый популярный среди прочих, и думаю при его использовании у нас не должно возникнуть проблем.

Далее создаем в папке `client/layouts` файл `head.jade`. Это будет единственный файл в нашем приложении не имеющий формат шаблона, короче просто опишем шапку, а позже разберем что такое шаблоны.

```jade
//- client/layouts/head.jade
head
  meta(charset='utf-8')
  meta(name='viewport', content='width=device-width, initial-scale=1')
  meta(name='description', content='')
  meta(name='author', content='')

  title Meteor. TODO List.
```

Можно открыть браузер и убедиться, что после добавления файла страница имеет указанный заголовок.

Прежде чем начнем верстать предлагаю провести базовую настройку клиентского роутинга, а чуть позже мы разберем этот момент подробнее. Для роутинга можно воспользоваться популярным решением, имеющим весь необходимый нам функционал. Установим пакет `iron:router` ([репозиторий](https://github.com/EventedMind/iron-router)).

После установки в директории `client/config` создаем файл `router.coffee`, со следующим содержанием:

```coffeescript
# client/config/router.coffee
Router.configure
  layoutTemplate: "application"
```

Очевидно, что здесь мы задаем базовый шаблон для нашего приложения, он будет называться `application`. Поэтому в папке `layouts` создаем файл `application.jade`. В этом файле мы опишем шаблон, некоторую сущность, которая на этапе сборки приложения превратится в код на `javascript`, но для нас он останется кодом на `jade`. Кстати в метеоре используется их собственный усатый шаблонизатор `spacebars` и библиотека `blaze`.

Если коротко, то процесс работы шаблонов выглядит следующим образом (на сколько я понял из документации). Шаблоны `spacebars` компилируются в объект библиотеки `blaze`, которая в последствии будет работать непосредственно с `DOM`. В описании к проекту есть сравнение с другими популярными библиотеками:

* по сравнению с обычными текстовыми шаблонизаторами блейз работает с домом, он не будет заново рендерить весь шаблон, что бы поменять один аттрибут, и у него нет проблем с вложенными шаблонами;
* по сравнению с шаблонами `Ember`, блейз рендерит только изменения, нет нужды в явных дата-байдингах и в описаниях зависимостей между шаблонами;
* по сравнению с шаблонами `angular` и `polymer`, блейз имеет понятный и просто синтаксис, меньший порог входа и вообще не позиционируется как технология будущего, а просто работает;
* по сравнению с `React` имеет простой синтаксис описания шаблонов и простое управление данными.

Это я практически перевел параграф из официального [описания](https://atmospherejs.com/meteor/blaze) библиотеки, так что прошу не кидаться в меня камнями, если с чем то не согласны. Сам я сталкивался с этими технологиями (кроме `ember`) и в принципе согласен с авторами библиотеки, из минусов в блейзе хочу заметить завязку на метеоре.

Но мы в своем проекте не используем явно ни `blaze`, ни `spacebars`. Для `jade` шаблонов процесс компиляции имеет такую последовательность: `jade` -> `spacebars` -> `blaze`.

Все шаблоны в метеор описываются в теге `template`, где должен быть аттрибут с именем шаблона. Помните, мы в настройках роутера указали `layoutTemplate: "application"`, вот `application`, как раз и является именем шаблона.

Вроде разобрались что такое шаблоны в метеор, самое время сверстать наш шаблон, он будет состоять из шапки и футера.

```jade
//- client/layouts/application.jade
template(name='application')
  nav.navbar.navbar-default.navbar-fixed-top
    div.container
      div.navbar-header
        button.navbar-toggle.collapsed(
          type='button',
          data-toggle='collapse',
          data-target='#navbar',
          aria-expanded='false',
          aria-controls='navbar'
        )
          span.sr-only Toggle navigation
          span.icon-bar
          span.icon-bar
          span.icon-bar
        a.navbar-brand(href='#') TODO List
      div#navbar.collapse.navbar-collapse
        ul.nav.navbar-nav

  .container
    +yield

  div.footer
    div.container
      p.text-muted TODO List, 2014.
```

Здесь нужно понимать, что это не совсем привычный нам `jade`, с его миксинами, джаваскриптом и инклудами. `Jade` должен скомпилироваться в шаблон `spacebars`, и это накладывает некоторые особенности. От `jade`, можно сказать мы заберем только синтаксис, остальное нам просто не нужно. В данном шаблоне используется конструкция `+yield`, это конструкция означает, что будет вместо нее будет отрендерен шаблон `yield`, это особенность `iron-router`, он автоматически подставит нужный шаблон в зависимости от пути, чуть позже мы займемся роутерами, а сейчас можно внести косметические изменения в верстку и посмотреть на результат.

```less
// client/styles/main.less
html {
  position: relative;
  min-height: 100%;
}

body {
  margin-bottom: 60px;

  & > .container{
    padding: 60px 15px 0;
  }
}

.footer {
  position: absolute;
  bottom: 0;
  width: 100%;
  height: 60px;
  background-color: #f5f5f5;

  .container .text-muted {
    margin: 20px 0;
  }
}
```

![my_helloworld](https://raw.githubusercontent.com/ovcharik/meteor-getting-started/master/images/my_helloworld.png)

Давайте посмотрим, что по сути мы сделали, а то слов было сказано достаточно много, а результата особо и не видно. А по сути мо создали одной командой метеор проект, добавили в него несколько пакетов и четыре файла, два из который к метеору отношения практически не имеют. У нас после этих простых операций появилась возможность использовать любимые технологии для написания кода (по крайней мере мои любимые), и клиентский роутинг, с функционалом которого мы еще разберемся. Подобный старт мне сильно напоминает работу с `Ruby on Rails`, только у нас не будет забот о том как писать серверный код, а для клиентского у нас есть набор удобных библиотек.
