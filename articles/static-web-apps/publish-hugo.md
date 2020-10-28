---
title: 'Esercitazione: Pubblicare un sito Hugo in App Web statiche di Azure'
description: Informazioni su come distribuire un'applicazione Hugo in App Web statiche di Azure.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.openlocfilehash: 5f511a898b3b2964f954ba150b05f02486456dcf
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92171495"
---
# <a name="tutorial-publish-a-hugo-site-to-azure-static-web-apps-preview"></a>Esercitazione: Pubblicare un sito Hugo in App Web statiche di Azure (anteprima)

Questo articolo illustra come creare e distribuire un'applicazione Web [Hugo](https://gohugo.io/) in [App Web statiche di Azure](overview.md). Il risultato finale è una nuova istanza di App Web statiche di Azure che include azioni di GitHub Actions associate per controllare la creazione e la pubblicazione dell'app.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> - Creare un'app Hugo
> - Configurare una risorsa di App Web statiche di Azure
> - Distribuire l'app Hugo in Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. Se non si ha un account, è possibile [crearne uno gratuito](https://azure.microsoft.com/free/).
- Un account GitHub. Se non si ha un account, è possibile [crearne uno gratuito](https://github.com/join).

## <a name="create-a-hugo-app"></a>Creare un'app Hugo

Creare un'app Hugo usando l'interfaccia della riga di comando di Hugo:

1. Seguire la [guida all'installazione](https://gohugo.io/getting-started/installing/) di Hugo relativa al sistema operativo usato.

1. Aprire un terminale

1. Eseguire l'interfaccia della riga di comando di Hugo per creare una nuova app.

   ```bash
   hugo new site static-app
   ```

1. Passare all'app appena creata.

   ```bash
   cd static-app
   ```

1. Inizializzare un repository Git.

   ```bash
    git init
   ```

1. Aggiungere quindi un tema al sito installando un tema come modulo secondario GIT e quindi specificandolo nel file di configurazione di Hugo.

   ```bash
   git submodule add https://github.com/budparr/gohugo-theme-ananke.git themes/ananke
   echo 'theme = "ananke"' >> config.toml
   ```

1. Eseguire il commit delle modifiche.

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Eseguire il push dell'applicazione in GitHub

Per connettersi ad App Web statiche di Azure, è necessario un repository in GitHub. I passaggi seguenti illustrano come creare un repository per il sito.

1. Creare da [https://github.com/new](https://github.com/new) un repository GitHub vuoto denominato **hugo-static-app** (non creare un file LEGGIMI).

1. Aggiungere il repository GitHub come repository remoto al repository locale. Assicurarsi di sostituire il segnaposto `<YOUR_USER_NAME>` nel comando seguente con il proprio nome utente di GitHub.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/hugo-static-app
   ```

1. Eseguire il push del repository locale in GitHub.

   ```bash
   git push --set-upstream origin master
   ```

## <a name="deploy-your-web-app"></a>Distribuire l'app Web

I passaggi seguenti illustrano come creare una nuova app del sito statica e distribuirla in un ambiente di produzione.

### <a name="create-the-application"></a>Creazione dell'applicazione

1. Passare al [portale di Azure](https://portal.azure.com)
1. Fare clic su **Crea una risorsa**
1. Cercare **App Web statiche**
1. Fare clic su **App Web statiche (anteprima)**
1. Fare clic su **Crea**

   :::image type="content" source="./media/publish-hugo/create-in-portal.png" alt-text="Creare una risorsa di App Web statiche di Azure nel portale":::

1. Per **Sottoscrizione** , accettare la sottoscrizione elencata o sceglierne una nuova nell'elenco a discesa.

1. Per _Gruppo di risorse_ selezionare **Nuovo** . In _Nome nuovo gruppo di risorse_ immettere **hugo-static-app** e scegliere **OK** .

1. Immettere quindi un nome per l'app nella casella **Nome** . I caratteri validi includono `a-z`, `A-Z`, `0-9` e `-`.

1. In _Area_ selezionare un'area disponibile nelle vicinanze.

1. In _SKU_ selezionare **Gratuito** .

   :::image type="content" source="./media/publish-hugo/basic-app-details.png" alt-text="Creare una risorsa di App Web statiche di Azure nel portale":::

1. Fare clic sul pulsante **Accedi con GitHub** .

1. Selezionare l' **organizzazione** in cui è stato creato il repository.

1. Selezionare **hugo-static-app** come valore di _Repository_ .

1. In _Ramo_ selezionare **master** .

   :::image type="content" source="./media/publish-hugo/completed-github-info.png" alt-text="Creare una risorsa di App Web statiche di Azure nel portale":::

### <a name="build"></a>Compilare

Successivamente, verranno aggiunte le impostazioni di configurazione usate dal processo di compilazione per compilare l'app. Le impostazioni seguenti consentono di configurare il file del flusso di lavoro di GitHub Actions.

1. Fare clic sul pulsante **Avanti: Compilazione >** per modificare la configurazione della build

1. Impostare _Percorso app_ su **/** .

1. Impostare _Percorso artefatto app_ su **public** .

   Non è necessario specificare alcun valore per _Percorso API_ perché al momento non viene distribuita alcuna API.

### <a name="review-and-create"></a>Rivedi e crea

1. Fare clic sul pulsante **Rivedi e crea** per verificare che tutti i dettagli siano corretti.

1. Fare clic su **Crea** per avviare la creazione della risorsa di App Web statiche di Azure ed effettuare il provisioning di un'azione di GitHub Actions per la distribuzione.

1. Attendere il completamento dell'azione di GitHub Actions.

1. Nella finestra _Panoramica_ del portale di Azure della risorsa di App Web statiche di Azure appena creata, fare clic sul collegamento _URL_ per aprire l'applicazione distribuita.

   :::image type="content" source="./media/publish-hugo/deployed-app.png" alt-text="Creare una risorsa di App Web statiche di Azure nel portale":::

#### <a name="custom-hugo-version"></a>Versione di Hugo personalizzata

Quando si genera un'app Web statica, viene generato un [file del flusso di lavoro](./github-actions-workflow.md) che contiene le impostazioni di configurazione di pubblicazione per l'applicazione. È possibile designare una specifica versione di Hugo nel file del flusso di lavoro fornendo un valore per `HUGO_VERSION` nella sezione `env`. La configurazione di esempio seguente mostra come impostare Hugo su una specifica versione.

```yaml
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
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for Github integrations (i.e. PR comments)
          action: "upload"
          ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
          # For more information regarding Static Web App workflow configurations, please visit: https://aka.ms/swaworkflowconfig
          app_location: "/" # App source code path
          api_location: "api" # Api source code path - optional
          app_artifact_location: "public" # Built app content directory - optional
          ###### End of Repository/Build Configurations ######
        env:
          HUGO_VERSION: 0.58.0
```

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere un dominio personalizzato](custom-domain.md)
