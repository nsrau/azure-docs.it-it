---
title: Come arrestare un'unità di file di Microsoft Azure FXT Edge
description: Procedure per l'avvio e l'arresto sicuro di un nodo del filer di Azure FXT Edge
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 91445c4219abaa67385105f52515f52c87e2cf06
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255995"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>Come spegnere in modo sicuro l'hardware del filer di Azure FXT Edge

Sebbene sia possibile usare il pulsante di alimentazione fisico per attivare un singolo nodo, è consigliabile non usarlo per arrestare l'unità in circostanze normali.

Dopo l'uso di un nodo del filer di Azure FXT Edge come parte di un cluster, è necessario usare il software del pannello di controllo del cluster per arrestare l'hardware. 

> [!NOTE] 
> Per evitare possibili perdite di dati o danneggiamenti, usare sempre il software del pannello di controllo per arrestare un filer di Azure FXT Edge. Non usare il pulsante di alimentazione fisico per l'arresto, a meno che non venga richiesto dal servizio supporto tecnico clienti Microsoft.
> 
> In caso di emergenza elettrica, scollegare i cavi di alimentazione o usare il meccanismo di disconnessione elettrica del data center.

## <a name="shut-down-a-node-from-the-control-panel"></a>Arrestare un nodo dal pannello di controllo

Seguire queste istruzioni per spegnere in modo sicuro un nodo del filer di Azure FXT Edge:

1. Accedere al pannello di controllo del cluster. (Direzioni in [aprire le pagine Impostazioni](fxt-cluster-create.md#open-the-settings-pages))
1. Fare clic sulla scheda **Impostazioni** , quindi caricare la pagina **cluster** > **FXT nodes** .
1. Nell'elenco dei nodi del cluster individuare quello che si desidera arrestare. Fare clic sul pulsante **Spegni** nella colonna **azioni** . 
1. Attendere alcuni istanti. Il nodo si arresterà e si spegnerà.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sui LED di stato e sugli altri indicatori, vedere [monitorare lo stato hardware del filer di Azure FXT Edge](fxt-monitor.md).
* Per altre informazioni sugli alimentatori di Azure FXT Edge, vedere [connettere cavi di alimentazione](fxt-network-power.md#connect-power-cables).
