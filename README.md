# Bonsai

Фамильный вики-движок и фотоальбом.

### [Демо: попробовать в действии](https://bonsai.kirillorlov.pro)

## Возможности

* Страницы с разметкой Markdown
* Медиа-файлы: фото, видео, планируется поддержка документов PDF
* Отметки людей на фото
* Родственные связи (с проверками и автоматическим выводом)
* Факты (дата рождения, пол, группа крови, владение языками, хобби, и так далее)
* Контроль доступа по ролям: администратор, редактор, читатель, гость
* История правок: для любой страницы или медиа-файла хранится история с diff'ами и возможностью отката к предыдущей версии

## Скриншоты

#### Публичные страницы:

<a href="https://user-images.githubusercontent.com/604496/46574247-037d4f00-c9a9-11e8-8585-0d574dda2600.png"><img src="https://user-images.githubusercontent.com/604496/46574252-1859e280-c9a9-11e8-821f-daeaaac7de3f.png" /></a>
<a href="https://user-images.githubusercontent.com/604496/46574259-2c054900-c9a9-11e8-8ecc-ca542053f665.png"><img src="https://user-images.githubusercontent.com/604496/46574288-9a4a0b80-c9a9-11e8-8373-2a7d3e00289c.png" /></a>
<a href="https://user-images.githubusercontent.com/604496/46574262-31629380-c9a9-11e8-9ea6-18fbe63f239f.png"><img src="https://user-images.githubusercontent.com/604496/46574291-9f0ebf80-c9a9-11e8-8656-8a54dd2f2be7.png" /></a>

#### Панель администратора:

<a href="https://user-images.githubusercontent.com/604496/46574266-3f181900-c9a9-11e8-828d-9d9a5db25acb.png"><img src="https://user-images.githubusercontent.com/604496/46574292-a209b000-c9a9-11e8-8193-cd99fc1f5f91.png" /></a>
<a href="https://user-images.githubusercontent.com/604496/46574268-43443680-c9a9-11e8-974f-f8a60fbeaa74.png"><img src="https://user-images.githubusercontent.com/604496/46574297-a504a080-c9a9-11e8-8612-d3e5cd1592a4.png" /></a>

## Установка с помощью Docker
1. Скачайте файл [docker-compose](docker-compose.yml).

2. _Опционально_: 

    Настройте доступ по HTTPS и внешнюю авторизацию для обеспечения максимальной безопасности ваших данных.
    Это трудоемкий шаг, поэтому если вы просто хотите попробовать Bonsai своими руками - его можно пропустить или отложить.

    Создайте [приложение авторизации Facebook](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/social/facebook-logins?view=aspnetcore-2.1&tabs=aspnetcore2x) (или Google, Yandex, Вконтакте).

    Отредактируйте файл `docker-compose.yml`:

    * Впишите данные для авторизации Facebook в поля `Auth__Facebook__AppId` и `Auth__Facebook__AppSecret`
    * Задайте настройку `Auth__AllowPasswordAuth=false` если хотите отключить менее безопасную авторизацию по паролю
    * Замените заглушку `@@YOUR_EMAIL@@` на свой адрес email (для автоматической генерации HTTPS-сертификата LetsEncrypt)
    * Замените заглушку `@@DOMAIN@@` на доменное имя (если у вас только IP-адрес, используйте xip.io, например `192.168.1.1.xip.io`)
    * Разкомментируйте две строки с ``Host(`@@DOMAIN@@`)``
    * Закомментируйте две строки с ``PathPrefix(`/`)`` 

3. Запустите все контейнеры с помощью `docker compose`:
   ```
   docker-compose up -d
   ```
4. После старта Bonsai будет доступен на портах 80 и 443.

## Разработка (на Windows)

Для участия в разработке понадобится:

* [.NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1): основной рантайм для Bonsai

