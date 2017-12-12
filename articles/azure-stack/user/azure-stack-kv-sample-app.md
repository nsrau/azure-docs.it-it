---
title: Consentono alle applicazioni di recuperare i segreti di insieme di credenziali chiave di Azure Stack | Documenti Microsoft
description: Usare un'app di esempio per lavorare con l'insieme di credenziali chiave di Azure Stack
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 3748b719-e269-4b48-8d7d-d75a84b0e1e5
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/26/2017
ms.author: mabrigg
ms.openlocfilehash: 50103dca21d047c5cee211b2250e750739131bc1
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="sample-application-that-uses-keys-and-secrets-stored-in-a-key-vault"></a>Applicazione di esempio che utilizza le chiavi e segreti archiviati in un insieme di credenziali chiave

In questo articolo si mostra come eseguire un'applicazione di esempio (HelloKeyVault) che recupera i segreti e tutte le chiavi da un insieme di credenziali chiave nello Stack di Azure.

## <a name="prerequisites"></a>Prerequisiti 

Eseguire i seguenti prerequisiti dal [Development Kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), o da un client esterno con codifica basata su Windows in caso di [connessi tramite VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Installare [moduli di PowerShell Azure compatibile Stack Azure](azure-stack-powershell-install.md).  
* Scaricare il [gli strumenti necessari per lavorare con Azure Stack](azure-stack-powershell-download.md). 

## <a name="create-and-get-the-key-vault-and-application-settings"></a>Creare e ottenere la chiave dell'insieme di credenziali e le impostazioni dell'applicazione

In primo luogo, si deve creare un insieme di credenziali chiave nello Stack di Azure e registrare un'applicazione in Azure Active Directory (Azure AD). È possibile creare e registrare gli insiemi di credenziali chiave usando il portale di Azure o PowerShell. In questo articolo viene illustrato il modo di PowerShell per eseguire le attività. Per impostazione predefinita, questo script di PowerShell crea una nuova applicazione in Active Directory. Tuttavia, è possibile inoltre utilizzare una delle applicazioni esistenti. Assicurarsi di fornire un valore per il `aadTenantName` e `applicationPassword` variabili. Se non si specifica un valore per il `applicationPassword` variabile, questo script genera una password casuale. 

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
Login-AzureRmAccount `
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

# Create a new resource group and a key vault within that resource group.
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

Nella schermata seguente mostra l'output dello script precedente:

![Configurazione dell'applicazione](media/azure-stack-kv-sample-app/settingsoutput.png)

Annotare il **VaultUrl**, **AuthClientId**, e **AuthClientSecret** valori restituiti dallo script precedente. Utilizzare questi valori per eseguire l'applicazione HelloKeyVault.

## <a name="download-and-run-the-sample-application"></a>Scaricare ed eseguire l'applicazione di esempio

Scaricare l'esempio di chiave dell'insieme di credenziali da Azure [esempi client insieme di credenziali chiave](https://www.microsoft.com/en-us/download/details.aspx?id=45343) pagina. Estrarre il contenuto del file con estensione zip nella workstation di sviluppo. Vi sono due esempi all'interno della cartella di esempi. Si usa l'esempio HellpKeyVault in questo articolo. Individuare il **Microsoft.Azure.KeyVault.Samples** > **esempi** > **HelloKeyVault** cartella e aprire l'applicazione HelloKeyVault in Visual Studio. 

Aprire il file HelloKeyVault\App.config e sostituire i valori del <appSettings> elemento con la **VaultUrl**, **AuthClientId**, e **AuthClientSecret** valori restituito dallo script precedente. Si noti che per impostazione predefinita il file app. config contiene un segnaposto per *AuthCertThumbprint*, ma utilizzare *AuthClientSecret* invece. Dopo la sostituzione, le impostazioni di ricompilare la soluzione e avviare l'applicazione.

![Impostazioni app](media/azure-stack-kv-sample-app/appconfig.png)
 
L'applicazione accede ad Azure AD e quindi utilizza tale token per l'autenticazione per l'insieme di credenziali chiave nello Stack di Azure. L'applicazione esegue operazioni, ad esempio creare, crittografare, eseguire il wrapping ed eliminare nelle chiavi e segreti dell'insieme di credenziali chiave. È anche possibile passare parametri specifici, ad esempio *crittografare* e *decrittografare* all'applicazione, che assicura che l'applicazione esegue solo le operazioni con l'insieme di credenziali. 


## <a name="next-steps"></a>Passaggi successivi
[Distribuire una VM con una password dell'insieme di credenziali delle chiavi](azure-stack-kv-deploy-vm-with-secret.md)

[Distribuire una macchina virtuale con un certificato di chiave dell'insieme di credenziali](azure-stack-kv-push-secret-into-vm.md)



