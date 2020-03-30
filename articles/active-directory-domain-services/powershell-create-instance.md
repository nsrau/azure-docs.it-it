---
title: Abilitare Servizi di dominio Azure DS tramite PowerShell. Documenti Microsoft
description: Informazioni su come configurare e abilitare Servizi di dominio Azure Active Directory con Azure AD PowerShell e Azure PowerShell.Learn how to configure and enable Azure Active Directory Domain Services using Azure AD PowerShell and Azure PowerShell.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: iainfou
ms.openlocfilehash: ee85002aea962dfa675ac6c09a6bfbaeba8e9e79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613227"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Abilitare Azure Active Directory Domain Services con PowerShell

Azure Active Directory Domain Services (Azure AD DS) offre servizi di dominio gestiti, come l'aggiunta a un dominio, Criteri di gruppo, LDAP e l'autenticazione Kerberos/NTLM, completamente compatibili con Windows Server Active Directory. È possibile utilizzare questi servizi di dominio senza distribuire, gestire e applicare patch manualmente ai controller di dominio. Azure AD DS si integra con il tenant di Azure AD esistente. Questa integrazione consente agli utenti di accedere con le proprie credenziali aziendali ed è possibile usare i gruppi e gli account utente esistenti per proteggere l'accesso alle risorse.

Questo articolo illustra come abilitare Servizi di dominio Active Directory di Azure usando PowerShell.This article shows you how to enable Azure AD DS using PowerShell.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questo articolo, sono necessarie le risorse seguenti:To complete this article, you need the following resources:

* Installare e configurare Azure PowerShell.
    * Se necessario, seguire le istruzioni per [installare il modulo di Azure PowerShell e connettersi alla sottoscrizione](/powershell/azure/install-az-ps)di Azure.
    * Assicurarsi di accedere alla sottoscrizione di Azure usando il cmdlet [Connect-AzAccount.][Connect-AzAccount]
* Installare e configurare Azure AD PowerShell.Install and configure Azure AD PowerShell.
    * Se necessario, seguire le istruzioni per [installare il modulo Azure AD PowerShell e connettersi ad Azure AD.](/powershell/azure/active-directory/install-adv2)
    * Assicurarsi di accedere al tenant di Azure AD utilizzando il cmdlet [Connect-AzureAD.][Connect-AzureAD]
* Per abilitare Azure AD DS, sono necessari privilegi di *amministratore globale* nel tenant di Azure AD.
* Per creare le risorse di Azure AD DS richieste, sono necessari privilegi di *collaboratore* nella sottoscrizione di Azure.

## <a name="create-required-azure-ad-resources"></a>Creare le risorse di Azure AD necessarieCreate required Azure AD resources

Servizi di dominio Active Directory di Azure richiede un'entità servizio e un gruppo di Azure AD. Queste risorse consentono al dominio gestito di Servizi di dominio Active Directory di Azure sincronizzare i dati e definire gli utenti che dispongono di autorizzazioni amministrative nel dominio gestito.

Creare innanzitutto un'entità servizio di Azure AD per Servizi di dominio Azure AD per comunicare ed autenticarsi. Viene utilizzato un ID applicazione specifico denominato *Domain Controller Services* con ID *2565bd9d-da50-47d4-8b85-4c97f669dc36*. Non modificare questo ID applicazione.

Creare un'entità servizio di Azure AD usando il cmdlet [New-AzureADServicePrincipal:Create][New-AzureADServicePrincipal] an Azure AD service principal using the New-AzureADServicePrincipal cmdlet:

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Creare ora un gruppo di Azure AD denominato *AAD DC Administrators*. Agli utenti aggiunti a questo gruppo vengono quindi concesse le autorizzazioni per eseguire attività di amministrazione nel dominio gestito di Servizi di dominio Active Directory di Azure.Users added to this group are then granted granted permissions to perform administration tasks on the Azure AD DS managed domain.

Creare il gruppo *AAD DC Administrators* utilizzando il cmdlet [New-AzureADGroup:][New-AzureADGroup]

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Dopo aver creato il gruppo Amministratori controller di dominio *AAD,* aggiungere un utente al gruppo utilizzando il cmdlet [Add-AzureADGroupMember.][Add-AzureADGroupMember] Si ottiene *innanzitutto* l'ID oggetto del gruppo AAD DC Administrators utilizzando il cmdlet [Get-AzureADGroup,][Get-AzureADGroup] quindi l'ID oggetto dell'utente desiderato utilizzando il cmdlet [Get-AzureADUser.][Get-AzureADUser]