1. Установите [NodeJS](https://nodejs.org/en/) (10+)
2. Установите [PostgreSQL server](https://www.openscg.com/bigsql/postgresql/installers.jsp/) (9.6+)
3. Скачайте [shared-сборку ffmpeg](https://ffmpeg.zeranoe.com/builds/) для вашей операционной системы и извлеките данные в папку `External/ffmpeg` в корне проекта (необходимы исполняемые файлы `ffmpeg` и `ffprobe`).
4. Создайте файл `appsettings.Development.json`, пропишите строку подключения к БД:

    ```
    {
      "ConnectionStrings": {
        "Database": "Server=127.0.0.1;Port=5432;Database=bonsai;User Id=<login>;Password=<password>;Persist Security Info=true"
      },
      "Auth": {
	    "AllowPasswordAuth": true
      } 
    }
    ```

5. _Опционально, но рекомендуемо_:

    Создайте [приложение авторизации Facebook](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/social/facebook-logins?view=aspnetcore-2.1&tabs=aspnetcore2x) (или Google, Yandex, Вконтакте).

	Впишите данные для авторизации в файл `appsettings.Development.json` и установите свойство `AllowPasswordAuth` в значение `false`:

	```
	{
	    "Auth": {
		    "AllowPasswordAuth": false,
		    "Facebook": {
			  "AppId": "<...>",
			  "AppSecret": "<...>" 
			},
			"Google": {
			  "ClientId": "<...>",
			  "ClientSecret": "<...>" 
			},
			"Yandex": {
			  "ClientId": "<...>",
			  "ClientSecret": "<...>" 
			},
			"Vkontakte": {
			  "ClientId": "<...>",
			  "ClientSecret": "<...>" 
			}
		}
	}
	```
    
6. Создайте базу данных:

    ```
    dotnet ef database update
    ```
7. Запустите сборку стилей и скриптов:

    ```
    npm install
    npm run build
    ```
8. Запустите приложение (из Visual Studio или через `dotnet run`).

## Безопасность

### Резервные копии данных

Если вам ценна информация, которую вы заносите в Bonsai, обязательно **НАСТРОЙТЕ РЕЗЕРВНОЕ КОПИРОВАНИЕ**.

Копировать необходимо следующие данные:

* Базу данных (десятки мегабайт)
* Загруженные медиа-файлы в папке `wwwroot/media` (могут быть гигабайты)

Существует множество подходов, платных и бесплатных: загрузка в облако, копирование на дополнительные носители и т.д.
Выбор наиболее уместного подхода, с учетом вашего бюджета и объема данных, остается за вами.

### Способы авторизации

Bonsai поддерживает 2 метода авторизации: OAuth с использованием внешних сайтов и авторизация по паролю.

OAuth является предпочтительным: он проще для пользователей, более безопасный и универсальный. **Если можете, используйте его!**
Для этого вам потребуется создать приложение авторизации на сайте [Facebook](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/social/facebook-logins?view=aspnetcore-3.0), [Google](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/social/google-logins?view=aspnetcore-3.0), [ВКонтакте](https://vk.com/editapp?act=create) или в [Яндексе](https://oauth.yandex.ru/client/new), как написано в инструкции.
Можно подключить несколько авторизационных приложений одновременно - пользователи смогут выбирать из них то, которое им больше по душе.

Также вы можете создать учетную запись с авторизацией по логину и паролю. Она пригодится в двух случаях:

* Быстро попробовать Bonsai в действии (установка без создания приложений значительно быстрее)
* Дать доступ родственникам, которые не зарегистрированы в соцсетях

Несколько фактов об авторизации, которые стоит иметь в виду:

* У одной учетной записи может быть только один способ авторизации: или пароль, или Facebook, или Google, и т.д.
* После создания учетной записи поменять тип авторизации нельзя.
* Учетные записи с авторизацией по паролю автоматически блокируются, если пароль был введен неверно слишком много раз подряд.
* Пароль может сменить только администратор вручную. Если у вас только одна учетная запись администратора и вы забыли от нее пароль - восстановить доступ можно только с помощью манипуляций с базой данных!