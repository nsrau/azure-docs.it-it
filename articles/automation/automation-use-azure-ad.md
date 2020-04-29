---
title: Configurare Azure AD in Automazione di Azure per l'autenticazione in Azure
description: Informazioni su come usare Azure AD in automazione di Azure come provider per l'autenticazione in Azure.
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 90338a1ffa79e6c2347832cb2e74633db02ec72d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80548333"
---
# <a name="use-azure-ad-in-azure-automation-to-authenticate-to-azure"></a>Configurare Azure AD in Automazione di Azure per l'autenticazione in Azure

Il servizio [Azure Active Directory (ad)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) consente una serie di attività amministrative, ad esempio la gestione degli utenti, la gestione del dominio e la configurazione del Single Sign-on. Questo articolo descrive come usare Azure AD in automazione di Azure come provider per l'autenticazione in Azure. 

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo Az sul ruolo di lavoro ibrido per runbook, vedere [Installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per aggiornare i moduli dell'account di Automazione alla versione più recente, vedere [Come aggiornare i moduli Azure PowerShell in Automazione di Azure](automation-update-azure-modules.md).

## <a name="installing-azure-ad-modules"></a>Installazione di moduli Azure AD

È possibile abilitare Azure AD tramite i moduli di PowerShell seguenti:

* Azure Active Directory PowerShell per Graph (AzureRM e AZ Modules). Automazione di Azure viene fornito con il modulo AzureRM e il relativo aggiornamento recente, il modulo AZ. La funzionalità include l'autenticazione non interattiva in Azure tramite l'autenticazione basata su credenziali di Azure AD User (OrgId). Vedere [Azure ad 2.0.2.76](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76).

* Microsoft Azure Active Directory per Windows PowerShell (modulo MSOnline). Questo modulo consente le interazioni con Microsoft online, incluso Office 365.

>[!NOTE]
>PowerShell Core non supporta il modulo MSOnline. Per usare i cmdlet del modulo, è necessario eseguirli da Windows PowerShell. Si consiglia di usare la versione più recente di Azure Active Directory PowerShell per i moduli Graph anziché il modulo MSOnline. 

### <a name="preinstallation"></a>Preinstallazione

Prima di installare i moduli Azure AD nel computer:

* Disinstallare le versioni precedenti del modulo AzureRM/AZ e del modulo MSOnline. 

* Disinstallare l'assistente per l'accesso ai Microsoft Online Services per verificare il corretto funzionamento dei nuovi moduli di PowerShell.  

### <a name="install-the-azurerm-and-az-modules"></a>Installare i moduli AzureRM e AZ

>[!NOTE]
>Per lavorare con questi moduli, è necessario usare PowerShell versione 5,1 o successiva con una versione di Windows a 64 bit. 

1. Installare Windows Management Framework (WMF) 5,1. Vedere [installare e configurare WMF 5,1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure?view=powershell-7).

2. Installare AzureRM e/o AZ usando le istruzioni riportate in [Install Azure PowerShell on Windows with PowerShellGet](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.13.0).

### <a name="install-the-msonline-module"></a>Installare il modulo MSOnline

>[!NOTE]
>Per installare il modulo MSOnline, è necessario essere un membro di un ruolo di amministratore di Office 365. Vedere [informazioni sui ruoli di amministratore](https://docs.microsoft.com/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide).

1. Verificare che la funzionalità Microsoft .NET Framework 3.5. x sia abilitata nel computer. È probabile che nel computer sia installata una versione più recente, ma la compatibilità con le versioni precedenti del .NET Framework può essere abilitata o disabilitata. 

2. Installare la versione a 64 bit dell' [Assistente per l'accesso ai Microsoft Online Services](https://www.microsoft.com/download/details.aspx?id=41950).

3. Eseguire Windows PowerShell come amministratore per creare un prompt dei comandi di Windows PowerShell con privilegi elevati.

4. Distribuire Azure Active Directory da [MSOnline 1,0](http://www.powershellgallery.com/packages/MSOnline/1.0).

5. Se viene richiesto di installare il provider NuGet, digitare Y e premere INVIO.

6. Se viene richiesto di installare il modulo da [PSGallery](https://www.powershellgallery.com/), digitare Y e premere INVIO.

### <a name="install-support-for-pscredential"></a>Installare il supporto per PSCredential

Automazione di Azure usa la classe [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) per rappresentare un asset delle credenziali. Gli script recuperano `PSCredential` gli oggetti `Get-AutomationPSCredential` usando il cmdlet. Per altre informazioni, vedere [asset delle credenziali in automazione di Azure](shared-resources/credentials.md).

## <a name="assigning-a-subscription-administrator"></a>Assegnazione di un amministratore della sottoscrizione

È necessario assegnare un amministratore per la sottoscrizione di Azure. Questa persona ha il ruolo di proprietario per l'ambito della sottoscrizione. Vedere [controllo degli accessi in base al ruolo in automazione di Azure](automation-role-based-access-control.md). 

## <a name="changing-the-azure-ad-users-password"></a>Modifica della password dell'utente Azure AD

Per modificare la password dell'utente Azure AD:

1. Disconnettersi da Azure.

2. Chiedere all'amministratore di accedere ad Azure come Azure AD utente appena creato, usando il nome utente completo (incluso il dominio) e una password temporanea. 

3. Chiedere all'amministratore di modificare la password quando richiesto.

## <a name="configuring-azure-automation-to-use-the-azure-ad-user-to-manage-the-azure-subscription"></a>Configurazione di automazione di Azure per l'uso dell'utente Azure AD per gestire la sottoscrizione di Azure

Per la comunicazione tra automazione di Azure e Azure AD, è necessario recuperare le credenziali associate alla connessione di Azure Azure AD. Esempi di queste credenziali sono l'ID tenant, l'ID sottoscrizione e il tipo. Per ulteriori informazioni sulla connessione tra Azure e Azure AD, vedere [connettere l'organizzazione a Azure Active Directory](https://docs.microsoft.com/azure/devops/organizations/accounts/connect-organization-to-azure-ad?view=azure-devops).

## <a name="creating-a-credential-asset"></a>Creazione di un asset credenziali

Con le credenziali di Azure per Azure AD disponibili, è il momento di creare un asset delle credenziali di automazione di Azure per archiviare in modo sicuro le credenziali di Azure AD in modo che gli script manuali operativi e desired state Configuration (DSC) possano accedervi. Questa operazione può essere eseguita usando i cmdlet di portale di Azure o PowerShell.

### <a name="create-the-credential-asset-in-azure-portal"></a>Creare l'asset delle credenziali in portale di Azure

È possibile usare la portale di Azure per creare l'asset delle credenziali. Eseguire questa operazione dall'account di automazione usando le **credenziali** in **risorse condivise**. Vedere [asset delle credenziali in automazione di Azure](shared-resources/credentials.md).

### <a name="create-the-credential-asset-with-windows-powershell"></a>Creare l'asset delle credenziali con Windows PowerShell

Per preparare un nuovo asset credenziali in Windows PowerShell, lo script crea prima un `PSCredential` oggetto usando il nome utente e la password assegnati. Lo script usa quindi questo oggetto per creare l'asset tramite una chiamata al cmdlet [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) . In alternativa, lo script può chiamare il cmdlet [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) per richiedere all'utente di digitare un nome e una password. Vedere [asset delle credenziali in automazione di Azure](shared-resources/credentials.md). 

## <a name="managing-azure-resources-from-an-azure-automation-runbook"></a>Gestione delle risorse di Azure da un Runbook di automazione di Azure

È possibile gestire le risorse di Azure da manuali operativi di automazione di Azure usando l'asset delle credenziali. Di seguito è riportato un esempio di Runbook di PowerShell che raccoglie l'asset delle credenziali da usare per arrestare e avviare le macchine virtuali in una sottoscrizione di Azure. Questo runbook USA `Get-AutomationPSCredential` innanzitutto per recuperare le credenziali da usare per l'autenticazione in Azure. Chiama quindi il cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.6.1) per connettersi ad Azure usando le credenziali. Lo script usa il cmdlet [Select-AzureSubscription](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0) per scegliere la sottoscrizione da usare. 

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

* È possibile trovare informazioni sugli asset delle credenziali di automazione negli [asset delle credenziali in automazione di Azure](shared-resources/credentials.md).
* Vedere [gestire i moduli in automazione di Azure](shared-resources/modules.md) per informazioni su come usare i moduli di automazione.
* Per altre informazioni sui metodi che possono essere usati per avviare un Runbook in automazione di Azure, vedere [avvio di un Runbook in automazione di Azure](automation-starting-a-runbook.md).
* Per altre informazioni su PowerShell, inclusi i riferimenti al linguaggio e i moduli di formazione, vedere la [documentazione di PowerShell](https://docs.microsoft.com/powershell/scripting/overview).