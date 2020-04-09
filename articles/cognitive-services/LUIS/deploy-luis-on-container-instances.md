---
title: Distribuire il contenitore LUIS nelle istanze del contenitore di AzureDeploy LUIS container on Azure Container instances
titleSuffix: Azure Cognitive Services
description: Distribuire il contenitore LUIS in un'istanza di Azure Container e testarlo in un Web browser.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: aahi
ms.openlocfilehash: 08af17106846a0f5f7a0ccc2b01da1b2e15c1143
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879209"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>Distribuire il contenitore Language Understanding (LUIS) nelle istanze del contenitore di AzureDeploy the Language Understanding (LUIS) container to Azure Container instances

Informazioni su come distribuire il contenitore LUIS di Servizi [cognitivi](luis-container-howto.md) nelle [istanze del contenitore](https://docs.microsoft.com/azure/container-instances/)di Azure. In questa procedura viene illustrata la creazione di una risorsa Rilevatore anomalie. Quindi viene illustrato il pull dell'immagine contenitore associata. Infine, evidenziamo la possibilità di esercitare l'orchestrazione dei due da un browser. L'utilizzo dei contenitori può spostare l'attenzione degli sviluppatori dalla gestione dell'infrastruttura al contrario concentrandosi sullo sviluppo di applicazioni.

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="create-an-azure-file-share"></a>Creare una condivisione file di Azure

Il contenitore LUIS richiede un `.gz` file di modello che viene estratto in fase di esecuzione. Il contenitore deve essere in grado di accedere a questo file di modello tramite un montaggio del volume dall'istanza del contenitore. Per informazioni sulla creazione di una condivisione file di Azure, vedere [Creare una condivisione file.](../../storage/files/storage-how-to-create-file-share.md) Prendere nota del nome dell'account di Archiviazione di Azure, della chiave e del nome della condivisione file in un secondo momento.

### <a name="export-and-upload-packaged-luis-app"></a>Esportare e caricare l'app LUIS in pacchetto

Per caricare il modello LUIS (app in pacchetto) nella condivisione file di Azure, è necessario <a href="luis-container-howto.md#export-packaged-app-from-luis" target="_blank" rel="noopener">esportarlo prima <span class="docon docon-navigate-external x-hidden-focus"> </span>dal portale LUIS </a>. Nel portale di Azure passare alla pagina **Panoramica** della risorsa dell'account di archiviazione e selezionare **Condivisioni file.** Seleziona il nome della condivisione file che hai creato di recente, quindi seleziona il pulsante **Carica.**

> [!div class="mx-imgBorder"]
> ![Carica in condivisione file](media/luis-how-to-deploy-to-aci/upload-file-share.png)

Caricare il file del modello LUIS.

[!INCLUDE [Create LUIS Container instance resource](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]
