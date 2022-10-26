---
title: Smarthome
subtitle: Raport z kamienia milowego na dzień 28 października 2022
author:
    - Marcin Retajczyk
    - Kamila Skorupka
    - Piotr Sokołowski
---

# Opis projektu

<!-- Dokładny opis realizowanego projektu, jego zakres, przykłady zastosowania, planowane funkcjonalności (przypadki użycia), porównanie z istniejącymi rozwiązaniami, itp. -->

Realizowany przez nas projekt zakłada powstanie systemu inteligentnego domu, obsługującego odczyt danych z czujników, jak i sterowanie podłączonymi urządzeniami. Planujemy umożliwienie automatyzacji niektórych czynności, wzorując się na aplikacji IFTTT:

![IFTTT - dodawanie nowego apletu](ifttt.png)

Proponowane przez nas rozwiązanie pozwalałoby na niemal dowolną konfigurację automatyzacji domu, przez ustawienie w pierwszej sekcji (If This) progu dla wartości odczytanej z danego czujnika oraz akcji dla podpiętego urządzenia w sekcji drugiej (Then That).

Zakładamy, że cały system będzie utrzymywany na naszych serwerach. Chcemy jednak dopuścić do użytku urządzenia stworzone przez użytkowników. W związku z tym, przy konfiguracji czujników w aplikacji, użytkownik sam wybierze, jakie dane otrzymuje i na jakich tematach MQTT. Możliwe będzie więc dodanie wielu czujników obsługiwanych przez jedno urządzenie.

![Diagram przypadków użycia - odczyty z czujnika](schematy/odczyty-z-czujnikow.png)

![Diagram przypadków użycia - akcje użytkownika](schematy/akcje-uzytkownika.png)

![Diagram przypadków użycia - sterowanie urządzeniami](schematy/sterowanie-urzadzeniami.png)

Najpopularniejszym rozwiązaniem tego typu jest Google Home, który pozwala na integrację urządzeń obsługiwanych przez inne systemy. Ma jednak ograniczoną ilość rodzajów urządzeń, przez co przykładowo czujnik temperatury i wilgotności musi być dodany jako termostat. Pojawia się wtedy informacja o ustawionym chłodzeniu, chociaż urządzenie w rzeczywistości takiej funkcjonalności nie posiada:

![Czujnik DHT22 dodany do Google Home jako termostat](ghome.png)

# Podział na moduły

<!-- Podział projektu na moduły oraz interfejsy pomiędzy poszczególnymi modułami. Moduły powinny być tak zaplanowane, żeby reprezentowały dobrze wydzieloną część systemu (nadającą się do powtórnego wykorzystania) i żeby dało się je (w miarę) równolegle implementować. -->

Podział na moduły:
- Urządzenia
  - Aplikacja bazowa
    - Konfiguracja urządzenia przez Wi-Fi (tryb accesspoint)
    - Usypianie/wybudzanie urządzenia
    - Odczyt danych z czujnika przez bridge (generycznie!)
    - Publikowanie danych do brokera MQTT
  - Biblioteka czujnika (gotowa biblioteka + plik nagłówkowy z informacjami o dostępnych danych)
  - Bridge (wspólny sposób czytania danych z różnych czujników)
