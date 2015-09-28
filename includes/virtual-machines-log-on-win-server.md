<properties services="virtual-machines" title="How to Log on to a Virtual Machine Running Windows Server" authors="KBDAzure" solutions="" manager="timlt" editor="tysonn" />

4. Facendo clic su **Connetti** si crea e si scarica un file Remote Desktop Protocol (file con estensione rdp). Fare clic su **Apri** per utilizzare questo file.

5. Nella finestra di Desktop remoto, fare clic su **Connetti** per continuare.

	![Procedere alla connessione](./media/virtual-machines-log-on-win-server/connectpublisher.png)

6. Nella finestra Protezione di Windows, digitare le credenziali dell'account amministrativo della macchina virtuale, quindi fare clic su **OK**.

 	>[AZURE.TIP]Nella maggior parte dei casi verranno usati nome utente e password specificati durante la creazione della macchina virtuale. Controllare il nome utente per essere certi che le informazioni del dominio siano corrette:
	>
	>- Se la macchina virtuale appartiene a un dominio nell'organizzazione, assicurarsi che il nome utente contenga il nome di tale dominio.
	>- Se la macchina virtuale non appartiene a un dominio, rimuovere le informazioni del dominio inserendo all'inizio della riga il carattere " o usare il nome della macchina come nome di dominio. Ad esempio, `\MyUserName` o `MyTestVM\MyUserName`.
	>- Se la macchina virtuale è un controller di dominio, digitare nome utente e password di un account amministratore di dominio per tale dominio.

7.	Fare clic su **Sì** per verificare l'identità della macchina virtuale e terminare la procedura di accesso.

	![Verificare l'identità della macchina virtuale](./media/virtual-machines-log-on-win-server/connectverify.png)

<!---HONumber=Sept15_HO3-->