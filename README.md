# konfiguracja-serwera-proxy
Poradnik, dzięki któremu w bezpieczny sposób skonfigurujesz serwer proxy w celu połączenia kilku serwerów Minecraft.

# 0. Dlaczego Velocity?
Częstym błędem wielu osób jest korzystanie z przestarzałego, lecz poznanego już software'u. Mogą to być pluginy, silnik serwera Minecraft, a także silnik serwera proxy. Przykładem tego jest BungeeCord, który jest strasznie zasobożerny (w porównaniu z alternatywami) i ciężko w nim o jakiekolwiek bezpieczeństwo. Oczywiście istnieje jeszcze WaterFall, jednak po przejęciu Velocity przez Papera, rozwój najpopularniejszego forka BungeeCorda spadł do poziomów tak niskich jak rozwój starego BungeeCorda.

Kolejnym błędem jest korzystanie z tzw. scamforków. Są to forki closed-source BungeeCorda/WaterFalla niskiej jakości, które nie dość, że więcej zmieniają na gorsze niż na lepsze, to dodatkowo łamią licencję BungeeCorda. Oferują się także jako "w pełni bezpieczne", jednak jak wiadomo to właśnie team Papera jest pierwszy do naprawienia każdej nowej luki w bezpieczeństwie.

Autor Velocity posiada największe doświadczenie w dziedzinie serwerów proxy dla Minecrafta. Jest głównym autorem WaterFalla, którego oddał Paperowi by móc stworzyć właśnie Velocity. Do jego portfolio zalicza się także RedisBungee oraz dwie popularne modyfikacje poprawiające wydajność: Krypton i LazyDFU. Velocity jest dwukrotnie wydajniejszy niż WaterFall, a do tego oferuje lepsze bezpieczeństwo. Jest mniej podatny na możliwe, przyszłe exploity oraz oferuje funkcję Modern Forwarding, dzięki której konfiguracja firewalla nie jest aż tak niezbędna jak w przypadku silników bazujących na BungeeCordzie.

------------------------------------------------------------------------------------------------------------

# 1. Instalacja i pierwsze uruchomienie silnika Velocity
Aby zainstalować Velocity, należy wejść na stronę https://velocitypowered.com/, nacisnąć duży przycisk "Download Now" i wybrać albo najnowszą wersję, albo najnowszego builda (to już zależy od ciebie). Teraz możesz wrzucić silnik do wybranego folderu na twoim serwerze i uruchomić go komendą **java -Xmx1G -Xms1G -XX:+UseG1GC -XX:G1HeapRegionSize=4M -XX:+UnlockExperimentalVMOptions -XX:+ParallelRefProcEnabled -XX:+AlwaysPreTouch -XX:MaxInlineLevel=15 -jar velocity.jar**. Oczywiście możesz też użyć tą komendę w pliku startowym. Dla serwera proxy wystarczy 0,5gb pamięci RAM, jednak warto przypisać trochę więcej na wypadek większego ataku. Ważne jest też by zostawić wolny ram, autor Velocity zaleca by w systemie zostało tyle samo ramu ile jest przy pisane do Velocity + 2gb. W przypadku przypisanego 1gb, maszyna musi mieć przynajmniej 4gb.

------------------------------------------------------------------------------------------------------------

# 2. Konfiguracja Velocity
Jeśli uruchomiłeś serwer proxy pewnie zauważyłeś, że wygenerowały się nowe pliki. Ważny dla ciebie jest plik 'velocity.toml'. Opiszę kilka ważniejszych opcji:

**bind**
Pozwala zmienić port serwera proxy.

**motd**
Pozwala zmienić napis wyświetlający się w liście serwerów. Są do tego zazwyczaj używane pluginy, można wpisać tu coś w stylu "Trwa uruchamianie serwera". Ukaże się to podczas uruchamiania serwera proxy, zanim uruchomi się plugin.

**show-max-players**
Wyświetlana w liście serwerów maksymalna ilość graczy.

**online-mode**
Nie zaleca się zmieniać tej opcji na publicznym serwerze przede wszystkim ze względów prawnych, lecz także ze względów bezpieczeństwa. Ustaw na false tylko jeśli musisz coś przetestować przy użyciu kilku kont.

**player-info-forwarding-mode**
Bardzo ważna opcja, pozwala wybrać tryb przesyłu danych. Jeśli twój serwer wpuszcza graczy tylko z wersji 1.13 lub nowszych, ustaw ją na "MODERN". Jeśli jednak na serwer mogą wchodzić gracze z wersji starszych niż 1.13, wpisz tu "BUNGEEGUARD". Na każdej z tych opcji inaczej konfiguruje się serwery Minecraft żeby przyjmowały połączenia z serwera proxy, zostanie to opisane niżej.

