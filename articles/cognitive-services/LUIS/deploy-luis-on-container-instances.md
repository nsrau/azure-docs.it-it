---
title: Distribuire il contenitore LUIS in istanze di contenitore di Azure
titleSuffix: Azure Cognitive Services
description: Distribuire il contenitore LUIS in un'istanza di contenitore di Azure e testarlo in un Web browser.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: aahi
ms.openlocfilehash: edc2ad0f895b8a1bb6448ce1cdf79b1b2ce83951
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95997197"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>Distribuire il contenitore Language Understanding (LUIS) in istanze di contenitore di Azure

Informazioni su come distribuire il contenitore [Luis](luis-container-howto.md) di servizi cognitivi in [istanze di contenitore](../../container-instances/index.yml)di Azure. Questa procedura illustra la creazione di una risorsa del rilevatore di anomalie. Viene quindi illustrato il pull dell'immagine del contenitore associata. Infine, viene evidenziata la possibilità di eseguire l'orchestrazione dei due da un browser. L'uso dei contenitori può spostare l'attenzione degli sviluppatori fuori dalla gestione dell'infrastruttura per concentrarsi invece sullo sviluppo di applicazioni.

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="create-an-azure-file-share"></a>Creare una condivisione file di Azure

Il contenitore LUIS richiede un `.gz` file di modello di cui è stato eseguito il pull in fase di esecuzione. Il contenitore deve essere in grado di accedere al file del modello tramite un montaggio del volume dall'istanza del contenitore. Per informazioni sulla creazione di una condivisione file di Azure, vedere [creare una condivisione file](../../storage/files/storage-how-to-create-file-share.md). Prendere nota del nome dell'account di archiviazione di Azure, della chiave e del nome della condivisione file, perché saranno necessari in un secondo momento.

### <a name="export-and-upload-packaged-luis-app"></a>Esportare e caricare l'app LUIS in pacchetto

Per caricare il modello LUIS (app in pacchetto) nella condivisione file di Azure, è necessario <a href="luis-container-howto.md#export-packaged-app-from-luis" target="_blank" rel="noopener">prima <span class="docon docon-navigate-external x-hidden-focus"></span> esportarlo dal portale Luis </a>. Dalla portale di Azure passare alla pagina **Panoramica** della risorsa account di archiviazione e selezionare **condivisioni file**. Selezionare il nome della condivisione file creata di recente, quindi selezionare il pulsante **carica** .

> [!div class="mx-imgBorder"]
> ![Carica nella condivisione file](media/luis-how-to-deploy-to-aci/upload-file-share.png)

Caricare il file del modello LUIS.

[!INCLUDE [Create LUIS Container instance resource](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]