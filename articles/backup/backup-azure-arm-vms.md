<properties
	pageTitle="Eseguire il backup di macchine virtuali di Azure in un insieme di credenziali di Servizi di ripristino | Microsoft Azure"
	description="Individuare, registrare ed eseguire il backup delle macchine virtuali di Azure in un insieme di credenziali di Servizi di ripristino usando queste procedure per il backup di macchine virtuali di Azure."
	services="backup"
	documentationCenter=""
	authors="markgalioto"
	manager="cfreeman"
	editor=""
	keywords="backup della macchina virtuale; eseguire il backup della macchina virtuale; backup e ripristino di emergenza; backup di vm di ARM"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/29/2016"
	ms.author="trinadhk; jimpark; markgal;"/>


# Eseguire il backup di macchine virtuali di Azure in un insieme di credenziali di Servizi di ripristino

> [AZURE.SELECTOR]
- [Eseguire il backup di macchine virtuali in un insieme di credenziali di Servizi di ripristino](backup-azure-arm-vms.md)
- [Eseguire il backup di macchine virtuali in un insieme di credenziali per il backup](backup-azure-vms.md)

Questo articolo descrive la procedura per eseguire il backup di macchine virtuali di Azure, distribuite con il modello di distribuzione classica e con il modello di distribuzione Resource Manager, in un insieme di credenziali di Servizi di ripristino. La maggior parte dell'attività per il backup delle macchine virtuali si svolge durante la preparazione. Prima di poter eseguire il backup o proteggere una macchina virtuale, è necessario completare i [prerequisiti](backup-azure-arm-vms-prepare.md) per preparare l'ambiente per la protezione delle macchine virtuali. Dopo aver completato i prerequisiti, è possibile avviare il backup per eseguire snapshot della macchina virtuale.

>[AZURE.NOTE] Azure offre due modelli di distribuzione per creare e usare le risorse: [Resource Manager e distribuzione classica](../resource-manager-deployment-model.md). È possibile proteggere le macchine virtuali distribuite con il modello di distribuzione classica e con il modello di distribuzione Resource Manager con gli insiemi di credenziali di Servizi di ripristino. Vedere [Eseguire il backup delle macchine virtuali di Azure](backup-azure-vms.md) per informazioni dettagliate sull'uso delle macchine virtuali con il modello di distribuzione classica.

Per altre informazioni, vedere gli articoli [Pianificare l'infrastruttura di backup delle macchine virtuali in Azure](backup-azure-vms-introduction.md) e [Macchine virtuali - Documentazione](https://azure.microsoft.com/documentation/services/virtual-machines/).

## Attivazione del processo di backup

Il criterio di backup associato all'insieme di credenziali dei servizi di ripristino definisce quando e con quale frequenza si eseguono i processi di backup. Per impostazione predefinita, il primo backup pianificato è il backup iniziale. Fino all'esecuzione del backup iniziale, lo stato dell'ultimo backup nel pannello **Processi di backup** è **Avviso (backup iniziale in sospeso)**.

![Backup in sospeso](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

A meno che l'avvio del backup iniziale non sia imminente, è consigliabile **eseguire il backup adesso**. La procedura seguente viene avviata dal dashboard dell'insieme di credenziali. Questa procedura viene usata per l'esecuzione del processo di backup iniziale dopo aver completato tutti i prerequisiti. Se è già stato eseguito il processo di backup iniziale, questa procedura non è disponibile. I criteri di backup associati determinano il processo di backup successivo.

Per eseguire il processo di backup iniziale:

1. Nel riquadro **Backup** del dashboard dell'insieme di credenziali fare clic su **Macchine virtuali di Azure**. <br/> ![Icona Impostazioni](./media/backup-azure-vms-first-look-arm/rs-vault-in-dashboard-backup-vms.png)

    Si apre il pannello **Elementi di backup**.

2. Nel pannello **Elementi di backup** fare clic con il pulsante destro del mouse sull'insieme di credenziali di cui si vuole eseguire il backup e scegliere **Esegui backup**.

    ![Icona Impostazioni](./media/backup-azure-vms-first-look-arm/back-up-now.png)

    Viene attivato il processo di backup. <br/>

    ![Processo di backup attivato](./media/backup-azure-vms-first-look-arm/backup-triggered.png)

3. Per verificare che il backup iniziale sia stato completato, nel riquadro **Processi di backup** del dashboard dell'insieme di credenziali fare clic su **Macchine virtuali di Azure**.

    ![Riquadro dei processi di backup](./media/backup-azure-vms-first-look-arm/open-backup-jobs.png)

    Si apre il pannello dei processi di backup.

4. Nel pannello **Processi di backup** è possibile visualizzare lo stato di tutti i processi.

    ![Riquadro dei processi di backup](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view.png)

    >[AZURE.NOTE] Come parte dell'operazione di backup, il servizio Backup di Azure esegue un comando nell'estensione per il backup in ogni macchina virtuale, in modo da scaricare tutte le scritture e creare uno snapshot coerente.

    Al termine del processo di backup, lo stato è *Completato*.


## Risoluzione dei problemi
In caso di problemi durante il backup della macchina virtuale, vedere l'[articolo sulla risoluzione dei problemi delle macchine virtuali](backup-azure-vms-troubleshoot.md).

## Passaggi successivi

Adesso che la macchina virtuale è protetta, vedere gli articoli seguenti per altre attività di gestione e di ripristino che si possono eseguire con le macchine virtuali.

- [Gestire e monitorare il backup delle macchine virtuali di Azure](backup-azure-manage-vms.md)
- [Ripristino di macchine virtuali](backup-azure-arm-restore-vms.md)

<!---HONumber=AcomDC_0803_2016-->