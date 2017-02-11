---
title: Come installare e configurare Azure PowerShell
description: Informazioni su come installare e configurare Azure PowerShell.
editor: tysonn
manager: dongill
documentationcenter: 
services: 
author: coreyp-at-msft
ms.assetid: 802b28e0-1a8c-4872-a9ea-c889b15b9498
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: coreyp
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f20adb27f41cdbd6918340ae688b4ce00dd2d593


---
# <a name="how-to-install-and-configure-azure-powershell"></a>Come installare e configurare Azure PowerShell
<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Azure CLI">Interfaccia della riga di comando di Azure</a></div>

## <a name="what-is-azure-powershell"></a>Informazioni su Azure PowerShell.
Azure PowerShell è un set di moduli che offre i cmdlet per gestire Azure con Windows PowerShell. È possibile utilizzare i cmdlet per creare, testare, distribuire e gestire soluzioni e servizi offerti tramite la piattaforma Azure. Nella maggior parte dei casi, è possibile usare i cmdlet per le stesse attività presenti nel portale di Azure, come ad esempio la creazione e la configurazione di servizi cloud, macchine virtuali, reti virtuali e app Web.

## <a name="how-versioning-works"></a>Funzionamento del controllo delle versioni
Azure PowerShell usa il versionamento semantico. In questo modo, se la versione A > versione B, allora la versione A contiene le API più aggiornate. Ciò significa anche che le modifiche apportate alla versione principale si traducono in modifiche di rilievo su uno o più cmdlet.  Quindi, ad esempio, la versione 1.7.0 rappresenta un aggiornamento rapido per risolvere una modifica di rilievo nelle versioni 1.x di Azure PowerShell.

Per altre informazioni sulle procedure di versionamento semantico in Azure PowerShell, vedere la specifica sul versionamento semantico all'indirizzo: http://semver.org

