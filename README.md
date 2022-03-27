# AllegroPay Services

Projekt utworzony na potrzeby zadania rekrutacyjnego do AllegroPay. 

***Na wstępie chciałem zaznaczyć iż w historii commit'ów jest lekki bałagan z racji dużej ilości różnych testów całości rozwiązania. Nie chciałem tworzyć repo na nowo, aby można było prześledzić przebieg tworzenia całości rozwiązania zadania.***

### Wymagania

1. Utwórz otwarte repozytorium na GitHub zawierające rozwiązanie zadania
2. Zintegruj projekt na GitHub z Azure DevOps tak, aby można było korzystać z Azure Pipelines do budowania i walidacji projektu
3. Udostępnij adres repozytorium do Allegro

### Cele

1. W repozytorium utwórz 3 serwisy .NET Core 3.1 (WebApi) typu ‘hello world’
    - Imie.Nazwisko.Service1
    - Imie.Nazwisko.Service2
    - Imie.Nazwisko.Service3

(usługi mają nie mieć włączonych żadnych zabezpieczeń jak CORS, SSL itp, mają się jedynie uruchomić i zwrócić WeatherForecast tak, jak w oficjalnym tutorial’u - [.NET Core WebApi Tutorial](https://docs.microsoft.com/en-us/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.1&tabs=visual-studio)

2. Do każdego serwisu utwórz projekt testowy (XUnit): Imie.Nazwisko.Service[i].Tests

3. Zbuduj CI pipeline za pomocą którego:
    - Będzie można walidować wszystkie Pull requesty * -> master
    - Będzie można zbudować i przetestować całą solucję ilekroć coś zostanie zmerge’owane do branch’a master
    - Będzie można skonteneryzować całą solucję i wyemitować obrazy kontenerów do hub.docker.co

## Realizacja
### Github

Pierwszym krokiem jaki został wykonany, to utworzenie publicznego repozytorium github, na którym został umieszczony cały kod źródłowy. Cały kod został napisany w Visual Studio 2022 Community i z nim zostało zintegrowane repozytorium github.

### Azure DevOps Server

Kolejnym krokiem było zintegrowanie repozytorium github z Azure DevOps Server. Posiadałem już konto na platformie ADS, dlatego wystarczyło tylko utworzyć osobny projekt i przejść do utworzenia pipeline, wykorzystując do tego utworzone odpowiednie pliki YAML znajdujące się w repozytorium GitHub. 

#### Walidacja Pull Requests

Pierwszym krokiem do poprawnej walidacji Pull Request jest zabezpieczenie przed wbijaniem jakichkolwiek zmian bezpośrednio do gałęzi `master`. Dzięki czemu nanoszenie zmian na główny branch możliwe będzie tylko przy wydzieleniu feature branch'a i utworzeniu Pull Request'a.  

Następnie w pliku pr-validation-pipelines.yml dodane zostały triggery, które powodują, że pipeline nie reaguje na wbicie bespośrednie do brancha `master`, ale reaguje  na utworzenie PR do wspomnianej głównej gałęzi.

    pr:
    - master
    trigger: none

#### Wyzwolenie pipeline przy każdym merge'u do gałęzi `master`

Drugim plikiem jest plik azure-pipelines.yml zawierający już właściwy pipeline wraz z ponownym zbudowaniem i przetestowaniem solucji oraz koneneryzacją serwisów i umieszczeniem ich w [repozytorium docker hub](https://hub.docker.com/repository/docker/pawelgromala/allegro_pay)

    trigger: 
      - master
    pr: none

### Docker/Docker-Compose

Celem skonteneryzowania serwisów utworzono pliki Dockerfile w każdym z projektów:

    FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build-env
    WORKDIR /app

    # Copy csproj and restore as distinct layers
    COPY *.csproj ./
    RUN dotnet restore

    # Copy everything else and build
    COPY . ./
    RUN dotnet publish -c Release -o out

    # Build runtime image
    FROM mcr.microsoft.com/dotnet/aspnet:6.0
    WORKDIR /app
    COPY --from=build-env /app/out .
    ENTRYPOINT ["dotnet", "Pawel.Gromala.Service1.dll"]

Na potrzeby projektu został również utworzony plik docker-compose.yml celem uruchamiania wszystkich serwisów z odpowiednią konfiguracją i wystawieniem portów. Plik ten został zamieszczony w lokalizacji Docker/docker-compose.yml, a zawartość przedstawia się jak poniżej:


    version: '3.3'

    services:
        Pawel.Gromala.Service1:
            image: pawelgromala/allegro_pay:latest_service1
            container_name: allegro_pay_service1
            restart: unless-stopped
            ports:
                - 8001:80

        Pawel.Gromala.Service2:
            image: pawelgromala/allegro_pay:latest_service2
            container_name: allegro_pay_service2
            restart: unless-stopped
            ports:
                - 8002:80

        Pawel.Gromala.Service3:
            image: pawelgromala/allegro_pay:latest_service3
            container_name: allegro_pay_service3
            restart: unless-stopped
            ports:
                - 8003:80
