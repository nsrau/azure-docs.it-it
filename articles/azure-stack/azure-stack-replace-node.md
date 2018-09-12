---
title: Sostituire un nodo di unità di scala su un sistema integrato Azure Stack | Microsoft Docs
description: Informazioni su come sostituire un nodo di unità di scala fisico su un sistema integrato Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: f9434689-ee66-493c-a237-5c81e528e5de
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2018
ms.author: mabrigg
ms.openlocfilehash: 1b37b150dad4951a4ade81f226b515ce9cae9053
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2018
ms.locfileid: "44377055"
---
# <a name="replace-a-scale-unit-node-on-an-azure-stack-integrated-system"></a>Sostituire un nodo di unità di scala su un sistema integrato Azure Stack

*Si applica a: i sistemi integrati di Azure Stack*

Questo articolo descrive il processo generale per sostituire un computer fisico (noto anche come un *nodo di unità di scala*) in Azure Stack integrato system. Sostituzione di nodo unità di scala effettiva passaggi variano in base al fornitore dell'hardware OEM (OEM). Vedere la documentazione del fornitore campo unità sostituibile sul (FRU) per informazioni dettagliate specifiche per il sistema.

Il diagramma di flusso seguente viene illustrato il processo FRU generale per sostituire un nodo di unità di scala intera.

![Diagramma di flusso per processo di sostituzione del nodo](media/azure-stack-replace-node/replacenodeflow.png)

* Questa azione potrebbe non essere necessaria in base alla condizione dell'hardware fisica.

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

1. Usare la [svuotare](azure-stack-node-actions.md#scale-unit-node-actions) azione per inserire il nodo di unità di scala in modalità di manutenzione. Questa azione potrebbe non essere necessaria in base alla condizione dell'hardware fisica.

   > [!NOTE]
   > In ogni caso, un solo nodo possa essere svuotato e spenta allo stesso tempo senza causare l'interruzione di S2D (spazi di archiviazione diretta).

2. Se il nodo sia ancora acceso, usare il [spegnere](azure-stack-node-actions.md#scale-unit-node-actions) azione. Questa azione potrebbe non essere necessaria in base alla condizione dell'hardware fisica.
 
   > [!NOTE]
   > Nel caso improbabile che spegnimento azione non funziona, usare invece l'interfaccia web di baseboard management controller (BMC).

1. Sostituire il computer fisico. In genere, questa operazione viene eseguita dal fornitore dell'hardware OEM.
2. Usare la [Repair](azure-stack-node-actions.md#scale-unit-node-actions) azione per l'aggiunta del nuovo computer fisico per l'unità di scala.
3. Usare l'endpoint con privilegi per [controllare lo stato di ripristino disco virtuale](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair). Con le nuove unità dati, un processo di riparazione archiviazione completa può richiedere più ore a seconda del carico del sistema e spazio usato.
4. Al termine l'azione di ripristino, verificare che tutti gli avvisi attivi sono stati chiusi automaticamente.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla sostituzione a caldo disco fisico, vedere [sostituisce un disco](azure-stack-replace-disk.md). 
- Per informazioni sulla sostituzione di componenti hardware non a caldo, vedere [sostituire un componente hardware](azure-stack-replace-component.md).
