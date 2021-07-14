# konfiguracja-serwera-proxy
Poradnik, dzięki któremu w bezpieczny sposób skonfigurujesz serwer proxy w celu połączenia kilku serwerów Minecraft.

# 1. Wybór silnika proxy
Jest to bardzo ważne żeby wybrać dobry silnik proxy. Przedstawię tu kilka popularniejszych silników proxy:

![image](https://user-images.githubusercontent.com/84963597/125640159-15744a41-b13d-4154-8da9-fd06e2952d8e.png)
# BungeeCord
Pierwszy silnik proxy autorstwa twórcy Spigota. Posiada sporo błędów i luk bezpieczeństwa oraz jest mało wydajny. Nie zaleca się z niego korzystać.

Plusy:
- ogromna baza pluginów

Minusy:
- zasobożerny
- mało bezpieczny

Ocena: 2/10

------------------------------------------------------------------------------------------------------------

![image](https://user-images.githubusercontent.com/84963597/125652189-f7f2021f-1361-49ca-b8dc-1da09d404adb.png)
# WaterFall
Fork (ulepszenie) BungeeCorda autorstwa twórców Papera naprawiający exploity crashujące i znacznie zwiększający wydajność. Jest najpopularniejszym silnikiem proxy.

Plusy:
- w miarę wydajny
- w miarę bezpieczny

Minusy:
- możliwość włamania się na konto dowolnej osoby jeśli ten stoi na współdzielonym hostingu bez możliwości konfiguracji firewalla

Ocena: 7/10

------------------------------------------------------------------------------------------------------------

![image](https://user-images.githubusercontent.com/84963597/125655554-323699ff-a36a-40a3-9bd1-bb8953e431f4.png)
# FlameCord
Fork WaterFalla zabezpieczający serwer przed atakami typu nullping. Jeśli twój serwer jest w trybie online, nie potrzebujesz tego silnika.

Plusy:
- zabezpiecza crackowane serwery przed atakami, które mogą taki serwer wywalić

Minusy:
- wspiera piractwo

Ocena: 6/10

------------------------------------------------------------------------------------------------------------

![image](https://user-images.githubusercontent.com/84963597/125662115-d2ec1845-0670-4379-91d4-4ab02176a2e4.png)
# Velocity
Silnik proxy napisany od zera przez oryginalnego twórcę waterfalla, jest od waterfalla dwukrotnie wydajniejszy i bezpieczniejszy.

Plusy:
- bardzo wydajny
- brak potrzeby konfiguracji firewalla dla bezpieczeństwa
- mniej podatny na exploity crashujące

Minusy:
- brak kompatybilności z pluginami pisanymi na bungeecorda

Ocena: 9/10

Teraz gdy już znasz kilka silników, można wybierać. W poradniku zostanie przedstawiona konfiguracja Velocity, którego nic przez długi czas nie przebije.

------------------------------------------------------------------------------------------------------------

# 2. Instalacja silnika Velocity
Przyszedł czas na zainstalowanie silnika proxy. Żeby to zrobić, należy wejść na stronę https://velocitypowered.com/, nacisnąć duży przycisk "Download Now" i wybrać albo najnowszą wersję, albo najnowszego builda (to już zależy od ciebie). Teraz możesz wrzucić silnik do wybranego folderu na twoim serwerze i uruchomić go w ten sam sposób co silnik serwera mc, lecz z tymi flagami: **-XX:+UseG1GC -XX:G1HeapRegionSize=4M -XX:+UnlockExperimentalVMOptions -XX:+ParallelRefProcEnabled -XX:+AlwaysPreTouch -XX:MaxInlineLevel=15**. Dla serwera proxy wystarczy 0,5gb pamięci RAM.

------------------------------------------------------------------------------------------------------------

# 3. Konfiguracja Velocity
Jeśli uruchomiłeś serwer proxy pewnie zauważyłeś, że wygenerowały się nowe pliki. Ważny dla ciebie jest plik 'velocity.toml'. Opiszę kilka ważniejszych opcji:

**bind**
Pozwala zmienić port serwera proxy.

**motd**
Pozwala zmienić napis wyświetlający się w liście serwerów. Są do tego zazwyczaj używane pluginy, można wpisać tu coś w stylu "Trwa uruchamianie serwera".

**show-max-players**
Wyświetlana w liście serwerów maksymalna ilość graczy.

**online-mode**
Jeśli posiadasz testowy serwer na swoim komputerze i nie posiadasz dostępu do internetu, ustaw na false. W przeciwnym wypadku nie zmieniaj tej opcji.

**player-info-forwarding-mode**
Bardzo ważna opcja, pozwala wybrać tryb przesyłu danych. Jeśli twój serwer wpuszcza graczy tylko z wersji 1.13 lub nowszych, ustaw ją na "MODERN". Jeśli jednak na serwer mogą wchodzić gracze z wersji starszych niż 1.13, wpisz tu "BUNGEEGUARD". Na każdej z tych opcji inaczej konfiguruje się serwery Minecraft żeby przyjmowały połączenia z serwera proxy, zostanie to opisane niżej.

**forwarding-secret**
Jest to "hasło" twojego serwera proxy, pozwala bezpiecznie łączyć się z serwerami Minecraft.

**[servers]**
Tutaj możesz dodać swoje serwery Minecraft. Jeśli serwer Minecraft stoi na tej samej maszynie co serwer proxy, jako adres użyj '127.0.0.1'. Jeśli nie, wpisz tam adres serwera Minecraft. Port możesz użyć jaki chcesz, ważne jest żeby nie był zajęty. Port w configu Velocity musi być taki sam jak port w server.properties wybranego serwera. Istnieje tutaj też opcja 'try', w której możesz ustawić serwer, na który gracz będzie przenoszony po dołączeniu przez serwer proxy.

**[forced-hosts]**
Możesz tutaj ustawić połączenia pomijające serwer lobby.

To są wszystkie ważne opcje, pamiętaj żeby poprawnie je ustawić.

------------------------------------------------------------------------------------------------------------

# 4. Konfiguracja serwerów Minecraft
To jeszcze nie koniec! Trzeba jeszcze poprawnie skonfigurować serwery Minecraft żeby połączenie było bezpieczne. Pierwszym plikiem będzie server.properties, w którym musisz ustawić port taki sam jak w configu Velocity oraz wyłączyć tryb online (online-mode=false). Pozostałe ustawienia są zależne od wersji:

**Dla serwerów wpuszczających wyłącznie graczy z wersji 1.13 i nowszych**
1. W paper.yml w sekcji 'velocity-support' ustaw 'enabled: true' i 'online-mode: true' oraz wpisz hasło serwera proxy (to z configu Velocity) w opcji 'secret'.

**Dla serwerów wpuszczających graczy z wersji starszych niż 1.13**
1. W spigot.yml ustaw 'bungeecord' na 'true'
2. Zainstaluj plugin BungeeGuard na serwerach Minecraft
3. W configu BungeeGuarda wpisz hasło serwera proxy (to z configu Velocity) w opcji 'allowed-tokens'


**Teraz możesz uruchomić swoje serwery! Jeśli wszystko dobrze zrobiłeś, będziesz mógł połączyć się przez adres serwera proxy.**

------------------------------------------------------------------------------------------------------------

# 5. Przydatne pluginy na serwer proxy
Jak już wiesz, pluginy pisane na BungeeCorda nie działają na Velocity. Jest jednak sporo alternatyw. Opiszę kilka z nich:
- [LuckPerms](https://luckperms.net/) - Pozwala zarządzać uprawnieniami i rangami.
- [PistonMOTD](https://forums.velocitypowered.com/t/pistonmotd-best-motd-plugin-multi-platform-support/537) - Plugin pozwalający edytować MOTD swojego serwera w sposób bardziej rozbudowany niż opcja w configu Velocity.
- [Anti-VPN](https://forums.velocitypowered.com/t/anti-vpn-get-the-best-save-money-on-overpriced-plugins-and-block-vpn-users/207) - Blokuje połączenia z vpnów.
- [LibertyBans](https://www.spigotmc.org/resources/libertybans.81063/) - Rozbudowany system karania graczy.

Więcej pluginów znajdziesz na [forum Velocity](https://forums.velocitypowered.com/c/plugins/5).
