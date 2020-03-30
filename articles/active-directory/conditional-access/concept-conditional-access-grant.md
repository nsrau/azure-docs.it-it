---
title: Concedere i controlli nei criteri di accesso condizionale - Azure Active DirectoryGrant controls in Conditional Access policy - Azure Active Directory
description: Che cosa sono i controlli delle sovvenzioni in un criterio di accesso condizionale di Azure ADWhat are grant controls in an Azure AD Conditional Access policy
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02ec8dace971cd4dc1407c9e8d20839504c9ecc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331851"
---
# <a name="conditional-access-grant"></a>Accesso condizionale: sovvenzione

All'interno di un criterio di accesso condizionale, un amministratore può utilizzare i controlli di accesso per concedere o bloccare l'accesso alle risorse.

![Criteri di accesso condizionale con un controllo di concessione che richiede l'autenticazione a più fattoriConditional Access policy with a grant control requiring multi-factor authentication](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>Blocca accesso

Block prende in considerazione tutte le assegnazioni e impedisce l'accesso in base alla configurazione dei criteri di accesso condizionale.

Il blocco è un potente controllo che dovrebbe essere esercitato con conoscenze adeguate. È qualcosa che gli amministratori devono utilizzare la [modalità solo report](concept-conditional-access-report-only.md) per testare prima di abilitare.

## <a name="grant-access"></a>Concedere l'accesso

Gli amministratori possono scegliere di applicare uno o più controlli quando concedono l'accesso. Questi controlli includono le seguenti opzioni: 

- [Richiedere l'autenticazione a più fattori (Azure Multi-Factor Authentication)Require multi-factor authentication (Azure Multi-Factor Authentication)](../authentication/concept-mfa-howitworks.md)
- [Richiedere che il dispositivo sia contrassegnato come conforme (Microsoft Intune)Require device to be marked as compliant (Microsoft Intune)](/intune/protect/device-compliance-get-started)
- [Richiedere un dispositivo aggiunto ad Azure AD ibridoRequire hybrid Azure AD joined device](../devices/concept-azure-ad-join-hybrid.md)
- [Richiedere app client approvata](app-based-conditional-access.md)
- [Richiedere criteri di protezione dell'app](app-protection-based-conditional-access.md)

Quando gli amministratori scelgono di combinare queste opzioni, possono scegliere i seguenti metodi:

- Richiedi tutti i controlli selezionati (controllo **E** controllo)
- Richiedi uno dei controlli selezionati (controllo **OR** di controllo)

Per impostazione predefinita, l'accesso condizionale richiede tutti i controlli selezionati.

### <a name="require-multi-factor-authentication"></a>Richiedi autenticazione a più fattori

Selezionando questa casella di controllo si richiederà agli utenti di eseguire Azure Multi-Factor Authentication.Selecting this checkbox will require users to perform Azure Multi-Factor Authentication. Per altre informazioni sulla distribuzione di Azure Multi-Factor Authentication, l'articolo Pianificazione di una distribuzione di [Azure MultiFactor Authentication basata su cloud](../authentication/howto-mfa-getstarted.md)è disponibile.

### <a name="require-device-to-be-marked-as-compliant"></a>Richiedere che i dispositivi siano contrassegnati come conformi

Le organizzazioni che hanno distribuito Microsoft Intune possono usare le informazioni restituite dai propri dispositivi per identificare i dispositivi che soddisfano requisiti di conformità specifici. Queste informazioni sulla conformità dei criteri vengono inoltrate da Intune ad Azure AD in cui l'accesso condizionale può prendere decisioni per concedere o bloccare l'accesso alle risorse. Per altre informazioni sui criteri di conformità, vedere l'articolo [Impostare le regole nei dispositivi per consentire l'accesso alle risorse nell'organizzazione usando Intune.For](/intune/protect/device-compliance-get-started)more information about compliance policies, see the article Set rules on devices to allow access to resources in your organization using Intune.

Un dispositivo può essere contrassegnato come conforme da Intune (per qualsiasi sistema operativo del dispositivo) o da un sistema MDM di terze parti per i dispositivi Windows 10.A device can be marked as compliant by Intune (for any device OS) or by third-party MDM system for Windows 10 devices. Jamf pro è l'unico sistema MDM di terze parti supportato. Ulteriori informazioni sull'integrazione sono disponibili nell'articolo [Integrare Jamf Pro con Intune per la conformità.](/intune/protect/conditional-access-integrate-jamf)

I dispositivi devono essere registrati in Azure AD prima di poter essere contrassegnati come conformi. Ulteriori informazioni sulla registrazione del dispositivo possono essere trovate nell'articolo, [Che cos'è un'identità del dispositivo](../devices/overview.md).

### <a name="require-hybrid-azure-ad-joined-device"></a>Richiedere un dispositivo aggiunto ad Azure AD ibridoRequire hybrid Azure AD joined device

Le organizzazioni possono scegliere di usare l'identità del dispositivo come parte dei criteri di accesso condizionale. Le organizzazioni possono richiedere che i dispositivi siano aggiunti ad Azure AD ibrido usando questa casella di controllo. Per ulteriori informazioni sulle identità dei dispositivi, vedere l'articolo [Che cos'è un'identità del dispositivo?](../devices/overview.md).

### <a name="require-approved-client-app"></a>Richiedere app client approvata

Le organizzazioni possono richiedere che un tentativo di accesso alle app cloud selezionate debba essere eseguito da un'app client approvata. Queste app client approvate supportano i criteri di [protezione delle app di Intune](/intune/app-protection-policy) indipendentemente da qualsiasi soluzione di gestione dei dispositivi mobili (MDM).

Per sfruttare questo controllo di concessione, l'accesso condizionale richiede che il dispositivo sia registrato in Azure Active Directory che richiede l'uso di un'app broker. L'app broker può essere Microsoft Authenticator per iOS o il Portale aziendale Microsoft per i dispositivi Android. Se un'app broker non è installata nel dispositivo quando l'utente tenta di eseguire l'autenticazione, l'utente viene reindirizzato all'app store per installare l'app broker.

Questa impostazione si applica alle seguenti app iOS e Android:

- Microsoft Azure Information Protection
- Microsoft Bookings
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune Managed Browser
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft Office
- Microsoft Office Hub
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype for Business
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer
- Microsoft Whiteboard

**Osservazioni**

- Le app client approvate supportano la funzionalità di gestione di applicazioni mobili di Intune.
- Il requisito **Richiedi app client approvata**:
   - Supporta solo iOS e Android come condizione per le piattaforme del dispositivo.
   - Per registrare il dispositivo è necessaria un'app broker. In iOS, l'app broker è Microsoft Authenticator e in Android è l'app Portale aziendale di Intune.On iOS, the broker app is Microsoft Authenticator and on Android, it is Intune Company Portal app.
- L'accesso condizionale non può considerare Microsoft Edge in modalità InPrivate un'app client approvata.

Vedere l'articolo [Procedura: Richiedere app client approvate per l'accesso alle app cloud con accesso condizionale](app-based-conditional-access.md) per esempi di configurazione.

### <a name="require-app-protection-policy"></a>Richiedere criteri di protezione dell'app

Nei criteri di accesso condizionale è possibile richiedere che nell'app client siano presenti criteri di protezione delle app di [Intune](/intune/app-protection-policy) prima che l'accesso sia disponibile per le app cloud selezionate. 

Per sfruttare questo controllo di concessione, l'accesso condizionale richiede che il dispositivo sia registrato in Azure Active Directory che richiede l'uso di un'app broker. L'app broker può essere Microsoft Authenticator per iOS o il Portale aziendale Microsoft per i dispositivi Android. Se un'app broker non è installata nel dispositivo quando l'utente tenta di eseguire l'autenticazione, l'utente viene reindirizzato all'app store per installare l'app broker.

Questa impostazione è valida solo per le app client seguenti:

- Microsoft Cortana
- Microsoft OneDrive
- Microsoft Outlook
- Microsoft Planner

**Osservazioni**

- Le app per i criteri di protezione delle app supportano la funzionalità di gestione delle applicazioni mobili di Intune con la protezione dei criteri.
- Richiedi requisiti **dei criteri di protezione delle app:The Require app protection policy** requirements:
    - Supporta solo iOS e Android come condizione per le piattaforme del dispositivo.
    - Per registrare il dispositivo è necessaria un'app broker. In iOS, l'app broker è Microsoft Authenticator e in Android è l'app Portale aziendale di Intune.On iOS, the broker app is Microsoft Authenticator and on Android, it is Intune Company Portal app.

Vedere l'articolo Procedura: Richiedere criteri di [protezione delle app e un'app client approvata per l'accesso alle app cloud con accesso condizionale](app-protection-based-conditional-access.md) per esempi di configurazione.

### <a name="terms-of-use"></a>Condizioni per l'utilizzo

Se l'organizzazione ha creato le condizioni per l'utilizzo, le opzioni aggiuntive potrebbero essere visibili sotto i controlli delle sovvenzioni. Queste opzioni consentono agli amministratori di richiedere il riconoscimento delle condizioni per l'utilizzo come condizione per accedere alle risorse protette dai criteri. Ulteriori informazioni sulle condizioni per l'utilizzo sono disponibili nell'articolo [Condizioni per l'utilizzo di Azure Active Directory](terms-of-use.md).

## <a name="next-steps"></a>Passaggi successivi

- [Accesso condizionale: controlli sessioneConditional Access: Session controls](concept-conditional-access-session.md)

- [Criteri comuni di Accesso condizionaleConditional Access common policies](concept-conditional-access-policy-common.md)

- [Modalità solo report](concept-conditional-access-report-only.md)
