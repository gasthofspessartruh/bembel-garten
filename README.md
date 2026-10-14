# Weiterleitung bembel-garten.de

Diese drei Dateien sind eine reine Weiterleitung. `bembel-garten.de` ist eine
Kurzadresse, die eigentliche Seite liegt auf `bembelgarten-steiger.de`.

Der Umweg über ein eigenes GitHub-Repo hat einen einzigen Grund: **das
TLS-Zertifikat.** Strato stellt für eine bloß reservierte Domain ohne
Webpaket keins aus, dadurch bekäme jeder eine Warnung, der `https://` tippt
oder dessen Browser automatisch auf HTTPS hochstuft. GitHub Pages stellt für
jede Custom Domain ein kostenloses Let's-Encrypt-Zertifikat aus.

## Was drin ist

| Datei | Zweck |
|---|---|
| `CNAME` | sagt GitHub Pages, dass dieses Repo `bembel-garten.de` bedient |
| `index.html` | leitet die Startseite weiter |
| `404.html` | leitet **alle anderen Pfade** weiter, mit Pfad |

Der Trick steckt in `404.html`: GitHub Pages liefert sie für jeden Pfad aus,
den es in diesem Repo nicht gibt. Da es hier außer der Startseite nichts
gibt, landet jeder Aufruf dort, und das Skript hängt den angefragten Pfad an
die Zieladresse an. `bembel-garten.de/speisekarte` kommt also auf
`bembelgarten-steiger.de/speisekarte` an, nicht bloß auf der Startseite.

Ohne JavaScript greift der `meta refresh` und führt zur Startseite.

## Einrichten

1. **Repo anlegen**: `gasthofspessartruh/bembel-garten`, **öffentlich**
   (GitHub Pages braucht das ohne kostenpflichtigen Tarif).

2. **Die drei Dateien hochladen**:

   ```bash
   git init && git branch -M main && git add . && git commit -m "Weiterleitung auf bembelgarten-steiger.de"
   ```

   ```bash
   git remote add origin git@github.com:gasthofspessartruh/bembel-garten.git && git push -u origin main
   ```

3. **Pages einschalten**: Settings → Pages → Deploy from a branch, Branch
   `main`, Ordner `/ (root)`. Custom domain `bembel-garten.de` eintragen.

4. **DNS bei Strato umstellen.** Der bestehende A-Eintrag auf
   `217.160.0.212` (die Strato-Parkseite) muss weg, stattdessen:

   ```
   A     @   185.199.108.153
   A     @   185.199.109.153
   A     @   185.199.110.153
   A     @   185.199.111.153
   AAAA  @   2606:50c0:8000::153
   AAAA  @   2606:50c0:8001::153
   AAAA  @   2606:50c0:8002::153
   AAAA  @   2606:50c0:8003::153
   CNAME www gasthofspessartruh.github.io.
   ```

5. Warten, bis GitHub das Zertifikat ausgestellt hat, dann **Enforce HTTPS**
   anhaken.

## Zu beachten

- Das ist eine Weiterleitung per `meta refresh` und JavaScript, **kein echter
  301**. Auf statischem Hosting geht kein 301. Das `rel="canonical"` in
  `index.html` sagt Suchmaschinen, welche Adresse die richtige ist, das
  genügt in der Praxis.
- `bembel-garten.de` gehört **nicht** in die `static/CNAME` der Hauptseite.
  GitHub Pages erlaubt genau eine Custom Domain pro Repository, eine zweite
  Zeile dort würde `bembelgarten-steiger.de` lahmlegen. Deshalb dieses
  eigene Repo.
- Hier wird nichts gebaut, es gibt keine CI. Die Dateien liegen so, wie sie
  ausgeliefert werden.