**forwarding-secret**
Jest to "hasło" twojego serwera proxy, pozwala bezpiecznie łączyć się z serwerami Minecraft.

**[servers]**
Tutaj możesz dodać swoje serwery Minecraft. Jeśli serwer proxy stoi na tej samej maszynie co dany serwer Minecraft, jako adres użyj '127.0.0.1'. Jeśli nie, wpisz tam adres serwera Minecraft. Port możesz użyć jaki chcesz, ważne jest żeby nie był zajęty. Port w configu Velocity musi być taki sam jak port w server.properties wybranego serwera. Istnieje tutaj też opcja 'try', w której możesz ustawić serwer, na który gracz będzie przenoszony po dołączeniu przez serwer proxy (najczęściej używana dla serwera lobby).

**[forced-hosts]**
Możesz tutaj ustawić połączenia pomijające serwer lobby. Wymagane są odpowiednie ustawienia domeny.

**To są wszystkie ważne opcje, pamiętaj żeby poprawnie je ustawić.**

------------------------------------------------------------------------------------------------------------

# 3. Konfiguracja serwerów Minecraft
To jeszcze nie koniec! Trzeba poprawnie skonfigurować serwery Minecraft żeby połączenie było bezpieczne. Pierwszym plikiem będzie server.properties, w którym musisz ustawić port (server-port) taki sam jak w configu Velocity oraz wyłączyć tryb online (online-mode=false). Jeśli serwer Minecraft stoi na tej samej maszynie co serwer proxy, nalezy także zmienić adres IP (server-ip) na '127.0.0.1'. Pozostałe ustawienia są zależne od wersji:

**Dla serwerów wpuszczających wyłącznie graczy z wersji 1.13 i nowszych**
1. W velocity.toml ustaw 'player-info-forwarding-mode' na "MODERN"
2. W paper.yml w sekcji 'velocity-support' ustaw 'enabled: true' i 'online-mode: true' oraz wpisz hasło serwera proxy (to z configu Velocity) w opcji 'secret'.

**Dla serwerów wpuszczających graczy z wersji starszych niż 1.13**
1. W velocity.toml ustaw 'player-info-forwarding-mode' na "BUNGEEGUARD"
2. W spigot.yml ustaw 'bungeecord' na 'true'
3. Zainstaluj plugin [BungeeGuard](https://www.spigotmc.org/resources/bungeeguard.79601/) na serwerach Minecraft
4. W configu BungeeGuarda wpisz hasło serwera proxy (to z configu Velocity) w opcji 'allowed-tokens'


**Teraz możesz uruchomić swoje serwery! Jeśli wszystko dobrze zrobiłeś, będziesz mógł połączyć się przez adres serwera proxy.**

------------------------------------------------------------------------------------------------------------

# 4. Dodatkowa konfiguracja
Opisane tu zmiany nie są tak niezbędne jak te wcześniejsze, jednak często są przydatne. Poprawiają ogólne działanie sieci, więc warto z nich skorzystać.

**tcp-fast-open - Velocity**
 Przyspiesza łączenie się gracza z serwerem Proxy jeśli serwer ten stoi na dowolnej dystrybucji Linuxa.
 Zalecana wartość: true

**network-compression-threshold - Server.properties**
 Kompresja pakietów na serwerach Minecraft jest zbędna, ponieważ zajmuje się tym serwer proxy. Można ją wyłączyć.
 Zalecana wartość: -1


# 5. Przydatne pluginy na serwer proxy
Jak już wiesz, pluginy pisane na BungeeCorda nie działają na Velocity. Jest jednak sporo alternatyw. Opiszę kilka z nich:
- [LuckPerms](https://luckperms.net/) - Pozwala zarządzać uprawnieniami i rangami.
- [MiniMOTD](https://www.spigotmc.org/resources/minimotd-server-list-motd-plugin-with-rgb-gradients.81254/) - Plugin pozwalający edytować MOTD serwera w sposób bardziej rozbudowany niż opcja w configu Velocity.
- [CommandWhitelist](https://www.spigotmc.org/resources/commandwhitelist-spigot-waterfall-velocity.81326/) - Umożliwia dodanie komend serwera proxy do whitelisty dla różnych rang.
- [Plan](https://www.spigotmc.org/resources/plan-player-analytics.32536/) - Statystyki serwera.
- [LibertyBans](https://www.spigotmc.org/resources/libertybans.81063/) - Rozbudowany system karania graczy.
- [Geyser](https://geysermc.org/) - Pozwala dołączać graczom korzystajacym z Bedrock Edition.

Staraj się trzymać na proxy jedynie niezbędne pluginy. Velocity jest bardzo lekki, lecz pluginy już niekoniecznie.
