---
title: Abilitare Azure Active Directory Domain Services con PowerShell | Microsoft Docs
description: Informazioni su come configurare e abilitare Azure Active Directory Domain Services usando Azure AD PowerShell e Azure PowerShell.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: sample
ms.date: 07/09/2020
ms.author: iainfou
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 27fec8b8b76bec4c5ac428258b1495fc1bef1abe
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89068967"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Abilitare Azure Active Directory Domain Services con PowerShell

Azure Active Directory Domain Services (Azure AD DS) offre servizi di dominio gestiti, come l'aggiunta a un dominio, Criteri di gruppo, LDAP e l'autenticazione Kerberos/NTLM, completamente compatibili con Windows Server Active Directory. È possibile utilizzare questi servizi di dominio senza distribuire, gestire e applicare patch manualmente ai controller di dominio. Azure AD DS si integra con il tenant di Azure AD esistente. Questa integrazione consente agli utenti di accedere con le proprie credenziali aziendali ed è possibile usare i gruppi e gli account utente esistenti per proteggere l'accesso alle risorse.

Questo articolo illustra come abilitare Azure AD Domain Services usando PowerShell.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione di questo articolo, sono necessarie le risorse seguenti:

* Installare e configurare Azure PowerShell.
    * Se necessario, seguire le istruzioni nell'articolo per [installare il modulo Azure PowerShell e connettersi alla sottoscrizione di Azure](/powershell/azure/install-az-ps).
    * Assicurarsi di accedere alla sottoscrizione di Azure usando il cmdlet [Connect-AzAccount][Connect-AzAccount].
* Installare e configurare Azure AD PowerShell.
    * Se necessario, seguire le istruzioni nell'articolo per [installare il modulo Azure AD PowerShell e connettersi ad Azure AD](/powershell/azure/active-directory/install-adv2).
    * Assicurarsi di accedere al tenant di Azure usando il cmdlet [Connect-AzureAD][Connect-AzureAD].
* Per abilitare Azure AD DS, sono necessari privilegi di *amministratore globale* nel tenant di Azure AD.
* Per creare le risorse di Azure AD DS richieste, sono necessari privilegi di *collaboratore* nella sottoscrizione di Azure.

## <a name="create-required-azure-ad-resources"></a>Creare le risorse di Azure AD necessarie

Azure AD Domain Services richiede un'entità servizio e un gruppo di Azure AD. Queste risorse consentono al dominio gestito di Azure AD Domain Services di sincronizzare i dati e di definire quali utenti dispongono di autorizzazioni amministrative nel dominio gestito.

Per prima cosa, creare un'entità servizio di Azure AD per Azure AD Domain Services per comunicare ed eseguire l'autenticazione. Viene usato un ID applicazione specifico denominato *Domain Controller Services* con ID *2565bd9d-da50-47d4-8b85-4c97f669dc36*. Non modificare questo ID applicazione.

Creare un'entità servizio di Azure AD usando il cmdlet [New-AzureADServicePrincipal][New-AzureADServicePrincipal]:

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Ora creare un gruppo di Azure AD denominato *AAD DC Administrators*. Agli utenti aggiunti a questo gruppo vengono concesse le autorizzazioni per eseguire attività di amministrazione nel dominio gestito.

Creare il gruppo *AAD DC Administrators* usando il cmdlet [New-AzureADGroup][New-AzureADGroup]:

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Dopo aver creato il gruppo *AAD DC Administrators*, aggiungere un utente al gruppo usando il cmdlet [Add-AzureADGroupMember][Add-AzureADGroupMember]. È prima necessario ottenere l'ID oggetto del gruppo *AAD DC Administrators* usando il cmdlet [Get-AzureADGroup][Get-AzureADGroup], quindi l'ID oggetto dell'utente desiderato usando il cmdlet [Get-AzureADUser][Get-AzureADUser].

Nell'esempio seguente, l'ID oggetto utente per l'account con nome dell'entità utente `admin@contoso.onmicrosoft.com`. Sostituire questo account utente con il nome dell'entità utente dell'utente che si vuole aggiungere al gruppo *AAD DC Administrators*:

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="create-supporting-azure-resources"></a>Creare risorse di supporto di Azure

