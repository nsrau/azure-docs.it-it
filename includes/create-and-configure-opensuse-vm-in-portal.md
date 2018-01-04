1. Accedere al [portale di Azure](http://portal.azure.com).  
2. Fare clic su **New** > **calcolo** > **vedere tutti** e quindi digitare **SUSE**.
   
3. Selezionare un'immagine di macchina virtuale OpenSUSE e quindi fare clic sulla freccia per continuare.
5. Nella prima pagina di **configurazione della macchina virtuale** :
   
   * Specificare un **Nome macchina virtuale**, ad esempio "testlinuxvm". Il nome deve contenere un numero di caratteri compreso tra 3 e 15, può essere composto solo da lettere, numeri e trattini e deve iniziare con una lettera e terminare con una lettera o con un numero.
   * Verificare il valore di **Livello** e selezionare una **dimensione**. Il livello determina le dimensioni che è possibile scegliere. La dimensione influisce sul costo dell'uso e sulle opzioni di configurazione come il numero di dischi dati che è possibile collegare. Per informazioni dettagliate, vedere [Dimensioni delle macchine virtuali](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
   * Digitare un **nuovo nome utente** o accettare quello predefinito **azureuser**. Questo nome viene aggiunto al file dell'elenco Sudoers.
   * Definire il tipo di **Autenticazione** da usare. Per linee guida generali relative alle password, vedere [Password complesse](http://msdn.microsoft.com/library/ms161962.aspx).
6. Nella pagina di **configurazione della macchina virtuale** successiva:
   
   * Usare l'impostazione predefinita **Crea un nuovo servizio cloud**.
   * Nella casella **Nome DNS** digitare un nome DNS univoco da usare come parte dell'indirizzo, ad esempio "testlinuxvm".
   * Nella casella **Regione/Gruppo di affinità/Rete virtuale** selezionare l'area in cui verrà ospitata l'immagine virtuale.
   * In **Endpoint**mantenere l'endpoint SSH. A questo punto, è possibile aggiungere altri endpoint oppure aggiungerli, modificarli o eliminarli dopo aver creato la macchina virtuale.
     
     > [!NOTE]
     > Se si desidera che una macchina virtuale usi una rete virtuale, è **necessario** specificare la rete quando si crea la macchina. Non è possibile aggiungere una macchina virtuale a una rete virtuale dopo averla creata. Per altre informazioni, vedere [Panoramica di Rete virtuale](../articles/virtual-network/virtual-networks-overview.md).
     > 
     > 
7. Nell'ultima pagina di **configurazione della macchina virtuale** mantenere le impostazioni predefinite e quindi fare clic sul segno di spunta per terminare.

Nel portale la nuova macchina virtuale viene elencata in **Macchine virtuali**. Se lo stato indica l'esecuzione del **provisioning**, è in corso la configurazione della macchina virtuale. Quando lo stato indica che la macchina virtuale è **in esecuzione**, è possibile passare al passaggio successivo.

## <a name="connect-to-the-virtual-machine"></a>Connettersi alla macchina virtuale
Per connettersi alla macchina virtuale si userà SSH o PuTTY, a seconda del sistema operativo del computer da cui si esegue la connessione:

* Da un computer che esegue Linux, usare SSH. Al prompt dei comandi digitare:
  
    `$ ssh newuser@testlinuxvm.cloudapp.net -o ServerAliveInterval=180`
  
    Digitare la password dell'utente.
* Da un computer che esegue Windows, usare PuTTY. Se non è installato, scaricarlo dalla [pagina di download di PuTTY][PuTTYDownload].
  
    Salvare il file **putty.exe** in una directory del computer. Aprire un prompt dei comandi, passare a questa cartella ed eseguire **putty.exe**.
  
    Digitare il nome host, ad esempio "testlinuxvm.cloudapp.net", quindi digitare "22" nella casella **Port**.
  
    ![Schermata di PuTTY][Image6]  

## <a name="update-the-virtual-machine-optional"></a>Aggiornare la macchina virtuale (facoltativo)
1. Dopo la connessione alla macchina virtuale, è possibile installare aggiornamenti di sistema e patch. Per eseguire l'aggiornamento, digitare:
   
    `$ sudo zypper update`
2. Selezionare **Software** e quindi **Online Update** per visualizzare l'elenco degli aggiornamenti disponibili. Selezionare **Accept** per avviare l'installazione e applicare tutte le nuove patch, ad eccezione di quelle facoltative, attualmente disponibili per il sistema.
3. Al termine dell'installazione fare clic su **Finish**.  Il sistema è aggiornato.

[PuTTYDownload]: http://www.puttyssh.org/download.html

[Image6]: ./media/create-and-configure-opensuse-vm-in-portal/putty.png
