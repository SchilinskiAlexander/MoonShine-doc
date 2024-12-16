# Installation

- [Voraussetzungen](#voraussetzungen)
- [Installation über Composer](#composer)
- [Panel-Installation](#installation)

---

<a name="voraussetzungen"></a>
## Voraussetzungen

Um mit MoonShine zu arbeiten, müssen vor der Installation die folgenden Voraussetzungen erfüllt sein:

- PHP 8.2+
- Laravel 10.48+
- Composer 2+

<a name="composer"></a>
## Installation über Composer

```shell
composer require "moonshine/moonshine:^3.0"
```

<a name="installation"></a>
## Panel-Installation

```shell
php artisan moonshine:install
```

> [!TIPP]
> Führen Sie die Installation nur einmal am Anfang durch. Nach der Installation kann alles über die [Konfiguration](/docs/{{version}}/configuration) eingestellt werden.

Während des Installationsprozesses werden Sie aufgefordert:

1. *Authentifizierung*. Aktivieren/Deaktivieren Sie das `middleware`, das prüft, ob der Benutzer Zugriff auf das Panel hat.
2. *Migrationen*. Notwendig, wenn Sie die integrierten Funktionen von `MoonShine` zur Verwaltung von Benutzern und Rollen nutzen möchten.
3. *Benachrichtigungen*. Aktivieren/Deaktivieren Sie das Benachrichtigungssystem, und Sie werden auch gefragt, ob der Datenbanktreiber zum Speichern von Benachrichtigungen in der Datenbank verwendet werden soll.
4. *Template-Theme*. Standard oder kompakt.
5. *Superuser*. Wenn Sie die Migrationsoption gewählt haben, werden Sie aufgefordert, einen Superuser zu erstellen, der mit den während der Installation angegebenen Anmeldedaten Zugriff auf das Admin-Panel erhält.
6. *Vergessen Sie nicht, das GitHub-Repository zu favorisieren. Vielen Dank!*

Während der Installation werden folgende Änderungen vorgenommen:

- `php artisan storage:link`
- `app/Providers/MoonShineServiceProvider.php`, und der Provider wird zu `bootstrap/providers.php` hinzugefügt
- `app/MoonShine`
- `config/moonshine.php`
- `lang/vendor/moonshine`
- `public/vendor/moonshine`
- `app/MoonShine/Pages/Dashboard.php`
- `app/MoonShine/Layouts/MoonShineLayout.php`

Nach der Installation hat das Projekt die folgende Struktur:

- `app/MoonShine` — Das Hauptverzeichnis mit Ressourcen, Seiten und Seitentemplates.
  - `app/MoonShine/Pages` — Der Kern von MoonShine besteht aus Seiten. Jede Route im Admin-Panel rendert eine Seite mit einer Reihe von Komponenten. Wenn mehrere Seiten durch eine gemeinsame Aufgabe verbunden sind, können sie in Ressourcen gruppiert werden.
  - `app/MoonShine/Resources` — Ressourcen dienen der logischen Gruppierung von Seiten. Im Hinblick auf `ModelResource` (CrudResource) enthalten solche Ressourcen sofort die vollständige Funktionalität für CRUD-Operationen sowie alle notwendigen Seiten zum Erstellen, Bearbeiten, Anzeigen und Auflisten von Einträgen.
  - `app/MoonShine/Layouts/MoonShineLayout.php` — Das Haupttemplate für alle Seiten. Hier können Sie die Struktur der Komponenten, das Erscheinungsbild und das Menü ändern. Sie können beliebig viele Templates erstellen und das benötigte für jede Seite auswählen.
- `app/Providers/MoonShineServiceProvider.php` — Dieser Provider registriert Ressourcen und Seiten und legt auch globale Einstellungen fest. Das Panel kann sowohl über ein bequemes Objekt im Provider als auch über die Datei `config/moonshine.php` konfiguriert werden.
- `config/moonshine.php` — Eine Datei mit den Haupteinstellungen von MoonShine. Sie können nur die geänderten Schlüssel darin belassen oder die Datei vollständig löschen und alles über `MoonShineServiceProvider` konfigurieren.

Nun ist alles bereit, um Ihr Admin-Panel zu nutzen und zu erstellen. Sie können es unter `/admin` aufrufen.

Wir empfehlen, die Dokumentation Schritt für Schritt zu befolgen, um ein tieferes Verständnis des Konzepts zu erlangen. Der nächste Abschnitt ist **Konfiguration**, in dem Sie auch Antworten darauf finden, wie Sie vorgehen, wenn Sie den Weg einer benutzerdefinierten Authentifizierungsimplementierung und Benutzerentitäten gewählt haben.
