<properties
	pageTitle="Come installare e configurare Azure PowerShell"
	description="Informazioni su come installare e configurare Azure PowerShell."
	editor="tysonn"
	manager="stevenka"
	documentationCenter=""
	services=""
	authors="coreyp-at-msft"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="powershell"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="coreyp"/>

# Come installare e configurare Azure PowerShell#

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Interfaccia della riga di comando di Azure">Interfaccia della riga di comando di Azure</a></div>

##Informazioni su Azure PowerShell.#
Azure PowerShell è un modulo che offre i cmdlet per gestire Azure con Windows PowerShell. È possibile utilizzare i cmdlet per creare, testare, distribuire e gestire soluzioni e servizi offerti tramite la piattaforma Azure. Nella maggior parte dei casi, è possibile utilizzare i cmdlet per le stesse attività presenti nel portale di gestione, come ad esempio la creazione e la configurazione di servizi cloud, macchine virtuali, reti virtuali e app web.

<a id="Install"></a>
## Passaggio 1: installare

Di seguito sono illustrati i due metodi di installazione per Azure PowerShell. È possibile eseguire l'installazione da WebPI o da PowerShell Gallery.

> [AZURE.NOTE] Potrebbe essere necessario riavviare il computer dopo l'installazione per visualizzare tutti i comandi in Windows PowerShell Integrated Scripting Environment (ISE).

###Installazione di Azure PowerShell da WebPI

