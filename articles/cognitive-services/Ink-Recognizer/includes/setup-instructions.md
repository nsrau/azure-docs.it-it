---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 942bcc6b150f990f9a9acab0d4ef68bfb6125c1b
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996846"
---
>[!NOTE]
> Gli endpoint per le risorse create dopo il 1° luglio 2019 usano il formato di sottodominio personalizzato riportato di seguito. Per altre informazioni e per un elenco completo degli endpoint a livello di area, vedere [Nomi di sottodomini personalizzati per Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

I Servizi cognitivi di Azure sono rappresentati dalle risorse di Azure a cui si effettua la sottoscrizione. Creare una risorsa per il riconoscimento input penna usando il [portale Azure](../../cognitive-services-apis-create-account.md). 

* È anche possibile ottenere un [codice di valutazione](https://azure.microsoft.com/try/cognitive-services/#decision) gratuito valido per sette giorni. Dopo aver eseguito l'iscrizione, questo codice e un endpoint saranno disponibili sul [sito Web di Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).

Dopo aver creato una risorsa, ottenere l'endpoint e il codice aprendo la risorsa nel [portale di Azure](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade) e facendo clic su **Avvio rapido**.

Creare due [variabili di ambiente](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource):

* `INK_RECOGNITION_SUBSCRIPTION_KEY`: l'endpoint per la risorsa. L'aspetto sarà simile al seguente: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

* `INK_RECOGNITION_ENDPOINT`: la chiave di sottoscrizione per l'autenticazione delle richieste.   
