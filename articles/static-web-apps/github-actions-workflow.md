---
title: Flussi di lavoro di GitHub Actions per App Web statiche di Azure
description: Informazioni su come usare i repository GitHub per configurare la distribuzione continua in App Web statiche di Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 0d4a455458812bef1d79aba583a6317c08b65863
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91948375"
---
# <a name="github-actions-workflows-for-azure-static-web-apps-preview"></a>Flussi di lavoro di GitHub Actions per App Web statiche di Azure (anteprima)

Quando si crea una nuova risorsa App Web statica di Azure, Azure genera un flusso di lavoro di GitHub Actions per controllare la distribuzione continua dell'app. Il flusso di lavoro è basato su un file YAML. Questo articolo descrive in dettaglio la struttura e le opzioni del file del flusso di lavoro.

Le distribuzioni vengono avviate da [trigger](#triggers), che eseguono i [processi](#jobs) definiti da singoli [passaggi](#steps).

## <a name="file-location"></a>Percorso del file

Quando si collega il repository GitHub ad App Web statiche di Azure, viene aggiunto un file del flusso di lavoro al repository.

Per visualizzare il file del flusso di lavoro generato, seguire questa procedura.

1. Aprire il repository dell'app in GitHub.
1. Nella scheda _Code_ (Codice) fare clic sulla cartella `.github/workflows`.
1. Fare clic sul file con un nome simile a `azure-static-web-apps-<RANDOM_NAME>.yml`.

Il file YAML nel repository sarà simile all'esempio seguente:

```yml
name: Azure Static Web Apps CI/CD

on:
  push:
    branches:
    - master
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
    - master

jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
    - uses: actions/checkout@v2
      with:
        submodules: true
    - name: Build And Deploy
      id: builddeploy
      uses: Azure/static-web-apps-deploy@v0.0.1-preview
      with:
        azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
        repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
        action: 'upload'
        ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
        app_location: '/' # App source code path
        api_location: 'api' # Api source code path - optional
        app_artifact_location: 'dist' # Built app content directory - optional
        ###### End of Repository/Build Configurations ######

  close_pull_request_job:
    if: github.event_name == 'pull_request' && github.event.action == 'closed'
    runs-on: ubuntu-latest
    name: Close Pull Request Job
    steps:
    - name: Close Pull Request
      id: closepullrequest
      uses: Azure/static-web-apps-deploy@v0.0.1-preview
      with:
        azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
        action: 'close'
```

## <a name="triggers"></a>Trigger

Un [trigger](https://help.github.com/actions/reference/events-that-trigger-workflows) di GitHub Actions notifica a un flusso di lavoro di GitHub Actions di eseguire un processo in base ai trigger di evento. I trigger vengono elencati usando la proprietà `on` nel file del flusso di lavoro.

```yml
on:
  push:
    branches:
    - master
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
    - master
```

Tramite le impostazioni associate alla proprietà `on`, è possibile definire quali rami attivano un processo e impostare i trigger da attivare per i diversi stati delle richieste pull.

In questo esempio viene avviato un flusso di lavoro quando viene modificato il ramo _master_. Le modifiche che avviano il flusso di lavoro includono il push dei commit e l'apertura delle richieste pull per il ramo scelto.

## <a name="jobs"></a>Processi

Ogni trigger di evento richiede un gestore eventi. I [processi](https://help.github.com/actions/reference/workflow-syntax-for-github-actions#jobs) definiscono cosa accade quando viene attivato un evento.

Nel file del flusso di lavoro di App Web statiche sono disponibili due processi.

| Nome  | Descrizione |
|---------|---------|
|`build_and_deploy_job` | Viene eseguito quando si effettua il push dei commit o si apre una richiesta pull nel ramo elencato nella proprietà `on`. |
|`close_pull_request_job` | Viene eseguito solo quando si chiude una richiesta pull che rimuove l'ambiente di gestione temporanea creato dalle richieste pull. |

## <a name="steps"></a>Passaggi

I passaggi sono attività sequenziali per un processo. Un passaggio esegue azioni quali l'installazione di dipendenze, l'esecuzione di test e la distribuzione dell'applicazione nell'ambiente di produzione.

Un file del flusso di lavoro definisce i passaggi seguenti.

| Processo  | Passaggi  |
|---------|---------|
| `build_and_deploy_job` |<ol><li>Estrae il repository nell'ambiente dell'azione.<li>Compila e distribuisce il repository in App Web statiche di Azure.</ol>|
| `close_pull_request_job` | <ol><li>Notifica ad App Web statiche di Azure che una richiesta pull è stata chiusa.</ol>|

## <a name="build-and-deploy"></a>Eseguire la compilazione e la distribuzione

Il passaggio denominato `Build and Deploy` esegue la compilazione e la distribuzione nell'istanza di App Web statiche di Azure. Nella sezione `with` è possibile personalizzare i valori seguenti per la distribuzione.

```yml
with:
    azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
    repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
    action: 'upload'
    ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
    app_location: '/' # App source code path
    api_location: 'api' # Api source code path - optional
    app_artifact_location: 'dist' # Built app content directory - optional
    ###### End of Repository/Build Configurations ######
```

| Proprietà | Descrizione | Obbligatoria |
|---|---|---|
| `app_location` | Percorso del codice dell'applicazione.<br><br>Ad esempio, immettere `/` se il codice sorgente dell'applicazione si trova nella radice del repository o `/app` se il codice dell'applicazione si trova in una directory denominata `app`. | Sì |
| `api_location` | Percorso del codice di Funzioni di Azure.<br><br>Ad esempio, immettere `/api` se il codice dell'app si trova in una cartella denominata `api`. Se nella cartella non viene rilevata alcuna app Funzioni di Azure, la compilazione non restituisce un errore perché il flusso di lavoro presuppone che non si desideri un'API. | No |
| `app_artifact_location` | Percorso della directory di output di compilazione relativa ad `app_location`.<br><br>Se ad esempio il codice sorgente dell'applicazione si trova in `/app` e lo script di compilazione esegue l'output dei file nella cartella `/app/build`, impostare `build` come valore di `app_artifact_location`. | No |

I valori `repo_token`, `action` e `azure_static_web_apps_api_token` sono impostati automaticamente da App Web statiche di Azure e non devono essere modificati manualmente.

## <a name="custom-build-commands"></a>Comandi di compilazione personalizzati

È possibile avere un controllo con granularità fine sui ciò che i comandi eseguono durante una distribuzione. Nella sezione `with` di un processo è possibile definire i comandi seguenti.

La distribuzione chiama sempre `npm install` prima di qualsiasi comando personalizzato.

| Comando            | Descrizione |
|---------------------|-------------|
| `app_build_command` | Definisce un comando personalizzato da eseguire durante la distribuzione dell'applicazione per contenuti statici.<br><br>Ad esempio, per configurare una build di produzione per un'applicazione angolare, creare uno script NPM denominato `build-prod` da eseguire `ng build --prod` e immettere `npm run build-prod` come comando personalizzato. Se lasciato vuoto, il flusso di lavoro tenterà di eseguire i comandi `npm run build` o `npm run build:Azure`.  |
| `api_build_command` | Definisce un comando personalizzato da eseguire durante la distribuzione dell'applicazione per le API di Funzioni di Azure. |

## <a name="route-file-location"></a>Percorso file di route

È possibile personalizzare il flusso di lavoro affinché cerchi il file [routes.json](routes.md) in qualsiasi cartella del repository. Nella sezione `with` di un processo è possibile definire la proprietà seguente.

| Proprietà            | Descrizione |
|---------------------|-------------|
| `routes_location` | Definisce il percorso della directory in cui è disponibile il file _routes.json_. Questo percorso è relativo alla radice del repository. |

 È particolarmente importante essere espliciti sul percorso del file _routes.json_ se il passaggio di compilazione del framework front-end non sposta questo file in `app_artifact_location` per impostazione predefinita.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare le richieste pull negli ambienti di pre-produzione](review-publish-pull-requests.md)
