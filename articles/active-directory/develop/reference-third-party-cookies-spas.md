---
title: Come gestire Intelligent Tracking Protection (ITP) in Safari | Azure
titleSuffix: Microsoft identity platform
description: Autenticazione di app a pagina singola quando i cookie di terze parti non sono più consentiti.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: hirsin
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: 064c9a00e1cd7c139f3f42a053dcf8a5db13f161
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92104581"
---
# <a name="handle-itp-in-safari-and-other-browsers-where-third-party-cookies-are-blocked"></a>Gestire ITP in Safari e in altri browser in cui i cookie di terze parti sono bloccati

Attualmente molti browser bloccano i cookie di terze parti nelle richieste a domini che non corrispondono a quelli visualizzati nella barra del browser. Questo scenario interrompe il flusso implicito e richiede nuovi modelli di autenticazione per il corretto accesso degli utenti. Microsoft Identity Platform usa il flusso di autorizzazione con PKCE e i token di aggiornamento per mantenere gli utenti connessi quando i cookie di terze parti sono bloccati.

## <a name="what-is-intelligent-tracking-protection-itp"></a>Che cos'è Intelligent Tracking Protection (ITP)?

Apple Safari offre una funzionalità di protezione della privacy abilitata per impostazione predefinita denominata [Intelligent Tracking Protection](https://webkit.org/tracking-prevention-policy/) o *ITP*. ITP blocca i cookie "di terze parti", ovvero i cookie sulle richieste tra domini.

Una forma comune di rilevamento delle azioni utente prevede il caricamento di un iframe in un sito di terze parti in background e l'utilizzo dei cookie per correlare l'utente nella rete Internet. Purtroppo, questo modello rappresenta anche la modalità standard di implementazione del [flusso implicito](v2-oauth2-implicit-grant-flow.md) nelle app a pagina singola. Quando un browser blocca i cookie di terze parti per impedire il rilevamento delle azioni utente, vengono interrotte anche le applicazioni a pagina singola.

Safari non è l'unico browser che blocca i cookie di terze parti per tutelare la privacy degli utenti. Brave blocca i cookie di terze parti per impostazione predefinita, mentre Chromium (la piattaforma su cui sono basati Google Chrome e Microsoft Edge) ha annunciato che in futuro non saranno più supportati i cookie di terze parti.

La soluzione descritta in questo articolo funziona in tutti questi browser o ovunque i cookie di terze parti siano bloccati.

## <a name="overview-of-the-solution"></a>Panoramica della soluzione

Per continuare ad autenticare gli utenti nelle applicazioni a pagina singola, gli sviluppatori di app devono usare il [flusso del codice di autorizzazione](v2-oauth2-auth-code-flow.md). Nel flusso del codice di autorizzazione, il provider di identità rilascia un codice e l'applicazione a pagina singola riscatta il codice per un token di accesso e un token di aggiornamento. Quando l'app richiede token aggiuntivi, può usare il [flusso del token di aggiornamento](v2-oauth2-auth-code-flow.md#refresh-the-access-token) per ottenere i nuovi token. MSAL.js 2.0, la libreria di Microsoft Identity Platform per le applicazioni a pagina singola, implementa il flusso del codice di autorizzazione per le applicazioni a pagina singola e, con aggiornamenti minimi, rappresenta una sostituzione per MSAL.js 1.x.

Per Microsoft Identity Platform, le applicazioni a pagina singola e i client nativi seguono indicazioni simili in merito al protocollo:

* Uso di una [richiesta di verifica del codice PKCE](https://tools.ietf.org/html/rfc7636)
    * L'estensione PKCE è *obbligatoria* per le applicazioni a pagina singola in Microsoft Identity Platform. L'estensione PKCE è *consigliata* per client nativi e riservati.
* Nessun utilizzo di un segreto client

Le applicazioni a pagina singola prevedono due restrizioni aggiuntive:

* [L'URI di reindirizzamento deve essere contrassegnato come tipo `spa`](v2-oauth2-auth-code-flow.md#redirect-uri-setup-required-for-single-page-apps) per abilitare CORS sugli endpoint di accesso.
* I token di aggiornamento rilasciati tramite il flusso del codice di autorizzazione agli URI di reindirizzamento `spa` prevedono una durata di 24 ore anziché una durata di 90 giorni.

:::image type="content" source="media/v2-oauth-auth-code-spa/active-directory-oauth-code-spa.svg" alt-text="Diagramma che mostra il flusso del codice di autorizzazione OAuth 2 tra un'app a singola pagina e l'endpoint del servizio token di sicurezza." border="false":::

## <a name="performance-and-ux-implications"></a>Prestazioni e implicazioni per l'esperienza utente

Alcune applicazioni che usano il flusso implicito tentano l'accesso senza reindirizzamento aprendo un iframe di accesso con `prompt=none`. Nella maggior parte dei browser questa richiesta risponderà con i token per l'utente attualmente connesso (presupponendo che sia già stato concesso il consenso). Questo modello significa che le applicazioni non richiedono un reindirizzamento di pagina completo per l'accesso dell'utente, migliorando così le prestazioni e l'esperienza utente, in quanto l'utente visita la pagina Web ed è già connesso. Poiché `prompt=none` in un iframe non è più un'opzione quando i cookie di terze parti sono bloccati, le applicazioni devono visitare la pagina di accesso in un frame di primo livello per ottenere un codice di autorizzazione.

Esistono due modi per eseguire l'accesso:

* **Reindirizzamenti di pagina completi**
    * Al primo caricamento dell'applicazione a pagina singola, l'utente viene reindirizzato alla pagina di accesso, se non esiste già una sessione o se la sessione è scaduta. Il browser dell'utente visita la pagina di accesso, presenta i cookie che contengono la sessione utente e quindi reindirizza l'utente all'applicazione con il codice e i token in un frammento.
    * Durante questa procedura di reindirizzamento, l'applicazione a pagina singola viene caricata due volte. Seguire le procedure consigliate per la memorizzazione nella cache delle applicazioni a pagina singola, per non scaricare completamente l'app due volte.
    * È consigliabile definire nell'app una sequenza di precaricamento che verifichi la presenza di una sessione di accesso e reindirizzi alla pagina di accesso prima che l'app venga decompressa completamente ed esegua il payload JavaScript.
* **Popup**
    * Se l'esperienza utente di un reindirizzamento di pagina completo non funziona per l'applicazione, è consigliabile usare un popup per gestire l'autenticazione.
    * Quando il popup termina il reindirizzamento all'applicazione dopo l'autenticazione, il codice nel gestore di reindirizzamento archivia il codice e i token nell'archivio locale per consentire all'applicazione di usarli. MSAL.js supporta i popup per l'autenticazione, così come la maggior parte delle librerie.
    * I browser stanno riducendo il supporto per i popup, quindi potrebbero non rappresentare l'opzione più affidabile. Per soddisfare i requisiti del browser, potrebbe essere necessaria l'interazione dell'utente con l'applicazione a pagina singola prima di creare il popup.

>[!NOTE]
> Apple [descrive un metodo popup](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/) come correzione di compatibilità temporanea per concedere alla finestra originale l'accesso ai cookie di terze parti. Sebbene Apple possa rimuovere questo trasferimento di autorizzazioni in futuro, questo non avrà alcun effetto sulle indicazioni fornite in questo documento. In questo caso, il popup viene usato come finestra di passaggio alla pagina di accesso, per trovare una sessione e consentire il rilascio di un codice di autorizzazione. Questa soluzione dovrebbe continuare a funzionare anche in futuro.

### <a name="a-note-on-iframe-apps"></a>Nota sulle app iframe

Un modello comune nelle app Web consiste nell'usare un iframe per incorporare un'app all'interno di un'altra. Il frame di primo livello gestisce l'autenticazione dell'utente e l'applicazione ospitata nell'iframe può considerare attendibile l'accesso dell'utente, recuperando i token in modo invisibile all'utente tramite il flusso implicito. L'acquisizione di token invisibile all'utente non funziona più quando i cookie di terze parti sono bloccati. L'applicazione incorporata nell'iframe deve passare all'uso dei popup per accedere alla sessione dell'utente, perché non può passare alla pagina di accesso.

## <a name="security-implications-of-refresh-tokens-in-the-browser"></a>Implicazioni per la sicurezza dei token di aggiornamento nel browser

Il rilascio dei token di aggiornamento al browser è considerato un problema di sicurezza. Gli attacchi di tipo cross-site scripting XSS (cross-site scripting) o i pacchetti JS compromessi possono rubare il token di aggiornamento e usarlo in modalità remota fino alla scadenza o alla revoca. Per ridurre al minimo il rischio di furto di token di aggiornamento, alle applicazioni a pagina singola verranno rilasciati token validi solo 24 ore. Dopo 24 ore, l'app deve acquisire un nuovo codice di autorizzazione tramite una visita del frame di primo livello alla pagina di accesso.

Questo modello di token di aggiornamento a durata limitata è stato scelto come compromesso tra la sicurezza e un'esperienza utente con prestazioni non ottimali. Senza i token di aggiornamento o i cookie di terze parti, il flusso del codice di autorizzazione (come consigliato dalla [bozza di procedure consigliate per la sicurezza di OAuth](https://tools.ietf.org/html/draft-ietf-oauth-security-topics-14)) diventa oneroso quando sono necessari token nuovi o aggiuntivi. È necessario un reindirizzamento di pagina completo o un popup per ogni singolo token, ogni volta che un token scade (di norma ogni ora per i token di Microsoft Identity Platform).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul [flusso del codice di autorizzazione](v2-oauth2-auth-code-flow.md).

Provare il flusso del codice di autorizzazione con la guida di [avvio rapido per MSAL.js 2.0](quickstart-v2-javascript-auth-code.md).
