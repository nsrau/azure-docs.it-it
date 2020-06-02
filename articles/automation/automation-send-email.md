---
title: Inviare un messaggio di posta elettronica da un runbook di Automazione di Azure
description: Questo articolo illustra come inviare un messaggio di posta elettronica da un runbook.
services: automation
ms.subservice: process-automation
ms.date: 07/15/2019
ms.topic: conceptual
ms.openlocfilehash: a92f65bd88a5aec79a179a6e2d53de15c274add4
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834561"
---
# <a name="send-an-email-from-a-runbook"></a>Inviare un messaggio di posta elettronica da un runbook

Per inviare un messaggio di posta elettronica da un runbook con [SendGrid](https://sendgrid.com/solutions), è possibile usare PowerShell. 

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Account SendGrid](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account).
* [Account di Automazione](automation-offering-get-started.md) con moduli **Az**.
* [Account RunAs](automation-create-runas-account.md) per archiviare ed eseguire il runbook.

## <a name="create-an-azure-key-vault"></a>Creare un Azure Key Vault

Per creare un'istanza di Azure Key Vault, usare lo script di PowerShell seguente. Sostituire i valori delle variabili con i valori specifici dell'ambiente in uso. Usare Azure Cloud Shell incorporato tramite il pulsante **Prova**, che si trova nell'angolo in alto a destra del blocco di codice. È anche possibile copiare ed eseguire il codice in locale se nel computer locale sono installati i [moduli Az](/powershell/azure/install-az-ps).

> [!NOTE]
> Per recuperare la chiave API, usare la procedura descritta in [Trovare la chiave API di SendGrid](/azure/sendgrid-dotnet-how-to-send-email#to-find-your-sendgrid-api-key).

```azurepowershell-interactive
$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
# If you omit the SubscriptionId parameter, the default subscription is selected.
Connect-AzAccount -SubscriptionId $SubscriptionId

# Use Get-AzLocation to see your available locations.
$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "<Enter a universally unique vault name>"
$SendGridAPIKey = "<SendGrid API key>"
$AutomationAccountName = "testaa"

# Create new Resource Group, or omit this step if you already have a resource group.
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region
$resourceId = $newKeyVault.ResourceId

# Convert the SendGrid API key into a SecureString
$Secret = ConvertTo-SecureString -String $SendGridAPIKey -AsPlainText -Force
Set-AzKeyVaultSecret -VaultName $VaultName -Name 'SendGridAPIKey' -SecretValue $Secret

# Grant access to the Key Vault to the Automation Run As account.
$connection = Get-AzAutomationConnection -ResourceGroupName $KeyVaultResourceGroupName -AutomationAccountName $AutomationAccountName -Name AzureRunAsConnection
$appID = $connection.FieldDefinitionValues.ApplicationId
Set-AzKeyVaultAccessPolicy -VaultName $VaultName -ServicePrincipalName $appID -PermissionsToSecrets Set, Get
```

Per altre modalità di creazione di un'istanza di Azure Key Vault e di archiviazione di un segreto, vedere le [guide di avvio rapido di Key Vault](/azure/key-vault/).

## <a name="import-required-modules-into-your-automation-account"></a>Importare i moduli necessari nell'account di Automazione

Per usare Azure Key Vault in un runbook, è necessario importare i moduli seguenti nell'account di Automazione:

    * [Az.Profile](https://www.powershellgallery.com/packages/Az.Profile)
    * [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault)

Per le istruzioni, vedere [Importare moduli Az](shared-resources/modules.md#import-az-modules).

## <a name="create-the-runbook-to-send-an-email"></a>Creare il runbook per inviare un messaggio di posta elettronica

Una volta creata un'istanza di Key Vault e archiviata la chiave API per `SendGrid`, è possibile creare il runbook che recupererà la chiave API e invierà un messaggio di posta elettronica. Verrà usato un runbook che usa `AzureRunAsConnection` come [account RunAs](automation-create-runas-account.md) per eseguire l'autenticazione con Azure e recuperare il segreto da Azure Key Vault. Il runbook sarà denominato **Send-GridMailMessage**. È possibile modificare lo script di PowerShell usato per l'esempio e riutilizzarlo per scenari diversi.

1. Accedere all'account di Automazione di Azure.
2. In **Automazione processi** selezionare **Runbook**.
3. Nella parte superiore dell'elenco di runbook, selezionare **+ Crea un runbook**.
4. Nella pagina Aggiungi runbook immettere **Send-GridMailMessage** come nome del runbook. Per il tipo di runbook selezionare **PowerShell**. Scegliere quindi **Create** (Crea).
   ![Creare un runbook](./media/automation-send-email/automation-send-email-runbook.png)
5. Il runbook viene creato e si apre la pagina Modifica runbook di PowerShell.
   ![Modificare il runbook](./media/automation-send-email/automation-send-email-edit.png)
6. Copiare l'esempio di PowerShell seguente nella pagina Modifica. Assicurarsi che `VaultName` specifichi il nome scelto per l'istanza di Key Vault.

    ```powershell-interactive
    Param(
      [Parameter(Mandatory=$True)]
      [String] $destEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $fromEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $subject,
      [Parameter(Mandatory=$True)]
      [String] $content
    )

    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Out-Null
    $VaultName = "<Enter your vault name>"
    $SENDGRID_API_KEY = (Get-AzKeyVaultSecret -VaultName $VaultName -Name "SendGridAPIKey").SecretValueText
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Authorization", "Bearer " + $SENDGRID_API_KEY)
    $headers.Add("Content-Type", "application/json")

    $body = @{
    personalizations = @(
        @{
            to = @(
                    @{
                        email = $destEmailAddress
                    }
            )
        }
    )
    from = @{
        email = $fromEmailAddress
    }
    subject = $subject
    content = @(
        @{
            type = "text/plain"
            value = $content
        }
    )
    }

    $bodyJson = $body | ConvertTo-Json -Depth 4

    $response = Invoke-RestMethod -Uri https://api.sendgrid.com/v3/mail/send -Method Post -Headers $headers -Body $bodyJson
    ```

7. Selezionare **Pubblica** per salvare e pubblicare il runbook.

Per verificare che il runbook venga eseguito correttamente, è possibile seguire la procedura descritta in [Testare un runbook](manage-runbooks.md#test-a-runbook) o [Avviare un runbook](start-runbooks.md).

Se il messaggio di posta elettronica non viene visualizzato inizialmente, controllare la cartella **Posta** **indesiderata**.

## <a name="clean-up-resources-after-the-email-operation"></a>Pulire le risorse dopo l'operazione di posta elettronica

1. Quando il runbook non è più necessario, selezionarlo nell'elenco e fare clic su **Elimina**.

2. Eliminare l'istanza di Key Vault usando il cmdlet [Remove-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/remove-azkeyvault?view=azps-3.7.0).

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Passaggi successivi

* Per inviare i dati del processo del runbook all'area di lavoro Log Analytics, vedere [Inoltrare i dati dei processi di Automazione di Azure ai log di Monitoraggio di Azure](automation-manage-send-joblogs-log-analytics.md).
* Per monitorare le metriche e i log a livello di base, vedere [Usare un avviso per attivare un runbook di Automazione di Azure](automation-create-alert-triggered-runbook.md).
* Per risolvere i problemi generati durante le operazioni del runbook, vedere [Risolvere i problemi relativi a un runbook](./troubleshoot/runbooks.md).