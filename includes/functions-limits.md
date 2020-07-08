---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/04/2020
ms.author: glenga
ms.openlocfilehash: 37a61e270f95ffdc420536046e0ce1eda1e489b4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85391485"
---
| Risorsa |[Piano a consumo](../articles/azure-functions/functions-scale.md#consumption-plan)|[Piano Premium](../articles/azure-functions/functions-scale.md#premium-plan)|[Piano dedicato](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/aks/quotas-skus-regions.md) |
| --- | --- | --- | --- | --- | --- |
|[Durata timeout](../articles/azure-functions/functions-scale.md#timeout) predefinita (min) |5 | 30 |30<sup>1</sup> | 30 | 30 |
|[Durata massima timeout](../articles/azure-functions/functions-scale.md#timeout) (min) |10 | senza limiti<sup>7</sup> | senza limiti<sup>2</sup> | unbounded | unbounded |
| Numero massimo di connessioni in uscita (per istanza) | 600 attivo (1200 totale) | unbounded | unbounded | unbounded | unbounded |
| Dimensioni massime richiesta (MB)<sup>3</sup> | 100 | 100 | 100 | 100 | Dipende dal cluster |
| Lunghezza massima stringa di query<sup>3</sup> | 4096 | 4096 | 4096 | 4096 | Dipende dal cluster |
| Lunghezza massima URL richiesta<sup>3</sup> | 8192 | 8192 | 8192 | 8192 | Dipende dal cluster |
|[ACU](../articles/virtual-machines/windows/acu.md) per istanza | 100 | 210-840 | 100-840 | 210-250<sup>8</sup> | [Prezzi del servizio Azure Kubernetes](https://azure.microsoft.com/pricing/details/container-service/) |
| Memoria massima (GB per istanza) | 1.5 | 3,5-14 | 1,75-14 | 3,5-14 | Qualsiasi nodo è supportato |
| App per le funzioni per piano |100 |100 |senza limiti<sup>4</sup> | unbounded | unbounded |
| [Piani del servizio app](../articles/app-service/overview-hosting-plans.md) | 100 per [area](https://azure.microsoft.com/global-infrastructure/regions/) |100 per gruppo di risorse |100 per gruppo di risorse | - | - |
| Archiviazione<sup>5</sup> |5 TB |250 GB |50-1000 GB | 1 TB | n/d |
| Domini personalizzati per applicazione</a> |500<sup>6</sup> |500 |500 | 500 | n/d |
| Supporto per il dominio personalizzato [SSL](../articles/app-service/configure-ssl-bindings.md) |connessione SNI SSL non associata inclusa | SNI SSL non vincolate e 1 IP SSL connessioni incluse |SNI SSL non vincolate e 1 IP SSL connessioni incluse | SNI SSL non vincolate e 1 IP SSL connessioni incluse | n/d |

<sup>1</sup> per impostazione predefinita, il timeout per il runtime di funzioni 1. x in un piano di servizio app non è associato.  
<sup>2</sup> richiede che il piano di servizio app sia impostato su [Always on](../articles/azure-functions/functions-scale.md#always-on). Paga con [tariffe](https://azure.microsoft.com/pricing/details/app-service/)standard.  
<sup>3</sup> questi limiti vengono [impostati nell'host](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>4</sup> il numero effettivo di app per le funzioni che è possibile ospitare dipende dall'attività delle app, dalle dimensioni delle istanze del computer e dall'utilizzo corrispondente delle risorse.  
<sup>5</sup> il limite di archiviazione è la dimensione totale del contenuto nell'archiviazione temporanea tra tutte le app nello stesso piano di servizio app. Il piano a consumo usa File di Azure per l'archiviazione temporanea.  
<sup>6</sup> quando l'app per le funzioni è ospitata in un [piano a consumo](../articles/azure-functions/functions-scale.md#consumption-plan), è supportata solo l'opzione CNAME. Per le app per le funzioni in un [piano Premium](../articles/azure-functions/functions-scale.md#premium-plan) o un [piano di servizio app](../articles/azure-functions/functions-scale.md#app-service-plan), è possibile eseguire il mapping di un dominio personalizzato usando un record CNAME o un record a.  
<sup>7</sup> garantito per un massimo di 60 minuti.  
<sup>8</sup> i ruoli di lavoro ospitano le app del cliente. I ruoli di lavoro sono disponibili in tre dimensioni fisse: una vCPU/3,5 GB di RAM; Due vCPU/7 GB di RAM; Quattro vCPU/14 GB di RAM.
