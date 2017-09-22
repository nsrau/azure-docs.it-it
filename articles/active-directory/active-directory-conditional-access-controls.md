---
title: Controlli nell'accesso condizionale di Azure Active Directory | Microsoft Docs
description: Informazioni sul funzionamento dei controlli nell'accesso condizionale di Azure Active Directory.
services: active-directory
keywords: accesso condizionale alle app, accesso condizionale con Azure AD, accesso sicuro alle risorse aziendali, criteri di accesso condizionale
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/07/2017
ms.author: markvi
ms.reviewer: calebb
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: 0f7d847c98e790c542f3a3e666b9a887099a6cbc
ms.contentlocale: it-it
ms.lasthandoff: 09/13/2017

---

# <a name="controls-in-azure-active-directory-conditional-access"></a>Controlli nell'accesso condizionale di Azure Active Directory 

Con l'[accesso condizionale di Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md) è possibile controllare il modo in cui gli utenti autorizzati accedono alle app cloud. Nei criteri di accesso condizionale si definisce la risposta ("fare questo") a una specifica condizione ("quando accade questo"). Nel contesto dell'accesso condizionale, 

- "**Quando accade questo**" è l'**istruzione della condizione**
- "**Fare questo**" sono i **controlli**

![Controllo](./media/active-directory-conditional-access-controls/11.png)

La combinazione di un'istruzione della condizione con i controlli rappresenta un criterio di accesso condizionale.

![Controllo](./media/active-directory-conditional-access-controls/12.png)

Ogni controllo è un requisito che deve essere soddisfatto dalla persona o dal sistema che esegue l'accesso o una restrizione sulle operazioni che l'utente può eseguire dopo l'accesso. 

Sono disponibili due tipi di controlli: 

- **Controlli di concessione**: per controllare l'accesso

- **Controlli di sessione**: per limitare l'accesso all'interno di una sessione

Questo argomento illustra i diversi controlli disponibili nell'accesso condizionale di Azure AD. 

## <a name="grant-controls"></a>Controlli di concessione

Con i controlli di concessione è possibile bloccare completamente l'accesso o concedere l'accesso con requisiti aggiuntivi selezionando i controlli desiderati. Per più controlli, è possibile:

- Richiedere che tutti i controlli selezionati siano soddisfatti (*AND*) 
- Richiedere che un solo controllo selezionato sia soddisfatto (*OR*)

![Controllo](./media/active-directory-conditional-access-controls/51.png)



### <a name="multi-factor-authentication"></a>Autenticazione a più fattori

È possibile usare questo controllo per richiedere l'autenticazione a più fattori per l'accesso all'app cloud specificata. Questo controllo supporta i provider di autenticazione a più fattori seguenti: 

- Azure Multi-Factor Authentication 

- Un provider di autenticazione a più fattori locale, in combinazione con Active Directory Federation Services (AD FS)
 
L'uso dell'autenticazione a più fattori assicura la protezione delle risorse nel caso in cui un utente non autorizzato abbia avuto accesso alle credenziali primarie di un utente valido.



### <a name="compliant-device"></a>Dispositivo conforme

È possibile configurare criteri di accesso condizionale in base al dispositivo. Lo scopo di un criterio di accesso condizionale basato sul dispositivo è di concedere l'accesso alle risorse configurate solo da dispositivi considerati attendibili. Richiedere un dispositivo conforme è una delle opzioni disponibili per definire un dispositivo attendibile. Per altre informazioni, vedere [Configurare i criteri di accesso condizionale basato su dispositivo di Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md).

### <a name="domain-joined-device"></a>Dispositivo aggiunto a un dominio

Richiedere un dispositivo aggiunto a un dominio è un'altra opzione disponibile per configurare criteri di accesso condizionale basato su dispositivo. Questo requisito si riferisce a tablet aziendali, laptop e desktop di Windows aggiunti ad Active Directory locale. Per altre informazioni, vedere [Configurare i criteri di accesso condizionale basato su dispositivo di Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md).





### <a name="approved-client-app"></a>App client approvata

Poiché i dipendenti di un'azienda usano i dispositivi mobili per attività sia lavorative che personali, può essere utile avere la possibilità di proteggere i dati aziendali accessibili da dispositivi anche nel caso in cui vengano gestiti da altri utenti.
È possibile usare i [criteri di protezione delle app di Intune](https://docs.microsoft.com/intune/app-protection-policy) per proteggere i dati aziendali indipendentemente dalla soluzione di gestione dei dispositivi mobili (MDM).


Con le app client approvate, è possibile richiedere a un'app client che prova ad accedere alle app cloud di supportare i [criteri di protezione delle app di Intune](https://docs.microsoft.com/intune/app-protection-policy). È possibile, ad esempio, limitare l'accesso a Exchange Online all'app Outlook. I criteri di accesso condizionale che richiedono app client approvate sono noti anche come [criteri di accesso condizionale basato su app](active-directory-conditional-access-mam.md). Per un elenco di app client approvate supportate, vedere [Requisito per le app client approvate](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement).



## <a name="session-controls"></a>Controlli di sessione

I controlli di sessione consentono di limitare l'esperienza in un'app cloud. Questi controlli sono imposti dalle app cloud e si basano sulle informazioni aggiuntive relative alla sessione fornite da Azure AD all'app.

![Controllo](./media/active-directory-conditional-access-controls/31.png)

### <a name="use-app-enforced-restrictions"></a>Usa restrizioni imposte dalle app

È possibile usare questo controllo per chiedere ad Azure AD di passare le informazioni sul dispositivo all'app cloud, che in questo modo può sapere se l'utente proviene da un dispositivo conforme o da un dispositivo aggiunto a un dominio. Questo controllo è attualmente supportato solo con SharePoint come app cloud. SharePoint usa le informazioni sul dispositivo per offrire agli utenti un'esperienza completa o limitata, a seconda dello stato del dispositivo.
Per altre informazioni su come richiedere l'accesso limitato con SharePoint, vedere [Controllare l'accesso da dispositivi non gestiti](https://aka.ms/spolimitedaccessdocs).



## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come configurare criteri di accesso condizionale, vedere [Introduzione all'accesso condizionale in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Se si è pronti per configurare i criteri di accesso condizionale per l'ambiente in uso, vedere il [Procedure consigliate per l'accesso condizionale in Azure Active Directory](active-directory-conditional-access-best-practices.md). 

