<properties services="virtual-machines" title="How to Log on to a Virtual Machine Running Windows Server" authors="cynthn" solutions="" manager="timlt" editor="tysonn" />

4. Facendo clic su **Connetti** si crea e si scarica un file Remote Desktop Protocol (file con estensione rdp). Fare clic su **Apri** per utilizzare questo file.

5. Nella finestra di Desktop remoto, fare clic su **Connetti** per continuare.

	![Screenshot di un avviso su un server di pubblicazione sconosciuto.](./media/virtual-machines-log-on-win-server/connectpublisher.png)

6. Nella finestra Protezione di Windows, digitare le credenziali dell'account della macchina virtuale, quindi fare clic su **OK**.

 	In genere, le credenziali corrispondono al nome utente e alla password dell'account locale specificati al momento della creazione della macchina virtuale. In questo caso, il dominio è il nome della macchina virtuale e viene immesso come *vmname*& #92;*nome utente*.
	
	Se la macchina virtuale appartiene a un dominio nell'organizzazione, verificare che il nome utente contenga il nome del dominio nel formato *Dominio*&#92;*Nome utente*. L'account deve anche appartenere al gruppo degli amministratori o deve possedere privilegi di accesso remoto alla VM.
	
	Se la macchina virtuale è un controller di dominio, digitare nome utente e password di un account amministratore di dominio per tale dominio.

7.	Fare clic su **Sì** per verificare l'identità della macchina virtuale e terminare la procedura di accesso.

	![Screenshot che visualizza un messaggio sulla verifica dell'identità della VM.](./media/virtual-machines-log-on-win-server/connectverify.png)

<!---HONumber=AcomDC_0420_2016-->