- Webapp
  - GUI z pomiarami/wykresami
    ![home-assistant](https://media.discordapp.net/attachments/1026161539591458967/1028411338659209216/unknown.png)
  - Ustawienia użytkownika (dane kontaktowe, hasło, username, avatar)
  - Ustawienia domu (dodawanie/usuwanie pomieszczeń, tworzenie/anulowanie zaproszeń)
- API
  - Endpointy z aktualnymi pomiarami (REST API - `GET`)
  - Endpointy z historycznymi danymi (REST API - `GET`)
  - Ustawienia użytkownika
  - Ustawienia domu
  - Zadania `cron`

# Literatura

<!-- Badania (research) literatury. Należy znaleźć m.in. artykuły opisujące podobne do planowanego systemu rozwiązania, oraz artykuły przedstawiające problematykę/rozwiązania/implementacje/itp. zagadnień użytecznych z punku widzenia projektowanego systemu (np. modele matematyczne/fizyczne/itp., których można użyć w implementacji projektowanego systemu, zagadnienia dot. przesyłania danych w podobnych systemach, bezpieczeństwa itp.). Należy krótko przedstawić co z danego artykułu zostanie potencjalnie wykorzystane w projektowanym systemie wraz z krótkim uzasadnieniem dlaczego. -->


* artykuły opisujące podobne do planowanego systemu rozwiązania
       
       
    - [LINK](https://ieeexplore.ieee.org/document/8073958) Dzięki temu artykułowi dowiadujemy się jak monitorować **temperaturę i wilgotności** w różnych punktach lokalizacji i zrobienie alertu gdy temperatura przekroczy pewną wartość. Rozwiązanie na płytce ESP8266,  Publikowanie danych z czujników w czasie rzeczywistym.

        Wykorzystanie:
        W naszym projekcie chcielibyśmy zastosować funkcjonalność monitorowania temperatury i wilgotności. Którą będzie można w łatwy sposób sprawdzić w aplikacji.
        Artykułu użyliśmy dlatego, że problem został rozwiązany na płytce ESP8266, której używamy w projekcie oraz system alertów, który również chcemy zaimplementować. 
        
   - [LINK](https://ieeexplore.ieee.org/abstract/document/7906792) Artykuł pokazuje monitorowanie **wilgotności, temperatury** potrzebnych różnym uprawom za pomocą czujników. 
Dane są zbierane i odbierane przez arduino, które mogą być połączone z interaktywną stroną internetową,
która pokazuje wartości w czasie rzeczywistym wraz z wartościami standardowymi różnych czynników 
wymaganych przez uprawy.
        
        Wykorzystanie:
        Kolejny przykład zastosowania monitorowania temperatury i wilgotności. 
        Artykułu użyliśmy dlatego, że problem został rozwiązany na płytce ESP8266 oraz Arduino którego używamy w projekcie. 
        
   - [LINK](https://ieeexplore.ieee.org/document/8711997)  Artykuł wykorzystujący działanie **barometru** BMP280.
        Aplikacja informujące o warunkach pogodowych w czasie rzeczywistym.
        Dane zebrane są porównywane z danymi z kilku aplikacji prognozujących pogodę w niektórych mediach.
        Dane są przesyłane i przechowywane w bazie danych, a następnie wyświetlane w aplikacji internetowej
        i mobilnej. Na podstawie tych informacji, użytkownik może uzyskać informacje bezpośrednio w czasie rzeczywistym.
        
        Wykorzystanie:
        W naszej aplikacji chcemy mieć również informację o warunkach w czasie rzeczywistym, w artykule został wykorzystany czujnik BMP280, który posiadamy i chcielibyśmy wykorzystać w projekcie. Oprócz tego, zainteresowało nas samo działanie aplikacji m.in przesyłanie, przechowywanie i przetwarzanie danych. 

   - [LINK](https://www.sciencedirect.com/science/article/pii/S0378778816319971)  W artykule przedstawiono prototyp inteligentnego systemu oświetleniowego. Jest to aplikacja mobilna, która wykorzystywała wbudowany **czujnik światła** otoczenia do uruchomienia algorytmu sprzężenia zwrotnego w pętli zamkniętej w celu wdrożenia zbierania światła dziennego.
        
        Wykorzystanie:
        W naszej aplikacji planujemy wykorzystanie czujnika światła.  W artykule został wykorzystany własnie taki czujnik w połączeniu z IoT,  okaże się to pomocne podczas implementacji rozwiązania sterowania roletami w pomieszczeniu.
        
* artykuły przedstawiające problematykę/rozwiązania/implementacje/itp. zagadnienia użytecznych z punku widzenia projektowanego systemu

    - [LINK](https://d1wqtxts1xzle7.cloudfront.net/53208020/CSEIT172289-with-cover-page-v2.pdf?Expires=1666806891&Signature=XkU0hZ4jNAWaRJWjZpawXp~PRLD~Mm1hkTbRAC431yleN6DXDHI8Gj30Iy3OXTWek6yzBipqXe6ZkP7bWD5NeQNbWbfDPuxvN0s2fWFfxKS6X~HSESriqSCpAK~bxwlLqm0VDzA04KxN-IwYI2~hvPyh5uX5CKr3WypJQHuDCdRfeM5~zRiiMZnl9PPD-5kJtyz6Z6mTFiTZ5KQjxurvFvbHk0IiqlIXJ4ihH3xCH~OmrFcL0yER--FTwTKt5B2wBY1jVPnwHin6Sxs2rAuBs4vKNUM0i0NCPeGPuRzbhOkK85LwmQedHayyg4XkN7mGB1AEMTSdFgwgR3~E~TrQrw__&Key-Pair-Id=APKAJLOHF5GGSLRBV4ZA) Urządzenie IoT -  Wykorzstanie PHP do stworzenia aplikacji webowej, Arudino, ESP8266, Wifi, Bazy danych 
    
   -  https://ieeexplore.ieee.org/abstract/document/7570919
   -  [TODO](https://ieeexplore.ieee.org/abstract/document/9422655) mongoDB + esp8266 , PlatformIO and Arduino 


<!--

# Algorytmy wykorzystane w projekcie

 Algorytmy, techniki, prawa fizyczne, wzory, itp. które zostaną wykorzystane. Na przykład jeśli częścią projektu jest symulator to na bazie jakich algorytmów/praw fizycznych będzie on zaimplementowany, jeśli częścią systemu jest optymalizacja/rozpoznawanie czegoś to jakie techniki (np. z zakresu uczenia maszynowego) zostaną zastosowane. -->

# Stos technologiczny



## Mikrokontroler

Docelowa implementacja powstanie w frameworku Arduino (C++). Alternatywą dla naszego sprzętu (ESP8266) jest MicroPython, jednak na korzyść Arduino przemawia spora kolekcja bibliotek do czujników.

Wstępnie zastąpimy fizyczne urządzenia skryptami napisanymi w języku JavaScript lub Python. Oba te języki pozwalają na szybką implementację algorytmu publikowania danych przez MQTT.

## MQTT listener

Implementacja powstanie w języku JavaScript (NodeJS) lub Python. Oba pozwalają na wygodną obsługę MQTT oraz bazy danych.

## API

Zostanie napisane z wykorzystaniem frameworku Symfony w języku PHP 8 wraz z dodatkami PHPStan do analizy statycznej kodu i PHPUnit do testowania kodu. Alternatywą był język Python (frameworki Django oraz Flask).

## Aplikacja

Planujemy stworzyć Single-Page Application, do czego możemy wykorzystać rozmaite frameworki JavaScript, jak AngularJS, React czy Vue.js, jednak nie wykluczamy klasycznej aplikacji z podstronami. W tym wypadku mogłaby to być wspólna aplikacja z API (monolit?), bądź pracująca niezależnie.
