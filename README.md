# dondns-mikrotik

DonDNS para RouterOs - Mikrotik

## Instalar mediante la interfaz web o winbox

Puedes instalar el script de forma muy sencilla mediante la interfaz web. Para ello:

- Ves a la sección **System -> Scripts**.
- Selecciona **Add New**
- Dale el nombre que quieras y en **Source** copia y pega el siguiente código
editando los valores necesarios.

```txt
##############Script Settings##################

:local DONDNSUser NOMBRE_DE_API_AQUI
:local DONDNSPass CONTRASEÑA_DE_API_AQUI
:local DONDNSDomain micasa.midominio.com.foo.bar
:local WANInter pppoe-out1

###############################################

:local IpCurrent [/ip address get [find interface=$WANInter] address]
:for i from=( [:len $IpCurrent] - 1) to=0 do={
  :if ( [:pick $IpCurrent $i] = /) do={
    :local NewIP [:pick $IpCurrent 0 $i];
    :if ([:resolve $DONDNSDomain] != $NewIP) do={
      /tool fetch mode=http url=http://dondns.dondominio.com/json/?user=$DONDNSUser&password=$DONDNSPass&host=$DONDNSDomain&ip=$NewIP keep-result=no
      :log info DonDNS Update: $DONDNSDomain - $NewIP
     }
   }
}
```

## Instalar mediante terminal

Para instalar el actualizador de DNS en tu router **MikroTik con RouterOS** sólo tendrás
que ejecutar el siguiente script en un terminal del router:

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
    \n      /tool fetch mode=http url=\"http://dondns.dondominio.com/json/\3Fuser=\$DONDNSUser&password=\$DONDNSPass&host=\$DONDNSDomain&ip=\$NewIP\" keep-result=no\r\
    \n      :log info \"DonDNS Update: \$DONDNSDomain - \$NewIP\"\r\
    \n     }\r\
    \n   }\r\
    \n}\r\
    \n\r\
    \n"
/system scheduler
add interval=5m name="DonDNS scheduler" on-event=DonDNS start-date=jan/02/1970 start-time=01:00:40
```
