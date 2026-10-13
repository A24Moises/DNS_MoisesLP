## Instalar el servidor BIND9 en el equipo `darthvader`

- comando `dig @localhost xunta.gal`

![Captura](./capturas/Captura%20de%20ecrã%20de%202026-09-15%2010-21-36.png)

## Configurar el servidor BIND9 en el equipo `mandalorian` para que entregue como reenviador a `darthvader`

- Contenido de `named.conf.options`

        options {
	        directory "/var/cache/bind";
	        forwarders{
		        192.168.20.10;
	        };
        };

- comando `dig @localhost santiagodecompostela.gal.`        

![Captura](./capturas/2026-10-06_14-12.png)

## Contenido de archivo de zona de resolución directa `db.starwars.lan`

        $TTL    86400
        @       IN      SOA     darthvader.starwars.lan. moises.starwars.lan. (
                                1   ; Número de serie
                                3600        ; Actualización (Refresh)
                                1800        ; Reintento (Retry)
                                1209600     ; Caducidade (Expire)
                                86400 )     ; TTL mínimo

        @               IN      NS      darthvader.starwars.lan.
        @               IN      NS      darthsidious.starwars.lan.
        @               IN      TXT     "Que a forza te acompanhe"
        @               IN      MX      10 c3po.starwars.lan.
        @               IN      A       starwars.lan
        darthvader      IN      A       192.168.20.10
        skywalker       IN      A       192.168.20.101
        skywalker       IN      A       192.168.20.111
        luke            IN      A       192.168.20.22
        darthsidious    IN      A       192.168.20.11
        yoda            IN      A       192.168.20.24
        yoda            IN      A       192.168.20.25
        c3po            IN      A       192.168.20.26
        palpatine       IN      CNAME   darthsidious.starwars.lan.

## Contenido de archivo `named.conf.local`

        zone "starwars.lan" {
                type primary;
                file "/etc/bind/db.starwars.lan";
        };

## Contenido de archivo de zona de resolución inversa `db.192`

        $TTL    604800
        @       IN      SOA     darthvader.starwars.lan. moises.starwars.lan. (
                                1   ; Número de serie
                                3600        ; Actualización (Refresh)
                                1800        ; Reintento (Retry)
                                1209600     ; Caducidade (Expire)
                                86400 )     ; TTL mínimo
        ;
        @       IN      NS      darthvader.starwars.lan.
        10      IN      PTR     darthvader.starwars.lan.
        11      IN      PTR     darthsidious.starwars.lan.
        22      IN      PTR     luke.starwars.lan.
        24      IN      PTR     yoda.starwars.lan.
        25      IN      PTR     yoda.starwars.lan.
        26      IN      PTR     c3po.starwars.lan.
        101     IN      PTR     skywalker.starwars.lan.
        111     IN      PTR     skywalker.starwars.lan.

## Contenido de archivo `/etc/bind/named.conf.local`

        zone "starwars.lan" {
                type primary;
                file "/etc/bind/db.starwars.lan";
        };

        zone "20.168.192.in-addr.arpa"{
                type primary;
                file "/etc/bind/db.192";
        };

## Resultado de los comandos:

- `nslookup darthvader.starwars.lan localhost`

        Server:         localhost
        Address:        127.0.0.1#53

        Name:   darthvader.starwars.lan
        Address: 192.168.20.10

- `nslookup skywalker.starwars.lan localhost`

        Server:         localhost
        Address:        127.0.0.1#53

        Name:   skywalker.starwars.lan
        Address: 192.168.20.111
        Name:   skywalker.starwars.lan
        Address: 192.168.20.101

- `nslookup starwars.lan localhost`

        ;; Got SERVFAIL reply from 127.0.0.1, trying next server
        ;; Got SERVFAIL reply from 127.0.0.1
        Server:         localhost
        Address:        127.0.0.1#53

        ** server can't find starwars.lan: SERVFAIL

- `nslookup -q=mx starwars.lan localhost`

        Server:         localhost
        Address:        127.0.0.1#53

        starwars.lan    mail exchanger = 10 c3po.starwars.lan.

