---
title: Controlli della sessione nei criteri di accesso condizionale - Azure Active DirectorySession controls in Conditional Access policy - Azure Active Directory
description: Che cosa sono i controlli di sessione in un criterio di accesso condizionale di Azure ADWhat are session controls in an Azure AD Conditional Access policy
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
ms.openlocfilehash: e99b9b87f939d614679fdecf24c9d36d99bf2938
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671903"
---
# <a name="conditional-access-session"></a>Accesso condizionale: Sessione

All'interno di un criterio di accesso condizionale, un amministratore può utilizzare i controlli di sessione per abilitare esperienze limitate all'interno di applicazioni cloud specifiche.

![Criteri di accesso condizionale con un controllo di concessione che richiede l'autenticazione a più fattoriConditional Access policy with a grant control requiring multi-factor authentication](./media/concept-conditional-access-session/conditional-access-session.png)

## <a name="application-enforced-restrictions"></a>Restrizioni imposte dall'applicazione

Le organizzazioni possono usare questo controllo per richiedere ad Azure AD di passare le informazioni sul dispositivo alle app cloud selezionate. Le informazioni sul dispositivo consentono alle app cloud di sapere se una connessione viene avviata da un dispositivo conforme o aggiunto al dominio. Questo controllo supporta solo SharePoint Online ed Exchange Online come app cloud selezionate. Una volta selezionata, l'app cloud usa le informazioni sul dispositivo per offrire agli utenti un'esperienza completa o limitata, a seconda dello stato del dispositivo.

Per altre informazioni sull'uso e la configurazione delle restrizioni applicate dall'app, vedere gli articoli seguenti:For more information on the use and configuration of app enforced restrictions, see the following articles:

- [Abilitazione dell'accesso limitato con SharePoint Online](/sharepoint/control-access-from-unmanaged-devices)
- [Abilitazione dell'accesso limitato con Exchange Online](https://aka.ms/owalimitedaccess)

## <a name="conditional-access-application-control"></a>Controllo dell'applicazione con accesso condizionaleConditional Access application control

Il controllo delle app con accesso condizionale usa un'architettura proxy inverso ed è integrato in modo univoco con l'accesso condizionale di Azure AD. Accesso condizionale di Azure AD consente di applicare i controlli di accesso sulle app dell'organizzazione in base a determinate condizioni. Le condizioni definiscono chi (utente o gruppo di utenti), cosa (quali app cloud) e dove (a quali posizioni e reti) viene applicato un criterio di accesso condizionale. Dopo aver determinato le condizioni, è possibile instradare gli utenti a [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) dove è possibile proteggere i dati con il controllo delle app con accesso condizionale applicando i controlli di accesso e sessione.

Tramite il controllo delle app con l'accesso condizionale, è possibile monitorare e controllare l'accesso e le sessioni delle app dell'utente in tempo reale, in base ai criteri di accesso e di sessione. I criteri di accesso e di sessione vengono usati nel portale di Cloud App Security per definire in modo più preciso i filtri e impostare azioni da applicare ai singoli utenti. Con i criteri di accesso e di sessione è possibile eseguire le operazioni seguenti:

- Impedire l'esfiltrazione dei dati: è possibile bloccare il download, tagliare, copiare e stampare documenti sensibili su, ad esempio, dispositivi non gestiti.
- Proteggi al download: anziché bloccare il download di documenti sensibili, è possibile richiedere l'etichettatura e la protezione dei documenti con Azure Information Protection.Protect on download: Instead of blocking the download of sensitive documents, you can require documents to be labeled and protected with Azure Information Protection. Questa azione garantisce la protezione del documento e l'accesso dell'utente è limitato in una sessione potenzialmente rischiosa.
- Impedisci il caricamento di file senza etichetta: prima che un file sensibile venga caricato, distribuito e utilizzato da altri utenti, è importante assicurarsi che il file disponga dell'etichetta e della protezione corrette. Puoi assicurarti che i file senza etichetta con contenuto riservato vengano bloccati fino a quando l'utente non classifica il contenuto.
- Monitorare le sessioni utente per la conformità: gli utenti rischiosi vengono monitorati quando accedono alle app e le loro azioni vengono registrate dall'interno della sessione. È possibile esaminare e analizzare il comportamento degli utenti per comprendere dove e in quali condizioni applicare i criteri di sessione in futuro.
- Blocca l'accesso: puoi bloccare in modo granulare l'accesso per app e utenti specifici in base a diversi fattori di rischio. Ad esempio, è possibile bloccarli se utilizzano certificati client come forma di gestione dei dispositivi.
- Bloccare le attività personalizzate: alcune app hanno scenari univoci che comportano rischi, ad esempio l'invio di messaggi con contenuto riservato in app come Microsoft Teams o Slack.Block custom activities: Some apps have unique scenarios that carry risk, for example, sending messages with sensitive content in apps like Microsoft Teams or Slack. In questi tipi di scenari, è possibile eseguire la scansione dei messaggi per i contenuti sensibili e bloccarli in tempo reale.

Per altre informazioni, vedere l'articolo Distribuire il [controllo delle app con accesso condizionale per le app in primo piano](/cloud-app-security/proxy-deployment-aad).

## <a name="sign-in-frequency-preview"></a>Frequenza di accesso (anteprima)Sign-In frequency (Preview)

La frequenza di accesso definisce il periodo di tempo prima che a un utente venga chiesto di accedere di nuovo quando si tenta di accedere a una risorsa.

L'impostazione della frequenza di accesso funziona con le app che hanno implementato protocolli OAUTH2 o OIDC in base agli standard. La maggior parte delle app native Microsoft per Windows, Mac e Mobile, incluse le seguenti applicazioni Web, è conforme all'impostazione.

- Word, Excel, PowerPoint Online
- OneNote Online
- Office.com
- Portale di amministrazione di Office 365
- Exchange Online
- SharePoint e OneDrive
- Client Web di Teams
- Dynamics CRM Online
- Portale di Azure

Per ulteriori informazioni, vedere l'articolo Configurare la gestione delle sessioni di [autenticazione con accesso condizionale](howto-conditional-access-session-lifetime.md#user-sign-in-frequency).

## <a name="persistent-browser-session-preview"></a>Sessione del browser permanente (anteprima)

Una sessione del browser permanente consente agli utenti di rimanere connessi dopo la chiusura e la riapertura della finestra del browser.

Per ulteriori informazioni, vedere l'articolo Configurare la gestione delle sessioni di [autenticazione con accesso condizionale](howto-conditional-access-session-lifetime.md#persistence-of-browsing-sessions).

## <a name="next-steps"></a>Passaggi successivi

- [Criteri comuni di Accesso condizionaleConditional Access common policies](concept-conditional-access-policy-common.md)

- [Modalità solo report](concept-conditional-access-report-only.md)
