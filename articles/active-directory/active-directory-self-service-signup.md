---
title: Iscrizione self-service o di prova in Azure Active Directory | Microsoft Docs
description: Usare l'iscrizione self-service in un tenant di Azure Active Directory (Azure AD)
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: b9f01876-29d1-4ab8-8b74-04d43d532f4b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/28/2018
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: 9f2b541d5028596f9beabc7fd82001b4c9dacad4
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="what-is-self-service-signup-for-azure-active-directory"></a>Informazioni sull'iscrizione self-service per Azure Active Directory.
Questo articolo descrive il processo di iscrizione self-service e come supportarlo in Azure Active Directory (Azure AD). Se si intende sostituire un nome di dominio da un tenant di Azure AD non gestito, vedere [Take over an unmanaged directory as administrator](domains-admin-takeover.md) (Sostituire una directory non gestita come amministratore).

## <a name="why-use-self-service-signup"></a>Perché usare l'iscrizione self-service?
* Acquisire più velocemente clienti su servizi da loro richiesti
* Creare offerte basate sulla posta elettronica per un servizio
* Creare flussi di iscrizione basati sulla posta elettronica che consentono rapidamente agli utenti di creare identità usando i relativi alias di posta elettronica di lavoro facili da ricordare
* Una directory di Azure AD creata in modo self-service può essere trasformata in una directory gestita che può essere usata per altri servizi

## <a name="terms-and-definitions"></a>Termini e definizioni
* **Iscrizione self-service**: si tratta del metodo in base al quale un utente si iscrive a un servizio cloud e ha un'identità creata automaticamente in Azure AD in base al dominio di posta elettronica.
* **Directory di Azure AD non gestita**: si tratta della directory in cui viene creata tale identità. È una directory priva di un amministratore globale.
* **Utente verificato per la posta elettronica**: si tratta di un tipo di account utente in Azure AD. Un utente che dispone di un'identità creata automaticamente a seguito dell'iscrizione per un'offerta self-service è noto come utente di posta elettronica verificato. Un utente di posta elettronica verificato è un membro regolare di una directory contrassegnata con creationmethod=EmailVerified.

## <a name="how-do-i-control-self-service-settings"></a>Come controllare le impostazioni di self-service?
Gli amministratori attualmente dispongono di due controlli self-service. Possono controllare:

* Se gli utenti possono essere aggiunti alla directory tramite posta elettronica.
* Se gli utenti possono ottenere autonomamente una licenza per applicazioni e servizi.

### <a name="how-can-i-control-these-capabilities"></a>Come è possibile controllare queste funzionalità?
Un amministratore può configurare queste funzionalità usando i parametri seguenti del cmdlet Set-MsolCompanySettings di Azure AD:

* **AllowEmailVerifiedUsers** controlla se un utente può essere aggiunto a una directory non gestita o crearne una. Se tale parametro è impostato su $false, nessun utente verificato tramite posta elettronica può essere aggiunto alla directory.
* **AllowAdHocSubscriptions** controlla la possibilità per gli utenti di eseguire l'iscrizione self-service. Se tale parametro è impostato su $false, nessun utente può eseguire l'iscrizione self-service. 
  
  > [!NOTE]
  > Le iscrizioni di prova a Flow e PowerApps non sono controllate dall'impostazione **AllowAdHocSubscriptions**. Per altre informazioni, vedere gli articoli seguenti:
  > * [Come è possibile impedire agli utenti di iniziare a usare Power BI?](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
  > * [Domande e risposte riguardo alla registrazione a Microsoft Flow nell'organizzazione](https://docs.microsoft.com/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Come operano congiuntamente i controlli?
Questi due parametri possono essere usati insieme per definire un controllo più preciso sull'iscrizione self-service. Ad esempio, il comando seguente consentirà agli utenti di eseguire l'iscrizione self-service, ma solo se tali utenti dispongono già di un account in Azure AD (in altre parole, gli utenti, per i quali sarebbe necessario che venisse creato prima un account di posta elettronica verificato, non possono eseguire l'iscrizione self-service):

````
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
````
Il diagramma di flusso seguente illustra le diverse combinazioni di questi parametri e le condizioni risultanti per la directory e l'iscrizione self-service.

![][1]

Per ulteriori informazioni ed esempi su come usare questi parametri, vedere [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

## <a name="next-steps"></a>Passaggi successivi
* [Aggiungere un nome di dominio personalizzato ad Azure AD](add-custom-domain.md)
* [Come installare e configurare Azure PowerShell](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Informazioni di riferimento sui cmdlet di Azure](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
