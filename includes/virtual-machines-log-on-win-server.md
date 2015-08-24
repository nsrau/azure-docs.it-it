
1. Accedere al [portale di Azure](http://manage.windowsazure.com), se questa operazione non è già stata eseguita.

2. Fare clic su **Macchine virtuali** e quindi selezionare la macchina virtuale appropriata.

3. Nella barra dei comandi fare clic su **Connect**.

	![Accesso alla macchina virtuale](./media/virtual-machines-log-on-win-server/connectwindows.png)

4. Fare clic su **Apri** per usare il file RDP (Remote Desktop Protocol) creato automaticamente per la macchina virtuale.

5. Fare clic su **Connetti** per continuare.

	![Procedere alla connessione](./media/virtual-machines-log-on-win-server/connectpublisher.png)

6. Digitare le credenziali dell'account amministrativo della macchina virtuale, quindi fare clic su **OK**.

 >[AZURE.TIP]Nella maggior parte dei casi verranno usati nome utente e password specificati durante la creazione della macchina virtuale. Controllare il nome utente per essere certi che le informazioni del dominio siano corrette:

>- Se la macchina virtuale appartiene a un dominio nell'organizzazione, assicurarsi che il nome utente contenga il nome di tale dominio.
- Se la macchina virtuale non appartiene a un dominio, rimuovere le informazioni del dominio inserendo all'inizio della riga il carattere " o usare il nome della macchina come nome di dominio. Ad esempio, `\MyUserName` o `MyTestVM\MyUserName`.
- Se la macchina virtuale è un controller di dominio, digitare nome utente e password di un account amministratore di dominio per tale dominio.

Fare clic su **Yes** per verificare l'identità della macchina virtuale.

![Verificare l'identità della macchina virtuale](./media/virtual-machines-log-on-win-server/connectverify.png)

È ora possibile usare la macchina virtuale in remoto.

<!---HONumber=August15_HO7-->