---
author: ggailey777
ms.service: cost-management-billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: 4643bb7f95e4fd1249d3ab6699c1f835c77f18fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77198344"
---
| Risorsa | [Piano di consumo](../articles/azure-functions/functions-scale.md#consumption-plan) | [Piano Premium](../articles/azure-functions/functions-scale.md#premium-plan) | [Piano di servizio app](../articles/azure-functions/functions-scale.md#app-service-plan)<sup>1</sup> |
| --- | --- | --- | --- |
| Scalabilità orizzontale | Basate su eventi | Basate su eventi | [Scala manuale/automatica](../articles/app-service/manage-scale-up.md) | 
| Numero massimo di istanzeMax instances | 200 | 100 | 10-20 |
|[Durata di timeout](../articles/azure-functions/functions-scale.md#timeout) predefinita (min) |5 | 30 |30<sup>(di esempio)</sup> |
|[Durata massima timeout](../articles/azure-functions/functions-scale.md#timeout) (min) |10 | unbounded<sup>8</sup> | <sup>unbounded 3</sup> |
| Numero massimo di connessioni in uscita (per istanza)Max outbound connections (per instance) | 600 attivo (1200 in totale) | unbounded | unbounded |
| Dimensione massima richiesta (MB)<sup>4</sup> | 100 | 100 | 100 |
| Lunghezza massima della stringa di query<sup>4Max</sup> query string length 4 | 4096 | 4096 | 4096 |
| Lunghezza massima URL richiesta<sup>4</sup> | 8192 | 8192 | 8192 |
| [ACU](../articles/virtual-machines/windows/acu.md) per istanza | 100 | 210-840 | 100-840 |
| Memoria massima (GB per istanza) | 1.5 | 3.5-14 | 1.75-14 |
| App per funzioni per piano |100 |100 |<sup>unbounded 5</sup> |
| [Piani di servizio app](../articles/app-service/overview-hosting-plans.md) | 100 per [regione](https://azure.microsoft.com/global-infrastructure/regions/) |100 per gruppo di risorse |100 per gruppo di risorse |
| Stoccaggio<sup>6</sup> |1 GB |250 GB |50-1000 GB |
| Domini personalizzati per applicazione</a> |500<sup>7</sup> |500 |500 |
| Supporto per il dominio personalizzato [SSL](../articles/app-service/configure-ssl-bindings.md) |connessione SSL SNI senza limiti inclusa | connessioni SSL SNI unbounded e 1 IP SSL incluse |connessioni SSL SNI unbounded e 1 IP SSL incluse | 

<sup>1</sup> Per i limiti specifici per le varie opzioni del piano di servizio app, vedere Limiti del piano di [servizio app](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).  
<sup>2</sup> Per impostazione predefinita, il timeout per il runtime di Funzioni 1.x in un piano di servizio app è illimitato.  
<sup>3</sup> Richiede che il piano di servizio app sia impostato su [Always On](../articles/azure-functions/functions-scale.md#always-on). Pagare a [tariffe](https://azure.microsoft.com/pricing/details/app-service/)standard .  
<sup>4</sup> Questi limiti sono [impostati nell'host](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>5</sup> Il numero effettivo di app per le funzioni che è possibile ospitare dipende dall'attività delle app, dalle dimensioni delle istanze del computer e dall'utilizzo delle risorse corrispondenti.  
<sup>6</sup> Il limite di archiviazione è la dimensione totale del contenuto nello spazio di archiviazione temporaneo in tutte le app nello stesso piano di servizio app. Piano di consumo usa File di Azure per l'archiviazione temporanea.  
<sup>7</sup> Quando l'app per le funzioni è ospitata in un piano di [consumo](../articles/azure-functions/functions-scale.md#consumption-plan), è supportata solo l'opzione CNAME. Per le app per le funzioni in un [piano Premium](../articles/azure-functions/functions-scale.md#premium-plan) o in un piano di [servizio app,](../articles/azure-functions/functions-scale.md#app-service-plan)è possibile eseguire il mapping di un dominio personalizzato usando un record CNAME o A.  
<sup>8</sup> Garantito fino a 60 minuti.