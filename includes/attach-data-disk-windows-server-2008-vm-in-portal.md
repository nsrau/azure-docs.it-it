Per collegare un disco dati, attenersi alla procedura seguente:

1.  Nel [portale di gestione di Azure][1] fare clic su **Virtual Machines** quindi selezionare la macchina virtuale appena creata (**testwinvm**).

2.  Sulla barra dei comandi fare clic su **Attach** e quindi su **Attach Empty Disk**.
    
    Verrà visualizzata la finestra di dialogo **Attach empty disk to virtual machine**.

3.  Le impostazioni **Virtual Machine Name**, **Storage Location** e **File Name** sono definite automaticamente. È sufficiente immettere la dimensione desiderata per il disco. Digitare **5** nel campo **Size**.
    
    ![Attach Empty
    Disk](./media/attach-data-disk-windows-server-2008-vm-in-portal/AttachDataDiskWinVM2.png)
    
    **Nota:** tutti i dischi vengono creati da un file VHD in Archiviazione di Azure. È possibile specificare un nome per il file VHD aggiunto alla risorsa di archiviazione, tuttavia il nome del disco viene generato automaticamente in Azure.

4.  Fare clic sul segno di spunta per collegare il disco dati alla macchina virtuale.

5.  Fare clic sul nome della macchina virtuale da visualizzare nel dashboard. In questo modo è possibile verificare che il disco dati sia stato collegato correttamente alla macchina virtuale.
    
    A questo punto la macchina virtuale è collegata a due dischi. Il disco collegato verrà visualizzato nella tabella Disks.
    
    ![Attach Empty
    Disk](./media/attach-data-disk-windows-server-2008-vm-in-portal/AttachDataDiskWinVM3.png)
    
    Quando viene collegato alla macchina virtuale, il disco dati è offline e non inizializzato. Prima di poter archiviare i dati, è necessario accedere alla macchina virtuale e inizializzare il disco.

## Connettersi alla macchina virtuale tramite Desktop remoto e completare la configurazione

1.  Dopo avere effettuato il provisioning della macchina virtuale, fare clic su **Virtual Machines** nel portale di gestione quindi fare clic sulla nuova macchina virtuale. Verranno visualizzate le informazioni sulla macchina virtuale.

2.  Nella parte inferiore della pagina fare clic su **Connect**. Aprire il file .rpd utilizzando il programma Desktop remoto Windows (*%windir%\\system32\\mstsc.exe*).

3.  Nella finestra di dialogo **Protezione di Windows** immettere la password dell'account **Administrator**. (Potrebbe essere richiesta la verifica delle credenziali della macchina virtuale). In occasione del primo accesso alla macchina virtuale, potrebbe essere necessario completare diversi processi, come la configurazione del desktop, l'aggiornamento di Windows e il completamento delle attività di configurazione iniziali di Windows. Dopo avere eseguito la connessione alla macchina virtuale con Desktop remoto Windows, la macchina virtuale funziona come qualsiasi altro computer.

4.  Dopo aver eseguito l'accesso alla macchina virtuale, aprire **Server Manager**. Nel riquadro sinistro espandere **Storage**, quindi fare clic su **Disk Management**.
    
    ![Server
    Manager](./media/attach-data-disk-windows-server-2008-vm-in-portal/servermanager.png)

5.  Verrà visualizzata la finestra **Initalize Disk**. Fare clic su **OK**.
    
    ![Initialize
    Disk](./media/attach-data-disk-windows-server-2008-vm-in-portal/initializedisk0.png)

6.  Fare clic con il pulsante destro del mouse sull'area di allocazione spazio per il disco 2, scegliere **New Simple Volume**, quindi terminare la procedura guidata con i valori predefiniti.
    
    ![New Simple
    Volume](./media/attach-data-disk-windows-server-2008-vm-in-portal/initializediskvolume.png)
    
    Il disco sarà ora online e pronto per l'uso con una nuova lettera di unità.
    
    ![Initialize
    Success](./media/attach-data-disk-windows-server-2008-vm-in-portal/initializesuccess.png)



[1]: http://manage.windowsazure.com