Per ottenere le API più aggiornate, è consigliabile usare la versione 2.x. Tuttavia, se si dispone di script scritti con la versione 1.x e non si desidera assorbire le modifiche di rilievo nella versione 2.x descritte nelle [note sulla versione](https://github.com/Azure/azure-powershell/blob/dev/documentation/release-notes/migration-guide.2.0.0.md) 2.x, è necessario installare la versione 1.7.0.

Se è installata la versione più recente del modulo del profilo, potrebbe verificarsi una mancata corrispondenza delle versioni. A questo punto viene caricata una versione precedente del modulo con dipendenza da quest'ultima. Il modo più semplice per risolvere il problema è l'installazione tramite il file MSI più aggiornato. Il file MSI elimina automaticamente le versioni precedenti dei moduli.

### <a name="installing-module-versions-side-by-side"></a>Installazione di versioni del modulo affiancate
La versione 2.1.0 e la versione 1.2.6 per AzureStack sono le prime versioni del modulo progettate per l'installazione e l'uso affiancati. Poiché Azure PowerShell usa moduli binari, è necessario aprire una nuova finestra di PowerShell e impiegare **Import-Module** per importare una versione specifica dei cmdlet di AzureRM:

    Import-Module AzureRM -RequiredVersion 2.1.0**

Le versioni precedenti alla versione 2.1.0 e diverse dalla 1.2.6 non funzionano bene se affiancate ad altre versioni del modulo di Azure PowerShell. Quando si carica una versione precedente dei moduli Azure PowerShell usando un comando simile a quello indicato in alto, verranno caricate le versioni incompatibili del modulo **AzureRM.Profile**, con conseguente richiesta di accesso da parte dei cmdlet a ogni esecuzione degli stessi, anche dopo avere eseguito l'accesso.

Il modo più semplice per risolvere questo problema è installare la versione più recente di Azure PowerShell dal feed WebPI o dal file con estensione MSI: questa operazione rimuove le versioni precedenti dei moduli installati dalla raccolta. 

Si noti che i moduli di Azure e AzureRM hanno dipendenze in comune, pertanto se si usano entrambi i moduli quando si aggiorna uno dei due, è necessario aggiornare anche l'altro. Le versioni precedenti del modulo Azure presentano lo stesso problema con il caricamento del modulo affiancato delle versioni precedenti del modulo AzureRM.

<a id="Install"></a>

## <a name="step-1-install"></a>Passaggio 1: installare
Di seguito sono illustrati i due metodi di installazione per Azure PowerShell. È possibile eseguire l'installazione da PowerShell Gallery o da WebPI.

### <a name="installing-azure-powershell-from-the-powershell-gallery"></a>Installazione di Azure PowerShell da PowerShell Gallery
Il modo migliore consiste nell'usare PowerShell Gallery. Per usare PowerShell Gallery, è necessario il modulo PowerShellGet. Il modulo è disponibile qui: [PowerShellGallery.com](https://www.powershellgallery.com/)

Installare Azure PowerShell 1.3.0 o versione successiva da PowerShell Gallery con un prompt di Windows PowerShell o PowerShell Integrated Scripting Environment (ISE) con privilegi elevati, usando i comandi seguenti:

    # Install the Azure Resource Manager modules from the PowerShell Gallery
    Install-Module AzureRM

    # Install the Azure Service Management module from the PowerShell Gallery
    Install-Module Azure

#### <a name="more-about-these-commands"></a>Informazioni sui comandi
* **Install-Module AzureRM** consente di installare un modulo di rollup per i cmdlet di Azure Resource Manager. Il modulo AzureRM varia a seconda 
* dell'intervallo di versioni specifico di ciascun modulo Azure Resource Manager. L'intervallo di versioni incluso assicura che nessuna modifica importante al modulo può essere considerata durante l'installazione di moduli AzureRM con la stessa versione principale. Durante l'installazione del modulo AzureRM, qualsiasi modulo Azure Resource Manager non installato precedentemente viene scaricato e installato da PowerShell Gallery. Per altre informazioni sul Versionamento Semantico usato dai moduli Azure PowerShell, vedere [semver.org](http://semver.org). 
* **Install-Module Azure** consente di installare il modulo Azure. Si tratta del modulo di gestione dei servizi di Azure PowerShell 0.9.x. Questo modulo non deve essere sottoposto a modifiche rilevanti e deve essere intercambiabile con la versione precedente del modulo di Azure.

### <a name="installing-azure-powershell-from-webpi"></a>Installazione di Azure PowerShell da WebPI
La procedura di installazione di Azure PowerShell 1.0 e versioni successive da WebPI è identica a quella necessaria per la versione 0.9.x. Scaricare [Azure PowerShell](http://aka.ms/webpi-azps) e avviare l'installazione. Se è installata la versione Azure PowerShell 0.9.x, l'aggiornamento prevede la disinstallazione di tale versione. Se sono stati installati moduli Azure PowerShell da PowerShell Gallery, il programma di installazione rimuove automaticamente i moduli prima dell'installazione per garantire la coerenza dell'ambiente di Azure PowerShell.

> [!NOTE]
> Se sono stati installati precedentemente moduli Azure da PowerShell Gallery, il programma di installazione li rimuove automaticamente per evitare confusione tra le versioni dei moduli installati e la posizione in cui si trovano. I moduli PowerShell Gallery vengono in genere installati in **%ProgramFiles%\WindowsPowerShell\Modules**. Il programma di installazione di WebPI, invece, installa i moduli Azure in **%ProgramFiles(x86)%\Microsoft SDKs\Azure\PowerShell\**. Se si verifica un errore durante l'installazione, è possibile rimuovere manualmente le cartelle Azure* nella cartella **%ProgramFiles%\WindowsPowerShell\Modules** e ripetere l'installazione.
> 
> 

Al termine dell'installazione, l'impostazione ```$env:PSModulePath``` includerà le directory contenenti i cmdlet di Azure PowerShell.

> [!NOTE]
> Durante l'installazione da WebPI è possibile che si verifichi un errore con **$env:PSModulePath** di PowerShell. Se il computer richiede il riavvio a causa di aggiornamenti di sistema o di altre installazioni, gli aggiornamenti di **$env:PSModulePath** potrebbero non includere il percorso in cui è installato Azure PowerShell. In questo caso, sarà visualizzato il messaggio cmdlet non riconosciuto quando si tenta di usare i cmdlet di Azure PowerShell dopo l'installazione o l'aggiornamento. In questo caso, riavviare il computer per risolvere il problema.
> 
> 

Se si riceve un messaggio simile al seguente quando si tenta di caricare o eseguire i cmdlet:

```
    PS C:\> Get-AzureRmResource
    Get-AzureRmResource : The term 'Get-AzureRmResource' is not recognized as the name of a cmdlet, function,
    script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is
    correct and try again.
    At line:1 char:1
    + Get-AzureRmResource
    + ~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : ObjectNotFound: (get-azurermresourcefork:String) [], CommandNotFoundException
        + FullyQualifiedErrorId : CommandNotFoundException
```

Questo comportamento può essere corretto riavviando il computer o importando i cmdlet da C:\Programmi\WindowsPowerShell\Modules\Azure\XXXX\ come indicato di seguito, dove XXXX è la versione di PowerShell installata:

```
Import-Module "C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\azure.psd1"
Import-Module "C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\expressroute\expressroute.psd1"
```

## <a name="step-2-start"></a>Passaggio 2: avviare
È possibile eseguire i cmdlet dalla console standard di Windows PowerShell o da PowerShell Integrated Scripting Environment (ISE).
Il metodo utilizzato per aprire l'una o l'altra console varia a seconda della versione di Windows in esecuzione:

* In un computer che esegue almeno Windows 8 o Windows Server 2012 è possibile utilizzare la funzionalità di ricerca integrata. Nella schermata **Start** iniziare a digitare power. Viene visualizzato un elenco specifico di app che include Windows PowerShell. Per aprire la console, fare clic su una delle app. Per aggiungere l'app alla schermata **Start**, fare clic con il pulsante destro del mouse sull'icona.
* In un computer che esegue una versione di Windows precedente a Windows 8 o Windows Server 2012 è possibile usare il **menu Start**. Scegliere **Tutti i programmi** dal menu **Start**, fare clic su **Accessori**, selezionare la cartella **Windows PowerShell** e quindi fare clic su **Windows PowerShell**.

È anche possibile eseguire **Windows PowerShell ISE** per usare voci di menu e tasti di scelta rapida ed eseguire molte delle stesse attività disponibili nella console di Windows PowerShell. Per usare ISE, nella console di Windows PowerShell, Cmd.exe, o nella casella **Esegui** digitare **powershell_ise.exe**.

### <a name="commands-to-help-you-get-started"></a>Comandi utili per iniziare
    # To make sure the Azure PowerShell module is available after you install
    Get-Module –ListAvailable 

    # To log in to Azure Resource Manager
    Login-AzureRmAccount

    # You can also use a specific Tenant if you would like a faster log in experience
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

    # To list all of the blobs in all of your containers in all of your accounts
    Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob


## <a name="step-3-connect"></a>Passaggio 3: connettere
I cmdlet necessita della sottoscrizione per la gestione dei servizi. È possibile acquistare una sottoscrizione di Azure (se non se ne possiede già una). Per istruzioni, vedere la pagina relativa alle [modalità di acquisto in Azure](http://go.microsoft.com/fwlink/p/?LinkId=320795).

1. Digitare **Login-AzureRmAccount**
2. Digitare l'indirizzo di posta elettronica e la password associati all'account. Le informazioni delle credenziali vengono autenticate e salvate in Azure, quindi la finestra viene chiusa.

--OPPURE--

Accedere all'account aziendale o dell'istituto di istruzione:

    $cred = Get-Credential
    Login-AzureRmAccount -Credential $cred
> [!NOTE]
> Se sono disponibili più tenant associati all'account dell'organizzazione, specificare il parametro TenantId:
> 
> 

    $loadersubscription = Get-AzureRmSubscription -SubscriptionName $YourSubscriptionName -TenantId $YourAssociatedSubscriptionTenantId


> [!NOTE]
> Questo metodo di accesso non interattivo funziona solo con un account aziendale o dell'istituto di istruzione. Un account aziendale o dell'istituto di istruzione è un utente che viene gestito dalla propria azienda o dal proprio istituto di istruzione e che viene definito nell'istanza di Azure Active Directory per tale azienda o istituto di istruzione. Se non si dispone di un account aziendale o dell'istituto di istruzione e si usa un account Microsoft per accedere alla sottoscrizione di Azure, è possibile crearne facilmente uno tramite la procedura seguente.
> 
> 1. Accedere al [portale di Azure classico](https://manage.windowsazure.com) e fare clic su **Active Directory**.
> 2. Se non esiste alcuna directory, selezionare **Crea directory** e specificare le informazioni richieste.
> 3. Selezionare la directory e aggiungere un nuovo utente. Il nuovo utente può accedere usando un account aziendale o dell'istituto di istruzione. Durante la creazione dell'utente, verranno forniti un indirizzo di posta elettronica e una password provvisoria. Salvare queste informazioni perché verranno usate nel passaggio 5 seguente.
> 4. Nel portale di Azure classico selezionare **Impostazioni** e quindi **Amministratori**. Selezionare **Aggiungi** e aggiungere il nuovo utente come co-amministratore. In questo modo l'account aziendale o dell'istituto di istruzione potrà gestire la sottoscrizione di Azure.
> 5. Infine, disconnettersi dal portale di Azure classico ed eseguire di nuovo l'accesso con l'account aziendale o dell'istituto di istruzione. La prima volta che si accede con questo account verrà richiesto di cambiare la password.
> 
> Per altre informazioni sull'iscrizione a Microsoft Azure con un account aziendale o dell'istituto di istruzione, vedere [Iscrizione ad Azure come organizzazione](active-directory/sign-up-organization.md).
> 
> Per altre informazioni su autenticazione e gestione delle sottoscrizioni in Azure, vedere [Gestire account, sottoscrizioni e ruoli amministrativi](http://go.microsoft.com/fwlink/?LinkId=324796).
> 
> 

### <a name="view-account-and-subscription-details"></a>Visualizzare i dettagli dell'account e della sottoscrizione
Con Azure PowerShell è possibile usare più account e sottoscrizioni. Per aggiungere più account, eseguire più volte **Add-AzureRmAccount**.

Per visualizzare gli account Azure disponibili, digitare **Get-AzureAccount**.

Per visualizzare la propria sottoscrizione di Azure, digitare **Get-AzureRmSubscription**.

## <a name="a-idhelpagetting-help"></a><a id="Help"></a>Risorse della Guida
Queste risorse consentono di ottenere informazioni per cmdlet specifici:

* Dall'interno della console è possibile utilizzare la Guida incorporata. Per accedere a tale Guida, usare il cmdlet **Get-Help**. 
* Per le informazioni messe a disposizione dalla community, visitare i seguenti forum:
  
  * [Forum Azure su MSDN](http://go.microsoft.com/fwlink/p/?LinkId=320212)
  * [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)

## <a name="learn-more"></a>Ulteriori informazioni
Per altre informazioni sull'uso dei cmdlet, vedere le risorse seguenti:

Per istruzioni di base sull'uso di Windows PowerShell, vedere [Uso di Windows PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=321939).

Per informazioni di riferimento per i cmdlet, vedere [Informazioni di riferimento per i cmdlet di Azure](https://msdn.microsoft.com/library/windowsazure/jj554330.aspx).

Per script di esempio e istruzioni per imparare a usare gli script per la gestione di Azure, vedere [Script Center](http://go.microsoft.com/fwlink/p/?LinkId=321940).




<!--HONumber=Nov16_HO3-->


