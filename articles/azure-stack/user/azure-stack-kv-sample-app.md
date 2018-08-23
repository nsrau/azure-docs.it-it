---
title: Consentire alle applicazioni di recuperare i segreti di insieme di credenziali delle chiavi di Azure Stack | Microsoft Docs
description: Usare un'app di esempio per lavorare con Azure Stack di Key Vault
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3748b719-e269-4b48-8d7d-d75a84b0e1e5
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/15/2018
ms.author: sethm
ms.openlocfilehash: ed02174247de1a99f3d9a4880fd0afa60f867552
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2018
ms.locfileid: "42139415"
---
# <a name="a-sample-application-that-uses-keys-and-secrets-stored-in-a-key-vault"></a>Un'applicazione di esempio che usa le chiavi e segreti archiviati in un insieme di credenziali delle chiavi

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Seguire i passaggi descritti in questo articolo per eseguire un'applicazione di esempio (HelloKeyVault) che recupera le chiavi e segreti da un insieme di credenziali delle chiavi in Azure Stack.

## <a name="prerequisites"></a>Prerequisiti

È possibile installare i prerequisiti seguenti da Azure Stack [Development Kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), o da un client esterno con codifica basata su Windows se sei [connesse tramite VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Installare [moduli di Azure Stack-compatibili con Azure PowerShell](azure-stack-powershell-install.md).
* Scaricare il [gli strumenti necessari per lavorare con Azure Stack](azure-stack-powershell-download.md).

## <a name="create-and-get-the-key-vault-and-application-settings"></a>Creare e ottenere l'insieme di credenziali delle chiavi e le impostazioni dell'applicazione

Per preparare l'applicazione di esempio:

* Creare un insieme di credenziali delle chiavi in Azure Stack.
* Registrare un'applicazione in Azure Active Directory (Azure AD).

È possibile usare il portale di Azure o PowerShell per preparare l'applicazione di esempio. Questo articolo illustra come creare un insieme di credenziali delle chiavi e registrare un'applicazione tramite PowerShell.

>[!NOTE]
>Per impostazione predefinita, lo script di PowerShell crea una nuova applicazione in Active Directory. Tuttavia, è possibile registrare una delle applicazioni esistenti.

 Prima di eseguire lo script seguente, assicurarsi di specificare i valori per il `aadTenantName` e `applicationPassword` variabili. Se non si specifica un valore per `applicationPassword`, questo script genera una password casuale.

```powershell
$vaultName           = 'myVault'
$resourceGroupName   = 'myResourceGroup'
$applicationName     = 'myApp'
$location            = 'local'

# Password for the application. If not specified, this script will generate a random password during app creation.
$applicationPassword = ''

# Function to generate a random password for the application.
Function GenerateSymmetricKey()
{
    $key = New-Object byte[](32)
    $rng = [System.Security.Cryptography.RNGCryptoServiceProvider]::Create()
    $rng.GetBytes($key)
    return [System.Convert]::ToBase64String($key)
}

Write-Host 'Please log into your Azure Stack user environment' -foregroundcolor Green

$tenantARM = "https://management.local.azurestack.external"
$aadTenantName = "PLEASE FILL THIS IN WITH YOUR AAD TENANT NAME. FOR EXAMPLE: myazurestack.onmicrosoft.com"

# Configure the Azure Stack operator’s PowerShell environment.
Add-AzureRMEnvironment `
  -Name "AzureStackUser" `
  -ArmEndpoint $tenantARM

Set-AzureRmEnvironment `
  -Name "AzureStackAdmin" `
  -GraphAudience "https://graph.windows.net/"

$TenantID = Get-AzsDirectoryTenantId `
  -AADTenantName $aadTenantName `
  -EnvironmentName AzureStackUser

# Sign in to the user portal.
Add-AzureRmAccount `
  -EnvironmentName "AzureStackUser" `
  -TenantId $TenantID `

$now = [System.DateTime]::Now
$oneYearFromNow = $now.AddYears(1)

$applicationPassword = GenerateSymmetricKey

# Create a new Azure AD application.
$identifierUri = [string]::Format("http://localhost:8080/{0}",[Guid]::NewGuid().ToString("N"))
$homePage = "http://contoso.com"

Write-Host "Creating a new AAD Application"
$ADApp = New-AzureRmADApplication `
  -DisplayName $applicationName `
  -HomePage $homePage `
  -IdentifierUris $identifierUri `
  -StartDate $now `
  -EndDate $oneYearFromNow `
  -Password $applicationPassword

Write-Host "Creating a new AAD service principal"
$servicePrincipal = New-AzureRmADServicePrincipal `
  -ApplicationId $ADApp.ApplicationId

# Create a new resource group and a key vault in that resource group.
New-AzureRmResourceGroup `
  -Name $resourceGroupName `
  -Location $location

Write-Host "Creating vault $vaultName"
$vault = New-AzureRmKeyVault -VaultName $vaultName `
  -ResourceGroupName $resourceGroupName `
  -Sku standard `
  -Location $location

# Specify full privileges to the vault for the application.
Write-Host "Setting access policy"
Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName `
  -ObjectId $servicePrincipal.Id `
  -PermissionsToKeys all `
  -PermissionsToSecrets all

Write-Host "Paste the following settings into the app.config file for the HelloKeyVault project:"
'<add key="VaultUrl" value="' + $vault.VaultUri + '"/>'
'<add key="AuthClientId" value="' + $servicePrincipal.ApplicationId + '"/>'
'<add key="AuthClientSecret" value="' + $applicationPassword + '"/>'
Write-Host

```

La schermata successiva mostra l'output dello script utilizzato per creare l'insieme di credenziali delle chiavi:

![Chiave dell'insieme di credenziali con le chiavi di accesso](media/azure-stack-kv-sample-app/settingsoutput.png)

Annotare il **VaultUrl**, **AuthClientId**, e **AuthClientSecret** valori restituiti dallo script precedente. Per eseguire l'applicazione HelloKeyVault è usare questi valori.

## <a name="download-and-configure-the-sample-application"></a>Scaricare e configurare l'applicazione di esempio

Scaricare l'esempio di insieme di credenziali delle chiavi da Azure [esempi di client di Key Vault](https://www.microsoft.com/en-us/download/details.aspx?id=45343) pagina. Estrarre il contenuto del file con estensione zip nella workstation di sviluppo. Esistono due applicazioni nella cartella degli esempi, questo articolo Usa HelloKeyVault.

Per caricare l'esempio HelloKeyVault:

* Selezionare il **Microsoft.Azure.KeyVault.Samples** > **esempi** > **HelloKeyVault** cartella.
* Aprire l'applicazione HelloKeyVault in Visual Studio.

### <a name="configure-the-sample-application"></a>Configurare l'applicazione di esempio

In Visual Studio:

* Aprire il file HelloKeyVault\App.config e Sfoglia per trovare le &lt; **appSettings** &gt; elemento.
* Aggiorna il **VaultUrl**, **AuthClientId**, e **AuthClientSecret** chiavi con i valori restituiti dall'oggetto utilizzato per creare l'insieme di credenziali delle chiavi. (Per impostazione predefinita, il file app. config contiene un segnaposto per *AuthCertThumbprint*. Sostituire il segnaposto con *AuthClientSecret*.)

  ![Impostazioni app](media/azure-stack-kv-sample-app/appconfig.png)

* Ricompilare la soluzione.

## <a name="run-the-application"></a>Eseguire l'applicazione

Quando si esegue HelloKeyVault, l'applicazione accede ad Azure AD e quindi Usa il token AuthClientSecret per l'autenticazione in key vault in Azure Stack.

È possibile usare l'esempio HelloKeyVault per:

* Eseguire operazioni di base, ad esempio creare, crittografare, eseguire il wrapping ed eliminare su chiavi e segreti.
* Passare i parametri, ad esempio *crittografare* e *decrittografare* a HelloKeyVault e applicare le modifiche specificate a un insieme di credenziali delle chiavi.

## <a name="next-steps"></a>Passaggi successivi

[Distribuire una VM con una password dell'insieme di credenziali delle chiavi](azure-stack-kv-deploy-vm-with-secret.md)

[Distribuire una macchina virtuale con un certificato di Key Vault](azure-stack-kv-push-secret-into-vm.md)
