---
title: Configurare Azure AD in Automazione di Azure per l'autenticazione in Azure
description: Questo articolo descrive come usare Azure AD in Automazione di Azure come provider per l'autenticazione in Azure.
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: bbd1f977b548c2b8e7980709ea125c07e22b1fa2
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400673"
---
# <a name="use-azure-ad-to-authenticate-to-azure"></a>Usare Azure AD per l'autenticazione in Azure

Il servizio [Azure Active Directory (AD)](../active-directory/fundamentals/active-directory-whatis.md) consente di eseguire una serie di attività amministrative, ad esempio la gestione degli utenti e del dominio e la configurazione Single Sign-On. Questo articolo descrive come usare Azure AD in Automazione di Azure come provider per l'autenticazione in Azure. 

## <a name="install-azure-ad-modules"></a>Installare moduli di Azure AD

È possibile abilitare Azure AD tramite i moduli di PowerShell seguenti:

* Azure Active Directory PowerShell per Graph (moduli AzureRM e Az). Automazione di Azure viene fornito con il modulo AzureRM e l'aggiornamento recente relativo, il modulo AZ. La funzionalità include l'autenticazione non interattiva in Azure tramite l'autenticazione basata su credenziali dell'utente Azure AD (OrgId). Vedere [Azure AD 2.0.2.76](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76).

* Microsoft Azure Active Directory per Windows PowerShell (modulo MSOnline). Questo modulo consente di interagire con Microsoft online, incluso Microsoft 365.

>[!NOTE]
>PowerShell Core non supporta il modulo MSOnline. Per usare i cmdlet del modulo, è necessario eseguirli da Windows PowerShell. Si consiglia di usare la versione più recente di Azure Active Directory PowerShell per i moduli Graph anziché il modulo MSOnline. 

### <a name="preinstallation"></a>Preinstallazione

Prima di installare i moduli Azure AD nel computer, eseguire queste operazioni.

* Disinstallare le versioni precedenti del modulo AzureRM/Az e del modulo MSOnline. 

* Disinstallare Assistente per l'accesso ai Microsoft Online Services per verificare il corretto funzionamento dei nuovi moduli di PowerShell.  

### <a name="install-the-azurerm-and-az-modules"></a>Installare i moduli AzureRM e Az

>[!NOTE]
>Per usare questi moduli, è necessario usare PowerShell versione 5.1 o successiva con una versione di Windows a 64 bit. 

1. Installare Windows Management Framework (WMF) 5.1. Vedere [Installare e configurare WMF 5.1](/powershell/scripting/wmf/setup/install-configure?view=powershell-7).

2. Installare AzureRM e/o Az seguendo le istruzioni indicate in [Installare Azure PowerShell in Windows con PowerShellGet](/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.13.0).

### <a name="install-the-msonline-module"></a>Installare il modulo MSonline

>[!NOTE]
>Per installare il modulo MSOnline, è necessario essere un membro di un ruolo di amministratore. Vedere [Informazioni sui ruoli di amministratore](/microsoft-365/admin/add-users/about-admin-roles).

1. Verificare che la funzionalità Microsoft .NET Framework 3.5. x sia abilitata nel computer. È probabile che nel computer sia installata una versione più recente, ma la compatibilità con le versioni precedenti del .NET Framework può essere abilitata o disabilitata. 

