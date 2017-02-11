---
title: Eseguire il backup di macchine virtuali di Azure in un insieme di credenziali di Servizi di ripristino | Microsoft Docs
description: Individuare, registrare ed eseguire il backup delle macchine virtuali di Azure in un insieme di credenziali di Servizi di ripristino usando queste procedure per il backup di macchine virtuali di Azure.
services: backup
documentationcenter: 
author: markgalioto
manager: cfreeman
editor: 
keywords: backup della macchina virtuale; eseguire il backup della macchina virtuale; backup e ripristino di emergenza; backup di vm di ARM
ms.assetid: 5c68481d-7be3-4e68-b87c-0961c267053e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2016
ms.author: trinadhk; jimpark; markgal;
translationtype: Human Translation
ms.sourcegitcommit: ac8df40db8ddcc84a0a6221dddd0f17fecbe6586
ms.openlocfilehash: e80d4fdb6f189bf46096422602508b0827f41a67


---
# <a name="back-up-azure-vms-to-a-recovery-services-vault"></a>Eseguire il backup di macchine virtuali di Azure in un insieme di credenziali di Servizi di ripristino
> [!div class="op_single_selector"]
> * [Eseguire il backup di macchine virtuali in un insieme di credenziali di Servizi di ripristino](backup-azure-arm-vms.md)
> * [Eseguire il backup di macchine virtuali in un insieme di credenziali per il backup](backup-azure-vms.md)
>
>

Questo articolo descrive la procedura per eseguire il backup di macchine virtuali di Azure, distribuite con il modello di distribuzione classica e con il modello di distribuzione Resource Manager, in un insieme di credenziali di Servizi di ripristino. Per eseguire il backup di macchine virtuali, il più del lavoro è dato dalla preparazione. Prima di poter eseguire il backup o proteggere una macchina virtuale, è necessario completare i [prerequisiti](backup-azure-arm-vms-prepare.md) per preparare l'ambiente per la protezione delle macchine virtuali. Dopo aver completato i prerequisiti, è possibile avviare il backup per eseguire snapshot della macchina virtuale.


[!INCLUDE [learn about backup deployment models](../../includes/backup-deployment-models.md)]

Per altre informazioni, vedere gli articoli relativi alla [pianificazione dell'infrastruttura di backup delle macchine virtuali in Azure](backup-azure-vms-introduction.md) e alle [macchine virtuali di Azure](https://azure.microsoft.com/documentation/services/virtual-machines/).

## <a name="triggering-the-backup-job"></a>Attivazione del processo di backup
Il criterio di backup associato all'insieme di credenziali di servizi di ripristino definisce quando e con quale frequenza eseguire l'operazione di backup. Per impostazione predefinita, il primo backup pianificato è il backup iniziale. Fino all'esecuzione del backup iniziale, lo stato dell'ultimo backup nel pannello **Processi di Backup** è **Avviso (backup iniziale in sospeso)**.

![Backup in sospeso](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

A meno che l'avvio del backup iniziale non sia imminente, è consigliabile scegliere l'opzione **Esegui backup ora**. La procedura seguente viene avviata dal dashboard dell'insieme di credenziali. Questa procedura viene usata per l'esecuzione del processo di backup iniziale dopo aver completato tutti i prerequisiti. Se è già stato eseguito il processo di backup iniziale, questa procedura non è disponibile. I criteri di backup associati determinano il processo di backup successivo.  

Per eseguire il processo di backup iniziale:

1. Nel dashboard dell'insieme di credenziali, nel riquadro **Backup**, fare clic su **Macchine virtuali di Azure**. <br/>
    ![Icona Impostazioni](./media/backup-azure-vms-first-look-arm/rs-vault-in-dashboard-backup-vms.png)

    Si apre il pannello **Elementi di backup** .
2. Nel pannello **Elementi di backup** fare clic con il pulsante destro del mouse sull'insieme di credenziali di cui si vuole eseguire il backup e scegliere **Esegui backup ora**.

    ![Icona Impostazioni](./media/backup-azure-vms-first-look-arm/back-up-now.png)

    Viene attivato il processo di backup. <br/>

    ![Processo di backup attivato](./media/backup-azure-vms-first-look-arm/backup-triggered.png)
3. Per verificare che il backup iniziale sia stato completato, nel riquadro **Processi di backup** del dashboard dell'insieme di credenziali fare clic su **Macchine virtuali di Azure**.

    ![Riquadro dei processi di backup](./media/backup-azure-vms-first-look-arm/open-backup-jobs.png)

    Si apre il pannello dei processi di backup.
4. Nel pannello **Processi di backup** è possibile visualizzare lo stato di tutti i processi.

    ![Riquadro dei processi di backup](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view.png)

   > [!NOTE]
   > Durante il backup, l'estensione di backup in ogni macchina virtuale scarica tutte le scritture ed esegue uno snapshot.
   >
   >

    Al termine del processo di backup, lo stato è *Completato*.

## <a name="troubleshooting-errors"></a>Risoluzione dei problemi
Se si riscontrano problemi durante il backup della macchina virtuale, leggere l'[articolo sulla risoluzione dei problemi delle macchine virtuali](backup-azure-vms-troubleshoot.md) per assistenza.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver protetto la macchina virtuale, vedere gli articoli seguenti per ottenere informazioni sulle attività di gestione delle macchine virtuali e su come ripristinare le macchine virtuali.

* [Gestire e monitorare il backup delle macchine virtuali di Azure](backup-azure-manage-vms.md)
* [Ripristino di macchine virtuali](backup-azure-arm-restore-vms.md)



<!--HONumber=Nov16_HO3-->


