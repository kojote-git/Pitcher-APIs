# Pitcher APIs
Бэкенд Pitcher состоит из трёх серверов. Scraper-API отвечает за сбор информации с различных онлайн сервисов, таких как Play Store, Google Trends, Twitter, новостные порталы. ML-API проводит обработку естественного языка и даёт представление о мнении людей, основываясь на тексте. Ananlyser-API отвечает на запросы фронтенда и мобильного приложения.

Analyser-API endpoints:

1. http://localhost:5080/registration (метод **POST**). 
    Принимает тело запроса следующего формата: 
    ```json
    {
         "username": "", 
         "password": "", 
         "email": "", 
         "fullname": "".
         "isCompany": true/false
    }
    ```
    
    company_name не обязателен. userType указывает кем является пользователь:
    
    0) пользователь-любитель, который работает с сервисом не от компании
    
    1) пользователь, устроенный в компанию
    
    2) пользователь-компания

    Возвращает, если всё правильно:
    ```json
    {
        "response": true,
        "message": "User <> was created",
        "access_token": "",
        "refresh_token": ""
    }
    ```
    
    Возвращает, если уже существует:
    ```json
    {
        "response": false,
        "message": "User <> already exists"
    }
    ```

    Если, ошибка на стороне сервера:
    ```json
    {
        "response": false, 
        "message": "Something went wrong"
    }
    ```

2. http://localhost:5080/login (метод **POST**). 
    Принимает тело запроса следующего формата: 
    ```json
    {
         "user": "", //email
         "user_password": ""
    }
    ```
    user - email пользователя.
    
    Возвращает, если всё правильно:
    ```json
    {
        "response": true,
        "message": "Logged in as <>",
        "access_token": "",
        "refresh_token": ""
    }
    ```

    Возвращает, если не существует:
    ```json
    {

          "response": false,
          "message": "User <> doesn't exist"

    }
    ```
    
    Если, ошибка на стороне сервера:
    ```json
    {
        "response": false, 
        "message": "Something went wrong"
    }
    ```
    
3. http://localhost:5080/logout/access (метод: **POST**)
    
    Добавляет токен в чёрный список.
    
    Нужно передать в хэдере запроса access token.
    
    Возвращает, если токен существует и не в чёрном списке:
    ```json
    {
        "response": true, 
        "message": "Access token has been revoked"
    }
    ```
    
    Если, ошибка на стороне сервера:
    ```json
    {
        "response": false, 
        "message": "Something went wrong"
    }
    ```
    
4. http://localhost:5080/logout/refresh (метод: **POST**)
    
    Добавляет токен обновления в чёрный список.
    
    Нужно передать в хэдере запроса refresh token.
    
    Возвращает, если токен существует и не в чёрном списке:
    ```json
    {
        "response": true, 
        "message": "Refresh token has been revoked"
    }
    ```
    
    Если, ошибка на стороне сервера:
    ```json
    {
        "response": false, 
        "message": "Something went wrong"
    }
    ```
    
5. http://localhost:5080/token/refresh (метод: **POST**)
    
    Нужно передать в хэдере запроса refresh token.
    ```json
    {
        "access_token": ""
    }
    ```
6. http://localhost:5080/users (метод: **GET**)
    
    Возвращает краткую информацию по всем пользователям.
    
    Возвращает если всё в порядке:
    ```json
    {
        "users": [
            {
                "username": "",
                "fullname": "",
                "isCompany": ""
            },...
        ]
    }
    ```
    
7. http://localhost:5080/users (метод: **DELETE**)
    
    Удаляет всех пользователей.
    
    Возвращает если всё в порядке:
    ```json
        "response": true,
        "message": "<> row(s) deleted"
    ```
    
    Если, ошибка на стороне сервера:
    ```json
    {
        "response": false, 
        "message": "Something went wrong"
    }
    ```

8. http://localhost:5080/oauth/login (метод **GET**)

    Инициирует логин через сторонние сервисы.
    
    Нужно передать аргумент provider, который указывает через какой сервис залогинить.
    
    Если логин через Google: http://localhost:5080/oauth/login?provider=google
    
    Если логин через Facebook: http://localhost:5080/oauth/login?provider=facebook

9. http://localhost:5080/oauth/facebook/callback?provider=facebook (метод **GET**)

    Получает данные с Facebook об аккаунте, с которого залогинились.
    
    Возвращает такие же данные как и **/login**.
    
10. http://localhost:5080/oauth/google/callback?provider=google (метод **GET**)

    Получает данные с Google об аккаунте, с которого залогинились.
    
    Возвращает такие же данные как и **/login**.
   
11. http://localhost:5080/update_or_delete_user (метод **DELETE**)

    В хедере нужно передать токен доступа. Удаляет аккаунт пользователя.
    
12. http://localhost:5080/update_or_delete_user (метод **PUT**)

    Редактирует аккаунт. Нужен токен доступа. Принимает на вход:
    
    ```json
    {
        "username": "",
        "fullname": "",
        "bio": ""
    }
    ```
    
    Все поля не обязательны. Также можно передавать файл с аватаркой пользователя.

13. Создание исследования (метод **POST**)

    Для создания исследования передай на http://localhost:5080/research/use следующий json:
    ```json
    {
        "topic": "", //обязательно
        "description": "", //необязательно
        "keywords": ["","","",...],
        "modules": ["search", "play_store", "news", "twitter"], //передавать только комбинации из этих вариантов
        "update_interval": "",
        "app_id": "",
        "app_name": "",
        "app_dev": "",
        "isPublic": true/false,
        "analysers": "vader"/"polyglot"
    }
    ```
    Возвращает:
    
    ```json
    {
        "response": true,
        "id": int,
        "message": "Research <> was created"
    }
    ```

14. Получение инфы об исследовании (метод **GET**)

    http://localhost:5080/research/use?res_id={}
    
    ```json
    {
        "id": int,
        "topic": "",
        "description": "",
        "creation": "",
        "last_update": "",
        "views": int,
        "owner": {
            "id": int,
            "username": "",
            "fullname": ""
        },
        "keywords": [],
        "active_modules": [],
        "likes": int,
        "subscriptions": int
    }
    ```
15. Поиск исследований (метод **GET**)

    Получаешь по ссылке, где все параметры необязательны кроме **keyword**: http://localhost:5080/research/search?keyword={}&sorting=creation/last_update/views/popularity/subscribers&start_date={dd.mm.YYYY}&end_date={dd.mm.YYYY}&analyser=vader/polyglot&isCompany=0/1&modules=play_store/search/twitter/news
    
    Возвращает:
    
    ```json
        {
            "response": true,
            "results": [
                {
                    "id": int,
                    "topic": "",
                    "description": "",
                    "creation": "dd.mm.YYYY",
                    "views": int,
                    "likes": int,
                    "subscriptions": int
                },...
            ]
        }
    ```

16. Лайки (поставить, получить пролайканые нынешним пользователем, удалить лайк) (методы: **POST**, **GET**, **DELETE**)

    Ссылка: http://localhost:5080/research/like
    
  Для delete и post передай json: ```json {"research_id": int} ```.
  
  Тоже самое для подписок, но url меняется на http://localhost:5080/research/subscribe
