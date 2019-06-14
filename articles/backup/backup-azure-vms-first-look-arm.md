---
title: Eseguire il backup di una macchina virtuale di Azure dalle impostazioni della macchina virtuale con il servizio Backup di Azure
description: Informazioni su come eseguire il backup di una macchina virtuale di Azure con il servizio Backup di Azure
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: raynew
ms.openlocfilehash: 906024ecb3e95c75c45efddafbbf76944c6aea29
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058076"
---
# <a name="back-up-an-azure-vm-from-the-vm-settings"></a>Eseguire il backup di una macchina virtuale di Azure dalle impostazioni della macchina virtuale

Questo articolo spiega come eseguire il backup di macchine virtuali di Azure con il servizio [Backup di Azure](backup-overview.md). Esistono due metodi per eseguire il backup di macchine virtuali di Azure:

- Singola macchina virtuale di Azure: le istruzioni riportate in questo articolo descrivono come eseguire il backup di una macchina virtuale di Azure direttamente dalle impostazioni della macchina virtuale.
- Più macchine virtuali di Azure: è possibile configurare un insieme di credenziali di Servizi di ripristino e configurare il backup per più macchine virtuali di Azure. Seguire le istruzioni di [questo articolo](backup-azure-arm-vms-prepare.md) per questo scenario.



## <a name="before-you-start"></a>Prima di iniziare

1. [Informazioni](backup-architecture.md#how-does-azure-backup-work) sul funzionamento del backup e [verifica](backup-support-matrix.md#azure-vm-backup-support) dei requisiti di supporto.
2. [Ottenere una panoramica](backup-azure-vms-introduction.md) del backup delle macchine virtuali di Azure.

### <a name="azure-vm-agent-installation"></a>Installazione dell'agente di macchine virtuali di Azure

Per eseguire il backup di macchine virtuali di Azure, Backup di Azure installa un'estensione nell'agente di macchine virtuali in esecuzione nel computer. L'agente sarà in esecuzione se la macchina virtuale è stata creata da un'immagine di Azure marketplace. In alcuni casi, ad esempio se si crea una macchina virtuale personalizzata o si esegue la migrazione di una macchina da locale. potrebbe essere necessario installare l'agente manualmente.

- Per installare manualmente l'agente di macchine virtuali, seguire le istruzioni per macchine virtuali [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) o [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux).
- Quando si abilita il backup, dopo aver installato l'agente, Backup di Azure installa l'estensione di backup all'agente. Questi esegue aggiornamenti e patch all'estensione senza intervento dell'utente.

## <a name="back-up-from-azure-vm-settings"></a>Eseguire il backup dalle impostazioni della macchina virtuale di Azure


1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **Tutti i servizi**, digitare **Macchine virtuali** in Filtro e fare clic su **Macchine virtuali**.
3. Nell'elenco di macchine virtuali selezionare quella di cui si vuole eseguire il backup.
4. Nel menu della macchina virtuale fare clic su **Backup**.
5. Nell'**insieme di credenziali di Servizi di ripristino**, eseguire le operazioni seguenti:
   - Se si dispone già di un insieme di credenziali, fare clic su **Seleziona esistente**e selezionare un insieme di credenziali.
   - Se non si dispone di un insieme di credenziali, fare clic su **Crea nuovo**. Specificare un nome per l'insieme di credenziali. Viene creato nella stessa area e nello stesso gruppo di risorse della macchina virtuale. Non è possibile modificare queste impostazioni quando si abilita il backup direttamente dalle impostazioni della macchina virtuale.

   ![Procedura guidata Abilita backup](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

6. In **Scegliere i criteri di backup**, eseguire le operazioni seguenti:

   - Lasciare il valore predefinito. Questa impostazione esegue il backup della macchina virtuale una volta al giorno all'ora specificata e conserva i backup nell'insieme di credenziali per 30 giorni.
   - Selezionare un criterio di backup esistente, se lo si possiede.
   - Creare un nuovo criterio e definire le impostazioni dei criteri.  

   ![Selezionare il criterio di backup](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. Fare clic su **Abilita backup**. Consente di associare i criteri di backup alla macchina virtuale.

    ![Pulsante Abilita backup](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. È possibile monitorare l'avanzamento della configurazione nelle notifiche del portale.
9. Dopo il completamento del processo, fare clic su **Backup** nel menu della macchina virtuale. La pagina mostra lo stato del backup per la macchina virtuale, le informazioni sui punti di ripristino, i processi in esecuzione e gli avvisi emessi.

   ![Stato di Backup](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

10. Dopo l'abilitazione del backup, viene eseguito un backup iniziale. È possibile avviare il backup iniziale immediatamente o attendere fino a quando non viene avviato in base alla pianificazione dello stesso.
    - Fino al completamento del backup iniziale, lo **stato dell'ultimo backup** è **Avviso (backup iniziale in sospeso)** .
    - Per visualizzare quando verrà eseguito il successivo backup pianificato, fare clic sul nome del criterio di backup.


> [!NOTE]
> Servizio Backup di Azure consente di creare un gruppo di risorse separato (diverso dal gruppo di risorse della macchina virtuale) per archiviare lo snapshot, con il formato di denominazione **AzureBackupRG_geography_number** (esempio: AzureBackupRG_northeurope_1). I dati nel gruppo di risorse verranno mantenuti per la durata in giorni come specificato nella sezione "Snapshot di ripristino istantaneo Mantieni" del criterio di Backup di macchine virtuali di Azure. Applicare un blocco a questo gruppo di risorse può causare errori di backup.


## <a name="run-a-backup-immediately"></a>Eseguire immediatamente un backup

1. Per eseguire immediatamente un backup, fare clic su **Backup** > **Esegui backup ora** nel menu della macchina virtuale.

    ![Esegui backup](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

2. In **Esegui backup ora** usare il comando del calendario per selezionare fino a quando conservare il punto di ripristino > e **OK**.

    ![Giorno di conservazione backup](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

3. Le notifiche del portale consentono di sapere se è stato attivato il processo di backup. Per monitorare lo stato di backup, fare clic su **Visualizza tutti i processi**.




## <a name="back-up-from-the-recovery-services-vault"></a>Eseguire il backup da un insieme di credenziali di Servizi di ripristino

Seguire le istruzioni in questo articolo per abilitare il backup per macchine virtuali di Azure configurando un insieme di credenziali di Servizi di ripristino di Backup di Azure e per abilitare il backup nell'insieme di credenziali.

## <a name="next-steps"></a>Passaggi successivi

- Se si riscontrano problemi con una qualsiasi delle procedure riportate in questo articolo, consultare la [Guida alla risoluzione dei problemi](backup-azure-vms-troubleshoot.md).
- [Informazioni](backup-azure-manage-vms.md) sulla gestione dei backup.
