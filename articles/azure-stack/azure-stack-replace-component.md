---
title: "Sostituire un componente hardware in un nodo di unità di scala di Azure Stack | Documenti Microsoft"
description: Informazioni su come sostituire un componente hardware in un sistema integrato dello Stack di Azure.
services: azure-stack
documentationcenter: 
author: troettinger
manager: byronr
editor: 
ms.assetid: c6e036bf-8c80-48b5-b2d2-aa7390c1b7c9
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: twooley
ms.openlocfilehash: 430274025bc6cd657a733fb80feb6cd7b39a36f3
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2017
---
# <a name="replace-a-hardware-component-on-an-azure-stack-scale-unit-node"></a>Sostituire un componente hardware in un nodo di unità di scala di Azure Stack

*Si applica a: Azure Stack integrate di sistemi*

In questo articolo viene descritto il processo generale per sostituire i componenti hardware non collegabile a caldo. Sostituzione effettiva passaggi variano in base al fornitore dell'hardware (OEM) original equipment manufacturer. Vedere la documentazione del fornitore campo unità sostituibili (FRU) per i passaggi dettagliati che sono specifiche per il sistema integrato dello Stack di Azure.

Di seguito sono indicati i componenti non collegabile a caldo:

- CPU *
- Memoria *
- (BMC) della scheda madre/baseboard management controller / video card
- Disco controller/scheda bus host (HBA) / backplane
- Scheda di rete (NIC)
- Il disco del sistema operativo *
- Unità di dati (unità che non supportano swap a caldo, ad esempio schede PCI-e-in) *

* Questi componenti possono supportare swap a caldo, ma possono variare in base all'implementazione del fornitore. Vedere la documentazione di FRU del fornitore OEM per la procedura dettagliata.

Il diagramma di flusso seguente viene illustrato il processo generale di FRU per sostituire un componente hardware non collegabile a caldo.

![Diagramma di flusso che mostra un componente flusso di sostituzione](media/azure-stack-replace-component/ReplaceComponentFlow.PNG)

* Questa operazione potrebbe non essere necessaria in base alla condizione dell'hardware fisica.

* * Se il fornitore dell'hardware OEM esegue la sostituzione dei componenti e aggiornamento del firmware potrebbe variare in base supportate nel contratto.

## <a name="review-alert-information"></a>Rivedere le informazioni di avviso

L'integrità di Azure Stack e un sistema di monitoraggio controlla lo stato delle schede di rete e delle unità di dati che sono controllate da spazi di archiviazione diretta. Altri componenti hardware non effettua il monitoraggio. Per tutti gli altri componenti hardware, vengono generati avvisi nell'hardware specifici del fornitore di soluzione che viene eseguito nell'host hardware del ciclo di vita di monitoraggio.

## <a name="component-replacement-process"></a>Processo di sostituzione del componente

I passaggi seguenti vengono forniti come una panoramica del processo di sostituzione di componenti. Non eseguire la procedura senza fare riferimento alla documentazione fornita dall'OEM FRU.

1. Utilizzare il [svuotare](azure-stack-node-actions.md#scale-unit-node-actions) azione per inserire il nodo di unità di scala in modalità di manutenzione. Questa azione potrebbe non essere necessaria in base alla condizione dell'hardware fisica.
2. Dopo il nodo di unità di scala è in modalità manutenzione, utilizzare il [spegnere](azure-stack-node-actions.md#scale-unit-node-actions) azione. Questa azione potrebbe non essere necessaria in base alla condizione dell'hardware fisica.
 
   > [!NOTE]
   > Nel caso improbabile in cui spegnimento azione non funziona, utilizzare l'interfaccia web di baseboard management controller (BMC).

3. Sostituire il componente hardware danneggiato. Se il fornitore dell'hardware OEM esegue la sostituzione dei componenti potrebbe variare in base al contratto di supporto.  
4. Aggiornare il firmware. Seguire il processo di aggiornamento del firmware specifici del fornitore tramite l'host di ciclo di vita dell'hardware per assicurarsi che il componente hardware sostituito sia il firmware approvato livello applicato. Se il fornitore dell'hardware OEM esegue questo passaggio potrebbe variare in base al contratto di supporto.  
5. Utilizzare il [ripristino](azure-stack-node-actions.md#scale-unit-node-actions) azione per riportare il nodo di unità di scala nell'unità di scala.
6. Utilizzare l'endpoint con privilegi per [controllare lo stato di ripristino disco virtuale](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair). Con le nuove unità di dati, un processo di ripristino completo di archiviazione può richiedere più ore in base al carico di sistema e utilizzato lo spazio.
7. Al termine, l'azione di ripristino, verificare che tutti gli avvisi attivi sono stati chiusi automaticamente.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla sostituzione di un disco fisico a caldo, vedere [sostituire un disco](azure-stack-replace-disk.md).
- Per informazioni sulla sostituzione di un nodo fisico, vedere [sostituire un nodo di unità di scala](azure-stack-replace-node.md). 