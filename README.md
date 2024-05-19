W projekcie zaimplementowano kilka mikroserwisów, które obejmują moduły e-commerce takie jak Katalog, Koszyk, Zniżki oraz Zamówienia. Wykorzystano bazy danych NoSQL (DocumentDb, Redis) oraz bazy relacyjne (PostgreSQL, SQL Server), a mikroserwisy komunikują się za pomocą RabbitMQ Event Driven Communication i korzystają z Yarp API Gateway.

![image](https://github.com/tomszymanski/microservices/assets/101809461/d9561313-294d-40b0-bc8b-5d84a1d29baa)


Co zawiera to repozytorium:
Mikroserwis Katalogu
  Minimalne API ASP.NET Core i najnowsze funkcje .NET8 oraz C# 12
  Implementacja Vertical Slice Architecture z folderami funkcji i pojedynczym plikiem .cs zawierającym różne klasy
  Implementacja CQRS przy użyciu biblioteki MediatR
  Walidacja CQRS za pomocą MediatR i FluentValidation
  Wykorzystanie biblioteki Marten do .NET Transactional Document DB na PostgreSQL
  Wykorzystanie Carter do definicji punktów końcowych Minimal API
  Zagadnienia przekrojowe: logowanie, globalne obsługiwanie wyjątków i kontrola stanu zdrowia

Mikroserwis Koszyka
  Aplikacja ASP.NET 8 Web API, zgodna z zasadami REST API, CRUD
  Wykorzystanie Redis jako rozproszonej pamięci podręcznej dla bazy danych koszyka
  Implementacja wzorców Proxy, Decorator i Cache-aside
  Wykorzystanie usługi Grpc do zniżek do synchronizacji komunikacji między usługami w celu obliczenia ostatecznej ceny produktu
  Publikowanie kolejki BasketCheckout z użyciem MassTransit i RabbitMQ

Mikroserwis Zniżek
  Aplikacja serwera Grpc ASP.NET
  Budowanie wysoko wydajnej komunikacji między usługami gRPC z mikroserwisem Koszyka
  Udostępnianie usług Grpc poprzez tworzenie wiadomości Protobuf
  Entity Framework Core ORM — SQLite Data Provider i migracje do uproszczenia dostępu do danych i zapewnienia wysokiej wydajności
  Połączenie do bazy danych SQLite i konteneryzacja

Komunikacja między mikroserwisami
  Synchronizacja komunikacji między usługami gRPC
  Asynchroniczna komunikacja mikroserwisów z użyciem RabbitMQ Message-Broker Service
  Wykorzystanie RabbitMQ Publish/Subscribe Topic Exchange Model
  Wykorzystanie MassTransit do abstrakcji nad systemem RabbitMQ Message-Broker
  Publikowanie kolejki zdarzeń BasketCheckout z mikroserwisu Koszyka i subskrybowanie tego zdarzenia przez mikroserwis Zamówień
  Tworzenie RabbitMQ EventBus.Messages library i dodawanie odniesień do mikroserwisów

Mikroserwis Zamówień
  Implementacja DDD, CQRS i Clean Architecture zgodnie z najlepszymi praktykami
  Rozwój CQRS z użyciem pakietów MediatR, FluentValidation i Mapster
  Konsumowanie kolejki zdarzeń BasketCheckout za pomocą konfiguracji MassTransit-RabbitMQ
  Połączenie do bazy danych SqlServer i konteneryzacja
  Wykorzystanie Entity Framework Core ORM i automatyczna migracja do SqlServer przy starcie aplikacji

Mikroserwis Yarp API Gateway
  Tworzenie API Gateway z Yarp Reverse Proxy stosując wzorzec Gateway Routing
  Konfiguracja Yarp Reverse Proxy; Route, Cluster, Path, Transform, Destinations
  Ograniczenie przepustowości z FixedWindowLimiter na konfiguracji Yarp Reverse Proxy

Mikroserwis WebUI ShoppingApp
  Aplikacja internetowa ASP.NET Core z Bootstrap 4 i szablonem Razor
  Wywoływanie API Yarp z Refit HttpClientFactory

Ustanowienie Docker Compose ze wszystkimi mikroserwisami na dockerze
  Konteneryzacja mikroserwisów
  Konteneryzacja baz danych
  Nadpisanie zmiennych środowiskowych
  Uruchomienie projektu


Będziesz potrzebować następujących narzędzi:
  Visual Studio 2022
  .Net Core 8 lub nowszy
  Docker Desktop

Instalacja
Postępuj zgodnie z tymi krokami, aby skonfigurować swoje środowisko programistyczne (przed uruchomieniem uruchom Docker Desktop):

Sklonuj repozytorium
Po zainstalowaniu Docker dla Windows przejdź do Settings > Advanced z ikony Docker w zasobniku systemowym, aby skonfigurować minimalną ilość pamięci i CPU w następujący sposób:
Pamięć: 4 GB
CPU: 2
W katalogu głównym rozwiązania wybierz docker-compose i ustaw jako projekt startowy. Uruchom docker-compose bez debugowania w Visual Studio. Możesz również przejść do katalogu głównego, który zawiera pliki docker-compose.yml, i uruchomić poniższe polecenie:
docker-compose -f docker-compose.yml -f docker-compose.override.yml up -d

Poczekaj na uruchomienie wszystkich mikroserwisów przez docker-compose. To wszystko! (niektóre mikroserwisy potrzebują więcej czasu na uruchomienie, więc proszę poczekać, jeśli nie działają od razu)
Uruchom Shopping Web UI -> https://localhost:6065 w przeglądarce, aby zobaczyć stronę główną. Możesz użyć projektu Web, aby wywołać mikroserwisy za pomocą Yarp API Gateway. Po zatwierdzeniu koszyka możesz śledzić rekord kolejki na pulpicie RabbitMQ.
