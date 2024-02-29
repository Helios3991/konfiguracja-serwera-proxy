# Konfiguracja Serwera Proxy
**Poradnik ten został napisany pod najnowsze wersje silników Paper oraz Velocity i jest aktywnie rozwijany. Jeśli korzystasz ze starszych wersji któregoś z tych silników, konfiguracja może się różnić.**

# 0. Dlaczego Velocity?
Częstym błędem wielu osób jest korzystanie z przestarzałego, lecz poznanego już software'u. Mogą to być pluginy, silnik serwera Minecraft, a także silnik serwera proxy. Przykładem tego jest BungeeCord, który jest bardzo zasobożerny (w porównaniu z alternatywami) i mało bezpieczny. Do popularnych silników proxy można zaliczyć także WaterFall, stary projekt twórcy Velocity, który nie jest już aktywnie rozwijany.

Kolejnym błędem jest korzystanie z tzw. scamforków. Są to forki BungeeCorda/WaterFalla w formie closed source, które najczęściej można znaleźć na stronie BuiltByBit (wcześniej MC-Market). Charakteryzują się one niską jakością wykonania, niestabilnością oraz "ładnymi opisami". Często opisy te zawierają nieprawdziwe informacje mające na celu bardziej przekonać potencjalnego klienta do zakupu, przykładem jest fearmongering na temat tzw. Crasherów - ataków na warstwie aplikacyjnej, które wykorzystują luki w kodzie w celu przeciążenia lub natychmiastowego zatrzymania procesu serwera. Tu trzeba pamiętać, że błędy tego typu są na samym szczycie priorytetów teamu Papera, a więc zarówno Velocity, jak i WaterFall naprawiają je jako pierwsze.

Autor Velocity posiada największe doświadczenie w dziedzinie serwerów proxy dla Minecrafta. Do jego portfolio zaliczają się przede wszystkim WaterFall oraz RedisBungee, a także dwie modyfikacje poprawiające wydajność: Krypton i LazyDFU. Velocity jest dwukrotnie wydajniejszy niż WaterFall, a do tego oferuje lepsze bezpieczeństwo. Jest mniej podatny na potencjalne exploity oraz oferuje funkcję Modern Forwarding, dzięki której konfiguracja firewalla nie jest aż tak niezbędna jak w przypadku silników bazujących na BungeeCordzie.

------------------------------------------------------------------------------------------------------------