La procedura di installazione di Azure PowerShell 1.0 e versioni successive da WebPI è identica a quella necessaria per la versione 0.9.x. Scaricare [Azure PowerShell](http://aka.ms/webpi-azps) e avviare l'installazione. Se si dispone di Azure PowerShell 0.9.x installata, verrà richiesto di disinstallare 0.9.x . Se sono stati installati moduli di Azure PowerShell da PowerShell Gallery, il programma di installazione richiede che i moduli vengano rimossi prima dell'installazione per garantire la coerenza dell'ambiente di PowerShell Azure.

> [AZURE.NOTE] Se sono stati installati i moduli di Azure di PowerShell Gallery, il programma di installazione li rimuoverà automaticamente. per evitare che si crei confusione sui moduli installati e sul relativo percorso. I moduli di PowerShell Gallery vengono in genere installati in **%ProgramFiles%\\WindowsPowerShell\\Modules**. Il programma di installazione di WebPI, invece, installa i moduli di Azure in **%ProgramFiles%\\Microsoft SDKs\\Azure\\PowerShell**. Se viene caricata una dipendenza dal modulo durante la disinstallazione, **PowerShellGet** disinstalla i moduli e lascia file DLL bloccati e le relative cartelle. Se si verifica un errore durante l'installazione, rimuovere le cartelle Azure* nella cartella **%Programmi%\\WindowsPowerShell\\Modules**.

Se Azure PowerShell è stato installato tramite PowerShell Gallery, ma si vuole invece usare l'installazione di WebPI, l'installazione di WebPI rimuoverà automaticamente i cmdlet installati da PowerShell Gallery.

> [AZURE.NOTE] Durante l'installazione da WebPI si verifica un problema noto con **$env:PSModulePath** di PowerShell. Se il computer richiede il riavvio a causa di aggiornamenti di sistema o di altre installazioni, **$env:PSModulePath** potrebbe non includere il percorso in cui è installato Azure PowerShell. Per risolvere il problema, riavviare il computer.

###Installazione di Azure PowerShell da PowerShell Gallery

Per installare Azure PowerShell 1.0 o versioni successive da PowerShell Gallery, usare i comandi seguenti:

    # Install the Azure Resource Manager modules from the PowerShell Gallery
    Install-Module AzureRM
    Install-AzureRM

    # Install the Azure Service Management module from the PowerShell Gallery
    Install-Module Azure

    # Import AzureRM modules for the given version manifest in the AzureRM module
    Import-AzureRM

    # Import Azure Service Management module
    Import-Module Azure

####Informazioni sui comandi

- **Install-Module AzureRM** installa un modulo di bootstrap per i moduli AzureRM. Questo modulo contiene i cmdlet per aggiornare, disinstallare e importare i moduli di AzureRM in modo sicuro e coerente. Il modulo AzureRM contiene un elenco di moduli e l'intervallo delle versioni (minima e massima) richieste per garantire che non vengano introdotte modifiche di rilievo per la versione principale di AzureRM. Per altre informazioni sul controllo delle versioni semantico, vedere [semver.org](http://semver.org). Ciò significa che è possibile creare cmdlet propri usando una versione specifica di AzureRM e avere la certezza che da nessuno dei moduli installati con il programma di avvio automatico verranno introdotte modifiche di rilievo.
- **Install-AzureRM** installa tutti i moduli dichiarati nel modulo di bootstrap.
- **Install-Module Azure** installa il modulo di Azure. Si tratta del modulo di gestione dei servizi di Azure PowerShell 0.9.x. Questo modulo non deve essere sottoposto a modifiche rilevanti e deve essere intercambiabile con la versione precedente del modulo di Azure.
- **Import-AzureRM** importa tutti i moduli nell'elenco del modulo AzureRM, in cui sono indicati i moduli e le versioni. In questo modo i moduli di Azure PowerShell caricati rientrano nell'intervallo delle versioni richieste dal modulo AzureRM.
- **Import-Module Azure** importa il modulo di gestione dei servizi di Azure. Si noti che il modulo di Azure e i moduli AzureRM vengono caricati nella sessione di PowerShell e possono essere usati insieme.


## Passaggio 2: avviare
È possibile eseguire i cmdlet dalla console standard di Windows PowerShell o da PowerShell Integrated Scripting Environment (ISE). Il metodo utilizzato per aprire l'una o l'altra console varia a seconda della versione di Windows in esecuzione:

- In un computer che esegue almeno Windows 8 o Windows Server 2012 è possibile utilizzare la funzionalità di ricerca integrata. Nella schermata **Start** iniziare a digitare power. Viene visualizzato un elenco specifico di app che include Windows PowerShell. Per aprire la console, fare clic su una delle app. Per aggiungere l'app alla schermata **Start**, fare clic con il pulsante destro del mouse sull'icona.

- In un computer che esegue una versione di Windows precedente a Windows 8 o Windows Server 2012 è possibile usare il **menu Start**. Scegliere **Tutti i programmi** dal menu **Start**, fare clic su **Accessori**, quindi sulla cartella **Windows PowerShell** e infine su **Windows PowerShell**.

È anche possibile eseguire **Windows PowerShell ISE** per usare voci di menu e tasti di scelta rapida per eseguire molte delle stesse attività disponibili nella console di Windows PowerShell. Per usare ISE, digitare **powershell\_ise.exe** nella console di Windows PowerShell, in Cmd.exe o nella casella **Esegui**.

###Comandi utili per iniziare

    # To make sure the Azure PowerShell module is available after you install
    Get-Module –ListAvailable 
	
	# If the Azure PowerShell module is not listed when you run Get-Module, you may need to import it
    Import-Module Azure 
	
    # To login to Azure Resource Manager
    Login-AzureRmAccount

    # You can also use a specific Tenant if you would like a faster login experience
    # Login-AzureRmAccount -TenantId xxxx

    # To view all subscriptions for your account
    Get-AzureRmSubscription

    # To select a default subscription for your current session
    Get-AzureRmSubscription –SubscriptionName “your sub” | Select-AzureRmSubscription

    # View your current Azure PowerShell session context
    # This session state is only applicable to the current session and will not affect other sessions
    Get-AzureRmContext

    # To select the default storage context for your current session
    Set-AzureRmCurrentStorageAccount –ResourceGroupName “your resource group” –StorageAccountName “your storage account name”

    # View your current Azure PowerShell session context
    # Note: the CurrentStorageAccount is now set in your session context
    Get-AzureRmContext

    # To import the Azure.Storage data plane module (blob, queue, table)
    Import-Module Azure.Storage

    # To list all of the blobs in all of your containers in all of your accounts
    Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob


## Passaggio 3: connettere
Per la gestione dei servizi da parte dei cmdlet, è necessario disporre di una sottoscrizione. È possibile acquistare una sottoscrizione di Azure, se non se ne possiede già una. Per istruzioni, vedere la pagina relativa alle [modalità di acquisto in Azure](http://go.microsoft.com/fwlink/p/?LinkId=320795).

1. Digitare **Login-AzureRmAccount**.

2. Digitare l'indirizzo di posta elettronica e la password associati all'account. Le informazioni delle credenziali vengono autenticate e salvate in Azure, quindi la finestra viene chiusa.

--OPPURE--

Accedere all'account aziendale o dell'istituto di istruzione:

    $cred = Get-Credential
    Login-AzureRmAccount -Credential $cred
> [AZURE.NOTE] Se sono disponibili più tenant associati all'account dell'organizzazione, specificare il parametro TenantId:

    $loadersubscription = Get-AzureRmSubscription -SubscriptionName $YourSubscriptionName -TenantId $YourAssociatedSubscriptionTenantId


> [AZURE.NOTE] Questo metodo di accesso non interattivo funziona solo con un account aziendale o dell'istituto di istruzione. Un account aziendale o dell'istituto di istruzione è un utente che viene gestito dalla propria azienda o dal proprio istituto di istruzione e che viene definito nell'istanza di Azure Active Directory per tale azienda o istituto di istruzione. Se non si dispone di un account aziendale o dell'istituto di istruzione e si usa un account Microsoft per accedere alla sottoscrizione di Azure, è possibile crearne facilmente uno mediante la procedura seguente.

> 1. Accedere al [portale di Azure classico](https://manage.windowsazure.com) e fare clic su **Active Directory**.

> 2. Se non esiste alcuna directory, selezionare **Create your directory** e specificare le informazioni richieste.

> 3. Selezionare la directory e aggiungere un nuovo utente. Il nuovo utente può accedere usando un account aziendale o dell'istituto di istruzione. Durante la creazione dell'utente, verranno forniti un indirizzo di posta elettronica e una password provvisoria. Salvare queste informazioni perché verranno usate nel passaggio 5 seguente.

> 4. Nel portale di Azure classico selezionare **Impostazioni** e quindi **Amministratori**. Selezionare **Add** e aggiungere il nuovo utente come co-amministratore. In questo modo l'account aziendale o dell'istituto di istruzione può gestire la sottoscrizione di Azure.

> 5. Infine, disconnettersi dal portale di Azure classico ed eseguire di nuovo l'accesso con l'account aziendale o dell'istituto di istruzione. La prima volta che si accede con questo account verrà richiesto di cambiare la password.

> Per altre informazioni sull'iscrizione a Microsoft Azure con un account aziendale o dell'istituto di istruzione, vedere [Iscrizione ad Azure come organizzazione](/active-directory/sign-up-organization.md).

> Per altre informazioni su autenticazione e gestione delle sottoscrizioni in Azure, vedere [Gestire account, sottoscrizioni e ruoli amministrativi](http://go.microsoft.com/fwlink/?LinkId=324796).

### Visualizzare account e dettagli della sottoscrizione

Con Azure PowerShell è possibile utilizzare più account e sottoscrizioni. Per aggiungere più account, eseguire più volte **Add-AzureRmAccount**.

Per visualizzare gli account Azure disponibili, digitare **Get-AzureAccount**.

Per visualizzare le sottoscrizioni di Azure, digitare **Get-AzureRmSubscription**.

##<a id="Help"></a>Risorse della Guida##

Queste risorse consentono di ottenere informazioni per cmdlet specifici:


-   Dall'interno della console è possibile utilizzare la Guida incorporata. Per accedere a tale Guida, utilizzare il cmdlet **Get-Help**. 

- Per le informazioni messe a disposizione dalla community, visitare i seguenti forum:

 - [Forum Azure su MSDN](http://go.microsoft.com/fwlink/p/?LinkId=320212)
 - [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)

##Altre informazioni


Per altre informazioni sull'uso dei cmdlet, vedere le risorse seguenti:

Per istruzioni di base sull'uso di Windows PowerShell, vedere [Uso di Windows PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=321939).

Per informazioni di riferimento sui cmdlet, vedere [Informazioni di riferimento per i cmdlet di Azure](https://msdn.microsoft.com/library/windowsazure/jj554330.aspx).

Per script di esempio e istruzioni per imparare a usare gli script per la gestione di Azure, vedere lo [Script Center](http://go.microsoft.com/fwlink/p/?LinkId=321940).

<!---HONumber=AcomDC_0323_2016-->