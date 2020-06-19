---
title: 'Esercitazione: Pubblicare un sito Hugo in App Web statiche di Azure'
description: Informazioni su come distribuire un'applicazione Hugo in App Web statiche di Azure.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.openlocfilehash: 44472981e48a7018fcdf55f28d33d0dda9479d44
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84669903"
---
# <a name="tutorial-publish-a-hugo-site-to-azure-static-web-apps-preview"></a>Esercitazione: Pubblicare un sito Hugo in App Web statiche di Azure (anteprima)

Questo articolo illustra come creare e distribuire un'applicazione Web [Hugo](https://gohugo.io/) in [App Web statiche di Azure](overview.md). Il risultato finale è una nuova risorsa di App Web statiche di Azure che include le azioni di GitHub Actions associate per controllare la creazione e la pubblicazione dell'app.

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

1. Inizializzare un repository GIT.

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

1. Per **Sottoscrizione**, accettare la sottoscrizione elencata o sceglierne una nuova nell'elenco a discesa.

1. Per _Gruppo di risorse_ selezionare **Nuovo**. In _Nome nuovo gruppo di risorse_ immettere **hugo-static-app** e scegliere **OK**.

1. Immettere quindi un nome per l'app nella casella **Nome**. I caratteri validi includono `a-z`, `A-Z`, `0-9` e `-`.

1. In _Area_selezionare un'area disponibile nelle vicinanze.

1. In _SKU_ selezionare **Gratuito**.

   :::image type="content" source="./media/publish-hugo/basic-app-details.png" alt-text="Dettagli inseriti":::

1. Fare clic sul pulsante **Accedi con GitHub**.

1. Selezionare l'**organizzazione** in cui è stato creato il repository.

1. Selezionare **hugo-static-app** come valore di _Repository_.

1. In _Ramo_ selezionare **master**.

   :::image type="content" source="./media/publish-hugo/completed-github-info.png" alt-text="Informazioni su GitHub inserite":::

### <a name="build"></a>Compilare

Successivamente, verranno aggiunte le impostazioni di configurazione usate dal processo di compilazione per compilare l'app. Le impostazioni seguenti consentono di configurare il file del flusso di lavoro di GitHub Actions.

1. Fare clic sul pulsante **Avanti: Compilazione >** per modificare la configurazione della build

1. Impostare _Percorso app_ su **/** .

1. Impostare _Percorso artefatto app_ su **public**.

   Non è necessario specificare alcun valore per _Percorso API_ perché al momento non viene distribuita alcuna API.

### <a name="review-and-create"></a>Rivedi e crea

1. Fare clic sul pulsante **Rivedi e crea** per verificare che tutti i dettagli siano corretti.

1. Fare clic su **Crea** per avviare la creazione della risorsa di App Web statiche di Azure ed effettuare il provisioning di un'azione di GitHub Actions per la distribuzione.

1. Al termine della distribuzione, passare al terminale ed eseguire il pull del commit nel computer con l'azione di GitHub Actions.

   ```bash
   git pull
   ```

1. Aprire l'app Hugo in un editor di testo e aprire il file _.github/workflows/azure-pages-<NOME_FLUSSO_DI_LAVORO>.yml_.

1. Sostituire la riga `- uses: actions/checkout@v2` (riga 18) con la seguente per compilare l'applicazione Hugo. Se è necessario Hugo Extended, rimuovere il commento `extended: true`.

   ```yml
   - uses: actions/checkout@v2
     with:
       submodules: true  # Fetch Hugo themes (true OR recursive)
       fetch-depth: 0    # Fetch all history for .GitInfo and .Lastmod

   - name: Setup Hugo
     uses: peaceiris/actions-hugo@v2.4.11
     with:
       hugo-version: "latest"  # Hugo version: latest OR x.y.z
       # extended: true

   - name: Build
     run: hugo
   ```
   
   Per altre informazioni sull'installazione di Hugo nello strumento di esecuzione di GitHub Actions, vedere [peaceiris/actions-hugo](https://github.com/peaceiris/actions-hugo).

1. Eseguire il commit del flusso di lavoro aggiornato ed effettuare il push in GitHub.

   ```bash
   git add -A
   git commit -m "Updating GitHub Actions workflow"
   git push
   ```

1. Attendere il completamento dell'azione di GitHub Actions.

1. Nella finestra _Panoramica_ del portale di Azure della risorsa di App Web statiche di Azure appena creata, fare clic sul collegamento _URL_ per aprire l'applicazione distribuita.

   :::image type="content" source="./media/publish-hugo/deployed-app.png" alt-text="Applicazione distribuita":::

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere un dominio personalizzato](custom-domain.md)
