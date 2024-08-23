## 1. Что такое [модули в NestJS](https://docs.nestjs.com/modules#feature-modules)?

### 1.1 Основы модулей

Модули являются основными строительными блоками приложения NestJS. 
Каждый модуль инкапсулирует функциональность, объединяя связанные компоненты, такие как провайдеры, контроллеры, сущности, и другие модули. 
Модули помогают организовать код, делая его более модульным, понятным и легко масштабируемым.

Каждое приложение на NestJS состоит как минимум из одного модуля — `AppModule`, 
который является корневым модулем. 
Отдельные модули создаются для конкретных функциональных областей приложения, 
что позволяет разделять и изолировать различную логику.

### 1.2 Основные части модуля

Модуль в NestJS — это класс, помеченный декоратором `@Module()`. 
Этот декоратор принимает объект конфигурации, который включает:

- **imports**: массив модулей, которые необходимы для работы данного модуля. Эти модули делаются доступными в текущем модуле.
- **controllers**: массив контроллеров, которые обрабатывают HTTP-запросы и возвращают ответы клиенту.
- **providers**: массив провайдеров, которые предоставляют сервисы и другую функциональность, используемую внутри модуля.
- **exports**: массив провайдеров, которые должны быть доступны за пределами модуля, чтобы другие модули могли их использовать.

### Пример простого модуля

```typescript
import { Module } from '@nestjs/common';
import { UsersController } from './users.controller';
import { UsersService } from './users.service';

@Module({
  imports: [],
  controllers: [UsersController],
  providers: [UsersService],
  exports: [UsersService],
})
export class UsersModule {}
```
### 1.3 Схема модулей приложения bookstall
<img src="https://production-it-incubator.s3.eu-central-1.amazonaws.com/file-manager/Image/e0634ded-b021-4f7a-b883-abe22aedd788_webirar-modules.png" alt="modules" width="900" height="350">

На схеме изображены:
 - корневой `AppModule`
 - 2 модуля из npm пакета (библиотека), 
 - `UsersModule`, в котором инкапсулирована логика по работе с Users
 - `BooksModule`, который мы должны реализовать ;)

# 2. [Провайдеры в nestjs](https://docs.nestjs.com/providers) и трехслойная архитектура

### 2.1 Что такое провайдеры?

Провайдеры в NestJS — это классы, 
которые NestJS может инжектировать в другие классы с использованием механизма
Dependency Injection (DI). 
Провайдером может быть любой класс, помеченный декоратором `@Injectable()`. 
Провайдеры служат для инкапсуляции бизнес-логики, 
доступа к данным или предоставления других сервисов, необходимых приложению.

### 1.3 Инъекция зависимостей
Nestjs автоматически создает экземпляры классов, которые помечены декоратором `@Injectable()`
и добавляет в конструктор аргументы в нужной последовательности.

```typescript
const userRepository = new UsersRepository();

const userService = new UsersService(userRepository);

const usersController = new UsersController(userService);
```

Нам надо лишь зарегистрировать провайдеры в массиве providers в модуле, как показано выше.

## 3. Трехслойная архитектура
   Трехслойная архитектура — это способ организации кода, который делит приложение на три 
   четко определенных слоя. 
   Эти слои взаимодействуют между собой по четко определенным интерфейсам, 
   что способствует разделению ответственности, улучшает тестируемость и поддерживаемость кода.

<img src="https://production-it-incubator.s3.eu-central-1.amazonaws.com/file-manager/Image/df5399a2-c909-4696-a23e-048e217ccb50_layers_arch.png" alt="3_layers" width="800" height="250">

### 2.1 Presentation Layer (Слой представления)
Слой представления отвечает за обработку входящих запросов и возврат ответов пользователю. 
В контексте NestJS этот слой представлен контроллерами (Controllers).

Контроллеры: Принимают HTTP-запросы от клиентов (например, от веб-приложений или мобильных приложений), 
вызывают соответствующие методы сервисов для выполнения бизнес-логики и возвращают результаты клиенту.

### 2.2 Business Logic Layer (Слой бизнес-логики)
Слой бизнес-логики реализует основную бизнес-логику приложения. 
Этот слой включает в себя сервисы (Services), которые инкапсулируют правила и процессы,
специфичные для вашего приложения. Например, при добавлении новой 
книги надо проверить не нарушаются ли авторские права или не нарушаются ли возрастные ограничения.

### 2.3 Data Access Layer (Слой доступа к данным)
Слой доступа к данным отвечает за взаимодействие с базой данных или другими источниками данных. 
Этот слой реализуется через репозитории (Repositories).

Репозитории: Управляют взаимодействием с базой данных, предоставляя методы для выполнения CRUD операций (создание, чтение, обновление, удаление данных).

### 2.4 Взаимодействие между слоями
В трехслойной архитектуре каждый слой имеет четко определенную роль и взаимодействует с другими слоями через четко определенные интерфейсы:

 - Контроллеры (Presentation Layer) принимают запросы и передают их на обработку в сервисы (Business Logic Layer).
 - Сервисы (Business Logic Layer) реализуют бизнес-логику и взаимодействуют с репозиториями (Data Access Layer) для получения и сохранения данных.
 - Репозитории (Data Access Layer) выполняют операции с данными и возвращают их обратно в сервисы.  

Этот подход упрощает тестирование, 
расширение и поддержку приложения, так как каждый слой имеет свою четкую ответственность и 
может быть изменен независимо от других слоев.

