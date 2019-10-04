---
title: Come arrestare un'unità di Microsoft Azure FXT Edge filtro
description: Procedure per l'avvio e chiusura di un nodo di Azure FXT Edge filtro
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: v-erkell
ms.openlocfilehash: 6921e7a52e43a63055b59242c02cc6ca3b8c5313
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620091"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>Come in modo sicuro spegnere hardware Azure FXT Edge filtro

Sebbene sia possibile usare il pulsante di alimentazione per passare su un singolo nodo, si dovrebbe non usarlo per arrestare l'unità in circostanze normali.

Dopo che un nodo filtro Edge FXT di Azure è in uso come parte di un cluster, utilizzare il software di Pannello di controllo del cluster per arrestare l'hardware. 

> [!NOTE] 
> Per evitare la perdita di dati possibili, utilizzare sempre il software del Pannello di controllo per arrestare un filtro di Edge FXT di Azure. Non utilizzare il pulsante di alimentazione fisica per l'arresto a meno che non viene richiesto di farlo dal supporto tecnico Microsoft.
> 
> In caso di emergenza elettrico, scollegare i cavi di alimentazione o usare i dati dell'energia elettrica del centro disconnettere meccanismo.

## <a name="shut-down-a-node-from-the-control-panel"></a>Arrestare un nodo dal Pannello di controllo

Seguire queste istruzioni per in modo sicuro spegnere un nodo di Azure FXT Edge filtro:

1. Accedere al pannello di controllo del cluster. (Istruzioni disponibili nel [aprire le pagine di impostazioni](fxt-cluster-create.md#open-the-settings-pages))
1. Fare clic sui **le impostazioni** tab, quindi caricare il **Cluster** > **FXT nodi** pagina.
1. Nell'elenco dei nodi del cluster, trovare quella che si desidera arrestare. Fare clic sui **spegnere** sul pulsante relativo **azioni** colonna. 
1. Attendere alcuni istanti. Il nodo verrà arrestata e spegnere stesso.

## <a name="next-steps"></a>Passaggi successivi

* Scopri i LED di stato e altri indicatori nel [lo stato dell'hardware monitoraggio Azure FXT Edge Filer](fxt-monitor.md).
* Altre informazioni su power Azure FXT Edge Filer fornite nel [collegare i cavi di alimentazione](fxt-network-power.md#connect-power-cables).