Nell'esempio seguente, l'ID oggetto utente per `admin@aaddscontoso.onmicrosoft.com`l'account con un UPN di . Sostituire questo account utente con l'UPN dell'utente che si desidera aggiungere al gruppo *AAD DC Administrators:*

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@aaddscontoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="create-supporting-azure-resources"></a>Creare risorse di supporto di Azure

Registrare innanzitutto il provider di risorse Servizi di dominio Azure AD usando il cmdlet Register-AzResourceProvider:First, register the Azure AD Domain Services resource provider using the [Register-AzResourceProvider][Register-AzResourceProvider] cmdlet:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Successivamente, creare un gruppo di risorse utilizzando il cmdlet New-AzResourceGroup.Next, create a resource group using the [New-AzResourceGroup][New-AzResourceGroup] cmdlet. Nell'esempio seguente il gruppo di risorse è denominato *myResourceGroup* e viene creato nell'area *westus.* Usa il tuo nome e la regione desiderata:

```powershell
$ResourceGroupName = "myResourceGroup"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Creare la rete virtuale e le subnet per Servizi di dominio Azure AD. Vengono create due subnet: una per *DomainServices*e una per *Carichi di lavoro*. Servizi di dominio Active Directory di Azure viene distribuito nella subnet *DomainServices* dedicata. Non distribuire altre applicazioni o carichi di lavoro in questa subnet. Usare i carichi di *lavoro* separati o altre subnet per il resto delle macchine virtuali.

Creare le subnet utilizzando il cmdlet [New-AzVirtualNetworkSubnetConfig][New-AzVirtualNetworkSubnetConfig] , quindi creare la rete virtuale utilizzando il cmdlet [New-AzVirtualNetwork.][New-AzVirtualNetwork]

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

## <a name="create-an-azure-ad-ds-managed-domain"></a>Creare un dominio gestito di Azure AD DSCreate an Azure AD DS managed domain

Creiamo ora un dominio gestito di Servizi di dominio Active Directory di Azure.Now let's create an Azure AD DS managed domain. Impostare l'ID sottoscrizione di Azure e quindi specificare un nome per il dominio gestito, ad esempio *aaddscontoso.com*. È possibile ottenere l'ID sottoscrizione utilizzando il cmdlet [Get-AzSubscription.][Get-AzSubscription]

Se si sceglie un'area che supporta le zone di disponibilità, le risorse di Azure AD DS vengono distribuite in più zone per garantire maggiore ridondanza.

Le zone di disponibilità sono località fisiche esclusive all'interno di un'area di Azure. Ogni zona è costituita da uno o più data center dotati di impianti indipendenti per l'alimentazione, il raffreddamento e la connettività di rete. Per garantire la resilienza, sono presenti almeno tre zone separate in tutte le aree abilitate.

Non è necessario eseguire alcuna operazione di configurazione per distribuire Azure AD DS in più zone. La piattaforma Azure gestisce automaticamente la distribuzione delle risorse nelle zone. Per altre informazioni e per visualizzare la disponibilità dell'area, vedere Che cosa sono le zone di [disponibilità in Azure?][availability-zones].

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

La creazione della risorsa e la restituzione del controllo richiedono alcuni minuti al prompt di PowerShell.It takes a few minutes to create the resource and return control to the PowerShell prompt. Il dominio gestito di Servizi di dominio Active Directory di Azure continua a essere sottoposto a provisioning in background e può richiedere fino a un'ora per completare la distribuzione. Nel portale di Azure, la pagina **Panoramica** per il dominio gestito di Servizi di dominio Active Directory di Azure mostra lo stato corrente in questa fase di distribuzione.

Quando il portale di Azure mostra che il dominio gestito di Azure AD DS ha terminato il provisioning, è necessario completare le attività seguenti:When the Azure portal shows that the Azure AD DS managed domain has finished provisioning, the following tasks need to be completed:

* Aggiornare le impostazioni DNS per la rete virtuale, in modo che le macchine virtuali possano trovare il dominio gestito per l'autenticazione o l'aggiunta al dominio.
    * Per configurare il DNS, selezionare il dominio gestito di Servizi di dominio Active Directory di Azure nel portale. Nella finestra **Panoramica** viene richiesto di configurare automaticamente queste impostazioni DNS.
* Se è stato creato un dominio gestito di Servizi di dominio Active Directory di Azure in un'area che supporta le zone di disponibilità, creare un gruppo di sicurezza di rete per limitare il traffico nella rete virtuale per il dominio gestito di Servizi di dominio Active Directory di Azure.If you created an Azure AD DS managed domain in a region that supports Availability zones, create a network security group to restrict traffic in the virtual network for the Azure AD DS managed domain. Viene creato un servizio di bilanciamento del carico standard di Azure che richiede il seposto di queste regole. Questo gruppo di sicurezza di rete protegge Servizi di dominio Active Directory ed è necessario per il corretto funzionamento del dominio gestito.
    * Per creare il gruppo di sicurezza di rete e le regole obbligatorie, selezionare il dominio gestito di Servizi di dominio Active Directory di Azure nel portale. Nella finestra **Panoramica** viene richiesto di creare e configurare automaticamente il gruppo di sicurezza di rete.
* [Abilitare](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) la sincronizzazione delle password con Servizi di dominio Azure AD in modo che gli utenti finali possano accedere al dominio gestito usando le credenziali aziendali.

## <a name="complete-powershell-script"></a>Script di PowerShell completo

Lo script di PowerShell completo seguente combina tutte le attività illustrate in questo articolo. Copiare lo script e salvarlo `.ps1` in un file con estensione. Eseguire lo script in una console di PowerShell locale o in [Azure Cloud Shell.][cloud-shell]

> [!NOTE]
> Per abilitare Servizi di dominio Active Directory di Azure, è necessario essere un amministratore globale per il tenant di Azure AD. È inoltre necessario almeno i privilegi *di collaboratore* nella sottoscrizione di Azure.You also need at least Contributor privileges in the Azure subscription.

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@aaddscontoso.onmicrosoft.com"
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

La creazione della risorsa e la restituzione del controllo richiedono alcuni minuti al prompt di PowerShell.It takes a few minutes to create the resource and return control to the PowerShell prompt. Il dominio gestito di Servizi di dominio Active Directory di Azure continua a essere sottoposto a provisioning in background e può richiedere fino a un'ora per completare la distribuzione. Nel portale di Azure, la pagina **Panoramica** per il dominio gestito di Servizi di dominio Active Directory di Azure mostra lo stato corrente in questa fase di distribuzione.

Quando il portale di Azure mostra che il dominio gestito di Azure AD DS ha terminato il provisioning, è necessario completare le attività seguenti:When the Azure portal shows that the Azure AD DS managed domain has finished provisioning, the following tasks need to be completed:

* Aggiornare le impostazioni DNS per la rete virtuale, in modo che le macchine virtuali possano trovare il dominio gestito per l'autenticazione o l'aggiunta al dominio.
    * Per configurare il DNS, selezionare il dominio gestito di Servizi di dominio Active Directory di Azure nel portale. Nella finestra **Panoramica** viene richiesto di configurare automaticamente queste impostazioni DNS.
* Se è stato creato un dominio gestito di Servizi di dominio Active Directory di Azure in un'area che supporta le zone di disponibilità, creare un gruppo di sicurezza di rete per limitare il traffico nella rete virtuale per il dominio gestito di Servizi di dominio Active Directory di Azure.If you created an Azure AD DS managed domain in a region that supports Availability zones, create a network security group to restrict traffic in the virtual network for the Azure AD DS managed domain. Viene creato un servizio di bilanciamento del carico standard di Azure che richiede il seposto di queste regole. Questo gruppo di sicurezza di rete protegge Servizi di dominio Active Directory ed è necessario per il corretto funzionamento del dominio gestito.
    * Per creare il gruppo di sicurezza di rete e le regole obbligatorie, selezionare il dominio gestito di Servizi di dominio Active Directory di Azure nel portale. Nella finestra **Panoramica** viene richiesto di creare e configurare automaticamente il gruppo di sicurezza di rete.
* [Abilitare](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) la sincronizzazione delle password con Servizi di dominio Azure AD in modo che gli utenti finali possano accedere al dominio gestito usando le credenziali aziendali.

## <a name="next-steps"></a>Passaggi successivi

Per visualizzare in azione il dominio gestito di Servizi di dominio Active Directory di Azure, è possibile aggiungere un [dominio a una macchina virtuale Windows][windows-join], [configurare LDAP protetto][tutorial-ldaps]e configurare la [sincronizzazione dell'hash delle password.][tutorial-phs]

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
[cloud-shell]: /azure/cloud-shell/cloud-shell-windows-users
[availability-zones]: ../availability-zones/az-overview.md
