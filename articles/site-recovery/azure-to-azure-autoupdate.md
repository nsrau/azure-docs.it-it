---
title: Aggiornamento automatico del servizio Mobility di Azure al ripristino di emergenza di Azure | Microsoft Docs
description: Viene fornita una panoramica dell'aggiornamento automatico del servizio Mobility durante la replica delle macchine virtuali di Azure tramite Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 3f0f28ca22321b537ab7e8911c5cbb513a1ade81
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55818924"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Aggiornamento automatico del servizio Mobility in una replica di Azure

Azure Site Recovery ha una frequenza di rilascio mensile in cui vengono aggiunti miglioramenti delle funzionalità esistenti o nuove funzionalità e vengono risolti gli eventuali problemi noti. Ciò significa che è necessario pianificare la distribuzione delle patch ogni mese affinché il servizio rimanga aggiornato. Per evitare il sovraccarico associato all'aggiornamento, gli utenti possono scegliere di consentire a Site Recovery di gestire gli aggiornamenti dei componenti. Come descritto in dettaglio nel [riferimento dell'architettura](azure-to-azure-architecture.md) per il ripristino di emergenza da Azure ad Azure, il servizio Mobility viene installato in tutte le macchine virtuali di Azure per cui è abilitata la replica durante la replica delle macchine virtuali da un'area di Azure a un'altra. Una volta abilitato l'aggiornamento automatico, l'estensione del servizio Mobility viene aggiornata con il rilascio di ogni nuova versione. In questo documento viene illustrato quanto segue:

- Come funziona l'aggiornamento automatico
- Abilitare gli aggiornamenti automatici
- Problemi comuni e risoluzione dei problemi
 
## <a name="how-does-automatic-update-work"></a>Come funziona l'aggiornamento automatico

Quando si consente a Site Recovery di gestire gli aggiornamenti, viene distribuito un runbook globale (usato dai servizi di Azure) tramite un account di automazione che viene creato nella stessa sottoscrizione dell'insieme di credenziali. Un account di automazione viene usato per un insieme di credenziali specifico. Il runbook verifica per quali macchine virtuali in un insieme di credenziali sono attivi gli aggiornamenti automatici e avvia un aggiornamento dell'estensione del servizio Mobility se è disponibile una versione più recente. La pianificazione predefinita del runbook avviene ogni giorno alle 00.00 in base al fuso orario dell'area geografica della macchina virtuale replicata. Se necessario, è anche possibile modificare la pianificazione del runbook tramite l'account di automazione. 

> [!NOTE]
> Per abilitare gli aggiornamenti automatici non è necessario riavviare le macchine virtuali di Azure e la replica in corso non subisce alcun impatto.

> [!NOTE]
> La fatturazione per i processi usati dall'account di automazione è basata sul numero di minuti di esecuzione del processo usati in quel mese; per impostazione predefinita 500 minuti sono inclusi come unità gratuite per un account di automazione. L'esecuzione dei processi giornalieri va da **alcuni secondi a circa un minuto**; sono previsti **crediti gratuiti**.

UNITÀ GRATUITE INCLUSE (AL MESE)**   PREZZO tempo di esecuzione del processo    500 minuti a ₹ 0,14/minuto

## <a name="enable-automatic-updates"></a>Abilitare gli aggiornamenti automatici

È possibile scegliere di consentire a Site Recovery di gestire gli aggiornamenti nei modi seguenti:

