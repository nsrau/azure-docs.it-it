<properties
   pageTitle="Uso degli script di Windows PowerShell per la pubblicazione in ambienti di sviluppo e test | Microsoft Azure"
   description="Informazioni su come utilizzare gli script di Windows PowerShell da Visual Studio per pubblicare allo sviluppo e gli ambienti di prova."
   services="visual-studio-online"
   documentationCenter="na"
   authors="kempb"
   manager="douge"
   editor="tglee" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="09/08/2015"
   ms.author="kempb" />

# Utilizzo degli script di Windows PowerShell per la pubblicazione in ambienti di sviluppo e test

Quando si crea un'applicazione web in Visual Studio, è possibile generare uno script Windows PowerShell che può essere utilizzato in un secondo momento per automatizzare la pubblicazione del sito Web in Azure come un'applicazione Web nel servizio App Azure o una macchina virtuale. È possibile modificare ed estendere lo script di Windows PowerShell nell'editor di Visual Studio in base alle proprie esigenze o integrare lo script di compilazione esistente, il test e la pubblicazione di script.

Utilizzando questi script, è possibile eseguire il provisioning (noto anche come ambienti di sviluppo e test) di versioni personalizzate del sito per un utilizzo temporaneo. Ad esempio, si potrebbe impostare una particolare versione del sito Web in una macchina virtuale di Azure o in una slot di gestione temporanea in un sito Web per eseguire un gruppo di test, riprodurre un bug, testare una correzione di bug, una versione di valutazione di una modifica proposta o configurare un ambiente personalizzato per una dimostrazione o una presentazione. Dopo aver creato uno script che pubblica il progetto, è possibile ricreare ambienti identici eseguendo nuovamente lo script in base alle esigenze o eseguire lo script con la build dell'applicazione web per creare un ambiente di test personalizzato.

## Elementi necessari

- Azure SDK 2.3 o versioni successive Vedere [Scaricare Visual Studio](http://go.microsoft.com/fwlink/?LinkID=624384) per ulteriori informazioni.

    Non è necessario Azure SDK per generare script per i progetti web. Questa funzionalità è per i progetti web, non per i ruoli web nei servizi cloud.

- Azure PowerShell 0.7.4 o versione successiva Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](powershell-install-configure.md).

- [Windows PowerShell 3.0](http://go.microsoft.com/?linkid=9811175) o versione successiva.

## Strumenti aggiuntivi

Sono disponibili altri strumenti e risorse per l'utilizzo di PowerShell in Visual Studio per lo sviluppo in Azure. Vedere [PowerShell Tools per Visual Studio](http://go.microsoft.com/fwlink/?LinkId=404012).

## Come generare script di pubblicazione

È possibile generare gli script di pubblicazione per una macchina virtuale che ospita il sito Web quando si crea un nuovo progetto seguendo [queste istruzioni](virtual-machines-dotnet-create-visual-studio-powershell.md). È inoltre possibile [generare script per le app web di pubblicazione nel servizio di Azure App](web-sites-dotnet-get-started.md).

## Script generati da Visual Studio

Visual Studio genera una cartella a livello di soluzione denominata **PublishScripts** che contiene due file di Windows PowerShell, uno script di pubblicazione per la macchina virtuale o sito Web e un modulo che contiene funzioni che è possibile utilizzare negli script. Visual Studio genera inoltre un file in formato JSON che specifica i dettagli del progetto in distribuzione.

### Pubblicazione di script da parte di Windows PowerShell

Lo script di pubblicazione contiene specifici passaggi di pubblicazione per la distribuzione in una macchina virtuale o in un sito Web. Visual Studio fornisce la colorazione della sintassi per lo sviluppo di Windows PowerShell . La Guida per le funzioni è disponibile, ed è possibile modificare liberamente le funzioni nello script per adattarle ai propri requisiti.

### Modulo di Windows PowerShell

Il modulo Windows PowerShell generato da Visual Studio contiene funzioni che utilizzano lo script di pubblicazione. Queste sono le funzioni di Azure PowerShell e non possono essere modificate. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](powershell-install-configure.md).

### File di configurazione JSON.

