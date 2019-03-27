---
title: Disponibilità di risorse per Istanze di Azure Container
description: Disponibilità delle risorse di calcolo e memoria per il servizio Istanze di Azure Container in aree di Azure differenti.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 03/01/2019
ms.author: danlep
ms.openlocfilehash: 1ca23a95c746139963aa70ed20bb888152fd5cd8
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554871"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Disponibilità di risorse per Istanze di Azure Container nelle aree di Azure

Questo articolo illustra in dettaglio la disponibilità di risorse di calcolo e memoria per Istanze di Azure Container nelle aree di Azure. 

I valori presentati sono le risorse massime disponibili per distribuzione di un [gruppo di contenitori](container-instances-container-groups.md). I valori sono aggiornati al momento della pubblicazione. Per informazioni aggiornate, usare l'API di [elenco delle funzionalità](/rest/api/container-instances/listcapabilities/listcapabilities). 

> [!NOTE]
> I gruppi di contenitori con questi limiti di risorse dipendono dell'area di distribuzione. Quando un'area ha un carico elevato, può verificarsi un errore durante la distribuzione delle istanze. Per mitigare tale errore di distribuzione, provare a distribuire le istanze con impostazioni di risorsa inferiori oppure provare a eseguire la distribuzione in un secondo momento.

Per informazioni su quote e altri limiti nelle distribuzioni, vedere [Quote e limiti per Istanze di Azure Container](container-instances-quotas.md).

## <a name="availability---general"></a>Disponibilità - Generale

| Località | OS | CPU | Memoria (GB) |
| -------- | -- | :---: | :-----------: |
| Canada centrale, Stati Uniti centrali, Stati Uniti orientali 2, Stati Uniti centro-meridionali | Linux | 4 | 16 |
| Stati Uniti orientali, Europa settentrionale, Europa occidentale, Stati Uniti occidentali, Stati Uniti occidentali 2 | Linux | 4 | 14 |
| Giappone orientale | Linux | 2 | 8 |
| Australia orientale, Asia sud-orientale | Linux | 2 | 7 |
| India centrale, Asia orientale, Stati Uniti centro-settentrionali, India meridionale | Linux | 2 | 3,5 |
| Stati Uniti orientali, Europa occidentale, Stati Uniti occidentali | Windows | 4 | 14 |
| Australia orientale, Canada centrale, India centrale, Stati Uniti centrali, Asia orientale, Stati Uniti orientali 2, Giappone orientale, Stati Uniti centro-settentrionali, Europa settentrionale, Stati Uniti centro-meridionali, India meridionale, Asia sud-orientale, Stati Uniti occidentali 2 | Windows | 2 | 3,5 |

## <a name="availability---virtual-network-deployment-preview"></a>Disponibilità - Distribuzione nella rete virtuale (anteprima)

Le aree geografiche e le risorse seguenti sono disponibili per un gruppo di contenitori distribuito in una [rete virtuale di Azure](container-instances-vnet.md) (anteprima),

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Disponibilità - Risorse della GPU (anteprima)

Le aree geografiche e le risorse seguenti sono disponibili per un gruppo di contenitori distribuito con [risorse della GPU](container-instances-gpu.md) (anteprima),

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>Passaggi successivi

Segnalare al team se si desidera vedere altre aree geografiche o una maggiore disponibilità delle risorse all'indirizzo [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Per informazioni sulla risoluzione dei problemi di distribuzione di Istanze di Container, vedere [Risolvere i problemi di distribuzione di Istanze di Azure Container](container-instances-troubleshooting.md).
