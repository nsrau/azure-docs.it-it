---
title: Concedere controlli nei criteri di accesso condizionale-Azure Active Directory
description: Che cosa sono i controlli di concessione in un Azure AD criteri di accesso condizionale
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89063cc8131c28f20153c6fe9b4c71b58794e609
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192116"
---
# <a name="conditional-access-grant"></a>Accesso condizionale: Concedi

All'interno di un criterio di accesso condizionale, un amministratore può usare i controlli di accesso per concedere o bloccare l'accesso alle risorse.

![Criteri di accesso condizionale con un controllo di concessione che richiede l'autenticazione a più fattori](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>Blocca accesso

Block prende in considerazione le assegnazioni e impedisce l'accesso in base alla configurazione dei criteri di accesso condizionale.

Block è un potente controllo che deve essere dotato di una conoscenza adeguata. È necessario che gli amministratori usino la [modalità solo report](concept-conditional-access-report-only.md) per eseguire il test prima di abilitare.

## <a name="grant-access"></a>Concedere l'accesso

Gli amministratori possono scegliere di applicare uno o più controlli durante la concessione dell'accesso. Questi controlli includono le opzioni seguenti: 

- [Richiedi autenticazione a più fattori (Multi-Factor Authentication di Azure)](../authentication/concept-mfa-howitworks.md)
- [Richiedi che il dispositivo sia contrassegnato come conforme (Microsoft Intune)](https://docs.microsoft.com/intune/protect/device-compliance-get-started)
- [Richiedi dispositivo ibrido Azure AD aggiunto](../devices/concept-azure-ad-join-hybrid.md)
- [Richiedi app client approvata](app-based-conditional-access.md)
- [Richiedi i criteri di protezione delle app](app-protection-based-conditional-access.md)

Quando gli amministratori scelgono di combinare queste opzioni, possono scegliere i metodi seguenti:

- Richiedi tutti i controlli selezionati (controllo **e** controllo)
- Richiedi uno dei controlli selezionati (controllo **o** controllo)

Per impostazione predefinita, l'accesso condizionale richiede tutti i controlli selezionati.

### <a name="require-multi-factor-authentication"></a>Richiedi autenticazione a più fattori

Selezionando questa casella di controllo, gli utenti dovranno eseguire Multi-Factor Authentication di Azure. Altre informazioni sulla distribuzione di Azure Multi-Factor Authentication sono disponibili nell'articolo [pianificazione di una distribuzione di azure multi-factor authentication basata sul cloud](../authentication/howto-mfa-getstarted.md).

### <a name="require-device-to-be-marked-as-compliant"></a>Richiedere che i dispositivi siano contrassegnati come conformi

Le organizzazioni che hanno distribuito Microsoft Intune possono usare le informazioni restituite dai dispositivi per identificare i dispositivi che soddisfano requisiti di conformità specifici. Le informazioni di conformità dei criteri vengono trasmesse da Intune a Azure AD dove l'accesso condizionale può prendere decisioni per concedere o bloccare l'accesso alle risorse. Per altre informazioni sui criteri di conformità, vedere l'articolo [impostare le regole sui dispositivi per consentire l'accesso alle risorse dell'organizzazione con Intune](https://docs.microsoft.com/intune/protect/device-compliance-get-started).

### <a name="require-hybrid-azure-ad-joined-device"></a>Richiedi dispositivo ibrido Azure AD aggiunto

Le organizzazioni possono scegliere di usare l'identità del dispositivo come parte dei criteri di accesso condizionale. Le organizzazioni possono richiedere che i dispositivi siano ibridi Azure AD Uniti con questa casella di controllo. Per altre informazioni sulle identità dei dispositivi, vedere l'articolo [che cos'è un'identità del dispositivo?](../devices/overview.md).

### <a name="require-approved-client-app"></a>Richiedere app client approvata

Le organizzazioni possono richiedere che venga eseguito un tentativo di accesso alle app Cloud selezionate da un'app client approvata.

Questa impostazione è valida solo per le app client seguenti:

- Microsoft Azure Information Protection
- Prenotazioni Microsoft
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune Managed Browser
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
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

**Osservazioni:**

- Le app client approvate supportano la funzionalità di gestione di applicazioni mobili di Intune.
- Il requisito **Richiedi app client approvata**:
   - Supporta solo le condizioni della piattaforma per dispositivi iOS e Android.
- L'accesso condizionale non può considerare Microsoft Edge in modalità InPrivate per un'app client approvata.

### <a name="require-app-protection-policy"></a>Richiedere criteri di protezione dell'app

Nei criteri di accesso condizionale è possibile richiedere che un criterio di protezione delle app sia presente nell'app client prima che l'accesso sia disponibile per le app Cloud selezionate. 

![Controllare l'accesso con i criteri di protezione delle app](./media/technical-reference/22.png)

Questa impostazione è valida solo per le app client seguenti:

- Microsoft Cortana
- Microsoft OneDrive
- Microsoft Outlook
- Microsoft Planner

**Osservazioni:**

- Le app per i criteri di protezione delle app supportano la funzionalità di gestione delle applicazioni mobili di Intune con protezione dei criteri.
- Richiede i requisiti dei **criteri di protezione delle app** :
    - Supporta solo le condizioni della piattaforma per dispositivi iOS e Android.

## <a name="next-steps"></a>Passaggi successivi

- [Accesso condizionale: controlli della sessione](concept-conditional-access-session.md)

- [Criteri comuni di accesso condizionale](concept-conditional-access-policy-common.md)

- [Modalità solo report](concept-conditional-access-report-only.md)