Per iniziare, registrare il provider di risorse Azure Active Directory Domain Services usando il cmdlet [Register-AzResourceProvider][Register-AzResourceProvider]:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Creare quindi un gruppo di risorse con il cmdlet [New-AzResourceGroup][New-AzResourceGroup]. Nell'esempio seguente viene creato un gruppo di risorse denominato *myResourceGroup* nell'area *westus*. Usare il proprio nome e l'area desiderata:

```powershell
$ResourceGroupName = "myResourceGroup"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Creare una rete virtuale e le subnet per Azure AD Domain Services. Vengono create due subnet, una per *DomainServices* e una per *Workloads*. Azure AD Domain Services viene distribuito nella subnet *DomainServices* dedicata. Non distribuire altre applicazioni o carichi di lavoro in questa subnet. Per le altre macchine virtuali, usare la subnet separata *Workloads* o altre subnet.

Creare le subnet usando il cmdlet [New-AzVirtualNetworkSubnetConfig][New-AzVirtualNetworkSubnetConfig], quindi creare la rete virtuale usando il cmdlet [New-AzVirtualNetwork][New-AzVirtualNetwork].

```powershell
$VnetName = "myVnet"

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet= New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```

## <a name="create-a-managed-domain"></a>Creare un dominio gestito

Ora creare un dominio gestito. Impostare l'ID sottoscrizione di Azure e quindi specificare un nome per il dominio gestito, ad esempio *aaddscontoso.com*. Per ottenere l'ID sottoscrizione, usare il cmdlet [Get-AzSubscription][Get-AzSubscription].

Se si sceglie un'area che supporta le zone di disponibilità, le risorse di Azure AD DS vengono distribuite in più zone per garantire maggiore ridondanza.

Le zone di disponibilità sono località fisiche esclusive all'interno di un'area di Azure. Ogni zona è costituita da uno o più data center dotati di impianti indipendenti per l'alimentazione, il raffreddamento e la connettività di rete. Per garantire la resilienza sono presenti almeno tre zone separate in tutte le aree abilitate.

Non è necessario eseguire alcuna operazione di configurazione per distribuire Azure AD DS in più zone. La piattaforma Azure gestisce automaticamente la distribuzione delle risorse nelle zone. Per altre informazioni e per consultare la disponibilità delle aree, vedere [Informazioni sulle zone di disponibilità di Azure][availability-zones].

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -Force -Verbose
```

Sono necessari alcuni minuti per creare la risorsa e restituire il controllo al prompt di PowerShell. Il provisioning del dominio gestito continua a essere eseguito in background e il completamento della distribuzione può richiedere fino a un'ora. Nel portale di Azure la pagina **Panoramica** per il dominio gestito mostra lo stato corrente di questa fase di distribuzione.

Quando il portale di Azure indica che il provisioning del dominio gestito è stato completato, è necessario eseguire le attività seguenti:

* Aggiornare le impostazioni DNS per la rete virtuale, in modo che le macchine virtuali possano trovare il dominio gestito per l'autenticazione o l'aggiunta al dominio.
    * Per configurare il DNS, selezionare il dominio gestito nel portale. Nella finestra **Panoramica** viene richiesto di configurare automaticamente queste impostazioni DNS.
* Creare un gruppo di sicurezza di rete per limitare il traffico nella rete virtuale per il dominio gestito. Viene creato un servizio di bilanciamento del carico Standard di Azure che richiede la definizione di queste regole. Questo gruppo di sicurezza di rete protegge Azure AD Domain Services ed è necessario affinché il dominio gestito funzioni correttamente.
    * Per creare il gruppo di sicurezza di rete e le regole necessarie, installare prima di tutto lo script `New-AzureAddsNetworkSecurityGroup` con il comando `Install-Script -Name New-AaddsNetworkSecurityGroup` e quindi eseguire `New-AaddsNetworkSecurityGroup`. Le regole necessarie per il dominio gestito vengono create automaticamente.
