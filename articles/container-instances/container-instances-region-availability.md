---
title: Disponibilità delle risorse per area
description: Disponibilità delle risorse di calcolo e memoria per il servizio Istanze di Azure Container in aree di Azure differenti.
ms.topic: article
ms.date: 04/27/2020
ms.custom: references_regions
ms.openlocfilehash: 2a833c93c80d932305f47b6e292e5e5df4d0cb95
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94576615"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Disponibilità di risorse per Istanze di Azure Container nelle aree di Azure

Questo articolo illustra in dettaglio la disponibilità delle risorse di calcolo, memoria e archiviazione delle istanze di contenitore di Azure nelle aree di Azure e in base al sistema operativo di destinazione. Per un elenco generale delle aree disponibili per le istanze di contenitore di Azure, vedere [aree disponibili](https://azure.microsoft.com/regions/services/).

I valori presentati sono le risorse massime disponibili per distribuzione di un [gruppo di contenitori](container-instances-container-groups.md). I valori sono aggiornati al momento della pubblicazione.

> [!NOTE]
> I gruppi di contenitori con questi limiti di risorse dipendono dell'area di distribuzione. Quando un'area ha un carico elevato, può verificarsi un errore durante la distribuzione delle istanze. Per attenuare un errore di distribuzione di questo tipo, provare a distribuire le istanze con impostazioni di risorse inferiori oppure provare la distribuzione in un secondo momento o in un'area diversa con le risorse disponibili.

Per informazioni su quote e altri limiti nelle distribuzioni, vedere [Quote e limiti per Istanze di Azure Container](container-instances-quotas.md).

## <a name="linux-container-groups"></a>Gruppi di contenitori Linux

Le aree e le risorse massime seguenti sono disponibili per i gruppi di contenitori con contenitori Linux in distribuzioni generali, distribuzioni di [rete virtuale di Azure](container-instances-vnet.md) e distribuzioni con [risorse GPU](container-instances-gpu.md) (anteprima).

> [!IMPORTANT]
> Le risorse massime in un'area sono diverse a seconda della distribuzione. Ad esempio, in una distribuzione di rete virtuale di Azure è possibile che un'area disponga di una quantità di CPU e di memoria massima diversa rispetto a una distribuzione generale. La stessa area può avere anche un set diverso di valori massimi per una distribuzione con risorse GPU. Verificare il tipo di distribuzione prima di controllare le tabelle seguenti per i valori massimi nella propria area.

| Area | Max CPU (CPU max) | Memoria massima (GB) | VNET max CPU | Memoria massima VNET (GB) | Archiviazione (GB) | SKU GPU (anteprima) |
| -------- | :---: | :---: | :----: | :-----: | :-------: | :----: |
| Australia orientale | 4 | 16 | 4 | 16 | 50 | N/D |
| Brasile meridionale | 4 | 16 | 2 | 8 | 50 | N/D |
| Canada centrale | 4 | 16 | 4 | 16 | 50 | N/D |
| India centrale | 4 | 16 | N/D | N/D | 50 | V100 |
| Stati Uniti centrali | 4 | 16 | 4 | 16 | 50 | N/D |
| Asia orientale | 4 | 16 | 4 | 16 | 50 | N/D |
| Stati Uniti orientali | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |
| Stati Uniti orientali 2 | 4 | 16 | 4 | 16 | 50 | N/D |
| Francia centrale | 4 | 16 | 4 | 16 | 50 | N/D |
| Giappone orientale | 2 | 8 | 4 | 16 | 50 | N/D |
| Corea centrale | 4 | 16 | N/D | N/D | 50 | N/D |
| Stati Uniti centro-settentrionali | 2 | 3,5 | 4 | 16 | 50 | K80, P100, V100 |
| Europa settentrionale | 4 | 16 | 4 | 16 | 50 | K80 |
| Stati Uniti centro-meridionali | 4 | 16 | 4 | 16 | 50 | N/D |
| Asia sud-orientale | 4 | 16 | 4 | 16 | 50 | P100, V100 |
| India meridionale | 4 | 16 | N/D | N/D | 50 | N/D |
| Regno Unito meridionale | 4 | 16 | 4 | 16 | 50 | N/D |
| Stati Uniti centro-occidentali| 4 | 16 | 4 | 16 | 50 | N/D |
| Europa occidentale | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |
| Stati Uniti occidentali | 4 | 16 | 4 | 16 | 50 | N/D |
| West US 2 | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |

Le risorse massime seguenti sono disponibili per un gruppo di contenitori distribuito con [risorse GPU](container-instances-gpu.md) (anteprima).

> [!IMPORTANT]
> Al momento, le distribuzioni con risorse GPU non sono supportate in una distribuzione di rete virtuale di Azure e sono disponibili solo nei gruppi di contenitori Linux.

| SKU GPU | Conteggio GPU | Max CPU (CPU max) | Memoria massima (GB) | Archiviazione (GB) |
| --- | --- | --- | --- | --- |
| K80 | 1 | 6 | 56 | 50 |
| K80 | 2 | 12 | 112 | 50 |
| K80 | 4 | 24 | 224 | 50 |
| P100, V100 | 1 | 6 | 112 | 50 |
| P100, V100 | 2 | 12 | 224 | 50 |
| P100, V100 | 4 | 24 | 448 | 50 |

## <a name="windows-container-groups"></a>Gruppi di contenitori di Windows

Le aree e le risorse massime seguenti sono disponibili per i gruppi di contenitori con i contenitori di Windows Server [supportati e di anteprima](container-instances-faq.md#what-windows-base-os-images-are-supported) .

> [!IMPORTANT]
> Attualmente, le distribuzioni con gruppi di contenitori di Windows non sono supportate in una distribuzione di rete virtuale di Azure.

###  <a name="windows-server-2016"></a>Windows Server 2016

> [!NOTE]
> Per altre informazioni sugli host 1B, 2B e 3B, vedere [compatibilità delle versioni di host e contenitori](/virtualization/windowscontainers/deploy-containers/update-containers#host-and-container-version-compatibility) .

| Area | CPU max 1B/2B | Memoria massima 1B/2B (GB) |Massimo 3B CPU | Memoria massima 3B (GB) | Archiviazione (GB) |
| -------- | :---: | :---: | :----: | :-----: | :-------: |
| Australia orientale | 2 | 8 | 2 | 3,5 | 20 |
| Brasile meridionale | 4 | 16 | 4 | 16 | 20 |
| Canada centrale | 2 | 3,5 | 2 | 3,5 | 20 |
| India centrale | 2 | 3,5 | 2 | 3,5 | 20 |
| Stati Uniti centrali | 2 | 3,5 | 2 | 3,5 | 20 |
| Asia orientale | 2 | 3,5 | 2 | 3,5 | 20 |
| Stati Uniti orientali | 4 | 16 | 2 | 8 | 20 |
| Stati Uniti orientali 2 | 2 | 3,5 | 4 | 16 | 20 |
| Giappone orientale | 4 | 16 | 4 | 16 | 20 |
| Corea centrale | 4 | 16 | 4 | 16 | 20 |
| Stati Uniti centro-settentrionali | 4 | 16 | 4 | 16 | 20 |
| Europa settentrionale | 2 | 3,5 | 2 | 8 | 20 |
| Stati Uniti centro-meridionali | 2 | 3,5 | 2 | 3,5 | 20 |
| Asia sud-orientale | N/D | N/D | 2 | 3,5 | 20 |
| India meridionale | 2 | 3,5 | 2 | 3,5 | 20 |
| Regno Unito meridionale | 2 | 8 | 2 | 3,5 | 20 |
| Stati Uniti centro-occidentali | 4 | 16 | 4 | 16 | 20 |
| Europa occidentale | 4 | 16 | 4 | 16 | 20 |
| Stati Uniti occidentali | 4 | 16 | 2 | 8 | 20 |
| West US 2 | 2 | 3,5 | 2 | 3,5 | 20 |


### <a name="windows-server-2019-ltsc"></a>Windows Server 2019 LTSC

> [!NOTE]
> Per altre informazioni sugli host 1B, 2B e 3B, vedere [compatibilità delle versioni di host e contenitori](/virtualization/windowscontainers/deploy-containers/update-containers#host-and-container-version-compatibility) .

| Area | CPU max 1B/2B | Memoria massima 1B/2B (GB) |Massimo 3B CPU | Memoria massima 3B (GB) | Archiviazione (GB) |
| -------- | :---: | :---: | :----: | :-----: | :-------: |
| Australia orientale | 4 | 16 | 4 | 16 | 20 |
| Brasile meridionale | 4 | 16 | 4 | 16 | 20 |
| Canada centrale | 4 | 16 | 4 | 16 | 20 |
| India centrale | 4 | 16 | 4 | 16 | 20 |
| Stati Uniti centrali | 4 | 16 | 4 | 16 | 20 |
| Asia orientale | 4 | 16 | 4 | 16 | 20 |
| Stati Uniti orientali | 4 | 16 | 4 | 16 | 20 |
| Stati Uniti orientali 2 | 2 | 3,5 | 2 | 3,5 | 20 |
| Francia centrale | 4 | 16 | 4 | 16 | 20 |
| Giappone orientale | N/D | N/D | 4 | 16 | 20 |
| Corea centrale | 4 | 16 | 4 | 16 | 20 |
| Stati Uniti centro-settentrionali | 4 | 16 | 4 | 16 | 20 |
| Europa settentrionale | 4 | 16 | 4 | 16 | 20 |
| Stati Uniti centro-meridionali | 4 | 16 | 4 | 16 | 20 |
| Asia sud-orientale | 4 | 16 | 4 | 16 | 20 |
| India meridionale | 4 | 16 | 4 | 16 | 20 |
| Regno Unito meridionale | 4 | 16 | 4 | 16 | 20 |
| Stati Uniti centro-occidentali | 4 | 16 | 4 | 16 | 20 |
| Europa occidentale | 4 | 16 | 4 | 16 | 20 |
| Stati Uniti occidentali | 4 | 16 | 4 | 16 | 20 |
| West US 2 | 2 | 8 | 4 | 16 | 20 |

## <a name="next-steps"></a>Passaggi successivi

Segnalare al team se si desidera vedere altre aree geografiche o una maggiore disponibilità delle risorse all'indirizzo [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Per informazioni sulla risoluzione dei problemi di distribuzione di Istanze di Container, vedere [Risolvere i problemi di distribuzione di Istanze di Azure Container](container-instances-troubleshooting.md).


[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
