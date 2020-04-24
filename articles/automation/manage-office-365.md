---
title: Gestire i servizi di Office 365 con Automazione di Azure
description: Spiega come usare automazione di Azure per gestire i servizi di sottoscrizione di Office 365.
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
# <a name="manage-office-365-services-using-azure-automation"></a>Gestire i servizi di Office 365 con Automazione di Azure

È possibile usare automazione di Azure per la gestione dei servizi di sottoscrizione di Office 365, per prodotti come Microsoft Word e Microsoft Outlook. Le interazioni con Office 365 sono abilitate da [Azure Active Directory (Azure ad)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis). Vedere [usare Azure ad in automazione di Azure per l'autenticazione in Azure](automation-use-azure-ad.md).

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo AZ sul ruolo di lavoro ibrido per Runbook, vedere [installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando [come aggiornare i moduli Azure PowerShell in automazione di Azure](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Prerequisiti

Per gestire i servizi di sottoscrizione di Office 365 in automazione di Azure, sono necessari gli elementi seguenti.

* Una sottoscrizione di Azure. Vedere la [Guida alla decisione relativa alla sottoscrizione](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/).
* Un oggetto di automazione in Azure per mantenere le credenziali dell'account utente e manuali operativi. Vedere [un'introduzione ad automazione di Azure](https://docs.microsoft.com/azure/automation/automation-intro).
* Windows Azure. Vedere [usare Azure ad in automazione di Azure per l'autenticazione in Azure](automation-use-azure-ad.md).
* Un tenant di Office 365, con un account. Vedere [configurare il tenant di Office 365](https://docs.microsoft.com/sharepoint/dev/spfx/set-up-your-developer-tenant).

## <a name="installing-the-msonline-and-msonlineext-modules"></a>Installazione dei moduli MSOnline e MSOnlineExt

L'uso di Office 365 in automazione di Azure richiede Microsoft Azure Active Directory per Windows`MSOnline` PowerShell (modulo). È necessario anche il modulo [`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35), che semplifica la gestione di Azure ad in ambienti a tenant singolo e multi-tenant. Installare i moduli come descritto in [usare Azure ad in automazione di Azure per l'autenticazione in Azure](automation-use-azure-ad.md).

>[!NOTE]
>Per usare MSOnline PowerShell, è necessario essere membri di Azure AD. Gli utenti guest non possono usare il modulo.

## <a name="creating-an-azure-automation-account"></a>Creazione di un account di automazione di Azure

Per completare i passaggi descritti in questo articolo, è necessario un account in automazione di Azure. Vedere [creare un account di automazione di Azure](automation-quickstart-create-account.md).
 
## <a name="adding-msonline-and-msonlineext-as-assets"></a>Aggiunta di MSOnline e MSOnlineExt come asset

Aggiungere ora i moduli MSOnline e MSOnlineExt installati per abilitare la funzionalità di Office 365. Vedere [gestire i moduli in automazione di Azure](shared-resources/modules.md).

1. Nella portale di Azure selezionare account di **automazione**.
2. Scegliere l'account di automazione.
3. Selezionare **raccolta moduli** in **risorse condivise**.
4. Cercare MSOnline.
5. Selezionare il `MSOnline` modulo PowerShell e fare clic su **Import (importa** ) per importare il modulo come asset.
6. Ripetere i passaggi 4 e 5 per individuare e importare `MSOnlineExt` il modulo. 

## <a name="creating-a-credential-asset-optional"></a>Creazione di un asset delle credenziali (facoltativo)

È facoltativo creare un asset credenziali per l'utente amministratore di Office 365 che dispone delle autorizzazioni per l'esecuzione dello script. Questo può essere utile, tuttavia, per evitare di esporre nomi utente e password all'interno degli script di PowerShell. Per istruzioni, vedere [creazione di un asset credenziali](automation-use-azure-ad.md#creating-a-credential-asset).

## <a name="creating-an-office-365-service-account"></a>Creazione di un account del servizio Office 365

Per eseguire i servizi di sottoscrizione di Office 365, è necessario disporre di un account di servizio di Office 365 con le autorizzazioni per eseguire le operazioni desiderate. È possibile utilizzare un account amministratore globale, un account per servizio o una funzione o uno script da eseguire. In ogni caso, l'account del servizio richiede una password complessa e sicura. Vedere [configurare Office 365 per le aziende](https://docs.microsoft.com/microsoft-365/admin/setup/setup?view=o365-worldwide). 

## <a name="connecting-to-the-azure-ad-online-service"></a>Connessione al servizio Azure AD online

>[!NOTE]
>Per usare i cmdlet del modulo MSOnline, è necessario eseguirli da Windows PowerShell. PowerShell Core non supporta questi cmdlet.

È possibile usare il modulo MSOnline per connettersi a Azure AD dalla sottoscrizione di Office 365. La connessione usa un nome utente e una password di Office 365 o usa multi-factor authentication. È possibile connettersi usando il portale di Azure o un prompt dei comandi di Windows PowerShell (non è necessario che sia elevato).

Di seguito è riportato un esempio di PowerShell. Il cmdlet [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) richiede le credenziali e le archivia nella `Msolcred` variabile. Quindi, il cmdlet [Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) usa le credenziali per connettersi al servizio Azure directory online. Se si desidera connettersi a un ambiente specifico di Azure, utilizzare il `AzureEnvironment` parametro.

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

Se non viene visualizzato alcun errore, la connessione è stata completata. Un rapido test consiste nell'eseguire un cmdlet di Office 365, ad esempio `Get-MsolUser`, e vedere i risultati. Se si ricevono errori, si noti che un problema comune è una password non corretta.

>[!NOTE]
>È anche possibile usare il modulo AzureRM o il modulo AZ per connettersi a Azure AD dalla sottoscrizione di Office 365. Il cmdlet Main connection è [Connect-AzureAD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0). Questo cmdlet supporta il `AzureEnvironmentName` parametro per ambienti di Office 365 specifici.

## <a name="creating-a-powershell-runbook-from-an-existing-script"></a>Creazione di un Runbook di PowerShell da uno script esistente

È possibile accedere alla funzionalità di Office 365 da uno script di PowerShell. Di seguito è riportato un esempio di uno script per una `Office-Credentials` credenziale denominata con `admin@TenantOne.com`il nome utente di. USA `Get-AutomationPSCredential` per importare le credenziali di Office 365.

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

## <a name="running-the-script-in-a-runbook"></a>Esecuzione dello script in un Runbook

È possibile usare lo script in un Runbook di automazione di Azure. Ad esempio, si userà il tipo Runbook di PowerShell.

1. Creare un nuovo Runbook di PowerShell. Vedere [creare un Runbook di automazione di Azure](https://docs.microsoft.com/azure/automation/automation-quickstart-create-runbook).
2. Dall'account di automazione selezionare **manuali operativi** in **automazione processi**.
3. Selezionare il nuovo Runbook e fare clic su **modifica**.
4. Copiare lo script e incollarlo nell'editor di testo per Runbook.
5. Selezionare **Asset**, quindi **credenziali** e verificare che le credenziali di Office 365 siano presenti.
6. Fare clic su **Save**.
7. Selezionare **riquadro Test**, quindi fare clic su **Avvia** per avviare il test del Runbook. Vedere [gestire manuali operativi in automazione di Azure](https://docs.microsoft.com/azure/automation/manage-runbooks).
8. Al termine del test, uscire dal riquadro test.

## <a name="publishing-and-scheduling-the-runbook"></a>Pubblicazione e pianificazione di Runbook

Per pubblicare e pianificare il Runbook, vedere [gestire manuali operativi in automazione di Azure](https://docs.microsoft.com/azure/automation/manage-runbooks).

## <a name="next-steps"></a>Passaggi successivi

* È possibile trovare informazioni sugli asset delle credenziali di automazione negli [asset delle credenziali in automazione di Azure](shared-resources/credentials.md).
* Vedere [gestire i moduli in automazione di Azure](shared-resources/modules.md) per informazioni su come usare i moduli di automazione.
* Per una panoramica della gestione di Runbook, vedere [gestire manuali operativi in automazione di Azure](https://docs.microsoft.com/azure/automation/manage-runbooks).
* Per altre informazioni sui metodi che possono essere usati per avviare un Runbook in automazione di Azure, vedere [avvio di un Runbook in automazione di Azure](automation-starting-a-runbook.md).
* Per altre informazioni su PowerShell, inclusi i riferimenti al linguaggio e i moduli di formazione, vedere la [documentazione di PowerShell](https://docs.microsoft.com/powershell/scripting/overview).