<properties
   pageTitle="Eseguire il backup di file e cartelle Windows Server o Client Windows in Azure | Microsoft Azure"
   description="Eseguire il backup di un server Windows Server o client Windows in Azure con questa semplice procedura. È possibile eseguire il backup di file e cartelle di Windows nel cloud in pochi semplici passaggi."
   services="backup"
   documentationCenter=""
   authors="aashishr"
   manager="jwhit"
   editor=""
   keywords="backup di Windows server; backup server windows"/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="17/11/2015" ms.author="jimpark"; "aashishr"/>

# Eseguire il backup di file e cartelle Windows Server o Client Windows in Azure
È facile eseguire il backup di cartelle e file di Windows in Azure con questa semplice procedura. Se non è già stato fatto, completare i [prerequisiti](backup-configure-vault.md#before-you-start) per preparare l'ambiente per il backup del computer Windows prima di procedere.

## File di backup
1. Dopo aver registrato il computer, aprire lo snap-in MMC di Backup di Microsoft Azure.

    ![Risultato della ricerca](./media/backup-azure-backup-windows-server/result.png)

2. Fare clic su **Pianificazione di un backup**

    ![Pianificare un backup di Windows Server](./media/backup-azure-backup-windows-server/schedulebackup.png)

3. Selezionare gli elementi da sottoporre a backup. Backup di Azure in un computer che esegue Windows Server o un client Windows (ovvero senza System Center Data Protection Manager) consente di proteggere file e cartelle.

    ![Elementi per il backup di Windows Server](./media/backup-azure-backup-windows-server/items.png)

4. Specificare i criteri di conservazione e pianificazione del backup descritti in dettaglio nell'[articolo](backup-azure-backup-cloud-as-tape.md) seguente.

5. Scegliere il metodo di invio del backup iniziale. La scelta di eseguire il seeding iniziale dipende dalla quantità di dati da sottoporre a backup e dalla velocità del collegamento Internet durante il caricamento. Se si prevede di sottoporre a backup dati di dimensioni molto grandi su una latenza elevata e connessioni a larghezza di banda ridotta, si consiglia di completare il backup inviando un disco al data center di Azure più vicino. Questa procedura viene definita "Backup offline" e viene descritta in dettaglio in questo [articolo](backup-azure-backup-import-export.md). Se sono disponibili connessioni a larghezza di banda sufficiente, si consiglia di completare il backup iniziale sulla rete.

    ![Backup iniziale di Windows Server](./media/backup-azure-backup-windows-server/initialbackup.png)

6. Dopo aver completato il backup, accedere nuovamente allo snap-in MMC e fare clic su **Esegui backup** per completare il seeding iniziale sulla rete.

    ![Eseguire ora il backup di Windows Server](./media/backup-azure-backup-windows-server/backupnow.png)

7. Dopo aver completato il seeding iniziale, la vista **Processi** nella console di Backup di Azure indica lo stato.

    ![Completamento infrarossi](./media/backup-azure-backup-windows-server/ircomplete.png)

## Passaggi successivi
- [Gestione di Windows Server o Windows Client](backup-azure-manage-windows-server.md)
- [Ripristino di Windows Server o Windows Client da Azure](backup-azure-restore-windows-server.md)
- [Backup di Azure - Domande frequenti](backup-azure-backup-faq.md)

<!---HONumber=Nov15_HO4-->