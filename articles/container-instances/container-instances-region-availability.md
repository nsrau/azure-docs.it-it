---
title: "Disponibilità di risorse e aree di Istanze di contenitore di Azure | Azure Docs"
description: Informazioni sulle aree di Azure che supportano la distribuzione di Istanze di contenitore e sui limiti di CPU e memoria per tali istanze.
services: container-instances
documentationcenter: 
author: mmacy
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: marsma
ms.custom: 
ms.openlocfilehash: 2b9b1b864bbfd73383759212dd7d91f8e4941544
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="region-availability-for-azure-container-instances"></a>Disponibilità a livello di area per Istanze di contenitore di Azure

Durante la fase di anteprima, il servizio Istanze di contenitore di Azure è disponibile nelle aree seguenti con i limiti di CPU e memoria specificati.

| Percorso | OS | CPU | Memoria (GB) |
| -------- | -- | :---: | :-----------: |
| Europa occidentale, Stati Uniti occidentali, Stati Uniti orientali | Linux | 2 | 7 |
| Europa occidentale, Stati Uniti occidentali, Stati Uniti orientali | Windows | 2 | 3,5 |

## <a name="resource-availability"></a>Disponibilità delle risorse

La disponibilità di Istanze di contenitore con questi limiti di risorse dipende dell'area di distribuzione. Quando un'area ha un carico elevato, può verificarsi un errore durante la distribuzione delle istanze.

Per mitigare tale errore di distribuzione, provare a distribuire le istanze con impostazioni di memoria e CPU inferiori oppure provare a eseguire la distribuzione in un secondo momento.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla risoluzione dei problemi di distribuzione di Istanze di contenitore, vedere [Risolvere i problemi di distribuzione di Istanze di contenitore di Azure](container-instances-troubleshooting.md).