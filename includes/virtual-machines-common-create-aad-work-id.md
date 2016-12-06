
<br>

> [!NOTE]
> Se si sono ricevuti un nome utente e una password da un amministratore, è probabile che si abbia già un ID aziendale o dell’istituto d’istruzione (a volte detto anche *ID dell’organizzazione*). In tal caso, è possibile iniziare immediatamente a usare l'account di Azure per accedere alle risorse di Azure che ne richiedono uno. Se non è possibile usare tali risorse, potrebbe essere necessario tornare a questo articolo. Per altre informazioni, vedere [Account che si possono usare per l'accesso](https://msdn.microsoft.com/library/azure/dn629581.aspx#BKMK_SignInAccounts) e [Relazione tra una sottoscrizione di Azure e Azure AD](https://msdn.microsoft.com/library/azure/dn629581.aspx#BKMK_SubRelationToDir).
> 
> 

I passaggi sono semplici. È necessario individuare l'identità attiva nel portale di Azure classico, scoprire il dominio di Azure Active Directory predefinito e aggiungere un nuovo utente come co-amministratore di Azure.

## <a name="locate-your-default-directory-in-the-azure-classic-portal"></a>Individuare la directory predefinita nel portale di Azure classico
Per iniziare, accedere al [portale di Azure classico](https://manage.windowsazure.com) con l'identità dell'account Microsoft personale. Dopo l'accesso, scorrere verso il basso nel pannello blu sul lato sinistro e fare clic su **ACTIVE DIRECTORY**.

![Azure Active Directory](./media/virtual-machines-common-create-aad-work-id/azureactivedirectorywidget.png)

Occorre innanzitutto trovare alcuni informazioni sulla propria identità in Azure. Nel riquadro principale dovrebbero essere visualizzate informazioni simili alle seguenti, che mostrano la presenza di una directory predefinita.

![](./media/virtual-machines-common-create-aad-work-id/defaultaadlisting.png)

È possibile cercare altre informazioni sulla directory facendo clic sulla riga della directory predefinita per visualizzarne le proprietà.  

![](./media/virtual-machines-common-create-aad-work-id/defaultdirectorypage.png)

Per visualizzare il nome di dominio predefinito, fare clic su **DOMINI**.

![](./media/virtual-machines-common-create-aad-work-id/domainclicktoseeyourdefaultdomain.png)

Da qui si dovrebbe vedere che, al momento della creazione dell'account Azure, Azure Active Directory ha creato un dominio personale predefinito, che è un hash (un numero generato da una stringa di testo) dell'ID personale usato come sottodominio di onmicrosoft.com. Questo è il dominio a cui verrà ora aggiunto un nuovo utente.

## <a name="creating-a-new-user-in-the-default-domain"></a>Creazione di un nuovo utente nel dominio predefinito
Fare clic su **UTENTI** e cercare il proprio account personale. Nella colonna **ORIGINATO DA** dovrebbe essere evidente che si tratta di un **account Microsoft**. Si desidera creare un utente nel dominio predefinito .onmicrosoft.com di Azure Active Directory.

![](./media/virtual-machines-common-create-aad-work-id/defaultdirectoryuserslisting.png)

Nei passaggi seguenti si seguiranno [queste istruzioni](https://technet.microsoft.com/library/hh967632.aspx#BKMK_1) , ma con un esempio specifico.

Nella parte inferiore della pagina fare clic su **+AGGIUNGI UTENTE**. Nella pagina visualizzata digitare il nuovo nome utente e selezionare **Nuovo utente nell'organizzazione** per **Tipo di utente**. In questo esempio il nuovo nome utente è `ahmet`. Selezionare il dominio predefinito individuato in precedenza come dominio per l'indirizzo di posta elettronica di Ahmet. Al termine, fare clic sulla freccia Avanti.

![](./media/virtual-machines-common-create-aad-work-id/addingauserwithdirectorydropdown.png)

Aggiungere altri dettagli per Ahmet, ma assicurarsi di selezionare il valore appropriato per **RUOLO**. Una soluzione semplice consiste nell'usare **Amministratore globale** per assicurarsi che tutto funzioni, ma è consigliabile usare un ruolo di livello inferiore, se possibile. Questo esempio usa il ruolo **Utente**. Per altre informazioni, vedere le [autorizzazioni degli amministratori in base al ruolo](https://msdn.microsoft.com/library/azure/dn468213.aspx#BKMK_1). Non abilitare l'autenticazione a più fattori, a meno che non si desideri usarla per ogni log nell'operazione. Al termine, fare clic sulla freccia Avanti.

![](./media/virtual-machines-common-create-aad-work-id/userprofileuseradmin.png)

Fare clic sul pulsante **Crea** per generare e visualizzare una password temporanea per Ahmet.

![](./media/virtual-machines-common-create-aad-work-id/gettemporarypasswordforuser.png)

Copiare l'indirizzo di posta elettronica di nome utente o usare **INVIO PASSWORD PER POSTA ELETTRONICA**. Le informazioni saranno necessarie fra breve per l'accesso.

![](./media/virtual-machines-common-create-aad-work-id/receivedtemporarypassworddialog.png)

Verrà ora visualizzato il nuovo utente, **Ahmet Developer**, originato da Azure Active Directory. Si è creata la nuova identità di lavoro o scuola con Azure Active Directory, ma questa identità non ha ancora le autorizzazioni per usare le risorse di Azure.

![](./media/virtual-machines-common-create-aad-work-id/defaultdirectoryusersaftercreate.png)

Se si usa **INVIO PASSWORD PER POSTA ELETTRONICA**viene inviata la tipologia di messaggio di posta elettronica seguente.

![](./media/virtual-machines-common-create-aad-work-id/emailreceivedfromnewusercreation.png)

## <a name="adding-azure-co-administrator-rights-for-subscriptions"></a>Aggiunta di diritti di co-amministratore di Azure per le sottoscrizioni
A questo punto è necessario aggiungere il nuovo utente come co-amministratore della sottoscrizione, in modo che possa accedere al portale di gestione. A tale scopo, fare clic su **Impostazioni**nel pannello in basso a sinistra.

![](./media/virtual-machines-common-create-aad-work-id/thesettingswidget.png)

Nella parte superiore dell'area principale delle impostazioni fare clic su **AMMINISTRATORI**. Verrà visualizzata solo l'identità dell'account Microsoft personale. Nella parte inferiore della pagina fare clic su **+AGGIUNGI** per specificare un co-amministratore. Immettere qui l'indirizzo di posta elettronica del nuovo utente che è stato creato, compreso il dominio predefinito. Come mostrato nel prossimo screenshot, accanto all'utente viene visualizzato un segno di spunta verde per la directory predefinita. Ricordarsi di selezionare tutte le sottoscrizioni che l'utente dovrà amministrare.

![](./media/virtual-machines-common-create-aad-work-id/addingnewuserascoadmin.png)

Al termine dovrebbero essere visibili due utenti, tra cui la nuova identità di co-amministratore. Uscire dal portale.

![](./media/virtual-machines-common-create-aad-work-id/newuseraddedascoadministrator.png)

## <a name="logging-in-and-changing-the-new-users-password"></a>Accesso e modifica della password del nuovo utente
Accedere con le credenziali del nuovo utente creato.

![](./media/virtual-machines-common-create-aad-work-id/signinginwithnewuser.png)

Verrà immediatamente chiesto di creare una nuova password.

![](./media/virtual-machines-common-create-aad-work-id/mustupdateyourpassword.png)

Dovrebbe essere visualizzato un messaggio simile al seguente, che conferma il completamento dell'operazione.

![](./media/virtual-machines-common-create-aad-work-id/successtourdialog.png)

## <a name="next-steps"></a>Passaggi successivi
È ora possibile usare la nuova identità di Azure Active Directory con i [modelli del gruppo di risorse di Azure](../articles/xplat-cli-azure-resource-manager.md).

    azure login
    info:    Executing command login
    warn:    Please note that currently you can login only via Microsoft organizational account or service principal. For instructions on how to set them up, please read http://aka.ms/Dhf67j.
    Username: ahmet@aztrainpassxxxxxoutlook.onmicrosoft.com
    Password: *********
    /info:    Added subscription Azure Pass
    info:    Setting subscription Azure Pass as default
    +
    info:    login command OK
    ralph@local:~$ azure config mode arm
    info:    New mode is arm
    ralph@local:~$ azure group list
    info:    Executing command group list
    + Listing resource groups
    info:    No matched resource groups were found
    info:    group list command OK
    ralph@local:~$ azure group create newgroup westus
    info:    Executing command group create
    + Getting resource group newgroup
    + Creating resource group newgroup
    info:    Created resource group newgroup
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/newgroup
    data:    Name:                newgroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags:
    data:
    info:    group create command OK


<!--HONumber=Nov16_HO3-->