- [Come parte del passaggio di abilitazione della replica](#as-part-of-the-enable-replication-step)
- [Attivare o disattivare le impostazioni di aggiornamento dell'estensione nell'insieme di credenziali](#toggle-the-extension-update-settings-inside-the-vault)

### <a name="as-part-of-the-enable-replication-step"></a>Come parte del passaggio di abilitazione della replica:

Quando si abilita la replica di una macchina virtuale a partire [dalla visualizzazione della macchina virtuale](azure-to-azure-quickstart.md) o [dall'insieme di credenziali di Servizi di ripristino](azure-to-azure-how-to-enable-replication.md), verrà visualizzata l'opzione per scegliere di consentire a Site Recovery di gestire gli aggiornamenti per l'estensione di Site Recovery o di eseguire l'operazione manualmente.

![enable-replication-auto-update](./media/azure-to-azure-autoupdate/enable-rep.png)

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Attivare o disattivare le impostazioni di aggiornamento dell'estensione nell'insieme di credenziali

1. Nell'insieme di credenziali passare a **Manage** (Gestisci) -> **Site Recovery Infrastructure** (Infrastruttura di Site Recovery)
2. In **For Azure virtual Machines** (Per le macchine virtuali di Azure) -> **Extension Update Settings** (Impostazioni di aggiornamento dell'estensione), fare clic sul pulsante Attiva/Disattiva per scegliere se consentire ad *Azure Site Recovery di gestire gli aggiornamenti* o se *gestirli manualmente*. Fare clic su **Save**.

![vault-toggle-auto-update](./media/azure-to-azure-autoupdate/vault-toggle.png)

> [!Important] 
> Se si sceglie *Allow ASR to manage* (Consenti ad Azure Site Recovery di gestire), l'impostazione viene applicata a tutte le macchine virtuali nell'insieme di credenziali corrispondente.


> [!Note] 
> Entrambe le opzioni segnalano con quale account di automazione vengono gestiti gli aggiornamenti. Se è la prima volta che si abilita questa funzionalità in un insieme di credenziali, verrà creato un nuovo account di automazione. Tutte le operazioni di replica successive nello stesso insieme di credenziali useranno l'account precedentemente creato.

**Se si vuole usare un account di automazione personalizzato, usare lo script seguente:**

```azurepowershell
param(
    [Parameter(Mandatory=$true)]
    [String] $VaultResourceId,

    [Parameter(Mandatory=$true)]
    [ValidateSet("Enabled",'Disabled')]
    [Alias("Enabled or Disabled")]
    [String] $AutoUpdateAction,

    [Parameter(Mandatory=$false)]
    [String] $AutomationAccountArmId
)

$SiteRecoveryRunbookName = "Modify-AutoUpdateForVaultForPatner"
$TaskId = [guid]::NewGuid().ToString()
$SubscriptionId = "00000000-0000-0000-0000-000000000000"
$AsrApiVersion = "2018-01-10"
$RunAsConnectionName = "AzureRunAsConnection"
$ArmEndPoint = "https://management.azure.com"
$AadAuthority = "https://login.windows.net/"
$AadAudience = "https://management.core.windows.net/"
$AzureEnvironment = "AzureCloud"
$Timeout = "160"

function Throw-TerminatingErrorMessage
{
    Param
    (
        [Parameter(Mandatory=$true)]
        [String]
        $Message
    )

    throw ("Message: {0}, TaskId: {1}.") -f $Message, $TaskId
}

function Write-Tracing
{
    Param
    (
        [Parameter(Mandatory=$true)]      
        [ValidateSet("Informational", "Warning", "ErrorLevel", "Succeeded", IgnoreCase = $true)]
        [String]
        $Level,

        [Parameter(Mandatory=$true)]
        [String]
        $Message,

        [Switch]
        $DisplayMessageToUser
    )

    Write-Output $Message

}

function Write-InformationTracing
{
    Param
    (
        [Parameter(Mandatory=$true)]
        [String]
        $Message
    )

    Write-Tracing -Message $Message -Level Informational -DisplayMessageToUser
}

function ValidateInput()
{
    try
    {
        if(!$VaultResourceId.StartsWith("/subscriptions", [System.StringComparison]::OrdinalIgnoreCase))
        {
            $ErrorMessage = "The vault resource id should start with /subscriptions."
            throw $ErrorMessage
        }

        $Tokens = $VaultResourceId.SubString(1).Split("/")
        if(!($Tokens.Count % 2 -eq 0))
        {
            $ErrorMessage = ("Odd Number of tokens: {0}." -f $Tokens.Count)
            throw $ErrorMessage
        }

        if(!($Tokens.Count/2 -eq 4))
        {
            $ErrorMessage = ("Invalid number of resource in vault ARM id expected:4, actual:{0}." -f ($Tokens.Count/2))
            throw $ErrorMessage
        }

        if($AutoUpdateAction -ieq "Enabled" -and [string]::IsNullOrEmpty($AutomationAccountArmId))
        {
            $ErrorMessage = ("The automation account ARM id should not be null or empty when AutoUpdateAction is enabled.")
            throw $ErrorMessage
        }
    }
    catch
    {
        $ErrorMessage = ("ValidateInput failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

function Initialize-SubscriptionId()
{
    try
    {
        $Tokens = $VaultResourceId.SubString(1).Split("/")

        $Count = 0
        $ArmResources = @{}
        while($Count -lt $Tokens.Count)
        {
            $ArmResources[$Tokens[$Count]] = $Tokens[$Count+1]
            $Count = $Count + 2
        }
        
        return $ArmResources["subscriptions"]
    }
    catch
    {
        Write-Tracing -Level ErrorLevel -Message ("Initialize-SubscriptionId: failed with [Exception: {0}]." -f $_.Exception) -DisplayMessageToUser
        throw
    }
}

function Invoke-InternalRestMethod($Uri, $Headers, [ref]$Result)
{
    $RetryCount = 0
    $MaxRetry = 3
    do
    {
        try
        {
            $ResultObject = Invoke-RestMethod -Uri $Uri -Headers $Headers    
            ($Result.Value) += ($ResultObject)
            break
        }
        catch
        {
            Write-InformationTracing ("Retry Count: {0}, Exception: {1}." -f $RetryCount, $_.Exception)
            $RetryCount++
            if(!($RetryCount -le $MaxRetry))
            {
                throw
            }

            Start-Sleep -Milliseconds 2000
        }
    }while($true)
}

function Invoke-InternalWebRequest($Uri, $Headers, $Method, $Body, $ContentType, [ref]$Result)
{
    $RetryCount = 0
    $MaxRetry = 3
    do
    {
        try
        {
            $ResultObject = Invoke-WebRequest -Uri $UpdateUrl -Headers $Header -Method 'PATCH' `
                -Body $InputJson  -ContentType "application/json" -UseBasicParsing
            ($Result.Value) += ($ResultObject)
            break
        }
        catch
        {
            Write-InformationTracing ("Retry Count: {0}, Exception: {1}." -f $RetryCount, $_.Exception)
            $RetryCount++
            if(!($RetryCount -le $MaxRetry))
            {
                throw
            }

            Start-Sleep -Milliseconds 2000
        }
    }while($true)
}

function Get-Header([ref]$Header, $AadAudience, $AadAuthority, $RunAsConnectionName){
    try 
    {
        $RunAsConnection = Get-AutomationConnection -Name $RunAsConnectionName
        $TenantId = $RunAsConnection.TenantId
        $ApplicationId = $RunAsConnection.ApplicationId
        $CertificateThumbprint = $RunAsConnection.CertificateThumbprint
        $Path = "cert:\CurrentUser\My\{0}" -f $CertificateThumbprint
        $Secret = Get-ChildItem -Path $Path
        $ClientCredential = New-Object Microsoft.IdentityModel.Clients.ActiveDirectory.ClientAssertionCertificate(
                $ApplicationId,
                $Secret)

        # Trim the forward slash from the AadAuthority if it exist.
        $AadAuthority = $AadAuthority.TrimEnd("/")
        $AuthContext = New-Object Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext(
            "{0}/{1}" -f $AadAuthority, $TenantId )
        $AuthenticationResult = $authContext.AcquireToken($AadAudience, $Clientcredential)
        $Header.Value['Content-Type'] = 'application\json'
        $Header.Value['Authorization'] = $AuthenticationResult.CreateAuthorizationHeader()
        $Header.Value["x-ms-client-request-id"] = $TaskId + "/" + (New-Guid).ToString() + "-" + (Get-Date).ToString("u")
    }
    catch
    {
        $ErrorMessage = ("Get-BearerToken: failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

function Get-ProtectionContainerToBeModified([ref] $ContainerMappingList)
{
    try 
    {
        Write-InformationTracing ("Get protection container mappings : {0}." -f $VaultResourceId)
        $ContainerMappingListUrl = $ArmEndPoint + $VaultResourceId + "/replicationProtectionContainerMappings" + "?api-version=" + $AsrApiVersion
        
        Write-InformationTracing ("Getting the bearer token and the header.")
        Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
        
        $Result = @()
        Invoke-InternalRestMethod -Uri $ContainerMappingListUrl -Headers $header -Result ([ref]$Result)
        $ContainerMappings = $Result[0]

        Write-InformationTracing ("Total retrieved container mappings: {0}." -f $ContainerMappings.Value.Count)
        foreach($Mapping in $ContainerMappings.Value)
        {
            if(($Mapping.properties.providerSpecificDetails -eq $null) -or ($Mapping.properties.providerSpecificDetails.instanceType -ine "A2A"))
            {
                Write-InformationTracing ("Mapping properties: {0}." -f ($Mapping.properties))
                Write-InformationTracing ("Ignoring container mapping: {0} as the provider does not match." -f ($Mapping.Id))
                continue;
            }

            if($Mapping.Properties.State -ine "Paired")
            {
                Write-InformationTracing ("Ignoring container mapping: {0} as the the state is not paired." -f ($Mapping.Id))
                continue;
            }

            Write-InformationTracing ("Provider specific details {0}." -f ($Mapping.properties.providerSpecificDetails))
            $MappingAutoUpdateStatus = $Mapping.properties.providerSpecificDetails.agentAutoUpdateStatus
            $MappingAutomationAccountArmId = $Mapping.properties.providerSpecificDetails.automationAccountArmId
            $MappingHealthErrorCount = $Mapping.properties.HealthErrorDetails.Count

            if($AutoUpdateAction -ieq "Enabled" -and
                ($MappingAutoUpdateStatus -ieq "Enabled") -and
                ($MappingAutomationAccountArmId -ieq $AutomationAccountArmId) -and
                ($MappingHealthErrorCount -eq 0))
            {
                Write-InformationTracing ("Provider specific details {0}." -f ($Mapping.properties))
                Write-InformationTracing ("Ignoring container mapping: {0} as the auto update is already enabled and is healthy." -f ($Mapping.Id))
                continue;
            }

            ($ContainerMappingList.Value).Add($Mapping.id)
        }
    }
    catch
    {
        $ErrorMessage = ("Get-ProtectionContainerToBeModified: failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

$OperationStartTime = Get-Date
$ContainerMappingList = New-Object System.Collections.Generic.List[System.String]
$JobsInProgressList = @()
$JobsCompletedSuccessList = @()
$JobsCompletedFailedList = @()
$JobsFailedToStart = 0
$JobsTimedOut = 0
$Header = @{}

$AzureRMProfile = Get-Module -ListAvailable -Name AzureRM.Profile | Select Name, Version, Path
$AzureRmProfileModulePath = Split-Path -Parent $AzureRMProfile.Path
Add-Type -Path (Join-Path $AzureRmProfileModulePath "Microsoft.IdentityModel.Clients.ActiveDirectory.dll")

$Inputs = ("Tracing inputs VaultResourceId: {0}, Timeout: {1}, AutoUpdateAction: {2}, AutomationAccountArmId: {3}." -f $VaultResourceId, $Timeout, $AutoUpdateAction, $AutomationAccountArmId)
Write-Tracing -Message $Inputs -Level Informational -DisplayMessageToUser
$CloudConfig = ("Tracing cloud configuration ArmEndPoint: {0}, AadAuthority: {1}, AadAudience: {2}." -f $ArmEndPoint, $AadAuthority, $AadAudience)
Write-Tracing -Message $CloudConfig -Level Informational -DisplayMessageToUser
$AutomationConfig = ("Tracing automation configuration RunAsConnectionName: {0}." -f $RunAsConnectionName)
Write-Tracing -Message $AutomationConfig -Level Informational -DisplayMessageToUser

ValidateInput
$SubscriptionId = Initialize-SubscriptionId
Get-ProtectionContainerToBeModified ([ref]$ContainerMappingList)

$Input = @{
  "properties"= @{
    "providerSpecificInput"= @{
        "instanceType" = "A2A"
        "agentAutoUpdateStatus" = $AutoUpdateAction
        "automationAccountArmId" = $AutomationAccountArmId
    }
  }
}
$InputJson = $Input |  ConvertTo-Json

if ($ContainerMappingList.Count -eq 0)
{
    Write-Tracing -Level Succeeded -Message ("Exiting as there are no container mappings to be modified.") -DisplayMessageToUser
    exit
}

Write-InformationTracing ("Container mappings to be updated has been retrieved with count: {0}." -f $ContainerMappingList.Count)

try
{
    Write-InformationTracing ("Start the modify container mapping jobs.")
    ForEach($Mapping in $ContainerMappingList)
    {
    try {
            $UpdateUrl = $ArmEndPoint + $Mapping + "?api-version=" + $AsrApiVersion
            Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
            
            $Result = @()
            Invoke-InternalWebRequest -Uri $UpdateUrl -Headers $Header -Method 'PATCH' `
                -Body $InputJson  -ContentType "application/json" -Result ([ref]$Result)
            $Result = $Result[0]

            $JobAsyncUrl = $Result.Headers['Azure-AsyncOperation']
            Write-InformationTracing ("The modify container mapping job invoked with async url: {0}." -f $JobAsyncUrl)
            $JobsInProgressList += $JobAsyncUrl;

            # Rate controlling the set calls to maximum 60 calls per minute.
            # ASR throttling for set calls is 200 in 1 minute.
            Start-Sleep -Milliseconds 1000
        }
        catch{
            Write-InformationTracing ("The modify container mappings job creation failed for: {0}." -f $Ru)
            Write-InformationTracing $_
            $JobsFailedToStart++
        }
    }

    Write-InformationTracing ("Total modify container mappings has been initiated: {0}." -f $JobsInProgressList.Count)
}
catch
{
    $ErrorMessage = ("Modify container mapping jobs failed with [Exception: {0}]." -f $_.Exception)
    Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}

try
{
    while($JobsInProgressList.Count -ne 0)
    {
        Sleep -Seconds 30
        $JobsInProgressListInternal = @()
        ForEach($JobAsyncUrl in $JobsInProgressList)
        {
            try
            {
                Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
                $Result = Invoke-RestMethod -Uri $JobAsyncUrl -Headers $header
                $JobState = $Result.Status
                if($JobState -ieq "InProgress")
                {
                    $JobsInProgressListInternal += $JobAsyncUrl
                }
                elseif($JobState -ieq "Succeeded" -or `
                    $JobState -ieq "PartiallySucceeded" -or `
                    $JobState -ieq "CompletedWithInformation")
                {
                    Write-InformationTracing ("Jobs succeeded with state: {0}." -f $JobState)
                    $JobsCompletedSuccessList += $JobAsyncUrl
                }
                else
                {
                    Write-InformationTracing ("Jobs failed with state: {0}." -f $JobState)
                    $JobsCompletedFailedList += $JobAsyncUrl
                }
            }
            catch
            {
                Write-InformationTracing ("The get job failed with: {0}. Ignoring the exception and retrying the next job." -f $_.Exception)

                # The job on which the tracking failed, will be considered in progress and tried again later.
                $JobsInProgressListInternal += $JobAsyncUrl
            }

            # Rate controlling the get calls to maximum 120 calls per minute.
            # ASR throttling for get calls is 10000 in 60 minutes.
            Start-Sleep -Milliseconds 500
        }

        Write-InformationTracing ("Jobs remaining {0}." -f $JobsInProgressListInternal.Count)

        $CurrentTime = Get-Date
        if($CurrentTime -gt $OperationStartTime.AddMinutes($Timeout))
        {
            Write-InformationTracing ("Tracing modify cloud pairing jobs has timed out.")
            $JobsTimedOut = $JobsInProgressListInternal.Count
            $JobsInProgressListInternal = @()
        }

        $JobsInProgressList = $JobsInProgressListInternal
    }
}
catch
{
    $ErrorMessage = ("Tracking modify cloud pairing jobs failed with [Exception: {0}]." -f $_.Exception)
    Write-Tracing -Level ErrorLevel -Message $ErrorMessage  -DisplayMessageToUser
    Throw-TerminatingErrorMessage -Message $ErrorMessage 
}

Write-InformationTracing ("Tracking modify cloud pairing jobs completed.")
Write-InformationTracing ("Modify cloud pairing jobs success: {0}." -f $JobsCompletedSuccessList.Count)
Write-InformationTracing ("Modify cloud pairing jobs failed: {0}." -f $JobsCompletedFailedList.Count)
Write-InformationTracing ("Modify cloud pairing jobs failed to start: {0}." -f $JobsFailedToStart)
Write-InformationTracing ("Modify cloud pairing jobs timedout: {0}." -f $JobsTimedOut)

if($JobsTimedOut -gt  0)
{
    $ErrorMessage = "One or more modify cloud pairing jobs has timedout."
    Write-Tracing -Level ErrorLevel -Message ($ErrorMessage)   
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}
elseif($JobsCompletedSuccessList.Count -ne $ContainerMappingList.Count)
{
    $ErrorMessage = "One or more modify cloud pairing jobs failed."
    Write-Tracing -Level ErrorLevel -Message ($ErrorMessage)
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}

Write-Tracing -Level Succeeded -Message ("Modify cloud pairing completed.") -DisplayMessageToUser
```

### <a name="manage-manually"></a>Gestire manualmente

1. Se sono disponibili nuovi aggiornamenti per il servizio Mobility installato nella macchina virtuale di Azure, viene visualizzata la notifica "È disponibile un nuovo aggiornamento dell'agente di replica di Site Recovery. Fare clic per installare."

     ![Finestra Elementi replicati](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)
3. Selezionare la notifica per aprire la pagina di selezione della macchina virtuale.
4. Selezionare le macchine virtuali su cui aggiornare il servizio Mobility e quindi selezionare **OK**.

     ![Elenco delle macchine virtuali in Elementi replicati](./media/vmware-azure-install-mobility-service/update-okpng.png)

Verrà avviato il processo di aggiornamento del servizio Mobility per ognuna delle macchine virtuali selezionate.


## <a name="common-issues--troubleshooting"></a>Problemi comuni e risoluzione dei problemi

In caso di problemi con gli aggiornamenti automatici, verrà visualizzata una notifica in "Problemi comuni" nel dashboard dell'insieme di credenziali. 

Se si tenta di abilitare gli aggiornamenti automatici e l'operazione non dà esito positivo, vedere di seguito per la risoluzione dei problemi.

**Errore**: non si è autorizzati a creare un account RunAs di Azure (entità servizio) e a concedere il ruolo Collaboratore all'entità servizio. 
- Azione consigliata: verificare che all'account connesso sia assegnato il ruolo di "Collaboratore" e ripetere l'operazione. Fare riferimento a [questo](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) documento per altre informazioni sull'assegnazione delle autorizzazioni appropriate.
 
Quando gli aggiornamenti automatici sono attivati, la maggior parte dei problemi può essere risolta dal servizio Site Recovery e richiede di fare clic sul pulsante **Ripara**.

![repair-button](./media/azure-to-azure-autoupdate/repair.png)

Se il pulsante Ripara non è disponibile, fare riferimento al messaggio di errore visualizzato nel riquadro delle impostazioni dell'estensione.

 - **Errore**: l'account RunAs non ha l'autorizzazione per accedere alla risorsa dei servizi di ripristino.

    **Azione consigliata**: eliminare e [ricreare l'account RunAs](https://docs.microsoft.com/azure/automation/automation-create-runas-account) o verificare che l'applicazione Azure Active Directory dell'account RunAs di Automazione abbia accesso alla risorsa dei servizi di ripristino.

- **Errore**: impossibile trovare l'account RunAs. È possibile che uno degli elementi seguenti (applicazione di Azure Active Directory, entità servizio, ruolo, asset di certificato di Automazione, asset di connessione di Automazione) o l'identificazione personale non siano identici tra certificato e connessione. 

    **Azione consigliata**: eliminare e [ricreare l'account RunAs](https://docs.microsoft.com/azure/automation/automation-create-runas-account).
