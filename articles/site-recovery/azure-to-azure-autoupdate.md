---
title: L'aggiornamento automatico del servizio Mobility nel ripristino di emergenza da Azure ad Azure | Microsoft Docs
description: Panoramica di aggiornamento automatico del servizio Mobility quando si replicano macchine virtuali di Azure usando Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/20/2019
ms.author: rajanaki
ms.openlocfilehash: 1d36145b2a38c0f1106b4468eab226996e270ae1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65922132"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Aggiornamento automatico del servizio Mobility nella replica di Azure ad Azure

Azure Site Recovery Usa un ritmo di rilascio mensile per risolvere i problemi e migliorare le funzionalità esistenti o aggiungerne di nuovi. Per rimanere aggiornati con il servizio, è necessario pianificare la distribuzione delle patch ogni mese. Per evitare il sovraccarico associato a ogni aggiornamento, è invece possibile consentire Site Recovery gestire gli aggiornamenti dei componenti.

Come indicato nella [architettura di ripristino di emergenza da Azure ad Azure](azure-to-azure-architecture.md), il servizio Mobility viene installato in tutte le macchine virtuali di Azure (VM) per cui è abilitata la replica, durante la replica di macchine virtuali da un'area di Azure a un altro. Quando si usano gli aggiornamenti automatici, ogni nuova versione consente di aggiornare l'estensione del servizio Mobility.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-automatic-updates-work"></a>Lavoro degli aggiornamenti automatica

Quando si usa Site Recovery per gestire gli aggiornamenti, distribuisce un runbook globale (usato da servizi di Azure) tramite un account di automazione creato nella stessa sottoscrizione dell'insieme di credenziali. Ogni insieme di credenziali Usa un account di automazione. Il runbook controlla ogni macchina virtuale in un insieme di credenziali per gli aggiornamenti automatici attivi e consente di aggiornare l'estensione del servizio Mobility se è disponibile una versione più recente.

La pianificazione di runbook predefinita si ripete ogni giorno alle 12:00 AM nel fuso orario dell'area geografica della macchina virtuale replicata. È anche possibile modificare la pianificazione del runbook tramite l'account di automazione.

> [!NOTE]
> A partire da 35 Rollup aggiornamento, è possibile scegliere un account di automazione esistente da utilizzare per gli aggiornamenti. Prima dell'aggiornamento, Site Recovery creato questo account per impostazione predefinita. Questa opzione è disponibile quando si abilita la replica per una macchina virtuale. Se si modifica l'impostazione, verrà applicata per tutte le macchine virtuali di Azure protette nel medesimo insieme di credenziali.
 
> Attivazione degli aggiornamenti automatici non richiedono il riavvio delle VM di Azure o influire sulla replica in corso.

> Processo di fatturazione nell'account di automazione è basato sul numero di minuti di esecuzione processo usato in un mese. Per impostazione predefinita, 500 minuti di esecuzione sono inclusi come unità gratuite per un account di automazione. L'esecuzione del processo richiede alcuni secondi a circa un minuto, ogni giorno e viene illustrata come unità gratuite.

| Unità gratuite incluse (ogni mese) | Prezzo |
|---|---|
| 500 minuti di esecuzione di processi di runtime | ₹0.14 al minuto

## <a name="enable-automatic-updates"></a>Abilitare gli aggiornamenti automatici

È possibile consentire a Site Recovery gestire gli aggiornamenti nei modi seguenti.

### <a name="manage-as-part-of-the-enable-replication-step"></a>Gestisci come parte del passaggio abilita la replica

