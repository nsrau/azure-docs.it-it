<properties
   pageTitle="Creare un'identità di lavoro o scuola in AAD | Microsoft Azure"
   description="Informazioni su come creare un'identità di lavoro o scuola in Azure Active Directory da utilizzare con Gestione risorse e con i modelli di distribuzione classica."
   services="virtual-machines"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure"
   ms.date="09/01/2015"
   ms.author="rasquill"/>

# Creazione di un'identità di lavoro o scuola in Azure Active Directory

Se si è creato un account Azure personale, oppure si ha un sottoscrizione MSDN personale e si è creato l'account Azure per sfruttare i vantaggi dei crediti MSDN in Azure, si è usata un'identità *account Microsoft*. Numerose funzionalità di Azure (ad esempio i [modelli dei gruppi di risorse](../resource-group-overview.md)) richiedono un account di lavoro o scuola (un'identità gestita da Azure Active Directory) per funzionare.


> [AZURE.NOTE]Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../resource-manager-deployment-model.md). In questo articolo viene illustrato l'utilizzo di entrambi i modelli, ma Microsoft indica che le distribuzioni più nuove utilizzano il modello di gestione delle.


Fortunatamente, uno degli aspetti migliori dell'account Azure personale sta nel fatto che è dotato di un dominio predefinito di Azure Active Directory, che è possibile usare per creare un nuovo account di lavoro o scuola da usare con le funzionalità di Azure che lo richiedono.

