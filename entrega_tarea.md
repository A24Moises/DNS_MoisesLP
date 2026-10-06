
## Instala o servidor BIND9 no equipo









## Resultado de los comandos:
- nslookup darthvader.starwars.lan localhost
Server:         localhost
Address:        127.0.0.1#53

Name:   darthvader.starwars.lan
Address: 192.168.20.10

- nslookup skywalker.starwars.lan localhost
Server:         localhost
Address:        127.0.0.1#53

Name:   skywalker.starwars.lan
Address: 192.168.20.111
Name:   skywalker.starwars.lan
Address: 192.168.20.101

- nslookup starwars.lan localhost
;; Got SERVFAIL reply from 127.0.0.1, trying next server
;; Got SERVFAIL reply from 127.0.0.1
Server:         localhost
Address:        127.0.0.1#53

** server can't find starwars.lan: SERVFAIL

- nslookup -q=mx starwars.lan localhost
Server:         localhost
Address:        127.0.0.1#53

starwars.lan    mail exchanger = 10 c3po.starwars.lan.

- nslookup -q=ns starwars.lan localhost
Server:         localhost
Address:        127.0.0.1#53

starwars.lan    nameserver = darthvader.starwars.lan.
starwars.lan    nameserver = darthsidious.starwars.lan.

- nslookup -q=soa starwars.lan localhost
Server:         localhost
Address:        127.0.0.1#53

starwars.lan
        origin = darthvader.starwars.lan
        mail addr = moises.starwars.lan
        serial = 1
        refresh = 3600
        retry = 1800
        expire = 1209600
        minimum = 86400

- nslookup -q=txt lenda.starwars.lan localhost

--------
- nslookup 192.168.20.11 localhost

11.20.168.192.in-addr.arpa      name = darthsidious.starwars.lan.