Il file JSON viene creato nella cartella **Configurazioni** e contiene dati di configurazione che consente di specificare esattamente quali risorse distribuire in Azure. Il nome del file generato da Visual Studio è project-name-WAWS-dev. json se è stato creato un sito Web, o project name-VM-dev.json se è stata creata una macchina virtuale. Di seguito è riportato un esempio di un file di configurazione JSON generato quando si crea un sito Web. La maggior parte dei valori è facilmente comprensibile. Il nome del sito Web viene generato da Azure, pertanto potrebbe non corrispondere al nome del progetto.

```
{
    "environmentSettings": {
        "webSite": {
            "name": "WebApplication26632",
            "location": "West US"
        },
        "databases": [
            {
                "connectionStringName": "DefaultConnection",
                "databaseName": "WebApplication26632_db",
                "serverName": "YourDatabaseServerName",
                "user": "sqluser2",
                "password": "",
                "edition": "",
                "size": "",
                "collation": "",
                "location": "West US"
            }
        ]
    }
}
```
Quando si crea una macchina virtuale, il file di configurazione JSON è simile al seguente. Si noti che viene creato un servizio cloud come contenitore per la macchina virtuale. La macchina virtuale contiene i consueti endpoint per l'accesso web tramite HTTP e HTTPS, come pure gli endpoint per distribuzione Web, che consente di pubblicare nel sito Web dal computer locale, Desktop remoto e Windows PowerShell.

```
{
    "environmentSettings": {
        "cloudService": {
            "name": "myusernamevm1",
            "affinityGroup": "",
            "location": "West US",
            "virtualNetwork": "",
            "subnet": "",
            "availabilitySet": "",
            "virtualMachine": {
                "name": "myusernamevm1",
                "vhdImage": "a699494373c04fc0bc8f2bb1389d6106__Win2K8R2SP1-Datacenter-201403.01-en.us-127GB.vhd",
                "size": "Small",
                "user": "vmuser1",
                "password": "",
                "enableWebDeployExtension": true,
                "endpoints": [
                    {
                        "name": "Http",
                        "protocol": "TCP",
                        "publicPort": "80",
                        "privatePort": "80"
                    },
                    {
                        "name": "Https",
                        "protocol": "TCP",
                        "publicPort": "443",
                        "privatePort": "443"
                    },
                    {
                        "name": "WebDeploy",
                        "protocol": "TCP",
                        "publicPort": "8172",
                        "privatePort": "8172"
                    },
                    {
                        "name": "Remote Desktop",
                        "protocol": "TCP",
                        "publicPort": "3389",
                        "privatePort": "3389"
                    },
                    {
                        "name": "Powershell",
                        "protocol": "TCP",
                        "publicPort": "5986",
                        "privatePort": "5986"
                    }
                ]
            }
        },
        "databases": [
            {
                "connectionStringName": "",
                "databaseName": "",
                "serverName": "",
                "user": "",
                "password": ""
            }
        ],
        "webDeployParameters": {
            "iisWebApplicationName": "Default Web Site"
        }
    }
}
```

È possibile modificare la configurazione JSON per modificare l'operazione eseguita quando si eseguono gli script di pubblicazione. Le sezioni `cloudService` e `virtualMachine` sono necessarie, ma è possibile eliminare la sezione `databases` se non è necessario. Le proprietà che sono vuote nel file di configurazione predefinito generato da Visual Studio sono facoltative; quelle che dispongono di valori nel file di configurazione predefinite sono necessarie.

Se si dispone di un sito Web che dispone di più ambienti di distribuzione (noti come slot) anziché di un unico sito di produzione in Azure, è possibile includere il nome dello slot nel nome del sito Web nel file di configurazione JSON. Ad esempio, se si dispone di un sito Web denominato **mysite** e una slot per esso denominata **test** allora l'URI è mysite test.cloudapp.net, ma il nome corretto da utilizzare nel file di configurazione è mysite(test). È possibile eseguire questo solo se il sito Web e le slot sono già presenti nella sottoscrizione. Se non sono presenti, creare il sito Web eseguendo lo script senza specificare la slot, quindi creare la slot nel portale di gestione di Azure e successivamente eseguire lo script con il nome del sito Web modificato. Per ulteriori informazioni su tutte le slot di distribuzione per le app web, vedere [Configurare ambienti di gestione temporanea per le app web nel Servizio dell’App di Azure](web-sites-staged-publishing.md).

## Come eseguire gli script di pubblicazione

