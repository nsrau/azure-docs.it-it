<properties services="virtual-machines" title="How to Log on to a Virtual Machine Running Windows Server" authors="cynthn" solutions="" manager="timlt" editor="tysonn" />

4. Facendo clic su **Connetti** si crea e si scarica un file Remote Desktop Protocol (file con estensione rdp). Fare clic su **Apri** per utilizzare questo file.

5. Nella finestra di Desktop remoto, fare clic su **Connetti** per continuare.

	![Procedere alla connessione](./media/virtual-machines-log-on-win-server/connectpublisher.png)

6. Nella finestra Protezione di Windows, digitare le credenziali dell'account della macchina virtuale, quindi fare clic su **OK**.

 	Nella maggior parte dei casi, le credenziali corrispondono al nome utente e alla password dell'account locale specificati al momento della creazione della macchina virtuale. In questo caso, il dominio è il nome della macchina virtuale e viene immesso come *vmname*& #92;*nome utente*.
	
	Se la macchina virtuale appartiene a un dominio nell'organizzazione, assicurarsi che il nome utente contenga il nome del dominio nel formato *Dominio*&#92;*Nome utente*. L'account dovrà inoltre appartenere al gruppo degli amministratori o devono essergli stati concessi privilegi di accesso remoto alla macchina virtuale.
	
	Se la macchina virtuale è un controller di dominio, digitare nome utente e password di un account amministratore di dominio per tale dominio.

7.	Fare clic su **Sì** per verificare l'identità della macchina virtuale e terminare la procedura di accesso.

	![Verificare l'identità della macchina virtuale](./media/virtual-machines-log-on-win-server/connectverify.png)

<!---HONumber=AcomDC_0128_2016-->