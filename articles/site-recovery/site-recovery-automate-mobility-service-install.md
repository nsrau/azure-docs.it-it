<properties
	pageTitle="Eseguire la replica di macchine virtuali VMware in Azure con Automation DSC per Azure | Microsoft Azure"
	description="Descrive come usare Automation DSC per Azure per distribuire automaticamente il servizio Mobility di ASR e l'agente di Azure per le macchine virtuali o i computer fisici in Azure."
	services="site-recovery"
	documentationCenter=""
	authors="krnese"
	manager="lorenr"
	editor=""/>  

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/26/2016"
	ms.author="krnese"/>

# Replicare le VM VMware in Azure tramite Site Recovery con Automation DSC per Azure

In OMS offriamo una soluzione completa per il backup e il ripristino di emergenza che può essere integrata nel piano di continuità aziendale.

Abbiamo iniziato questo percorso con Hyper-V usando la replica Hyper-V, ampliando tuttavia lo scenario per supportare un'installazione eterogenea poiché siamo consapevoli che il cloud dei clienti includa più hypervisor e piattaforme.

Se attualmente si eseguono carichi di lavoro VMware o/e server fisici, facciamo affidamento su un server di gestione che esegue tutti i componenti di Site Recovery in esecuzione nell'ambiente per gestire la comunicazione e la replica di dati con Azure, quando Azure è la destinazione.

## Distribuire il servizio Mobility di ASR con OMS Automation DSC
Iniziamo riepilogando le operazioni eseguite da questo server di gestione:

Il server di gestione esegue diversi ruoli del server, ad esempio quello di **configurazione** coordinando le comunicazioni e gestendo i processi di ripristino e di replica dei dati.

Inoltre, il ruolo di **elaborazione** agisce come un gateway di replica che riceve i dati di replica dai computer di origine protetti, li ottimizza attraverso la memorizzazione nella cache, la compressione e la crittografia e li invia all'account di archiviazione di Azure. Una delle funzioni del ruolo di elaborazione è anche quella di eseguire il push dell'installazione del servizio Mobility nei computer protetti ed eseguire l'individuazione automatica delle VM VMware.

In caso di failback da Azure, il ruolo di **destinazione master** sarà responsabile e gestirà i dati di replica come parte di questa operazione.

Per i computer protetti, facciamo affidamento sul **servizio Mobility**, un componente distribuito in ogni macchina (VM VMware o server fisico) di cui si vuole eseguire la replica in Azure. Il suo ruolo è quello di acquisire scritture di dati sul computer e inoltrarle al server di gestione (ruolo di elaborazione).

Nell'ambito della continuità aziendale è importante comprendere i carichi di lavoro, l'infrastruttura e i componenti coinvolti per soddisfare i requisiti per gli obiettivi RTO e RPO. In questo contesto, il servizio Mobility è fondamentale per garantire che i carichi di lavoro siano protetti nel modo previsto.

Come possiamo garantire nel modo migliore che l'installazione sia affidabile e protetta usando alcuni componenti di OMS?

L'esempio in questo articolo illustra come è possibile usare OMS Automation DSC insieme a OMS Site Recovery per assicurarsi che il servizio Mobility e l'agente VM di Azure vengano distribuiti sui computer Windows da proteggere e che siano sempre in esecuzione, quando Azure è la destinazione della replica.

## Prerequisiti

- Un repository per archiviare la configurazione necessaria
- Un repository per archiviare la passphrase necessaria per la registrazione al server di gestione
- La soluzione Windows Management Framework 5.0 installata sui computer di cui che si vuole abilitare la protezione (requisito per OMS Automation DSC)

Se si vuole usare DSC per i computer Windows usando la soluzione WMF 4.0, vedere la sezione relativa all'uso di DSC negli ambienti non connessi

Per ogni server di gestione viene generata una passphrase univoca. Se si intende distribuire più server di gestione, è necessario assicurarsi che la passphrase corretta venga archiviata nel file passphrase txt.

Il servizio Mobility può essere installato tramite la riga di comando e accetta diversi argomenti.

Ecco perché è necessario disporre dei file binari (dopo averli estratti dalla configurazione) e archiviarli in un punto in cui è possibile recuperarli usando una configurazione DSC.

