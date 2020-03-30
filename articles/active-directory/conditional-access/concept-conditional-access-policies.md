---
title: Creazione di criteri di accesso condizionale - Azure Active DirectoryBuilding a Conditional Access policy - Azure Active Directory
description: Quali sono tutte le opzioni disponibili per creare un criterio di accesso condizionale e cosa significano?
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
ms.openlocfilehash: 907ad8705742e4b2e38b13c3c675ebd333bd27d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295319"
---
# <a name="building-a-conditional-access-policy"></a>Creazione di criteri di accesso condizionaleBuilding a Conditional Access policy

Come spiegato nell'articolo [What is Conditional Access](overview.md), un criterio di accesso condizionale è un'istruzione if-then, di **assegnazioni** e **controlli di accesso**. Un criterio di accesso condizionale riunisce i segnali, per prendere decisioni e applicare i criteri organizzativi.

In che modo un'organizzazione crea questi criteri? Che cosa è necessario?

![Accesso condizionale (Segnali , Decisioni , Esecuzione e Politiche)](./media/concept-conditional-access-policies/conditional-access-signal-decision-enforcement.png)

## <a name="assignments"></a>Assegnazioni

La parte delle assegnazioni controlla chi, cosa e dove dei criteri di accesso condizionale.

### <a name="users-and-groups"></a>Utenti e gruppi

[Utenti e gruppi](concept-conditional-access-users-groups.md) assegnano chi includerà o escluderà i criteri. Questa assegnazione può includere tutti gli utenti, gruppi specifici di utenti, i ruoli di directory o gli utenti guest esterni. 

### <a name="cloud-apps-or-actions"></a>App o azioni cloud

[Le app o le azioni cloud](concept-conditional-access-cloud-apps.md) possono includere o escludere le applicazioni cloud o le azioni dell'utente che saranno soggette ai criteri.

### <a name="conditions"></a>Condizioni

Un criterio può contenere più [condizioni](concept-conditional-access-conditions.md).

#### <a name="sign-in-risk"></a>Rischio di accesso

Per le organizzazioni con [Azure AD Identity Protection,](../identity-protection/overview.md)i rilevamenti dei rischi generati possono influire sui criteri di accesso condizionale.

#### <a name="device-platforms"></a>Piattaforme del dispositivo

Le organizzazioni con più piattaforme del sistema operativo per dispositivi potrebbero voler applicare criteri specifici su piattaforme diverse. 

Le informazioni utilizzate per calcolare la piattaforma del dispositivo provengono da origini non verificate, ad esempio stringhe agente utente che possono essere modificate.

#### <a name="locations"></a>Percorsi

I dati sulla posizione sono forniti dai dati di geolocalizzazione IP. Gli amministratori possono scegliere di definire le posizioni e scegliere di contrassegnarne alcune come attendibili come quelle per i percorsi di rete dell'organizzazione.

#### <a name="client-apps"></a>App client

Per impostazione predefinita, i criteri di accesso condizionale si applicano alle app browser, alle app per dispositivi mobili e ai client desktop che supportano l'autenticazione moderna. 

Questa condizione di assegnazione consente ai criteri di accesso condizionale di destinazione di applicazioni client specifiche che non utilizzano l'autenticazione moderna. Queste applicazioni includono client Exchange ActiveSync, applicazioni di Office precedenti che non utilizzano l'autenticazione moderna e protocolli di posta come IMAP, MAPI, POP e SMTP.

#### <a name="device-state"></a>Stato del dispositivo

Questo controllo viene usato per escludere i dispositivi aggiunti ad Azure AD ibrido o contrassegnati come conformi in Intune.This control is used to exclude devices that are hybrid Azure AD joined, or marked a compliant in Intune. Questa esclusione può essere eseguita per bloccare i dispositivi non gestiti. 

## <a name="access-controls"></a>Controlli di accesso

La parte relativa ai controlli di accesso condizionali controlla la modalità di applicazione di un criterio.

### <a name="grant"></a>Concedi

[Grant](concept-conditional-access-grant.md) fornisce agli amministratori un mezzo per l'applicazione dei criteri in cui possono bloccare o concedere l'accesso.

#### <a name="block-access"></a>Blocca accesso

Bloccare l'accesso fa proprio questo, bloccherà l'accesso sotto le assegnazioni specificate. Il controllo del blocco è potente e deve essere esercitato con le conoscenze appropriate.

#### <a name="grant-access"></a>Concedere l'accesso

Il controllo delle sovvenzioni può attivare l'applicazione di uno o più controlli. 

- Richiedere l'autenticazione a più fattori (Azure Multi-Factor Authentication)Require multi-factor authentication (Azure Multi-Factor Authentication)
- Richiedere che il dispositivo venga contrassegnato come conforme (Intune)Require device to be marked as compliant (Intune)
- Richiedi dispositivo aggiunto ad Azure AD ibrido
- Richiedere app client approvata
- Richiedere criteri di protezione dell'app

Gli amministratori possono scegliere di richiedere uno dei controlli precedenti o tutti i controlli selezionati utilizzando le opzioni seguenti. L'impostazione predefinita per più controlli è richiedere tutti.

- Richiedi tutti i controlli selezionati (controllo e controllo)
- Richiedi uno dei controlli selezionati (controllo o controllo)

### <a name="session"></a>sessione

[I controlli della sessione](concept-conditional-access-session.md) possono limitare l'esperienza 

- Usa restrizioni imposte dalle app
   - Attualmente funziona solo con Exchange Online e SharePoint Online.
      - Passa le informazioni sul dispositivo per consentire il controllo dell'esperienza che concede l'accesso completo o limitato.
- Usare il controllo delle app con accesso condizionaleUse Conditional Access App Control
   - Usa i segnali di Microsoft Cloud App Security per eseguire operazioni come: 
      - Bloccare il download, tagliare, copiare e stampare documenti sensibili.
      - Monitorare il comportamento rischioso della sessione.
      - Richiedere l'etichettatura dei file sensibili.
- Frequenza di accesso
   - Possibilità di modificare la frequenza di accesso predefinita per l'autenticazione moderna.
- Sessione del browser permanente
   - Consente agli utenti di rimanere connessi dopo la chiusura e la riapertura della finestra del browser.

## <a name="simple-policies"></a>Politiche semplici

Un criterio di accesso condizionale deve contenere almeno quanto segue da applicare:

- **Nome** del criterio.
- **Assegnazioni**
   - **Utenti e/o gruppi** a cui applicare il criterio.
   - **App cloud o azioni** a cui applicare i criteri.
- **Controlli di accesso**
   - **Concedere** o **bloccare** i controlli

![Criteri di accesso condizionale vuoti](./media/concept-conditional-access-policies/conditional-access-blank-policy.png)

L'articolo [Common Conditional Access policies](concept-conditional-access-policy-common.md) include alcuni criteri che riteniamo utili per la maggior parte delle organizzazioni.

## <a name="next-steps"></a>Passaggi successivi

[Simulare il comportamento di accesso con lo strumento Elementi di ricerca condizionali](troubleshoot-conditional-access-what-if.md)

[Pianificazione di una distribuzione di Azure Multi-Factor Authentication basata sul cloud](../authentication/howto-mfa-getstarted.md)

[Gestione della conformità dei dispositivi con IntuneManaging device compliance with Intune](/intune/device-compliance-get-started)

[Sicurezza delle app Microsoft Cloud e accesso condizionale](/cloud-app-security/proxy-intro-aad)
