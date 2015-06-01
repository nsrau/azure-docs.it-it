<properties services="virtual-machines" title="Come accedere a una macchina virtuale che esegue Windows Server" authors="KBDAzure" solutions="" manager="timlt" editor="tysonn" />

>[AZURE.NOTE] Se è necessario reimpostare il nome utente o la password, oppure abilitare RDP nella macchina virtuale, è possibile usare l'estensione [VMAccess](http://go.microsoft.com/fwlink/p/?LinkId=396856). Per verificare i requisiti e ottenere suggerimenti per la risoluzione dei problemi, vedere [Connettersi a una macchina virtuale di Azure con RDP o SSH](http://go.microsoft.com/fwlink/p/?LinkId=398294).

1. Se necessario, accedere al [portale di gestione](http://manage.windowsazure.com) di Azure.

2. Fare clic su **Macchine virtuali** e quindi selezionare la macchina virtuale appropriata.

3. Sulla barra dei comandi fare clic su **Connetti**.

	![Log on to the virtual machine](./media/virtual-machines-log-on-win-server/connectwindows.png)

4. Fare clic su **Apri** per usare il file RDP (Remote Desktop Protocol) creato automaticamente per la macchina virtuale.
	
5. Fare clic su **Connetti** per continuare.

	![Continue with connecting](./media/virtual-machines-log-on-win-server/connectpublisher.png)

6. Digitare le credenziali dell'account amministrativo della macchina virtuale, quindi fare clic su **OK**. 

 >[AZURE.TIP] Nella maggior parte dei casi verranno usati nome utente e password specificati durante la creazione della macchina virtuale. Controllare il nome utente per essere certi che le informazioni del dominio siano corrette:

>- Se la macchina virtuale appartiene a un dominio nell'organizzazione, assicurarsi che il nome utente contenga il nome di tale dominio.
- Se la macchina virtuale non appartiene a un dominio, rimuovere le informazioni del dominio inserendo all'inizio della riga il carattere "" o usare il nome della macchina come nome di dominio. ad esempio `\MyUserName` o `MyTestVM\MyUserName`. 
- Se la macchina virtuale è un controller di dominio, digitare nome utente e password di un account amministratore di dominio per tale dominio.

Fare clic su **Sì** per verificare l'identità della macchina virtuale.

![Verify the identity of the machine](./media/virtual-machines-log-on-win-server/connectverify.png)

È ora possibile usare la macchina virtuale in remoto.


<!--HONumber=47-->
