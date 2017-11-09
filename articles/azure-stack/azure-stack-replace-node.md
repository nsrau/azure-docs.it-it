---
title: "Sostituire un nodo di unità di scala su un sistema integrato dello Stack di Azure | Documenti Microsoft"
description: "Informazioni su come sostituire un nodo di unità di scala fisico in un sistema integrato dello Stack di Azure."
services: azure-stack
documentationcenter: 
author: troettinger
manager: byronr
editor: 
ms.assetid: f9434689-ee66-493c-a237-5c81e528e5de
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: twooley
ms.openlocfilehash: 63ba6cedd32bcf6ea3039bc80cc4a3f3407adfa7
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2017
---
# <a name="replace-a-scale-unit-node-on-an-azure-stack-integrated-system"></a>Sostituire un nodo di unità di scala su un sistema integrato dello Stack di Azure

*Si applica a: Azure Stack integrate di sistemi*

In questo articolo viene descritto il processo generale per sostituire un computer fisico (detta anche un *nodo unità di scala*) su uno Stack di Azure sistema integrato. Sostituzione di nodo unità di scala effettiva passaggi variano in base al fornitore dell'hardware (OEM) original equipment manufacturer. Vedere la documentazione del fornitore campo unità sostituibili (FRU) per i passaggi dettagliati che sono specifiche per il sistema.

Il diagramma di flusso seguente illustra il processo FRU generale per sostituire un nodo di unità di scala intero.

![Diagramma di flusso per processo di sostituzione nodo](media/azure-stack-replace-node/ReplaceNodeFlow.PNG)

* Questa operazione potrebbe non essere necessaria in base alla condizione dell'hardware fisica.

## <a name="review-alert-information"></a>Rivedere le informazioni di avviso

Se un nodo di unità di scala è attivo, si riceverà tutti gli avvisi critici seguenti:

- Nodo non è connesso al controller di rete
- Nodo inaccessibile per la selezione host macchina virtuale
- Nodo di unità di scala è offline

![Elenco di avvisi per le unità di scala verso il basso](media/azure-stack-replace-node/NodeDownAlerts.PNG)

Se si apre l'avviso "nodo unità di scala non in linea", la descrizione dell'avviso contiene il nodo di unità di scala che non è accessibile. Altri avvisi viene visualizzato anche nella soluzione di monitoraggio specifico dell'OEM che è in esecuzione nell'host del ciclo di vita dell'hardware.

![Dettagli dell'avviso di nodo non in linea](media/azure-stack-replace-node/NodeOffline.PNG)

## <a name="scale-unit-node-replacement-process"></a>Processo di sostituzione nodo unità di scala

I passaggi seguenti vengono forniti come una panoramica del processo di sostituzione nodo unità di scala. Vedere documentazione FRU del produttore dell'hardware OEM per la procedura dettagliata che sono specifiche per il sistema. Non eseguire la procedura senza fare riferimento alla documentazione fornita dall'OEM.

1. Utilizzare il [svuotare](azure-stack-node-actions.md#scale-unit-node-actions) azione per inserire il nodo di unità di scala in modalità di manutenzione. Questa azione potrebbe non essere necessaria in base alla condizione dell'hardware fisica.
2. Se il nodo è ancora attiva, utilizzare il [spegnere](azure-stack-node-actions.md#scale-unit-node-actions) azione. Questa azione potrebbe non essere necessaria in base alla condizione dell'hardware fisica.
 
   > [!NOTE]
   > Nel caso improbabile in cui spegnimento azione non funziona, utilizzare l'interfaccia web di baseboard management controller (BMC).

1. Sostituire il computer fisico. In genere, questa operazione viene eseguita dal fornitore dell'hardware OEM.
2. Utilizzare il [ripristino](azure-stack-node-actions.md#scale-unit-node-actions) azione per aggiungere il nuovo computer fisico per l'unità di scala.
3. Utilizzare l'endpoint con privilegi per [controllare lo stato di ripristino disco virtuale](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair). Con le nuove unità di dati, un processo di ripristino completo di archiviazione può richiedere più ore in base al carico di sistema e utilizzato lo spazio.
4. Al termine, l'azione di ripristino, verificare che tutti gli avvisi attivi sono stati chiusi automaticamente.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla sostituzione di un disco fisico a caldo, vedere [sostituire un disco](azure-stack-replace-disk.md). 
- Per informazioni sulla sostituzione di un componente hardware non collegabile a caldo, vedere [sostituire un componente hardware](azure-stack-replace-component.md). 