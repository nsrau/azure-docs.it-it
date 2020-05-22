---
title: 'Esercitazione: Pubblicare un sito Gatsby in App Web statiche di Azure'
description: Questa esercitazione illustra come distribuire un'applicazione Gatsby in App Web statiche di Azure.
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: aapowell
ms.openlocfilehash: 14706b623c6973c09b499e4fcd905e3eed430898
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593379"
---
# <a name="tutorial-publish-a-gatsby-site-to-azure-static-web-apps-preview"></a>Esercitazione: Pubblicare un sito Gatsby in App Web statiche di Azure (anteprima)

Questo articolo illustra come creare e distribuire un'applicazione Web [Gatsby](https://gatsbyjs.org) in [App Web statiche di Azure](overview.md). Il risultato finale è un nuovo sito di App Web statiche che include le azioni di GitHub Actions associate per controllare la creazione e la pubblicazione dell'app.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> - Creare un'app Gatsby
> - Configurare un sito di App Web statiche di Azure
> - Distribuire l'app Gatsby in Azure

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. Se non si ha un account, è possibile [crearne uno gratuito](https://azure.microsoft.com/free/).
- Un account GitHub. Se non si ha un account, è possibile [crearne uno gratuito](https://github.com/join).
- [Node.js](https://nodejs.org) installato.

## <a name="create-a-gatsby-app"></a>Creare un'app Gatsby

Creare un'app Gatsby usando l'interfaccia della riga di comando di Gatsby:

1. Aprire un terminale
1. Usare lo strumento [npx](https://www.npmjs.com/package/npx) per creare una nuova app con l'interfaccia della riga di comando di Gatsby. L'operazione potrebbe richiedere alcuni minuti.

   ```bash
   npx gatsby new static-web-app
   ```

1. Passare all'app appena creata

   ```bash
   cd static-web-app
   ```

1. Inizializzare un repository GIT

   ```bash
   git init
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>Eseguire il push dell'applicazione in GitHub

Per creare una nuova risorsa di App Web statiche di Azure, è necessario avere un repository in GitHub.

1. Creare da [https://github.com/new](https://github.com/new) un repository GitHub vuoto denominato **gatsby-static-web-app** (non creare un file LEGGIMI).

1. Aggiungere quindi il repository GitHub appena creato come repository remoto al repository locale. Assicurarsi di sostituire il segnaposto `<YOUR_USER_NAME>` nel comando seguente con il proprio nome utente di GitHub.

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/gatsby-static-web-app
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

   :::image type="content" source="./media/publish-gatsby/create-in-portal.png" alt-text="Creare una risorsa di App Web statiche (anteprima) nel portale":::

1. Per _Sottoscrizione_, accettare la sottoscrizione elencata o sceglierne una nuova nell'elenco a discesa.

1. Per _Gruppo di risorse_ selezionare **Nuovo**. In _Nome nuovo gruppo di risorse_ immettere **gatsby-static-web-app** e scegliere **OK**.

1. Specificare quindi un nome univoco globale per l'app nella casella **Nome**. I caratteri validi includono `a-z`, `A-Z`, `0-9` e `-`. Questo valore viene usato come prefisso dell'URL per la risorsa di App Web statiche in formato `https://<YOUR_APP_NAME>.azurestaticapps.net`.

1. In _Area_selezionare un'area disponibile nelle vicinanze.

1. In _SKU_ selezionare **Gratuito**.

   :::image type="content" source="./media/publish-gatsby/basic-app-details.png" alt-text="Dettagli inseriti":::

1. Fare clic sul pulsante **Accedi con GitHub**.

1. Selezionare l'**organizzazione** in cui è stato creato il repository.

1. Selezionare **gatsby-static-web-app** come valore di _Repository_.

1. In _Ramo_ selezionare **master**.

   :::image type="content" source="./media/publish-gatsby/completed-github-info.png" alt-text="Informazioni su GitHub inserite":::

### <a name="build"></a>Compilare

Successivamente, verranno aggiunte le impostazioni di configurazione usate dal processo di compilazione per compilare l'app.

1. Fare clic sul pulsante **Avanti: Compilazione >** per modificare la configurazione della build

1. Per configurare le impostazioni del passaggio in GitHub Actions, impostare _Percorso app_ su **/** .

1. Impostare _Percorso artefatto app_ su **public**.

   Non è necessario specificare alcun valore per _Percorso API_ perché al momento non viene distribuita alcuna API.

   :::image type="content" source="./media/publish-gatsby/build-details.png" alt-text="Impostazioni di compilazione":::

### <a name="review-and-create"></a>Rivedi e crea

1. Fare clic sul pulsante **Rivedi e crea** per verificare che tutti i dettagli siano corretti.

1. Fare clic su **Crea** per avviare la creazione della risorsa di App Web statiche del servizio app ed effettuare il provisioning di un'azione di GitHub Actions per la distribuzione.

1. Al termine della distribuzione, fare clic su **Vai alla risorsa**.

1. Nella schermata della risorsa fare clic sul collegamento _URL_ per aprire l'applicazione distribuita. Potrebbe essere necessario attendere un paio di minuti il completamento dell'azione di GitHub Actions.

   :::image type="content" source="./media/publish-gatsby/deployed-app.png" alt-text="Applicazione distribuita":::

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere un dominio personalizzato](custom-domain.md)
