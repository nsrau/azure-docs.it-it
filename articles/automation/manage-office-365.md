---
title: Gestire i servizi di Office 365 con Automazione di AzureManage Office 365 services using Azure Automation
description: Viene illustrato come usare Automazione di Azure per gestire i servizi di sottoscrizione a Office 365.
services: automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 9cb505ced907b143fbd6a5f4f30c818092005bb8
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550422"
---
# <a name="manage-office-365-services-using-azure-automation"></a>Gestire i servizi di Office 365 con Automazione di AzureManage Office 365 services using Azure Automation

È possibile usare Automazione di Azure per la gestione dei servizi di sottoscrizione a Office 365, per prodotti come Microsoft Word e Microsoft Outlook.You can use Azure Automation for management of Office 365 subscription services, for products such as Microsoft Word and Microsoft Outlook. Le interazioni con Office 365 sono abilitate da [Azure Active Directory (Azure AD).](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) Vedere [Usare Azure AD in Automazione di Azure per eseguire l'autenticazione in Azure.See Use Azure AD in Azure Automation to authenticate to Azure.](automation-use-azure-ad.md)

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](automation-update-azure-modules.md)

## <a name="prerequisites"></a>Prerequisiti

Per gestire i servizi di sottoscrizione a Office 365 in Automazione di Azure.You need the following to manage Office 365 subscription services in Azure Automation.

