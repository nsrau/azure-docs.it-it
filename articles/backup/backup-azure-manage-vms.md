
<properties
	pageTitle="Backup di Azure: Gestire macchine virtuali"
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

1. Per visualizzare e gestire le impostazioni di backup per una macchina virtuale, fare clic sulla scheda **Elementi protetti**.

  - Fare clic sul nome di un elemento protetto per visualizzare la scheda **Dettagli backup** contenente le informazioni sull'ultimo backup.

        ![Virtual machine backup](./media/backup-azure-manage-vms/backup-vmdetails.png)

2. Per visualizzare e gestire le impostazioni dei criteri di backup per una macchina virtuale fare clic sulla scheda **Criteri**.

  - Nella scheda **Criteri di backup** vengono visualizzati i criteri esistenti. È possibile modificare i criteri in base alle esigenze. Se è necessario creare un nuovo criterio, fare clic su **Crea** nella pagina **Criteri**. Si noti che un criterio può essere rimosso solo se non ha alcuna macchina virtuale associata.

        ![Virtual machine policy](./media/backup-azure-manage-vms/backup-vmpolicy.png)

3. È possibile ottenere altre informazioni sulle azioni o sullo stato di una macchina virtuale nella pagina**Processi**. Fare clic su un processo nell'elenco per ottenere informazioni dettagliate oppure filtrare i processi per una macchina virtuale specifica.

    ![Processi](./media/backup-azure-manage-vms/backup-job.png)

## Backup su richiesta di una macchina virtuale
È possibile eseguire il backup su richiesta di una macchina virtuale dopo averla configurata per la protezione. Se il backup iniziale è in sospeso per la macchina virtuale, il backup su richiesta creerà una copia completa della macchina virtuale nell'insieme di credenziali di backup di Azure. Se il primo backup viene completato, il backup su richiesta invierà solo le modifiche rispetto al backup precedente all'insieme di credenziali di backup di Azure.

Per eseguire un backup su richiesta di una macchina virtuale:

1. Passare alla pagina **Elementi protetti** e selezionare **Macchina virtuale di Azure** come **Tipo**, se non è già selezionata, quindi fare clic su **Seleziona**.

    ![Tipo macchina virtuale](./media/backup-azure-manage-vms/vm-type.png)

2. Selezionare la macchina virtuale su cui si vuole eseguire un backup su richiesta e fare clic sul pulsante **Esegui backup ora** nella parte inferiore della pagina.

    ![Esegui backup](./media/backup-azure-manage-vms/backup-now.png)

    Verrà creato un processo di backup nella macchina virtuale selezionata. L'intervallo di conservazione del punto di ripristino creato tramite questo processo sarà uguale a quello specificato nei criteri associati alla macchina virtuale.

    ![Creazione del processo di backup](./media/backup-azure-manage-vms/creating-job.png)

    >[AZURE.NOTE]Per visualizzare i criteri associati a una macchina virtuale, eseguire il drill-down nella macchina virtuale nella pagina **Elementi protetti** e passare alla scheda dei criteri di backup.

3. Dopo la creazione del processo, è possibile fare clic sul pulsante **Visualizza processo** sulla barra degli strumenti per visualizzare il processo corrispondente nella pagina dei processi.

    ![Processo di backup creato](./media/backup-azure-manage-vms/created-job.png)

4. Dopo il completamento corretto del processo, verrà creato un punto di ripristino che potrà essere usato per ripristinare la macchina virtuale. Ciò incrementerà anche di 1 il valore della colonna del punto di ripristino nella pagina **Elementi protetti**.

## Arrestare la protezione delle macchine virtuali
È possibile scegliere di arrestare i backup futuri di una macchina virtuale con le opzioni seguenti:

- Mantenere i dati di backup associati alla macchina virtuale nell'insieme di credenziali di Backup di Azure.
- Eliminare i dati di backup associati alla macchina virtuale.

