---
title: Usare Azure AD in Automazione di Azure per eseguire l'autenticazione in AzureUse Azure AD in Azure Automation to authenticate to Azure
description: Informazioni su come usare Azure AD in Automazione di Azure come provider per l'autenticazione in Azure.Learn how to use Azure AD within Azure Automation as the provider for authentication to Azure.
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 77476c67761a950430b39d5baddf2c6efd77f1a2
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80479448"
---
# <a name="use-azure-ad-in-azure-automation-to-authenticate-to-azure"></a>Usare Azure AD in Automazione di Azure per eseguire l'autenticazione in AzureUse Azure AD in Azure Automation to authenticate to Azure

Il servizio [Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) consente numerose attività amministrative, ad esempio la gestione degli utenti, la gestione del dominio e la configurazione Single Sign-On. Questo articolo descrive come usare Azure AD in Automazione di Azure come provider per l'autenticazione in Azure.This article describes how to use Azure AD within Azure Automation as the provider for authentication to Azure. 

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](automation-update-azure-modules.md)

## <a name="installing-azure-ad-modules"></a>Installazione dei moduli di Azure ADInstalling Azure AD modules

È possibile abilitare Azure AD tramite i moduli di PowerShell seguenti:You can enable Azure AD through the following PowerShell modules:

* Azure Active Directory PowerShell per Graph (moduli AzureRM e Az). Automazione di Azure viene fornito con il modulo AzureRM e il relativo aggiornamento recente, il modulo Az.Azure Automation ships with the AzureRM module and its recent upgrade, the Az module. La funzionalità include l'autenticazione non interattiva in Azure usando l'autenticazione basata sulle credenziali dell'utente di Azure AD (OrgId). Vedere [Azure AD 2.0.2.76](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76).

* Microsoft Azure Active Directory per Windows PowerShell (modulo MSOnline). Questo modulo abilita le interazioni con Microsoft Online, incluso Office 365.

>[!NOTE]
>PowerShell Core non supporta il modulo MSOnline. Per utilizzare i cmdlet del modulo, è necessario eseguirli da Windows PowerShell. Si consiglia di usare Azure Active Directory PowerShell per i moduli Graph più recente anziché il modulo MSOnline. 

### <a name="preinstallation"></a>Preinstallazione

Prima di installare i moduli di Azure AD nel computer:Before installing the Azure AD modules on your computer:

* Disinstallare tutte le versioni precedenti del modulo AzureRM/Az e del modulo MSOnline. 

* Disinstallare l'Assistente per l'accesso ai Microsoft Online Services per garantire il corretto funzionamento dei nuovi moduli di PowerShell.  

### <a name="install-the-azurerm-and-az-modules"></a>Installare i moduli di AzureRM e AzInstall the AzureRM and Az modules

>[!NOTE]
>Per usare questi moduli, è necessario usare PowerShell versione 5.1 o successiva con una versione a 64 bit di Windows.To work with these modules, you must use PowerShell version 5.1 or later with a 64-bit version of Windows. 

1. Installare Windows Management Framework (WMF) 5.1. Vedere [Installazione e configurazione di WMF 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure?view=powershell-7).

2. Installare AzureRM e/o Az usando le istruzioni in [Installare Azure PowerShell in Windows con PowerShellGet](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.13.0).

### <a name="install-the-msonline-module"></a>Installare il modulo MSOnline

