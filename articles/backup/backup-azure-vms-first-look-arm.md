---
title: Eseguire il backup di una macchina virtuale di Azure dalle impostazioni della macchina virtuale
description: Questo articolo illustra come eseguire il backup di una singola macchina virtuale di Azure o di più macchine virtuali di Azure con il servizio backup di Azure.
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: 55b71d2a2901cdde984df3ebfd68a2a643b78b74
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89667520"
---
# <a name="back-up-an-azure-vm-from-the-vm-settings"></a>Eseguire il backup di una macchina virtuale di Azure dalle impostazioni della macchina virtuale

Questo articolo spiega come eseguire il backup di macchine virtuali di Azure con il servizio [Backup di Azure](backup-overview.md). Esistono due metodi per eseguire il backup di macchine virtuali di Azure:

- Singola macchina virtuale di Azure: le istruzioni in questo articolo descrivono come eseguire il backup di una macchina virtuale di Azure direttamente dalle impostazioni della macchina virtuale.
- Più macchine virtuali di Azure: è possibile configurare un insieme di credenziali di servizi di ripristino e configurare il backup per più macchine virtuali di Azure. Seguire le istruzioni di [questo articolo](backup-azure-arm-vms-prepare.md) per questo scenario.

## <a name="before-you-start"></a>Prima di iniziare

1. [Informazioni](backup-architecture.md#how-does-azure-backup-work) sul funzionamento del backup e [verifica](backup-support-matrix.md#azure-vm-backup-support) dei requisiti di supporto.
2. [Ottenere una panoramica](backup-azure-vms-introduction.md) del backup delle macchine virtuali di Azure.

### <a name="azure-vm-agent-installation"></a>Installazione dell'agente di macchine virtuali di Azure

Per eseguire il backup di macchine virtuali di Azure, backup di Azure installa un'estensione nell'agente di macchine virtuali in esecuzione nel computer. Se la macchina virtuale è stata creata da un'immagine di Azure Marketplace, l'agente verrà eseguito. In alcuni casi, ad esempio se si crea una VM personalizzata o si esegue la migrazione di un computer locale, potrebbe essere necessario installare l'agente manualmente.

- Per installare manualmente l'agente di macchine virtuali, seguire le istruzioni per macchine virtuali [Windows](../virtual-machines/extensions/agent-windows.md) o [Linux](../virtual-machines/extensions/agent-linux.md).
- Quando si abilita il backup, dopo aver installato l'agente, Backup di Azure installa l'estensione di backup all'agente. Questi esegue aggiornamenti e patch all'estensione senza intervento dell'utente.

## <a name="back-up-from-azure-vm-settings"></a>Eseguire il backup dalle impostazioni della macchina virtuale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare **tutti i servizi** e nel filtro digitare **macchine virtuali**, quindi selezionare **macchine virtuali**.
3. Dall'elenco di macchine virtuali selezionare la macchina virtuale di cui si vuole eseguire il backup.
4. Scegliere **backup**dal menu VM.
5. Nell'**insieme di credenziali di Servizi di ripristino**, eseguire le operazioni seguenti:
   - Se si dispone già di un insieme di credenziali, selezionare **Seleziona esistente**e selezionare un insieme di credenziali.
   - Se non si dispone di un insieme di credenziali, selezionare **Crea nuovo**. Specificare un nome per l'insieme di credenziali. Viene creato nella stessa area e nello stesso gruppo di risorse della macchina virtuale. Non è possibile modificare queste impostazioni quando si abilita il backup direttamente dalle impostazioni della macchina virtuale.

        ![Procedura guidata Abilita backup](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

6. In **scegliere i criteri di backup**eseguire una delle operazioni seguenti:

   - Lasciare il valore predefinito. Questa impostazione esegue il backup della macchina virtuale una volta al giorno all'ora specificata e conserva i backup nell'insieme di credenziali per 30 giorni.
   - Selezionare un criterio di backup esistente, se presente.
   - Creare un nuovo criterio e definire le impostazioni dei criteri.  

       ![Selezionare il criterio di backup](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. Selezionare **Abilita backup**. Consente di associare i criteri di backup alla macchina virtuale.

    ![Pulsante Abilita backup](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. È possibile monitorare l'avanzamento della configurazione nelle notifiche del portale.
9. Al termine del processo, nel menu VM selezionare **backup**. La pagina mostra lo stato del backup per la macchina virtuale, le informazioni sui punti di ripristino, i processi in esecuzione e gli avvisi emessi.

   ![Stato di Backup](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

10. Dopo l'abilitazione del backup, viene eseguito un backup iniziale. È possibile avviare il backup iniziale immediatamente o attendere fino a quando non viene avviato in base alla pianificazione dello stesso.
    - Fino al completamento del backup iniziale, lo **stato dell'ultimo backup** è **Avviso (backup iniziale in sospeso)**.
    - Per verificare quando verrà eseguito il successivo backup pianificato, selezionare il nome del criterio di backup.

## <a name="run-a-backup-immediately"></a>Eseguire immediatamente un backup

1. Per eseguire immediatamente un backup **, scegliere backup backup**  >  **ora**dal menu VM.

    ![Esegui backup](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

2. In **backup ora**usare il controllo Calendar per selezionare fino a quando il punto di ripristino non verrà mantenuto > e **OK**.

    ![Giorno di conservazione backup](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

3. Le notifiche del portale consentono di sapere se è stato attivato il processo di backup. Per monitorare lo stato di avanzamento del backup, selezionare **Visualizza tutti i processi**.

## <a name="back-up-from-the-recovery-services-vault"></a>Eseguire il backup da un insieme di credenziali di Servizi di ripristino

Seguire le istruzioni in [questo articolo](backup-azure-arm-vms-prepare.md) per abilitare il backup per le macchine virtuali di Azure configurando un insieme di credenziali di servizi di ripristino di backup di Azure e abilitando il backup nell'insieme di credenziali.

## <a name="next-steps"></a>Passaggi successivi

- Se si riscontrano problemi con una qualsiasi delle procedure riportate in questo articolo, consultare la [Guida alla risoluzione dei problemi](backup-azure-vms-troubleshoot.md).
- [Informazioni](backup-azure-manage-vms.md) sulla gestione dei backup.
