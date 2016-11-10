# bind
bind example config


ns - сервер імен name server
a - задає ip відповідно до імені компютера (імя -> ip)
prt - задає імя компютера відповідно до ip (ip -> імя) ip в зворотньому порядку: 192,168,0,1 ->1,0,168,192 
mx число - визначає сервер пошти
cname - заміна одного імені на інше (www.example.com  -> server.local.com) (www  cname server.local.com), де local.com це domain name
hinfo - інфо
txt - загальні відомості


параметр forvard:(only - ваш днс тільки пересилає на інші днс, first - пробує сам обробити запит, аж потім пересилає)
forvarders - адреси dns серверів, до яких звертатися.


[root@dlp ~]# vi /etc/named.conf
//
// named.conf
//
// Provided by Red Hat bind package to configure the ISC BIND named(8) DNS
// server as a caching only nameserver (as a localhost DNS resolver only).
//
// See /usr/share/doc/bind*/sample/ for example named configuration files.
//

options {
# comment out ( listen all interfaces on the server )
# listen-on port 53 { 127.0.0.1; };
# change ( if not use IPv6 )
listen-on-v6 { none; };
directory"/var/named";
dump-file"/var/named/data/cache_dump.db";
statistics-file"/var/named/data/named_stats.txt";
memstatistics-file"/var/named/data/named_mem_stats.txt";
# query range ( set internal server and so on )
allow-query{ localhost; 10.0.0.0/24; };
# transfer range ( set it if you have secondary DNS )
allow-transfer { localhost; 10.0.0.0/24; };
recursion yes;
dnssec-enable yes;
dnssec-validation yes;
dnssec-lookaside auto;
/* Path to ISC DLV key */
bindkeys-file "/etc/named.iscdlv.key";
managed-keys-directory "/var/named/dynamic";
};
logging {
        channel default_debug {
                file "data/named.run";
                severity dynamic;
        };
};

# change all from here
view "internal" {
        match-clients {
                localhost;
                10.0.0.0/24;
        };
        zone "." IN {
                type hint;
                file "named.ca";
        };
        zone "srv.world" IN {
                type master;
                file "srv.world.lan";
                allow-update { none; };
        };
        zone "0.0.10.in-addr.arpa" IN {
                type master;
                file "0.0.10.db";
                allow-update { none; };
        };
include "/etc/named.rfc1912.zones";
include "/etc/named.root.key";
};
view "external" {
        match-clients { any; };
        allow-query { any; };
        recursion no;
        zone "srv.world" IN {
                type master;
                file "srv.world.wan";
                allow-update { none; };
        };
        zone "80.0.16.172.in-addr.arpa" IN {
                type master;
                file "80.0.16.172.db";
                allow-update { none; };
        };
};

# allow-query ⇒ query range you permit
# allow-transfer ⇒ the range you permit to transfer zone info
# recursion ⇒ allow or not to search recursively
# view "internal" { ** }; ⇒ write for internal definition
# view "external" { ** }; ⇒ write for external definition
# For How to write for reverse resolving, Write network address reversely like below.
# 10.0.0.0/24
# network address⇒ 10.0.0.0
# range of network⇒ 10.0.0.0 - 10.0.0.255
# how to write⇒ 0.0.10.in-addr.arpa
# 172.16.0.80/29
# network address⇒ 172.16.0.80
# range of network⇒ 172.16.0.80 - 172.16.0.87
# how to write⇒ 80.0.16.172.in-addr.arpa


https://www.server-world.info/en/note?os=CentOS_6&p=dns&f=6
