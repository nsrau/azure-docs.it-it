---
title: Inviare un messaggio di posta elettronica da un runbook di Automazione di Azure
description: Informazioni su come usare SendGrid per inviare un messaggio di posta elettronica all'interno di un runbook.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 07/15/2019
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: ce05aadb53cc3ad24ed65ea139594010e1aef047
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68235092"
---
# <a name="tutorial-send-an-email-from-an-azure-automation-runbook"></a>Esercitazione: Inviare un messaggio di posta elettronica da un runbook di Automazione di Azure

Per inviare un messaggio di posta elettronica da un runbook con [SendGrid](https://sendgrid.com/solutions), è possibile usare PowerShell. Questa esercitazione illustra come creare un runbook riutilizzabile che invia un messaggio di posta elettronica usando una chiave API archiviata in [Azure Key Vault](/azure/key-vault/).

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
>
> * Creare un insieme di credenziali delle chiavi di Azure
> * Archiviare la chiave API per SendGrid nell'insieme di credenziali delle chiavi
> * Creare un runbook che recuperi la chiave API e invii un messaggio di posta elettronica

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, è necessario quanto segue:

* Sottoscrizione di Azure: Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Creare un account SendGrid](/azure/sendgrid-dotnet-how-to-send-email#create-a-sendgrid-account).
* [Account di automazione](automation-offering-get-started.md) con moduli **Az** e la [connessione con l'account RunAs](automation-create-runas-account.md), per archiviare ed eseguire il runbook.

## <a name="create-an-azure-keyvault"></a>Creare un insieme di credenziali delle chiavi di Azure

Per creare un insieme di credenziali delle chiavi di Azure, usare lo script di PowerShell seguente. Sostituire i valori delle variabili con i valori specifici dell'ambiente in uso. Usare Azure Cloud Shell incorporato tramite il pulsante <kbd>Prova</kbd>, che si trova nell'angolo in alto a destra del blocco di codice. È anche possibile copiare ed eseguire il codice in locale se il [modulo Azure PowerShell](/powershell/azure/install-az-ps) è installato nel computer locale.

> [!NOTE]
> Per recuperare la chiave API, usare la procedura [per trovare la chiave API per SendGrid](/azure/sendgrid-dotnet-how-to-send-email#to-find-your-sendgrid-api-key).

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

# Grant access to the KeyVault to the Automation RunAs account.
$connection = Get-AzAutomationConnection -ResourceGroupName $KeyVaultResourceGroupName -AutomationAccountName $AutomationAccountName -Name AzureRunAsConnection
$appID = $connection.FieldDefinitionValues.ApplicationId
Set-AzKeyVaultAccessPolicy -VaultName $VaultName -ServicePrincipalName $appID -PermissionsToSecrets Set, Get
```

Per le altre modalità di creazione di un insieme di credenziali delle chiavi di Azure e di archiviazione di un segreto, vedere le guide di avvio rapido di [Key Vault](/azure/key-vault/).

## <a name="import-required-modules-to-your-automation-account"></a>Importare i moduli richiesti nell'account di Automazione

Per usare Azure Key Vault in un runbook, sono necessari i moduli seguenti dell'account di Automazione:

* [Az.Profile](https://www.powershellgallery.com/packages/Az.Profile).
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault).

Fare clic su <kbd>Distribuisci in Automazione di Azure</kbd> nella scheda Automazione di Azure in Opzioni di installazione. Verrà aperto il portale di Azure. Nella pagina Importa selezionare l'account di Automazione e fare clic su <kbd>OK</kbd>.

Per altri metodi di aggiunta dei metodi richiesti, vedere [Importare i moduli](/azure/automation/shared-resources/modules#import-modules).

## <a name="create-the-runbook-to-send-an-email"></a>Creare il runbook per inviare un messaggio di posta elettronica

Dopo aver creato un insieme di credenziali delle chiavi e aver archiviato la chiave API per SendGrid, è possibile creare il runbook che recupererà la chiave API e invierà un messaggio di posta elettronica.

Questo runbook usa l'[account RunAs](automation-create-runas-account.md) di AzureRunAsConnection per eseguire l'autenticazione con Azure e recuperare il segreto da Azure Key Vault.

Usare questo esempio per creare un runbook denominato **Send-GridMailMessage**. È possibile modificare lo script di PowerShell e riutilizzarlo per scenari diversi.

1. Accedere all'account di Automazione di Azure.
2. In **Automazione processi** selezionare **Runbook**.
3. Nella parte superiore dell'elenco di runbook, selezionare **+ Crea un runbook**.
4. Nella pagina **Aggiungi runbook** immettere **Send-GridMailMessage** per il nome del runbook. Per il tipo di runbook selezionare **PowerShell**. Scegliere quindi **Create** (Crea).
   ![Creare un runbook](./media/automation-send-email/automation-send-email-runbook.png)
5. Viene creato il runbook e si apre la pagina **Modifica runbook di PowerShell**.
   ![Modificare il runbook](./media/automation-send-email/automation-send-email-edit.png)
6. Copiare l'esempio di PowerShell seguente nella pagina **Modifica**. Assicurarsi che `$VaultName` sia il nome specificato durante la creazione dell'insieme di credenziali delle chiavi.

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
Se il messaggio di posta elettronica non viene visualizzato inizialmente, controllare le cartelle della **Posta** **indesiderata**.

## <a name="clean-up"></a>Eseguire la pulizia

Quando non è più necessario, eliminare il runbook. A questo scopo, selezionare il runbook nell'elenco di runbook e fare clic su **Elimina**.

Per eliminare l'insieme di credenziali delle chiavi, usare il cmdlet [Remove-AzureRMKeyVault](/powershell/module/azurerm.keyvault/remove-azurermkeyvault?view=azurermps).

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Passaggi successivi

* Per problemi durante la creazione o l'avvio del runbook, vedere [Risoluzione dei problemi relativi ai runbook](./troubleshoot/runbooks.md).
* Per aggiornare i moduli nell'account di Automazione, vedere [Come aggiornare i moduli Azure PowerShell in Automazione di Azure](automation-update-azure-modules.md).
* Per monitorare l'esecuzione del runbook, vedere [Inoltrare lo stato del processo e i flussi del processo da Automazione ai log di Monitoraggio di Azure](automation-manage-send-joblogs-log-analytics.md).
* Per attivare un runbook usando un avviso, vedere [Usare un avviso per attivare un runbook di Automazione di Azure](automation-create-alert-triggered-runbook.md).
