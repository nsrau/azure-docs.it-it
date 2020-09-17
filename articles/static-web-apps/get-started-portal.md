---
title: 'Avvio rapido: Creazione della prima app Web statica con App Web statiche di Azure usando il portale di Azure'
description: Informazioni su come creare un'istanza di App Web statiche di Azure con il portale di Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 09/03/2020
ms.author: cshoe
ms.openlocfilehash: 66335548381a6f7e3ebf93c97eb212255639644c
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604088"
---
# <a name="quickstart-building-your-first-static-web-app-in-the-azure-portal"></a>Avvio rapido: Creazione della prima app Web statica nel portale di Azure

App Web statiche di Azure consente di pubblicare un sito Web in un ambiente di produzione creando app da un repository GitHub. In questa guida di avvio rapido si distribuisce un'applicazione Web in App Web statiche di Azure usando il portale di Azure.

Se non si ha una sottoscrizione di Azure, creare un [account per una versione di prova gratuita](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Prerequisiti

- [GitHub](https://github.com)
- Account [Azure](https://portal.azure.com)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

## <a name="create-a-static-web-app"></a>Creare un'app Web statica

Dopo aver creato il repository, è possibile creare un'app Web statica dal portale di Azure.

1. Passare al [portale di Azure](https://portal.azure.com)
1. Selezionare **Crea una risorsa**
1. Cercare **App Web statiche**
1. Selezionare **App Web statiche (anteprima)**
1. Selezionare **Crea**

Per iniziare, nella scheda _Informazioni di base_ configurare la nuova app e collegarla a un repository GitHub.

:::image type="content" source="media/getting-started-portal/basics-tab.png" alt-text="Scheda Informazioni di base":::

1. Selezionare la _sottoscrizione di Azure_
1. Selezionare un _gruppo di risorse_ o crearne uno nuovo
1. Assegnare all'app il nome **my-first-static-web-app**.
      1. I caratteri validi sono `a-z` (senza distinzione tra maiuscole e minuscole), `0-9` e `-`.
1. Selezionare un'_area_ vicina
1. Selezionare lo **SKU** _Gratuito_
1. Selezionare il pulsante **Accedi con GitHub** ed eseguire l'autenticazione con GitHub

Dopo aver eseguito l'accesso con GitHub, immettere le informazioni sul repository.

:::image type="content" source="media/getting-started-portal/repository-details.png" alt-text="Dettagli del repository":::

1. Selezionare l'_organizzazione_ preferita
1. Selezionare **my-first-web-static-app** nell'elenco a discesa _Repository_
1. Selezionare **master** dall'elenco a discesa _Ramo_

> [!NOTE]
> Se non viene visualizzato alcun repository, potrebbe essere necessario autorizzare App Web statiche di Azure in GitHub. Passare al repository GitHub e quindi a **Impostazioni > Applicazioni > App OAuth autorizzate**, selezionare **App Web statiche di Azure** e infine **Concedi**. Per concedere le autorizzazioni per i repository dell'organizzazione, è necessario essere un proprietario dell'organizzazione.

1. Nella scheda _Dettagli compilazione_ aggiungere i dettagli di configurazione specifici del framework front-end preferito.

    # <a name="no-framework"></a>[Nessun framework](#tab/vanilla-javascript)

    1. Selezionare **Personalizzato** nel menu a discesa _Set di impostazioni compilazione_
    1. Mantenere il valore predefinito nella casella _Percorso app_
    1. Cancellare il valore predefinito nella casella _Percorso_
    1. Lasciare vuota la casella _Percorso artefatto app_

    # <a name="angular"></a>[Angular](#tab/angular)

    1. Selezionare **Angular** nel menu a discesa _Set di impostazioni compilazione_
    1. Mantenere il valore predefinito nella casella _Percorso app_
    1. Cancellare il valore predefinito nella casella _Percorso_
    1. Digitare **dist/angular-basic** nella casella _Percorso artefatto app_

    # <a name="react"></a>[React](#tab/react)

    1. Selezionare **React** nel menu a discesa _Set di impostazioni compilazione_
    1. Mantenere il valore predefinito nella casella _Percorso app_
    1. Cancellare il valore predefinito nella casella _Percorso_
    1. Digitare **build** nella casella _Percorso artefatto app_

    # <a name="vue"></a>[Vue](#tab/vue)

    1. Selezionare **Vue.js** nel menu a discesa _Set di impostazioni compilazione_
    1. Mantenere il valore predefinito nella casella _Percorso app_
    1. Cancellare il valore predefinito nella casella _Percorso_
    1. Mantenere il valore predefinito nella casella _Percorso artefatto app_

    ---

1. Selezionare **Rivedi e crea**.

    :::image type="content" source="media/getting-started-portal/review-create.png" alt-text="Pulsante Rivedi e crea":::

    > [!NOTE]
    > Per modificare questi valori dopo aver creato l'app, è possibile modificare il [file del flusso di lavoro ](github-actions-workflow.md).

1. Selezionare **Crea**.

    :::image type="content" source="media/getting-started-portal/create-button.png" alt-text="Pulsante Crea":::

1. Selezionare **Vai alla risorsa**.

    :::image type="content" source="media/getting-started-portal/resource-button.png" alt-text="Pulsante Vai alla risorsa":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, è possibile eliminare l'istanza di App Web statiche di Azure seguendo questa procedura:

1. Aprire il [portale di Azure](https://portal.azure.com)
1. Cercare **my-first-web-static-app** nella barra di ricerca in alto
1. Selezionare il nome dell'app
1. Selezionare il pulsante **Elimina**
1. Selezionare **Sì** per confermare l'azione di eliminazione

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere un'API](add-api.md)
