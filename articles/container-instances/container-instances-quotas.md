---
title: Quote e aree disponibili per Istanze di Azure Container
description: Quote predefinite e aree disponibili per il servizio Istanze di Azure Container.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 12/17/2018
ms.author: danlep
ms.openlocfilehash: 647890517e6f08a4602ebed8ee1057cb45f10cbe
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54075499"
---
# <a name="quotas-and-region-availability-for-azure-container-instances"></a>Quote e aree disponibili per Istanze di Azure Container

Tutti i servizi di Azure prevedono determinati limiti predefiniti e quote per le risorse e le funzionalità. Le sezioni seguenti illustrano in modo dettagliato i limiti predefiniti per alcune risorse di Istanze di Azure Container e la disponibilità di questo servizio nelle aree di Azure.

## <a name="service-quotas-and-limits"></a>Quote e limiti del servizio

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

## <a name="region-availability"></a>Aree di disponibilità

Istanze di Azure Container è disponibile nelle aree seguenti con i limiti di CPU e memoria specificati. I valori sono aggiornati al momento della pubblicazione. Per informazioni aggiornate, usare l'API di [elenco delle funzionalità](/rest/api/container-instances/listcapabilities/listcapabilities). La disponibilità e i limiti delle risorse possono variare quando si usano le Istanze di Azure Container con una [rete virtuale](container-instances-vnet.md) (anteprima) o con le [risorse della GPU](container-instances-gpu.md) (anteprima).

| Località | OS | CPU | Memoria (GB) |
| -------- | -- | :---: | :-----------: |
| Canada centrale | Linux | 4 | 16 |
| Stati Uniti orientali, Europa settentrionale, Europa occidentale, Stati Uniti occidentali, Stati Uniti occidentali 2 | Linux | 4 | 14 |
| Giappone orientale | Linux | 2 | 8 |
| Australia orientale, Stati Uniti orientali 2, Asia sud-orientale | Linux | 2 | 7 |
| India centrale, Asia orientale, Stati Uniti centro-settentrionali, Stati Uniti centro-meridionali, India meridionale | Linux | 2 | 3,5 |
| Stati Uniti orientali, Europa occidentale, Stati Uniti occidentali |  Windows | 4 | 14 |
| Australia orientale, Canada centrale, India centrale, Asia orientale, Stati Uniti orientali 2, Giappone orientale, Stati Uniti centro-settentrionali, Europa settentrionale, Stati Uniti centro-meridionali, India meridionale, Asia sud-orientale, Stati Uniti occidentali 2 |  Windows | 2 | 3,5 |

La disponibilità di Istanze di Container con questi limiti di risorse dipende dell'area di distribuzione. Quando un'area ha un carico elevato, può verificarsi un errore durante la distribuzione delle istanze. Per mitigare tale errore di distribuzione, provare a distribuire le istanze con impostazioni di memoria e CPU inferiori oppure provare a eseguire la distribuzione in un secondo momento.

Segnalare al team aree geografiche aggiuntive necessarie o incrementi dei limiti di CPU/memoria all'indirizzo [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Per altre informazioni sulla risoluzione dei problemi di distribuzione di Istanze di Container, vedere [Risolvere i problemi di distribuzione di Istanze di Azure Container](container-instances-troubleshooting.md).

## <a name="next-steps"></a>Passaggi successivi

È possibile aumentare alcune quote e alcuni limiti predefiniti. Per richiedere un aumento di una o più risorse che lo consentono, inviare una [richiesta di supporto di Azure][azure-support] (selezionare "Quota" per **Tipo di problema**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
