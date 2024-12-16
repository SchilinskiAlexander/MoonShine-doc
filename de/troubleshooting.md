# Fehlerbehebung

- [Bilder werden nicht angezeigt](#bilder-werden-nicht-angezeigt)
- [HTTPS-Probleme](#https-probleme)
- [Fehler "Seite nicht gefunden"](#fehler-seite-nicht-gefunden)

---

<a name="bilder-werden-nicht-angezeigt"></a>
## Bilder werden nicht angezeigt
- Stellen Sie sicher, dass Sie den Befehl `php artisan storage:link` ausgeführt haben.
- Überprüfen Sie, ob das Standardlaufwerk auf `public` und nicht auf `local` eingestellt ist.
- Prüfen Sie, ob `APP_URL` in der `.env`-Datei korrekt gesetzt ist:

```php
APP_URL=http://moonshine.test:8080
```

<a name="https-probleme"></a>
## HTTPS-Probleme
Wenn Sie Formulare verwenden, die URLs mit http verwenden, aber https erwarten:

- Stellen Sie sicher, dass Sie ein gültiges SSL-Zertifikat haben.
- Setzen Sie in der Middleware `TrustProxies` folgendes: `protected $proxies = ['*']`

<a name="fehler-seite-nicht-gefunden"></a>
## Fehler "Seite nicht gefunden"
- Überprüfen Sie, ob der MoonShineServiceProvider in `bootstrap/providers.php` oder in `config/app.php` vorhanden ist.
  Zum Beispiel ändert das Apiato-Paket seine Struktur, und MoonShine kann nicht automatisch als Provider hinzugefügt werden. Fügen Sie es manuell hinzu.
- Stellen Sie sicher, dass die Ressource oder Seite im `MoonShineServiceProvider` deklariert ist.
- Leeren Sie den Cache.