## Passaggio 1: estrazione dei file binari

1. Per estrarre i file necessari per questa configurazione, passare alla directory seguente sul server di gestione: **\\Microsoft Azure Site Recovery\\home\\svsystems\\pushinstallsvc\\repository**

    In questa cartella dovrebbe essere presente un file MSI denominato:

    **Microsoft-ASR\_UA\_<versione>_Windows\_GA_<data>\_Release.exe**

    Per estrarre il programma di installazione usare il cmdlet seguente:

    .\\Microsoft-ASR\_UA\_9.1.0.0_Windows\_GA_02May2016\_release.exe /q /x:C:\\Users\\Administrator\\Desktop\\Mobility\_Service\\Extract

2. Selezionare tutti i file e inviarli in una cartella compressa.

La procedura è terminata. Ora sono disponibili i file binari necessari per automatizzare l'installazione del servizio Mobility con OMS Automation DSC.

### Passphrase
È quindi necessario scegliere dove si vuole posizionare la cartella compressa. In questo caso userò un account di archiviazione in Azure in cui ho inserito anche la passphrase necessaria per la configurazione, in modo che l'agente eseguirà la registrazione al server di gestione come parte del processo.

La passphrase ottenuta quando si distribuisce il server di gestione può essere salvata in un file txt come passphrase.txt.

Ho inserito sia la cartella compressa sia la passphrase in un contenitore dedicato nel mio account di archiviazione di Azure

![percorso della cartella](./media/site-recovery-automate-mobilitysevice-install/folder-and-passphrase-location.png)

Se si preferisce, è possibile mantenere questi file in una condivisione all'interno della rete. È sufficiente garantire che la risorsa DSC che useremo in un secondo momento abbia effettivamente accesso e possa usare la configurazione e la passphrase.

## Passaggio 2: creazione della configurazione DSC
La mia configurazione dipende dalla soluzione WMF 5.0, ovvero affinché il computer applichi correttamente la configurazione tramite OMS Automation DSC è necessario che sia presente la soluzione WMF 5.0.

Nell'ambiente la configurazione DSC usata è la seguente:

```
configuration ASRMobilityService {

    $RemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    $RemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    $RemoteAzureAgent = 'http://go.microsoft.com/fwlink/p/?LinkId=394789'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role ' + '"' + $Role + '"' + ' /InstallLocation ' + '"' + $Install + '"' + ' /CSEndpoint ' + '"' + $CSEndpoint + '"' + ' /PassphraseFilePath ' +  '"' +$LocalPassphrase + '"'  

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node localhost {

        xRemoteFile Setup {
           URI = $RemoteFile
           DestinationPath = $TempDestination
           DependsOn = "[File]Directory"
            }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = "[File]Directory"
            }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = "[File]Directory"
            }

        File Directory {
            DestinationPath = "C:\Temp\ASRSetup"
            Type = "Directory"            
            }

        Archive ASRzip {
           Path = $TempDestination
           Destination = "C:\Temp\ASRSetup"
           DependsOn = "[xRemotefile]Setup"
           }

        Package Install {
            Path = "C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE"
            Ensure = "Present"
            Name = "Microsoft Azure Site Recovery mobility Service/Master Target Server"
            ProductId = "275197FC-14FD-4560-A5EB-38217F80CBD1"
            Arguments = $Arguments
            DependsOn = "[Archive]ASRzip"
            }

        Package AzureAgent {
            Path = "C:\Temp\AzureVmAgent.msi"
            Ensure = "Present"
            Name = "Windows Azure VM Agent - 2.7.1198.735"
            ProductId = "5CF4D04A-F16C-4892-9196-6025EA61F964"
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = "[Package]Install"
            }

        Service ASRvx {
            Name = "svagents"
            Ensure = "Present"
            State = "Running"
            DependsOn = "[Package]Install"
            }

        Service ASR {
            Name = "InMage Scout Application Service"
            Ensure = "Present"
            State = "Running"
            DependsOn = "[Package]Install"
            }

        Service AzureAgentService {
            Name = "WindowsAzureGuestAgent"
            Ensure = "Present"
            State = "Running"
            DependsOn = "[Package]AzureAgent"
            }

        Service AzureTelemetry {
            Name = "WindowsAzureTelemetryService"
            Ensure = "Present"
            State = "Running"
            DependsOn = "[Package]AzureAgent"
            }
    }
}
```
La configurazione eseguirà le operazioni seguenti:

- Le variabili indicheranno alla configurazione dove ottenere i file binari per il servizio Mobility e l'agente VM di Azure, la passphrase e il percorso in cui archiviare l'output.
- Verrà importato il modulo xPSDesiredStateConfiguration DscResource in modo da poter usare "xRemoteFile" per scaricare i file dal repository.
- Verrà creata una directory in cui si vuole archiviare i file binari.
- La risorsa di archiviazione estrarrà i file dalla cartella compressa.
- La risorsa di installazione del pacchetto installerà il servizio Mobility dal programma di installazione UNIFIEDAGENT.EXE con argomenti specifici; le variabili negli argomenti devono essere modificate in modo da riflettere l'ambiente.
- Il pacchetto "AzureAgent" installerà l'agente VM di Azure consigliato in ogni VM in esecuzione in Azure e consentirà di aggiungere estensioni per le attività successive al failover della VM.
- Le risorse del servizio garantiranno che i servizi Mobility correlati e i servizi di Azure siano sempre in esecuzione.

Ho salvato la configurazione come **ASRMobilityService** in una cartella nel mio computer.

Ricordarsi di sostituire il valore CSIP nella configurazione per riflettere il server di gestione effettivo, in modo che l'agente venga connesso in modo corretto usando anche la passphrase corretta.

## Passaggio 3: caricamento di OMS Automation DSC

La configurazione DSC creata importerà il modulo di risorse DSC xPSDesiredStateConfiguration necessario ed è quindi necessario importare il modulo in OMS Automation prima di caricare la configurazione DSC.

Accedere all'account di automazione, passare a Risorse>Moduli e fare clic su Sfoglia raccolta.

Qui è possibile cercare il modulo e importarlo nel proprio account.

![importazione del modulo](./media/site-recovery-automate-mobilitysevice-install/search-and-import-module.png)  

Al termine passare al computer in cui sono installati i moduli di Azure RM e continuare a importare la configurazione DSC appena creata.

### Cmdlet di importazione

In PowerShell accedere alla sottoscrizione di Azure e modificare i cmdlet in modo da riflettere l'ambiente.

Prima carico la configurazione di OMS Automation DSC usando il cmdlet seguente:

```
Import-AzureRmAutomationDscConfiguration `
                                        -AutomationAccountName KNOMSAA `
                                        -ResourceGroupName KNOMS `
                                        -SourcePath C:\ASR\ASRMobilityService.ps1 `
                                        -Published `
                                        -Description "DSC Config for Mobility Service"
```

È quindi necessario compilare la configurazione in OMS Automation DSC, in modo che sia possibile iniziare a registrare i nodi per essa.

### Compilare la configurazione in OMS Automation DSC

Questo risultato si ottiene eseguendo il cmdlet seguente:

```
Start-AzureRmAutomationDscCompilationJob `
                                        -AutomationAccountName KNOMSAA `
                                        -ResourceGroupName KNOMS `
                                        -ConfigurationName ASRMobilityService
```

Questa operazione può richiedere alcuni minuti poiché stiamo distribuendo la configurazione al servizio di pull DSC ospitato.

Al termine è possibile recuperare le informazioni sul processo tramite PowerShell (Get-AzureRmAutomationDscCompilationJob) o usare portal.azure.com

![Recuperare il processo](./media/site-recovery-automate-mobilitysevice-install/retrieve-job.png)  

Abbiamo ora pubblicato e caricato correttamente la configurazione DSC in OMS Automation DSC.

## Passaggio 4: caricamento di macchine in OMS Automation DSC
*Uno dei prerequisiti per il completamento di questo scenario è che la versione della soluzione WMF nei computer Windows sia aggiornata. È possibile scaricare e installare la versione corretta per la piattaforma al seguente URL: https://www.microsoft.com/download/details.aspx?id=50395*

Si creerà ora una metaconfig per DSC che applicheremo ai nodi. Per un risultato positivo è necessario recuperare l'URL e la chiave primaria dell'endpoint per l'account di automazione selezionato in Azure.