> [AZURE.NOTE]Se si sono ricevuti un nome utente e una password da un amministratore, è probabile che si abbia già un ID di lavoro o scuola (a volte detto anche *ID organizzazione*). In tal caso, è possibile iniziare immediatamente a usare l'account di Azure per accedere alle risorse di Azure che ne richiedono uno. Se non è possibile usare tali risorse, potrebbe essere necessario tornare a questo articolo. Per altre informazioni, vedere [Account che è possibile usare per l'accesso](https://msdn.microsoft.com/library/azure/dn629581.aspx#BKMK_SignInAccounts) e [Relazione di una sottoscrizione di Azure e Azure AD](https://msdn.microsoft.com/library/azure/dn629581.aspx#BKMK_SubRelationToDir).

I passaggi sono semplici. È necessario individuare l'identità attiva nel portale di Azure, scoprire il dominio di Azure Active Directory predefinito e aggiungere un nuovo utente come co-amministratore di Azure.

## Individuare la directory predefinita nel portale di Azure

Per iniziare, accedere al [portale di Azure](https://manage.windowsazure.com) con l'identità dell'account Microsoft personale. Dopo l'accesso, scorrere verso il basso nel pannello blu sul lato sinistro e fare clic su **ACTIVE DIRECTORY**.

![Azure Active Directory](./media/resource-group-create-work-id-from-personal/azureactivedirectorywidget.png)

Occorre innanzitutto trovare alcuni informazioni sulla propria identità in Azure. Nel riquadro principale dovrebbero essere visualizzate informazioni simili alle seguenti, che mostrano la presenza di una directory predefinita.

![](./media/resource-group-create-work-id-from-personal/defaultaadlisting.png)

È possibile cercare altre informazioni sulla directory facendo clic sulla riga della directory predefinita per visualizzarne le proprietà.

![](./media/resource-group-create-work-id-from-personal/defaultdirectorypage.png)

Per visualizzare il nome di dominio predefinito, fare clic su **DOMINI**.

![](./media/resource-group-create-work-id-from-personal/domainclicktoseeyourdefaultdomain.png)

Da qui si dovrebbe vedere che, al momento della creazione dell'account Azure, Azure Active Directory ha creato un dominio personale predefinito, che è un hash (un numero generato da una stringa di testo) dell'ID personale usato come sottodominio di onmicrosoft.com. Questo è il dominio a cui verrà ora aggiunto un nuovo utente.

## Creazione di un nuovo utente nel dominio predefinito

Fare clic su **UTENTI** e cercare il proprio account personale. Nella colonna **ORIGINATO DA** dovrebbe essere evidente che si tratta di un **account Microsoft**. Si desidera creare un utente nel dominio predefinito .onmicrosoft.com di Azure Active Directory.

![](./media/resource-group-create-work-id-from-personal/defaultdirectoryuserslisting.png)

Nei passaggi seguenti si seguiranno [queste istruzioni](https://technet.microsoft.com/library/hh967632.aspx#BKMK_1), ma con un esempio specifico.

Nella parte inferiore della pagina fare clic su **+AGGIUNGI UTENTE**. Nella pagina visualizzata digitare il nuovo nome utente e selezionare **Nuovo utente nell'organizzazione** per **Tipo di utente**. In questo esempio il nuovo nome utente è `ahmet`. Selezionare il dominio predefinito individuato in precedenza come dominio per l'indirizzo di posta elettronica di Ahmet. Al termine, fare clic sulla freccia Avanti.

![](./media/resource-group-create-work-id-from-personal/addingauserwithdirectorydropdown.png)

Aggiungere altri dettagli per Ahmet, ma assicurarsi di selezionare il valore appropriato per **RUOLO**. Una soluzione semplice consiste nell'usare **Amministratore globale** per assicurarsi che tutto funzioni, ma è consigliabile usare un ruolo di livello inferiore, se possibile. Questo esempio usa il ruolo **Utente** (per altre informazioni sui ruoli fare clic [qui](https://msdn.microsoft.com/library/azure/dn468213.aspx#BKMK_1)). Non abilitare l'autenticazione a più fattori, a meno che non si desideri usarla per ogni log nell'operazione. Al termine, fare clic sulla freccia Avanti.

![](./media/resource-group-create-work-id-from-personal/userprofileuseradmin.png)

Fare clic sul pulsante **Crea** per generare e visualizzare una password temporanea per Ahmet.

![](./media/resource-group-create-work-id-from-personal/gettemporarypasswordforuser.png)

Copiare l'indirizzo di posta elettronica di nome utente o usare **INVIO PASSWORD PER POSTA ELETTRONICA**. Le informazioni saranno necessarie fra breve per l'accesso.

![](./media/resource-group-create-work-id-from-personal/receivedtemporarypassworddialog.png)

Ora verrà visualizzato il nuovo utente, **Ahmet the Developer**, originato da Azure Active Directory. Si è creata la nuova identità di lavoro o scuola con Azure Active Directory, ma questa identità non ha ancora le autorizzazioni per usare le risorse di Azure.

![](./media/resource-group-create-work-id-from-personal/defaultdirectoryusersaftercreate.png)

Se si usa **INVIO PASSWORD PER POSTA ELETTRONICA** viene inviato il tipo di messaggio di posta elettronica seguente.

![](./media/resource-group-create-work-id-from-personal/emailreceivedfromnewusercreation.png)

## Aggiunta di diritti di co-amministratore di Azure per le sottoscrizioni

A questo punto è necessario aggiungere il nuovo utente come co-amministratore della sottoscrizione, in modo che possa accedere al portale di gestione. A tale scopo, fare clic su **Impostazioni** nel pannello in basso a sinistra.

![](./media/resource-group-create-work-id-from-personal/thesettingswidget.png)

Nella parte superiore dell'area principale delle impostazioni fare clic su **AMMINISTRATORI**. Dovrebbe essere visualizzata solo l'identità dell'account Microsoft personale. Nella parte inferiore della pagina fare clic su **+AGGIUNGI** per specificare un co-amministratore. Immettere qui l'indirizzo di posta elettronica del nuovo utente che è stato creato, compreso il dominio predefinito. Come mostrato nel prossimo screenshot, accanto all'utente viene visualizzato un segno di spunta verde per la directory predefinita. Ricordarsi di selezionare tutte le sottoscrizioni che l'utente dovrà essere in grado di gestire.

![](./media/resource-group-create-work-id-from-personal/addingnewuserascoadmin.png)

Al termine dovrebbero essere visibili due utenti, tra cui la nuova identità di co-amministratore. Uscire dal portale.

![](./media/resource-group-create-work-id-from-personal/newuseraddedascoadministrator.png)

## Accesso e modifica della password del nuovo utente

Accedere con le credenziali del nuovo utente creato.

![](./media/resource-group-create-work-id-from-personal/signinginwithnewuser.png)

Verrà immediatamente chiesto di creare una nuova password.

![](./media/resource-group-create-work-id-from-personal/mustupdateyourpassword.png)

Dovrebbe essere visualizzato un messaggio simile al seguente, che conferma il completamento dell'operazione.

![](./media/resource-group-create-work-id-from-personal/successtourdialog.png)


## Passaggi successivi

È ora possibile usare la nuova identità di Azure Active Directory con i [modelli dei gruppi di risorse di Azure](xplat-cli-azure-resource-manager.md).

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

<!---HONumber=Oct15_HO3-->