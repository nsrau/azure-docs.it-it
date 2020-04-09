---
title: Supporto dei contenitori
titleSuffix: Azure Cognitive Services
description: Informazioni su come creare una risorsa istanza del contenitore di Azure.Learn how to create an Azure container instance resource.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 09957461fd92114d1181a570550ee1a189edd8ea
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876433"
---
## <a name="create-an-azure-container-instance-resource"></a>Creare una risorsa istanza contenitore di AzureCreate an Azure Container Instance resource

1. Passare alla pagina [Crea](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) per le istanze del contenitore.

2. Nella scheda **Nozioni di base** immettere i dettagli seguenti:

    |Impostazione|valore|
    |--|--|
    |Subscription|Selezionare la propria sottoscrizione.|
    |Resource group|Selezionare il gruppo di risorse disponibile `cognitive-services`o crearne uno nuovo, ad esempio .|
    |Nome contenitore|Immettere un `cognitive-container-instance`nome, ad esempio . Il nome deve essere in maiuscoletto inferiore.|
    |Location|Selezionare un'area per la distribuzione.|
    |Tipo di immagine|Se l'immagine del contenitore viene archiviata in un `Public`registro contenitori che non richiede credenziali, scegliere . Se l'accesso all'immagine del `Private`contenitore richiede credenziali, scegliere . Fare riferimento [ai repository contenitore e](../../cognitive-services-container-support.md#container-repositories-and-images) alle immagini per `Public` `Private` informazioni dettagliate sull'o meno sull'immagine del contenitore o ("Anteprima pubblica"). |
    |Nome dell'immagine|Immettere il percorso del contenitore Servizi cognitivi. Il percorso è quello che viene `docker pull` utilizzato come argomento del comando. Fare riferimento [ai repository contenitore e](../../cognitive-services-container-support.md#container-repositories-and-images) alle immagini per i nomi di immagine disponibili e il repository corrispondente.<br><br>Il nome dell'immagine deve essere completo specificando tre parti. In primo luogo, il registro contenitori, `<container-registry>/<repository>/<image-name>`quindi il repository, infine il nome dell'immagine: .<br><br>Ecco un esempio, rappresenterebbe l'immagine di estrazione di frasi chiave nel Registro di sistema del contenitore Microsoft nel repository di Servizi cognitivi di Azure.Here is an example, `mcr.microsoft.com/azure-cognitive-services/keyphrase` would represent the Key Phrase Extraction image in the Microsoft Container Registry under the Azure Cognitive Services repository. Un altro `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` esempio è, che rappresenterebbe l'immagine di sintesi vocale nel repository Microsoft del registro contenitori Container Preview. |
    |Tipo di sistema operativo|`Linux`|
    |Dimensione|Modificare le dimensioni con i consigli suggeriti per il contenitore del servizio cognitivo specifico:Change size to the suggested recommendations for your specific Cognitive Service container:<br>2 core CPU<br>4 GB

3. Nella scheda **Rete** immettere i dettagli seguenti:

    |Impostazione|valore|
    |--|--|
    |Porte|Impostare la `5000`porta TCP su . Espone il contenitore sulla porta 5000.|

4. Nella scheda **Avanzate** immettere le **variabili di ambiente** necessarie per le impostazioni di fatturazione del contenitore della risorsa Istanza contenitore di Azure:

    | Chiave | valore |
    |--|--|
    |`apikey`|Copiato dalla pagina **Chiavi** della risorsa. Si tratta di una stringa di 32 caratteri alfanumerici senza spazi o trattini, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Copiato dalla pagina **Panoramica** della risorsa.|
    |`eula`|`accept`|

5. Fare clic su **Revisione e creazione**
6. Al termine della convalida, fare clic su **Crea** per completare il processo di creazione
7. Quando la risorsa viene distribuita correttamente, è pronta