2. Installare la versione a 64 bit dell'[Assistente per l'accesso ai Microsoft Online Services](https://www.microsoft.com/download/details.aspx?id=41950).

3. Eseguire Windows PowerShell come amministratore per creare un prompt dei comandi di Windows PowerShell con privilegi elevati.

4. Distribuire Azure Active Directory da [MSOnline 1.0](http://www.powershellgallery.com/packages/MSOnline/1.0).

5. Se viene richiesto di installare il provider NuGet, digitare S e premere INVIO.

6. Se viene richiesto di installare il modulo da [PSGallery](https://www.powershellgallery.com/), digitare S e premere INVIO.

### <a name="install-support-for-pscredential"></a>Installare il supporto per PSCredential

Automazione di Azure usa la classe [PSCredential](/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) per rappresentare un asset di credenziali. Gli script recuperano gli oggetti `PSCredential` tramite il cmdlet `Get-AutomationPSCredential`. Per altre informazioni, vedere [Asset di credenziali in Automazione di Azure](shared-resources/credentials.md).

## <a name="assign-a-subscription-administrator"></a>Assegnare un amministratore della sottoscrizione

È necessario assegnare un amministratore per la sottoscrizione di Azure. Tale persona ha il ruolo di proprietario per l'ambito della sottoscrizione. Vedere [Controllo degli accessi in base al ruolo in Automazione di Azure](automation-role-based-access-control.md) 

## <a name="change-the-azure-ad-users-password"></a>Modificare la password dell'utente di Azure AD

Per modificare la password dell'utente di Azure AD:

1. Disconnettersi da Azure.

2. Chiedere all'amministratore di accedere ad Azure come utente di Azure AD (appena creato), usando il nome utente completo, incluso il dominio, e una password temporanea. 

3. Chiedere all'amministratore di modificare la password, quando richiesto.

## <a name="configure-azure-automation-to-manage-the-azure-subscription"></a>Configurare Automazione di Azure per gestire la sottoscrizione di Azure

Per la comunicazione tra Automazione di Azure e Azure AD, è necessario recuperare le credenziali associate alla connessione di Azure AD. Esempi di tali credenziali sono l'ID tenant, l'ID sottoscrizione e il tipo. Per altre informazioni sulla connessione tra Azure e Azure AD, vedere [Connettere l'organizzazione ad Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad?view=azure-devops).

## <a name="create-a-credential-asset"></a>Creare un asset di credenziali

Con le credenziali di Azure per Azure AD disponibili, è il momento di creare un asset di credenziali di Automazione di Azure per archiviare in modo sicuro le credenziali di Azure AD in modo che i runbook e gli script DSC (Desidered State Configuration) possano accedervi. Questa operazione può essere eseguita nel portale di Azure o usando cmdlet di PowerShell.

### <a name="create-the-credential-asset-in-azure-portal"></a>Creare l'asset di credenziali nel portale di Azure

È possibile usare la portale di Azure per creare l'asset di credenziali. Eseguire questa operazione dall'account di automazione tramite **Credenziali** in **Risorse condivise**. Vedere [Asset di credenziali in Automazione di Azure](shared-resources/credentials.md)

### <a name="create-the-credential-asset-with-windows-powershell"></a>Creare l'asset di credenziali con Windows PowerShell

Per preparare un nuovo asset di credenziali in Windows PowerShell, lo script crea prima un oggetto `PSCredential` usando il nome utente e la password assegnati. Lo script usa quindi questo oggetto per creare l'asset tramite una chiamata al cmdlet [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure.service/new-azureautomationcredential?view=azuresmps-4.0.0). In alternativa, lo script può chiamare il cmdlet [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) per richiedere all'utente di digitare un nome e una password. Vedere [Asset di credenziali in Automazione di Azure](shared-resources/credentials.md) 

## <a name="manage-azure-resources-from-an-azure-automation-runbook"></a>Gestire le risorse di Azure da un runbook di automazione di Azure

È possibile gestire le risorse di Azure da runbook di Automazione di Azure usando l'asset di credenziali. Di seguito è riportato un esempio di runbook di PowerShell che raccoglie l'asset di credenziali da usare per arrestare e avviare le macchine virtuali in una sottoscrizione di Azure. Tale runbook usa prima di tutto `Get-AutomationPSCredential` per recuperare le credenziali da usare per l'autenticazione in Azure. Chiama quindi il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.6.1) per connettersi ad Azure usando le credenziali. Lo script usa il cmdlet [Select-AzureSubscription](/powershell/module/servicemanagement/azure.service/select-azuresubscription?view=azuresmps-4.0.0) per scegliere la sottoscrizione da usare. 

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

* Per informazioni dettagliate sull'uso delle credenziali, vedere [Gestire le credenziali in Automazione di Azure](shared-resources/credentials.md).
* Per informazioni sui moduli, vedere [Gestire i moduli in Automazione di Azure](shared-resources/modules.md).
* Se è necessario avviare un runbook, vedere [Avviare un runbook in Automazione di Azure](start-runbooks.md).
* Per informazioni dettagliate su PowerShell, vedere [Documentazione di PowerShell](/powershell/scripting/overview).
