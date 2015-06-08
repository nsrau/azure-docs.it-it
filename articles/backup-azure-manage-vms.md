<properties
	pageTitle="Backup di Azure - gestione delle macchine virtuali"
	description="Informazioni su come gestire una macchina virtuale di Azure"
	services="backup"
	documentationCenter=""
	authors="jimpark"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/28/2015"
	ms.author="jimpark"/>

# Gestire le macchine virtuali


## Gestire le macchine virtuali protette

1. Per visualizzare e gestire le impostazioni di backup per una macchina virtuale fare clic sui **elementi protetti** scheda.

  - Fare clic sul nome di un elemento protetto per visualizzare il **Dettagli Backup** scheda per visualizzare informazioni sull'ultimo backup.

        ![Virtual machine backup](./media/backup-azure-manage-vms/backup-vmdetails.png)

2. Per visualizzare e gestire le impostazioni di criteri di backup per una macchina virtuale fare clic sui **criteri** scheda.

  - Il **criteri di Backup** scheda Mostra i criteri esistenti. È possibile modificare di conseguenza. Se è necessario creare un nuovo criterio scegliere **Crea** sul **criteri** pagina. Si noti che se si desidera rimuovere un criterio non dovrebbe avere tutte le macchine virtuali associate.

        ![Virtual machine policy](./media/backup-azure-manage-vms/backup-vmpolicy.png)

3. È possibile ottenere ulteriori informazioni sulle azioni o lo stato per una macchina virtuale **processi** pagina. Fare clic su un processo nell'elenco per ottenere ulteriori dettagli o filtrare i processi per una macchina virtuale specifica.

    ![Processi](./media/backup-azure-manage-vms/backup-job.png)

## Backup su richiesta di una macchina virtuale
È possibile eseguire backup di un oggetto virtuale una richiesta dopo che è configurato per la protezione. Se il backup iniziale è in sospeso per la macchina virtuale, backup su richiesta verrà creata una copia completa della macchina virtuale in Azure archivio di backup. Se viene completato il primo backup, backup su richiesta inviare solo le modifiche dal backup precedente all'archivio di backup di Azure.

Eseguire una richiesta backup di una macchina virtuale:

1. Passare a **elementi protetti** pagina e selezionare **macchina virtuale di Azure** come **tipo** (se non è già selezionata) e fare clic su **selezionare** pulsante.

    ![Tipo di macchina virtuale](./media/backup-azure-manage-vms/vm-type.png)

2. Selezionare la macchina virtuale in cui si desidera eseguire backup una richiesta e fare clic su **ora Backup** pulsante nella parte inferiore della pagina.

    ![Esegui backup](./media/backup-azure-manage-vms/backup-now.png)

    Verrà creato un processo di backup nella macchina virtuale selezionata. Mantenimento del punto di ripristino creato tramite questo processo sarà uguale a quella specificata nel criterio associato alla macchina virtuale.

    ![Creazione del processo di backup](./media/backup-azure-manage-vms/creating-job.png)

    >[AZURE.NOTE]Per visualizzare i criteri associati a una macchina virtuale, drill-down nella macchina virtuale nel **elementi protetti** pagina e passare alla scheda Criteri di backup.

3. Una volta creato il processo, è possibile fare clic su **Visualizza processo** pulsante nella barra degli strumenti popup per visualizzare il processo corrispondente nella pagina processi.

    ![Processo di backup creato](./media/backup-azure-manage-vms/created-job.png)

4. Dopo il completamento del processo, verrà creato un punto di ripristino che è possibile utilizzare per ripristinare la macchina virtuale. Anche questo viene incrementato il valore di colonna del punto di ripristino di 1 in **elementi protetti** pagina.

## Arresta la protezione di macchine virtuali
È possibile scegliere di interrompere i backup futuri di una macchina virtuale con le opzioni seguenti:

- Mantenere i dati di backup associati alla macchina virtuale nell'archivio di Backup di Azure
- Eliminare i backup dei dati associati alla macchina virtuale

