---
title: Problemi di Internet Explorer r Microsoft Edge (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Informazioni sui problemi che si verificano quando si usa Microsoft Authentication Library per JavaScript (MSAL.js) con i browser Internet Explorer e Microsoft Edge.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/18/2020
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 2a471504b88791b5bfb6ce6cc7c81d60bfbe5028
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83772081"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Problemi noti dei browser Internet Explorer e Microsoft Edge (MSAL.js)

## <a name="issues-due-to-security-zones"></a>Problemi causati da aree di sicurezza
Sono stati rilevati più report di problemi con l'autenticazione in Internet Explorer e Microsoft Edge (dall'aggiornamento del *browser Microsoft Edge alla versione 40.15063.0.0*). Stiamo monitorando questi problemi e abbiamo informato il team Microsoft Edge. Sebbene Microsoft Edge lavori su una soluzione, ecco una descrizione dei problemi che si verificano spesso e delle possibili soluzioni alternative che è possibile implementare.

### <a name="cause"></a>Causa
La causa della maggior parte di questi problemi è la seguente. L'archiviazione della sessione e l'archiviazione locale vengono partizionate in base alle aree di sicurezza nel browser Microsoft Edge. In questa particolare versione di Microsoft Edge, quando l'applicazione viene reindirizzata tra le zone, l'archiviazione della sessione e l'archiviazione locale vengono cancellate. In particolare, l'archiviazione della sessione viene cancellata nella normale navigazione del browser e l'archiviazione della sessione e l'archiviazione locale vengono cancellate nella modalità InPrivate del browser. MSAL.js salva un determinato stato nell'archiviazione della sessione e si basa sul controllo di questo stato durante i flussi di autenticazione. Quando l'archiviazione della sessione viene deselezionata, questo stato viene perso e pertanto comporta l'interruzione delle esperienze.

### <a name="issues"></a>Problemi

- **Cicli di reindirizzamento infinito e ricaricamenti di pagine durante l'autenticazione**. Quando gli utenti accedono all'applicazione in Microsoft Edge, vengono reindirizzati dalla pagina di accesso di AAD e sono bloccati in un ciclo di reindirizzamento infinito che comporta ripetuti ricaricamenti delle pagine. Questo è in genere accompagnato da un errore di `invalid_state` nell'archiviazione della sessione.

- **Cicli di acquisizione del token infiniti ed errore AADSTS50058**. Quando un'applicazione in esecuzione su Microsoft Edge tenta di acquisire un token per una risorsa, l'applicazione può rimanere bloccata in un ciclo infinito della chiamata del token di acquisizione insieme all'errore seguente di AAD nella traccia di rete:

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **La finestra popup non si chiude o si blocca quando si usa l'accesso tramite popup per l'autenticazione**. Quando si esegue l'autenticazione tramite la finestra popup in Microsoft Edge o Internet Explorer (InPrivate), dopo aver immesso le credenziali e aver eseguito l'accesso, se nello spostamento sono interessati più domini tra aree di sicurezza, la finestra popup non si chiude perché MSAL.js perde l'handle per la finestra popup.  

### <a name="update-fix-available-in-msaljs-023"></a>Aggiornamento: Aggiornamento disponibile in MSAL.js 0.2.3
Le correzioni per i problemi del ciclo di reindirizzamento dell'autenticazione sono state rilasciate in [MSAL.js 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases). Abilitare il flag `storeAuthStateInCookie` nella configurazione MSAL.js per sfruttare i vantaggi di questo aggiornamento. Per impostazione predefinita, questo flag è impostato su False.

Quando il flag `storeAuthStateInCookie` è abilitato, MSAL.js userà i cookie del browser per archiviare lo stato della richiesta necessario per la convalida dei flussi di autenticazione.

> [!NOTE]
> Questo aggiornamento non è ancora disponibile per i wrapper msal-angular e msal-angularjs. Questa correzione non risolve il problema relativo alle finestre popup.

Usare le soluzioni alternative seguenti.

#### <a name="other-workarounds"></a>Altre soluzioni alternative
Verificare che il problema si verifichi solo nella versione specifica del browser Microsoft Edge e funzioni negli altri browser prima di adottare queste soluzioni alternative.  
1. Come primo passaggio per aggirare questi problemi, verificare che il dominio dell'applicazione e gli eventuali altri siti interessati nei reindirizzamenti del flusso di autenticazione vengano aggiunti come siti attendibili nelle impostazioni di sicurezza del browser, in modo che appartengano alla stessa area di sicurezza.
A questo scopo, attenersi alla procedura seguente:
    - Aprire **Internet Explorer** e fare clic su **Impostazioni** (icona a forma di ingranaggio) nell'angolo superiore destro
    - Selezionare **Opzioni Internet**
    - Fare clic sulla scheda **Sicurezza**
    - Nell'opzione **Siti attendibili** fare clic sul pulsante **Siti** e aggiungere gli URL nella finestra di dialogo visualizzata.

2. Come indicato in precedenza, poiché solo l'archiviazione della sessione viene cancellata durante la normale navigazione, è possibile configurare MSAL.js per usare invece l'archiviazione locale. Questa impostazione può essere impostata come parametro di configurazione `cacheLocation` durante l'inizializzazione di MSAL.

Si noti che questo non risolverà il problema della navigazione InPrivate, perché l'archiviazione della sessione e l'archiviazione locale vengono cancellate.

## <a name="issues-due-to-popup-blockers"></a>Problemi causati da blocchi popup

In alcuni casi, i popup vengono bloccati in Internet Explorer o Microsoft Edge, ad esempio quando si verifica un secondo popup durante l'[autenticazione a più fattori](../authentication/concept-mfa-howitworks.md). Si riceverà un avviso nel browser per consentire il popup una volta o sempre. Se si sceglie di dare il consenso, il browser apre automaticamente la finestra popup e restituisce un handle `null`. Di conseguenza, la libreria non dispone di un handle per la finestra e non è possibile chiudere la finestra popup. Lo stesso problema non si verifica in Chrome quando viene richiesto di consentire i popup perché non apre automaticamente una finestra popup.

Come **soluzione**, gli sviluppatori dovranno consentire i popup in Internet Explorer e Microsoft Edge prima di iniziare a usare l'app per evitare questo problema.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su come [Usare MSAL.js in Internet Explorer](msal-js-use-ie-browser.md).
