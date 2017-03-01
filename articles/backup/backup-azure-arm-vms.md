---
title: Eseguire il backup di macchine virtuali di Azure | Documentazione Microsoft
description: Scoprire, registrare ed eseguire il backup di macchine virtuali di Azure in un insieme di credenziali di Servizi di ripristino.
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: backup della macchina virtuale; eseguire il backup della macchina virtuale; backup e ripristino di emergenza; backup di vm di ARM
ms.assetid: 5c68481d-7be3-4e68-b87c-0961c267053e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/15/2017
ms.author: trinadhk;jimpark;markgal;
translationtype: Human Translation
ms.sourcegitcommit: dca042ce1684b35e6a874075e0de28b9d8766331
ms.openlocfilehash: 981c8652629e96f482d9a62b70b0f0992517019f
ms.lasthandoff: 02/16/2017


---
# <a name="back-up-azure-vms-to-a-recovery-services-vault"></a>Eseguire il backup di macchine virtuali di Azure in un insieme di credenziali di Servizi di ripristino
> [!div class="op_single_selector"]
> * [Eseguire il backup di macchine virtuali in un insieme di credenziali di Servizi di ripristino](backup-azure-arm-vms.md)
> * [Eseguire il backup di macchine virtuali in un insieme di credenziali per il backup](backup-azure-vms.md)
>
>

Questo articolo descrive come eseguire il backup di macchine virtuali di Azure, distribuite con il modello di distribuzione classica e con il modello di distribuzione Resource Manager, in un insieme di credenziali di Servizi di ripristino. Per eseguire il backup di macchine virtuali, il più del lavoro è dato dalla preparazione. Prima di poter eseguire il backup o proteggere una macchina virtuale, è necessario completare i [prerequisiti](backup-azure-arm-vms-prepare.md) per preparare l'ambiente per la protezione delle macchine virtuali. Dopo aver completato i prerequisiti, è possibile avviare il backup per eseguire snapshot della macchina virtuale.


[!INCLUDE [learn about backup deployment models](../../includes/backup-deployment-models.md)]

Per altre informazioni, vedere gli articoli relativi alla [pianificazione dell'infrastruttura di backup delle macchine virtuali in Azure](backup-azure-vms-introduction.md) e alle [macchine virtuali di Azure](https://azure.microsoft.com/documentation/services/virtual-machines/).

## <a name="triggering-the-backup-job"></a>Attivazione del processo di backup
Il criterio di backup associato all'insieme di credenziali di servizi di ripristino definisce quando e con quale frequenza eseguire l'operazione di backup. Per impostazione predefinita, il primo backup pianificato è il backup iniziale. Fino all'esecuzione del backup iniziale, lo stato dell'ultimo backup nel pannello **Processi di Backup** è **Avviso (backup iniziale in sospeso)**.

![Backup in sospeso](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

A meno che l'avvio del backup iniziale non sia imminente, è consigliabile scegliere l'opzione **Esegui backup ora**. La procedura seguente viene avviata dal dashboard dell'insieme di credenziali. Questa procedura viene usata per l'esecuzione del processo di backup iniziale dopo aver completato tutti i prerequisiti. Se è già stato eseguito il processo di backup iniziale, questa procedura non è disponibile. I criteri di backup associati determinano il processo di backup successivo.  

Per eseguire il processo di backup iniziale:

1. Nel dashboard dell'insieme di credenziali fare clic sul numero sotto **Elementi di backup** oppure fare clic sul riquadro **Elementi di backup**. <br/>
  ![Icona Impostazioni](./media/backup-azure-vms-first-look-arm/rs-vault-config-vm-back-up-now-1.png)

  Si apre il pannello **Elementi di backup** .

  ![Elementi di backup](./media/backup-azure-vms-first-look-arm/back-up-items-list.png)

2. Nel pannello **Elementi di backup** selezionare l'elemento.

  ![Icona Impostazioni](./media/backup-azure-vms-first-look-arm/back-up-items-list-selected.png)

  Si apre l'elenco **Elementi di backup**. <br/>

  ![Processo di backup attivato](./media/backup-azure-vms-first-look-arm/backup-items-not-run.png)

3. Nell'elenco **Elementi di backup** fare clic sui puntini di sospensione **...** per aprire il menu di scelta rapida.

  ![Menu di scelta rapida](./media/backup-azure-vms-first-look-arm/context-menu.png)

  Si apre il menu di scelta rapida.

  ![Menu di scelta rapida](./media/backup-azure-vms-first-look-arm/context-menu-small.png)

4. Nel menu di scelta rapida fare clic su **Esegui backup ora**.

  ![Menu di scelta rapida](./media/backup-azure-vms-first-look-arm/context-menu-small-backup-now.png)

  Si apre il pannello Esegui backup ora.

  ![mostra il pannello Esegui backup ora](./media/backup-azure-vms-first-look-arm/backup-now-blade-short.png)

5. Nel pannello Esegui backup ora fare clic sull'icona del calendario, usare il comando del calendario per selezionare l'ultimo giorno di conservazione di tale punto di ripristino e fare clic su **Esegui backup**.

  ![impostare l'ultimo giorno di conservazione del punto di ripristino di Esegui backup ora](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

  Le notifiche sulla distribuzione consentono di sapere che il processo di backup è stato attivato e che è possibile monitorare lo stato di avanzamento del processo nella pagina Processi di backup. A seconda delle dimensioni della macchina virtuale, la creazione del backup iniziale potrebbe richiedere un po' di tempo.

6. Per visualizzare o tenere traccia dello stato del backup iniziale, nel dashboard dell'insieme di credenziali, nel riquadro **Processi di backup** fare clic sul riquadro **In corso**.

  ![Riquadro dei processi di backup](./media/backup-azure-vms-first-look-arm/open-backup-jobs-1.png)

  Si apre il pannello dei processi di backup.

  ![Riquadro dei processi di backup](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view-1.png)

  Nel pannello **Processi di backup** è possibile visualizzare lo stato di tutti i processi. Controllare se il processo di backup per la macchina virtuale è ancora in corso o se è terminato. Al termine del processo di backup, lo stato è *Completato*.

  > [!NOTE]
  > Come parte dell'operazione di backup, il servizio Backup di Azure esegue un comando nell'estensione di backup in ogni VM per scaricare tutte le scritture e creare uno snapshot coerente.
  >
  >

## <a name="troubleshooting-errors"></a>Risoluzione dei problemi
Se si riscontrano problemi durante il backup della macchina virtuale, leggere l'[articolo sulla risoluzione dei problemi delle macchine virtuali](backup-azure-vms-troubleshoot.md) per assistenza.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver protetto la macchina virtuale, vedere gli articoli seguenti per ottenere informazioni sulle attività di gestione delle macchine virtuali e su come ripristinare le macchine virtuali.

* [Gestire e monitorare il backup delle macchine virtuali di Azure](backup-azure-manage-vms.md)
* [Ripristino di macchine virtuali](backup-azure-arm-restore-vms.md)

