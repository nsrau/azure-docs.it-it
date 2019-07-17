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
ms.openlocfilehash: 05284d434e6bd22fd50957f7cc5ec966f88a4fd4
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229322"
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

    |Impostazione|Valore|
    |--|--|
    |Porte|Impostare la porta TCP su `5000`. Espone il contenitore sulla porta 5000.|

4. Nel **avanzate** , immettere le necessarie **variabili di ambiente** le impostazioni della risorsa ACI di fatturazione per il contenitore:

    | Chiave | Value |
    |--|--|
    |`apikey`|Copiato dal **chiavi** pagina della risorsa. È una stringa di caratteri alfanumerici 32 senza spazi o trattini, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Copiato dal **Panoramica** pagina della risorsa.|
    |`eula`|`accept`|

1. Fare clic su **esaminare e creare**
1. Dopo la convalida ha esito positivo, fare clic su **Create** per completare il processo di creazione
1. Quando la risorsa venga distribuita correttamente, è pronto