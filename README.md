# AllegroPay Services

Projekt utworzony na potrzeby zadania rekrutacyjnego do AllegroPay.

### Wymagania

1. Utwórz otwarte repozytorium na GitHub zawierające rozwiązanie zadania
2. Zintegruj projekt na GitHub z Azure DevOps tak, aby można było korzystać z Azure Pipelines do budowania i walidacji projektu
3. Udostępnij adres repozytorium do Allegro

### Cele

1. W repozytorium utwórz 3 serwisy .NET Core 3.1 (WebApi) typu ‘hello world’
    - Imie.Nazwisko.Service1
    - Imie.Nazwisko.Service2
    - Imie.Nazwisko.Service3

(usługi mają nie mieć włączonych żadnych zabezpieczeń jak CORS, SSL itp, mają się jedynie uruchomić i zwrócić WeatherForecast tak, jak w oficjalnym tutorial’u - https://docs.microsoft.com/en-us/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.1&tabs=visual-studio)

2. Do każdego serwisu utwórz projekt testowy (XUnit): Imie.Nazwisko.Service[i].Tests

3. Zbuduj CI pipeline za pomocą którego:
    - Będzie można walidować wszystkie Pull requesty * -> master
    - Będzie można zbudować i przetestować całą solucję ilekroć coś zostanie zmerge’owane do branch’a master
    - Będzie można skonteneryzować całą solucję i wyemitować obrazy kontenerów do hub.docker.co

## Realizacja
### Github

Pierwszym eementem jaki został wykonany, to zostało utworzone publiczne repozytorium na github na którym został umieszczony cały kod źródłowy. Cały kod został napisany w Visual Studio 2022 Community i z nim zostało zintegrowane repozytorium github.

### Azure DevOps Server

Kolejnym krokiem było zintegrowanie repozytorium github z Azure DevOps Server. Posiadałem już konto na platformie ADS, dlatego wystarczyło tylko utworzyć osobny projekt i przejść do utworzenia i konfiguracji Pipeline 



