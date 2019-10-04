---
title: Iscrizione self-service per gli account utente verificati tramite posta elettronica - Azure Active Directory | Microsoft Docs
description: Usare l'iscrizione self-service in un tenant di Azure Active Directory (Azure AD)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d949b746f05eb440f5ae28f683dfc838217ab47
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65956505"
---
# <a name="what-is-self-service-sign-up-for-azure-active-directory"></a>Che cos'è self-service per Azure Active Directory per l'abbonamento?

Questo articolo illustra come usare l'iscrizione Self-Service per popolare un'organizzazione in Azure Active Directory (Azure AD). Se si vuole assumere un nome di dominio da un'istanza non gestita di Azure dell'organizzazione di Active Directory, vedere [assumere una directory non gestita come amministratore](domains-admin-takeover.md).

## <a name="why-use-self-service-sign-up"></a>Perché usare iscrizione Self-Service?
* Acquisire più velocemente clienti su servizi da loro richiesti
* Creare offerte basate sulla posta elettronica per un servizio
* Creare flussi di iscrizione basati sulla posta elettronica che rapidamente consentono agli utenti di creare identità usando i relativi alias di posta elettronica di lavoro facile da ricordare
* Una directory di Azure AD creata in modo self-service può essere trasformata in una directory gestita che può essere usata per altri servizi

## <a name="terms-and-definitions"></a>Termini e definizioni
* **Iscrizione Self-Service**: si tratta del metodo in base al quale un utente si iscrive a un servizio cloud e ha un'identità creata automaticamente in Azure AD in base al dominio di posta elettronica.
* **Directory di Azure non gestita**: si tratta della directory in cui viene creata tale identità. È una directory priva di un amministratore globale.
* **Utente verificato per la posta elettronica**: si tratta di un tipo di account utente in Azure AD. Un utente che dispone di un'identità creata automaticamente a seguito dell'iscrizione per un'offerta self-service è noto come utente di posta elettronica verificato. Un utente di posta elettronica verificato è un membro regolare di una directory contrassegnata con creationmethod=EmailVerified.

## <a name="how-do-i-control-self-service-settings"></a>Come controllare le impostazioni di self-service?
Gli amministratori attualmente dispongono di due controlli self-service. Possono controllare:

* Se gli utenti possono essere aggiunti alla directory tramite posta elettronica
* Se gli utenti possono ottenere autonomamente una licenza per applicazioni e servizi

### <a name="how-can-i-control-these-capabilities"></a>Come è possibile controllare queste funzionalità?
Un amministratore può configurare queste funzionalità usando i parametri seguenti del cmdlet Set-MsolCompanySettings di Azure AD:

* **AllowEmailVerifiedUsers** controlla se un utente può essere aggiunto a una directory o crearne una. Se tale parametro è impostato su $false, nessun utente verificato tramite posta elettronica può essere aggiunto alla directory.
* **AllowAdHocSubscriptions** controlla la possibilità per gli utenti di eseguire l'iscrizione Self-Service. Se tale parametro è impostato su $false false per, nessun utente potrà eseguire l'iscrizione Self-Service.
  
AllowEmailVerifiedUsers e AllowAdHocSubscriptions sono le impostazioni a livello di directory che possono essere applicate a una directory o gestita o meno. Ad esempio:

* Si amministra una directory con un dominio verificato, ad esempio contoso.com
* Si usa collaborazione B2B da una directory diversa per invitare un utente che non esiste già (userdoesnotexist@contoso.com) nella home directory di contoso.com
* Nella home directory è attiva l'impostazione AllowEmailVerifiedUsers

Se le condizioni precedenti sono vere, verrà creato un utente membro nella home directory e un utente guest B2B nella directory di invito.

Flow e PowerApps versione di valutazione dell'iscrizione non sono controllati dal **AllowAdHocSubscriptions** impostazione. Per altre informazioni, vedere gli articoli seguenti:

* [Come è possibile impedire agli utenti di iniziare a usare Power BI?](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
* [Domande e risposte riguardo alla registrazione a Microsoft Flow nell'organizzazione](https://docs.microsoft.com/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Come operano congiuntamente i controlli?
Questi due parametri sono utilizzabile in combinazione per definire un controllo più preciso sull'iscrizione Self-Service. Ad esempio, il comando seguente consentirà agli utenti di eseguire funzionalità Self-Service per l'abbonamento, ma solo se tali utenti dispongono già di un account di Azure AD (in altre parole, gli utenti che sarebbe necessario un account di posta elettronica verificato deve essere creato prima di tutto non possono eseguire iscrizione Self-Service):

```powershell
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
```

Diagramma di flusso seguente illustra le diverse combinazioni di questi parametri e le condizioni risultanti per la directory e l'iscrizione Self-Service.

![diagramma di flusso dei controlli di iscrizione self-service](./media/directory-self-service-signup/SelfServiceSignUpControls.png)

Per ulteriori informazioni ed esempi su come usare questi parametri, vedere [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

## <a name="next-steps"></a>Passaggi successivi

* [Aggiungere un nome di dominio personalizzato ad Azure AD](../fundamentals/add-custom-domain.md)
* [Come installare e configurare Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Informazioni di riferimento sui cmdlet di Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)
* [Chiudere l'account aziendale o dell'istituto di istruzione in una directory non gestita](users-close-account.md)
