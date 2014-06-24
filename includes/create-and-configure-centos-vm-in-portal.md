<properties  writer="kathydav" editor="tysonn" manager="jeffreyg" />

**Nota**: in questo articolo viene creata una macchina virtuale non
connessa a una rete virtuale. Se si desidera utilizzare una rete
virtuale in modo da connettersi alla macchina virtuale direttamente
mediante nome host o per configurare connessioni cross-premise,
utilizzare il metodo **From Gallery** e specificare la rete virtuale
quando si crea la macchina virtuale. Per ulteriori informazioni sulle
reti virtuali, vedere [Informazioni generali su Rete virtuale di
Azure][1].

1.  Accedere al portale di gestione di Azure con il proprio account
    Azure.
2.  Nel portale di gestione fare clic su **+New** nella parte inferiore
    sinistra della pagina Web, scegliere **Virtual Machine** e quindi
    **From Gallery**.
    
    ![Creare una nuova macchina
    virtuale](./media/create-and-configure-centos-vm-in-portal/CreateVM.png)

3.  Selezionare un'immagine della macchina virtuale CentOS da
    **Platform Images**, quindi fare clic sulla freccia Next nella parte
    inferiore destra della pagina.

4.  Nella pagina **Virtual machine configuration** specificare le
    informazioni seguenti:
    * Specificare un **Virtual Machine Name**, ad esempio "testlinuxvm".
    * Specificare un nuovo **New User Name**, ad esempio "newuser", che
      verrà aggiunto al file dell'elenco Sudoers.
    * Nella casella **New Password** immettere una [password
      complessa][2].
    * Reimmettere la password nella casella **Confirm Password**.
    * Selezionare un valore appropriato per **Size** nell'elenco a
      discesa.
    
    Fare clic sulla freccia Next per continuare.

5.  Nella pagina **Virtual machine mode** specificare le informazioni
    seguenti:
    * Selezionare **Standalone Virtual Machine**.
    * Nella casella **DNS Name** immettere un indirizzo DNS valido. Ad
      esempio, "testlinuxvm".
    * Nella casella **Storage Account** selezionare **Use an
      automatically generated storage account**.
    * Nella casella **Region/Affinity Group/Virtual Network**
      selezionare l'area in cui verrà ospitata l'immagine virtuale.
    
    Fare clic sulla freccia Next per continuare.

6.  Nella pagina **Virtual machine options** selezionare **(none)**
    nella casella **Availability Set**.
    
    Fare clic sul segno di spunta per continuare.

7.  Attendere durante la preparazione della macchina virtuale in Azure.
## Configurare gli endpoint

Dopo la creazione della macchina virtuale, configurare gli endpoint in
modo da consentire la connessione remota.

1.  Nel portale di gestione fare clic su **Virtual Machines**, quindi
    sul nome della nuova macchina virtuale e infine su **Endpoints**.

2.  Fare clic su **Edit Endpoint** nella parte inferiore della pagina,
    quindi modificare l'endpoint SSH in modo che il valore per la
    relativa **Public Port** sia 22.
## Connettersi alla macchina virtuale

Dopo il provisioning della macchina virtuale e la configurazione degli
endpoint, è possibile connettersi tramite SSH o PuTTY.
### Connettersi tramite SSH

Se si utilizza Linux, connettersi alla macchina virtuale tramite SSH. Al
prompt dei comandi eseguire:

    $ ssh newuser@testlinuxvm.cloudapp.net -o ServerAliveInterval=180

Immettere la password dell'utente.
### Connettersi tramite PuTTY

Se si utilizza un computer Windows, connettersi alla macchina virtuale
tramite PuTTY, disponibile nell'apposita pagina di [download][3].

1.  Scaricare e salvare il file **putty.exe** in una directory del
    computer. Aprire un prompt dei comandi, passare a questa cartella ed
    eseguire **putty.exe**.

2.  Immettere "testlinuxvm.cloudapp.net" come nome host**** e "22" per
    la porta****. ![Schermata di
    PuTTY](./media/create-and-configure-centos-vm-in-portal/putty.png)
## Aggiornare la macchina virtuale (facoltativo)

Dopo la connessione alla macchina virtuale, è possibile installare gli
aggiornamenti, se lo si desidera. Eseguire:

    $ sudo yum update

Immettere di nuovo la password. Attendere il completamento
dell'installazione dell'aggiornamento.



[1]: http://go.microsoft.com/fwlink/p/?LinkID=294063
[2]: http://msdn.microsoft.com/en-us/library/ms161962.aspx
[3]: http://www.puttyssh.org/download.html
