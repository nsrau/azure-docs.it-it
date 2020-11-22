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
ms.openlocfilehash: eab55f881c250c2e07717604d4ba00587a8b6031
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2020
ms.locfileid: "95243206"
---
# <a name="manage-disaster-recovery-using-cross-region-replication"></a>Gestire il ripristino di emergenza con la replica tra aree 

Una replica in corso tra i volumi di origine e di destinazione (vedere [creare la replica del volume](cross-region-replication-create-peering.md)) prepara l'utente per un evento di ripristino di emergenza. 

Quando si verifica un evento di questo tipo, è possibile eseguire il [failover al volume di destinazione](#fail-over-to-destination-volume), consentendo al client di leggere e scrivere nel volume di destinazione. 

Dopo il ripristino di emergenza, è possibile eseguire un'operazione di [Risincronizzazione](#resync-replication) per eseguire il failback nel volume di origine. È quindi possibile [ristabilire la replica da origine a destinazione](#reestablish-source-to-destination-replication) e rimontare il volume di origine affinché il client acceda a. 

I dettagli sono descritti di seguito. 

## <a name="fail-over-to-destination-volume"></a>Failover al volume di destinazione

Quando è necessario attivare il volume di destinazione, ad esempio quando si desidera eseguire il failover nell'area di destinazione, è necessario interrompere il peering di replica e quindi montare il volume di destinazione.  

1. Per interrompere il peering della replica, selezionare il volume di destinazione. Fare clic su **replica** in servizio di archiviazione.  

2.  Prima di continuare, controllare i campi seguenti:  
    * Verificare che lo stato del mirror indichi ***Mirrored** _.   
        Non tentare di interrompere il peering della replica se lo stato del mirror indica _Uninitialized *.
    * Verificare che lo stato della relazione sia visualizzato ***inattivo** _.   
        Non tentare di interrompere il peering di replica se lo stato della relazione Mostra _Transferring *.   

    Vedere [visualizzare lo stato di integrità della relazione di replica](cross-region-replication-display-health-status.md). 

3.  Fare clic su **Interrompi peering**.  

4.  Digitare **Yes** quando richiesto e fare clic sul pulsante **Interrompi** . 

    ![Interrompere il peering della replica](../media/azure-netapp-files/cross-region-replication-break-replication-peering.png)

5.  Montare il volume di destinazione attenendosi alla procedura descritta in [montare o smontare un volume per le macchine virtuali Windows o Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).   
    Questo passaggio consente a un client di accedere al volume di destinazione.

## <a name="resync-volumes-after-disaster-recovery"></a><a name="resync-replication"></a>Risincronizza i volumi dopo il ripristino di emergenza

Dopo il ripristino di emergenza, è possibile riattivare il volume di origine eseguendo un'operazione di risincronizzazione.  L'operazione di risincronizzazione inverte il processo di replica e sincronizza i dati dal volume di destinazione al volume di origine.  

> [!IMPORTANT] 
> L'operazione di risincronizzazione sovrascrive i dati del volume di origine con i dati del volume di destinazione.  L'interfaccia utente segnala il rischio potenziale di perdita di dati. Verrà richiesto di confermare l'azione di risincronizzazione prima dell'avvio dell'operazione.

1. Per risincronizzare la replica, selezionare il volume di *origine* . Fare clic su **replica** in servizio di archiviazione. Quindi fare clic su **Risincronizza**.  

2. Digitare **Yes** quando richiesto e fare clic su **Risincronizza**. 
 
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
    * Verificare che lo stato del mirror indichi ***Mirrored** _.   
    Non tentare di interrompere il peering della replica se lo stato del mirror indica _uninitialized *.  
    * Verificare che lo stato della relazione sia visualizzato ***inattivo** _.   
    Non tentare di interrompere il peering di replica se lo stato della relazione Mostra _transferring *.    

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
* [Visualizzare lo stato integrità della relazione di replica](cross-region-replication-display-health-status.md)
* [Metriche di replica del volume](azure-netapp-files-metrics.md#replication)
* [Elimina volumi o repliche di volumi](cross-region-replication-delete.md)
* [Risolvere i problemi relativi alla replica tra più aree](troubleshoot-cross-region-replication.md)

