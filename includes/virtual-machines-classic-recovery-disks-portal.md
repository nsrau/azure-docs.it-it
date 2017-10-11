Se nella macchina virtuale in Azure viene rilevato un errore di avvio o del disco, potrebbe essere necessario eseguire alcuni passaggi per la risoluzione dei problemi sul disco rigido virtuale stesso. Un esempio comune è un aggiornamento di un'applicazione non riuscito che impedisce il corretto avvio della VM. Questo articolo illustra come usare il portale di Azure per connettere il disco rigido virtuale a un'altra VM per risolvere eventuali errori e quindi ricreare la VM originale.

## <a name="recovery-process-overview"></a>Panoramica del processo di ripristino
I passaggi per la risoluzione dei problemi sono i seguenti:

1. Eliminare la macchina virtuale su cui si riscontrano i problemi, ma mantenere i dischi rigidi virtuali.
2. Collegare e montare il disco rigido virtuale in un'altra VM per risolvere i problemi riscontrati.
3. Connettersi alla macchina virtuale usata per la risoluzione dei problemi. Modificare i file o eseguire gli strumenti per risolvere gli errori nel disco rigido virtuale originale.
4. Smontare e scollegare il disco rigido virtuale dalla macchina virtuale usata per la risoluzione dei problemi.
5. Creare una VM usando il disco rigido virtuale originale.

## <a name="delete-the-original-vm"></a>Eliminare la VM originale
In Azure, i dischi rigidi virtuali e le macchine virtuali sono due risorse distinte. In un disco rigido virtuale sono archiviati il sistema operativo, le applicazioni e le configurazioni. La macchina virtuale è invece costituita da metadati che definiscono le dimensioni o il percorso e da risorse di riferimento, ad esempio un disco rigido virtuale o una scheda di interfaccia di rete virtuale. A ogni disco rigido virtuale viene assegnato un lease quando il disco viene collegato a una VM. È possibile collegare e scollegare i dischi dati anche quando la macchina virtuale è in esecuzione, mentre non è possibile scollegare il disco del sistema operativo, a meno che la risorsa di macchina non sia stata eliminata. Il lease continua ad associare il disco del sistema operativo a una macchina virtuale anche quando questa viene arrestata e deallocata.

Il primo passaggio per ripristinare la macchina virtuale consiste nell'eliminare la risorsa della macchina virtuale stessa. Anche se si elimina la macchina virtuale, i dischi rigidi virtuali restano nell'account di archiviazione. Dopo aver eliminato la macchina virtuale, è possibile collegare il disco rigido virtuale a un'altra macchina virtuale per diagnosticare e risolvere gli errori. 

1. Accedere al [portale di Azure](https://portal.azure.com). 
2. Nel menu a sinistra fare clic su **Macchine virtuali (classico)**.
3. Selezionare la macchina virtuale che presenta il problema, fare clic su **Dischi** e quindi identificare il nome del disco rigido virtuale. 
4. Selezionare il disco rigido virtuale del sistema operativo e verificare la **Posizione** per identificare l'account di archiviazione che contiene il disco rigido virtuale. Nell'esempio seguente la stringa che precede immediatamente ".blob.core.windows.net" è il nome dell'account di archiviazione.

    ```
    https://portalvhds73fmhrw5xkp43.blob.core.windows.net/vhds/SCCM2012-2015-08-28.vhd
    ```

    ![Immagine relativa alla posizione della VM](./media/virtual-machines-classic-recovery-disks-portal/vm-location.png)

5. Fare clic con il pulsante destro del mouse sulla macchina virtuale e quindi scegliere **Elimina**. Assicurarsi che i dischi non siano selezionati quando si elimina la VM.
6. Creare una nuova VM di ripristino. Questa macchina virtuale deve trovarsi nella stessa area e nello stesso gruppo di risorse (servizio cloud) della VM che presenta il problema.
7. Selezionare la macchina virtuale di ripristino e quindi selezionare **Dischi** > **Collega esistente**.
8. Per selezionare il disco rigido virtuale esistente, fare clic su **File VHD**:

    ![Cercare il disco rigido virtuale esistente](./media/virtual-machines-classic-recovery-disks-portal/select-vhd-location.png)

9. Selezionare l'account di archiviazione, quindi il contenitore del disco rigido virtuale e il disco rigido virtuale, quindi fare clic sul pulsante **Seleziona** per confermare la scelta.

    ![Selezionare il disco rigido virtuale esistente](./media/virtual-machines-classic-recovery-disks-portal/select-vhd.png)

10. Dopo aver selezionato il disco rigido virtuale, selezionare **OK** per collegare il disco rigido virtuale esistente.
11. Dopo alcuni secondi, il riquadro **Dischi** della macchina virtuale esistente mostrerà il disco rigido virtuale esistente collegato come disco dati:

    ![Disco rigido virtuale esistente collegato come disco dati](./media/virtual-machines-classic-recovery-disks-portal/attached-disk.png)

## <a name="fix-issues-on-the-original-virtual-hard-disk"></a>Risolvere i problemi del disco rigido virtuale originale
Dopo aver montato il disco rigido virtuale, eseguire tutte le operazioni di manutenzione e i passaggi necessari per la risoluzione dei problemi. Dopo avere risolto i problemi, continuare con la procedura seguente.

## <a name="unmount-and-detach-the-original-virtual-hard-disk"></a>Smontare e scollegare il disco rigido virtuale originale
Dopo aver risolto eventuali errori, smontare e scollegare il disco rigido virtuale esistente dalla macchina virtuale usata per la risoluzione dei problemi. Non è possibile usare il disco rigido virtuale insieme ad altre macchine virtuali finché non viene rilasciato il lease che collega il disco rigido virtuale alla macchina virtuale usata per la risoluzione dei problemi.  

1. Accedere al [portale di Azure](https://portal.azure.com). 
2. Nel menu a sinistra selezionare**Macchine virtuali (classico)**.
3. Individuare la VM di ripristino. Selezionare Dischi, fare clic con il pulsante destro del mouse sul disco e quindi scegliere **Scollega**.

## <a name="create-a-vm-from-the-original-hard-disk"></a>Creare una macchina virtuale dal disco rigido originale

Per creare una macchina virtuale dal disco rigido virtuale originale, usare il [portale di Azure](https://portal.azure.com).

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella parte in alto a sinistra del portale selezionare **Nuovo** > **Calcolo** > **Macchina virtuale** > **Da raccolta**.
3. Nella sezione **Scegli un'immagine** selezionare **Dischi personali** e quindi selezionare il disco rigido virtuale originale. Verificare le informazioni sulla posizione. Questa è l'area in cui deve essere distribuita la VM. Selezionare il pulsante Avanti.
4. Nella sezione **Configurazione macchina virtuale** digitare il nome della macchina virtuale e selezionare una dimensione per la VM.
