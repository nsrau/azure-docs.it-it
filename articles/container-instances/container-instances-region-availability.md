---
title: Disponibilità delle risorse per area
description: Disponibilità delle risorse di calcolo e memoria per il servizio Istanze di Azure Container in aree di Azure differenti.
ms.topic: article
ms.date: 12/17/2019
ms.author: danlep
ms.openlocfilehash: 9de5b08eed3aa10015813cbb4724ef4e947005fb
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75888006"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Disponibilità di risorse per Istanze di Azure Container nelle aree di Azure

Questo articolo illustra in dettaglio la disponibilità di risorse di calcolo e memoria per Istanze di Azure Container nelle aree di Azure. 

I valori presentati sono le risorse massime disponibili per distribuzione di un [gruppo di contenitori](container-instances-container-groups.md). I valori sono aggiornati al momento della pubblicazione. 

> [!NOTE]
> I gruppi di contenitori con questi limiti di risorse dipendono dell'area di distribuzione. Quando un'area ha un carico elevato, può verificarsi un errore durante la distribuzione delle istanze. Per mitigare tale errore di distribuzione, provare a distribuire le istanze con impostazioni di risorsa inferiori oppure provare a eseguire la distribuzione in un secondo momento.

Per informazioni su quote e altri limiti nelle distribuzioni, vedere [Quote e limiti per Istanze di Azure Container](container-instances-quotas.md).

## <a name="availability---general"></a>Disponibilità - Generale

Per i gruppi di contenitori con Linux e i contenitori basati su Windows Server 2016 [supportati](container-instances-faq.md#what-windows-base-os-images-are-supported) sono disponibili le aree geografiche e le risorse seguenti.

| Aree | Sistema operativo | Max CPU (CPU max) | Memoria massima (GB) |
| -------- | -- | :---: | :-----------: |
| Brasile meridionale, Canada centrale, India centrale, Stati Uniti centrali, Asia orientale, Stati Uniti orientali, Stati Uniti orientali 2, Europa settentrionale, Stati Uniti centro-meridionali, Asia sudorientale, India meridionale, Regno Unito meridionale, Europa occidentale, Stati Uniti occidentali, Stati Uniti occidentali 2 | Linux | 4 | 16 |
| Australia orientale, Giappone orientale | Linux | 2 | 8 |
| Stati Uniti centro-settentrionali | Linux | 2 | 3,5 |
| Brasile meridionale, Giappone orientale, Europa occidentale | Windows | 4 | 16 |
| Stati Uniti orientali, Stati Uniti occidentali | Windows | 4 | 14 |
| Australia orientale, Canada centrale, India centrale, Stati Uniti centrali, Asia orientale, Stati Uniti orientali 2, Stati Uniti centro-settentrionali, Europa settentrionale, Stati Uniti centro-meridionali, Asia sudorientale, India meridionale, Regno Unito meridionale, Stati Uniti occidentali 2 | Windows | 2 | 3,5 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>Disponibilità - Distribuzioni di Windows Server 2019 LTSC, 1809 (anteprima)

Per i gruppi di contenitori con contenitori basati su Windows Server 2019 (anteprima) sono disponibili le aree geografiche e le risorse seguenti.

| Aree | Sistema operativo | Max CPU (CPU max) | Memoria massima (GB) |
| -------- | -- | :---: | :-----------: |
| Australia orientale, Brasile meridionale, Canada centrale, India centrale, Stati Uniti centrali, Asia orientale, Stati Uniti orientali, Giappone orientale, Stati Uniti centro-settentrionali, Europa settentrionale, Stati Uniti centro-meridionali, Asia sudorientale, India meridionale, Regno Unito meridionale, Europa occidentale | Windows | 4 | 16 |
| Stati Uniti orientali 2, Stati Uniti occidentali 2 | Windows | 2 | 3,5 |


## <a name="availability---virtual-network-deployment"></a>Disponibilità-distribuzione rete virtuale

Le aree e le risorse seguenti sono disponibili per un gruppo di contenitori distribuito in una [rete virtuale di Azure](container-instances-vnet.md).

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Disponibilità - Risorse della GPU (anteprima)

Per un gruppo di contenitori distribuito con [risorse della GPU](container-instances-gpu.md) (anteprima) sono disponibili le aree geografiche e le risorse seguenti.

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>Passaggi successivi

Segnalare al team se si desidera vedere altre aree geografiche o una maggiore disponibilità delle risorse all'indirizzo [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Per informazioni sulla risoluzione dei problemi di distribuzione di Istanze di Container, vedere [Risolvere i problemi di distribuzione di Istanze di Azure Container](container-instances-troubleshooting.md).
