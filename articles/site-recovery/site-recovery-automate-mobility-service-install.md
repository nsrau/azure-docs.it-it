---
title: Distribuire il servizio Site Recovery Mobility con Automation DSC di Azure | Documentazione Microsoft
description: Descrive come usare Automation DSC di Azure per distribuire automaticamente il servizio Mobility di Azure Site Recovery e l'agente di Azure per le macchine virtuali VMware e la replica del server fisico in Azure
services: site-recovery
documentationcenter: 
author: krnese
manager: lorenr
editor: 
ms.assetid: 1f8cd3ac-0522-48eb-a5f0-679ee9192ddb
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: krnese
ms.openlocfilehash: eb837858fe81fa1f2d4966051bd4b7b068defe8b
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
---
# <a name="deploy-the-mobility-service-with-azure-automation-dsc-for-replication-of-vm"></a>Distribuire il servizio Mobility di Azure con Automation DSC di Azure per la replica della macchina virtuale
In Operations Management Suite offriamo una soluzione completa per il backup e il ripristino di emergenza che può essere usata nell'ambito del piano di continuità aziendale.

Il percorso è iniziato da Hyper-V con la replica Hyper-V, poi è proseguito per supportare una configurazione eterogenea perché i clienti hanno più hypervisor e piattaforme nei loro cloud.

Se attualmente si eseguono carichi di lavoro VMware e/o server fisici, un server di gestione esegue tutti i componenti di Azure Site Recovery nell'ambiente per gestire la comunicazione e la replica dei dati con Azure, quando questo è la destinazione.

## <a name="deploy-the-site-recovery-mobility-service-by-using-automation-dsc"></a>Distribuire il servizio Site Recovery Mobility usando Automation DSC
Per iniziare, è bene riepilogare le operazioni eseguite da questo server di gestione.

Il server di gestione esegue diversi ruoli del server. Uno di questi ruoli è la *configurazione*, che coordina la comunicazione e gestisce i processi di ripristino e replica dei dati.

Inoltre, il ruolo di *elaborazione* funge da gateway di replica. Questo ruolo riceve i dati di replica da computer di origine protetti, li ottimizza attraverso il caching, la compressione e la crittografia e li invia a un account di archiviazione di Azure. Una delle funzioni del ruolo di elaborazione è anche quella di eseguire il push dell'installazione del servizio Mobility nei computer protetti ed eseguire l'individuazione automatica delle VM VMware.

In caso di failback da Azure, il ruolo di *destinazione master* gestirà i dati di replica come parte di questa operazione.

Per i computer protetti, ci si affida al *servizio Mobility*. Questo componente viene distribuito in ogni computer, ovvero VM VMware o server fisico, di cui si vuole eseguire la replica in Azure. Acquisisce scritture di dati sul computer e le inoltra al server di gestione (ruolo di elaborazione).

Nell'ambito della continuità aziendale è importante comprendere i carichi di lavoro, l'infrastruttura e i componenti coinvolti. È quindi possibile soddisfare i requisiti per gli obiettivi del tempo di ripristino (RTO) e gli obiettivi del punto di ripristino (RPO). In questo contesto, il servizio Mobility è fondamentale per garantire che i carichi di lavoro siano protetti nel modo previsto.

Come possiamo garantire nel modo migliore che la configurazione sia affidabile e protetta usando alcuni componenti di Operations Management Suite?

In questo articolo viene fornito un esempio di come è possibile usare Automation Desired State Configuration (DSC) di Azure insieme a Site Recovery per assicurarsi che:

* Il servizio Mobility e l'agente VM di Azure vengano distribuiti ai computer Windows che si desidera proteggere.
* Il servizio Mobility e l'agente VM di Azure siano sempre in esecuzione quando Azure è la destinazione della replica.

