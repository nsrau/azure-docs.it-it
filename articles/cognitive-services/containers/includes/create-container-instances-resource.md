---
title: Supporto dei contenitori
titleSuffix: Azure Cognitive Services
description: Informazioni su come creare una risorsa di istanza di contenitore di Azure.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/3/2019
ms.author: dapine
ms.openlocfilehash: 38addf4651373ba0f4df411325218a255c835508
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711406"
---
## <a name="create-an-azure-container-instance-resource"></a>Creare una risorsa di istanza di contenitore di Azure

1. Andare alla [Create](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) pagina per istanze di contenitore.

2. Nel **nozioni di base** , immettere i dettagli seguenti:

    |Impostazione|Value|
    |--|--|
    |Sottoscrizione|Selezionare la propria sottoscrizione.|
    |Gruppo di risorse|Selezionare il gruppo di risorse disponibili oppure crearne uno nuovo, ad esempio `cognitive-services`.|
    |Nome contenitore|Immettere un nome, ad esempio `cognitive-container-instance`. Il nome deve essere nell'estremità inferiore.|
    |Location|Selezionare un'area per la distribuzione.|
    |Tipo di immagine|`Public`|
    |Nome dell'immagine|Immettere il percorso del contenitore di servizi cognitivi. La posizione può essere lo stesso usato nel `docker pull` comando, vedere la [repository di contenitori e immagini](../../cognitive-services-container-support.md#container-repositories-and-images) per i nomi delle immagini disponibili e i repository corrispondenti.|
    |Tipo di sistema operativo|`Linux`|
    |Dimensione|Modificare le dimensioni per le raccomandazioni suggerite per il contenitore di servizi cognitivi specifico:<br>2 core CPU<br>4 GB

3. Nel **Networking** , immettere i dettagli seguenti:

    |Impostazione|Value|
    |--|--|
    |Porte|Impostare la porta TCP su `5000`. Espone il contenitore sulla porta 5000.|

4. Nel **avanzate** , immettere le necessarie **variabili di ambiente** per il contenitore [fatturazione impostazioni](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers#billing-arguments) della risorsa ACI:

    | Chiave | Value |
    |--|--|
    |`apikey`|Copiato dal **chiavi** pagina della risorsa Analitica di testo. È una stringa di caratteri alfanumerici 32 senza spazi o trattini, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Copiato dal **Panoramica** pagina della risorsa Analitica di testo. Esempio: `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |`eula`|`accept`|

1. Fare clic su **esaminare e creare**
1. Dopo la convalida ha esito positivo, fare clic su **Create** per completare il processo di creazione
1. Quando la risorsa venga distribuita correttamente, è pronto