Se non è stato eseguito prima uno script Windows PowerShell, è innanzitutto necessario impostare i criteri di esecuzione per consentire l'esecuzione di script. Questa è una funzionalità di sicurezza per impedire agli utenti di eseguire script di Windows PowerShell se sono vulnerabili a malware o virus che comportano l'esecuzione di script.

### Esecuzione dello script

1. Creare il pacchetto di distribuzione Web per il progetto. Un pacchetto di distribuzione Web è un archivio compresso (con estensione zip) che contiene i file che si desidera copiare in una macchina virtuale o il sito Web. È possibile creare pacchetti di distribuzione Web in Visual Studio per qualsiasi applicazione web.

    ![Creare pacchetto di distribuzione web](./media/vs-azure-tools-publishing-using-powershell-scripts/IC767885.png)

    Per ulteriori informazioni, vedere [Procedura: Creare un pacchetto di distribuzione Web in Visual Studio](https://msdn.microsoft.com/library/dd465323.aspx). È inoltre possibile automatizzare la creazione del pacchetto di distribuzione Web, come descritto nella sezione **Personalizzazione ed estensione degli script di pubblicazione** più avanti in questo argomento.

1. In **Esplora**, aprire il menu di scelta rapida per lo script e quindi scegliere **Aprire con PowerShell ISE**.

1. Se questa è la prima volta che sono stati eseguiti gli script di Windows PowerShell su questo computer, aprire una finestra del prompt dei comandi con privilegi di amministratore e digitare il comando seguente:

    `Set-ExecutionPolicy RemoteSigned`

1. Accedere ad Azure usando il comando seguente.

    `Add-AzureAccount`

    Quando richiesto, fornire nome utente e password.

    Si noti che quando si automatizza lo script, questo metodo per fornire credenziali di Azure non funzionerà. Al contrario, utilizzare il file. publishsettings per fornire le credenziali. Solamente una volta, si utilizzi il comando **Get-AzurePublishSettingsFile** per scaricare il file da Azure e successivamente utilizzare **Import-AzurePublishSettingsFile** per importare il file. Per istruzioni dettagliate, vedere [Come installare e configurare Azure PowerShell](powershell-install-configure.md).

1. (Facoltativo) Se si desidera creare risorse con Azure, ad esempio la macchina virtuale, il database e il sito Web senza pubblicare l'applicazione web, utilizzare il comando **Pubblica WebApplication.ps1** con l’argomento **Configurazione** impostato per il file di configurazione JSON. Questa riga di comando utilizza il file di configurazione JSON per determinare le risorse da creare. Poiché utilizza le impostazioni predefinite per gli altri argomenti della riga di comando, crea le risorse, ma non pubblica l'applicazione web. L’opzione Verbose fornisce ulteriori informazioni sulle attività in corso.

    `Publish-WebApplication.ps1 -Verbose –Configuration C:\Path\WebProject-WAWS-dev.json`

1. Utilizzare il comando **Pubblica WebApplication.ps1** come indicato in uno dei seguenti esempi per richiamare lo script e pubblicare l'applicazione web. Se è necessario eseguire l'override delle impostazioni predefinite per gli altri argomenti, ad esempio il nome della sottoscrizione, pubblicare il nome del pacchetto, le credenziali di macchina virtuale o le credenziali del server database, è possibile specificare tali parametri. Utilizzare l’opzione **– Verbose** per visualizzare ulteriori informazioni sullo stato di avanzamento del processo di pubblicazione.

    ```
    Publish-WebApplication.ps1 –Configuration C:\Path\WebProject-WAWS-dev-json `
    –SubscriptionName Contoso `
    -WebDeployPackage C:\Documents\Azure\ADWebApp.zip `
    -DatabaseServerPassword @{Name="dbServerName";Password="adminPassword"} `
    -Verbose
    ```

    Se si crea una macchina virtuale, il comando è simile al seguente. In questo esempio viene inoltre illustrato come specificare le credenziali per più database. Per le macchine virtuali create da questi script, il certificato SSL non è da un'autorità radice attendibile. Pertanto, è necessario utilizzare l’opzione **-AllowUntrusted**.

    ```
    Publish-WebApplication.ps1 `
    -Configuration C:\Path\ADVM-VM-test.json `
    -SubscriptionName Contoso `
    -WebDeployPackage C:\Path\ADVM.zip `
    -AllowUntrusted `
    -VMPassword @{name = "vmUserName"; password = "YourPasswordHere"} `
    -DatabaseServerPassword @{Name="server1";Password="adminPassword1"}, @{Name="server2";Password="adminPassword2"} `
    -Verbose
    ```

    Lo script può creare database, ma non crea server di database. Se si desidera creare un server di database, è possibile utilizzare la funzione **New-AzureSqlDatabaseServer** nel modulo di Azure.

## Personalizzazione ed estensione degli script di pubblicazione

È possibile personalizzare lo script di pubblicazione e i file di configurazione JSON. Le funzioni nel modulo Windows PowerShell **AzureWebAppPublishModule.psm1** non possono essere modificati. Se si desidera specificare un database diverso o modificare alcune delle proprietà della macchina virtuale, modificare il file di configurazione JSON. Se si desidera estendere le funzionalità dello script per automatizzare la compilazione e il test del progetto, è possibile implementare stub di funzioni in **Pubblica WebApplication.ps1**.

Per automatizzare la compilazione del progetto, aggiungere il codice che chiama MSBuild `New-WebDeployPackage` come illustrato nell'esempio di codice. Il percorso del comando MSBuild è diverso a seconda della versione di Visual Studio installata. Per ottenere il percorso corretto, è possibile utilizzare la funzione **Get-msbuildcmd come**, come illustrato nell'esempio seguente.

### Per automatizzare la compilazione del progetto

1. Aggiungere il parametro `$ProjectFile` nella sezione param globale.

    ```
    [Parameter(Mandatory = $false)]
    [ValidateScript({Test-Path $_ -PathType Leaf})]
    [String]
    $ProjectFile,
    ```

1. Copiare la funzione `Get-MSBuildCmd` nel file di script.

    ```
    function Get-MSBuildCmd
    {
            process
            {

                 $path =  Get-ChildItem "HKLM:\SOFTWARE\Microsoft\MSBuild\ToolsVersions" |
                                       Sort-Object {[double]$_.PSChildName} -Descending |
                                       Select-Object -First 1 |
                                       Get-ItemProperty -Name MSBuildToolsPath |
                                       Select -ExpandProperty MSBuildToolsPath
           
                $path = (Join-Path -Path $path -ChildPath 'msbuild.exe')

            return Get-Item $path
        }
    }
    ```

1. Sostituire `New-WebDeployPackage` con il seguente codice e sostituire i segnaposto per la costruzione di riga `$msbuildCmd`. Questo codice è per Visual Studio 2015. Se si utilizza Visual Studio 2013, modificare la proprietà **VisualStudioVersion** al di sotto di `12.0`.

    ```
    function New-WebDeployPackage
      {
        #Write a function to build and package your web application
          
        #To build your web application, use MsBuild.exe. For help, see MSBuild Command-Line Reference at: http://go.microsoft.com/fwlink/?LinkId=391339
          
        Write-VerboseWithTime 'Build-WebDeployPackage: Start'
          
        $msbuildCmd = '"{0}" "{1}" /T:Rebuild;Package /P:VisualStudioVersion=14.0 /p:OutputPath="{2}\MSBuildOutputPath" /flp:logfile=msbuild.log,v=d' -f (Get-MSBuildCmd), $ProjectFile, $scriptDirectory
          
        Write-VerboseWithTime ('Build-WebDeployPackage: ' + $msbuildCmd)
          
        #Start execution of the build command
        $job = Start-Process cmd.exe -ArgumentList('/C "' + $msbuildCmd + '"') -WindowStyle Normal -Wait -PassThru
          
        if ($job.ExitCode -ne 0)
        {
          throw('MsBuild exited with an error. ExitCode:' + $job.ExitCode)
        }

        #Obtain the project name
        $projectName = (Get-Item $ProjectFile).BaseName
          
        #Construct the path to web deploy zip package
        $DeployPackageDir =  '.\MSBuildOutputPath\_PublishedWebsites\{0}_Package\{0}.zip' -f $projectName
          
          
        #Get the full path for the web deploy zip package. This is required for MSDeploy to work
        $WebDeployPackage = Resolve-Path –LiteralPath $DeployPackageDir
          
        Write-VerboseWithTime 'Build-WebDeployPackage: End'
          
        return $WebDeployPackage
      }
    ```

1. Chiamare la funzione `New-WebDeployPackage` prima di questa riga: `$Config = Read-ConfigFile $Configuration` per le applicazioni web o `$Config = Read-ConfigFile $Configuration -HasWebDeployPackage:([Bool]$WebDeployPackage)` per le macchine virtuali.

    ```
    if($ProjectFile)
      {
        $WebDeployPackage = New-WebDeployPackage
      }
    ```

1. Richiamare uno script personalizzato dalla riga di comando mediante il passaggio dell’argomento `$Project`, come nella seguente riga di comando di esempio.

    ```
    .\Publish-WebApplicationVM.ps1 -Configuration .\Configurations\WebApplication5-VM-dev.json `
     -ProjectFile ..\WebApplication5\WebApplication5.csproj `
     -VMPassword @{Name="VMUser";Password="Test.123"} `
     -AllowUntrusted `
    -Verbose
    ```

    Per automatizzare il test dell'applicazione, aggiungere codice al `Test-WebApplication`. Assicurarsi di rimuovere il commento dalle righe in **Pubblica WebApplication.ps1** dove queste funzioni vengono chiamate. Se non si fornisce un'implementazione, è possibile compilare manualmente il progetto con Visual Studio e quindi eseguire lo script per pubblicare in Azure.

## Riepilogo della funzione di pubblicazione

Per visualizzare la Guida per le funzioni è possibile utilizzare il prompt dei comandi Windows PowerShell, utilizzare il comando `Get-Help function-name`. Nella Guida sono inclusi esempi e informazioni sui parametri. Lo stesso testo della Guida in linea è anche nei file di origine script **AzureWebAppPublishModule.psm1** e **pubblica WebApplication.ps1**. Lo script e la Guida si trovano nella lingua di Visual Studio.

**AzureWebAppPublishModule**

|Nome della funzione|Descrizione|
|---|---|
|Aggiungere AzureSQLDatabase|Creare un nuovo database SQL Azure.|
|Aggiungere AzureSQLDatabases|Creare database SQL Azure dai valori nel file di configurazione JSON generato da Visual Studio.|
|Aggiungere-AzureVM|Crea una macchina virtuale di Azure e restituisce l'URL della macchina virtuale distribuita. La funzione imposta i prerequisiti e quindi chiama la funzione **New-AzureVM** (modulo di Azure) per creare una nuova macchina virtuale.|
|Aggiungere AzureVMEndpoints|Aggiunge nuovi endpoint di input a una macchina virtuale e restituisce la macchina virtuale con il nuovo endpoint.|
|Aggiungere AzureVMStorage|Crea un nuovo account di archiviazione di Azure nella sottoscrizione corrente. Il nome dell'account inizia con "devtest" seguito da una stringa alfanumerica univoca. La funzione restituisce il nome del nuovo account di archiviazione. È necessario specificare un percorso o un gruppo di affinità per il nuovo account di archiviazione.|
|Aggiungere-AzureWebsite|Crea un sito Web con nome e percorso specificati. Questa funzione chiama la funzione **New-AzureWebsite** nel modulo di Azure. Se la sottoscrizione non include già un sito Web con il nome specificato, questa funzione crea il sito Web e restituisce un oggetto sito Web. In caso contrario, restituirà `$null`.|
|Backup-Sottoscrizione|Consente di salvare la sottoscrizione Azure corrente nella variabile`$Script:originalSubscription` nell'ambito dello script. Questa funzione consente di salvare la sottoscrizione Azure corrente (ottenuta da `Get-AzureSubscription -Current`) e il relativo account di archiviazione e la sottoscrizione viene modificata da questo script (archiviato nella variabile `$UserSpecifiedSubscription`) e il relativo account di archiviazione, nell'ambito dello script. Salvando i valori, è possibile utilizzare una funzione, ad esempio `Restore-Subscription`, per ripristinare l'account di archiviazione e di sottoscrizione corrente originale allo stato corrente se è stato modificato lo stato corrente.|
|Trovare-AzureVM|Ottiene la macchina virtuale di Azure specificata.|
|Formato DevTestMessageWithTime|Antepone la data e l’ora a un messaggio. Questa funzione è progettata per i messaggi scritti ai flussi di errore e dettagliati.|
|Get-AzureSQLDatabaseConnectionString|Assembla una stringa di connessione per connettersi a un database SQL Azure.|
|Get-AzureVMStorage|Restituisce il nome del primo account di archiviazione con il nome del modello "devtest *" (maiuscole e minuscole) nel percorso specificato o gruppo di affinità. Se l'account di archiviazione "devtest *" non corrisponde alla posizione o a un gruppo di affinità, la funzione lo ignora. È necessario specificare un percorso o un gruppo di affinità.|
|Get-MSDeployCmd|Restituisce un comando per eseguire lo strumento MsDeploy.exe.|
|Nuovo AzureVMEnvironment|Trova o crea una macchina virtuale nella sottoscrizione che corrisponde ai valori nel file di configurazione JSON.|
|Pubblicare-WebPackage|Utilizza MsDeploy.exe e un file Zip del pacchetto di pubblicazione web per distribuire le risorse a un sito Web. Questa funzione non genera alcun output. Se la chiamata a MSDeploy.exe non riesce, la funzione genera un'eccezione. Per ottenere un output più dettagliato, utilizzare l’opzione **-Verbose**.|
|Pubblicar-WebPackageToVM|Verifica i valori di parametro e chiama quindi la funzione **Publish-WebPackage**.|
|Leggere-configFile|Convalida il file di configurazione JSON e restituisce una tabella hash di valori selezionati.|
|Ripristina-Subscription|Reimposta la sottoscrizione corrente originale.|
|Test-AzureModule|Restituisce `$true` se la versione del modulo Azure installata è 0.7.4 o successiva. Restituisce `$false` Se il modulo non è installato o è una versione precedente. Questa funzione non ha parametri.|
|Test-AzureModuleVersion|Restituisce `$true` se la versione del modulo Azure è 0.7.4 o successiva. Restituisce `$false` se il modulo non è installato o è una versione precedente. Questa funzione non ha parametri.|
|Test-HttpsUrl|Converte l'URL di input in un oggetto System. Uri. Restituisce `$True` se l'URL è assoluto e il relativo schema è https. Restituisce `$false` se l'URL è relativo, lo schema non è HTTPS o la stringa di input non può essere convertita in un URL.|
|Test- Member|Restituisce `$true` se una proprietà o metodo è un membro dell'oggetto. In caso contrario, restituisce `$false`.|
|Scrivere-ErrorWithTime|Scrive un messaggio di errore prefisso con l'ora corrente. Questa funzione chiama la funzione **Format-DevTestMessageWithTime** per anteporre il tempo prima della scrittura del messaggio per il flusso di errore.|
|Scrivere-HostWithTime|Scrive un messaggio nel programma host (**Write-Host**) prestabilito con l'ora corrente. L'effetto della scrittura nel programma host varia. La maggior parte dei programmi che ospitano Windows PowerShell scrive questi messaggi nell'output standard.|
|Scrivere-VerboseWithTime|Scrive un messaggio dettagliato con l'ora corrente. Poiché chiama **Write-Verbose**, viene visualizzato il messaggio solo quando lo script viene eseguito con il parametro **Verbose** o quando la preferenza **VerbosePreference** è impostata su **Continua**.|

**Pubblicare-WebApplication**

|Nome della funzione|Descrizione|
|---|---|
|Nuovo-AzureWebApplicationEnvironment|Crea risorse di Azure, ad esempio una macchina virtuale o un sito Web.|
|Nuovo-WebDeployPackage|Questa funzione non è implementata. È possibile aggiungere comandi in questa funzione per compilare il progetto.|
|Pubblicare-AzureWebApplication|Pubblicare un'applicazione Web in Azure|
|Pubblicare-WebApplication|Crea e distribuisce le app Web, le macchine virtuali, i database SQL e gli account di archiviazione per un progetto web Visual Studio.|
|Test-WebApplication|Questa funzione non è implementata. È possibile aggiungere comandi in questa funzione per testare l’applicazione.|

## Passaggi successivi

Ulteriori informazioni sulla creazione di script PowerShell leggendo [Scripting con Windows PowerShell](https://technet.microsoft.com/library/bb978526.aspx) e altri script di Azure PowerShell nello [Script Center](https://azure.microsoft.com/documentation/scripts/).

<!---HONumber=AcomDC_1203_2015-->