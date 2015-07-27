**Importante**: se si vuole che una macchina virtuale usi una rete virtuale, è necessario assicurarsi di specificare la rete quando si crea la macchina. È possibile configurare una macchina virtuale in modo da aggiungerla a una rete virtuale solo quando viene creata. Per altre informazioni sulle reti virtuali, vedere [Informazioni generali su Rete virtuale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).


1. Accedere al [portale di gestione di Azure][AzurePreviewPortal] con il proprio account Azure.

2. Nel portale di gestione fare clic su **+New** nella parte inferiore sinistra della pagina Web, scegliere **Virtual Machine** e quindi **From Gallery**.

	![Creare una nuova macchina virtuale][Image1]

3. Nel gruppo **SUSE** selezionare un'immagine di macchina virtuale OpenSUSE e quindi fare clic sulla freccia Avanti in basso a destra della pagina.


4. Nella prima pagina **Configurazione macchina virtuale** inserire o verificare le impostazioni:

	- Specificare un **Nome macchina virtuale**, ad esempio "testlinuxvm".
	- Verificare il valore di **Livello** e selezionare una **Dimensione**. Il livello determina le dimensioni che è possibile scegliere.
	- Specificare un **Nuovo nome utente**, ad esempio "newuser", che verrà aggiunto al file dell'elenco Sudoers.
	- Definire il tipo di **Autenticazione** da usare. Per linee guida generali relative alle password, vedere [Password complesse](http://msdn.microsoft.com/library/ms161962.aspx).


5. Nella pagina successiva **Configurazione macchina virtuale** inserire o verificare le impostazioni:
	- Usare l'impostazione predefinita **Crea un nuovo servizio cloud**.
	- Nella casella **Nome DNS** digitare un nome DNS valido da usare come parte dell'indirizzo, ad esempio "testlinuxvm".
	- Nella casella **Regione/Gruppo di affinità/Rete virtuale** selezionare l'area in cui verrà ospitata l'immagine virtuale.

6.	Fare clic sulla freccia Avanti per completare l'operazione, quindi attendere la preparazione e l'avvio della macchina virtuale in Azure.

##Connettersi alla macchina virtuale
Per connettersi alla macchina virtuale si userà SSH o PuTTY, a seconda del sistema operativo in esecuzione nel computer:

- Se si usa Linux, connettersi alla macchina virtuale tramite SSH. Al prompt dei comandi eseguire:

	`$ ssh newuser@testlinuxvm.cloudapp.net -o ServerAliveInterval=180`

	Digitare la password dell'utente.

- Se si usa un computer Windows, connettersi alla macchina virtuale tramite PuTTY. È possibile scaricare PuTTY dalla [pagina di download PuTTY][PuTTYDownload].

	Scaricare e salvare il file **putty.exe** in una directory del computer. Aprire un prompt dei comandi, passare a questa cartella ed eseguire **putty.exe**.

	Digitare il nome host, ad esempio "testlinuxvm.cloudapp.net", quindi digitare "22" nella casella **Port**.

	![Schermata di PuTTY][Image6]

##Aggiornare la macchina virtuale (facoltativo)
1. Dopo la connessione alla macchina virtuale, è possibile installare aggiornamenti di sistema e patch, se lo si desidera. Per eseguire l'aggiornamento, digitare:

	`$ sudo zypper update`

2. Selezionare **Software**, quindi **Online Update** per visualizzare l'elenco degli aggiornamenti disponibili. Selezionare **Accept** per avviare l'installazione e applicare tutte le nuove patch, ad eccezione di quelle facoltative, attualmente disponibili per il sistema.

3. Al termine dell'installazione fare clic su **Finish**. Il sistema è aggiornato.

[PuTTYDownload]: http://www.puttyssh.org/download.html
[AzurePreviewPortal]: http://manage.windowsazure.com

[Image1]: ./media/create-and-configure-opensuse-vm-in-portal/CreateVM.png

[Image6]: ./media/create-and-configure-opensuse-vm-in-portal/putty.png

<!---HONumber=July15_HO3-->