# 1. Instalacja i pierwsze uruchomienie silnika Velocity
Aby zainstalować Velocity, należy wejść na [stronę Papera](https://papermc.io/downloads#Velocity) i pobrać najnowszy build. Teraz możesz wrzucić silnik do wybranego folderu na twoim serwerze i uruchomić go komendą:  
**java -Xmx1G -Xms1G -XX:+UseG1GC -XX:G1HeapRegionSize=4M -XX:+UnlockExperimentalVMOptions -XX:+ParallelRefProcEnabled -XX:+AlwaysPreTouch -XX:MaxInlineLevel=15 -jar velocity.jar**  
Oczywiście możesz też użyć tą komendę w swoim pliku startowym. Dla samego serwera proxy wystarczy przypisać 0.5gb pamięci RAM, jednak pluginy też trochę potrzebują, dlatego lepiej przypisać 1gb-2gb. Ważne jest też by zostawić wolny ram dla overheadu, autor Velocity zaleca by w systemie zostało tyle samo ramu, ile jest przypisane do Velocity + 2gb. Oznacza to, że w przypadku przypisanego 1gb, maszyna powinna mieć przynajmniej 4gb. W zależności od ilości zainstalowanych pluginów może być konieczne zwiększenie przypisanego ramu (flagi Xmx oraz Xms) do 2gb.

------------------------------------------------------------------------------------------------------------

# 2. Konfiguracja Velocity
Jeśli uruchomiłeś serwer proxy pewnie zauważyłeś, że wygenerowały się nowe pliki. Ważny dla ciebie jest plik 'velocity.toml'. Opiszę kilka ważniejszych opcji:

**bind = "0.0.0.0:25577"**  
Pozwala zmienić port serwera proxy oraz wybrać jeden z adresów IP w przypadku, gdy maszyna posiada ich więcej niż 1. Adres 0.0.0.0 oznacza listę wszystkich dostępnych adresów IP.  
**Zalecane**: Jeśli posiadasz cały adres IP - "0.0.0.0:25565". Jeśli nie, wpisz port który otrzymałeś od swojego hostingu.

**motd = "<#09add3>A Velocity Server"**  
Pozwala zmienić tekst wyświetlający się w liście serwerów. Zazwyczaj używane są do tego pluginy, ponieważ oferują większą funkcjonalność.

**show-max-players = 500**  
Wyświetlana w liście serwerów maksymalna ilość slotów.

**online-mode = true**  
Opcja ta sama co w przypadku serwera Minecraft - pozwala przełączyć autoryzację graczy z serwerami Microsoftu. Wyłączenie jej pozwoli dołączać na serwer graczom z piracką wersją gry oraz otworzy wiele luk w bezpieczeństwie, dlatego zalecam zostawić ją włączoną.

**player-info-forwarding-mode = "NONE"**  
Pozwala wybrać tryb przesyłu UUID oraz adresów IP dołączających graczy między serwerem proxy a serwerem Minecraft. Jeśli twój serwer wpuszcza graczy tylko z wersji 1.13 lub nowszych, ustaw ją na "MODERN". Jeśli jednak na serwer mogą wchodzić gracze z wersji starszych niż 1.13, wpisz tu "BUNGEEGUARD". Na każdej z tych opcji inaczej konfiguruje się serwery Minecraft, aby przyjmowały połączenia z serwera proxy, zostało to opisane w punkcie 3.  
**Zalecane**: "MODERN"

**[servers]**  
Tutaj możesz dodać swoje serwery Minecraft. Więcej informacji na temat adresów które powinieneś użyć znajdziesz w sekcji [Networking](https://github.com/Helios3991/konfiguracja-serwera-proxy#5-networking). Port możesz użyć dowolny, pod warunkiem, że nie jest on zajęty przez inny proces i masz do niego dostęp. Port w configu Velocity musi być taki sam jak port w server.properties wybranego serwera Minecraft. Istnieje tutaj także opcja 'try', w której możesz ustawić serwer, na który gracz będzie przenoszony po dołączeniu przez serwer proxy (najczęściej używana dla serwera lobby).

**[forced-hosts]**  
Możesz tutaj ustawić połączenia pomijające serwer lobby zależnie od podanej subdomeny. Wymagane są odpowiednie ustawienia domeny. Jeśli z tego nie korzystasz, usuń całą zawartość tej opcji.  
**Zalecane**: Usuń całą sekcję, zostawiając tylko linijkę '[forced-hosts]'.

**To są wszystkie ważne opcje, pamiętaj żeby poprawnie je ustawić.**

------------------------------------------------------------------------------------------------------------

# 3. Konfiguracja serwerów Minecraft
Sam serwer Minecraft też posiada parę opcji, które trzeba zmienić. Pierwszym plikiem będzie server.properties, w którym musisz ustawić port (server-port) taki sam jak w configu Velocity oraz wyłączyć tryb online (online-mode=false). Jeśli serwer Minecraft stoi na tej samej maszynie co serwer proxy, należy także zmienić adres IP (server-ip) na '127.0.0.1'. Pozostałe ustawienia są zależne od wersji:

Zacznij od ustawienia w pliku server.properties adresu IP oraz portu, które są zgodne z tymi w configu Velocity.

**Dla serwerów wpuszczających wyłącznie graczy z wersji 1.13 i nowszych**  
1. W velocity.toml ustaw 'player-info-forwarding-mode' na "MODERN"
2. W config/paper-global.yml w sekcji 'velocity-support' ustaw 'enabled: true' i 'online-mode: true' oraz wpisz klucz serwera proxy (znajdujący się w pliku forwarding.secret) w opcji 'secret'.

**Dla serwerów wpuszczających graczy z wersji starszych niż 1.13**  
1. W velocity.toml ustaw 'player-info-forwarding-mode' na "BUNGEEGUARD"
2. W spigot.yml ustaw 'bungeecord' na 'true'
3. Zainstaluj plugin [BungeeGuard](https://www.spigotmc.org/resources/bungeeguard.79601/) na serwerach Minecraft
4. W configu BungeeGuarda wpisz klucz serwera proxy (znajdujący się w pliku forwarding.secret) w opcji 'allowed-tokens'


**Teraz możesz uruchomić swoje serwery! Jeśli wszystko dobrze zrobiłeś, będziesz mógł połączyć się przez adres i port serwera proxy.**

------------------------------------------------------------------------------------------------------------

# 4. Dodatkowa konfiguracja
Opisane tu zmiany nie są tak niezbędne jak te wcześniejsze, jednak często są przydatne. Poprawiają ogólne działanie sieci, więc warto z nich skorzystać, jeśli spełniasz warunki.

**tcp-fast-open - Velocity**  
- Przyspiesza łączenie się gracza z serwerem Proxy, jeśli serwer ten stoi na dowolnej dystrybucji Linuxa.
- Zalecana wartość: true
- Warunek: Serwer proxy musi stać na Linuxie.

**network-compression-threshold - Server.properties**  
- Gdy serwer Minecraft stoi na tej samej maszynie co serwer proxy to nie musi on kompresować przesyłanych danych, ponieważ wysyła je tylko do serwera proxy, który zajmuje się kompresją. Nie zmieniaj tej opcji, jeśli serwery stoją na różnych maszynach.
- Zalecana wartość: -1
- Warunek: Serwer musi stać na tej samej maszynie co proxy.

------------------------------------------------------------------------------------------------------------

# 5. Networking
Poprawna konfiguracja ruchu sieciowego między serwerami ma ogromny wpływ na bezpieczeństwo twojej sieci serwerów, a także na użycie zasobów łącza.  

**Na początek wyjaśnię parę terminów:**  
1. Sieć publiczna/globalna - To właśnie nazywamy Internetem. Jeśli posiadasz dostęp do Internetu, nieważne gdzie jesteś, możesz połączyć się z dowolnym urządzeniem w sieci publicznej.
2. Sieć lokalna - Taka sieć jest dużo mniejsza niż sieć globalna i ogranicza się do urządzeń stojących za pojedynczym routerem. Lokalny ruch sieciowy nie wydostaje się do sieci publicznej poprzez router. To właśnie poprzez sieć lokalną przechodzi ruch sieciowy
podczas gry LAN czy komunikacji z drukarką.
3. Adres publiczny - To adres twojego routera w sieci publicznej (przykład: 104.28.212.230). Otwierając usługę sieciową na tym adresie będzie ona widoczna w całej sieci globalnej. Placeholder adresowy '0.0.0.0' oznacza wszystkie publiczne adresy IP do których urządzenie posiada dostęp.
4. Adres prywatny - To adres pojedynczego urządzenia w sieci lokalnej. Dla przykładu, router może mieć adres 192.168.0.1, komputer 192.168.0.10, a drukarka 192.168.0.15. Otwierając usługę sieciową na tym adresie będzie ona widoczna wyłącznie w sieci lokalnej.
5. Adres lokalny(localhost) - Znany już niektórym adres 127.0.0.1, to adres dostępny dla pojedynczego (wirtualnego) urządzenia. Usługa sieciowa otwarta na tym adresie nie będzie widoczna nawet w sieci lokalnej.
6. Adres niestandardowy - Są to adresy wprowadzone przez zewnętrzne oprogramowanie do specyficznych zastosowań. Przykładowo popularny panel dla serwerów gier Pterodactyl wprowadza adres 172.18.0.1, który pozwala połączyć się jedynie z poziomu jednego Node'a. Niektóre hostingi zarządzane oferują także adresy wewnętrzne, które są ograniczone do pojedynczego pakietu zasobów (przykład: [Bloom](https://demo.bloom.host), adres 'ec4a194c-c98d-44db-ab76-5b5bd99fbc98').
7. Serwer backendowy - To serwer, do którego dostęp potrzebują jedynie inne serwery. Przykładami takich serwerów są np. bazy danych oraz serwery Minecraft stojące za serwerem proxy.

Twoim celem jest obniżenie poziomu dostępu do sieci oraz trasy ruchu sieciowego dla serwerów backendowych do minimum, w którym twoje pozostałe serwery nadal mogą się z nimi kontaktować. Jeśli korzystasz z pojedynczej maszyny niezarządzanej (vps/dedyk), otwórz serwery backendowe na adresie 127.0.0.1. Jeśli twój hosting zarządzany oferuje adresy wewnętrzne, nie zawahaj się z nich skorzystać. Jeśli stoisz na jednym Node w czystym Pterodactylu, użyj adres 172.18.0.1. Jeśli twój hosting nie oferuje żadnych funkcjonalności dotyczących konfiguracji ruchu sieciowego, to znaczy że nie jest on odpowiednio dostosowany pod sieci serwerów i powinieneś poszukać alternatywy.

------------------------------------------------------------------------------------------------------------

# 6. Niebezpieczeństwa trybu Offline
Tryb Offline charakteryzuje się brakiem autoryzacji z serwerami Microsoftu. Pozwala dołączać graczom z piracką wersją Minecrafta, potocznie (i bardzo myląco) nazywaną Non-Premium. Dla niektórych właścicieli serwerów może to być pożądane działanie, jednak niesie to ze sobą wiele problemów i niebezpieczeństw. Do poważniejszych z nich można zaliczyć: możliwość dołączania z dowolną nazwą użytkownika bez jakiejkolwiek autoryzacji, ataki z wykorzystaniem botów, proste i szybkie tworzenie wielu kont oraz problemy dotyczące UUID w niektórych pluginach. Niektóre problemy można częściowo ukryć, jednak nie są to skuteczne rozwiązania.

------------------------------------------------------------------------------------------------------------

# 6. Zabezpieczenie trybu Offline
Ten punkt opisze co można zrobić, aby tryb Offline był bezpieczniejszy niż domyślnie. Jeśli zdecydowałeś się na tryb Online, możesz go pominąć.

**Autoryzacja**  
Na początek warto wspomnieć, że istnieją 3 rodzaje autoryzacji - Online (Autoryzacja z serwerami Microsoftu), Offline (Autoryzacja pluginem, wszyscy gracze zakładają hasła do swojego konta na serwerze Minecraft) oraz Hybrydowa (Gracze z zakupioną grą będą autoryzowani z serwerami Microsoftu, a piraci będą autoryzowani pluginem). Istnieje wiele pluginów służących do autoryzacji graczy, jednak trzeba szczególnie uważać by nie trafić na jeden z tych gorszych - luka w takim pluginie może skutecznie zakończyć cały twój serwer. Zalecam unikać tych pluginów, których kod źródłowy nie jest dostępny publicznie.  

W poradniku użyję plugin [LibreLogin](https://github.com/kyngs/LibreLogin):
1. Pobierz plugin [LibreLogin](https://modrinth.com/plugin/libre-login)
2. Pobierz i jednorazowo uruchom serwer [NanoLimbo](https://github.com/Nan1t/NanoLimbo/releases) w celu wygenerowania pliku konfiguracyjnego
3. Zmień opcje:
     - bind.port: Port który chcesz użyć dla serwera Limbo
     - bind.ip: Adres który chcesz użyć dla serwera Limbo
     - infoForwarding.type: MODERN
     - infoForwarding.secret: Klucz serwera proxy
5. Dodaj serwer NanoLimbo do configu serwera proxy tak samo jak pozostałe serwery
6. Wrzuć LibreLogin do folderu 'plugins' w plikach serwera Velocity
7. Otwórz plik 'config.conf' w folderze 'plugins/LibreLogin'
8. Zmień opcje:
    - auto-register=true
    - new-uuid-creator=MOJANG
9. Wpisz swoje serwery lobby oraz Limbo w odpowiednie miejsca
10. Skonfiguruj przedział portów dla serwera Limbo w pliku konfiguracyjnym LibreLoginu tak, aby mieścił się w nim port twojego serwera Limbo
11. Uruchom Velocity 

Inne sprawdzone pluginy: [LimboAuth](https://github.com/Elytrium/LimboAuth)

**Weryfikacja**  
Aby ochronić serwer przed atakiem botów, trzeba weryfikować wszystkie próby dołączenia na serwer. Filtry (tzw. antyboty) dzielą się na dwie rodziny: wewnętrzne i zewnętrzne. Wewnętrzne filtry (pluginy) skuteczniej wykryją bota, jednak mogą być nieprzyjemne dla niektórych graczy (przykład: blokada vpn, obowiązek przepisania kodu). Dodatkowo, podczas nawet małego ataku serwer zostanie szybko przeciążony, ponieważ całe filtrowanie odbywa się na zasobach tego serwera. Zewnętrzne filtry (proxy) to zbiory wielu maszyn stojące między graczem a serwerem Velocity. Wytrzymają wielokrotnie silniejsze ataki niż pluginy, lecz przepuszczą parę botów i mogą minimalnie zwiększyć ping. Idealnym rozwiązaniem jest użycie obu rodzajów filtrów jednocześnie.  

W poradniku użyję [LimboFilter](https://github.com/Elytrium/LimboFilter) jako filtr wewnętrzny:
1. Pobierz pluginy [LimboFilter](https://modrinth.com/plugin/limbofilter) oraz [LimboAPI](https://modrinth.com/plugin/limboapi)
2. Wrzuć pobrane pluginy do folderu 'plugins' w plikach serwera Velocity
Teraz możesz dopasować pod siebie opcje w configu pluginu, takie jak parametry mapy z kodem, rodzaje filtrowania czy część wizualna.  
**UWAGA: Poradnik dla zewnętrznych filtrów znajduje się w punkcie 7.**

Inne sprawdzone pluginy: [EpicGuard](https://github.com/4drian3d/EpicGuard)

------------------------------------------------------------------------------------------------------------

# 7. Zabezpieczenie serwera przed atakami DDoS
Ataki DDoS to problem każdego serwera, nieważne jak dużego. Na szczęście można go łatwo rozwiązać. Większość hostingów oferuje filtry wyłącznie na warstwach 3 i 4, przez co warstwa 7 (aplikacyjna) jest otwarta na ataki. Istnieje wiele filtrów, lecz lepiej uważnie się im przyjrzeć przed wyborem - niektóre z nich należą do cyberprzestępców. Ta część będzie dotyczyła głównie niskiego budżetu, ponieważ w średnim budżecie można dostać maszynę wraz z filtrami na wszystkich trzech warstwach.

**AKTUALNIE NIC TU NIE MA, ROZBUDOWANY PORADNIK POJAWI SIĘ W PRZYSZŁOŚCI**

------------------------------------------------------------------------------------------------------------

# 8. Przydatne pluginy na serwer proxy
Kiedyś sporym minusem Velocity były małe ilości pluginów, jednak aktualnie coraz częściej można spotkać plugin wspierający Velocity, ale niewspierający BungeeCorda. Pluginy Velocity znajdują się przede wszystkim na [Modrinth](https://modrinth.com/plugins?g=categories:%27velocity%27) oraz [Hangar](https://hangar.papermc.io/?platform=VELOCITY). Większość pluginów dla BungeeCorda ze SpigotMC także posiada wsparcie dla Velocity.
Kilka polecanych pluginów:
- [LuckPerms](https://luckperms.net/) - Pozwala zarządzać uprawnieniami i rangami.
- [FastMOTD](https://modrinth.com/plugin/fastmotd) - Wydajny plugin pozwalający edytować MOTD serwera w sposób bardziej rozbudowany niż opcja w configu Velocity.
- [CommandWhitelist](https://modrinth.com/plugin/commandwhitelist) - Umożliwia dodanie komend serwera proxy do whitelisty dla danych rang.
- [AjQueue](https://modrinth.com/plugin/ajqueue)/[LimboQueue](https://modrinth.com/plugin/limboqueue) - Kolejka dołączania graczy.
- [LibertyBans](https://modrinth.com/plugin/libertybans) - Rozbudowany system karania graczy.
- [LimboReconnect](https://modrinth.com/plugin/limboreconnect) - Korzystając z tego pluginu gracze nie będą wyrzucani z serwera podczas restartu.
- [EnhancedVelocity](https://modrinth.com/plugin/enhancedvelocity) - Posiada małe funkcje typu Quality of Life.
- [VLobby](https://modrinth.com/plugin/vlobby) - Dodaje komendę /lobby.

Staraj się trzymać na proxy jedynie niezbędne pluginy. Velocity jest bardzo lekki, lecz pluginy już niekoniecznie.