* Una sottoscrizione di Azure. Vedere [Guida alle decisioni sull'abbonamento](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/).
* Oggetto di automazione in Azure per contenere le credenziali dell'account utente e i runbook. Vedere [Introduzione all'automazione di Azure.See An introduction to Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro).
* Windows Azure. Vedere [Usare Azure AD in Automazione di Azure per eseguire l'autenticazione in Azure.See Use Azure AD in Azure Automation to authenticate to Azure.](automation-use-azure-ad.md)
* Un tenant di Office 365 con un account. Vedere Configurare il tenant di [Office 365.](https://docs.microsoft.com/sharepoint/dev/spfx/set-up-your-developer-tenant)

## <a name="installing-the-msonline-and-msonlineext-modules"></a>Installazione dei moduli MSOnline e MSOnlineExt

L'uso di Office 365 all'interno di Automazione di Azure richiede Microsoft Azure Active Directory per Windows PowerShell (modulo).`MSOnline` Sarà inoltre necessario il [`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35)modulo , che semplifica la gestione di Azure AD in ambienti single e multi-tenant. Installare i moduli come descritto in [Usare Azure AD in Automazione di Azure per eseguire l'autenticazione in Azure.Install the](automation-use-azure-ad.md)modules as described in Use Azure AD in Azure Automation to authenticate to Azure .

>[!NOTE]
>Per usare MSOnline PowerShell, è necessario essere membri di Azure AD. Gli utenti guest non possono utilizzare il modulo.

## <a name="creating-an-azure-automation-account"></a>Creazione di un account di Automazione di AzureCreating an Azure Automation account

Per completare i passaggi descritti in questo articolo, è necessario un account in Automazione di Azure.To complete the steps in this article, you need an account in Azure Automation. Vedere [Creare un account di Automazione di Azure.](automation-quickstart-create-account.md)
 
## <a name="adding-msonline-and-msonlineext-as-assets"></a>Aggiunta di MSOnline e MSOnlineExt come risorse

Aggiungere ora i moduli MSOnline e MSOnlineExt installati per abilitare la funzionalità di Office 365. Fare riferimento a [Gestire i moduli in Automazione di Azure](shared-resources/modules.md).Refer to Manage modules in Azure Automation .

1. Nel portale di Azure selezionare **Account di automazione**.
2. Scegli il tuo account Automation.
3. Selezionare **Raccolta moduli** in **Risorse condivise**.
4. Cercare MSOnline.
5. Selezionare `MSOnline` il modulo PowerShell e fare clic su **Importa** per importare il modulo come asset.
6. Ripetere i passaggi 4 e `MSOnlineExt` 5 per individuare e importare il modulo. 

## <a name="creating-a-credential-asset-optional"></a>Creazione di un asset credenziali (facoltativo)Creating a credential asset (optional)

È facoltativo creare un asset delle credenziali per l'utente amministratore di Office 365 che dispone delle autorizzazioni per eseguire lo script. Può essere utile, tuttavia, impedire di esporre nomi utente e password all'interno di script di PowerShell.It can help, though, to keep from exposing user names and passwords inside PowerShell scripts. Per istruzioni, consultate [Creazione di un asset credenziali.](automation-use-azure-ad.md#creating-a-credential-asset)

## <a name="creating-an-office-365-service-account"></a>Creazione di un account di servizio di Office 365

Per eseguire i servizi di abbonamento a Office 365, è necessario un account di servizio di Office 365 con le autorizzazioni per eseguire le operazioni desiderate. È possibile utilizzare un account amministratore globale, un account per servizio o disporre di una funzione o di uno script da eseguire. In ogni caso, l'account del servizio richiede una password complessa e sicura. Vedere [Configurare Office 365 per le aziende.](https://docs.microsoft.com/microsoft-365/admin/setup/setup?view=o365-worldwide) 

## <a name="connecting-to-the-azure-ad-online-service"></a>Connessione al servizio online di Azure ADConnecting to the Azure AD online service

>[!NOTE]
>Per utilizzare i cmdlet del modulo MSOnline, è necessario eseguirli da Windows PowerShell. PowerShell Core non supporta questi cmdlet.

È possibile usare il modulo MSOnline per connettersi ad Azure AD dalla sottoscrizione a Office 365.You can use the MSOnline module to connect to Azure AD from the Office 365 subscription. La connessione utilizza un nome utente e una password di Office 365 o utilizza l'autenticazione a più fattori (MFA). È possibile connettersi usando il portale di Azure o un prompt dei comandi di Windows PowerShell (non è necessario eseguire l'elevazione.

Di seguito è riportato un esempio di PowerShell.A PowerShell example is shown below. Il cmdlet [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) richiede le credenziali e `Msolcred` le archivia nella variabile. Il cmdlet [Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) utilizza quindi le credenziali per connettersi al servizio online della directory di Azure. Se si vuole connettersi a un `AzureEnvironment` ambiente Azure specifico, usare il parametro.

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

Se non ricevi errori, la connessione è stata eseguita correttamente. Un test rapido consiste nell'eseguire un cmdlet `Get-MsolUser`di Office 365, ad esempio , e visualizzare i risultati. Se si ricevono errori, si noti che un problema comune è una password errata.

>[!NOTE]
>È anche possibile usare il modulo AzureRM o il modulo Az per connettersi ad Azure AD dalla sottoscrizione a Office 365.You can also use the AzureRM module or the Az module to connect to Azure AD from the Office 365 subscription. Il cmdlet di connessione principale è [Connect-AzureAD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0). Questo cmdlet supporta `AzureEnvironmentName` il parametro per ambienti Office 365 specifici.

## <a name="creating-a-powershell-runbook-from-an-existing-script"></a>Creazione di un runbook di PowerShell da uno script esistenteCreating a PowerShell runbook from an existing script

È possibile accedere alle funzionalità di Office 365 da uno script di PowerShell.You access Office 365 functionality from a PowerShell script. Di seguito è riportato un esempio `Office-Credentials` di script `admin@TenantOne.com`per una credenziale denominata con nome utente di . Viene `Get-AutomationPSCredential` utilizzato per importare le credenziali di Office 365.

```powershell
$emailFromAddress = "admin@TenantOne.com" 
$emailToAddress = "servicedesk@TenantOne.com" 
$emailSMTPServer = "outlook.office365.com" 
$emailSubject = "Office 365 License Report" 

$credObject = Get-AutomationPSCredential -Name "Office-Credentials" 
Connect-MsolService -Credential $credObject

$O365Licenses = Get-MsolAccountSku | Out-String 
Send-MailMessage -Credential $credObject -From $emailFromAddress -To $emailToAddress -Subject $emailSubject -Body 

$O365Licenses -SmtpServer $emailSMTPServer -UseSSL
```

## <a name="running-the-script-in-a-runbook"></a>Esecuzione dello script in un runbook

È possibile usare lo script in un runbook di Automazione di Azure.You can use your script in an Azure Automation runbook. A scopo di esempio, verrà usato il tipo di runbook di PowerShell.For example purposes, we'll use the PowerShell runbook type.

1. Creare un nuovo runbook di PowerShell.Create a new PowerShell runbook. Fare riferimento a [Creare un runbook di Automazione di Azure .Refer](https://docs.microsoft.com/azure/automation/automation-quickstart-create-runbook)to Create an Azure Automation runbook .
2. Dall'account Di automazione selezionare **Runbook** in **Process Automation**.
3. Selezionare il nuovo runbook e fare clic su **Modifica**.
4. Copiare lo script e incollarlo nell'editor testuale per il runbook.
5. Selezionare **ASSETS**, quindi espandere **Credenziali** e verificare che la credenziale di Office 365 sia presente.
6. Fare clic su **Salva**.
7. Selezionare **Riquadro di test**, quindi fare clic su **Avvia** per avviare il test del runbook. Vedere [Gestire i runbook in Automazione di Azure.See Manage runbooks in Azure Automation.](https://docs.microsoft.com/azure/automation/manage-runbooks)
8. Al termine del test, uscire dal riquadro Test.When testing is complete, exit from the Test pane.

## <a name="publishing-and-scheduling-the-runbook"></a>Pubblicazione e pianificazione del runbook

Per pubblicare e pianificare il runbook, vedere [Gestire i runbook in Automazione](https://docs.microsoft.com/azure/automation/manage-runbooks)di Azure.To publish and then schedule your runbook, see Manage runbooks in Azure Automation.

## <a name="next-steps"></a>Passaggi successivi

* È possibile trovare informazioni sugli asset delle credenziali di automazione [in Asset credenziali in Automazione di Azure.You](shared-resources/credentials.md)can find information about Automation credential assets in Credential assets in Azure Automation .
* Vedere [Gestire i moduli in Automazione di Azure](shared-resources/modules.md) per scoprire come usare i moduli di automazione.
* Per una panoramica della gestione dei runbook, vedere [Gestire i runbook in Automazione di Azure.For](https://docs.microsoft.com/azure/automation/manage-runbooks)an overview of runbook management, see Manage runbooks in Azure Automation.
* Per altre informazioni sui metodi che possono essere usati per avviare un runbook in Automazione di Azure, vedere [Avvio di un runbook in Automazione di Azure.To](automation-starting-a-runbook.md)learn more about the methods that can be used to start a runbook in Azure Automation, see Starting a runbook in Azure Automation.
* Per altre informazioni su PowerShell, inclusi i moduli di riferimento e apprendimento del linguaggio, vedere la documentazione di [PowerShell](https://docs.microsoft.com/powershell/scripting/overview).