## <a name="prerequisites"></a>prerequisiti
* Un repository per archiviare la configurazione necessaria
* Un repository per archiviare la passphrase necessaria per la registrazione al server di gestione

  > [!NOTE]
  > Per ogni server di gestione viene generata una passphrase univoca. Se si intende distribuire più server di gestione, è necessario assicurarsi che la passphrase corretta venga archiviata nel file passphrase txt.
  >
  >
* La soluzione Windows Management Framework (WMF) 5.0 installata sui computer di cui che si vuole abilitare la protezione (requisito per Automation DSC)

  > [!NOTE]
  > Se si desidera usare DSC per computer Windows su cui è installato WMF 4.0, vedere la sezione [Uso di DSC in ambienti non connessi](## Use DSC in disconnected environments).
  

Il servizio Mobility può essere installato tramite la riga di comando e accetta diversi argomenti. Ecco perché è necessario disporre dei file binari (dopo averli estratti dalla configurazione) e archiviarli in un punto in cui è possibile recuperarli usando una configurazione DSC.

## <a name="step-1-extract-binaries"></a>Passaggio 1: estrazione dei file binari
1. Per estrarre i file necessari per la configurazione, accedere alla directory seguente nel server di gestione:

    **\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**

    In questa cartella dovrebbe essere presente un file MSI denominato:

    **Microsoft-ASR_UA_version_Windows_GA_date_Release.exe**

    Per estrarre il programma di installazione usare il comando seguente:

    **.\Microsoft-ASR_UA_9.1.0.0_Windows_GA_02May2016_release.exe /q /x:C:\Users\Administrator\Desktop\Mobility_Service\Extract**
2. Selezionare tutti i file e inviarli a una cartella compressa.

Ora sono disponibili i file binari necessari per automatizzare la configurazione del servizio Mobility con Automation DSC.

### <a name="passphrase"></a>Passphrase
È quindi necessario scegliere dove si vuole posizionare la cartella compressa. È possibile usare un account di archiviazione di Azure, come illustrato più avanti, per archiviare la passphrase necessaria per la configurazione. L'agente verrà quindi registrato con il server di gestione come parte del processo.

La passphrase ottenuta durante la distribuzione del server di gestione può essere salvata in un file txt come passphrase.txt.

Inserire sia la cartella compressa sia la passphrase in un contenitore dedicato nell'account di archiviazione di Azure.

![Percorso della cartella](./media/site-recovery-automate-mobilitysevice-install/folder-and-passphrase-location.png)

Se si preferisce, è possibile mantenere questi file in una condivisione all'interno della rete. È sufficiente garantire che la risorsa DSC che verrà usata in un secondo momento disponga dell'accesso e possa usare la configurazione e la passphrase.

## <a name="step-2-create-the-dsc-configuration"></a>Passaggio 2: creazione della configurazione DSC
Il programma di configurazione dipende da WMF 5.0. Affinché il computer applichi correttamente la configurazione tramite Automation DSC, è necessario che sia presente la soluzione WMF 5.0.

Nell'ambiente la configurazione DSC di esempio usata è la seguente:

```powershell
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
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node localhost {

        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            Ensure = 'Present'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
```
La configurazione eseguirà le operazioni seguenti:

* Le variabili indicheranno alla configurazione dove ottenere i file binari per il servizio Mobility e l'agente VM di Azure, la passphrase e il percorso in cui archiviare l'output.
* La configurazione importerà la risorsa DSC xPSDesiredStateConfiguration in modo da poter usare `xRemoteFile` per scaricare i file dal repository.
* La configurazione creerà la directory in cui si vuole archiviare i dati binari.
* La risorsa di archiviazione estrarrà i file dalla cartella compressa.
* La risorsa Install del pacchetto installerà il servizio Mobility dal programma di installazione UNIFIEDAGENT.EXE con gli argomenti specifici. Le variabile che costruiscono gli argomenti devono essere modificate per riflettere l'ambiente.
* La risorsa AzureAgent del pacchetto installerà l'agente VM di Azure, consigliato su tutte le VM eseguite in Azure. L'agente VM di Azure rende inoltre possibile aggiungere estensioni alla VM dopo il failover.
* Una o più risorse del servizio garantiranno che i servizi Mobility correlati e i servizi di Azure siano sempre in esecuzione.

Salvare la configurazione come **ASRMobilityService**.

> [!NOTE]
> È necessario ricordarsi di sostituire il valore CSIP nella configurazione per riflettere il server di gestione effettivo, in modo che l'agente venga connesso e che usi la passphrase corretta.
>
>

## <a name="step-3-upload-to-automation-dsc"></a>Passaggio 3: caricamento su Automation DSC
Dal momento che la configurazione DSC creata importerà il modulo di risorse DSC xPSDesiredStateConfiguration richiesto, è necessario importare il modulo in Automation prima di caricare la configurazione DSC.

Accedere all'account di Automazione, andare su **Asset** > **Moduli** e fare clic su **Esplora raccolta**.

Qui è possibile cercare il modulo e importarlo nel proprio account.

![Importazione del modulo](./media/site-recovery-automate-mobilitysevice-install/search-and-import-module.png)

Al termine passare al computer su cui sono installati i moduli di Azure Resource Manager e continuare a importare la configurazione DSC appena creata.

### <a name="import-cmdlets"></a>Cmdlet di importazione
In PowerShell accedere alla sottoscrizione di Azure. Modificare i cmdlet in modo da riflettere l'ambiente e acquisire le informazioni sull'account di Automation in forma di variabile:

```powershell
$AAAccount = Get-AzureRmAutomationAccount -ResourceGroupName 'KNOMS' -Name 'KNOMSAA'
```

Caricare la configurazione di Automation DSC usando il cmdlet seguente:

```powershell
$ImportArgs = @{
    SourcePath = 'C:\ASR\ASRMobilityService.ps1'
    Published = $true
    Description = 'DSC Config for Mobility Service'
}
$AAAccount | Import-AzureRmAutomationDscConfiguration @ImportArgs
```

### <a name="compile-the-configuration-in-automation-dsc"></a>Compilare la configurazione in Automation DSC
È quindi necessario compilare la configurazione in Automation DSC, in modo che sia possibile iniziare a registrare i nodi su di essa. Questo risultato si ottiene eseguendo il cmdlet seguente:

```powershell
$AAAccount | Start-AzureRmAutomationDscCompilationJob -ConfigurationName ASRMobilityService
```

Questa operazione può richiedere alcuni minuti poiché si sta distribuendo la configurazione al servizio di pull DSC ospitato.

Al termine della configurazione, è possibile recuperare le informazioni sul processo tramite PowerShell (Get-AzureRmAutomationDscCompilationJob) o usando il [portale di Azure](https://portal.azure.com/).

![Recuperare il processo](./media/site-recovery-automate-mobilitysevice-install/retrieve-job.png)

La configurazione DSC in Automation DSC è stata ora pubblicata e caricata correttamente.

## <a name="step-4-onboard-machines-to-automation-dsc"></a>Passaggio 4: caricamento di computer in Automation DSC
> [!NOTE]
> Uno dei prerequisiti per il completamento di questo scenario è che la versione della soluzione WMF nei computer Windows sia aggiornata. È possibile scaricare e installare la versione corretta per la piattaforma dall' [Area download](https://www.microsoft.com/download/details.aspx?id=50395).
>
>

Si creerà ora una metaconfig per DSC che sarà applicata ai nodi. Per un risultato positivo è necessario recuperare l'URL e la chiave primaria dell'endpoint per l'account di automazione selezionato in Azure. Questi valori si trovano in **Chiavi** nel pannello **Tutte le impostazioni** dell'account di Automazione.

![Valori chiave](./media/site-recovery-automate-mobilitysevice-install/key-values.png)

In questo esempio è presente un server fisico Windows Server 2012 R2 che si desidera proteggere con Site Recovery.

### <a name="check-for-any-pending-file-rename-operations-in-the-registry"></a>Verificare la presenza di eventuali operazioni di ridenominazione di file in sospeso nel Registro di sistema
Prima di iniziare ad associare il server con l'endpoint Automation DSC, è consigliabile controllare le operazioni di ridenominazione dei file in sospeso nel Registro di sistema, in quanto un riavvio in sospeso potrebbe impedire il completamento della configurazione.

Per verificare che non vi sia alcun riavvio in sospeso nel server, eseguire il cmdlet seguente:

```powershell
Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\' | Select-Object -Property PendingFileRenameOperations
```
Se non è presente, è possibile continuare. In caso contrario, è necessario riavviare il server in una finestra di manutenzione.

Per applicare la configurazione sul server, avviare PowerShell Integrated Scripting Environment (ISE) ed eseguire lo script seguente. Lo script è essenzialmente una configurazione locale per DSC che indicherà al motore di gestione configurazione locale di eseguire la registrazione con il servizio Automation DSC e recuperare la configurazione specifica (ASRMobilityService.localhost).

```powershell
[DSCLocalConfigurationManager()]
configuration metaconfig {
    param (
        $URL,
        $Key
    )
    node localhost {
        Settings {
            RefreshFrequencyMins = '30'
            RebootNodeIfNeeded = $true
            RefreshMode = 'PULL'
            ActionAfterReboot = 'ContinueConfiguration'
            ConfigurationMode = 'ApplyAndMonitor'
            AllowModuleOverwrite = $true
        }

        ResourceRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }

        ConfigurationRepositoryWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
            ConfigurationNames = 'ASRMobilityService.localhost'
        }

        ReportServerWeb AzureAutomationDSC {
            ServerURL = $URL
            RegistrationKey = $Key
        }
    }
}
metaconfig -URL 'https://we-agentservice-prod-1.azure-automation.net/accounts/<YOURAAAccountID>' -Key '<YOURAAAccountKey>'

Set-DscLocalConfigurationManager .\metaconfig -Force -Verbose
```

Questa configurazione causerà la registrazione del motore di gestione configurazione locale con Automation DSC. Inoltre determinerà come il motore deve funzionare, come comportarsi in presenza di una differenza nella configurazione (ApplyAndAutoCorrect) e come procedere con la configurazione se è richiesto un riavvio.

Dopo l'esecuzione dello script, il nodo dovrebbe iniziare la registrazione ad Automation DSC.

![Registrazione del nodo in corso](./media/site-recovery-automate-mobilitysevice-install/register-node.png)

Se si torna al portale di Azure, si può notare che il nodo appena registrato è ora presente nel portale.

![Nodo registrato nel portale](./media/site-recovery-automate-mobilitysevice-install/registered-node.png)

Per verificare che la registrazione del nodo sia stata eseguita correttamente, è possibile eseguire il cmdlet PowerShell seguente sul server:

```powershell
Get-DscLocalConfigurationManager
```

Dopo che il pull della configurazione è stato eseguito e la configurazione è stata applicata al server, procedere alla verifica eseguendo il seguente cmdlet:

```powershell
Get-DscConfigurationStatus
```

L'output mostra che il server ha eseguito correttamente il pull della configurazione:

![Output](./media/site-recovery-automate-mobilitysevice-install/successful-config.png)

Inoltre, la configurazione del servizio Mobility dispone di un proprio log in *SystemDrive*\ProgramData\ASRSetupLogs.

La procedura è terminata. Il servizio Mobility è stato distribuito e registrato correttamente sul computer che si desidera proteggere tramite Site Recovery. DSC si assicurerà che i servizi necessari siano sempre in esecuzione.

![Distribuzione completata](./media/site-recovery-automate-mobilitysevice-install/successful-install.png)

Una volta che il server di gestione ha rilevato la corretta distribuzione, procedere per configurare la protezione e abilitare la replica nel computer con Site Recovery.

## <a name="use-dsc-in-disconnected-environments"></a>Uso di DSC in ambienti non connessi
Se i computer non sono connessi a Internet, è comunque possibile fare affidamento su DSC per distribuire e configurare il servizio Mobility sui carichi di lavoro da proteggere.

È possibile creare istanze del server di pull DSC nel proprio ambiente per fornire essenzialmente le stesse funzionalità ottenute da Automation DSC. Ciò significa che, dopo la registrazione, i client eseguiranno il pull della configurazione sull'endpoint DSC. Tuttavia, un'altra opzione consiste nel push manuale della configurazione sui computer locali o remoti.

Notare che in questo esempio esiste un parametro aggiunto per il nome del computer. I file remoti si trovano ora in una condivisione remota che deve essere accessibile ai computer che si desidera proteggere. La fine dello script esegue la configurazione e quindi inizia ad applicare la configurazione DSC al computer di destinazione.

### <a name="prerequisites"></a>prerequisiti
Assicurarsi che sia installato il modulo xPSDesiredStateConfiguration di PowerShell. Per i computer Windows su cui è installata la soluzione WMF 5.0, è possibile installare il modulo xPSDesiredStateConfiguration eseguendo il cmdlet seguente nei computer di destinazione:

```powershell
Find-Module -Name xPSDesiredStateConfiguration | Install-Module
```

È inoltre possibile scaricare e salvare il modulo nel caso in cui sia necessario distribuirlo ai computer Windows con WMF 4.0. Eseguire questo cmdlet su un computer in cui è presente PowerShellGet (WMF 5.0):

```powershell
Save-Module -Name xPSDesiredStateConfiguration -Path <location>
```

Anche per WMF 4.0, assicurarsi che sui computer sia installato [Windows 8.1 update KB2883200](https://www.microsoft.com/download/details.aspx?id=40749) .

È possibile eseguire il push della configurazione seguente sui computer Windows con WMF 5.0 e WMF 4.0:

```powershell
configuration ASRMobilityService {
    param (
        [Parameter(Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [System.String] $ComputerName
    )

    $RemoteFile = '\\myfileserver\share\asr.zip'
    $RemotePassphrase = '\\myfileserver\share\passphrase.txt'
    $RemoteAzureAgent = '\\myfileserver\share\AzureVmAgent.msi'
    $LocalAzureAgent = 'C:\Temp\AzureVmAgent.msi'
    $TempDestination = 'C:\Temp\asr.zip'
    $LocalPassphrase = 'C:\Temp\Mobility_service\passphrase.txt'
    $Role = 'Agent'
    $Install = 'C:\Program Files (x86)\Microsoft Azure Site Recovery'
    $CSEndpoint = '10.0.0.115'
    $Arguments = '/Role "{0}" /InstallLocation "{1}" /CSEndpoint "{2}" /PassphraseFilePath "{3}"' -f $Role,$Install,$CSEndpoint,$LocalPassphrase

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    node $ComputerName {      
        File Directory {
            DestinationPath = 'C:\Temp\ASRSetup\'
            Type = 'Directory'            
        }

        xRemoteFile Setup {
            URI = $RemoteFile
            DestinationPath = $TempDestination
            DependsOn = '[File]Directory'
        }

        xRemoteFile Passphrase {
            URI = $RemotePassphrase
            DestinationPath = $LocalPassphrase
            DependsOn = '[File]Directory'
        }

        xRemoteFile AzureAgent {
            URI = $RemoteAzureAgent
            DestinationPath = $LocalAzureAgent
            DependsOn = '[File]Directory'
        }

        Archive ASRzip {
            Path = $TempDestination
            Destination = 'C:\Temp\ASRSetup'
            DependsOn = '[xRemotefile]Setup'
        }

        Package Install {
            Path = 'C:\temp\ASRSetup\ASR\UNIFIEDAGENT.EXE'
            Ensure = 'Present'
            Name = 'Microsoft Azure Site Recovery mobility Service/Master Target Server'
            ProductId = '275197FC-14FD-4560-A5EB-38217F80CBD1'
            Arguments = $Arguments
            DependsOn = '[Archive]ASRzip'
        }

        Package AzureAgent {
            Path = 'C:\Temp\AzureVmAgent.msi'
            Ensure = 'Present'
            Name = 'Windows Azure VM Agent - 2.7.1198.735'
            ProductId = '5CF4D04A-F16C-4892-9196-6025EA61F964'
            Arguments = '/q /l "c:\temp\agentlog.txt'
            DependsOn = '[Package]Install'
        }

        Service ASRvx {
            Name = 'svagents'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service ASR {
            Name = 'InMage Scout Application Service'
            State = 'Running'
            DependsOn = '[Package]Install'
        }

        Service AzureAgentService {
            Name = 'WindowsAzureGuestAgent'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }

        Service AzureTelemetry {
            Name = 'WindowsAzureTelemetryService'
            State = 'Running'
            DependsOn = '[Package]AzureAgent'
        }
    }
}
ASRMobilityService -ComputerName 'MyTargetComputerName'

Start-DscConfiguration .\ASRMobilityService -Wait -Force -Verbose
```

Se si vuole creare un'istanza del server di pull DSC sulla rete aziendale per riprodurre le funzionalità disponibili in Automation DSC, vedere [Configurazione di un server di pull Web DSC](https://msdn.microsoft.com/powershell/dsc/pullserver?f=255&MSPPError=-2147217396).

## <a name="optional-deploy-a-dsc-configuration-by-using-an-azure-resource-manager-template"></a>Facoltativo: distribuire una configurazione DSC usando il modello di Azure Resource Manager
Questo articolo ha illustrato come è possibile creare la propria configurazione DSC per distribuire automaticamente il servizio Mobility e l'agente VM di Azure e assicurarsi che siano in esecuzione sui computer da proteggere. È disponibile anche un modello di Azure Resource Manager che consente di distribuire questa configurazione DSC a un account di Automazione di Azure nuovo o esistente. Il modello userà i parametri di input per creare gli asset di automazione che conterranno le variabili per l'ambiente.

Una volta distribuito il modello, è possibile fare riferimento al passaggio 4 di questa guida per caricare i computer.

Il modello eseguirà le operazioni seguenti:

1. Usare un account di Automazione esistente e crearne uno nuovo
2. Accettare i parametri di input per:
   * ASRRemoteFile - il percorso in cui è stata memorizzata la configurazione del servizio Mobility
   * ASRPassphrase - il percorso in cui è stato memorizzato il file passphrase.txt
   * ASRCSEndpoint - l'indirizzo IP del server di gestione
3. Importare il modulo xPSDesiredStateConfiguration di PowerShell
4. Creare e compilare la configurazione DSC

Tutti i passaggi precedenti verranno eseguiti nell'ordine corretto in modo da poter iniziare il caricamento dei computer per la protezione.

Il modello con le istruzioni per la distribuzione si trova in [GitHub](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/DSC).

Distribuire il modello tramite PowerShell:

```powershell
$RGDeployArgs = @{
    Name = 'DSC3'
    ResourceGroupName = 'KNOMS'
    TemplateFile = 'https://raw.githubusercontent.com/krnese/AzureDeploy/master/OMS/MSOMS/DSC/azuredeploy.json'
    OMSAutomationAccountName = 'KNOMSAA'
    ASRRemoteFile = 'https://knrecstor01.blob.core.windows.net/asr/ASR.zip'
    ASRRemotePassphrase = 'https://knrecstor01.blob.core.windows.net/asr/passphrase.txt'
    ASRCSEndpoint = '10.0.0.115'
    DSCJobGuid = [System.Guid]::NewGuid().ToString()
}
New-AzureRmResourceGroupDeployment @RGDeployArgs -Verbose
```

## <a name="next-steps"></a>Passaggi successivi
Dopo avere distribuito gli agenti del servizio Mobility, è possibile [abilitare la replica](site-recovery-vmware-to-azure.md) per le macchine virtuali.
