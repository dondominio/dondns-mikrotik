# dondns-microtik
DonDNS para RouterOs - Mikrotik

```
/system script
add name=DonDNS source="##############Script Settings##################\r\
    \n\r\
    \n:local DONDNSUser \"NOMBRE_DE_API_AQUI\"\r\
    \n:local DONDNSPass \"CONTRASEÑA_DE_API_AQUI\"\r\
    \n:local DONDNSDomain \"micasa.midominio.com.foo.bar\"\r\
    \n:local WANInter \"pppoe-out1\"\r\
    \n\r\
    \n###############################################\r\
    \n\r\
    \n:local IpCurrent [/ip address get [find interface=\$WANInter] address];\r\
    \n:for i from=( [:len \$IpCurrent] - 1) to=0 do={\r\
    \n  :if ( [:pick \$IpCurrent \$i] = \"/\") do={\r\
    \n    :local NewIP [:pick \$IpCurrent 0 \$i];\r\
    \n    :if ([:resolve \$DONDNSDomain] != \$NewIP) do={\r\
    \n      /tool fetch mode=http url=\"http://www.dondominio.com/api/xml/dynamicip/\3Fuser=\$DONDNSUser&password=\$DONDNSPass&host=\$DONDNSDomain&ip=\$NewIP\" keep-result=no\r\
    \n      :log info \"DonDNS Update: \$DONDNSDomain - \$NewIP\"\r\
    \n     }\r\
    \n   }\r\
    \n}\r\
    \n\r\
    \n"
/system scheduler
add interval=5m name="DonDNS scheduler" on-event=DonDNS start-date=jan/02/1970 start-time=01:00:40
```
