# Kursinis darbas: Virtualaus IoT tinklo projektavimas, pažeidžiamumų analizė ir saugumo stebėsena naudojant Wazuh

## Aplinkos paruošimas
1. VirtualBox'e sukurti 3 VM: Wazuh serverį (Wazuh v4.14.10 OVA), IoT įrenginio imitaciją (Ubuntu Server, iot-01) ir atakų mašiną (Kali Linux).
2. Sujungti visas VM vidiniu tinklu 192.168.56.0/24 (host-only adapteris).
3. iot-01 mašinoje įdiegti Mosquitto MQTT brokerį (1883/tcp) ir paleisti `jutiklis.service` tarnybą (publikuoja temperatūros matavimus į `sensor/temperatura`).

## Wazuh agento registracija
1. iot-01 įdiegti Wazuh agentą ir užregistruoti jį Wazuh serveryje.
2. Serverio faile `/var/ossec/etc/rules/local_rules.xml` pritaikyti taisykles 100100 ir 100101 prievadų skenavimui aptikti.
3. Agento faile `ossec.conf` įjungti `/var/log/kern.log` stebėseną ir `syscheck` (FIM) stebimam katalogui.

## Atakų scenarijų paleidimas
Iš Kali mašinos vykdyti:
- `nmap -sS -p- 192.168.56.101` — prievadų skenavimas
- `hydra -l testuser -P pass.txt ssh://192.168.56.101` — SSH slaptažodžių spėjimas
- failo pakeitimas stebimame kataloge (FIM patikrai)

Rezultatai stebimi Wazuh **Threat Hunting → Events** skiltyje.

## C fazės sustiprinimas
iot-01 įjungta tik SSH raktų autentifikacija, uždrausta anoniminė MQTT prieiga, sukonfigūruotas `ufw` (leidžiama tik 22/tcp ir 1883/tcp).

## Failai šioje saugykloje
- `ossec.conf` — Wazuh agento konfigūracija (iot-01)
- `jutiklis.service` — IoT įrenginio imitacijos systemd tarnyba
- `local_rules.xml` — pritaikytos Wazuh aptikimo taisyklės (Wazuh serveris)
