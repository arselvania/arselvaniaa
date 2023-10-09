[English](readme.md)
# Мини-приложение магазина сладостей для Telegram.
## 📖 Введение
Данное приложение было разработано с использованием веб-сервера Express, СУБД PostgreSQL (помимо таблиц были разработаны представления и функции, которые облегчат работу с БД). 
Приложение включает в себя: главную страницу интернет-магазина для пользователя, редактор таблиц для администратора магазина и небольшую логику для курьеров магазина.
Приложение разрабатывалось под Vercel, именно поэтому все js файлы располагаются в папке api. 

## 🤖 Создание бота
Для использования мини-приложения требуется создать Telegram-бота. Для этого, напишите [@BotFather](https://t.me/BotFather) и отправьте ему команду /newbot. После чего задайте имя бота (например, Доставка Продуктов), после чего укажите @юзернейм бота, по которому пользователи смогут найти его. Юзернейм должен оканчиваться на bot. Если юзернейм уже занят - попробуйте другой. После успешного создания бота, вам будет показан токен бота. Он секретный, его не следует передавать никому. Данный токен потребуется скопировать и сохранить в `.env`.

Для приёма оплат требуется получить платёжный токен. Чтобы получить его, отправьте [@BotFather](https://t.me/BotFather) команду /mybots, чтобы получить список созданных ботов, после чего выберите своего бота, нажмите на Payments и выберите платёжного провайдера. Вы будете перенаправлены к другому боту, который выдаст вам токен. После чего токен появится и в [@BotFather](https://t.me/BotFather). Скопируйте платёжный токен и укажите его в `.env`.

Курьеры получают список активных заказов через inline поиск. Для работы с ним, его нужно включить через [@BotFather](https://t.me/BotFather). Для этого нужно также выбрать своего бота в /mybots, затем зайти в Bot Settings > Inline Mode > Turn on. Вы можете выбрать сообщение, которое будет показываться пользователям при использовании inline-режима. Обратите внимание, что сообщение будет видно всем пользователям бота, а не только курьерам, поэтому лучше написать там что-то нейтральное, например "...".

На этом базовая настройка бота закончена. 

Значения `EDITOR_ID` и `ADMINS_CHAT_ID` можно получить, к примеру, используя веб-версию Telegram: ID будут показываться в адресной строке, когда вы будете выбирать чаты. 

### Функционал бота (кратко)
Покупатель получает доступ к мини-приложению после отправки команды /start, курьер - /courier, администратор (редактор) - /editor. Режим курьера доступен только тем пользователям, которые добавлены в таблицу `staff` (автоматическое добавление происходит после добавления новых пользователей в группу с ID == `ADMINS_CHAT_ID`). Режим редактирования доступен только пользователю, чей ID указан в переменной `EDITOR_ID`.

Покупатель может выбирать товары, менять категории, лайкать товары. 

В магазине предусмотрены скидки на каждый товар по отдельности и персонально для каждого пользователя. На данный момент я сделал так, чтобы скидки суммировались. После успешной оплаты курьерам в ADMINS_CHAT_ID приходит оповещение о новом заказе. Курьер может принять заказ через inline меню или через мини-приложение. При каждом изменении статуса, покупателю будет приходить оповещение. Статус можно менять с шагом не более 1, т.е. не получится принять заказ и сразу завершить его, измения статусов последовательные. После того, как курьер завершит заказ, покупатель сможет оценить от ⭐×1 до ⭐×5 все товары в заказе, что потом повлияет на их позиционирование во вкладке "Рекомендации".  

Редактор магазина может добавить или удалить категории товаров, изменить их, назначить скидки по товару или отдельному пользователю. 

## ⚙️ Установка
### Зависимости
Для корректной работы данного проекта потребуется установить [Node.js](https://nodejs.org/en) и менеджер пакетов npm.
После установки, откройте папку с предварительно склонированным репозиторием при помощи VisualStudio Code или любого редактора, который удобен для вас. 
Проект использует сторонние пакеты для своей работы: 
* axios - для отправки запросов (Telegram Bot API),
* express - веб-сервер,
* body-parser - вспомогательный пакет для express для распарсивания тела запросов,
* cors - вспомогательный пакет для express для обработки [CORS](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing). Вы можете не устанавливать его, если в вашем приложении не будет CORS запросов,
* pg - реализует подключение к базе данных и выполнение всех запросов.
В консоли пропишите команды для установки зависимостей:
```
npm i axios
npm i express
npm i body-parser
npm i cors
npm i pg
```
Если вы хотите разместить данный проект на Vercel, пропишите команду
```
npm i -g vercel
```
После чего, разместить проект на Vercel можно будет при помощи команды:
```
vercel --prod
```
После развёртки приложения, вы получите URL, которые потребуется заполнить в конфиге. Можно добавить и свой домен, зайдя в свойства проекта > Domains.

## 📦 База данных
Таблицы, представления и функции в этом проекте предназначены для СУБД PostgreSQL. 

Для создания таблиц, представлений и функций, вам потребуется подключиться к вашей БД на PostgreSQL любым удобным для вас способом. Вероятно, самым удобным для вас будет подключиться при помощи [pgAdmin](https://www.pgadmin.org/).

Для того, чтобы подключиться к серверу БД с использованием pgAdmin, откройте и нажмите правой кнопкой мыши по Servers и выберите там Register > Server. В окне введите все данные для подключения: адрес сервера, имя пользователя, пароль и название базы данных. 

После сохранения и успешного подключения, вы увидите свою БД в списке. Нажмите по ней правой кнопкой мыши и выберите Query Tool. В Query Tool поочередно вставляйте содержимое файлов tables.sql, functions.sql, views.sql, расположенных в папке sql данного репозитория и нажимайте на Execute/Refresh (кнопка ▶️ сверху) или клавишу F5. После чего, вы можете добавить тестовые данные из examples.sql.
### Таблицы
![ERD](erd.png)
### Представления
В БД реализованы следующие представления:
* active_orders_v - отображает все незавершённые заказы
* available_to_leave_feedback_v - показывает список товаров, которые можно оценить, здесь все товары с завершённых заказов
* admins_v - список курьеров и администраторов системы
* cart_v - корзина пользователей
* sweets_v - все продукты
* categories_v - все категории
* sweets_categories_list_v - пара товар-категория. Если у товара несколько категорий, то будет несколько пар на каждую из категорий
* users_v - список всех пользователей, которые взаимодействовали с ботом, включая курьеров и администраторов
* recommendations_v - список товаров с индексом рекомендаций
* sweets_and_categories_v - то же самое, что и sweets_categories_list_v, но с полной информацией о товаре
* likes_v - товары, которые пользователи добавляли в избранное

### Функции
Данные функции реализованы на стороне СУБД, а вызываются посредством функций, хранящихся в файле databaseworker.js, таким образом в данном файле хранятся обёртки из данных функций.
| №  | Name                              |   | Аргументы                                                                                    | Вызывающая функция из databaseworker.js | Назначение                                                                      |
| -- | --------------------------------- | - | --------------------------------------------------------------------------------------- | ----------------------- | ------------------------------------------------------------------------------- |
| 1  | user_create_or_update             | F | id, first_name, last_name, username                                                     | CreateOrUpdateUser      | добавление или обновление информации о пользователе                             |
| 2  | category_create                   | F | name, description                                                                       | CreateCategory          | создание категории                                                              |
| 3  | order_update_payment_charge       | F | telegram_payment_charge_id, provider_payment_charge_id, orderid                         | ConfirmPayment          | подтверждение оплаты (OnSuccessfulPaytment)                                     |
| 4  | cart_items_getuser                | F | userid                                                                                  | GetCart                 | получить корзину пользователя                                                   |
| 5  | order_create                      | F | userid                                                                                  | CreateOrder             | создать заказ                                                                   |
| 6  | order_getid_byuserid              | F | userid                                                                                  | GetUserLastOrderId      | получить последний заказ пользователя                                           |
| 7  | order_update_address              | F | orderid, country_code, state, city, street_line1, street_line2, post_code, phone_number | SetOrderAdress          | обновление адреса заказа                                                        |
| 8  | staff_create                      | F | userid, staff_level                                                                     | AddStaff                | добавление курьера                                                              |
| 9  | staff_remove                      | F | userid                                                                                  | RemoveStaff             | удаление курьера                                                                |
| 10 | staff_check                       | F | userid                                                                                  | IsAdmin                 | проверка курьер ли                                                              |
| 11 | admins_v                          | V | \-                                                                                      | GetAdmins               | получение курьеров и админов                                                    |
| 12 | sweets_getall                     | F | userid                                                                                  | GetSweets               | получение списка всех товаров, с учётом лайков пользователя                     |
| 13 | sweets_v                          | V | \-                                                                                      | GetSweets               | получение списка всех товаров                                                   |
| 14 | likes_v_getsweets                 | F | userid                                                                                  | GetSweets               | получение списка понравившихся товаров по пользователю                          |
| 15 | get_recommendations               | F | userid                                                                                  | GetSweets               | получение списка рекомендаций для пользователя, с учётом лайков                 |
| 16 | sweets_and_categories_v_getsweets | F | categoryid, userid                                                                      | GetSweets               | получение списка по категориям                                                  |
| 17 | cart_add                          | F | idsweet, iduser, count                                                                  | AddToCart               | добавление в корзину                                                            |
| 18 | categories_v                      | V | \-                                                                                      | GetAllCategories        | получение списка доступных категорий                                            |
| 19 | orders_get_available              | F | idcourier                                                                               | GetAvailableOrders      | получение списка доступных курьеру заказов                                      |
| 20 | order_update_status               | F | idorder, idcourier, statusNew                                                           | UpdateOrderStatus       | обновление статуса заказа. Разница между статусами не должна быть больше одного |
| 21 | get_available_feedbacks           | F | iduser                                                                                  | GetAvailableFeedbacks   | получение списка товаров, доступных пользователю для оценки                     |
| 22 | order_items_list_update_score     | F | iduser, idsweet, score                                                                  | UpdateScore             | оценка товара пользователем                                                     |
| 23 | favorite_add                      | F | idsweet, iduser                                                                         | AddToFavorites          | добавление в избранное. Если уже есть - то удаление оттуда                      |
| 24 | sweet_create                      | F | name, cost, discount, wt, description, picture_base64, quantity, categories             | CreateSweet             | создание нового кондитерского изделия                                           |
| 25 | sweets_categories_list_v          | V | \-                                                                                      | GetCategoriesAndSweets  | получить список изделий с их категориями                                        |
| 26 | categories_list_add               | F | idsweet, idcategory                                                                     | AddCategoryToSweet      | добавить товару категорию                                                       |
| 27 | categories_list_remove            | F | idsweet, idcategory                                                                     | RemoveCategoryFromSweet | удалить категорию у товара                                                      |
| 28 | sweets_update                     | F | idsweet, cost, discount, quantity                                                       | UpdateSweet             | обновление информации о товаре                                                  |
| 29 | sweets_remove                     | F | idsweet                                                                                 | RemoveSweet             | удаление (только отметка об удалении) товара                                    |
| 30 | category_update                   | F | id, name, description                                                                   | UpdateCategory          | обновление информации о категории                                               |
| 31 | category_delete                   | F | id                                                                                      | DeleteCategory          | удаление категории (только отметка)                                             |
| 32 | users_v                           | V | \-                                                                                      | GetUsers                | получение списка пользователей                                                  |
| 33 | user_update_discount              | F | iduser, discount                                                                        | UpdateDiscount          | обновление скидки для пользователя (от 0 до 0.9)                                |

*P.S. Если я что-то забыл, выделяя таблицы, функции и представления в отдельные файлы, проверьте файл [full.sql](full.sql).*

## 🔌 Взаимодействие с API
Обработку API вызовов берут на себя файлы index.js и bot.js. bot.js работает с виртуальной директорией /bot, а index.js с `/api` (часть покупателя), `/admin-api` (редактор), `/courier-api` (курьерская часть). 

Учитывая это, требуется создать перенаправления данных директорий на соответствующие файлы. 

Если вы используете Vercel, то файл vercel.json должен выглядеть следующим образом:
```json
{
    "rewrites": [
        {
            "source": "/api/(.*)",
            "destination": "/api/index.js"
        },
        {
            "source": "/admin-api/(.*)",
            "destination": "/api/index.js"
        },
        {
            "source": "/courier-api/(.*)",
            "destination": "/api/index.js"
        },
        {
            "source": "/bot",
            "destination": "/api/bot.js"
        }
        ...
}
```

Через обращение к `/api/:type` вызываются следующие функции:
| :type | Вызываемая функция  | Аргументы                  |
|--------------|----------------------------------------|------------------------------------------|
| "sweets" (GET)    | GetSweets("", user.id)                 | В зависимости от наличия `idcategory`  |
|              | GetSweets(request.query.idcategory, user.id)|                                      |
| "categories" (GET) | GetAllCategories()                     | отс.                                      |
| "invoice" (GET)    | CreateInvoiceLink(user, prices)        | отс.                                      |
| "add_cart" (POST)  | AddToCart(idssweet, user.id, counts)   | `idssweet`, `user.id`, `counts`           |
| "like" (POST)       | AddToFavorites(request.body.idsweet, user.id) | `request.body.idsweet`, `user.id`  |

Через обращение к `/admin-api/:type`:
| :type | Вызываемая функция  | Аргументы                  |
|------------------------|-----------------------------------|----------------------------------|
| "getcategories" (GET)        | GetAllCategories()                 | отс.
| "sweets" (GET)               | GetSweets()                        | отс.
| "getsweetscategories" (GET)  | GetCategoriesAndSweets()           | отс.
| "getusers" (GET)             | GetUsers()                         | отс. 
| "getcategories" (GET)      | GetAllCategories()      | No conditions or arguments                                 |
| "addsweet" (POST)           | CreateSweet()           | name, cost, discount, wt, description, picture_base64, quantity, categories |
| "createcategory" (POST)    | CreateCategory()        | name, description                           |
| "sw_category_add" (POST)   | AddCategoryToSweet()    | idsweet, idcategory                         |
| "sw_category_remove" (POST) | RemoveCategoryFromSweet()| idsweet, idcategory                         |
| "updatesweet" (POST)       | UpdateSweet()           | idsweet, cost, discount, quantity          |
| "updatecategory" (POST)    | UpdateCategory()        | id, name, description                       |
| "updatediscount" (POST)    | UpdateDiscount()        | iduser, discount                            |
| "deletesweet" (POST)        | RemoveSweet()  | idsweet                                     |
| "deletecategory" (POST)    | DeleteCategory()        | id                                          |

Через обращение к `/courier-api/:type`
| :type  | Вызываемая функция      | Аргументы                                |
|----------------------|-----------------------|------------------------------------------------------------|
| "getavailableorders" (GET) | GetAvailableOrders() | user.id     |
| "updatestatus" (POST)     | UpdateOrderStatus()   | idorder, user.id, body.statusnew |

Все аргументы передаются в теле запроса, если это POST запрос, и в форме GET-параметров, если это GET запрос. Дополнительно в query-string после `:type` следует `telegramInitData`.

## 🗃️ Файлы
### index.js
Точка входа.

Для работы приложения требуется перенаправить на index.js входящие запросы `api/*`, `admin-api/*`
Каждый запрос здесь верифицируется при помощи функции `verifyTelegramWebAppData` (см. ниже), а запросы к `/admin-api` проверяются на соответствие с `EDITOR_ID` (см. secrets.js).

### bot.js
Обработчик для директории /bot, вебхук Telegram Bot API (если приходит POST запрос). Входящие запросы передаёт в файл TelegramEventsHandlers.js

Также обрабатывает установку вебхука по заданному домену (если обратится по адресу ваш_домен/bot).

### TelegramEventsHandlers.js
Содержит обработчики событий на сообщение, inline-поиск (это когда курьер будет искать свой заказ), нажатие кнопок (когда курьер будет менять статус заказа), запрос проверки перед оплатой (`OnPrecheckoutQuery`), запрос успешной оплаты (`OnSuccessfulPayment`), создание платёжной ссылки (`CreateInvoiceLink`), добавление/удаление курьеров при вступлении их в группу, оповещение курьеров при создании заказа (при его успешной оплате).

### TelegramWebApp.js
Содержит функции для проверки telegramInitData на валидность. Валидным telegramInitData считается тогда, когда совпадает переданный и рассчитанный хеши, а также, когда время авторизации (`auth_date`) не старее получаса.

### lightTeleAPI.js
Здесь я реализовал свои обёртки для вызова нужных мне функций Telegram Bot API (`answerInlineQuery`, `sendMessage`, `sendSticker`, `editMessageText`, `answerCallbackQuery`, `deleteMessage`, `createInvoiceLink`, `answerPreCheckoutQuery`, `setWebhook`).

### databaseworker.js
Здесь реализованы обёртки для всех функций и представлений, которые были перечислены в таблице выше. 

Все функции вызываются через `RunFunction (название_функции, [массив_аргументов])`, где для них генерируется Prepared Statement, а представления - через `SeeView(view_name)`.

### secrets.js
В данном файле содержатся `BOT_TOKEN` - токен бота, полученный через [@BotFather](https://t.me/BotFather), платёжный токен `PAYMENT_TOKEN`, ID редактора `EDITOR_ID` (только он сможет получать доступ к редактированию), `ADMINS_GROUP_CHATID` (ID группы, куда будет отправляться оповещение о новых заказах).

Остальные константы необходимо определить при помощи файла `.env` (или зайдя в проект > Settings > Environment Variables, если вы используете Vercel). 

Это следующие переменные:
* `PGHOST` - адрес для подключения к СУБД,
* `PGDATABASE` - название базы данных,
* `PGUSER` - имя пользователя,
* `PGPASSWORD` - пароль.

Самым лучшим вариантом будет сохранять все свои секретные данные в файле `.env` или подобных и не делиться с ними ни с кем.

Примерное содержание файла `.env`:
```env
BOT_TOKEN="123456:ABC-DEF1234ghIkl-zyx57W2v1u123ew11"
PAYMENT_TOKEN="333556:GMASDawttypp"
EDITOR_ID="4255555"
ADMINS_GROUP_CHATID="-1295900"

PGHOST="pgsql.panci.to"
PGDATABASE="pancitos_examples_database"
PGUSER="seller"
PGPASSWORD="44442300058"
```

Если вы используйте Vercel, Heroku или подобные решения, переменные окружения можно указать в Настройках проекта > Environment Variables.

После указания их в .env файле, файл secrets.js будет выглядеть следующим образом:
```js
const BOT_TOKEN = process.env.BOT_TOKEN;
const PAYMENT_TOKEN = process.env.PAYMENT_TOKEN;
const EDITOR_ID = process.env.EDITOR_ID;
const ADMINS_GROUP_CHATID = process.env.ADMINS_GROUP_CHATID;

...
```

### strings.js
В данном файле хранятся тексты сообщений на разных языках, которые отправляются пользователю. В переменной `DEFAULT_LANGCODE` хранится значение языка по умолчанию, который будет отправлен в том случае, если строки на запрашиваемом языке нет. Всегда нужно иметь строку на данной языке, иначе пользователю будет отправлено `undefined`.

### vercel.json
Файл для конфигурирования Vercel под данный проект, здесь перечислены перенаправления запросов на `index.js` и на `bot.js`. 

Для того, чтобы можно было открыть статичные файлы, там добавлены следующие записи:
```json
...
{
    "source": "/editor.js",
    "destination": "/editor/editor.js"
},
{
    "source": "/bootstrap.min_custom.css",
    "destination": "/app/bootstrap.min_custom.css"
},
{
    "source": "/app.js",
    "destination": "/app/app.js"
}
...
```

## app/index.html
Собственно главная страница магазина, содержит немного вёрстки с применением Bootstrap, сюда app.js будет добавлять сгенерированный HTML код для карточек товаров. 

При помощи переменных были переопределены некоторые CSS-классы, добавив тем кастомизацию интерфейса магазина под тему пользователя Telegram.

Помимо Bootstrap, необходимо подключить библиотеку jQuery и библиотеку для работы Telegram Web App.

## app/app.js
Здесь содержится вся логика работы мини-приложения: генерация кода карточек товаров, генерация кода для категорий, обработка нажатия кнопок для уменьшения/увеличения количества добавляемых товаров, лайк/анлайк товара. 

Из констант нужно поменять `API_ENDPOINT`, указав там адрес сервера, куда будут отправляться запросы. Если вы размещаете всё на одном сервере, укажите тот же самый, который указывали в файле `strings.js`. Также поменяйте `MIN_AMOUNT` и `MAX_AMOUNT` - это минимальная и максимальная суммы, которая принимается платёжным провайдером для обработки. Посмотреть значения для своих валют можно [здесь](https://core.telegram.org/bots/payments/currencies.json). 

Запросы отправляются на домен, который указан в константе `API_ENDPOINT`. К каждому запросу добавляется [telegramInitData](https://core.telegram.org/bots/webapps#webappinitdata) в query string для проверки.

## editor/index.html
Главная страница редактора магазина. Здесь аналогично index.html для пользовательской стороны. 

## editor/editor.js
Логика для генерации таблиц с товарами, вкладок и отработка нажатий на кнопки редактирования/удаления. 

Здесь можно удалить, отредактировать или добавить новый товар или категорию, изменить скидку для пользователя.  

Запросы отправляются на домен, который указан в константе `API_ENDPOINT` с добавлением `telegramInitData` в query string.

## courier/index.html
Главная страница для курьерской страницы. Аналогично всем остальным

## courier/courier.js
Логика генерации списка активным заказов. Каждый курьер видит только свои незавершённые и новые заказы.

Запросы отправляются на домен, который указан в константе `API_ENDPOINT` с добавлением `telegramInitData` в query string.


P.S. Вы можете сделать универсальную страничку для всех трёх ролей и со стороны бэкенда перенаправлять нужные группы пользователей на нужные страницы. Я сделаю это в ближайшем будущем 🤞.