Questi valori si trovano in "Chiavi" nel pannello "Tutte le impostazioni" dell'account di automazione.

![Valori chiave](./media/site-recovery-automate-mobilitysevice-install/key-values.png)  

Nell'ambiente è presente un server fisico Windows Server 2012 R2 che intendo proteggere con OMS Site Recovery.

Prima di iniziare ad associare il server con l'endpoint Automation DSC, è consigliabile controllare le operazioni di ridenominazione dei file in sospeso nel Registro di sistema in quanto un riavvio in sospeso potrebbe impedire il completamento della configurazione.

### Verificare la presenza di eventuali operazioni di ridenominazione di file in sospeso nel Registro di sistema

Per verificare che non vi sia alcun riavvio in sospeso nel server, eseguire il cmdlet seguente:

```
Get-ItemProperty "HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager" | select PendingFileRenameOperations
```
Se non è presente, è possibile continuare. In caso contrario, è necessario riavviare il server in una finestra di manutenzione.

Per applicare la configurazione sul server, avvio PowerShell ISE e viene eseguito lo script seguente. Lo script è essenzialmente una configurazione locale per DSC che indicherà al motore di gestione configurazione locale di eseguire la registrazione con il servizio OMS Automation DSC e recuperare la configurazione specifica (ASRMobilityService.localhost)

```
[DSCLocalConfigurationManager()]

Configuration metaconfig
{
    node localhost
    {

        Settings
        {
            RefreshFrequencyMins = '30'
            RebootNodeIfNeeded = $true
            RefreshMode = 'PULL'
            ActionAfterReboot = 'ContinueConfiguration'
            ConfigurationMode = 'ApplyAndMonitor'
            AllowModuleOverwrite = $true
        }
                ResourceRepositoryWeb AzureAutomationDSC
            {
                ServerURL = 'https://we-agentservice-prod-1.azure-automation.net/accounts/4d54e439-8540-4c8f-8e0f-3d7afdda6746'
                RegistrationKey = 'KaTg5zTtI/gf8OGiN28L9FT2gMbowzOv3P9rouQfhxcR+42Mac80QKxOFXYLMdKxRdz8QtJiXLb7noG1U4zJ6g=='
            }
                ConfigurationRepositoryWeb AzureAutomationDSC
            {
                ServerURL = 'https://we-agentservice-prod-1.azure-automation.net/accounts/4d54e439-8540-4c8f-8e0f-3d7afdda6746'
                RegistrationKey = 'KaTg5zTtI/gf8OGiN28L9FT2gMbowzOv3P9rouQfhxcR+42Mac80QKxOFXYLMdKxRdz8QtJiXLb7noG1U4zJ6g=='
                ConfigurationNames = 'ASRMobilityService.localhost'
            }
                ReportServerWeb AzureAutomationDSC
            {
                ServerURL = 'https://we-agentservice-prod-1.azure-automation.net/accounts/4d54e439-8540-4c8f-8e0f-3d7afdda6746'
                RegistrationKey = 'KaTg5zTtI/gf8OGiN28L9FT2gMbowzOv3P9rouQfhxcR+42Mac80QKxOFXYLMdKxRdz8QtJiXLb7noG1U4zJ6g=='
            }

    }
}
metaconfig

Set-DscLocalConfigurationManager .\metaconfig -Force -Verbose
```

Questa configurazione eseguirà la gestione della configurazione locale per la registrazione a OMS Automation DSC e indicherà sostanzialmente come il motore deve funzionare, procedere in caso di una deviazione della configurazione (ApplyAndAutoCorrect) e nel caso sia necessario un riavvio, procedere con la configurazione in un secondo momento.

Al termine di queste operazioni il nodo dovrebbe iniziare la registrazione ad Automation DSC

![Registrare il nodo](./media/site-recovery-automate-mobilitysevice-install/register-node.png)

Se si torna a portal.azure.com, noteremo che il nodo appena registrato è ora presente nel portale.

![Registrare il nodo](./media/site-recovery-automate-mobilitysevice-install/registered-node.png)  

