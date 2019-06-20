---
author: ggailey777
ms.service: billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: f2470f937d2d812bf79cea3c23d89a50717a5a92
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67277302"
---
| Resource | [Piano a consumo](../articles/azure-functions/functions-scale.md#consumption-plan) | [Piano Premium](../articles/azure-functions/functions-scale.md#premium-plan) | [Piano di servizio app](../articles/azure-functions/functions-scale.md#app-service-plan)<sup>1</sup> |
| --- | --- | --- | --- |
| Scalabilità orizzontale | Aktivita typu EventDriven | Aktivita typu EventDriven | [Scalabilità manuale o automatica](../articles/app-service/web-sites-scale.md) | 
| Numero massimo di istanze | 200 | 20 | 10-20 |
|Default [durata del timeout](../articles/azure-functions/functions-scale.md#timeout) (min) |5 | 30 |30<sup>2</sup> |
|Max [durata del timeout](../articles/azure-functions/functions-scale.md#timeout) (min) |10 | unbounded | unbounded<sup>3</sup> |
| Numero massimo di connessioni in uscita (per ogni istanza) | 600 active (totale di 1200) | unbounded | unbounded |
| Dimensioni richiesta massime (MB)<sup>4</sup> | 100 | 100 | 100 |
| Lunghezza della stringa di query max<sup>4</sup> | 4096 | 4096 | 4096 |
| La lunghezza massima consentita URL richiesta<sup>4</sup> | 8192 | 8192 | 8192 |
| [ACU](../articles/virtual-machines/windows/acu.md) per ogni istanza | 100 | 210-840 | 100-840 |
| Memoria massima (GB per istanza) | 1,5 | 3.5-14 | 1.75-14 |
| App per le funzioni per ogni piano |100 |100 |unbounded<sup>5</sup> |
| [Piani del servizio app](../articles/app-service/overview-hosting-plans.md) | 100 dollari per [area](https://azure.microsoft.com/global-infrastructure/regions/) |100 per gruppo di risorse |100 per gruppo di risorse |
| Archiviazione<sup>6</sup> |1 GB |250 GB |50-1000 GB |
| Domini personalizzati per applicazione</a> |500<sup>7</sup> |500 |500 |
| Supporto per il dominio personalizzato [SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |unbounded connessione SNI SSL incluso | unbounded connessioni SNI SSL e 1 IP SSL incluse |unbounded connessioni SNI SSL e 1 IP SSL incluse | 

<sup>1</sup> per i limiti specifici per le varie opzioni di piano di servizio App, vedere la [limiti del piano di servizio App](../articles/azure-subscription-service-limits.md#app-service-limits).  
<sup>2</sup> per impostazione predefinita, il timeout per il runtime 1.x di funzioni in un piano di servizio App è unbounded.  
<sup>3</sup> richiede il piano di servizio App sia impostata su [Always On](../articles/azure-functions/functions-scale.md#always-on). Pagare in standard [tariffe](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup> questi limiti riguardano [impostata nell'host](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>5</sup> al numero effettivo di App per le funzioni che è possibile ospitare dipende l'attività delle App, le dimensioni di istanze delle macchine e l'utilizzo delle risorse corrispondenti.
<sup>6</sup> il limite di archiviazione è la dimensione totale del contenuto nell'archiviazione temporanea in tutte le app nello stesso piano di servizio App. Piano a consumo Usa file di Azure per l'archiviazione temporanea.  
<sup>7</sup> quando l'app per le funzioni è ospitato un [piano a consumo](../articles/azure-functions/functions-scale.md#consumption-plan), solo l'opzione di CNAME è supportata. Per le app di funzione in una [Premium-piano](../articles/azure-functions/functions-scale.md#premium-plan) o un' [piano di servizio App](../articles/azure-functions/functions-scale.md#app-service-plan), è possibile eseguire il mapping di un dominio personalizzato usando un record CNAME o un record.