>[!NOTE]
>Per installare il modulo MSOnline, è necessario essere membri di un ruolo di amministratore di Office 365. Vedere [Informazioni sui ruoli di amministratore](https://docs.microsoft.com/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide).

1. Verificare che la funzionalità Microsoft .NET Framework 3.5.x sia abilitata nel computer. È probabile che nel computer sia installata una versione più recente, ma è possibile abilitare o disabilitare la compatibilità con le versioni precedenti di .NET Framework. 

2. Installare la versione a 64 bit dell'Assistente per l'accesso ai [Microsoft Online Services](https://www.microsoft.com/download/details.aspx?id=41950).

3. Eseguire Windows PowerShell come amministratore per creare un prompt dei comandi di Windows PowerShell con privilegi elevati.

4. Distribuire Azure Active Directory da [MSOnline 1.0.](http://www.powershellgallery.com/packages/MSOnline/1.0)

5. Se viene richiesto di installare il provider NuGet, digitare Y e premere INVIO.

6. Se viene richiesto di installare il modulo da [PSGallery](https://www.powershellgallery.com/), digitare Y e premere INVIO.

### <a name="install-support-for-pscredential"></a>Installare il supporto per PSCredentialInstall support for PSCredential

Automazione di Azure usa la classe [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) per rappresentare un asset delle credenziali. Gli script `PSCredential` recuperano `Get-AutomationPSCredential` oggetti utilizzando il cmdlet. Per altre informazioni, vedere Asset delle credenziali in Automazione di Azure.For more information, see [Credential assets in Azure Automation.](shared-resources/credentials.md)

## <a name="assigning-a-subscription-administrator"></a>Assegnazione di un amministratore della sottoscrizioneAssigning a subscription administrator

È necessario assegnare un amministratore per la sottoscrizione di Azure.You must assign an administrator for the Azure subscription. Questa persona ha il ruolo di proprietario per l'ambito della sottoscrizione. Vedere Controllo degli accessi in base al [ruolo in Automazione di Azure.See Role-based access control in Azure Automation](automation-role-based-access-control.md). 

## <a name="changing-the-azure-ad-users-password"></a>Modifica della password dell'utente di Azure AD

Per modificare la password dell'utente di Azure AD:To change the Azure AD user's password:

1. Disconnettersi da Azure.Log out of Azure.

2. Fare in modo che l'amministratore eseda l'accesso ad Azure come utente di Azure AD appena creato, usando il nome utente completo (incluso il dominio) e una password temporanea. 

3. Chiedere all'amministratore di modificare la password quando richiesto.

## <a name="configuring-azure-automation-to-use-the-azure-ad-user-to-manage-the-azure-subscription"></a>Configurazione di Automazione di Azure per l'uso dell'utente di Azure AD per gestire la sottoscrizione di AzureConfiguring Azure Automation to use the Azure AD user to manage the Azure subscription

Affinché Automazione di Azure comunichi con Azure AD, è necessario recuperare le credenziali associate alla connessione di Azure ad Azure AD. Esempi di queste credenziali sono l'ID tenant, l'ID sottoscrizione e così via. Per altre informazioni sulla connessione tra Azure e Azure AD, vedere [Connettere l'organizzazione ad Azure Active Directory.For](https://docs.microsoft.com/azure/devops/organizations/accounts/connect-organization-to-azure-ad?view=azure-devops)more about the connection between Azure and Azure AD, see Connect your organization to Azure Active Directory .

## <a name="creating-a-credential-asset"></a>Creazione di una risorsa credenziali

Con le credenziali di Azure per Azure AD disponibili, è necessario creare un asset delle credenziali di Automazione di Azure per archiviare in modo sicuro le credenziali di Azure AD in modo che i runbook e gli script di configurazione dello stato del desiderio possano accedervi. È possibile eseguire questa operazione usando il portale di Azure o i cmdlet di PowerShell.You can do this using either the Azure portal or PowerShell cmdlets.

### <a name="create-the-credential-asset-in-azure-portal"></a>Creare l'asset delle credenziali nel portale di AzureCreate the credential asset in Azure portal

È possibile usare il portale di Azure per creare l'asset delle credenziali. Eseguire questa operazione dall'account di automazione utilizzando **le credenziali** in **Risorse condivise**. Vedere [Asset delle credenziali in Automazione di Azure.See Credential assets in Azure Automation.](shared-resources/credentials.md)

### <a name="create-the-credential-asset-with-windows-powershell"></a>Creare l'asset delle credenziali con Windows PowerShellCreate the credential asset with Windows PowerShell

Per preparare un nuovo asset delle credenziali in `PSCredential` Windows PowerShell, lo script crea innanzitutto un oggetto utilizzando il nome utente e la password assegnati. Lo script usa quindi questo oggetto per creare l'asset tramite una chiamata al cmdlet [New-AzureAutomationCredential.The](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) script then uses this object to create the asset through a call to the New-AzureAutomationCredential cmdlet. In alternativa, lo script può chiamare il cmdlet [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) per richiedere all'utente di digitare un nome e una password. Vedere [Asset delle credenziali in Automazione di Azure.See Credential assets in Azure Automation.](shared-resources/credentials.md) 

## <a name="managing-azure-resources-from-an-azure-automation-runbook"></a>Gestione delle risorse di Azure da un runbook di Automazione di AzureManaging Azure resources from an Azure Automation runbook

È possibile gestire le risorse di Azure dai runbook di Automazione di Azure usando l'asset delle credenziali. Di seguito è riportato un runbook di PowerShell di esempio che raccoglie l'asset delle credenziali da usare per arrestare e avviare le macchine virtuali in una sottoscrizione di Azure.Below is an example PowerShell runbook that collects the credential asset to use for stopping and starting virtual machines in an Azure subscription. Questo runbook `Get-AutomationPSCredential` usa innanzitutto per recuperare le credenziali da usare per l'autenticazione in Azure.This runbook first uses to retrieve the credential to use to authenticate to Azure. Chiama quindi il cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.6.1) per connettersi ad Azure usando le credenziali. Lo script usa il cmdlet [Select-AzureSubscription](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0) per scegliere la sottoscrizione da usare. 

```azurepowershell
Workflow Stop-Start-AzureVM 
{ 
    Param 
    (    
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureSubscriptionId, 
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureVMList="All", 
        [Parameter(Mandatory=$true)][ValidateSet("Start","Stop")] 
        [String] 
        $Action 
    ) 
     
    $credential = Get-AutomationPSCredential -Name 'AzureCredential' 
    Connect-AzAccount -Credential $credential 
    Select-AzureSubscription -SubscriptionId $AzureSubscriptionId 
 
    if($AzureVMList -ne "All") 
    { 
        $AzureVMs = $AzureVMList.Split(",") 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
    } 
    else 
    { 
        $AzureVMs = (Get-AzVM).Name 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
 
    } 
 
    foreach($AzureVM in $AzureVMsToHandle) 
    { 
        if(!(Get-AzVM | ? {$_.Name -eq $AzureVM})) 
        { 
            throw " AzureVM : [$AzureVM] - Does not exist! - Check your inputs " 
        } 
    } 
 
    if($Action -eq "Stop") 
    { 
        Write-Output "Stopping VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Stop-AzVM -Force 
        } 
    } 
    else 
    { 
        Write-Output "Starting VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Start-AzVM 
        } 
    } 
}
```  

## <a name="next-steps"></a>Passaggi successivi

* È possibile trovare informazioni sugli asset delle credenziali di automazione [in Asset credenziali in Automazione di Azure.You](shared-resources/credentials.md)can find information about Automation credential assets in Credential assets in Azure Automation .
* Vedere [Gestire i moduli in Automazione di Azure](shared-resources/modules.md) per scoprire come usare i moduli di automazione.
* Per altre informazioni sui metodi che possono essere usati per avviare un runbook in Automazione di Azure, vedere [Avvio di un runbook in Automazione di Azure.To](automation-starting-a-runbook.md)learn more about the methods that can be used to start a runbook in Azure Automation, see Starting a runbook in Azure Automation.
* Per altre informazioni su PowerShell, inclusi i moduli di riferimento e apprendimento del linguaggio, vedere la documentazione di [PowerShell](https://docs.microsoft.com/powershell/scripting/overview).