---
title: Creare un'istanza di Gestione API di Azure tramite Visual Studio Code | Microsoft Docs
description: Visual Studio Code per creare un'istanza di Gestione API di Azure.
ms.service: api-management
ms.workload: integration
author: vladvino
ms.author: apimpm
ms.topic: quickstart
ms.date: 09/14/2020
ms.openlocfilehash: 19080679291b88b693c95bd71f8ddc0e59286356
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2020
ms.locfileid: "90057350"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-using-visual-studio-code"></a>Avvio rapido: Creare una nuova istanza del servizio Gestione API di Azure usando Visual Studio Code

Gestione API di Azure consente alle organizzazioni di pubblicare API per permettere a sviluppatori esterni, partner e interni di sfruttare tutte le potenzialità dei dati e dei servizi. Gestione API fornisce le competenze fondamentali per assicurare un programma API di successo attraverso il coinvolgimento degli sviluppatori, informazioni aziendali approfondite, strumenti di analisi, sicurezza e protezione. Gestione API di Azure consente di creare e gestire gateway API moderni per servizi back-end esistenti ospitati ovunque. Per altre informazioni, vedere la [panoramica](api-management-key-concepts.md).

Questo argomento di avvio rapido illustra i passaggi per la creazione di una nuova istanza di Gestione API con l'*anteprima dell'estensione di Gestione API* per Visual Studio Code. È possibile usare l'estensione anche per eseguire operazioni di gestione comuni nell'istanza di Gestione API.

## <a name="prerequisites"></a>Prerequisiti 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Verificare inoltre di aver installato quanto segue:

- [Visual Studio Code](https://code.visualstudio.com/)

- [Azure API Management Extension for Visual Studio Code (Preview)](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement&ssr=false#overview)

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Avviare Visual Studio Code e aprire l'estensione di Azure. Se l'icona di Azure non è visualizzata nella barra attività, assicurarsi che l'estensione di *Gestione API di Azure* sia abilitata.

Selezionare **Accedi ad Azure** per avviare una finestra del browser e accedere al proprio account Microsoft.

![Accedere ad Azure dall'estensione di Gestione API per VS Code](./media/vscode-create-service-instance/vscode-apim-login.png)

## <a name="create-an-api-management-service"></a>Creare un servizio Gestione API

Dopo l'accesso all'account Microsoft, il riquadro di navigazione di *Azure: Gestione API* elencherà le sottoscrizioni di Azure di cui si dispone.

Fare clic con il pulsante destro del mouse sulla sottoscrizione che si intende usare e scegliere l'opzione di **creazione di Gestione API in Azure**.

![Creazione guidata di Gestione API in VS Code](./media/vscode-create-service-instance/vscode-apim-create.png)

Nel riquadro visualizzato specificare un nome per la nuova istanza di Gestione API. Deve essere globalmente univoco all'interno di Azure, contenere da 1 a 50 caratteri alfanumerici e/o trattini, iniziare con una lettera e terminare con un carattere alfanumerico.

Verrà così creata una nuova istanza di Gestione API (insieme a un gruppo di risorse padre) con il nome specificato. Per impostazione predefinita, l'istanza viene creata nell'area *Stati Uniti occidentali* con SKU *Consumo*.

> [!TIP]
> Se si abilita la **creazione avanzata** nelle *impostazioni dell'estensione di Gestione API di Azure*, è anche possibile specificare uno [SKU di Gestione API](https://azure.microsoft.com/pricing/details/api-management/), un'[area di Azure](https://status.azure.com/en-us/status) e un [gruppo di risorse](../azure-resource-manager/management/overview.md) per distribuire l'istanza di Gestione API.
>
> Se per il provisioning dello SKU *Consumo* è necessario meno di un minuto, la creazione di altri SKU in genere richiede 30-40 minuti.

A questo punto, si è pronti per importare e pubblicare la prima API. È possibile eseguire questa operazione e altre operazioni comuni di Gestione API all'interno dell'estensione per Visual Studio Code. Per altre informazioni, vedere la documentazione sull'[estensione di Gestione API per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement&ssr=false#overview).

![Istanza di Gestione API appena creata nel riquadro dell'estensione di Gestione API per VS Code](./media/vscode-create-service-instance/vscode-apim-instance.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessaria, rimuovere l'istanza di Gestione API facendo clic con il pulsante destro del mouse e scegliendo **Apri nel portale** per [eliminare il servizio Gestione API](get-started-create-service-instance.md#clean-up-resources) e il relativo gruppo di risorse.

In alternativa, è possibile selezionare **Elimina Gestione API** per eliminare solo l'istanza di Gestione API. Questa operazione non ne elimina il gruppo di risorse.

![Eliminare l'istanza di Gestione API da VS Code](./media/vscode-create-service-instance/vscode-apim-delete.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Importare e pubblicare la prima API](import-and-publish.md)