Se è stata selezionata la prima opzione, è possibile utilizzare i dati di backup per ripristinare la macchina virtuale. Per una panoramica dei prezzi per tali macchine virtuali, fare clic su [qui](http://azure.microsoft.com/pricing/details/backup/).

Per arrestare la protezione per una macchina virtuale:

1. Passare a **elementi protetti** pagina e selezionare **macchina virtuale di Azure** come tipo di filtro (se non è già selezionata) e fare clic su **selezionare** pulsante.

    ![Tipo di macchina virtuale](./media/backup-azure-manage-vms/vm-type.png)

2. Selezionare la macchina virtuale e fare clic su **Arresta protezione dati** nella parte inferiore della pagina.

    ![Arrestare la protezione](./media/backup-azure-manage-vms/stop-protection.png)

3. Per impostazione predefinita, Azure Backup non elimina i dati di backup associati alla macchina virtuale.

    ![Conferma arresto protezione dati](./media/backup-azure-manage-vms/confirm-stop-protection.png)

    Se si desidera eliminare i dati di backup, selezionare la casella di controllo.

    ![Casella di controllo](./media/backup-azure-manage-vms/checkbox.png)

    Selezionare un motivo per l'interruzione del backup. Sebbene sia facoltativo, fornire un motivo consentirà di Azure Backup per i commenti e suggerimenti di lavoro e assegnare priorità gli scenari del cliente.

4. Fare clic su **Invia** pulsante consente di inviare il **arrestare la protezione** processo. Fare clic su **Visualizza processo** per visualizzare il processo nel corrispondente **processi** pagina.

    ![Arrestare la protezione](./media/backup-azure-manage-vms/stop-protect-success.png)

    Se non è stato selezionato **Elimina i dati di backup associati** opzione durante **Arresta protezione dati** procedura guidata, quindi registra il completamento del processo, modifiche di stato di protezione per **arrestata la protezione dati**.

    ![Arresto protezione dati](./media/backup-azure-manage-vms/protection-stopped-status.png)

    Se è stato selezionato il **Elimina i dati di backup associati** opzione, macchina virtuale non farà parte di **elementi protetti** pagina.

## Riproteggere la macchina virtuale
Se non è stato selezionato il **eliminare associati dati di backup** opzione **Arresta protezione dati**, è possibile proteggere nuovamente la macchina virtuale seguendo i passaggi simili al backup di macchine virtuali registrate. Una volta protetta, questa macchina virtuale sono mantenuti prima di arrestare la protezione dati di backup e i punti di ripristino creato dopo proteggere di nuovo.

Dopo aver proteggere di nuovo, lo stato di protezione della macchina virtuale verrà modificato in **Protected** se sono presenti punti di ripristino prima di **Arresta protezione dati**.

  ![VM reprotected](./media/backup-azure-manage-vms/reprotected-status.png)

>[AZURE.NOTE]Quando si protegge nuovamente la macchina virtuale, è possibile scegliere un criterio diverso rispetto ai criteri con cui è stato inizialmente proteggere macchina virtuale.

## Annullare la registrazione di macchine virtuali

Se si desidera rimuovere la macchina virtuale dall'insieme di credenziali di backup:

1. Fare clic sui **UNREGISTER** pulsante nella parte inferiore della pagina.

    ![Disabilitare la protezione](./media/backup-azure-manage-vms/unregister-button.png)

    Verrà visualizzata una notifica popup nella parte inferiore della schermata di richiesta di conferma. Fare clic su **Sì** per continuare.

    ![Disabilitare la protezione](./media/backup-azure-manage-vms/confirm-unregister.png)

## Eliminare i dati di Backup
È possibile eliminare i dati di backup associati a una macchina virtuale sia:

- Durante il processo di arresto protezione dati
- Arrestare la protezione dopo il processo viene completato in una macchina virtuale

Per eliminare i dati di backup in una macchina virtuale, ovvero il "arrestata la protezione dati" stato di registrare il completamento del **arrestare Backup** processo:

1. Passare a **elementi protetti** pagina e selezionare **macchina virtuale di Azure** come tipo e fare clic su **selezionare** pulsante.

    ![Tipo di macchina virtuale](./media/backup-azure-manage-vms/vm-type.png)

2. Selezionare la macchina virtuale. La macchina virtuale sarà in **arrestata la protezione dati** stato.

    ![Protezione arrestato](./media/backup-azure-manage-vms/protection-stopped-b.png)

3. Fare clic sui **eliminare** pulsante nella parte inferiore della pagina.

    ![Eliminazione del backup](./media/backup-azure-manage-vms/delete-backup.png)

4. Nel **eliminare dati di backup** procedura guidata, selezionare il motivo per l'eliminazione di dati di backup (scelta consigliati) e fare clic su **Invia**.

    ![Eliminare i dati di backup](./media/backup-azure-manage-vms/delete-backup-data.png)

5. Verrà creato un processo per eliminare i dati di backup della macchina virtuale selezionata. Fare clic su **Visualizza processo** per visualizzare il processo corrispondente nella pagina dei processi.

    ![Eliminazione dei dati completata](./media/backup-azure-manage-vms/delete-data-success.png)

    Una volta completato il processo, voce corrispondente alla macchina virtuale verrà rimossa dal **gli elementi protetti** pagina.


###Dashboard

Nel **Dashboard** pagina è possibile esaminare le informazioni su macchine virtuali di Azure, i relativi archivi e i processi associati nelle ultime 24 ore. È possibile visualizzare lo stato del backup e eventuali errori di backup associati.

  ![Dashboard](./media/backup-azure-manage-vms/dashboard-protectedvms.png)

<!---HONumber=GIT-SubDir-->