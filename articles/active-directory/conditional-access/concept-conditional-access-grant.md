---
title: Concedere controlli nei criteri di accesso condizionale-Azure Active Directory
description: Che cosa sono i controlli di concessione in un Azure AD criteri di accesso condizionale
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: a59939fc7988e1a94bdfb9fac2d77011422e4983
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87274681"
---
# <a name="conditional-access-grant"></a>Accesso condizionale: Concedi

All'interno di un criterio di accesso condizionale, un amministratore può usare i controlli di accesso per concedere o bloccare l'accesso alle risorse.

![Criteri di accesso condizionale con un controllo di concessione che richiede l'autenticazione a più fattori](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>Blocca accesso

Block prende in considerazione le assegnazioni e impedisce l'accesso in base alla configurazione dei criteri di accesso condizionale.

Block è un potente controllo che deve essere dotato di una conoscenza adeguata. I criteri con istruzioni Block possono avere effetti collaterali imprevisti. Il test e la convalida corretti sono fondamentali prima di abilitare su larga scala. Gli amministratori devono utilizzare strumenti come la [modalità solo report di accesso condizionale](concept-conditional-access-report-only.md) e [lo strumento What If nell'accesso condizionale](what-if-tool.md) durante l'esecuzione di modifiche.

## <a name="grant-access"></a>Concedere l'accesso

Gli amministratori possono scegliere di applicare uno o più controlli durante la concessione dell'accesso. Questi controlli includono le opzioni seguenti: 

- [Richiedi autenticazione a più fattori (Multi-Factor Authentication di Azure)](../authentication/concept-mfa-howitworks.md)
- [Richiedi che il dispositivo sia contrassegnato come conforme (Microsoft Intune)](/intune/protect/device-compliance-get-started)
- [Richiedi dispositivo ibrido Azure AD aggiunto](../devices/concept-azure-ad-join-hybrid.md)
- [Richiedi app client approvata](app-based-conditional-access.md)
- [Richiedere criteri di protezione dell'app](app-protection-based-conditional-access.md)
- [Richiedere la modifica della password](#require-password-change)

Quando gli amministratori scelgono di combinare queste opzioni, possono scegliere i metodi seguenti:

- Richiedi tutti i controlli selezionati (controllo **e** controllo)
- Richiedi uno dei controlli selezionati (controllo **o** controllo)

Per impostazione predefinita, l'accesso condizionale richiede tutti i controlli selezionati.

### <a name="require-multi-factor-authentication"></a>Richiedi autenticazione a più fattori

Selezionando questa casella di controllo, gli utenti dovranno eseguire Multi-Factor Authentication di Azure. Altre informazioni sulla distribuzione di Azure Multi-Factor Authentication sono disponibili nell'articolo [pianificazione di una distribuzione di azure multi-factor authentication basata sul cloud](../authentication/howto-mfa-getstarted.md).

### <a name="require-device-to-be-marked-as-compliant"></a>Richiedere che i dispositivi siano contrassegnati come conformi

Le organizzazioni che hanno distribuito Microsoft Intune possono usare le informazioni restituite dai dispositivi per identificare i dispositivi che soddisfano requisiti di conformità specifici. Le informazioni di conformità dei criteri vengono trasmesse da Intune a Azure AD dove l'accesso condizionale può prendere decisioni per concedere o bloccare l'accesso alle risorse. Per altre informazioni sui criteri di conformità, vedere l'articolo [impostare le regole sui dispositivi per consentire l'accesso alle risorse dell'organizzazione con Intune](/intune/protect/device-compliance-get-started).

Un dispositivo può essere contrassegnato come conforme da Intune (per qualsiasi sistema operativo del dispositivo) o da un sistema MDM di terze parti per i dispositivi Windows 10. JAMF Pro è l'unico sistema MDM di terze parti supportato. Altre informazioni sull'integrazione sono disponibili nell'articolo [integrare JAMF Pro con Intune per la conformità](/intune/protect/conditional-access-integrate-jamf).

I dispositivi devono essere registrati in Azure AD prima che possano essere contrassegnati come conformi. Altre informazioni sulla registrazione del dispositivo sono disponibili nell'articolo, [che cos'è un'identità del dispositivo](../devices/overview.md).

### <a name="require-hybrid-azure-ad-joined-device"></a>Richiedere un dispositivo aggiunto ad Azure AD ibrido

Le organizzazioni possono scegliere di usare l'identità del dispositivo come parte dei criteri di accesso condizionale. Le organizzazioni possono richiedere che i dispositivi siano ibridi Azure AD Uniti con questa casella di controllo. Per altre informazioni sulle identità dei dispositivi, vedere l'articolo [che cos'è un'identità del dispositivo?](../devices/overview.md).

Quando si usa il [flusso OAuth del codice del dispositivo](../develop/v2-oauth2-device-code.md), non sono supportate le condizioni Richiedi controllo concessione dispositivo gestito o stato dispositivo. Questo perché il dispositivo che esegue l'autenticazione non può fornire lo stato del dispositivo al dispositivo che fornisce un codice e lo stato del dispositivo nel token è bloccato al dispositivo che esegue l'autenticazione. Usare invece il comando Richiedi concessione di autenticazione a più fattori.

### <a name="require-approved-client-app"></a>Richiedere app client approvata

Le organizzazioni possono richiedere che venga eseguito un tentativo di accesso alle app Cloud selezionate da un'app client approvata. Queste app client approvate supportano i [criteri di protezione delle app di Intune](/intune/app-protection-policy) in modo indipendente da qualsiasi soluzione di gestione di dispositivi mobili (MDM).

Per sfruttare questo controllo di concessione, l'accesso condizionale richiede che il dispositivo sia registrato in Azure Active Directory che richiede l'uso di un'app Broker. L'app broker può essere Microsoft Authenticator per iOS o il Portale aziendale Microsoft per i dispositivi Android. Se un'app Broker non è installata nel dispositivo quando l'utente tenta di eseguire l'autenticazione, l'utente viene reindirizzato all'App Store per installare l'app Broker.

Questa impostazione si applica alle app iOS e Android seguenti:

- Microsoft Azure Information Protection
- Microsoft Bookings
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Power Automate
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft Office
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

**Osservazioni:**

- Le app client approvate supportano la funzionalità di gestione di applicazioni mobili di Intune.
- Il requisito **Richiedi app client approvata**:
   - Supporta solo iOS e Android come condizione per le piattaforme del dispositivo.
   - Per registrare il dispositivo è necessaria un'app Broker. In iOS l'app Broker è Microsoft Authenticator e in Android è Portale aziendale Intune app.
- L'accesso condizionale non può considerare Microsoft Edge in modalità InPrivate per un'app client approvata.

Vedere l'articolo [procedura: richiedere app client approvate per l'accesso alle app cloud con accesso condizionale](app-based-conditional-access.md) per esempi di configurazione.

### <a name="require-app-protection-policy"></a>Richiedere criteri di protezione dell'app

Nei criteri di accesso condizionale è possibile richiedere che i [criteri di protezione delle app di Intune](/intune/app-protection-policy) siano presenti nell'app client prima che l'accesso sia disponibile per le app Cloud selezionate. 

Per sfruttare questo controllo di concessione, l'accesso condizionale richiede che il dispositivo sia registrato in Azure Active Directory che richiede l'uso di un'app Broker. L'app broker può essere Microsoft Authenticator per iOS o il Portale aziendale Microsoft per i dispositivi Android. Se un'app Broker non è installata nel dispositivo quando l'utente tenta di eseguire l'autenticazione, l'utente viene reindirizzato all'App Store per installare l'app Broker.

Questa impostazione è valida solo per le app client seguenti:

- Microsoft Cortana
- Microsoft Edge
- Microsoft Excel
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Word
- Più righe per Intune
- Nove messaggi di posta elettronica & calendario

> [!NOTE]
> Microsoft Kaizala, Microsoft Skype for business e Microsoft Visio non supportano la concessione per la **richiesta dei criteri di protezione delle app** . Se è necessario che queste app funzionino, usare l'autorizzazione Richiedi solo le **app approvate** . L'uso della clausola OR tra le due concessioni non funzionerà per queste tre applicazioni.

**Osservazioni:**

- Le app per i criteri di protezione delle app supportano la funzionalità di gestione delle applicazioni mobili di Intune con protezione dei criteri.
- Richiede i requisiti dei **criteri di protezione delle app** :
    - Supporta solo iOS e Android come condizione per le piattaforme del dispositivo.
    - Per registrare il dispositivo è necessaria un'app Broker. In iOS l'app Broker è Microsoft Authenticator e in Android è Portale aziendale Intune app.

Per gli esempi di configurazione, vedere l'articolo [procedura: richiedere i criteri di protezione delle app e un'app client approvata per l'accesso alle app cloud con accesso condizionale](app-protection-based-conditional-access.md) .

### <a name="require-password-change"></a>Richiedere la modifica della password 

Quando viene rilevato il rischio utente, utilizzando le condizioni dei criteri di rischio dell'utente, gli amministratori possono scegliere di modificare la password in modo sicuro utilizzando Azure AD la reimpostazione della password self-service. Se viene rilevato un rischio per l'utente, gli utenti possono eseguire la reimpostazione della password self-service per la correzione automatica, in questo modo si chiuderà l'evento di rischio utente per evitare inutili rumori per gli amministratori. 

Quando a un utente viene richiesto di modificare la password, verrà prima di tutto richiesto di completare l'autenticazione a più fattori. È necessario assicurarsi che tutti gli utenti abbiano effettuato la registrazione per l'autenticazione a più fattori, in modo che vengano preparati nel caso in cui vengano rilevati rischi per il proprio account.  

> [!WARNING]
> Prima di attivare i criteri di rischio utente, è necessario che gli utenti abbiano prima effettuato la registrazione per la reimpostazione della password self-service. 

Quando si configura un criterio usando il controllo delle modifiche della password, si verifica un paio di restrizioni.  

1. Il criterio deve essere assegnato a "tutte le app Cloud". In questo modo si impedisce a un utente malintenzionato di usare un'app diversa per modificare la password dell'utente e reimpostare il rischio per l'account, semplicemente eseguendo l'accesso a un'altra app. 
1. Non è possibile usare richiesta di modifica della password con altri controlli, ad esempio richiedere un dispositivo conforme.  
1. Il controllo delle modifiche della password può essere usato solo con la condizione di assegnazione di utenti e gruppi, la condizione di assegnazione di app Cloud (che deve essere impostata su tutti) e le condizioni di rischio utente. 

### <a name="terms-of-use"></a>Condizioni per l'utilizzo

Se l'organizzazione ha creato le condizioni per l'utilizzo, è possibile che siano visibili opzioni aggiuntive sotto i controlli di concessione. Queste opzioni consentono agli amministratori di richiedere il riconoscimento delle condizioni per l'utilizzo come condizione di accesso alle risorse protette dai criteri. Altre informazioni sulle condizioni per l'utilizzo sono disponibili nell'articolo [Azure Active Directory condizioni](terms-of-use.md)per l'utilizzo.

## <a name="next-steps"></a>Passaggi successivi

- [Accesso condizionale: controlli della sessione](concept-conditional-access-session.md)

- [Criteri comuni di accesso condizionale ](concept-conditional-access-policy-common.md)

- [Modalità solo report](concept-conditional-access-report-only.md)
