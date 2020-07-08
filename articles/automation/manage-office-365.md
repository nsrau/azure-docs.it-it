---
title: Gestire i servizi di Office 365 con Automazione di Azure
description: Questo articolo descrive come usare Automazione di Azure per gestire i servizi di abbonamento di Office 365.
services: automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 322e2a3679ed29ab9ecc4cdc3c6e1fe4d0f20276
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83831169"
---
# <a name="manage-office-365-services"></a>Gestire i servizi di Office 365

È possibile usare Automazione di Azure per la gestione dei servizi di abbonamento di Office 365, per prodotti come Microsoft Word e Microsoft Outlook. Le interazioni con Office 365 sono abilitate da [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis). Vedere [Configurare Azure AD in Automazione di Azure per l'autenticazione in Azure](automation-use-azure-ad.md).

## <a name="prerequisites"></a>Prerequisiti

Per gestire i servizi di abbonamento di Office 365 in Automazione di Azure, sono necessari i seguenti elementi.

* Una sottoscrizione di Azure. Vedere [Guida alle decisioni relative alle sottoscrizioni](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/).
* Un oggetto di Automazione in Azure per contenere le credenziali dell'account utente e i runbook. Vedere [Introduzione ad Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-intro).
* Azure AD. Vedere [Configurare Azure AD in Automazione di Azure per l'autenticazione in Azure](automation-use-azure-ad.md).
* Un tenant di Office 365, con un account. Vedere [Configurare il tenant di Office 365](https://docs.microsoft.com/sharepoint/dev/spfx/set-up-your-developer-tenant).

## <a name="install-the-msonline-and-msonlineext-modules"></a>Installare i moduli MSOnline e MSOnlineExt

L'uso di Office 365 con Automazione di Azure richiede Microsoft Azure Active Directory per Windows PowerShell (modulo `MSOnline`). È necessario anche il modulo [`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35), che semplifica la gestione di Azure AD in ambienti a tenant singolo e multi-tenant. Installare i moduli come descritto in [Configurare Azure AD in Automazione di Azure per l'autenticazione in Azure](automation-use-azure-ad.md).

>[!NOTE]
>Per usare MSOnline PowerShell, è necessario essere membri di Azure AD. Gli utenti guest non possono usare il modulo.

## <a name="create-an-azure-automation-account"></a>Creare un account di Automazione di Azure

Per seguire la procedura descritta in questo articolo, è necessario un account in Automazione di Azure. Vedere [Creare un account di Automazione di Azure](automation-quickstart-create-account.md).
 
## <a name="add-msonline-and-msonlineext-as-assets"></a>Aggiungere MSOnline e MSOnlineExt come asset

Aggiungere ora i moduli MSOnline e MSOnlineExt installati per abilitare la funzionalità di Office 365. Consultare [Gestire i moduli in Automazione di Azure](shared-resources/modules.md).

1. Nel portale di Azure selezionare **Account di Automazione**.
2. Scegliere l'account di Automazione.
3. Selezionare **Raccolta moduli** in **Risorse condivise**.
4. Cercare MSOnline.
5. Selezionare il modulo PowerShell `MSOnline` e fare clic su **Importa** per importare il modulo come asset.
6. Ripetere i passaggi 4 e 5 per individuare e importare il modulo `MSOnlineExt`. 

## <a name="create-a-credential-asset-optional"></a>Creare un asset di credenziali (facoltativo)

Facoltativamente, è possibile creare un asset di credenziali per l'utente amministratore di Office 365 che dispone delle autorizzazioni per l'esecuzione dello script. Questo può essere utile per evitare di esporre nomi e password degli utenti all'interno degli script di PowerShell. Per le istruzioni, vedere [Creare un asset di credenziali](automation-use-azure-ad.md#create-a-credential-asset).

## <a name="create-an-office-365-service-account"></a>Creare un account del servizio Office 365

Per eseguire i servizi di abbonamento di Office 365, è necessario disporre di un account del servizio di Office 365 con le autorizzazioni per eseguire le operazioni desiderate. È possibile utilizzare un account amministratore globale, un account per servizio oppure eseguire una funzione o uno script. In ogni caso, l'account del servizio richiede una password complessa e sicura. Vedere [Configurare Microsoft 365 per le aziende](https://docs.microsoft.com/microsoft-365/admin/setup/setup?view=o365-worldwide). 

## <a name="connect-to-the-azure-ad-online-service"></a>Connettersi al servizio online Azure AD

>[!NOTE]
>Per usare i cmdlet del modulo MSOnline, è necessario eseguirli da Windows PowerShell. PowerShell Core non supporta questi cmdlet.

È possibile usare il modulo MSOnline per connettersi ad Azure AD dall’abbonamento di Office 365. La connessione usa un nome utente e una password di Office 365 o l’autenticazione a più fattori (MFA). È possibile connettersi usando il portale di Azure o un prompt dei comandi di Windows PowerShell (non è necessario che sia con privilegi elevati).

Di seguito è riportato un esempio con PowerShell. Il cmdlet [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) richiede le credenziali e le archivia nella variabile `Msolcred`. Il cmdlet [Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) usa quindi le credenziali per connettersi al servizio directory online di Azure. Se si desidera connettersi a un ambiente Azure specifico, utilizzare il parametro `AzureEnvironment`.

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

Se non viene visualizzato alcun errore, la connessione è stata completata. Per effettuare un rapido test, eseguire un cmdlet di Office 365, ad esempio `Get-MsolUser`, e visualizzare i risultati. Se si ricevono errori, un problema comune potrebbe essere una password non corretta.

>[!NOTE]
>È inoltre possibile usare il modulo AzureRM o il modulo Az per connettersi ad Azure AD dall’abbonamento di Office 365. Il cmdlet di connessione principale è [Connect-AzureAD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0). Questo cmdlet supporta il parametro `AzureEnvironmentName` per ambienti Office 365 specifici.

## <a name="create-a-powershell-runbook-from-an-existing-script"></a>Creare un runbook di PowerShell da uno script esistente

È possibile accedere alla funzionalità di Office 365 da uno script di PowerShell. Di seguito è riportato un esempio di uno script per una credenziale denominata `Office-Credentials` con il nome utente `admin@TenantOne.com`. Questo script utilizza `Get-AutomationPSCredential` per importare le credenziali di Office 365.

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

## <a name="run-the-script-in-a-runbook"></a>Eseguire lo script in un runbook

È possibile usare lo script in un runbook di Automazione di Azure. Per l’esempio, si userà il tipo runbook di PowerShell.

1. Creare un nuovo runbook di PowerShell. Consultare [Creare un runbook di Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-quickstart-create-runbook).
2. Dall'account di Automazione selezionare **Runbook** in **Automazione processi**.
3. Selezionare il nuovo runbook e fare clic su **Modifica**.
4. Copiare lo script e incollarlo nell'editor di testo per il runbook.
5. Selezionare **ASSET**, quindi espandere **Credenziali** e verificare che le credenziali di Office 365 siano presenti.
6. Fare clic su **Salva**.
7. Selezionare **Riquadro di test**, quindi fare clic su **Avvio** per iniziare il test del runbook. Vedere [Gestire runbook in Automazione di Azure](https://docs.microsoft.com/azure/automation/manage-runbooks).
8. Una volta completato il test, uscire dal riquadro di test.

## <a name="publish-and-schedule-the-runbook"></a>Pubblicare e pianificare il runbook

Per pubblicare e pianificare il runbook, vedere [Gestire runbook in Automazione di Azure](https://docs.microsoft.com/azure/automation/manage-runbooks).

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni dettagliate sull'uso delle credenziali, vedere [Gestire le credenziali in Automazione di Azure](shared-resources/credentials.md).
* Per informazioni sui moduli, vedere [Gestire i moduli in Automazione di Azure](shared-resources/modules.md).
* Se è necessario avviare un runbook, vedere [Avviare un runbook in Automazione di Azure](start-runbooks.md).
* Per informazioni dettagliate su PowerShell, vedere [Documentazione di PowerShell](https://docs.microsoft.com/powershell/scripting/overview).