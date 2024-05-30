# LAV06 - Iskanje in popravljanje napak

Čez 5 ur je rok za oddajo vaje in sošolec, ki med vajami ni poslušal ne ve zakaj mu nič ne dela.
Odvisno kateri od sošolcev je to se zgodi eno od naslednjega:

- pošlje vam svojo datoteko in vas prosi, da mu jo popravite
- pošlje vam sporočilo "ej a ti veš zakaj mi to ne dela?", priloži sliko vaje kjer je polovica lučk na napravah rdečih in čaka da dobi seznam ukazov, ki jih mora prepisati

(alternativno: to vas prosi sredi ocenjevanja)

Ker ste prijazni in nimate boljšega delati v soboto popoldan (ali sredi ocenjevanja), mu seveda pomagate.
Prenesete datoteko (na voljo na moodlu) in začnete iskati napake.

## Pogoste napake

- **napačen tip kabla** - med napravama enakega tipa (usmerjevalnik-usmerjevalnik, stikalo-stikalo) je potrebno uporabiti crossover kabel (črtkana črta), med različnimi (usmerjevalnik-stikalo) pa navaden kabel (polna črta). Če se sosednji napravi, ki imata pravilne IP naslove ne pingata je zelo verjetno, da je problem v kablu.
- **računalnik nima nastavljenega IP naslova** - če iz računalnika ne morete pingati usmerjevalnika je možno, da računalnik sploh nima nastavljenega IP naslova (alternativno IP naslova na računalniku in usmerjevalniku nista v istem omrežju)
- **manjka privzeti prehod (default gateway)** - če iz računalnika lahko pingate usmerjevalnik in ničesar drugega je možno, da računalnik nima nastavljenega privzetega prehoda (default gateway), (alternativno je težava v usmerjanju in usmerjevalnik ne ve kje je ciljno omrežje ali pa je nastavljen napačen default gateway)
- **napačen IP naslov** - če ima naprava nastavljen napačen IP naslov (tak ki ni v istem omrežju) ne more komunicirati z drugimi napravami v omrežju. To najlažje odkrijete tako, da pogledate IP naslove na vmesnikih. (miška nad napravo in se vam izpiše tabela)
- **napačna maska** - če pri nastavljanju IP naslova uporabite napačno masko ping ne bo deloval. To najlažje odkrijete tako, da pogledate IP naslove na vmesnikih. (miška nad napravo in se vam izpiše tabela)
- **vmesnik je izklopljen** - če je lučka zraven vmesnika rdeča to pomeni da je vmesnik izklopljen (lahko tudi na sosednji napravi). Preverite v tabelici, ki se pojavi ko miško postavite nad napravo, piše `Link: down`. Popravite tako, da vmesnik vklopite z ukazom `no shutdown`.
- **manjka statična routa (statično usmerjanje)** - če paket pride do usmerjevalnika, naprej pa ne zna je zelo verjetno, da v usmerjevalni tabeli (`show ip route`) manjka zapis za to omrežje. Popravite tako, da vnesete statično ruto z ukazom `ip route <ciljno omrežje> <maska> <naslednji skok>`. Alternativno je lahko tudi napačen naslednji skok.
- **ne oglašujete omrežja/oglašujete napačno omrežje (dinamično usmerjanje)** - če pozabite oglaševati omrežje ostali usmerjevalniki ne bodo vedeli kje to omrežje je, hkrati pa se ne bo vzpostavil sosedski odnos med usmerjevalnikoma v tem omrežju. Kako najti tako napako?
  - v usmerjevalni tabeli ni vnosa za to omrežje (`show ip route`)
  - sosedski odnos se ne vzpostavi (`show ip ospf neighbor` ali `show ip eigrp neighbors`)
- **napačen ASN pri EIGRP** - če je AS številka na usmerjevalnikih različna se sosedski odnos ne bo vzpostavil (`show ip eigrp neighbors`)

## Seznam uporabnih ukazov

Vse `show` ukaze se izvaja v `user exec` načinu:

```txt
Router# show ip interface brief
```

Če ste v `privileged exec` načinu lahko pred `show` napišete še `do`, ki vam omogoča, da izvajate ukaze kot da ste v `user` načinu:

```txt
Router(config)# do show ip interface brief
```

Ukazi:

- `show ip interface brief` - izpiše tabelo z vsemi vmesniki in njihovimi IP naslovi (isto kot če miško postavite nad napravo)
- `show ip route` - izpiše usmerjevalno tabelo
  - če je zelo dolga in vas zanima le določen tip lahko uporabite npr. `show ip route ospf`
- `show ip ospf neighbor` - izpiše OSPF sosede
- `show ip eigrp neighbors` - izpiše EIGRP sosede
- `show running-config` - izpiše celotno konfiguracijo usmerjevalnika. Tu vidite vse kar ste nastavili

## Brisanje napak

Napačne ukaze lahko brišete z ukazom `no <ukaz>`.
Na primer:

- `no ip address 192.168.1.1 255.255.255.0`
- `no ip route 192.168.1.0 255.255.255.0 123.45.67.89`
- `no network 192.168.1.0 0.0.0.3`
