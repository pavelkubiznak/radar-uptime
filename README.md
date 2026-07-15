# radar-uptime

Externí hlídač dostupnosti **https://radar.sintera.cz** (Sintera Match / radar).

## Proč existuje

V červenci 2026 byl radar 10 dní nedostupný zvenku (firewall zablokoval port), aniž by to kdokoli poznal — server totiž lokálně fungoval. Tenhle hlídač běží **na infrastruktuře GitHubu, mimo náš server**, takže výpadek zvenku zachytí, i když je server jinak „zdravý".

## Jak to funguje

- Každých ~15 minut zavolá `https://radar.sintera.cz/login` a čeká HTTP 200 + obsah přihlašovací stránky.
- 3 pokusy po 30 s (ať to nespadne kvůli chvilkovému výkyvu). Když všechny selžou, workflow **skončí chybou**.
- Při chybě přijde **e-mail** (viz níže).
- Jednou denně proběhne „heartbeat" commit, aby GitHub cron nevypnul po 60 dnech nečinnosti.

## Kam chodí upozornění

**Výchozí (nic nenastavuješ):** GitHub sám pošle e-mail majiteli repa, když workflow selže.
Ověř si, že to máš zapnuté: GitHub → Settings → Notifications → **Actions** → „Send notifications for failed workflows only".

**Volitelně hezčí vlastní e-mail** („Radar je NEDOSTUPNÝ"): přidej v tomhle repu 3 secrets
(Settings → Secrets and variables → Actions → New repository secret):
- `ALERT_MAIL_TO` — kam poslat (např. pavel.kubiznak@gmail.com)
- `SMTP_USER` — Gmail adresa odesílatele
- `SMTP_PASS` — Gmail **app password** (ne běžné heslo)

Bez těchto secrets to funguje taky — jen přes vestavěný e-mail GitHubu.

## Ruční test

GitHub → záložka **Actions** → „Radar uptime" → **Run workflow**. Zelená = radar běží.
