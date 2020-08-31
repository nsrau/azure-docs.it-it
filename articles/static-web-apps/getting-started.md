---
title: 'Avvio rapido: Creazione della prima app Web statica con App Web statiche di Azure'
description: Informazioni su come creare un sito Web App Web statiche di Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: db3836e6171d187539b8615efcb5ab782c368020
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752573"
---
# <a name="quickstart-building-your-first-static-web-app"></a>Avvio rapido: Creazione della prima app Web statica

App Web statiche di Azure consente di pubblicare un sito Web in un ambiente di produzione creando app da un repository GitHub. In questa guida di avvio rapido si distribuisce un'applicazione Web in App Web statiche di Azure usando l'estensione per Visual Studio Code.

Se non si ha una sottoscrizione di Azure, creare un [account per una versione di prova gratuita](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Prerequisiti

- [GitHub](https://github.com)
- Account [Azure](https://portal.azure.com)
- [Visual Studio Code](https://code.visualstudio.com)
- [Estensione App Web statiche di Azure per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Aprire quindi Visual Studio Code e passare a **File > Apri cartella** per aprire nell'editor il repository appena clonato sul computer.

## <a name="create-a-static-web-app"></a>Creare un'app Web statica

1. In Visual Studio Code selezionare il logo di Azure sulla barra delle attività per aprire la finestra delle estensioni di Azure.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Logo di Azure":::

    > [!NOTE]
    > Sono necessari l'accesso ad Azure e GitHub. Se non è già stato effettuato l'accesso ad Azure e GitHub da Visual Studio Code, l'estensione richiederà di accedere a entrambi durante il processo di creazione.

1. Posizionare il puntatore del mouse sull'etichetta _App Web statiche_ e selezionare il **segno più**.

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="Nome applicazione":::

1. Nella parte superiore dell'editor verrà visualizzato il riquadro comandi, in cui verrà richiesto di assegnare un nome all'applicazione.

    Digitare **my-first-static-web-app** e premere **INVIO**.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Creare l'app Web statica":::

1. Selezionare il ramo **master** e premere **INVIO**.

    :::image type="content" source="media/getting-started/extension-branch.png" alt-text="Nome del ramo":::

1. Selezionare **/** come posizione per il codice dell'applicazione e premere **INVIO**.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Posizione del codice dell'applicazione":::

1. L'estensione cerca la posizione dell'API nell'applicazione. Questo articolo non implementa un'API.

    Selezionare **Skip for now** (Ignora per ora) e premere **INVIO**.

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="Posizione dell'API":::

1. Selezionare la posizione in cui vengono compilati i file per la produzione nell'app.

    # <a name="no-framework"></a>[Nessun framework](#tab/vanilla-javascript)

    Deselezionare la casella e premere **INVIO**.

    :::image type="content" source="media/getting-started/extension-artifact-no-framework.png" alt-text="Percorso dei file dell'app":::

    # <a name="angular"></a>[Angular](#tab/angular)

    Digitare **dist/angular-basic** e premere **INVIO**.

    :::image type="content" source="media/getting-started/extension-artifact-angular.png" alt-text="Percorso dei file di un'app Angular":::

    # <a name="react"></a>[React](#tab/react)

    Digitare **build** e premere **INVIO**.

    :::image type="content" source="media/getting-started/extension-artifact-react.png" alt-text="Percorso dei file di un'app React":::

    # <a name="vue"></a>[Vue](#tab/vue)

    Digitare **dist** e premere **INVIO**.

    :::image type="content" source="media/getting-started/extension-artifact-vue.png" alt-text="Percorso dei file di un'app Vue":::

    ---

1. Selezionare la località più vicina e premere **INVIO**.

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="Posizione risorsa":::

1. Al termine della creazione dell'app, in Visual Studio Code verrà visualizzata una notifica di conferma.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="Conferma della creazione":::

1. Nella finestra di esplorazione di Visual Studio Code tornare alla sezione _App Web statiche_, fare clic con il pulsante destro del mouse su **Produzione** e quindi scegliere **Apri nel portale** per visualizzare l'app nel portale di Azure.

    :::image type="content" source="media/getting-started/extension-open-in-portal.png" alt-text="Aprire il portale":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Pulizia delle risorse

Se non si intende continuare a usare questa applicazione, è possibile eliminare l'istanza di App Web statiche di Azure tramite l'estensione.

Nella finestra di esplorazione di Visual Studio Code tornare alla sezione _App Web statiche_, fare clic con il pulsante destro del mouse su **my-first-static-web-app** e quindi scegliere **Elimina**.

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="Eliminare l'app":::

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere un'API](add-api.md)
