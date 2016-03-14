## Creare un insieme di credenziali di backup per una macchina virtuale

Un insieme di credenziali di backup è un'entità che archivia tutti i backup e i punti di ripristino che sono stati creati nel corso del tempo. L'insieme di credenziali di backup contiene anche i criteri di backup che verranno applicati alle macchine virtuali di cui viene eseguito il backup.

Questa figura mostra le relazioni tra le diverse entità di Backup di Azure: ![Entità e relazioni di Backup di Azure](./media/backup-create-vault-for-vms/vault-policy-vm.png)

Per creare un insieme di credenziali per il backup:

1. Accedere al [portale di Azure](http://manage.windowsazure.com/).

2. Nel portale di Azure fare clic su **Nuovo** > **Integrazione ibrida** > **Backup**. Quando si fa clic su **Backup**, si passa automaticamente al portale classico (visualizzato dopo la nota).

    ![Portale Ibiza](./media/backup-create-vault-for-vms/Ibiza-portal-backup01.png)

    >[AZURE.NOTE] Se la sottoscrizione è stata usata l'ultima volta nel portale classico, può venire aperta nel portale classico. In questo caso, per creare un insieme di credenziali di backup, fare clic su **Nuovo** > **Servizi dati** > **Servizi di ripristino** > **Insieme di credenziali di backup** > **Creazione rapida** (vedere la figura seguente).

    ![Creare un insieme di credenziali per il backup](./media/backup-create-vault-for-vms/backup_vaultcreate.png)

3. In **Nome** immettere un nome descrittivo per identificare l'insieme di credenziali. Il nome deve essere univoco per la sottoscrizione di Azure. Digitare un nome che contenga tra i 2 e i 50 caratteri. Deve iniziare con una lettera e può contenere solo lettere, numeri e trattini.

4. In **Region** selezionare l'area geografica per l'insieme di credenziali. L'insieme di credenziali deve trovarsi nella stessa area geografica delle macchine virtuali che si vuole proteggere. Se si hanno macchine virtuali in più aree, è necessario creare un insieme di credenziali di backup in ogni area. Per archiviare i dati di backup, non è necessario specificare account di archiviazione perché l'insieme di credenziali per il backup e il servizio Backup di Azure gestiscono questa operazione in modo automatico.

5. In **Sottoscrizione** selezionare la sottoscrizione che si vuole associare all'insieme di credenziali di backup. Sono presenti scelte multiple solo se l'account dell'organizzazione è associato a più sottoscrizioni di Azure.

5. Fare clic su **Crea insieme di credenziali**. La creazione dell'insieme di credenziali per il backup può richiedere alcuni minuti. Monitorare le notifiche di stato nella parte inferiore del portale.

    ![Creare una notifica di tipo avviso popup dell'insieme di credenziali](./media/backup-create-vault-for-vms/creating-vault.png)

6. La creazione corretta dell'insieme di credenziali verrà confermata da un messaggio. L'insieme verrà elencato come **Attivo** nella pagina **Servizi di ripristino**. Subito dopo la creazione dell'insieme di credenziali, assicurarsi di scegliere l'opzione di ridondanza dell'archiviazione corretta. Leggere l'articolo relativo all'[impostazione dell'opzione di ridondanza nell'insieme di credenziali per il backup](backup-configure-vault.md#azure-backup---storage-redundancy-options).

    ![Elenco degli insiemi di credenziali per il backup](./media/backup-create-vault-for-vms/backup_vaultslist.png)

7. Fare clic sull'insieme di credenziali per il backup per visualizzare la pagina **Avvio rapido** in cui sono riportate le istruzioni per il backup delle macchine virtuali di Azure.

    ![Istruzioni per il backup delle macchine virtuali nella pagina Dashboard](./media/backup-create-vault-for-vms/vmbackup-instructions.png)

<!---HONumber=AcomDC_0302_2016-->