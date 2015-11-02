<properties
   pageTitle="Eseguire il backup di file e cartelle Windows Server o Client Windows in Azure | Microsoft Azure"
   description="Informazioni su come eseguire operazioni di backup in Azure da un computer che esegue Windows Server o un client Windows."
   services="backup"
   documentationCenter=""
   authors="aashishr"
   manager="jwhit"
   editor=""/>

<tags 
   ms.service="backup"
   ms.workload="storage-backup-recovery"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="10/21/2015"
   ms.author="jimpark"; "aashishr"/>

# Eseguire il backup di file e cartelle Windows Server o Client Windows in Azure
Questo articolo illustra i passaggi necessari per eseguire il backup di file e cartelle di Windows Server o di un client Windows in Azure.

## Prima di iniziare
Prima di procedere, assicurarsi che tutti i [Prerequisiti ](backup-configure-vault.md#before-you-start) per l'utilizzo di Backup di Microsoft Azure per proteggere Windows Server o un client Windows siano stati soddisfatti. I prerequisiti si applicano ad attività, ad esempio: la creazione di un archivio di backup, il download delle credenziali dell'insieme di credenziali, l'installazione dell'agente di Backup di Azure e la registrazione del server con l'insieme di credenziali.

## File di backup
1. Dopo aver registrato il computer, aprire lo snap-in MMC di Backup di Microsoft Azure.

    ![Risultato della ricerca](./media/backup-azure-backup-windows-server/result.png)

2. Fare clic su **Pianificazione di un backup**

    ![Pianificazione di un backup](./media/backup-azure-backup-windows-server/schedulebackup.png)

3. Selezionare gli elementi da sottoporre a backup. Backup di Azure in un computer che esegue Windows Server o un client Windows (ovvero senza System Center Data Protection Manager) consente di proteggere file e cartelle.

    ![Elementi da sottoporre a backup](./media/backup-azure-backup-windows-server/items.png)

4. Specificare i criteri di conservazione e pianificazione del backup descritti in dettaglio nell'[articolo](backup-azure-backup-cloud-as-tape.md) seguente.

5. Scegliere il metodo di invio del backup iniziale. La scelta di eseguire il seeding iniziale dipende dalla quantità di dati da sottoporre a backup e dalla velocità del collegamento Internet durante il caricamento. Se si prevede di sottoporre a backup dati di dimensioni molto grandi su una latenza elevata e connessioni a larghezza di banda ridotta, si consiglia di completare il backup inviando un disco al data center di Azure più vicino. Questa procedura viene definita "Backup offline" e viene descritta in dettaglio in questo [articolo](backup-azure-backup-import-export.md). Se sono disponibili connessioni a larghezza di banda sufficiente, si consiglia di completare il backup iniziale sulla rete.

    ![Backup iniziale](./media/backup-azure-backup-windows-server/initialbackup.png)

6. Dopo aver completato il backup, accedere nuovamente allo snap-in MMC e fare clic su **Esegui backup** per completare il seeding iniziale sulla rete.

    ![Esegui backup ora](./media/backup-azure-backup-windows-server/backupnow.png)

7. Dopo aver completato il seeding iniziale, la vista **Processi** nella console di Backup di Azure indica lo stato.

    ![Completamento infrarossi](./media/backup-azure-backup-windows-server/ircomplete.png)

## Passaggi successivi
- [Gestione di Windows Server o Windows Client](backup-azure-manage-windows-server.md)
- [Ripristino di Windows Server o Windows Client da Azure](backup-azure-restore-windows-server.md)
- [Backup di Azure - Domande frequenti](backup-azure-backup-faq.md)

<!----HONumber=Oct15_HO4-->