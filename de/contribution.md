# Contribution Guide

- [How can we help?](#how-can-we-help)
- [What we use](#lets-use)
- [Where do we start?](#where-do-we-start)
- [Pull requests](#pull-requests)
- [Where to discuss the development?](#where-to-discuss-the-development)
- [If you find a mistake](#if-you-find-a-mistake)
- [Main branch](#main-branch)
- [Coding style](#coding-style)
- [Developer instructions](#dev-guide)
- [How to make a pull request?](#pr)
- [Any questions?](#any-questions)

---

<a name="how-can-we-help"></a>
## How can we help?

The community needs active users. You can help in many ways:

- Contributing to the code;
- Developing the frontend;
- Reporting bugs;
- Helping other users understand the details;
- Improving the documentation;
- Promoting the project.

<a name="lets-use"></a>
## What we use

- Blade;
- TailwindCSS;
- AlpineJs.

<a name="where-do-we-start"></a>
## Where do we start?

There is already a functional product that is fully operational and testable. Functional does not mean perfect, so our task is to make it better.

<a name="pull-requests"></a>
## Pull requests

You can propose new features or improvements for **MoonShine**! Bugs and issues can be fixed and submitted for review. I also welcome new specialists to contribute to the open-source project.

<a name="where-to-discuss-the-development"></a>
## Where to discuss the development?

A separate chat in Telegram has been created for active project participants. If you are ready to participate in development, join - [MoonShine](https://t.me/MoonShine_Laravel).

<a name="if-you-find-a-mistake"></a>
## If you find a mistake

1. You have enough experience to propose a solution. I would be very happy to receive your PR with a description of the issue and a proposed fix.

2. If you do not know how to solve the problem - create a GitHub issue, and we will fix the problem soon.

> [!WARNING]
> It is important that your PR passes all platform tests and includes a detailed description so that all development participants understand what exactly happened.

<a name="main-branch"></a>
## Main branch

Currently, the main branch is `3.x`

<a name="coding-style"></a>
## Coding style

**MoonShine** adheres to the PSR-12 coding standard and the PSR-4 autoloading standard.

<a name="dev-guide"></a>
## Developer instructions

1. Create a directory for the project and clone the demo.

```
git clone git@github.com:moonshine-software/demo-project.git .
```

2. Add the `packages` directory and run the command below.

```
cd packages && git clone git@github.com:moonshine-software/moonshine.git && cd moonshine && composer install && npm install
```

3. Go back to the project directory and in `composer.json` change the dependency for moonshine/moonshine.

```
"moonshine/moonshine": "3.*.*-dev",
```

4. Add the following to `composer.json`.

```
"repositories": [
    {
        "type": "path",
        "url": "packages/moonshine",
        "options": {
            "versions": {
                "moonshine/moonshine": "3.*.*-dev"
            },
            "symlink": true
        }
    }
]
```

5. Create a `.env` file from `.env.example` (don't forget to create the database) and run the installation below.

```
php artisan key:generate
php artisan storage:link
php artisan migrate --seed
php artisan moonshine:user
php artisan serve
```

## Create something useful!

<a name="pr"></a>
## How to make a pull request?

- Go to the MoonShine repository and click "Fork",
- Clone your fork using git,
- Create a new branch for your changes,
- Commit your changes following the [conventional commits convention](https://www.conventionalcommits.org),
- Push your changes to your fork,
- Go back to the MoonShine repository and click "New pull request",
- Provide a detailed description of your changes in the "Description" field,
- Wait for a review!

<a name="any-questions"></a>
## Any questions?

My name is Danil! Feel free to email me at [thecutcode@gmail.com](mailto:thecutcode@gmail.com)
# Beitragshinweise

- [Wie können wir helfen?](#wie-können-wir-helfen)
- [Was verwenden wir?](#was-verwenden-wir)
- [Wo fangen wir an?](#wo-fangen-wir-an)
- [Pull Requests](#pull-requests)
- [Wo diskutieren wir die Entwicklung?](#wo-diskutieren-wir-die-entwicklung)
- [Wenn Sie einen Fehler finden](#wenn-sie-einen-fehler-finden)
- [Hauptbranch](#hauptbranch)
- [Codierungsstil](#codierungsstil)
- [Entwickleranweisungen](#entwickleranweisungen)
- [Wie erstellt man einen Pull Request?](#wie-erstellt-man-einen-pull-request)
- [Fragen?](#fragen)

---

<a name="wie-können-wir-helfen"></a>
## Wie können wir helfen?

Die Community benötigt aktive Benutzer. Sie können auf viele Arten helfen:

- Zum Code beitragen;
- Das Frontend entwickeln;
- Fehler melden;
- Anderen Benutzern helfen, die Details zu verstehen;
- Die Dokumentation verbessern;
- Das Projekt fördern.

<a name="was-verwenden-wir"></a>
## Was verwenden wir?

- Blade;
- TailwindCSS;
- AlpineJs.

<a name="wo-fangen-wir-an"></a>
## Wo fangen wir an?

Es gibt bereits ein funktionierendes Produkt, das voll funktionsfähig und testbar ist. "Funktionierend" bedeutet jedoch nicht "perfekt", daher besteht unsere Aufgabe darin, es zu verbessern.

<a name="pull-requests"></a>
## Pull Requests

Sie können neue Funktionen oder Verbesserungen für **MoonShine** vorschlagen! Fehler und Probleme können behoben und zur Überprüfung eingereicht werden. Ich begrüße auch neue Spezialisten, die zum Open-Source-Projekt beitragen möchten.

<a name="wo-diskutieren-wir-die-entwicklung"></a>
## Wo diskutieren wir die Entwicklung?

Ein separater Chat auf Telegram wurde für aktive Projektteilnehmer erstellt. Wenn Sie bereit sind, an der Entwicklung teilzunehmen, treten Sie bei - [MoonShine](https://t.me/MoonShine_Laravel).

<a name="wenn-sie-einen-fehler-finden"></a>
## Wenn Sie einen Fehler finden

1. Sie haben genügend Erfahrung, um eine Lösung vorzuschlagen? Ich würde mich sehr freuen, Ihren PR mit einer Beschreibung des Problems und einem Lösungsvorschlag zu erhalten.

2. Wenn Sie nicht wissen, wie das Problem gelöst werden kann, erstellen Sie ein GitHub-Issue, und wir werden das Problem bald beheben.

> [!WARNUNG]
> Es ist wichtig, dass Ihr PR alle Plattformtests besteht und eine detaillierte Beschreibung enthält, damit alle Entwicklungsteilnehmer verstehen, was genau passiert ist.

<a name="hauptbranch"></a>
## Hauptbranch

Derzeit ist der Hauptbranch `3.x`.

<a name="codierungsstil"></a>
## Codierungsstil

**MoonShine** hält sich an den PSR-12-Codierungsstandard und den PSR-4-Autoloading-Standard.

<a name="entwickleranweisungen"></a>
## Entwickleranweisungen

1. Erstellen Sie ein Verzeichnis für das Projekt und klonen Sie die Demo.

```
git clone git@github.com:moonshine-software/demo-project.git .
```

2. Fügen Sie das `packages`-Verzeichnis hinzu und führen Sie den folgenden Befehl aus.

```
cd packages && git clone git@github.com:moonshine-software/moonshine.git && cd moonshine && composer install && npm install
```

3. Gehen Sie zurück in das Projektverzeichnis und ändern Sie in der `composer.json`-Datei die Abhängigkeit für moonshine/moonshine.

```
"moonshine/moonshine": "3.*.*-dev",
```

4. Fügen Sie Folgendes zu `composer.json` hinzu.

```
"repositories": [
    {
        "type": "path",
        "url": "packages/moonshine",
        "options": {
            "versions": {
                "moonshine/moonshine": "3.*.*-dev"
            },
            "symlink": true
        }
    }
]
```

5. Erstellen Sie eine `.env`-Datei aus `.env.example` (vergessen Sie nicht, die Datenbank zu erstellen) und führen Sie die folgende Installation aus.

```
php artisan key:generate
php artisan storage:link
php artisan migrate --seed
php artisan moonshine:user
php artisan serve
```

## Erstellen Sie etwas Nützliches!

<a name="wie-erstellt-man-einen-pull-request"></a>
## Wie erstellt man einen Pull Request?

- Gehen Sie zum MoonShine-Repository und klicken Sie auf "Fork".
- Klonen Sie Ihren Fork mit Git.
- Erstellen Sie einen neuen Branch für Ihre Änderungen.
- Committen Sie Ihre Änderungen unter Beachtung der [conventional commits convention](https://www.conventionalcommits.org).
- Pushen Sie Ihre Änderungen in Ihren Fork.
- Gehen Sie zurück zum MoonShine-Repository und klicken Sie auf "New pull request".
- Geben Sie eine detaillierte Beschreibung Ihrer Änderungen im Feld "Description" an.
- Warten Sie auf eine Überprüfung!

<a name="fragen"></a>
## Fragen?

Mein Name ist Danil! Schreiben Sie mir gerne eine E-Mail an [thecutcode@gmail.com](mailto:thecutcode@gmail.com).