Se è stata selezionata la prima opzione, sarà possibile usare i dati di backup per ripristinare la macchina virtuale. Per i dettagli relativi ai prezzi per queste macchine virtuali, fare clic [qui](http://azure.microsoft.com/pricing/details/backup/).

Per arrestare la protezione per una macchina virtuale:

1. Passare alla pagina **Elementi protetti** e selezionare **Macchina virtuale di Azure** come tipo di filtro, se non è già selezionata, quindi fare clic su **Seleziona**.

    ![Tipo macchina virtuale](./media/backup-azure-manage-vms/vm-type.png)

2. Selezionare la macchina virtuale e fare clic su **Arresta protezione** nella parte inferiore della pagina.

    ![Arresta protezione](./media/backup-azure-manage-vms/stop-protection.png)

3. Per impostazione predefinita, Backup di Azure non elimina i dati di backup associati alla macchina virtuale.

    ![Conferma arresto protezione](./media/backup-azure-manage-vms/confirm-stop-protection.png)

    Per eliminare i dati di backup, selezionare la casella di controllo.

    ![Casella di controllo](./media/backup-azure-manage-vms/checkbox.png)

    Selezionare un motivo per l'arresto del backup. Benché ciò sia facoltativo, specificando un motivo si consentirà a Backup di Azure di valutare i commenti e i suggerimenti e di assegnare priorità idonee agli scenari dei clienti.

4. Fare clic su **Invia** per inviare il processo **Arresta protezione**. Fare clic su **Visualizza processo** per visualizzare il processo corrispondente nella pagina **Processi**.

    ![Arresta protezione](./media/backup-azure-manage-vms/stop-protect-success.png)

    Se non è stata selezionata l'opzione **Elimina i dati di backup associati** durante la procedura guidata **Arresta protezione**, dopo il completamento del processo lo stato della protezione verrà cambiato in **Protezione arrestata**. I dati rimangono in Backup di Azure finché non vengono eliminati esplicitamente. È comunque possibile eliminare i dati selezionando la macchina virtuale nella pagina **Elementi protetti** e facendo clic su **Elimina**.

    ![Protezione arrestata](./media/backup-azure-manage-vms/protection-stopped-status.png)

    Se è stata selezionata l'opzione **Elimina i dati di backup associati**, la macchina virtuale non sarà inclusa nella pagina **Elementi protetti**.

## Riattivare la protezione della macchina virtuale
Se non è stata selezionata l'opzione **Elimina i dati di backup associati** in **Arresta protezione**, sarà possibile riattivare la protezione della macchina virtuale eseguendo una procedura analoga al backup di macchine virtuali registrate. Dopo la protezione, i dati di backup di questa macchina virtuale verranno conservati prima dell'arresto della protezione e verranno creati punti di ripristino dopo la riattivazione della protezione.

Dopo la riattivazione della protezione, lo stato di protezione della macchina virtuale verrà cambiato in **Protetto** se sono presenti punti di ripristino prima di **Arresta protezione**.

  ![Protezione VM riattivata](./media/backup-azure-manage-vms/reprotected-status.png)

>[AZURE.NOTE]Quando si riattiva la protezione della macchina virtuale, è possibile scegliere un criterio diverso rispetto ai criteri con cui la macchina virtuale è stata protetta inizialmente.

## Annullare la registrazione di macchine virtuali

Per rimuovere la macchina virtuale dall'insieme di credenziali per il backup:

1. Fare clic sul pulsante **ANNULLA REGISTRAZIONE** nella parte inferiore della pagina.

    ![Disabilita protezione](./media/backup-azure-manage-vms/unregister-button.png)

    Verrà visualizzata una notifica di tipo avviso popup nella parte inferiore della schermata di richiesta di conferma. Fare clic su **Sì** per continuare.

    ![Disabilita protezione](./media/backup-azure-manage-vms/confirm-unregister.png)

## Eliminare i dati di backup
È possibile eliminare i dati di backup associati a una macchina virtuale:

- Durante il processo Arresta protezione
- Dopo il completamento del processo Arresta protezione in una macchina virtuale

Per eliminare i dati di backup in una macchina virtuale con stato "Protezione arrestata" dopo il completamento corretto del processo **Arresta backup**:

1. Passare alla pagina **Elementi protetti** e selezionare **Macchina virtuale di Azure** come tipo, quindi fare clic su **Seleziona**.

    ![Tipo macchina virtuale](./media/backup-azure-manage-vms/vm-type.png)

2. Selezionare la macchina virtuale. Lo stato della macchina virtuale sarà **Protezione arrestata**.

    ![Protezione arrestata](./media/backup-azure-manage-vms/protection-stopped-b.png)

3. Fare clic sul pulsante **ELIMINA** nella parte inferiore della pagina.

    ![Elimina backup](./media/backup-azure-manage-vms/delete-backup.png)

4. Nella procedura guidata **Elimina dati di backup** selezionare un motivo per l'eliminazione dei dati di backup (opzione consigliata) e fare clic su **Invia**.

    ![Elimina dati di backup](./media/backup-azure-manage-vms/delete-backup-data.png)

5. Verrà creato un processo per l'eliminazione dei dati di backup di una macchina virtuale selezionata. Fare clic su **Visualizza processo** per visualizzare il processo corrispondente nella pagina Processi.

    ![Eliminazione dei dati completata](./media/backup-azure-manage-vms/delete-data-success.png)

    Dopo il completamento del processo, la voce corrispondente alla macchina virtuale verrà rimossa dalla pagina **Elementi protetti**.


###Dashboard

Nella pagina **Dashboard** è possibile esaminare le informazioni sulle macchine virtuali di Azure, le relative risorse di archiviazione e i processi associati nelle ultime 24 ore. È possibile visualizzare lo stato del backup ed eventuali errori di backup associati.

  ![Dashboard](./media/backup-azure-manage-vms/dashboard-protectedvms.png)
 

<!---HONumber=62-->