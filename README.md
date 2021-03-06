# PL

## QEmu - Proste uruchomienie Linuxa w wersji Live nie wymagającej dysku twardego

### Instalacja

Na samym początku sprawdziłem, czy mam włączoną wirtualizację w BIOSie. Posłużyła mi do tego komenda

```
$ lscpu | grep Virt
```

Następnie zaaktualizowałem wszystkie pakiety i przystąpiłem do instalacji QEmu.

```
$ sudo apt update
$ sudo apt install qemu qemu-kvm
```

Aby zfinalizować operację musiałem wpisać **Y** a następnie zatwierdzić wybór przyciskiem **ENTER**.

KVM i QEMU powinno zostać zainstalowane.

### Tworzenie katalogów dla VM

Utworzyłem katalog, w którym będą przechowywane wszystkie dane maszyny wirtualnej.

Utworzyłem katalog maszyny wirtualnej za pomocą następującego polecenia:

```
$ mkdir -p ~/qemu/alpine
```

Gdzie [alpine](https://alpinelinux.org/downloads/) jest dystrybucją linuxa, którą wybrałem do zainstalowania. Z oficjalnej strony pobrałem wersję standard x86_64. Obraz ważył 124MB. Zaraz po pobraniu obrazu ISO Alpine Linuxa wszedłem do katalogu, aby sprawdzić, czy rzeczywiście istnieje za pomocą poniższej komendy.

```
$ cd ~/qemu/alpine/
```

Obraz wrzuciłem do katalogu z VM z poziomu GUI. Następnie za pomocą komendy `ls` z poziomu terminala sprawdziłem, czy plik na pewno się tam znajduje.

```
s3jk1@hopper:~/qemu/alpine$ ls
alpine-standard-3.12.0-x86_64.iso
```

Teraz utworzyłem obraz QEMU alpine.img i przydzieliłem mu 8 GB miejsca na dysku. Jest to wirtualny dysk twardy, na którym zainstalowałem Alpine Linuxa. QEMU ma własne polecenia tworzenia obrazu QEMU. Listing niżej:

```
s3jk1@hopper:~/qemu/alpine$ qemu-img create -f qcow2 alpine.img 8G
Formatting 'alpine.img', fmt=qcow2 size=8589934592 cluster_size=65536 lazy_refcounts=off refcount_bits=16
```

Za pomocą komendy `ls` sprawdzam, czy na pewno utworzyłem obraz **alpine.img**.

```
s3jk1@hopper:~/qemu/alpine$ ls
alpine.img  alpine-standard-3.12.0-x86_64.iso
```

### Uruchamianie instalatora Alpine

Rozpocząłem emulację QEMU z KVM i zainstalowałem Alpine Linux na obrazie **alpine.img**.

Użyłem skryptu powłoki **install.sh**, aby rozpocząć instalację, ponieważ uważam, że ułatwi to późniejsze zrozumienie poolecenia i zmodyfikowanie.

Aby utworzyć plik **install.sh** uruchomiłem następujące polecenie:

```
$ nano install.sh
```

W ten sposób otworzyłem edytor tekstowy GNU nano 4.8, w którym napisałem poniższy skrypt powłoki

```
kvm -hda alpine.img \
    -cdrom alpine-standard-3.12.0-x86_64.iso \
    -m 512 \
    -net nic \
    -net user \
    -soundhw all \
```

Zapisałem plik za pomocą kombinacji klawiszy **CTRL** + **X**, a następnij wpisałem **Y**, aby potwierdzić, że chcę zmodyfikować bufor i potwierdziłem całość przyciskiem **ENTER**.

W tym przypadku `-m 512` oznacza 512 MB pamięci (RAM), która została przydzielona maszynie wirtualnej.

Teraz muszę zmienić ustawienia za pomocą `chmod`, tak aby skrypt powłoki **install.sh** był wykonywalny, robię to za pomocą poniższej komendy:

```
$ chmod +x install.sh
```

Gdy plik już otrzymał uprawnienia do bycia wykonywalnym, postanowiłem wystartować skrypt instalacyjny za pomocą poniższej komendy:

```
$ ./install.sh
```

![01](/images/01.png)

Instalator Alpine zbootował się bez problemu, prosząc mnie o `localhost login`. Wpisałem tam **root** i zatwierdziłem operację przyciskiem **ENTER**. Bez problemu zalogowałem się.

Teraz wystartowałem instalator za pomocą poniższej komendy:

```
# setup-alpine
```

![02](/images/02.png)

Instalator zapytał mnie o układ klawiatury, który chcę ustawić, z racji, że mieszkam w Polsce i korzystam z Polskiej klawiatury, wybrałem opcję **pl** i zatwierdziłem ją przyciskiem **ENTER**.

Później gdy zapytano mnie o wariant, znowu wybrałem po prostu opcję **pl** i zatwierdziłem ją opcją **ENTER**.

Gdy zostałem zapytanie o **hostname** uznałem, że wpiszę swój pseudonim internetowy **s3jk1**. **Hostname** zatwierdziłem przyciskiem **ENTER**.

![03](/images/03.png)

Zaraz po tym zostałem zapytany o interfejs sieciowy. Wartością domyślną jest **eth0**, co w moim przypadku się zgadza, więc nacisnąłem po prostu **ENTER**, aby wybrać domyślną opcję.

Teraz instalator poprosił mnie o wpisanie adresu IP interfejsu sieciowego. Wybrałem domyślny, czyli adres IP przypisany przez dhcp. Do tej operacji wystarczyło wcisnąć dwa razy **ENTER** i przejść dalej.

Przy drugim wciśnięciu klawisza **ENTER** instalator zapytał się jedynie, czy nie chcę manualnie zmieniać konfiguracji sieci, domyślnie była opcja **no**, a że nie chciałem niczego konfigurować, to po prostu kontynuowałem instalację.

Po tym kroku zostałem zapytany o nowe hasło, z racji iż jest to projekt na uczelnię i nie chcę komplikować niczego, to wpisałem standardowo **admin123**, całą operację zatwierdziłem klawiszem **ENTER**.

Następnie zostałem poproszony o powtórzenie hasła, więc wpisałem po raz kolejny **admin123** i potwierdziłem operację przyciskiem **ENTER**.

Nadszedł czas, aby wybrać strefę czasową. Domyślnie jest to **UTC**, wybrałem ją i wcisnąłem **ENTER**.

Będąc zapytanym o **HTTP/FTP proxy URL** domyślnie wcisnąłem **ENTER** zatwierdzając wybór **none**.

![04](/images/04.png)

Kiedy zostałem zapytany o lustrzane obrazy, wcisnąłem po prostu **ENTER**, zatwierdzając operację, ponieważ domyślny wybór **dl-cdn.alpinelinux.org** mi pasował.

Będąc zapytanym o **serwer SSH** wybrałem domyślnie **openssh**.

Następnie w instalatorze pokazał mi się dostępny dysk, zostałem zapytany którego dysku chcę użyć, w tym wypadku nie miałem zbyt dużego wyboru, ponieważ stworzyłem tylko jeden. Wpisałem **sda**, aby wybrać ten jeden dysk, który został opisany w następujący sposób **8.6 GB ATA QEMU HARDDISK**. Potwierdziłem operację przyciskiem **ENTER**, instalator wypisał mi informację, że powyższy dysk został wybrany, a następnie zostałem zapytany w jaki sposób ma zostać użyty, czy ma być używany jako **sys**, **data**, **lvm**. Wybrałem opcję **sys** i potwierdziłem operację przyciskiem **ENTER**.

Zostałem powiadomiony, że dany dysk zostanie wyczyszczony, jednak nie przejmowałem się tym za bardzo. Wyczyściłem wcześniej opisany dysk wpisując **Y** i zatwierdzając wybór przyciskiem **ENTER**.

Alpine w tym momencie zaczęło się instalować. Cała operacja potrwała trochę dłużej.

Po jakimś czasie Alpine Linux został zainstalowany.

Zostałem poproszony o restart, więc wykonałem go za pomocą polecenia **reboot**.

Po chwili zostałem znowu poproszony o zalogowanie się. W loginie wpisałem **root**, a hasłem było **admin123**.

Tak więc instalacja przebiegła pomyślnie.

![05](/images/05.png)

## Uruchamiane Alpine

Po zakończeniu instalacji wróciłem do terminala, na którym dotychczas pracowałem. Za pomocą skrótu klawiszowego **CTRL** + **Z** zatrzymałem skrypt powłoki **install.sh**. Listing niżej:

```
s3jk1@hopper:~/qemu/alpine$ ./install.sh
^Z
[1]+  Stopped                 ./install.sh
```

W celu uruchomienia Alpine utworzyłem kolejny skrypt powłoki o nazwie **start.sh** w katalogu maszyny wirtualnej za pomocą następującego polecenia:

```
$ nano start.sh
```

W terminalu otworzył mi się edytor **GNU nano 4.8**, dodałem więc kolejne linie do skryptu powłoki **start.sh**:

```
kvm -hda alpine.img \
    -m 512 \
    -net nic \
    -net user \
    -soundhw all
```

Zostałem zapytany czy zapisać zmodyfikowany bufor, potwierdziłem opcję wpisując **Y**, zostałem zapytany o to, jak nazwać plik, domyślnie była już tam wpisana oczekiwana nazwa **start.sh**, więc zatwierdziłem po prostu wybór przyciskiem **ENTER**. Następnie przyznałem uprawnienia skryptowi **start.sh** do bycia wykonywanym za pomocą poniższej komendy:

```
$ chmod +x start.sh
```

Na koniec uruchomiłem nowo zainstalowany system operacyjny Alpine za pomocą QEMU KVM w następujący sposób:

```
$ ./start.sh
```

Oczywiście zalogowałem się za pomocą danych, które podałem wcześniej przy instalacji, czyli loginu **root** i hasła **admin123**.

![06](/images/06.png)

## QEmu - Utworzenie małego dysku wirtualnego i podpięcie go do maszyny wirtualnej

Zacząłem od zainstalowania na swoim systemie QEmu za pomocą poleceń:

```
$ sudo apt-get install qemu
$ sudo apt install qemu-utils
```

Gdy już wszystkie pakiety zostały zainstalowane postanowiłem utworzyć wirtualny dysk o pojemności 10GB.

```
$ qemu-img create -f vdi hdd1.vdi 10G
Formatting 'hdd1.vdi', fmt=vdi size=10737418240 static=off
```

Następnie ściągnąłem [VirtualBox](https://www.virtualbox.org/wiki/Linux_Downloads) dla Ubuntu 20.04 z którego nacodzień korzystam i zainstalowałem ów program z instalatora `*.deb`.

![07](/images/07.png)

Program zaraz po zainstalowaniu zaprezentował się tak. Następnie zainstalowałem GParted, który będzie potrzebny do wykonania zadania. Wykonałem to za pomocą poniższego polecenia. Wpisałem hasło, wcisnąłem **Y** i zatwierdziłem wybór przyciskiem **ENTER**.

```
$ sudo apt-get install gparted
```

Po wpisaniu `gparted` w terminalu zostałem znowu poproszony o hasło, więc je wpisałem. Moim oczom ukazał się program GParted.

![08](/images/08.png)

Później stworzyłem Wirtualną Maszynę na potrzeby zadania, jednak obraz ISO GParted Live nie chciał w żaden sposób wystartować.

![09](/images/09.png)
![10](/images/10.png)
![11](/images/11.png)
![12](/images/12.png)
![13](/images/13.png)
![14](/images/14.png)

## Ustawić na maszynie wirtualnej dwie karty sieciowe

Zacząłem od stworzenia nowej maszyny wirtualnej na potrzeby tego zadania, postanowiłem wybrać MX z dystrybucji Linuxa. Nazwałem wirtualną maszynę **MX-19.2 Linux**, wybrałem ścieżkę `/home/s3jk1/VirtualBox VMs`, tam będzie znajdować się moja maszyna. Wybrałem również typ operacyjnego systemu i wersję, tak aby VM zadziałało. Później określiłem ile maszyna ma mieć przydzielonej pamięci itd.

![15](/images/15.png)

Po zakończeniu instalacji wystartowałem maszynę, wszystko zadziałało jak trzeba. Dokończyłem instalację już w maszynie wirtualnej. Po zakończeniu instalacji zresetowałem system zgodnie z poleceniem instalatora.

![16](/images/16.png)

Gdy już system włączył mi się drugi raz, wyłączyłem całą maszynę, zatrzymałem ją i wszedłem w ustawienia VM, przeszedłem w zakładkę network, a następnie w zakładce pierwszej zaznaczyłem opcję [x] **Enable Network Adapter** i wybrałem opcję **NAT** tak jak było to w poleceniu.

![17](/images/17.png)

W następnej kolejności wszedłem w zakładkę **Adapter 2**, gdzie zaznaczyłem [x] **Enable Network Adapter** i wybrałem aby ten adapter był podpięty jako **Bridged Adapter**. Nazwę wybrało mi już domyślnie, więc jej nie zmieniałem.

![18](/images/18.png)

Zapisałem opcję i znowu włączyłem maszynę wirtualną, wszedłem w terminal za pomocą kombinacji klawiszy **CTRL** + **ALT** + **T**, wpisałem tam `ip address`, aby wyświetliło mi wszystkie informacje na temat urządzeń sieciowych.

![19](/images/19.png)

<hr>

Wpisałem jeszcze raz w terminalu komendę, aby wyskoczyły mi adresy, tym razem w formie bardziej zrozumiałej za człowieka, posłużyła mi do tego poniższa komenda. Dzięki niej wyświetliłem trasę domyślną, która działa jako serwer DHCP w większości sieci domowych.

```
$ ip r
```

![20](/images/20.png)

Protokół DHCP umożliwia hostowi kontaktowanie się z serwerem centralnym, który przechowuje listę adresów IP, które mogą być przypisane do jednej lub wielu podsieci. Klient DHCP może zażądać adresu z tej puli, a następnie użyć go tymczasowo do komunikacji w sieci.

Zwykle w większości sieci domowych lub małych firm internetowe Wi-Fi lub router działa również jako serwer DHCP.

Znalazłem również domyślne adresy IP w narzędziu konfiguracji sieci GUI.

![21](/images/21.png)

![22](/images/22.png)