Per verificare che la registrazione sia stata eseguita correttamente, è possibile eseguire il cmdlet PowerShell seguente sul server:

```
Get-DscLocalConfigurationManager
```

Dopo che il pull della configurazione è stato eseguito e la configurazione è stata applicata al server, procedere alla verifica eseguendo:

```
Get-DscConfigurationStatus
```

L'output mostra che il server ha eseguito correttamente il pull della configurazione:

![Registrare il nodo](./media/site-recovery-automate-mobilitysevice-install/successful-config.png)  

Inoltre, la configurazione del servizio Mobility dispone di un proprio log in "<UnitàSistema>\\ProgramData\\ASRSetupLogs".

La procedura è terminata: il servizio Mobility è stato distribuito e registrato correttamente sul computer da proteggere con Site Recovery ed è possibile fare affidamento su DSC per fare in modo che i servizi necessari siano sempre in esecuzione.

![Registrare il nodo](./media/site-recovery-automate-mobilitysevice-install/successful-install.png)  

Una volta che queste operazioni sono state rilevate dal server di gestione, procedere per configurare la protezione e abilitare la replica nel computer con Site Recovery.

## Uso di DSC in ambienti non connessi

Se le macchine non sono connesse a Internet, è comunque possibile fare affidamento su DSC per distribuire e configurare il servizio Mobility sui carichi di lavoro da proteggere.

È possibile creare un'istanza del server di pull DSC nell'ambiente che fornirà le stesse funzionalità disponibili con OMS Automation DSC, con i client che eseguiranno il pull della configurazione dopo la registrazione all'endpoint DSC. Esiste tuttavia un'altra opzione che prevede l'uso del push, in cui si esegue manualmente il push della configurazione DSC sulle macchine, in locale e in remoto.

Si noti che in questo esempio è stato aggiunto un parametro per computername e i file remoti si trovano ora in una condivisione remota che deve essere accessibile alle macchine da proteggere; alla fine dello script applichiamo la configurazione e quindi iniziamo ad applicare la configurazione DSC nel computer di destinazione.

### Prerequisiti

·Installazione del modulo xPSDesiredStateConfiguration di PowerShell

Per i computer Windows in cui è installata la soluzione WMF 5.0, è possibile installare semplicemente il modulo xPSDesiredStateConfiguration eseguendo il cmdlet seguente nel computer o nei computer di destinazione:

```
Find-Module -Name xPSDesiredStateConfiguration | Install-Module
```

È anche possibile scaricare e salvare il modulo nel caso in cui sia necessario distribuirlo ai computer Windows mediante la soluzione WMF 4.0, eseguendo questo cmdlet su un computer in cui è presente PowerShellGet (5.0 WMF):

```
Save-Module -Name xPSDesiredStateConfiguration -Path <location>
```

Anche per WMF 4.0, verificare che l'aggiornamento seguente venga installato sui computer:

https://www.microsoft.com/download/details.aspx?id=40749  

È possibile eseguire il push della configurazione seguente verso i computer Windows con la soluzione WMF 5.0 e 4.0

