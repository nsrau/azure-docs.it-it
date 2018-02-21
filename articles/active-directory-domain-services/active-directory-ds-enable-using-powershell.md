---
title: Abilitare Azure Active Directory Domain Services con PowerShell | Microsoft Docs
description: Abilitare Azure Active Directory Domain Services con PowerShell
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: maheshu
ms.openlocfilehash: a456a6265400abe1d3a3620df74e41d8b4399b97
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Abilitare Azure Active Directory Domain Services con PowerShell
Questo articolo illustra come abilitare Azure Active Directory (AD) Domain Services con PowerShell.

## <a name="task-1-install-the-required-powershell-modules"></a>Attività 1: Installare i moduli di PowerShell necessari

### <a name="install-and-configure-azure-ad-powershell"></a>Installare e configurare Azure AD PowerShell
Seguire le istruzioni nell'articolo per [installare il modulo Azure AD PowerShell e connettersi ad Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Installare e configurare Azure PowerShell
Seguire le istruzioni nell'articolo per [installare il modulo Azure PowerShell e connettersi alla sottoscrizione di Azure](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="task-2-create-the-required-service-principal-in-your-azure-ad-directory"></a>Attività 2: Creare l'entità servizio richiesta nella directory di Azure AD
Digitare il comando PowerShell seguente per creare l'entità servizio richiesta per Azure AD Domain Services nella directory di Azure AD.
```powershell
# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId “2565bd9d-da50-47d4-8b85-4c97f669dc36”
```

## <a name="task-3-create-and-configure-the-aad-dc-administrators-group"></a>Attività 3: Creare e configurare il gruppo 'AAD DC Administrators'
L'attività successiva consiste nel creare il gruppo di amministratori che verrà usato per delegare le attività di amministrazione nel dominio gestito.
```powershell
# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Dopo aver creato il gruppo, aggiungere un paio di utenti.
```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso100.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="task-4-register-the-azure-ad-domain-services-resource-provider"></a>Attività 4: Registrare il provider di risorse di Azure AD Domain Services
Digitare il comando PowerShell seguente per registrare il provider di risorse per Azure AD Domain Services:
```powershell
# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AAD
```

## <a name="task-5-create-a-resource-group"></a>Attività 5: Creare un gruppo di risorse
Digitare il comando PowerShell seguente per creare un gruppo di risorse:
```powershell
$ResourceGroupName = "ContosoAaddsRg"
$AzureLocation = "westus"

# Create the resource group.
New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

In questo gruppo di risorse è possibile creare la rete virtuale e il dominio gestito di Azure AD Domain Services.


## <a name="task-6-create-and-configure-the-virtual-network"></a>Attività 6: Creare e configurare la rete virtuale
A questo punto, creare la rete virtuale in cui abilitare Azure AD Domain Services. Assicurarsi di creare una subnet dedicata per Azure AD Domain Services. Non distribuire le macchine virtuali del carico di lavoro in questa subnet dedicata.

Digitare i comandi PowerShell seguenti per creare una rete virtuale con una subnet dedicata per Azure AD Domain Services.

```powershell
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```


## <a name="task-7-provision-the-azure-ad-domain-services-managed-domain"></a>Attività 7: Eseguire il provisioning del dominio gestito di Azure AD Domain Services
Digitare il comando PowerShell seguente per abilitare Azure AD Domain Services per la directory:

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso100.com"
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"
$AzureLocation = "westus"

# Enable Azure AD Domain Services for the directory.
New-AzureRmResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **Non dimenticare i passaggi di configurazione aggiuntivi dopo aver eseguito il provisioning del dominio gestito.**
> Dopo aver eseguito il provisioning del dominio gestito, è necessario completare le attività seguenti:
> * **[Aggiornare le impostazioni DNS](active-directory-ds-getting-started-dns.md)** per la rete virtuale, in modo che le macchine virtuali possano trovare il dominio gestito per l'autenticazione o l'aggiunta al dominio.
* **[Abilitare la sincronizzazione password in Azure Active Directory Domain Services](active-directory-ds-getting-started-password-sync.md)**, in modo che gli utenti finali possano accedere al dominio gestito con le credenziali aziendali.
>


## <a name="powershell-script"></a>Script di PowerShell
Per eseguire tutte le attività elencate in questo articolo, è possibile usare lo script di PowerShell seguente. Copiare lo script e salvarlo in un file con estensione '.ps1'. Eseguire lo script in PowerShell o con PowerShell Integrated Scripting Environment (ISE).

> [!NOTE]
> **Autorizzazioni necessarie per eseguire questo script** Per abilitare Azure AD Domain Services, è necessario essere l'amministratore globale per la directory di Azure AD. Sono inoltre necessari almeno i privilegi di "Collaboratore" nella rete virtuale in cui abilitare Azure AD Domain Services.
>

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso100.onmicrosoft.com"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso100.com"
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"
$AzureLocation = "westus"

# Connect to your Azure AD directory.
Connect-AzureAD

# Login to your Azure subscription.
Login-AzureRmAccount

# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId “2565bd9d-da50-47d4-8b85-4c97f669dc36”

# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"

# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq '$AaddsAdminUserUpn'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId

# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AAD

# Create the resource group.
New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $AzureLocation `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet

# Enable Azure AD Domain Services for the directory.
New-AzureRmResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **Non dimenticare i passaggi di configurazione aggiuntivi dopo aver eseguito il provisioning del dominio gestito.**
> Dopo aver eseguito il provisioning del dominio gestito, è necessario completare le attività seguenti:
> * Aggiornare le impostazioni DNS per la rete virtuale, in modo che le macchine virtuali possano trovare il dominio gestito per l'autenticazione o l'aggiunta al dominio.
* Abilitare la sincronizzazione password in Azure Active Directory Domain Services, in modo che gli utenti finali possano accedere al dominio gestito con le credenziali aziendali.
>

## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato il dominio gestito, eseguire le attività di configurazione seguenti per poter usare il dominio gestito:

* [Aggiornare le impostazioni del server DNS per la rete virtuale in modo che puntino al dominio gestito](active-directory-ds-getting-started-dns.md)
* [Abilitare la sincronizzazione password nel dominio gestito](active-directory-ds-getting-started-password-sync.md)
