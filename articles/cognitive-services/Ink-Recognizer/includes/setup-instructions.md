---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: c68e5b7ab24e2d7e7f30ddc356ae3c4382137507
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369088"
---
>[!NOTE]
> Gli endpoint per le risorse create dopo il 1° luglio 2019 usano il formato di sottodominio personalizzato riportato di seguito. Per altre informazioni e per un elenco completo degli endpoint a livello di area, vedere [Nomi di sottodomini personalizzati per Servizi cognitivi](../../cognitive-services-custom-subdomains.md). 

I Servizi cognitivi di Azure sono rappresentati dalle risorse di Azure a cui si effettua la sottoscrizione. Creare una risorsa per il riconoscimento input penna usando il [portale Azure](../../cognitive-services-apis-create-account.md).

Dopo aver creato una risorsa, ottenere l'endpoint e il codice aprendo la risorsa nel [portale di Azure](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade) e facendo clic su **Avvio rapido**.

Creare due [variabili di ambiente](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource):

* `INK_RECOGNITION_SUBSCRIPTION_KEY`: la chiave di sottoscrizione per l'autenticazione delle richieste. 

* `INK_RECOGNITION_ENDPOINT`: l'endpoint per la risorsa. L'aspetto sarà simile al seguente: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com`