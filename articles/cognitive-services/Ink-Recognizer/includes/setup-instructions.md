---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: ede1fb4bd2a9a6e6536959053e3ca4d8e4a82f87
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327358"
---
>[!NOTE]
> Gli endpoint per le risorse create dopo il 1° luglio 2019 usano il formato di sottodominio personalizzato riportato di seguito. Per altre informazioni e per un elenco completo degli endpoint a livello di area, vedere [Nomi di sottodomini personalizzati per Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

I Servizi cognitivi di Azure sono rappresentati dalle risorse di Azure a cui si effettua la sottoscrizione. Creare una risorsa per il riconoscimento input penna usando il [portale Azure](../../cognitive-services-apis-create-account.md).

Dopo aver creato una risorsa, ottenere l'endpoint e il codice aprendo la risorsa nel [portale di Azure](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade) e facendo clic su **Avvio rapido**.

Creare due [variabili di ambiente](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource):

* `INK_RECOGNITION_SUBSCRIPTION_KEY`: la chiave di sottoscrizione per l'autenticazione delle richieste. 

* `INK_RECOGNITION_ENDPOINT`: l'endpoint per la risorsa. L'aspetto sarà simile al seguente: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com`   
