---
title: 'Esercitazione: Pubblicare un sito VuePress in App Web statiche di Azure'
description: Questa esercitazione illustra come distribuire un'applicazione VuePress in App Web statiche di Azure.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.custom: devx-track-js
ms.openlocfilehash: 72b1bd4d46b0b04364b25a8460361d6a008d42a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91250131"
---
# <a name="tutorial-publish-a-vuepress-site-to-azure-static-web-apps-preview"></a>Esercitazione: Pubblicare un sito VuePress in App Web statiche di Azure (anteprima)

Questo articolo illustra come creare e distribuire un'applicazione Web [VuePress](https://vuepress.vuejs.org/) in [App Web statiche di Azure](overview.md). Il risultato finale è una nuova applicazione di App Web statiche di Azure che include le azioni di GitHub Actions associate per controllare la creazione e la pubblicazione dell'app.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> - Creare un'app VuePress
> - Configurare una risorsa di App Web statiche di Azure
> - Distribuire l'app VuePress in Azure

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. Se non si ha un account, è possibile [crearne uno gratuito](https://azure.microsoft.com/free/).
- Un account GitHub. Se non si ha un account, è possibile [crearne uno gratuito](https://github.com/join).
- [Node.js](https://nodejs.org) installato.

## <a name="create-a-vuepress-app"></a>Creare un'app VuePress

Creare un'app VuePress dall'interfaccia della riga di comando:

1. Creare una nuova cartella per l'app VuePress.

   ```bash
   mkdir static-site
   ```

1. Aggiungere un file _README.md_ nella cartella.

   ```bash
   echo '# Hello From VuePress' > README.md
   ```

1. Inizializzare il file _package.json_.

   ```bash
   npm init -y
   ```

1. Aggiungere VuePress come valore di `devDependency`.

   ```bash
   npm install --save-dev vuepress
   ```

1. Aprire il file _package.json_ in un editor di testo e aggiungere un comando di compilazione alla sezione [`scripts`](https://docs.npmjs.com/cli-commands/run-script.html).

   ```json
   ...
   "scripts": {
       "build": "vuepress build"
   }
   ...
   ```

1. Creare un file _.gitignore_ per escludere la cartella _node\_modules_.

    ```bash
    echo 'node_modules' > .gitignore
    ```

1. Inizializzare un repository Git.

   ```bash
    git init
    git add -A
    git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Eseguire il push dell'applicazione in GitHub

Per connettersi ad App Web statiche di Azure, è necessario un repository in GitHub. I passaggi seguenti illustrano come creare un repository per il sito.

1. Creare da [https://github.com/new](https://github.com/new) un repository GitHub vuoto denominato **vuepress-static-app** (non creare un file LEGGIMI).

1. Aggiungere il repository GitHub come repository remoto al repository locale. Assicurarsi di sostituire il segnaposto `<YOUR_USER_NAME>` nel comando seguente con il proprio nome utente di GitHub.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/vuepress-static-app
   ```

1. Eseguire il push del repository locale in GitHub.

   ```bash
   git push --set-upstream origin master
   ```

## <a name="deploy-your-web-app"></a>Distribuire l'app Web

I passaggi seguenti illustrano come creare una nuova applicazione di App Web statiche e distribuirla in un ambiente di produzione.

### <a name="create-the-application"></a>Creazione dell'applicazione

1. Passare al [portale di Azure](https://portal.azure.com)
1. Fare clic su **Crea una risorsa**
1. Cercare **App Web statiche**
1. Fare clic su **App Web statiche (anteprima)**
1. Fare clic su **Crea**

   :::image type="content" source="./media/publish-vuepress/create-in-portal.png" alt-text="Creare una risorsa di App Web statiche (anteprima) nel portale":::

1. Per **Sottoscrizione**, accettare la sottoscrizione elencata o sceglierne una nuova nell'elenco a discesa.

1. Per _Gruppo di risorse_ selezionare **Nuovo**. In _Nome nuovo gruppo di risorse_ immettere **vuepress-static-app** e scegliere **OK**.

1. Immettere quindi un nome per l'app nella casella **Nome**. I caratteri validi includono `a-z`, `A-Z`, `0-9` e `-`.

1. In _Area_selezionare un'area disponibile nelle vicinanze.

1. In _SKU_ selezionare **Gratuito**.

   :::image type="content" source="./media/publish-vuepress/basic-app-details.png" alt-text="Creare una risorsa di App Web statiche (anteprima) nel portale":::

1. Fare clic sul pulsante **Accedi con GitHub**.

1. Selezionare l'**organizzazione** in cui è stato creato il repository.

1. Selezionare **vuepress-static-app** come valore di _Repository_.

1. In _Ramo_ selezionare **master**.

   :::image type="content" source="./media/publish-vuepress/completed-github-info.png" alt-text="Creare una risorsa di App Web statiche (anteprima) nel portale":::

### <a name="build"></a>Compilare

Successivamente, verranno aggiunte le impostazioni di configurazione usate dal processo di compilazione per compilare l'app. Le impostazioni seguenti consentono di configurare il file del flusso di lavoro di GitHub Actions.

1. Fare clic sul pulsante **Avanti: Compilazione >** per modificare la configurazione della build

1. Impostare _Percorso app_ su **/** .

1. Impostare _Percorso artefatto app_ su **.vuepress/dist**.

Non è necessario specificare alcun valore per _Percorso API_ perché al momento non viene distribuita alcuna API.

   :::image type="content" source="./media/publish-vuepress/build-details.png" alt-text="Creare una risorsa di App Web statiche (anteprima) nel portale":::

### <a name="review-and-create"></a>Rivedi e crea

1. Fare clic sul pulsante **Rivedi e crea** per verificare che tutti i dettagli siano corretti.

1. Fare clic su **Crea** per avviare la creazione della risorsa di App Web statiche di Azure ed effettuare il provisioning di un'azione di GitHub Actions per la distribuzione.

1. Al termine della distribuzione, fare clic su **Vai alla risorsa**.

1. Nella schermata della risorsa fare clic sul collegamento _URL_ per aprire l'applicazione distribuita. Potrebbe essere necessario attendere un paio di minuti il completamento dell'azione di GitHub Actions.

   :::image type="content" source="./media/publish-vuepress/deployed-app.png" alt-text="Creare una risorsa di App Web statiche (anteprima) nel portale":::

### <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere un dominio personalizzato](custom-domain.md)
