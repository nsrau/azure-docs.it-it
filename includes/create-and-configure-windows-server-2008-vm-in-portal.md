<properties  writer="kathydav" editor="tysonn" manager="jeffreyg" />

**Nota**: in questo articolo viene creata una macchina virtuale non connessa a una rete virtuale. Se si desidera utilizzare una rete virtuale in modo da connettersi alla macchina virtuale direttamente mediante nome host o per configurare connessioni cross-premise, utilizzare il metodo **From Gallery** e specificare la rete virtuale quando si crea la macchina virtuale. Per ulteriori informazioni sulle reti virtuali, vedere [Informazioni generali su Rete virtuale di Azure][1].

Per creare una macchina virtuale, attenersi alla procedura seguente:

1.  Accedere al [portale di gestione di Azure][2] con il proprio account Azure.

2.  Nel portale di gestione fare clic su **+New** nella parte inferiore sinistra della pagina Web, scegliere **Virtual Machine** e quindi **From Gallery**. ![Creare una nuova macchina virtuale](./media/create-and-configure-windows-server-2008-vm-in-portal/CreateWinVM.png)

3.  Selezionare un'immagine della macchina virtuale Windows Server 2008 R2 SP1, quindi fare clic sulla freccia Next nella parte inferiore destra della pagina.

4.  Nella pagina **Virtual machine configuration** specificare le informazioni seguenti:

* Specificare un **Nome macchina virtuale**, ad esempio "testwinvm".
* Nella casella **New User Name** immettere "Amministratore".
* Nella casella **New Password** immettere una [password complessa][3].
* Reimmettere la password nella casella **Confirm Password**.
* Selezionare un valore appropriato per **Size** nell'elenco a discesa.
  
  Fare clic sulla freccia Next per continuare.

1.  Nella pagina **Virtual machine mode** specificare le informazioni seguenti:

* Selezionare **Standalone Virtual Machine**.
* Nella casella **DNS Name** immettere un sottodominio valido nel formato **testwinvm.cloudapp.net**.
* Nella casella **Region/Affinity Group/Virtual Network** selezionare l'area in cui verrà ospitata l'immagine virtuale.
  
  Fare clic sulla freccia Next per continuare.

1.  Nella pagina **Virtual machine options** selezionare **(none)** nella casella **Availability Set**. Fare clic sul segno di spunta per continuare.

2.  Attendere durante la preparazione della macchina virtuale in Azure.



[1]: http://go.microsoft.com/fwlink/p/?LinkID=294063
[2]: http://manage.windowsazure.com
[3]: http://msdn.microsoft.com/it-it/library/ms161962.aspx