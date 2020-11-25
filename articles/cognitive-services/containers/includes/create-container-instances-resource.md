---
title: Supporto dei contenitori
titleSuffix: Azure Cognitive Services
description: Informazioni su come creare una risorsa dell'istanza di contenitore di Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 09957461fd92114d1181a570550ee1a189edd8ea
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009867"
---
## <a name="create-an-azure-container-instance-resource"></a>Creare una risorsa dell'istanza di contenitore di Azure

1. Passare alla pagina [Crea](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) per le istanze di contenitore.

2. Nella scheda informazioni di **base** immettere i dettagli seguenti:

    |Impostazione|Valore|
    |--|--|
    |Subscription|Selezionare la propria sottoscrizione.|
    |Resource group|Selezionare il gruppo di risorse disponibile o crearne uno nuovo, ad esempio `cognitive-services` .|
    |Nome contenitore|Immettere un nome, ad esempio `cognitive-container-instance` . Il nome deve essere in maiuscolo.|
    |Location|Selezionare un'area per la distribuzione.|
    |Tipo di immagine|Se l'immagine del contenitore è archiviata in un registro contenitori che non richiede credenziali, scegliere `Public` . Se l'accesso all'immagine del contenitore richiede credenziali, scegliere `Private` . Per informazioni [container repositories and images](../../cognitive-services-container-support.md#container-repositories-and-images) dettagliate sul fatto che l'immagine del contenitore sia `Public` o `Private` ("anteprima pubblica"), vedere repository del contenitore e immagini. |
    |Nome dell'immagine|Immettere il percorso del contenitore di servizi cognitivi. Il percorso è quello usato come argomento del `docker pull` comando. Vedere i [repository del contenitore e le immagini](../../cognitive-services-container-support.md#container-repositories-and-images) per i nomi delle immagini disponibili e il repository corrispondente.<br><br>Il nome dell'immagine deve essere completo specificando tre parti. Innanzitutto, il registro contenitori, quindi il repository, infine il nome dell'immagine: `<container-registry>/<repository>/<image-name>` .<br><br>Di seguito è riportato un esempio che `mcr.microsoft.com/azure-cognitive-services/keyphrase` rappresenta l'immagine estrazione frasi chiave in Microsoft container Registry nel repository dei servizi cognitivi di Azure. Un altro esempio è, `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` che rappresenterebbe il riconoscimento vocale nell'immagine di testo nel repository Microsoft del registro contenitori di anteprima del contenitore. |
    |Tipo di sistema operativo|`Linux`|
    |Dimensione|Modificare le dimensioni in base alle raccomandazioni suggerite per il contenitore di servizi cognitivi specifico:<br>2 core CPU<br>4 GB

3. Nella scheda **rete** immettere i dettagli seguenti:

    |Impostazione|Valore|
    |--|--|
    |Porte|Impostare la porta TCP su `5000` . Espone il contenitore sulla porta 5000.|

4. Nella scheda **Avanzate** immettere le **variabili di ambiente** necessarie per le impostazioni di fatturazione del contenitore della risorsa istanza di contenitore di Azure:

    | Chiave | Valore |
    |--|--|
    |`apikey`|Copiato dalla pagina **chiavi** della risorsa. Si tratta di una stringa di caratteri alfanumerici 32 senza spazi o trattini `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .|
    |`billing`|Copiato dalla pagina **Panoramica** della risorsa.|
    |`eula`|`accept`|

5. Fare clic su **Verifica e crea**
6. Al termine della convalida, fare clic su **Crea** per completare il processo di creazione
7. Quando la risorsa viene distribuita correttamente, è pronta
