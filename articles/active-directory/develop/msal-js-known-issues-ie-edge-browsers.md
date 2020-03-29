---
title: Problemi in Internet Explorer & Microsoft Edge (MSAL.js) Azure
titleSuffix: Microsoft identity platform
description: Informazioni sui problemi relativi a Microsoft Authentication Library for JavaScript (MSAL.js) con internet Explorer e Microsoft Edge browser.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 5ae2dee68ec0da8e8a00d4f01583461462bc196c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696096"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Problemi noti nei browser Internet Explorer e Microsoft Edge (MSAL.js)

## <a name="issues-due-to-security-zones"></a>Problemi dovuti alle aree di sicurezza
Sono stati rilevati più rapporti di problemi con l'autenticazione in Internet Explorer e Microsoft Edge (dall'aggiornamento della versione del *browser Microsoft Edge a 40.15063.0.0*). Stiamo monitorando questi e abbiamo informato il team di Microsoft Edge. Mentre Microsoft Edge lavora su una risoluzione, ecco una descrizione dei problemi che si verificano di frequente e le possibili soluzioni alternative che possono essere implementate.

### <a name="cause"></a>Causa
La causa della maggior parte di questi problemi è la seguente. L'archiviazione della sessione e l'archiviazione locale sono partizionati dalle aree di sicurezza nel browser Microsoft Edge. In questa particolare versione di Microsoft Edge, quando l'applicazione viene reindirizzata tra aree, l'archiviazione della sessione e l'archiviazione locale vengono cancellati. In particolare, l'archiviazione della sessione viene cancellata nella normale navigazione del browser e sia la sessione che l'archiviazione locale vengono cancellate nella modalità InPrivate del browser. MSAL.js salva determinati stati nell'archivio della sessione e si basa sul controllo di questo stato durante i flussi di autenticazione. Quando l'archiviazione della sessione viene cancellata, questo stato viene perso e pertanto si verificano esperienze interrotte.

### <a name="issues"></a>Problemi

- **Cicli di reindirizzamento infiniti e ricaricamenti delle pagine durante l'autenticazione.** Quando gli utenti accedono all'applicazione in Microsoft Edge, vengono reindirizzati dalla pagina di accesso a AAD e sono bloccati in un ciclo di reindirizzamento infinito con conseguente ricaricamento ripetuto della pagina. Questo è in genere `invalid_state` accompagnato da un errore nell'archivio della sessione.

- Infiniti cicli di token di **acquisizione e errore AADSTS50058**. Quando un'applicazione in esecuzione su Microsoft Edge tenta di acquisire un token per una risorsa, l'applicazione potrebbe rimanere bloccato in un ciclo infinito della chiamata di token di acquisizione con il seguente errore da AAD nella traccia di rete:

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- Finestra popup non si chiude o è bloccato quando si **utilizza l'accesso tramite Popup per autenticare**. Durante l'autenticazione tramite finestra popup in Microsoft Edge o IE (InPrivate), dopo aver immesso le credenziali e effettuato l'accesso, se più domini tra le aree di protezione sono coinvolti nella navigazione, la finestra popup non si chiude perché MSAL.js perde l'handle per nella finestra popup.  

### <a name="update-fix-available-in-msaljs-023"></a>Aggiornamento: Correzione disponibile in MSAL.js 0.2.3
Le correzioni per i problemi del ciclo di reindirizzamento dell'autenticazione sono state rilasciate in [MSAL.js 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases). Abilitare `storeAuthStateInCookie` il flag nella configurazione di MSAL.js per sfruttare questa correzione. Per impostazione predefinita, questo flag è impostato su false.

Quando `storeAuthStateInCookie` il flag è abilitato, MSAL.js utilizzerà i cookie del browser per archiviare lo stato di richiesta necessario per la convalida dei flussi di autenticazione.

> [!NOTE]
> Questa correzione non è ancora disponibile per i wrapper msal-angolari e msal-angolarjs. Questa correzione non risolve il problema con le finestre Popup.

Utilizzare le soluzioni alternative riportate di seguito.

#### <a name="other-workarounds"></a>Altre soluzioni alternative
Assicurarsi di verificare che il problema si verifichi solo sulla versione specifica del browser Microsoft Edge e funziona sugli altri browser prima di adottare queste soluzioni alternative.  
1. Come primo passaggio per risolvere questi problemi, assicurarsi che il dominio applicazione, e tutti gli altri siti coinvolti nei reindirizzamenti del flusso di autenticazione, vengano aggiunti come siti attendibili nelle impostazioni di sicurezza del browser, in modo che appartengano alla stessa area di sicurezza.
A questo scopo, attenersi alla procedura seguente:
    - Apri **Internet Explorer** e fai clic sulle **impostazioni** (icona a forma di ingranaggio) nell'angolo in alto a destra
    - Selezionare **Opzioni Internet**
    - Selezionare la scheda **Sicurezza**
    - Sotto l'opzione **Siti attendibili,** fare clic sul pulsante **Siti** e aggiungere gli URL nella finestra di dialogo visualizzata.

2. Come accennato in precedenza, poiché solo l'archiviazione della sessione viene cancellata durante la normale navigazione, è possibile configurare MSAL.js per utilizzare l'archiviazione locale. Questo può essere `cacheLocation` impostato come parametro config durante l'inizializzazione di MSAL.

Si noti che questo non risolverà il problema per InPrivate Browsing poiché sia la sessione che l'archiviazione locale vengono cancellate.

## <a name="issues-due-to-popup-blockers"></a>Problemi dovuti a blocchi popup

In alcuni casi i popup vengono bloccati in IE o Microsoft Edge, ad esempio quando si verifica un secondo popup durante l'autenticazione a più fattori. Riceverai un avviso nel browser per consentire il popup una o sempre. Se si sceglie di consentire, il browser apre `null` automaticamente la finestra popup e restituisce un handle per tale posizione. Di conseguenza, la libreria non dispone di un handle per la finestra e non è possibile chiudere la finestra popup. Lo stesso problema non si verifica in Chrome quando ti chiede di consentire i popup perché non apre automaticamente una finestra popup.

Come **soluzione alternativa,** gli sviluppatori dovranno consentire i popup in IE e Microsoft Edge prima di iniziare a utilizzare l'app per evitare questo problema.

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni [sull'utilizzo di MSAL.js in Internet Explorer](msal-js-use-ie-browser.md).
