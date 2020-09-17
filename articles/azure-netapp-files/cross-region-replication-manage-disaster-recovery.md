---
title: Gestire il ripristino di emergenza con Azure NetApp Files la replica tra aree | Microsoft Docs
description: Viene descritto come gestire il ripristino di emergenza utilizzando Azure NetApp Files replica tra aree.
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
ms.openlocfilehash: ad006279a656758ba856cd3f39c17b0410e525e6
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708789"
---
# <a name="manage-disaster-recovery-using-cross-region-replication"></a>Gestire il ripristino di emergenza con la replica tra aree 

Una replica in corso tra i volumi di origine e di destinazione (vedere [creare il peering di replica](cross-region-replication-create-peering.md)) prepara l'utente per un evento di ripristino di emergenza. 

Quando si verifica un evento di questo tipo, è possibile [eseguire il failover al volume di destinazione](#break-replication-peering-to-activate-the-destination-volume), consentendo al client di leggere e scrivere nel volume di destinazione. 

Dopo il ripristino di emergenza, è possibile eseguire il failback nel volume di origine con un' [operazione di risincronizzazione](#resync-replication-to-reactivate-the-source-volume) che sovrascrive i dati del volume di origine con i dati del volume di destinazione.  È quindi possibile [ristabilire la replica da origine a destinazione](#reestablish-source-to-destination-replication) e rimontare il volume di origine affinché il client acceda a. 

I dettagli sono descritti di seguito. 

## <a name="break-replication-peering-to-activate-the-destination-volume"></a>Interrompere il peering della replica per attivare il volume di destinazione

Quando è necessario attivare il volume di destinazione, ad esempio quando si desidera eseguire il failover nell'area di destinazione, è necessario interrompere il peering di replica e quindi montare il volume di destinazione.  

1. Per interrompere il peering della replica, selezionare il volume di destinazione. Fare clic su **replica** in servizio di archiviazione.  

2.  Prima di continuare, controllare i campi seguenti:  
    * Verificare che lo stato del mirror indichi il ***mirroring***.   
        Non tentare di interrompere il peering della replica se lo stato del mirror viene visualizzato come non *inizializzato*.
    * Verificare che lo stato della relazione mostri ***inattivo***.   
        Non tentare di interrompere il peering di replica se lo stato della relazione indica il *trasferimento*.   

    Vedere [visualizzare lo stato di integrità della relazione di replica](cross-region-replication-display-health-status.md). 

3.  Fare clic su **Interrompi peering**.  

4.  Digitare **Yes** quando richiesto e fare clic sul pulsante **Interrompi** . 

    ![Interrompere il peering della replica](../media/azure-netapp-files/cross-region-replication-break-replication-peering.png)

5.  Montare il volume di destinazione attenendosi alla procedura descritta in [montare o smontare un volume per le macchine virtuali Windows o Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).   
    Questo passaggio consente a un client di accedere al volume di destinazione.

## <a name="resync-replication-to-reactivate-the-source-volume"></a>Risincronizzare la replica per riattivare il volume di origine   

Dopo il ripristino di emergenza, è possibile riattivare il volume di origine eseguendo un'operazione di risincronizzazione.  L'operazione di risincronizzazione inverte il processo di replica e sincronizza i dati dal volume di destinazione al volume di origine.  

> [!IMPORTANT] 
> L'operazione di risincronizzazione sovrascrive i dati del volume di origine con i dati del volume di destinazione.  L'interfaccia utente segnala il rischio potenziale di perdita di dati. Verrà richiesto di confermare l'azione di risincronizzazione prima dell'avvio dell'operazione.

1. Per risincronizzare la replica, selezionare il volume di *origine* . Fare clic su **replica** in servizio di archiviazione. Quindi fare clic su **Risincronizza**.  

2. Digitare **Yes** quando richiesto e fare clic sul pulsante **Risincronizza** . 
 
    ![Risincronizzare la replica](../media/azure-netapp-files/cross-region-replication-resync-replication.png)

3. Monitorare lo stato di integrità del volume di origine attenendosi alla procedura descritta in [visualizzare lo stato di integrità della relazione di replica](cross-region-replication-display-health-status.md).   
    Quando lo stato di integrità del volume di origine Mostra i valori seguenti, l'operazione di risincronizzazione è completata e le modifiche apportate nel volume di destinazione vengono ora acquisite nel volume di origine:   

    * Stato con mirroring: con *mirroring*  
    * Stato trasferimento: *inattivo*  

## <a name="reestablish-source-to-destination-replication"></a>Ristabilire la replica da origine a destinazione

Al termine dell'operazione di risincronizzazione dalla destinazione all'origine, è necessario interrompere nuovamente il peering della replica per ristabilire la replica da origine a destinazione. È necessario rimontare anche il volume di origine in modo che il client possa accedervi.  

1. Interrompere il peering di replica:  
    a. Selezionare il volume di *destinazione* . Fare clic su **replica** in servizio di archiviazione.  
    b. Prima di continuare, controllare i campi seguenti:   
    * Verificare che lo stato del mirror indichi il ***mirroring***.   
    Non tentare di interrompere il peering della replica se lo stato del mirror viene visualizzato come non *inizializzato*.  
    * Verificare che lo stato della relazione mostri ***inattivo***.   
    Non tentare di interrompere il peering di replica se lo stato della relazione indica il *trasferimento*.    

        Vedere [visualizzare lo stato di integrità della relazione di replica](cross-region-replication-display-health-status.md). 

    c. Fare clic su **Interrompi peering**.   
    d. Digitare **Yes** quando richiesto e fare clic sul pulsante **Interrompi** .  

2. Risincronizzare il volume di origine con il volume di destinazione:  
    a. Selezionare il volume di *destinazione* . Fare clic su **replica** in servizio di archiviazione. Quindi fare clic su **Risincronizza**.   
    b. Digitare **Yes** quando richiesto e fare clic sul pulsante **Risincronizza** .

3. Rimontare il volume di origine attenendosi alla procedura descritta in [montare o smontare un volume per le macchine virtuali Windows o Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).  
    Questo passaggio consente a un client di accedere al volume di origine.

## <a name="next-steps"></a>Passaggi successivi  

* [Replica tra più aree](cross-region-replication-introduction.md)
* [Requisiti e considerazioni per l'uso della replica tra aree](cross-region-replication-requirements-considerations.md)
* [Visualizzare lo stato di integrità della relazione di replica](cross-region-replication-display-health-status.md)
* [Metriche di replica del volume](azure-netapp-files-metrics.md#replication)
* [Risolvere i problemi di replica tra aree](troubleshoot-cross-region-replication.md)

