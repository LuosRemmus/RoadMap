<!DOCTYPE html>
<html lang="en">
  <head>
    <style>
      h1 {
        color: white;
        font-size: 36px;
      }
      h1:hover{
        color: red;
      }
      h2 {
        color: white;
        font-size: 24px;
      }
      h2:hover{
        color: red;
      }
      h3 {
        color: white;
        font-size: 18px
      }
      h3:hover {
        color: red
      }
      h4{
        color: white;
        font-size: 16px
      }
      h4:hover{
        color: red
      }
      li{
        color: white;
        font-size: 18px;
        font-weight: bold
      }
      li:hover{
        color: red;
      }
      b:hover{
        color: red
      }
      i{
        color: green
      }
      .text{
        font-size: 14px;
        font-weight: normal
      }
    </style>
  </head>
  <body>
    <h1>RoadMap</h1>
    <div>
      <h2>Оглавление</h2>
      <ul>  
        <li>Docker</li>
          <ul>
            <li>Краткие сведения</li>
            <li>Преимущества и недостатки</li>
            <li>Аналоги</li>
            <li>Основные команды</li>
            <li>Dockerfile</li>
              <ul>
                <li>Краткие сведения</li>
                <li>Основные команды</li>
                <li>Пример файла</li>
              </ul>
            <li>docker-compose.yml</li>
              <ul>
                <li>Краткие сведения</li>
                <li>Основные команды</li>
                <li>Основные поля</li>
                <li>Основные технологии для развертывания</li>
                  <ul>
                    <li>Nats</li>
                    <li>PostgreSQL</li>
                    <li>PGAdmin</li>
                    <li>Selery</li>
                    <li>Redis</li>
                    <li>RabbitMQ</li>
                    <li>ElasticSearch</li>
                  </ul>
                <li>Деплой приложения</li>
              </ul>
            <li>Список источников</li>
          </ul>
        <li>PostgreSQL</li>
          <ul>
            <li>Краткие сведения</li>
            <li>Преимущества и недостатки</li>
            <li>Аналоги</li>
            <li>Основные команды</li>
            <li>SQL-запросы</li>
              <ul>
                <li>TBC</li>
              </ul>
            <li>Взаимодействие с Python</li>
              <ul>
                <li>SQLAlchemy</li>
                  <ul>
                    <li>ORM</li>
                    <li>Mapped, mapped_column()</li>
                    <li>Declarative Base, Metadata</li>
                    <li>Из каких библиотек всё брать</li>
                  </ul>
                <li>psycopg2/asyncpg</li>
                <li>alembic</li>
                  <ul>
                    <li>revision</li>
                  </ul>
              </ul>
          </ul>
        <li>Nats</li>
          <ul>
            <li>Краткие сведения</li>
            <li>Преимущества и недостатки</li>
            <li>Аналоги</li>
            <li>Основные команды</li>
            <li>Взаимодействие с Python</li>
          </ul>
        <li>Selery</li>
          <ul>
            <li>Краткие сведения</li>
            <li>Преимущества и недостатки</li>
            <li>Аналоги</li>
            <li>Основные команды</li>
            <li>Взаимодействие с Python</li>
          </ul>
        <li>Redis</li>
          <ul>
            <li>Краткие сведения</li>
            <li>Преимущества и недостатки</li>
            <li>Аналоги</li>
            <li>Основные команды</li>
            <li>Взаимодействие с Python</li>
          </ul>
        <li>ElasticSearch</li>
          <ul>
            <li>Краткие сведения</li>
            <li>Преимущества и недостатки</li>
            <li>Аналоги</li>
            <li>Основные команды</li>
            <li>Взаимодействие с Python</li>
          </ul>
        <li>Git</li>
          <ul>
            <li>Краткие сведения</li>
            <li>Преимущества и недостатки</li>
          </ul>
        <li>Linux</li>
          <ul>
            <li></li>
          </ul>
        <li>FastAPI</li>
          <ul>
            <li>Краткие сведения</li>
            <li>Преимущества и недостатки</li>
            <li>Аналоги</li>
            <li>Основные команды</li>
            <li>Взаимодействие с Python</li>
          </ul>
        <li>REST API</li>
          <ul>
            <li></li>
          </ul>
        <li>Python</li>
          <ul>
            <li>Краткие сведения</li>
            <li>Преимущества и недостатки</li>
            <li>Виртуальное окружение</li>
              <li>Краткие сведения</li>
              <li>Основные команды</li>
            <li>ООП</li>
              <ul>
                <li>Определение</li>
                <li>Назначение</li>
                <li>Основные концепции</li>
                  <ul>
                    <li>Наследование</li>
                    <li>Полиморфизм</li>
                    <li>Инкапсуляция</li>
                  </ul>
                <li>Дандер методы</li>
              </ul>    
          </ul>
      </ul>
    </div>
    <div id="docker" class="container">
      <h2>Docker</h2>
      <div class="content">
        <h3>Краткие сведения</h3>
        <div class="text">
          <p><b>Docker</b> - программное обеспечение для автоматизации развёртывания и управления приложениями в среде виртуализации на уровне операционной системы; позволяет «упаковать» приложение со всем его окружением и зависимостями в контейнер, а также предоставляет среду по управлению контейнерами.</p>
          <p><i>Простым языком</i>:<br><b>Docker</b> - это инструмент, который позволяет разработчикам, системными администраторам и другим специалистам деплоить их приложения в песочнице (которые называются контейнерами), для запуска на целевой операционной системе, например, <b>Linux</b>.</p>
        </div>
      <h3>Преимущества и недостатки</h3>
      <div class="text">
        <h4>Преимущества</h4>
        <p><ul>
          <li class="text"></li>
        </ul></p>
      </div>
      </div>
    </div>
  </body>
</html>