- `nslookup -q=ns starwars.lan localhost`

        Server:         localhost
        Address:        127.0.0.1#53

        starwars.lan    nameserver = darthvader.starwars.lan.
        starwars.lan    nameserver = darthsidious.starwars.lan.

- `nslookup -q=soa starwars.lan localhost`

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

- `nslookup -q=txt lenda.starwars.lan localhost`
        
        Server:         localhost
        Address:        127.0.0.1#53

        lenda.starwars.lan      text = "Que a forza te acompanhe"


- `nslookup 192.168.20.11 localhost`

        11.20.168.192.in-addr.arpa      name = darthsidious.starwars.lan.

## Enlace de repositorio de GitHub
        
[DNS_MoisesLP](https://github.com/A24Moises/DNS_MoisesLP)

-------------------------------------
-------------------------------------

# 1.3 Instalación de zonas secundarias.

Partindo da tarefa 1.1, engadiremos un servidor DNS secundario con BIND9 nunha máquina Debian

1. Tomaremos a máquina darthsidious, e configuraremola para ser servidor secundario, tanto da zona primaria de resolución directa como de resolución inversa. 
Captura os ficheiros de configuración en ambalas dúas máquinas. Fai unha captura onde se vexa o reinicio da máquina darthsidious, no que se vexa no log dos dous equipos e que se fixo a transferencia de zona.

### Fichero de zona en sevidor secundario:

![Captura1](./capturas/2026-10-13_11-32.png)

### Fichero de zona en sevidor primario:

![Captura2](./capturas/2026-10-13_11-35.png)

### Logs de transferencia de zona:

![Captura3](./capturas/2026-10-13_11-43.png)
   
2. Engade un rexistro tipo A (Chewbacca 192.168.20.28) na zona de resolución directa e tamén na de resolución inversa.  
Fai unha captura no momento do reinicio do equipo darthvader, no que se vexa o log dos dous equipos e que se amose que se fixo a transferencia de zona. Adxunta tamén unha captura do ficheiro de zona no servidor secundario.

### Reinicio del equipo darthvader
![Captura4](./capturas/2026-10-13_13-26.png)

### cheevacca añadido y serial aumentado en db.starwars.lan
![Captura5](./capturas/2026-10-13_11-45.png)

### cheevacca añadido y serial aumentado en db.192
![Captura6](./capturas/2026-10-13_12-04.png)

### Transferencia completa
![Captura7](./capturas/2026-10-13_12-36.png)


3. Comproba que o servidor secundario pode resolver ese nome.

        nslookup 192.168.20.11 localhost
        28.20.168.192.in-addr.arpa      name = chewbacca.starwars.lan.

4. Fai os cambios necesarios para que as trasferencias se fagan de forma segura empregando chaves.  
Repite as capturas e vídeos do punto 2, engadindo o rexistro r2d2 (192.168.20.29)

### r2d2 añadido y serial aumentado en db.starwars.lan
![Captura8](./capturas/2026-10-13_13-17.png)

### r2d2 añadido y serial aumentado en db.192
![Captura9](./capturas/2026-10-13_13-15.png)

### Resolucion de nombre r2d2
        nslookup 192.168.20.29 localhost
        29.20.168.192.in-addr.arpa      name = r2d2.starwars.lan.

### Creación de la llave
        key "chave-starwars" {
                algorithm hmac-sha256;
                secret "TOJ/IzS9Jjrm3sy4e9DvHvKGwDEbVU18L3tLv/HR6T8=";
        }; 

### Cambios en el fichero named.conf.local en darthsidious
![Captura10](./capturas/2026-10-13_13-31.png)

### Cambios en el fichero named.conf.local en darthvader
![Captura11](./capturas/2026-10-13_13-40.png)

### Logs de transferencias de llaves en darthvader
![Captura12](./capturas/2026-10-13_13-00.png)

###  Logs de transferencias de llaves en darthsidious
![Captura13](./capturas/2026-10-13_13-44.png)