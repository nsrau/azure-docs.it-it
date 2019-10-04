---
title: Supporto dei contenitori
titleSuffix: Azure Cognitive Services
description: Informazioni su come creare una risorsa dell'istanza di contenitore di Azure.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 8/21/2019
ms.author: dapine
ms.openlocfilehash: 07e2067571c7bc7403ee8a1d1a0600c451e1581f
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051183"
---
## <a name="create-an-azure-container-instance-resource"></a>Creare una risorsa dell'istanza di contenitore di Azure

1. Passare alla pagina [Crea](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) per le istanze di contenitore.

2. Nella scheda informazioni di **base** immettere i dettagli seguenti:

    |Impostazione|Value|
    |--|--|
    |Sottoscrizione|Selezionare la propria sottoscrizione.|
    |Gruppo di risorse|Selezionare il gruppo di risorse disponibile o crearne uno nuovo, `cognitive-services`ad esempio.|
    |Nome contenitore|Immettere un nome, ad `cognitive-container-instance`esempio. Il nome deve essere in maiuscolo.|
    |Location|Selezionare un'area per la distribuzione.|
    |Tipo immagine|Se l'immagine del contenitore è archiviata in un registro contenitori che non richiede credenziali `Public`, scegliere. Se l'accesso all'immagine del contenitore richiede credenziali, `Private`scegliere. Per informazioni dettagliate sul fatto che l'immagine del contenitore sia `Public` o `Private` ("anteprima pubblica"), vedere repository del [contenitore e immagini](../../cognitive-services-container-support.md#container-repositories-and-images) . |
    |Nome immagine|Immettere il percorso del contenitore di servizi cognitivi. Il percorso è quello usato come argomento del `docker pull` comando. Vedere i [repository del contenitore e le immagini](../../cognitive-services-container-support.md#container-repositories-and-images) per i nomi delle immagini disponibili e il repository corrispondente.<br><br>Il nome dell'immagine deve essere completo specificando tre parti. Innanzitutto, il registro contenitori, quindi il repository, infine il nome dell'immagine `<container-registry>/<repository>/<image-name>`:.<br><br>Di seguito è riportato un `mcr.microsoft.com/azure-cognitive-services/keyphrase` esempio che rappresenta l'immagine estrazione frasi chiave in Microsoft container Registry nel repository dei servizi cognitivi di Azure. Un altro esempio è `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` , che rappresenterebbe il riconoscimento vocale nell'immagine di testo nel repository Microsoft del registro contenitori di anteprima del contenitore. |
    |Tipo di sistema operativo|`Linux`|
    |Dimensione|Modificare le dimensioni in base alle raccomandazioni suggerite per il contenitore di servizi cognitivi specifico:<br>2 core CPU<br>4 GB

3. Nella scheda **rete** immettere i dettagli seguenti:

    |Impostazione|Valore|
    |--|--|
    |Porte|Impostare la porta TCP su `5000`. Espone il contenitore sulla porta 5000.|

4. Nella scheda **Avanzate** immettere le **variabili di ambiente** necessarie per le impostazioni di fatturazione del contenitore della risorsa istanza di contenitore di Azure:

    | Chiave | Value |
    |--|--|
    |`apikey`|Copiato dalla pagina **chiavi** della risorsa. Si tratta di una stringa di caratteri alfanumerici 32 senza spazi o trattini `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Copiato dalla pagina **Panoramica** della risorsa.|
    |`eula`|`accept`|

1. Fare clic su **Verifica e crea**
1. Al termine della convalida, fare clic su **Crea** per completare il processo di creazione
1. Quando la risorsa viene distribuita correttamente, è pronta