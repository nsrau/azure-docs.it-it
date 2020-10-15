---
title: Visualizzare lo stato di integrità di Azure NetApp Files relazione di replica | Microsoft Docs
description: Viene descritto come visualizzare lo stato di replica nel volume di origine o nel volume di destinazione di Azure NetApp Files.
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
ms.topic: how-to
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: 95c1202fb56e882554d40926e9d5ecec7be49086
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708809"
---
# <a name="display-health-status-of-replication-relationship"></a>Visualizzare lo stato integrità della relazione di replica 

È possibile visualizzare lo stato della replica nel volume di origine o nel volume di destinazione.  

## <a name="display-replication-status"></a>Visualizza lo stato della replica

1. Dal volume di origine o dal volume di destinazione fare clic su **replica** in servizio di archiviazione per ogni volume.

    Vengono visualizzati lo stato di replica e le informazioni di integrità seguenti:  
    * **Tipo** di endpoint: indica se il volume è l'origine o la destinazione della replica.
    * **Stato: Visualizza** lo stato di integrità della relazione di replica.
    * **Stato mirror** : Mostra uno dei valori seguenti:
        * Non *inizializzata*:  
            Si tratta dello stato iniziale e predefinito quando viene creata una relazione di peering. Lo stato rimane non inizializzato fino a quando l'inizializzazione non viene completata correttamente.
        * Con *mirroring*:   
            Il volume di destinazione è stato inizializzato ed è pronto per la ricezione degli aggiornamenti del mirroring.
        * *Interruppe*:   
            Si tratta dello stato dopo aver violato la relazione di peering. Il volume di destinazione è `‘RW’` e sono presenti snapshot.
    * **Stato relazione** : Mostra uno dei valori seguenti: 
        * *Inattività*:  
            Non sono in corso operazioni di trasferimento e i trasferimenti futuri non sono disabilitati.
        * *Trasferimento*in corso:  
            È in corso un'operazione di trasferimento e i trasferimenti futuri non sono disabilitati.
    * **Pianificazione della replica** : Mostra la frequenza con cui vengono eseguiti gli aggiornamenti di mirroring incrementali quando viene completata l'inizializzazione (copia di base).

    * **Stato totale** : Mostra la quantità totale di dati in byte trasferiti per l'operazione di trasferimento corrente. Questa quantità rappresenta i bit effettivi trasferiti e potrebbe essere diversa dallo spazio logico che i volumi di origine e di destinazione segnalano.  

    ![Stato di integrità della replica](../media/azure-netapp-files/cross-region-replication-health-status.png)

> [!NOTE] 
> La relazione di replica Mostra lo stato di integrità come non *integro* se i processi di replica precedenti non sono completi. Questo stato è il risultato di volumi di grandi dimensioni trasferiti con una finestra di trasferimento inferiore (ad esempio, un tempo di trasferimento di dieci minuti per un volume elevato). In questo caso, lo stato della relazione indica che lo stato del *trasferimento* e dello stato di integrità risulta non *integro*.

## <a name="next-steps"></a>Passaggi successivi  

* [Replica tra più aree](cross-region-replication-introduction.md)
* [Gestire il ripristino di emergenza](cross-region-replication-manage-disaster-recovery.md)
* [Metriche di replica del volume](azure-netapp-files-metrics.md#replication)
* [Risolvere i problemi relativi alla replica tra più aree](troubleshoot-cross-region-replication.md)

