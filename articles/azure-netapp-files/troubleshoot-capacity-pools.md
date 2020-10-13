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
ms.openlocfilehash: e7e3c2eb058d3549f2e1a10ffacf01fd354fbd47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91651055"
---
# <a name="troubleshoot-capacity-pool-issues"></a>Risolvere i problemi del pool di capacità

Questo articolo descrive le soluzioni ai problemi che potrebbero verificarsi durante la gestione dei pool di capacità. 

## <a name="error-conditions-and-resolutions"></a>Condizioni di errore e risoluzioni 

|     Condizione di errore    |     Soluzione    |
|-|-|
| Problemi di creazione di un pool di capacità |  Verificare che il numero di pool di capacità non superi il limite. Vedere [limiti delle risorse per Azure NetApp files](azure-netapp-files-resource-limits.md).  Se il conteggio è inferiore al limite e si verificano ancora problemi, archiviare un ticket di supporto e specificare il nome del pool di capacità. |
| Problemi di eliminazione di un pool di capacità  |  Assicurarsi di rimuovere tutti Azure NetApp Files volumi e snapshot nella sottoscrizione in cui si sta provando a eliminare il pool di capacità. <br> Se sono già stati rimossi tutti i volumi e gli snapshot e non è ancora possibile eliminare il pool di capacità, i riferimenti alle risorse potrebbero ancora esistere senza essere visualizzati nel portale. In questo caso, archiviare un ticket di supporto e specificare di aver eseguito i passaggi indicati in precedenza. |
| Creazione o modifica del volume non riuscita con `Requested throughput not available` errore | La velocità effettiva disponibile per un volume è determinata dalle dimensioni del pool di capacità e dal livello di servizio. Se la velocità effettiva non è sufficiente, è necessario aumentare le dimensioni del pool o regolare la velocità effettiva del volume esistente. | 

## <a name="next-steps"></a>Passaggi successivi  

* [Configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md)
* [Gestire un pool di capacità QoS manuale](manage-manual-qos-capacity-pool.md)
