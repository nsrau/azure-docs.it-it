<properties writer="kathydav" editor="tysonn" manager="jeffreyg" />

**Nota**: in questo articolo viene creata una macchina virtuale non connessa a una rete virtuale. Se si vuole usare una rete virtuale in modo da connettersi alla macchina virtuale direttamente con un nome host o per configurare connessioni
cross-premise, usare il metodo **From Gallery** e specificare la rete virtuale quando si crea la macchina virtuale. Per altre informazioni sulle reti virtuali, vedere [Informazioni generali su Rete virtuale di Azure][Informazioni generali su Rete virtuale di Azure].

1.  Accedere al portale di gestione di Azure con il proprio account Azure.
2.  Nel portale di gestione fare clic su **+New** nella parte inferiore sinistra della pagina Web, scegliere **Virtual Machine** e quindi **From Gallery**.

    ![Creare una nuova macchina virtuale][Creare una nuova macchina virtuale]

3.  Selezionare un'immagine della macchina virtuale CentOS da **Platform Images**, quindi fare clic sulla freccia Next nella parte inferiore destra della pagina.

4.  Nella pagina **Configurazione macchina virtuale** specificare le informazioni seguenti:

    -   Specificare un **Nome macchina virtuale**, ad esempio "testlinuxvm".
    -   Specificare un nuovo **Nuovo nome utente**, ad esempio "newuser", che verrà aggiunto al file dell'elenco Sudoers.
    -   Nella casella **Nuova password** immettere una [password complessa][password complessa].
    -   Nella casella **Conferma password** ridigitare la password.
    -   Selezionare un valore appropriato per **Dimensione** nell'elenco a discesa.

    Fare clic sulla freccia Avanti per continuare.

5.  Nella pagina **Modalità macchina virtuale** specificare le informazioni seguenti:

    -   Selezionare **Macchina virtuale autonoma**.
    -   Nella casella **Nome DNS** immettere un indirizzo DNS valido. Ad esempio, "testlinuxvm".
    -   Nella casella **Account di archiviazione** selezionare **Usa un account di archiviazione generato automaticamente**.
    -   Nella casella **Regione/Gruppo di affinità/Rete virtuale** selezionare l'area in cui verrà ospitata l'immagine virtuale.

    Fare clic sulla freccia Avanti per continuare.

6.  Nella pagina **Opzioni macchina virtuale** selezionare **(nessuno)** nella casella **Set di disponibilità**.

    Fare clic sul segno di spunta per continuare.

7.  Attendere durante la preparazione della macchina virtuale in Azure.

## Configurare gli endpoint

Dopo la creazione della macchina virtuale, configurare gli endpoint in modo da consentire la connessione remota.

1.  Nel portale di gestione fare clic su **Macchine virtuali**, quindi sul nome della nuova macchina virtuale e infine su **Endpoint**.

2.  Fare clic su **Modifica endpoint** nella parte inferiore della pagina, quindi modificare l'endpoint SSH in modo che il valore per la relativa **Porta pubblica** sia 22.

## Connettersi alla macchina virtuale

Dopo il provisioning della macchina virtuale e la configurazione degli endpoint, è possibile connettersi tramite SSH o PuTTY.

### Connettersi tramite SSH

Se si usa Linux, connettersi alla macchina virtuale tramite SSH. Al prompt dei comandi eseguire:

    $ ssh newuser@testlinuxvm.cloudapp.net -o ServerAliveInterval=180

Immettere la password dell'utente.

### Connettersi tramite PuTTY

Se si usa un computer Windows, connettersi alla macchina virtuale tramite PuTTY, disponibile nell'apposita [pagina di download][pagina di download].

1.  Scaricare e salvare il file **putty.exe** in una directory del computer. Aprire un prompt dei comandi, passare a questa cartella ed eseguire **putty.exe**.

2.  Immettere "testlinuxvm.cloudapp.net" come **Nome host** e "22" per la **Porta**.
    ![PuTTY Screen][PuTTY Screen]

## Aggiornare la macchina virtuale (facoltativo)

Dopo la connessione alla macchina virtuale, è possibile installare gli aggiornamenti, se lo si desidera. Eseguire:

    $ sudo yum update

Immettere di nuovo la password. Attendere il completamento dell'installazione dell'aggiornamento.

  [Informazioni generali su Rete virtuale di Azure]: http://go.microsoft.com/fwlink/p/?LinkID=294063
  [Creare una nuova macchina virtuale]: ./media/create-and-configure-centos-vm-in-portal/CreateVM.png
  [password complessa]: http://msdn.microsoft.com/it-it/library/ms161962.aspx
  [pagina di download]: http://www.puttyssh.org/download.html
  [PuTTY Screen]: ./media/create-and-configure-centos-vm-in-portal/putty.png
