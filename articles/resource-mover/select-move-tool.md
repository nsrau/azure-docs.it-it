---
title: Scegliere uno strumento per spostare le risorse di Azure tra aree
description: Esaminare le opzioni e gli strumenti per spostare le risorse di Azure tra aree
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: overview
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 1b233028b52175842c73660ff116ac592d8296e0
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90603357"
---
# <a name="choose-a-tool-for-moving-azure-resources"></a>Scegliere uno strumento per spostare le risorse di Azure

È possibile spostare le risorse in Azure come indicato di seguito:

- **Spostare le risorse tra aree**: spostare la risorsa dall'hub di Spostamento risorse o all'interno di un gruppo di risorse. 
- **Spostare le risorse tra gruppi di risorse o sottoscrizioni**: eseguire lo spostamento da un gruppo di risorse. 
- **Spostare le risorse tra cloud di Azure**: usare il servizio Azure Site Recovery per spostare le risorse tra cloud pubblici e governativi.
- **Spostare le risorse tra zone di disponibilità nella stessa area**: usare il servizio Azure Site Recovery per spostare le risorse tra zone di disponibilità nella stessa area di Azure.


## <a name="compare-move-tools"></a>Strumenti di spostamento a confronto

**Strumento** | **Quando usarla** | **Altre informazioni** 
--- | --- | ---
**Spostamento in un gruppo di risorse** | Spostare le risorse in una sottoscrizione o in un gruppo di risorse diverso o da un'area all'altra.<br/><br/> Se si esegue lo spostamento tra aree, nel gruppo di risorse si selezionano le risorse che si intende spostare, quindi si passa all'hub di Spostamento risorse per verificare le dipendenze e spostare le risorse nell'area di destinazione. | [Spostare le risorse in un altro gruppo di risorse o in un'altra sottoscrizione](../azure-resource-manager/management/move-resource-group-and-subscription.md).<br/><br/> [Spostare le risorse in un'altra area da un gruppo di risorse](move-region-within-resource-group.md).
**Spostamento dall'hub di Spostamento risorse** | Spostare le risorse tra aree. <br/><br/> È possibile eseguire lo spostamento in un'area di destinazione o in una specifica zona di disponibilità o in un set di disponibilità all'interno dell'area di destinazione. | [Spostare le risorse tra aree nell'hub di Spostamento risorse]().
**Spostamento delle macchine virtuali con Site Recovery** | Da usare per spostare le macchine virtuali di Azure tra cloud governativi e pubblici.<br/><br/> Da usare se si intende spostare le macchine virtuali tra zone di disponibilità nella stessa area. |[Spostare le risorse tra cloud governativi e pubblici](../site-recovery/region-move-cross-geos.md), [Spostare le risorse nelle zone di disponibilità nella stessa area](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).

## <a name="next-steps"></a>Passaggi successivi

[Leggere le informazioni](about-move-process.md) relative ai componenti di Spostamento risorse e al processo di spostamento.