Quando si abilita la replica per una macchina virtuale di avvio [dalla visualizzazione macchina virtuale](azure-to-azure-quickstart.md) oppure [dall'insieme di credenziali di servizi di ripristino](azure-to-azure-how-to-enable-replication.md), è possibile consentire il ripristino del sito gestire gli aggiornamenti per l'estensione Site Recovery o gestirlo manualmente.

![Impostazioni dell'estensione](./media/azure-to-azure-autoupdate/enable-rep.png)

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Attivare o disattivare le impostazioni di aggiornamento dell'estensione nell'insieme di credenziali

1. Nell'insieme di credenziali, andare al **Manage** > **infrastruttura di Site Recovery**.
2. Sotto **per le macchine virtuali di Azure** > **impostazioni di aggiornamento dell'estensione**, attivare il **consentire a Site Recovery per gestire** attiva/disattiva. Per gestire manualmente, disattivare tale funzionalità. 
3. Selezionare **Salva**.

![Impostazioni di aggiornamento dell'estensione](./media/azure-to-azure-autoupdate/vault-toggle.png)

> [!Important]
> Quando si sceglie **consentire a Site Recovery per gestire**, l'impostazione viene applicata a tutte le macchine virtuali nell'insieme di credenziali corrispondente.


> [!Note]
> Entrambe le opzioni di notifica dell'account di automazione usato per la gestione degli aggiornamenti. Se si usa questa funzionalità in un insieme di credenziali per la prima volta, per impostazione predefinita viene creato un nuovo account di automazione. In alternativa, è possibile personalizzare l'impostazione e scegliere un account di automazione esistente. Tutte le repliche enable successivi nel medesimo insieme di credenziali usano quello creato in precedenza.

Per un account di automazione personalizzata, usare lo script seguente:

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
                Write-InformationTracing ("Ignoring container mapping: {0} as the state is not paired." -f ($Mapping.Id))
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

$AzureRMProfile = Get-Module -ListAvailable -Name Az.Accounts | Select Name, Version, Path
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

            # Rate controlling the get calls to maximum 120 calls each minute.
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

### <a name="manage-updates-manually"></a>Gestire gli aggiornamenti manualmente

1. Se sono presenti nuovi aggiornamenti per il servizio Mobility installato nella VM, verrà visualizzata la notifica seguente: "Nuovo aggiornamento dell'agente Site recovery replica è disponibile. Fare clic per installare"

     ![Finestra Elementi replicati](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)
2. Selezionare la notifica per aprire la pagina di selezione della macchina virtuale.
3. Scegliere le macchine virtuali si vuole aggiornare e quindi selezionare **OK**. Verrà avviato il servizio Mobility di aggiornamento per ogni macchina virtuale selezionata.

     ![Elenco delle macchine virtuali in Elementi replicati](./media/vmware-azure-install-mobility-service/update-okpng.png)


## <a name="common-issues-and-troubleshooting"></a>Problemi comuni e risoluzione dei problemi

Se si verifica un problema con gli aggiornamenti automatici, verrà visualizzata una notifica di errore sotto **problemi di configurazione** nel dashboard dell'insieme di credenziali.

Se si non è stato possibile abilitare gli aggiornamenti automatici, vedere i seguenti errori comuni e le azioni consigliate:

- **Errore**: non si è autorizzati a creare un account RunAs di Azure (entità servizio) e a concedere il ruolo Collaboratore all'entità servizio.

   **Azione consigliata**: Assicurarsi che sia assegnato l'account di accesso come collaboratore e ripetere l'operazione. Vedere la sezione delle autorizzazioni necessarie nel [usare il portale per creare un'entità applicazione e del servizio che può accedere alle risorse di Azure AD](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) per altre informazioni sull'assegnazione di autorizzazioni.
 
   Per risolvere la maggior parte dei problemi dopo aver abilitato aggiornamenti automatici, selezionare **Repair**. Se il pulsante di ripristino non è disponibile, vedere il messaggio di errore visualizzato nel riquadro Impostazioni estensione update.

   ![Site Recovery service Ripara nelle impostazioni di aggiornamento dell'estensione](./media/azure-to-azure-autoupdate/repair.png)

- **Errore**: l'account RunAs non ha l'autorizzazione per accedere alla risorsa dei servizi di ripristino.

    **Azione consigliata**: Eliminare e quindi [ricreare l'account RunAs](https://docs.microsoft.com/azure/automation/automation-create-runas-account). In alternativa, assicurarsi che l'applicazione di Azure Active Directory dell'account RunAs di automazione abbia accesso alla risorsa di servizi di ripristino.

- **Errore**: impossibile trovare l'account RunAs. È possibile che uno degli elementi seguenti (applicazione di Azure Active Directory, entità servizio, ruolo, asset di certificato di Automazione, asset di connessione di Automazione) o l'identificazione personale non siano identici tra certificato e connessione. 

    **Azione consigliata**: Eliminare e quindi [ricreare l'account RunAs](https://docs.microsoft.com/azure/automation/automation-create-runas-account).

-  **Errore**: L'esecuzione di Azure come certificato usata dall'account di automazione sta per scadere. 

    Il certificato autofirmato che viene creato per l'account RunAs scade un anno dalla data di creazione. È possibile rinnovarlo in qualsiasi momento prima della scadenza. Se hanno effettuato l'iscrizione per le notifiche di posta elettronica, riceverai anche messaggi di posta elettronica quando un'azione è richiesta da parte dell'utente. Questo errore verrà visualizzato 2 mesi prima della data di scadenza e passerà a un errore critico se il certificato è scaduto. Dopo che il certificato è scaduto, l'aggiornamento automatico non sarà funzionante finché non si rinnova lo stesso.

   **Azione consigliata**: Fare clic su 'Ripristina' e 'Rinnova certificato' per risolvere il problema.
    
   ![rinnovare-cert](media/azure-to-azure-autoupdate/automation-account-renew-runas-certificate.PNG)

> [!NOTE]
> Una volta che si rinnova il certificato, aggiornare la pagina in modo che lo stato corrente viene aggiornato.
