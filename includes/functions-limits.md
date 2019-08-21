---
author: ggailey777
ms.service: billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: d00af77a2d502cb534a4ab4a0db9231e5e8748b7
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69636432"
---
| Risorsa | [Piano a consumo](../articles/azure-functions/functions-scale.md#consumption-plan) | [Piano Premium](../articles/azure-functions/functions-scale.md#premium-plan) | [Piano di servizio app](../articles/azure-functions/functions-scale.md#app-service-plan) <sup>1</sup> |
| --- | --- | --- | --- |
| Scalabilità orizzontale | Basato su eventi | Basato su eventi | [Scalabilità manuale/automatica](../articles/app-service/manage-scale-up.md) | 
| Numero massimo di istanze | 200 | 20 | 10-20 |
|[Durata predefinita timeout](../articles/azure-functions/functions-scale.md#timeout) (min) |5 | 30 |30<sup>2</sup> |
|[Durata](../articles/azure-functions/functions-scale.md#timeout) massima timeout (min) |10 | unbounded | senza limiti<sup>3</sup> |
| Numero massimo di connessioni in uscita (per istanza) | 600 attivo (1200 totale) | unbounded | unbounded |
| Dimensioni massime richiesta (MB)<sup>4</sup> | 100 | 100 | 100 |
| Lunghezza massima stringa di query<sup>4</sup> | 4096 | 4096 | 4096 |
| Lunghezza massima URL richiesta<sup>4</sup> | 8192 | 8192 | 8192 |
| [ACU](../articles/virtual-machines/windows/acu.md) per istanza | 100 | 210-840 | 100-840 |
| Memoria massima (GB per istanza) | 1,5 | 3,5-14 | 1,75-14 |
| App per le funzioni per piano |100 |100 |unbounded<sup>5</sup> |
| [Piani del servizio app](../articles/app-service/overview-hosting-plans.md) | 100 per [area](https://azure.microsoft.com/global-infrastructure/regions/) |100 per gruppo di risorse |100 per gruppo di risorse |
| Archiviazione<sup>6</sup> |1 GB |250 GB |50-1000 GB |
| Domini personalizzati per applicazione</a> |500<sup>7</sup> |500 |500 |
| Supporto per il dominio personalizzato [SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |connessione SNI SSL non associata inclusa | SNI SSL non vincolate e 1 IP SSL connessioni incluse |SNI SSL non vincolate e 1 IP SSL connessioni incluse | 

<sup>1</sup> per i limiti specifici per le varie opzioni del piano di servizio app, vedere i [limiti del piano di servizio app](../articles/azure-subscription-service-limits.md#app-service-limits).  
<sup>2</sup> per impostazione predefinita, il timeout per il runtime di funzioni 1. x in un piano di servizio app non è associato.  
<sup>3</sup> richiede che il piano di servizio app sia impostato su [Always on](../articles/azure-functions/functions-scale.md#always-on). Paga con [tariffe](https://azure.microsoft.com/pricing/details/app-service/)standard.  
<sup>4</sup> questi limiti vengono [impostati nell'host](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>5</sup> il numero effettivo di app per le funzioni che è possibile ospitare dipende dall'attività delle app, dalle dimensioni delle istanze del computer e dall'utilizzo corrispondente delle risorse.  
<sup>6</sup> il limite di archiviazione è la dimensione totale del contenuto nell'archiviazione temporanea tra tutte le app nello stesso piano di servizio app. Il piano a consumo usa File di Azure per l'archiviazione temporanea.  
<sup>7</sup> quando l'app per le funzioni è ospitata in un [piano a consumo](../articles/azure-functions/functions-scale.md#consumption-plan), è supportata solo l'opzione CNAME. Per le app per le funzioni in un [piano Premium](../articles/azure-functions/functions-scale.md#premium-plan) o un [piano di servizio app](../articles/azure-functions/functions-scale.md#app-service-plan), è possibile eseguire il mapping di un dominio personalizzato usando un record CNAME o un record a.
