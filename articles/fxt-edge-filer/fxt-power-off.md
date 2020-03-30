---
title: Come arrestare un'unità Filer di Microsoft Azure FXT Edge
description: Procedure per l'avvio e l'arresto sicuro di un nodo Filer di Azure FXT Edge
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 91445c4219abaa67385105f52515f52c87e2cf06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72255995"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>Come spegnere in modo sicuro l'hardware di Azure FXT Edge Filer

Sebbene sia possibile utilizzare il pulsante di alimentazione fisica per attivare un singolo nodo, non è consigliabile utilizzarlo per arrestare l'unità in circostanze normali.

Dopo che un nodo Filer di Azure FXT Edge è in uso come parte di un cluster, è necessario usare il software del pannello di controllo del cluster per arrestare l'hardware. 

> [!NOTE] 
> To avoid possible data loss or corruption, always use the Control Panel software to shut down an Azure FXT Edge Filer. Non utilizzare il pulsante di alimentazione fisica per l'arresto a meno che non venga richiesto dal Servizio Supporto Tecnico Clienti Microsoft.
> 
> In caso di emergenza elettrica, scollegare i cavi di alimentazione o utilizzare il meccanismo di disconnessione dell'elettricità del data center.

## <a name="shut-down-a-node-from-the-control-panel"></a>Arrestare un nodo dal Pannello di controllo

Seguire queste istruzioni per spegnere in modo sicuro un nodo Filer di Azure FXT Edge:Follow these instructions to safely power off an Azure FXT Edge Filer node:

1. Accedere al Pannello di controllo del cluster. (Indicazioni in [Aprire le pagine Impostazioni](fxt-cluster-create.md#open-the-settings-pages))
1. Fare clic sulla scheda **Impostazioni,** quindi caricare la pagina**Nodi FXT** **cluster.** > 
1. Nell'elenco dei nodi del cluster individuare quello che si desidera arrestare. Fare clic sul pulsante **Spegne** nella relativa colonna **Azioni.** 
1. Aspetta qualche istante. Il nodo si spegnerà e si spegnerà.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sui LED di stato e altri indicatori, vedere Monitorare lo stato hardware di [Azure FXT Edge Filer](fxt-monitor.md).
* Per altre informazioni sugli alimentatori di Azure FXT Edge Filer, vedere Collegare i cavi di [alimentazione.](fxt-network-power.md#connect-power-cables)
