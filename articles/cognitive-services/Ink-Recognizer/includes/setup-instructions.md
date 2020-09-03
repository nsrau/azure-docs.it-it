---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 9d46b304d598b4830cf325909f77eea6b68af757
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89304035"
---
>[!NOTE]
> Gli endpoint per le risorse create dopo il 1° luglio 2019 usano il formato di sottodominio personalizzato riportato di seguito. Per altre informazioni e per un elenco completo degli endpoint a livello di area, vedere [Nomi di sottodomini personalizzati per Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

I Servizi cognitivi di Azure sono rappresentati dalle risorse di Azure a cui si effettua la sottoscrizione. Creare una risorsa per il riconoscimento input penna usando il [portale Azure](../../cognitive-services-apis-create-account.md).

Dopo aver creato una risorsa, ottenere l'endpoint e il codice aprendo la risorsa nel [portale di Azure](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade) e facendo clic su **Avvio rapido**.

Creare due [variabili di ambiente](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource):

* `INK_RECOGNITION_SUBSCRIPTION_KEY`: la chiave di sottoscrizione per l'autenticazione delle richieste. 

* `INK_RECOGNITION_ENDPOINT`: l'endpoint per la risorsa. L'aspetto sarà simile al seguente: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com`   
