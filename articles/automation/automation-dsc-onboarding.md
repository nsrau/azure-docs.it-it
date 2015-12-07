<properties 
   pageTitle="Caricamento di computer fisici e macchine virtuali per la gestione con Automation DSC per Azure | Microsoft Azure" 
   description="Come configurare i computer per la gestione con Automation DSC per Azure" 
   services="automation" 
   documentationCenter="dev-center-name" 
   authors="coreyp-at-msft" 
   manager="stevenka" 
   editor="tysonn"/>

<tags
   ms.service="automation"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="TBD" 
   ms.date="11/23/2015"
   ms.author="coreyp"/>

# Caricamento di computer per la gestione con Automation DSC per Azure

## Perché gestire computer con Automation DSC per Azure

Come [PowerShell DSC (Desired State Configuration)](https://technet.microsoft.com/library/dn249912.aspx), Automation DSC (Desired State Configuration) per Azure è un servizio semplice ma potente per la gestione della configurazione dei nodi DSC (macchine virtuali e computer fisici) in qualsiasi data center nel cloud o locale. Consente la scalabilità tra migliaia di computer in modo rapido e facile da una posizione centrale e sicura. È possibile caricare facilmente i computer, assegnare agli stessi configurazioni dichiarative e visualizzare report che mostrano la conformità di ogni computer con lo stato desiderato specificato. Il livello di gestione di Automation DSC per Azure è per DSC ciò che il livello di gestione di Automazione di Azure rappresenta per gli script di PowerShell. In altre parole, così come Automazione di Azure facilita la gestione degli script di PowerShell, facilita anche la gestione delle configurazioni DSC. Per altre informazioni sui vantaggi che derivano dall'uso di Automation DSC per Azure, vedere [Panoramica di Automation DSC per Azure](automation-dsc-overview/).

È possibile usare Automation DSC per Azure in molti tipi di computer.

*    Macchine virtuali di Azure (classica)
*    Macchine virtuali di Azure
*    Computer fisici/macchine virtuali Windows locali o in un cloud diverso da Azure
*    Computer fisici/macchine virtuali Linux locali, in Azure o in un cloud diverso da Azure

Le sezioni seguenti illustrano come caricare ogni tipo di computer in Automation DSC per Azure.

## Macchine virtuali di Azure (classica)

Con Automation DSC per Azure è possibile caricare facilmente macchine virtuali di Azure (classica) per la gestione della configurazione tramite il portale di Azure o PowerShell. Dietro le quinte, e senza che l'amministratore debba connettersi in remoto alla VM, l'estensione DSC (Desired State Configuration) per le VM di Azure regista la VM con Automation DSC per Azure. Poiché l'estensione DSC per le VM di Azure viene eseguita in modalità asincrona, nella sezione seguente [**Risoluzione dei problemi di caricamento delle macchine virtuali di Azure**](#troubleshooting-azure-virtual-machine-onboarding) è disponibile la procedura per tenere traccia dell'avanzamento o risolverne i problemi.


### Portale di Azure

Nel [portale di anteprima di Azure](http://portal.azure.com/) fare clic su **Esplora** -> **Macchine virtuali (classica)**. Selezionare la macchina virtuale Windows da caricare. Nel pannello del dashboard della macchina virtuale fare clic su **Tutte le impostazioni** -> **Estensioni** -> **Aggiungi** -> **Automation DSC per Azure** -> **Crea**. Immettere i [valori di Gestione configurazione locale per PowerShell DSC](https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396) necessari per il proprio caso d'uso, la chiave e l'URL di registrazione dell'account di automazione e facoltativamente una configurazione del nodo da assegnare alla VM.

![](./media/automation-dsc-onboarding/DSC_Onboarding_1.png)


Per trovare l'URL e la chiave di registrazione dell'account di automazione in cui caricare la macchina virtuale, vedere la sezione [**Registrazione sicura**](#secure-registration) di seguito.

### PowerShell

    # log in to both Azure Service Management and Azure Resource Manager
    Add-AzureAccount
    Login-AzureRmAccount
    
    # fill in correct values for your VM / Automation Account here
    $VMName = ""
    $ServiceName = ""
    $AutomationAccountName = ""
    $AutomationAccountResourceGroup = ""

    # fill in the name of a Node Configuration in Azure Automation DSC, for this VM to conform to
    $NodeConfigName = ""

    # get Azure Automation DSC registration info
    $Account = Get-AzureRmAutomationAccount -ResourceGroupName $AutomationAccountResourceGroup -Name $AutomationAccountName
    $RegistrationInfo = $Account | Get-AzureRmAutomationRegistrationInfo

    # use the DSC extension to onboard the VM for management with Azure Automation DSC
    $VM = Get-AzureVM -Name $VMName -ServiceName $ServiceName
    
    $PublicConfiguration = ConvertTo-Json -Depth 8 @{
      SasToken = ""
      ModulesUrl = "https://eus2oaasibizamarketprod1.blob.core.windows.net/automationdscpreview/RegistrationMetaConfigV2.zip"
      ConfigurationFunction = "RegistrationMetaConfigV2.ps1\RegistrationMetaConfigV2"

    # update these DSC agent Local Configuration Manager defaults if they do not match your use case.
    # See https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396 for more details
    Properties = @{
       RegistrationKey = @{
         UserName = 'notused'
         Password = 'PrivateSettingsRef:RegistrationKey'
    }
      RegistrationUrl = $RegistrationInfo.Endpoint
      NodeConfigurationName = $NodeConfigName
      ConfigurationMode = "ApplyAndMonitor"
      ConfigurationModeFrequencyMins = 15
      RefreshFrequencyMins = 30
      RebootNodeIfNeeded = $False
      ActionAfterReboot = "ContinueConfiguration"
      AllowModuleOverwrite = $False
      }
    }

    $PrivateConfiguration = ConvertTo-Json -Depth 8 @{
      Items = @{
         RegistrationKey = $RegistrationInfo.PrimaryKey
      }
    }
    
    $VM = Set-AzureVMExtension `
     -VM $vm `
     -Publisher Microsoft.Powershell `
     -ExtensionName DSC `
     -Version 2.6 `
     -PublicConfiguration $PublicConfiguration `
     -PrivateConfiguration $PrivateConfiguration `
     -ForceUpdate

    $VM | Update-AzureVM

## Macchine virtuali di Azure

Automation DSC per Azure consente di caricare facilmente macchine virtuali di Azure per la gestione della configurazione tramite il portale di Azure, i modelli di Gestione risorse di Azure o PowerShell. Dietro le quinte, e senza che l'amministratore debba connettersi in remoto alla VM, l'estensione DSC (Desired State Configuration) per le VM di Azure regista la VM con Automation DSC per Azure. Poiché l'estensione DSC per le VM di Azure viene eseguita in modalità asincrona, nella sezione seguente [**Risoluzione dei problemi di caricamento delle macchine virtuali di Azure**](#troubleshooting-azure-virtual-machine-onboarding) è disponibile la procedura per tenere traccia dell'avanzamento o risolverne i problemi.


### Portale di Azure

Nel [portale di anteprima di Azure](http://portal.azure.com/) passare all'account di Automazione di Azure in cui caricare le macchine virtuali. Nel dashboard dell'account di automazione fare clic su **Nodi DSC** -> **Aggiungi VM di Azure**.

In **Seleziona macchine virtuali da caricare** selezionare una o più macchine virtuali di Azure da caricare.

![](./media/automation-dsc-onboarding/DSC_Onboarding_2.png)


In **Configura dati di registrazione** immettere i [valori di Gestione configurazione locale per PowerShell DSC](https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396) necessari per il proprio caso d'uso e facoltativamente una configurazione del nodo da assegnare alla VM.

![](./media/automation-dsc-onboarding/DSC_Onboarding_3.png)

 
### Modelli di Gestione risorse di Azure

Le macchine virtuali di Azure possono essere distribuite e caricate in Automation DSC di Azure usando i modelli di Gestione risorse di Azure. Per un modello di esempio che carica una VM esistente in Automation DSC per Azure, vedere [Configurare una VM tramite l'estensione DSC e Automation DSC per Azure](http://azure.microsoft.com/documentation/templates/dsc-extension-azure-automation-pullserver/). Per trovare la chiave e l'URL di registrazione accettati come input in questo modello, vedere la sezione [**Registrazione sicura**](#secure-registration) di seguito.

### PowerShell

Il cmdlet [Register-AzureRmAutomationDscNode](https://msdn.microsoft.com/library/mt603833.aspx) può essere usato per caricare macchine virtuali nel portale di anteprima di Azure tramite PowerShell.

## Computer fisici/macchine virtuali Windows locali o in un cloud diverso da Azure

I computer Windows locali e le macchine virtuali Windows in cloud non di Azure, ad esempio Amazon Web Services, possono essere caricati in Automation DSC per Azure con pochi e semplici passaggi, a condizione che abbiano l'accesso in uscita a Internet:

1. Assicurarsi che nei computer da caricare in Automation DSC per Azure sia installata la versione più recente di [WMF 5](http://www.microsoft.com/it-IT/download/details.aspx?id=48729).

2. Aprire la console di PowerShell o PowerShell ISE come amministratore in un computer nell'ambiente locale. In questo computer deve essere installata anche la versione più recente di WMF 5.

3. Connettersi a Gestione risorse di Azure usando il modulo Azure PowerShell:`Login-AzureRmAccount`

4. Dall'account di automazione in cui si caricheranno i nodi scaricare le metaconfigurazioni di PowerShell DSC per i computer che da caricare:

	`Get-AzureRmAutomationDscOnboardingMetaconfig -ResourceGroupName MyResourceGroup -AutomationAccountName      		MyAutomationAccount -ComputerName MyServer1, MyServer2 -OutputFolder C:\Users\joe\Desktop`

5. Facoltativamente, visualizzare e aggiornare le metaconfigurazioni nella cartella di output, se necessario, in modo che corrispondano ai [valori e ai campi di Gestione configurazione locale per PowerShell DSC](https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396), se i valori predefiniti non corrispondono al proprio caso d'uso.

6. Applicare in remoto la metaconfigurazione di PowerShell DSC ai computer da caricare:

	`Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2`

7. Se non è possibile applicare le metaconfigurazioni di PowerShell DSC in remoto, copiare la cartella di output dal passaggio 4 in ogni computer da caricare. Chiamare quindi Set-DscLocalConfigurationManager localmente in ogni computer da caricare.

8. Tramite il portale di Azure o i cmdlet verificare che ora i computer da caricare siano visualizzati come nodi DSC registrati nell'account di Automazione di Azure.

## Computer fisici/macchine virtuali Linux locali, in Azure o in un cloud diverso da Azure

I computer Linux locali, i computer Linux in Azure, e le macchine virtuali Linux in cloud non di Azure possono essere caricati in Automation DSC per Azure con pochi e semplici passaggi, a condizione che abbiano l'accesso in uscita a Internet:

1. Assicurarsi che nei computer da caricare in Automation DSC per Azure sia installata la versione più recente dell'[agente DSC per Linux](http://www.microsoft.com/it-IT/download/details.aspx?id=49150).

2. Se i [valori predefiniti di Gestione configurazione locale per PowerShell DSC](https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396) corrispondono al proprio caso d'uso:

	*    In tutti i computer Linux da caricare in Automation DSC per Azure usare Register.py per caricarli con i valori predefiniti di Gestione configurazione locale per PowerShell DSC:

		`/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

	*    Per trovare la chiave e l'URL di registrazione per l'account di automazione, vedere la sezione [**Registrazione sicura**](#secure-registration) di seguito.

	Se i valori predefiniti di Gestione configurazione locale per PowerShell DSC **non** **corrispondono** al proprio caso d'uso, seguire i passaggi da 3 a 9. In caso contrario, andare direttamente al passaggio 9.

3. Aprire la console di PowerShell o PowerShell ISE come amministratore in un computer Windows nell'ambiente locale. In questo computer deve essere installata la versione più recente di [WMF 5](http://www.microsoft.com/it-IT/download/details.aspx?id=48729).

4. Connettersi a Gestione risorse di Azure usando il modulo Azure Powershell:

	`Login-AzureRmAccount`

5.  Dall'account di automazione in cui si caricheranno i nodi scaricare le metaconfigurazioni di PowerShell DSC per i computer che da caricare:
	
	`Get-AzureRmAutomationDscOnboardingMetaconfig -ResourceGroupName MyResourceGroup -AutomationAccountName MyAutomationAccount -ComputerName MyServer1, MyServer2 -OutputFolder C:\Users\joe\Desktop_`

6.  Facoltativamente, visualizzare e aggiornare le metaconfigurazioni nella cartella di output, se necessario, in modo che corrispondano ai [valori e ai campi di Gestione configurazione locale per PowerShell DSC](http://https://technet.microsoft.com/library/dn249922.aspx?f=255&MSPPError=-2147217396), se i valori predefiniti non corrispondono al proprio caso d'uso.

7.  Applicare in remoto la metaconfigurazione di PowerShell DSC ai computer da caricare:
    	
    	$SecurePass = ConvertTo-SecureString -string "<root password>" -AsPlainText -Force
        $Cred = New-Object System.Management.Automation.PSCredential "root", $SecurPass
        $Opt = New-CimSessionOption -UseSs1:$true -SkipCACheck:$true -SkipCNCheck:$true -SkipRevocationCheck:$true

        # need a CimSession for each Linux machine to onboard
        
        $Session = New-CimSession -Credential:$Cred -ComputerName:<your Linux machine> -Port:5986 -Authentication:basic -SessionOption:$Opt
    	
    	Set-DscLocalConfigurationManager -CimSession $Session –Path C:\Users\joe\Desktop\DscMetaConfigs

8.  Se non è possibile caricare le metaconfigurazioni di PowerShell DSC in remoto, per ogni computer Linux da caricare copiare la metaconfigurazione corrispondente a quel computer dalla cartella nel passaggio 5 nel computer Linux. Chiamare quindi `SetDscLocalConfigurationManager.py` localmente in ogni computer Linux da caricare in Automation DSC per Azure:

	`/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py –configurationmof <path to metaconfiguration file>`

9.  Tramite il portale di Azure o i cmdlet verificare che ora i computer da caricare siano visualizzati come nodi DSC registrati nell'account di Automazione di Azure.

##Registrazione sicura

I computer possono essere caricati in modo sicuro in un account di Automazione di Azure tramite il protocollo di registrazione DSC WMF 5, che consente a un nodo DSC di autenticarsi con un server di pull o di report di PowerShell DSC V2, incluso Automation DSC per Azure. Il nodo si registra con il server con un **URL di registrazione**, autenticandosi con una **Chiave di registrazione**. Durante la registrazione, il nodo DSC e il server di pull/report DSC negoziano un certificato univoco per questo nodo da usare per l'autenticazione con il server successivamente alla registrazione. Questo processo impedisce ai nodi caricati di rappresentarsi reciprocamente, ad esempio nel caso di un nodo compromesso e che presenta un comportamento dannoso. Dopo la registrazione, la chiave di registrazione non viene usata di nuovo per l'autenticazione e viene eliminata dal nodo.

È possibile ottenere le informazioni richieste per il protocollo di registrazione DSC nel pannello **Gestisci chiavi** del portale di anteprima di Azure. Aprire questo pannello facendo clic sull'icona della chiave nel pannello **Informazioni di base** dell'account di automazione.

![](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

*    L'URL di registrazione si trova nel campo URL del pannello Gestisci chiavi.
*    La chiave di registrazione si trova nel campo Chiave di accesso primaria o Chiave di accesso secondaria del pannello Gestisci chiavi. È possibile usare una delle due chiavi.

Per maggiore sicurezza, le chiavi di accesso primaria e secondaria di un account di automazione possono essere rigenerate in qualsiasi momento, tramite il pannello **Gestisci chiavi**, per evitare l'esecuzione di registrazioni di nodi future con chiavi precedenti.

##Risoluzione dei problemi di caricamento di macchine virtuali di Azure

Automation DSC di Azure consente di caricare macchine virtuali Windows di Azure per la gestione della configurazione. Dietro le quinte, viene usata l'estensione DSC (Desired State Configuration) per le VM di Azure per registrare la VM con Automation DSC per Azure. Poiché l'estensione DSC (Desired State Configuration) per le VM di Azure viene eseguita in modalità asincrona, può essere importante tenere traccia dell'avanzamento e risolvere i problemi di esecuzione.

>[AZURE.NOTE]Qualsiasi metodo di caricamento di una VM Windows di Azure in Automation DSC per Azure che usa l'estensione DSC (Desired State Configuration) per le VM di Azure può richiedere fino a un'ora prima che il modo appaia come registrato in Automazione di Azure. Questo comportamento è causato dall'installazione di Windows Management Framework 5.0 nella macchina virtuale da parte dell'estensione DSC per le VM di Azure, necessario per caricare la VM in Automation DSC per Azure.

Per risolvere i problemi o visualizzare lo stato dell'estensione DSC per le VM di Azure, nel portale di anteprima di Azure passare alla VM da caricare, quindi fare clic -> **Tutte le impostazioni** -> **Estensioni** -> **DSC**. Per altri dettagli, è possibile fare clic su **Visualizza stato dettagliato**.

[![](./media/automation-dsc-onboarding/DSC_Onboarding_5.png)](https://technet.microsoft.com/library/dn249912.aspx)

## Scadenza del certificato e nuova registrazione

Dopo la registrazione, ogni nodo negozia automaticamente un certificato univoco per l'autenticazione che scade dopo un anno. Attualmente il protocollo di registrazione di PowerShell DSC non può rinnovare automaticamente i certificati quando si avvicina la scadenza, quindi è necessario registrare di nuovo i nodi dopo un anno. Prima di registrare di nuovo, verificare che ogni nodo esegua Windows Management Framework 5.0 RTM. Se il certificato di autenticazione di un nodo scade, e se il nodo non è registrato, il nodo non sarà in grado di comunicare con Automazione di Azure e sarà indicato che "Non risponde". La registrazione viene eseguita nello stesso modo con cui il nodo è stato registrato inizialmente. Una registrazione eseguita 90 giorni o meno dall'ora di scadenza del certificato, o in qualsiasi momento dopo l’ora di scadenza del certificato. comporterà un nuovo certificato che viene generato e utilizzato.

## Articoli correlati
* [Panoramica di Automation DSC per Azure](automation-dsc-overview.md)
* [Cmdlet di Automation DSC per Azure](https://msdn.microsoft.com/library/mt244122.aspx)
* [Prezzi di Automation DSC per Azure](http://azure.microsoft.com/pricing/details/automation/)

<!---HONumber=AcomDC_1125_2015-->