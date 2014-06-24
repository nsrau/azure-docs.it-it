<properties  writer="kathydav" editor="tysonn" manager="jeffreyg" />

**Importante**: Se si desidera che una macchina virtuale utilizzi una
rete virtuale, è necessario assicurarsi di specificare la rete quando si
crea la macchina. È possibile configurare una macchina virtuale in modo
da aggiungerla a una rete virtuale solo quando viene creata. Per
ulteriori informazioni sulle reti virtuali, vedere [Informazioni
generali su Rete virtuale di Azure][1].

1.  Accedere al [portale di gestione di Azure][2] con il proprio account
    Azure.

2.  Nel portale di gestione fare clic su **+New** nella parte inferiore
    sinistra della pagina Web, scegliere **Virtual Machine** e quindi
    **From Gallery**.
    
    ![Creare una nuova macchina
    virtuale](./media/create-and-configure-opensuse-vm-in-portal/CreateVM.png)

3.  Selezionare un'immagine della macchina virtuale OpenSUSE da
    **Platform Images**, quindi fare clic sulla freccia Avanti nella
    parte inferiore destra della pagina.

4.  Nella pagina **Virtual machine configuration** specificare le
    informazioni seguenti:
    
    * Specificare un **Virtual Machine Name**, ad esempio "testlinuxvm".
    * Specificare un nuovo **New User Name**, ad esempio "newuser", che
      verrà aggiunto al file dell'elenco Sudoers.
    * Nella casella **New Password** immettere una [password
      complessa][3].
    * Reimmettere la password nella casella **Confirm Password**.
    * Selezionare un valore appropriato per **Size** nell'elenco a
      discesa.
    
    Fare clic sulla freccia Next per continuare.

5.  Nella pagina **Virtual machine mode** specificare le informazioni
    seguenti:
    * Selezionare **Standalone Virtual Machine**.
    * Nella casella **DNS Name** immettere un indirizzo DNS valido. Ad
      esempio, "testlinuxvm".
    * Nella casella **Region/Affinity Group/Virtual Network**
      selezionare l'area in cui verrà ospitata l'immagine virtuale.
    
    Fare clic sulla freccia Next per continuare.

6.  Nella pagina **Virtual machine options** selezionare **(none)**
    nella casella **Availability Set**. Fare clic sul segno di spunta
    per continuare.

7.  Attendere durante la preparazione della macchina virtuale in Azure.
## Configurare gli endpoint

Dopo la creazione della macchina virtuale, è necessario configurare gli
endpoint in modo da consentire la connessione remota.

1.  Nel portale di gestione fare clic su **Virtual Machines**, quindi
    sul nome della nuova macchina virtuale e infine su **Endpoints**.

2.  Fare clic su **Edit Endpoint** nella parte inferiore della pagina,
    quindi modificare l'endpoint SSH in modo che il valore per la
    relativa **Public Port** sia 22.
## Connettersi alla macchina virtuale

Dopo il provisioning della macchina virtuale e la configurazione degli
endpoint, è possibile connettersi tramite SSH o PuTTY.
### Connettersi tramite SSH

Se si utilizza un computer Linux, connettersi alla macchina virtuale
tramite PuTTY. Al prompt dei comandi eseguire:

    $ ssh newuser@testlinuxvm.cloudapp.net -o ServerAliveInterval=180

Immettere la password dell'utente.
### Connettersi tramite PuTTY

Se si utilizza un computer Windows, connettersi alla macchina virtuale
tramite PuTTY, disponibile nell'apposita pagina di [download][4].

1.  Scaricare e salvare il file **putty.exe** in una directory del
    computer. Aprire un prompt dei comandi, passare a questa cartella ed
    eseguire **putty.exe**.

2.  Immettere "testlinuxvm.cloudapp.net" come nome host**** e "22" per
    la porta****. ![Schermata di
    PuTTY](./media/create-and-configure-opensuse-vm-in-portal/putty.png)
## Aggiornare la macchina virtuale (facoltativo)

1.  Dopo la connessione alla macchina virtuale, è possibile installare
    aggiornamenti del sistema e patch, se lo si desidera. Eseguire:
    
    `$ sudo zypper update`

2.  Selezionare **Software** e quindi **Online Update**. Verrà
    visualizzato un elenco di aggiornamenti disponibili. Selezionare
    **Accept** per avviare l'installazione e applicare tutte le nuove
    patch, ad eccezione di quelle facoltative, attualmente disponibili
    per il sistema.

3.  Al termine dell'installazione fare clic su **Finish**. Il sistema è
    aggiornato.



[1]: http://go.microsoft.com/fwlink/p/?LinkID=294063
[2]: http://manage.windowsazure.com
[3]: http://msdn.microsoft.com/en-us/library/ms161962.aspx
[4]: http://www.puttyssh.org/download.html
