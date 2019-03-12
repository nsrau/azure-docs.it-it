---
title: Sostituire un nodo di unità di scala su un sistema integrato Azure Stack | Microsoft Docs
description: Informazioni su come sostituire un nodo di unità di scala fisico su un sistema integrato Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.lastreviewed: 12/06/2018
ms.openlocfilehash: 1024661fabfd97ce5f058d071de514fa7d02e527
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57782310"
---
# <a name="replace-a-scale-unit-node-on-an-azure-stack-integrated-system"></a>Sostituire un nodo di unità di scala su un sistema integrato Azure Stack

*Si applica a: Sistemi integrati di Azure Stack*

Questo articolo descrive il processo generale per sostituire un computer fisico (detto anche un nodo di unità di scala) in un sistema integrato Azure Stack. Sostituzione di nodo unità di scala effettiva passaggi variano in base al fornitore dell'hardware OEM (OEM). Vedere la documentazione del fornitore campo unità sostituibile sul (FRU) per informazioni dettagliate specifiche per il sistema.

Il diagramma di flusso seguente viene illustrato il processo FRU generale per sostituire un nodo di unità di scala intera.

![Diagramma di flusso per processo di sostituzione del nodo](media/azure-stack-replace-node/replacenodeflow.png)

* Questa azione potrebbe non essere necessaria in base alla condizione dell'hardware fisica.

> [!Note]  
> Se l'operazione di arresto ha esito negativo, è consigliabile utilizzare l'operazione di svuotamento seguita dall'operazione di arresto. Per altre informazioni, vedere le operazioni del nodo disponibili  

## <a name="review-alert-information"></a>Esaminare le informazioni sugli avvisi

Se un nodo di unità di scala è inattivo, si riceverà gli avvisi critici seguenti:

- Nodo non è connesso al controller di rete
- Nodo inaccessibile per la selezione host per macchina virtuale
- Il nodo di unità di scala è offline

![Elenco degli avvisi per le unità di scala verso il basso](media/azure-stack-replace-node/nodedownalerts.png)

Se si apre la **nodo di unità di scala non è in linea** avviso, la descrizione dell'avviso contiene il nodo di unità di scala che non è accessibile. È anche possibile ricevere avvisi aggiuntivi nella soluzione di monitoraggio specifiche OEM che è in esecuzione nell'host del ciclo di vita dell'hardware.

![Dettagli dell'avviso non in linea di nodo](media/azure-stack-replace-node/nodeoffline.png)

## <a name="scale-unit-node-replacement-process"></a>Processo di sostituzione nodo unità di scala

I passaggi seguenti vengono forniti come una panoramica generale del processo di sostituzione nodo unità di scala. Vedere documentazione FRU OEM hardware del proprio fornitore per informazioni dettagliate specifiche per il sistema. Seguire questi passaggi senza fare riferimento alla documentazione fornita dall'OEM.

1. Usare la **arresto** azione di arresto normale del nodo di unità di scala. Questa azione potrebbe non essere necessaria in base alla condizione dell'hardware fisica. 

2. L'improbabile caso l'azione di arresto ha esito negativo, usare il [svuotare](azure-stack-node-actions.md#drain) azione per inserire il nodo di unità di scala in modalità di manutenzione. Questa azione potrebbe non essere necessaria in base alla condizione dell'hardware fisica.

   > [!NOTE]  
   > In ogni caso, solo un nodo può essere disabilitato e spenta allo stesso tempo senza causare l'interruzione di S2D (spazi di archiviazione diretta).

3. Dopo il nodo di unità di scala è in modalità di manutenzione, usare il [arrestare](azure-stack-node-actions.md#stop) azione. Questa azione potrebbe non essere necessaria in base alla condizione dell'hardware fisica.

   > [!NOTE]  
   > Nel caso improbabile che spegnimento azione non funziona, usare invece l'interfaccia web di baseboard management controller (BMC).

4. Sostituire il computer fisico. In genere, questa operazione viene eseguita dal fornitore dell'hardware OEM.
5. Usare la [Repair](azure-stack-node-actions.md#repair) azione per l'aggiunta del nuovo computer fisico per l'unità di scala.
6. Usare l'endpoint con privilegi per [controllare lo stato di ripristino disco virtuale](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair). Con le nuove unità dati, un processo di riparazione archiviazione completa può richiedere più ore a seconda del carico del sistema e spazio usato.
7. Al termine l'azione di ripristino, verificare che tutti gli avvisi attivi sono stati chiusi automaticamente.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla sostituzione di un disco fisico mentre il sistema viene acceso, vedere [sostituisce un disco](azure-stack-replace-disk.md). 
- Per informazioni sulla sostituzione di componenti hardware che richiede il sistema verrà spenta, vedere [sostituire un componente hardware](azure-stack-replace-component.md).