```
configuration ASRMobilityService {

    param (
    [Parameter(Mandatory=$true)]
    $computername
    )

    $RemoteFile = '\\myfileserver\share\asr.zip'
    $RemotePassphrase = '\\myfileserver\share\passphrase.txt'
    $RemoteAzureAgent = '\\myfileserver\share\AzureVmAgent.msi'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = "C:\Temp\Mobility_service\passphrase.txt"
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role ' + '"' + $Role + '"' + ' /InstallLocation ' + '"' + $Install + '"' + ' /CSEndpoint ' + '"' + $CSEndpoint + '"' + ' /PassphraseFilePath ' +  '"' +$LocalPassphrase + '"'  

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node $computername {      

        xRemoteFile Setup {
           URI = $RemoteFile
           DestinationPath = $TempDestination
           DependsOn = "[File]Directory"
            }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = "[File]Directory"
            }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = "[File]Directory"
            }

        File Directory {
            DestinationPath = "C:\Temp\ASRSetup"
            Type = "Directory"            
            }

        Archive ASRzip {
           Path = $TempDestination
           Destination = "C:\Temp\ASRSetup"
           DependsOn = "[xRemotefile]Setup"
           }

        Package Install {
            Path = "C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE"
            Ensure = "Present"
            Name = "Microsoft Azure Site Recovery mobility Service/Master Target Server"
            ProductId = "275197FC-14FD-4560-A5EB-38217F80CBD1"
            Arguments = $Arguments
            DependsOn = "[Archive]ASRzip"
            }

        Package AzureAgent {
            Path = "C:\Temp\AzureVmAgent.msi"
            Ensure = "Present"
            Name = "Windows Azure VM Agent - 2.7.1198.735"
            ProductId = "5CF4D04A-F16C-4892-9196-6025EA61F964"
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = "[Package]Install"
            }

        Service ASRvx {
            Name = "svagents"
            State = "Running"
            DependsOn = "[Package]Install"
            }

        Service ASR {
            Name = "InMage Scout Application Service"
            State = "Running"
            DependsOn = "[Package]Install"
            }

        Service AzureAgentService {
            Name = "WindowsAzureGuestAgent"
            State = "Running"
            DependsOn = "[Package]AzureAgent"
            }

        Service AzureTelemetry {
            Name = "WindowsAzureTelemetryService"
            State = "Running"
            DependsOn = "[Package]AzureAgent"
            }
    }
}
ASRMobilityService

Start-DscConfiguration -ComputerName $computername .\ASRMobilityService -wait -force -Verbose
```

Se si vuole creare un'istanza del server di pull DSC all'interno della rete aziendale per riprodurre le stesse funzionalità disponibili con OMS Automation DSC, vedere la Guida seguente: https://msdn.microsoft.com/powershell/dsc/pullserver?f=255&MSPPError=-2147217396

## Facoltativo: distribuire la configurazione DSC usando il modello di Azure Resource Manager

Questo articolo ha illustrato finora come è possibile creare la propria configurazione DSC per distribuire automaticamente il servizio Mobility e l'agente VM di Azure e assicurarsi che siano in esecuzione nei computer da proteggere. Inoltre, abbiamo anche un modello di Azure Resource Manager## ## che consente di distribuire questa configurazione DSC a un account di Automazione di Azure nuovo o esistente, creando asset di automazione che conterranno le variabili di ambiente tramite parametri di input nel modello.

Una volta eseguita la distribuzione, fare riferimento al passaggio 4 in questa guida per caricare le macchine.

Il modello eseguirà le operazioni seguenti:

·Usare un account di OMS Automation esistente e crearne uno nuovo

· Accettare i parametri di input per:

	· ASRRemoteFile – the location where you have stored the Mobility Service setup
	· ASRPassphrase – the location where you have stored the passphrase.txt file
	· ASRCSEndpoint – the IP address of your Management server
	· Import xPSDesiredStateConfiguration PowerShell module
	· Create and compile the DSC configuration


Tutti i passaggi precedenti verranno eseguiti nell'ordine corretto in modo che sia facile iniziare il caricamento delle macchine per la protezione.

Il modello con le istruzioni per la distribuzione si trova in:

https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/DSC  

Distribuire tramite PowerShell:

```
$OMSAutomationRGname = 'KNOMS'

$DSCJobGuid = (New-Guid).Guid

New-AzureRmResourceGroupDeployment `
                                  -Name "DSC3" `
                                  -ResourceGroupName $OMSAutomationRGname `
                                  -TemplateFile https://raw.githubusercontent.com/krnese/AzureDeploy/master/OMS/MSOMS/DSC/azuredeploy.json `
                                  -OMSAutomationAccountName KNOMSAA `
                                  -ASRRemoteFile "https://knrecstor01.blob.core.windows.net/asr/ASR.zip" `
                                  -ASRRemotePassphrase "https://knrecstor01.blob.core.windows.net/asr/passphrase.txt" `
                                  -ASRCSEndpoint '10.0.0.115' `
                                  -DSCJobGuid $DSCJobGuid `
                                  -Verbose
```

## Passaggi successivi:

Dopo avere distribuito gli agenti del servizio Mobility, è possibile continuare ad [abilitare la replica](site-recovery-vmware-to-azure.md#step-6-replicate-applications) per le macchine virtuali.

<!---HONumber=AcomDC_0810_2016-->