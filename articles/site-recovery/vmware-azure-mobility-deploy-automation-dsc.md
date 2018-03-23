---
title: Distribuire il servizio Site Recovery Mobility con Automation DSC di Azure | Documentazione Microsoft
description: Descrive come usare Automation DSC di Azure per distribuire automaticamente il servizio Mobility di Azure Site Recovery e l'agente di Azure per le macchine virtuali VMware e la replica del server fisico in Azure
services: site-recovery
author: krnese
manager: lorenr
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: krnese
ms.openlocfilehash: 0bf1b551ba578cd152201c131bd60470bdc9d86a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="deploy-the-mobility-service-with-azure-automation-dsc"></a>Distribuire il servizio Mobility con Automation DSC per Azure

Il servizio Mobility viene distribuito nelle VM VMware e nei server fisici di cui si vuole eseguire la replica in Azure con [Azure Site Recovery](site-recovery-overview.md).

Questo articolo descrive come distribuire il servizio in computer Windows usando Automation DSC (Desired State Configuration) per Azure.


## <a name="prerequisites"></a>prerequisiti

* Un repository per archiviare la configurazione necessaria
* Un repository per archiviare la passphrase necessaria per la registrazione con il server di gestione. Per ogni server di configurazione viene generata una passphrase univoca. 
* Windows Management Framework (WMF) 5.0 deve essere installato nei computer per i quali si vuole abilitare la protezione. Si tratta di un requisito per Automation DSC.
Per usare DSC per i computer Windows nei quali è installato WMF 4.0, vedere come [usare DSC in ambienti non connessi](## Use DSC in disconnected environments).
 

## <a name="extract-binaries"></a>Estrarre i file binari

Il servizio Mobility può essere installato tramite la riga di comando e accetta diversi argomenti. È necessario ottenere i file binari (dopo averli estratti dal percorso di installazione) e archiviarli in una posizione in cui è possibile recuperarli usando una configurazione DSC.

1. Per estrarre i file necessari per l'installazione, passare alla directory seguente nel server di gestione: **\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**
2. In questa cartella verificare che sia presente un file MSI denominato **Microsoft-ASR_UA_version_Windows_GA_date_Release.exe**.
3. Usare il comando seguente per estrarre il programma di installazione: **.\Microsoft-ASR_UA_9.1.0.0_Windows_GA_02May2016_release.exe /q /x:C:\Users\Administrator\Desktop\Mobility_Service\Extract**
2. Selezionare tutti i file e inviarli a una cartella compressa.

Ora sono disponibili i file binari necessari per automatizzare la configurazione del servizio Mobility con Automation DSC.

## <a name="store-the-passphrase"></a>Archiviare la passphrase

È necessario determinare dove posizionare la cartella compressa. È possibile usare un account di archiviazione di Azure, come illustrato più avanti, per archiviare la passphrase necessaria per la configurazione. L'agente verrà quindi registrato con il server di gestione come parte del processo.

1. Salvare la passphrase ottenuta durante la distribuzione del server di configurazione in un file di testo denominato passphrase.txt.
2. Inserire sia la cartella compressa sia la passphrase in un contenitore dedicato nell'account di archiviazione di Azure.


Se si preferisce, è possibile mantenere questi file in una condivisione all'interno della rete. È sufficiente garantire che la risorsa DSC che verrà usata in un secondo momento disponga dell'accesso e possa usare la configurazione e la passphrase.

## <a name="create-the-dsc-configuration"></a>Creare la configurazione DSC

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

## <a name="upload-to-automation-dsc"></a>Caricare in Automation DSC
Dal momento che la configurazione DSC creata importerà il modulo di risorse DSC xPSDesiredStateConfiguration richiesto, è necessario importare il modulo in Automation prima di caricare la configurazione DSC.

1. Accedere all'account di Automazione, andare su **Asset** > **Moduli** e fare clic su **Esplora raccolta**.
2. Cercare il modulo e importarlo nel proprio account.
3. Al termine passare al computer su cui sono installati i moduli di Azure Resource Manager e continuare a importare la configurazione DSC appena creata.

## <a name="import-cmdlets"></a>Cmdlet di importazione

1. In PowerShell accedere alla sottoscrizione di Azure.
2. Modificare i cmdlet in modo da riflettere l'ambiente e acquisire le informazioni sull'account di Automation in forma di variabile:

    ```powershell
    $AAAccount = Get-AzureRmAutomationAccount -ResourceGroupName 'KNOMS' -Name 'KNOMSAA'
    ```

3. Caricare la configurazione di Automation DSC usando il cmdlet seguente:

    ```powershell
    $ImportArgs = @{
        SourcePath = 'C:\ASR\ASRMobilityService.ps1'
        Published = $true
        Description = 'DSC Config for Mobility Service'
    }
    $AAAccount | Import-AzureRmAutomationDscConfiguration @ImportArgs
    ```

## <a name="compile-the-configuration-in-automation-dsc"></a>Compilare la configurazione in Automation DSC

1. Compilare la configurazione in Automation DSC, in modo che sia possibile iniziare a registrare i nodi su di essa. Questo risultato si ottiene eseguendo il cmdlet seguente:

    ```    powershell
    $AAAccount | Start-AzureRmAutomationDscCompilationJob -ConfigurationName ASRMobilityService
    ```

Questa operazione può richiedere alcuni minuti poiché si sta distribuendo la configurazione al servizio di pull DSC ospitato.

2. Al termine della configurazione, è possibile recuperare le informazioni sul processo tramite PowerShell (Get-AzureRmAutomationDscCompilationJob) o usando il [portale di Azure](https://portal.azure.com/).

    ![Recuperare il processo](./media/vmware-azure-mobility-deploy-automation-dsc/retrieve-job.png)

La configurazione DSC in Automation DSC è stata ora pubblicata e caricata correttamente.

## <a name="onboard-machines-to-automation-dsc"></a>Eseguire l'onboarding dei computer in Automation DSC


1. Verificare che la versione della soluzione WMF nei computer Windows sia aggiornata. È possibile scaricare e installare la versione corretta per la piattaforma dall' [Area download](https://www.microsoft.com/download/details.aspx?id=50395).
2. Creare una metaconfigurazione per DSC che verrà applicata ai nodi. Per un risultato positivo è necessario recuperare l'URL e la chiave primaria dell'endpoint per l'account di automazione selezionato in Azure. Questi valori si trovano in **Chiavi** nel pannello **Tutte le impostazioni** dell'account di Automazione.

    ![Valori chiave](./media/vmware-azure-mobility-deploy-automation-dsc/key-values.png)

In questo esempio è presente un server fisico Windows Server 2012 R2 che si desidera proteggere con Site Recovery.

## <a name="check-for-any-pending-file-rename-operations"></a>Verificare se sono presenti operazioni di ridenominazione di file in sospeso

Prima di iniziare ad associare il server con l'endpoint Automation DSC, è consigliabile controllare le operazioni di ridenominazione dei file in sospeso nel Registro di sistema, in quanto un riavvio in sospeso potrebbe impedire il completamento della configurazione.

1. Per verificare che non vi sia alcun riavvio in sospeso nel server, eseguire il cmdlet seguente:

    ```powershell
    Get-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\' | Select-Object -Property PendingFileRenameOperations
    ```
2. Se non è presente, è possibile continuare. In caso contrario, è necessario riavviare il server in una finestra di manutenzione.
3. Per applicare la configurazione sul server, avviare PowerShell Integrated Scripting Environment (ISE) ed eseguire lo script seguente. Lo script è essenzialmente una configurazione locale per DSC che indicherà al motore di gestione configurazione locale di eseguire la registrazione con il servizio Automation DSC e recuperare la configurazione specifica (ASRMobilityService.localhost).

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

1. Dopo l'esecuzione dello script, il nodo dovrebbe iniziare la registrazione ad Automation DSC.

    ![Registrazione del nodo in corso](./media/vmware-azure-mobility-deploy-automation-dsc/register-node.png)

2. Se si torna al portale di Azure, si può notare che il nodo appena registrato è ora presente nel portale.

    ![Nodo registrato nel portale](./media/vmware-azure-mobility-deploy-automation-dsc/registered-node.png)

3. Per verificare che la registrazione del nodo sia stata eseguita correttamente, è possibile eseguire il cmdlet PowerShell seguente sul server:

    ```powershell
    Get-DscLocalConfigurationManager
    ```

4. Dopo che il pull della configurazione è stato eseguito e la configurazione è stata applicata al server, procedere alla verifica eseguendo il seguente cmdlet:

    ```powershell
    Get-DscConfigurationStatus
    ```

L'output mostra che il server ha eseguito correttamente il pull della configurazione:

![Output](./media/vmware-azure-mobility-deploy-automation-dsc/successful-config.png)

Inoltre, la configurazione del servizio Mobility dispone di un proprio log in *SystemDrive*\ProgramData\ASRSetupLogs.

La procedura è terminata. Il servizio Mobility è stato distribuito e registrato correttamente sul computer che si desidera proteggere tramite Site Recovery. DSC si assicurerà che i servizi necessari siano sempre in esecuzione.

![Distribuzione completata](./media/vmware-azure-mobility-deploy-automation-dsc/successful-install.png)

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
Dopo avere distribuito gli agenti del servizio Mobility, è possibile [abilitare la replica](vmware-azure-tutorial.md) per le macchine virtuali.
