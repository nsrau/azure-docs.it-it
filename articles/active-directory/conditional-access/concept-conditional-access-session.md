---
title: Controlli della sessione nei criteri di accesso condizionale-Azure Active Directory
description: Che cosa sono i controlli di sessione in un Azure AD criteri di accesso condizionale
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
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671903"
---
# <a name="conditional-access-session"></a>Accesso condizionale: sessione

All'interno di un criterio di accesso condizionale, un amministratore può usare i controlli della sessione per consentire esperienze limitate all'interno di specifiche applicazioni cloud.

![Criteri di accesso condizionale con un controllo di concessione che richiede l'autenticazione a più fattori](./media/concept-conditional-access-session/conditional-access-session.png)

## <a name="application-enforced-restrictions"></a>Restrizioni applicate dall'applicazione

Le organizzazioni possono usare questo controllo per richiedere Azure AD passare le informazioni sul dispositivo alle app Cloud selezionate. Le informazioni sul dispositivo consentono alle app cloud di sapere se una connessione viene avviata da un dispositivo conforme o aggiunto al dominio. Questo controllo supporta solo SharePoint Online ed Exchange Online come app cloud selezionate. Una volta selezionata, l'app cloud usa le informazioni sul dispositivo per offrire agli utenti un'esperienza completa o limitata, a seconda dello stato del dispositivo.

Per altre informazioni sull'uso e la configurazione delle restrizioni imposte dall'app, vedere gli articoli seguenti:

- [Abilitazione dell'accesso limitato con SharePoint Online](/sharepoint/control-access-from-unmanaged-devices)
- [Abilitazione dell'accesso limitato con Exchange Online](https://aka.ms/owalimitedaccess)

## <a name="conditional-access-application-control"></a>Controllo delle applicazioni con accesso condizionale

Controllo app per l'accesso condizionale usa un'architettura del proxy inverso ed è integrata in modo univoco con Azure AD l'accesso condizionale. Accesso condizionale di Azure AD consente di applicare i controlli di accesso sulle app dell'organizzazione in base a determinate condizioni. Le condizioni definiscono chi (utente o gruppo di utenti), cosa (quali app cloud) e dove (a quali posizioni e reti) viene applicato un criterio di accesso condizionale. Dopo aver determinato le condizioni, è possibile indirizzare gli utenti a [Microsoft cloud app Security](/cloud-app-security/what-is-cloud-app-security) dove è possibile proteggere i dati con controllo app per l'accesso condizionale applicando i controlli di accesso e di sessione.

Controllo app per l'accesso condizionale consente il monitoraggio e il controllo delle sessioni e dell'accesso alle app utente in tempo reale in base ai criteri di accesso e di sessione. I criteri di accesso e di sessione vengono usati nel portale di Cloud App Security per perfezionare ulteriormente i filtri e impostare le azioni da intraprendere su un utente. Con i criteri di accesso e di sessione è possibile:

- Impedisci exfiltration dati: è possibile bloccare il download, tagliare, copiare e stampare documenti riservati, ad esempio, i dispositivi non gestiti.
- Proteggi al download: anziché bloccare il download di documenti riservati, è possibile richiedere l'etichettatura e la protezione dei documenti con Azure Information Protection. Questa azione garantisce che il documento sia protetto e che l'accesso utente sia limitato in una sessione potenzialmente rischiosa.
- Impedisci il caricamento di file senza etichetta: prima che un file sensibile venga caricato, distribuito e usato da altri utenti, è importante assicurarsi che il file abbia l'etichetta e la protezione corrette. È possibile assicurarsi che i file senza etichetta con contenuto sensibile siano bloccati dal caricamento fino a quando l'utente non classifica il contenuto.
- Monitorare le sessioni utente per la conformità: gli utenti a rischio vengono monitorati quando accedono alle app e le relative azioni vengono registrate dall'interno della sessione. È possibile esaminare e analizzare il comportamento dell'utente per comprendere dove e in quali condizioni devono essere applicati i criteri di sessione in futuro.
- Bloccare l'accesso: è possibile bloccare in dettaglio l'accesso per app e utenti specifici in base a diversi fattori di rischio. Ad esempio, è possibile bloccarle se usano certificati client come un modulo di gestione dei dispositivi.
- Bloccare le attività personalizzate: alcune app hanno scenari univoci che portano a rischi, ad esempio l'invio di messaggi con contenuto sensibile in app come Microsoft teams o Slack. In questi tipi di scenari è possibile analizzare i messaggi per il contenuto sensibile e bloccarli in tempo reale.

Per altre informazioni, vedere l'articolo [distribuire controllo app per l'accesso condizionale per le app in primo piano](/cloud-app-security/proxy-deployment-aad).

## <a name="sign-in-frequency-preview"></a>Frequenza di accesso (anteprima)

Frequenza di accesso consente di definire il periodo di tempo prima che un utente debba accedere di nuovo quando tenta di accedere a una risorsa.

L'impostazione della frequenza di accesso funziona con le app che hanno implementato i protocolli OAUTH2 o OIDC in base agli standard. La maggior parte delle app Microsoft native per Windows, Mac e dispositivi mobili, incluse le applicazioni Web seguenti sono conformi all'impostazione.

- Word, Excel, PowerPoint online
- OneNote online
- Office.com
- Portale di amministrazione di O365
- Exchange Online
- SharePoint e OneDrive
- Client Web Teams
- Dynamics CRM Online
- Portale di Azure

Per altre informazioni, vedere l'articolo [configurare la gestione delle sessioni di autenticazione con l'accesso condizionale](howto-conditional-access-session-lifetime.md#user-sign-in-frequency).

## <a name="persistent-browser-session-preview"></a>Sessione del browser persistente (anteprima)

Una sessione del browser persistente consente agli utenti di rimanere connessi dopo la chiusura e la riapertura della finestra del browser.

Per altre informazioni, vedere l'articolo [configurare la gestione delle sessioni di autenticazione con l'accesso condizionale](howto-conditional-access-session-lifetime.md#persistence-of-browsing-sessions).

## <a name="next-steps"></a>Passaggi successivi

- [Criteri comuni di accesso condizionale](concept-conditional-access-policy-common.md)

- [Modalità solo report](concept-conditional-access-report-only.md)
