---
title: Risolvere i problemi del pool di capacità per Azure NetApp Files | Microsoft Docs
description: Descrive i potenziali problemi che possono verificarsi durante la gestione dei pool di capacità e fornisce soluzioni per i problemi.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 0b5558501042dd7816202ea05b3a332b23400ff4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91343319"
---
# <a name="troubleshoot-capacity-pool-issues"></a>Risolvere i problemi del pool di capacità

Questo articolo descrive le soluzioni ai problemi che potrebbero verificarsi durante la gestione dei pool di capacità. 

## <a name="issues-creating-a-capacity-pool"></a>Problemi di creazione di un pool di capacità

Verificare che il numero di pool di capacità non superi il limite. Vedere [limiti delle risorse per Azure NetApp files](azure-netapp-files-resource-limits.md).  Se il conteggio è inferiore al limite e si verificano ancora problemi, archiviare un ticket di supporto e specificare il nome del pool di capacità.

## <a name="issues-deleting-a-capacity-pool"></a>Problemi di eliminazione di un pool di capacità

Assicurarsi di aver rimosso tutti i volumi Azure NetApp Files e gli snapshot nella sottoscrizione in cui si sta provando a eliminare il pool di capacità.   

Se sono già stati rimossi tutti i volumi e gli snapshot e non è ancora possibile eliminare il pool di capacità, i riferimenti alle risorse potrebbero ancora esistere senza essere visualizzati nel portale. In questo caso, archiviare un ticket di supporto e specificare di aver eseguito i passaggi indicati in precedenza. 

## <a name="volume-creation-or-modification-fails-with-requested-throughput-not-available-error"></a>La creazione o la modifica del volume non riesce con l'errore "velocità effettiva richiesta non disponibile"

La velocità effettiva disponibile per un volume è determinata dalle dimensioni del pool di capacità e dal livello di servizio. Se la velocità effettiva non è sufficiente, è necessario aumentare le dimensioni del pool o regolare la velocità effettiva del volume esistente.


## <a name="next-steps"></a>Passaggi successivi  

* [Configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md)
* [Gestire un pool di capacità QoS manuale](manage-manual-qos-capacity-pool.md)