* [Abilitare la sincronizzazione password in Azure Active Directory Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) in modo che gli utenti finali possano accedere al dominio gestito con le credenziali aziendali.

## <a name="complete-powershell-script"></a>Script di PowerShell completo

Lo script di PowerShell completo seguente combina tutte le attività illustrate in questo articolo. Copiare lo script e salvarlo in un file con estensione `.ps1`. Eseguire lo script in una console di PowerShell locale o in [Azure Cloud Shell][cloud-shell].

> [!NOTE]
> Per abilitare Azure AD Domain Services, è necessario essere un amministratore globale per il tenant di Azure AD e disporre almeno dei privilegi di *Collaboratore* nella sottoscrizione di Azure.

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso.onmicrosoft.com"
$ResourceGroupName = "myResourceGroup"
$VnetName = "myVnet"
$AzureLocation = "westus"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

# Connect to your Azure AD directory.
Connect-AzureAD

# Login to your Azure subscription.
Connect-AzAccount

# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"

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
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $AzureLocation `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -Force -Verbose
```

Sono necessari alcuni minuti per creare la risorsa e restituire il controllo al prompt di PowerShell. Il provisioning del dominio gestito continua a essere eseguito in background e il completamento della distribuzione può richiedere fino a un'ora. Nel portale di Azure la pagina **Panoramica** per il dominio gestito mostra lo stato corrente di questa fase di distribuzione.

Quando il portale di Azure indica che il provisioning del dominio gestito è stato completato, è necessario eseguire le attività seguenti:

* Aggiornare le impostazioni DNS per la rete virtuale, in modo che le macchine virtuali possano trovare il dominio gestito per l'autenticazione o l'aggiunta al dominio.
    * Per configurare il DNS, selezionare il dominio gestito nel portale. Nella finestra **Panoramica** viene richiesto di configurare automaticamente queste impostazioni DNS.
* Creare un gruppo di sicurezza di rete per limitare il traffico nella rete virtuale per il dominio gestito. Viene creato un servizio di bilanciamento del carico Standard di Azure che richiede la definizione di queste regole. Questo gruppo di sicurezza di rete protegge Azure AD Domain Services ed è necessario affinché il dominio gestito funzioni correttamente.
    * Per creare il gruppo di sicurezza di rete e le regole necessarie, installare prima di tutto lo script `New-AzureAddsNetworkSecurityGroup` con il comando `Install-Script -Name New-AaddsNetworkSecurityGroup` e quindi eseguire `New-AaddsNetworkSecurityGroup`. Le regole necessarie per il dominio gestito vengono create automaticamente.
* [Abilitare la sincronizzazione password in Azure Active Directory Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) in modo che gli utenti finali possano accedere al dominio gestito con le credenziali aziendali.

## <a name="next-steps"></a>Passaggi successivi

Per vedere il dominio gestito in azione, è possibile [aggiungere a un dominio una macchina virtuale Windows][windows-join], [configurare LDAP sicuro][tutorial-ldaps] e [configurare la sincronizzazione degli hash delle password][tutorial-phs].

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md

<!-- EXTERNAL LINKS -->
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[New-AzureADGroup]: /powershell/module/AzureAD/New-AzureADGroup
[Add-AzureADGroupMember]: /powershell/module/AzureAD/Add-AzureADGroupMember
[Get-AzureADGroup]: /powershell/module/AzureAD/Get-AzureADGroup
[Get-AzureADUser]: /powershell/module/AzureAD/Get-AzureADUser
[Register-AzResourceProvider]: /powershell/module/Az.Resources/Register-AzResourceProvider
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[New-AzVirtualNetworkSubnetConfig]: /powershell/module/Az.Network/New-AzVirtualNetworkSubnetConfig
[New-AzVirtualNetwork]: /powershell/module/Az.Network/New-AzVirtualNetwork
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: ../cloud-shell/cloud-shell-windows-users.md
[availability-zones]: ../availability-zones/az-overview.md
