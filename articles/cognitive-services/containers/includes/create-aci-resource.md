---
title: Supporto dei contenitori
titleSuffix: Azure Cognitive Services
description: Informazioni su come creare una risorsa (ACI) istanza di contenitore di azure.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: b24a78873c3220b969fc548d2553e465e80773cd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455063"
---
## <a name="create-an-azure-container-instance-aci-resource"></a>Creare una risorsa di istanza di contenitore di Azure (ACI)

1. Andare alla [Create](https://ms.portal.azure.com/#create/Microsoft.ContainerInstances) pagina per istanze di contenitore.

2. Nel **nozioni di base** , immettere i dettagli seguenti:

    |Impostazione|Value|
    |--|--|
    |Sottoscrizione|Selezionare la propria sottoscrizione.|
    |Gruppo di risorse|Selezionare il gruppo di risorse disponibili oppure crearne uno nuovo, ad esempio `cognitive-services`.|
    |Nome contenitore|Immettere un nome, ad esempio `cognitive-container-instance`. Questo nome deve essere nell'estremità inferiore.|
    |Location|Selezionare un'area per la distribuzione.|
    |Tipo di immagine|`Public`|
    |Nome dell'immagine|Immettere il percorso del contenitore di servizi cognitivi. Può essere usato nella stessa posizione di `docker pull` comando _ad esempio_: <br>`mcr.microsoft.com/azure-cognitive-services/sentiment`|
    |Tipo di sistema operativo|`Linux`|
    |Dimensione|Modificare le dimensioni per le raccomandazioni suggerite per il contenitore di servizi cognitivi specifico.:<br>2 core<br>4 GB

3. Nel **Networking** , immettere i dettagli seguenti:

    |Impostazione|Value|
    |--|--|
    |Porte|Impostare la porta TCP su `5000`. Espone il contenitore sulla porta 5000.|

4. Nel **avanzate** , immettere i dettagli seguenti per passare attraverso il contenitore [fatturazione le impostazioni necessarie](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers#billing-arguments) alla risorsa ACI:

    |Chiave della pagina avanzate|Valore di pagina avanzate|
    |--|--|
    |`apikey`|Copiato dal **chiavi** pagina della risorsa Analitica di testo. È una stringa di caratteri alfanumerici 32 senza spazi o trattini, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.|
    |`billing`|Copiato dal **Panoramica** pagina della risorsa Analitica di testo. Esempio: `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`|
    |`eula`|`accept`|

1. Fare clic su **esaminare e creare**
1. Dopo la convalida ha esito positivo, fare clic su **Create** per completare il processo di creazione
1. Quando la risorsa venga distribuita correttamente, è pronto per l'utilizzo