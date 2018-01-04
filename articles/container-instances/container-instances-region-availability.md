---
title: "Disponibilità di risorse e aree di Istanze di contenitore di Azure"
description: Informazioni sulle aree di Azure che supportano la distribuzione di Istanze di contenitore e sui limiti di CPU e memoria per tali istanze.
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: overview
ms.date: 12/15/2017
ms.author: marsma
ms.openlocfilehash: ec7f469c47924f4ae22d6509996ca9cf498fc9ad
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2017
---
# <a name="region-availability-for-azure-container-instances"></a>Disponibilità a livello di area per Istanze di contenitore di Azure

Durante la fase di anteprima, il servizio Istanze di contenitore di Azure è disponibile nelle aree seguenti con i limiti di CPU e memoria specificati.

| Località | OS | CPU | Memoria (GB) |
| -------- | -- | :---: | :-----------: |
| Europa occidentale, Stati Uniti occidentali, Stati Uniti orientali | Linux | 4 | 14 |
| Europa occidentale, Stati Uniti occidentali, Stati Uniti orientali | Windows | 4 | 14 |

## <a name="resource-availability"></a>Disponibilità delle risorse

La disponibilità di Istanze di contenitore con questi limiti di risorse dipende dell'area di distribuzione. Quando un'area ha un carico elevato, può verificarsi un errore durante la distribuzione delle istanze.

Per mitigare tale errore di distribuzione, provare a distribuire le istanze con impostazioni di memoria e CPU inferiori oppure provare a eseguire la distribuzione in un secondo momento.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla risoluzione dei problemi di distribuzione di Istanze di contenitore, vedere [Risolvere i problemi di distribuzione di Istanze di contenitore di Azure](container-instances-troubleshooting.md).