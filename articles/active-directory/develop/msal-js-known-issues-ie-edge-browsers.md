---
title: Problemi noti in browser (Microsoft Authentication Library per JavaScript) | Azure
description: Informazioni su problemi noti quando si usa Microsoft Authentication Library per JavaScript (msal) con i browser Microsoft Edge e Internet Explorer.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c57ed956ec50c8bac26720a27894c07353928336
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65873901"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-with-msaljs"></a>Problemi noti nei browser Microsoft Edge e Internet Explorer con msal. js

## <a name="issues-due-to-security-zones"></a>Problemi a causa di aree di sicurezza
Avevamo più report di problemi con l'autenticazione in Internet Explorer e Microsoft Edge (dopo l'aggiornamento del *versione del browser Microsoft Edge per 40.15063.0.0*). Microsoft sta tenendo traccia di queste e avere informato il team di Microsoft Edge. Mentre Microsoft Edge funziona in una soluzione, ecco una descrizione dei problemi che si verificano spesso e le possibili soluzioni alternative che possono essere implementate.

### <a name="cause"></a>Causa
La causa per la maggior parte di questi problemi è come indicato di seguito. La memorizzazione della sessione e l'archiviazione locale vengono partizionati per le aree di sicurezza nel browser Microsoft Edge. In questa versione specifica di Microsoft Edge, quando l'applicazione viene reindirizzata tra più aree, la memorizzazione della sessione e l'archiviazione locale vengono cancellate. In particolare, lo spazio di archiviazione di sessione viene cancellata la navigazione tramite browser regolare e la sessione e l'archiviazione locale vengono cancellati in modalità InPrivate del browser. Msal. js Salva determinato stato nell'archivio di sessione e si basa sul controllo questo stato durante i flussi di autenticazione. Quando lo spazio di archiviazione di sessione viene deselezionata, questo stato viene perso e comporterà quindi esperienze interrotte.

### <a name="issues"></a>Problemi

- **Ricarica i cicli di reindirizzamento infinito e pagina durante l'autenticazione**. Quando gli utenti accedono all'applicazione in Microsoft Edge, essi vengono reindirizzati nuovamente dalla pagina di accesso AAD e sono bloccati in un ciclo di reindirizzamento infinito conseguente ricaricamenti pagina ripetuti. Questo è generalmente seguito da un `invalid_state` errore nell'archivio di sessione.

- **Infinito acquisire token cicli ed errore AADSTS50058**. Quando un'applicazione in esecuzione in Microsoft Edge prova ad acquisire un token per una risorsa, l'applicazione potrebbe rimanere bloccata in un ciclo infinito di acquisizione token chiamata sia il seguente errore da Azure Active Directory della traccia di rete:

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **Finestra popup non comporta la chiusura o è bloccata quando si usano account di accesso tramite Popup per l'autenticazione**. Durante l'autenticazione tramite la finestra popup in Microsoft Edge o IE(InPrivate), dopo aver immesso le credenziali e accesso, se sono coinvolti più domini tra più aree di sicurezza nel riquadro di spostamento non chiude la finestra popup perché msal. js perde l'handle a la finestra popup.  

    Ecco i collegamenti a questi problemi in issue tracker Microsoft Edge:  
    - [Bug 13861050](https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/13861050/)
    - [Bug 13861663](https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/13861663/)

### <a name="update-fix-available-in-msaljs-023"></a>Aggiornamento: Correzione disponibile in msal. js 0.2.3
Correzioni per i problemi di ciclo di reindirizzamento di autenticazione rilasciati negli [msal. js 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases). Abilitare il flag `storeAuthStateInCookie` nel file di configurazione di msal. js per sfruttare i vantaggi di questa correzione. Per impostazione predefinita, questo flag è impostato su false.

Quando il `storeAuthStateInCookie` flag è abilitato, i cookie del browser userà msal. js per archiviare lo stato della richiesta necessario per la convalida dei flussi di autenticazione.

> [!NOTE]
> Questa correzione non è ancora disponibile per i wrapper msal angular e msal angularjs. Questa correzione risolve il problema con le finestre Popup.

Usare le seguenti soluzioni alternative.

#### <a name="other-workarounds"></a>Altre soluzioni alternative
Assicurarsi di verificare che il problema è in corso solo nella versione specifica del browser Microsoft Edge e funziona su altri browser prima di adottare queste soluzioni alternative.  
1. Come primo passaggio per evitare questi problemi, assicurarsi che il dominio dell'applicazione, e ad altri siti interessati i reindirizzamenti del flusso di autenticazione vengono aggiunti come siti attendibili nelle impostazioni di sicurezza del browser, in modo che appartengono alla stessa area di sicurezza.
A questo scopo, attenersi alla procedura seguente:
    - Aprire **Internet Explorer** e fare clic sui **impostazioni** (icona a forma di ingranaggio) nell'angolo in alto a destra
    - Selezionare **Opzioni Internet**
    - Selezionare il **sicurezza** scheda
    - Sotto il **dei siti attendibili** opzione, fare clic sul **siti** pulsante e aggiungere gli URL nella finestra di dialogo visualizzata.

2. Come accennato in precedenza, poiché solo la sessione di archiviazione viene cancellata durante la navigazione normale, è possibile configurare msal. js per usare invece l'archiviazione locale. Può essere impostato come il `cacheLocation` parametro per la configurazione durante l'inizializzazione di MSAL.

Si noti che questa operazione non risolverà il problema relativo a InPrivate browsing poiché vengono cancellate sia sessione e l'archiviazione locale.

## <a name="issues-due-to-popup-blockers"></a>Problemi causati da blocchi popup

Vi sono casi quando popup bloccati in Internet Explorer o Microsoft Edge, ad esempio quando una finestra popup di secondo si verifica durante l'autenticazione a più fattori. Si riceverà una notifica nel browser per consentire i popup una sola volta o sempre. Se si sceglie di consentire o meno, il browser si apre automaticamente la finestra popup e restituisce un `null` gestire appositamente. Di conseguenza, la libreria non è un handle per la finestra e non è possibile chiudere la finestra popup. In Chrome, lo stesso problema non viene eseguito quando viene richiesto di consentire i popup perché non si apra automaticamente una finestra popup.

Come un **soluzione alternativa**, gli sviluppatori devono consentire i popup in Internet Explorer e Microsoft Edge prima che inizino con le app per evitare questo problema.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sulle [usando msal. js in Internet Explorer](msal-js-use-ie-browser.md).
