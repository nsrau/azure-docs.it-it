---
title: Comprendere e risolvere i problemi di Azure AD Application Proxy CORS
description: Consente di conoscere CORS in Azure AD Application Proxy e su come identificare e risolvere i problemi di condivisione CORS.
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: celested
ms.reviewer: japere
ms.openlocfilehash: 2b6adcf4231aa44a4f28d277e963efa16de8af81
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399337"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>Comprendere e risolvere i problemi di Azure Active Directory Application Proxy CORS

[Cross-origin resource sharing (CORS)](http://www.w3.org/TR/cors/) può talvolta costituire un problema per le App e le API pubblicata tramite il Proxy di applicazione Azure Active Directory. Questo articolo illustra i problemi di Azure AD Application Proxy CORS e le soluzioni.

Protezione del browser impedisce in genere una pagina web di creare richieste AJAX a un altro dominio. Questa restrizione viene chiamata il *criterio della stessa origine*e impedisce a un sito dannoso di leggere dati sensibili da un altro sito. Tuttavia, in alcuni casi si potrebbe voler consentire ad altri siti di chiamare l'API web. CORS è uno standard W3C che consente a un server di ridurre i criteri di corrispondenza dell'origine e consentire alcune richieste multiorigine e rifiutarne altre.

## <a name="understand-and-identify-cors-issues"></a>Comprendere e identificare i problemi CORS

Due URL abbia la stessa origine se dispongono di porte, host e gli schemi identici ([RFC 6454](https://tools.ietf.org/html/rfc6454)), ad esempio:

-   http:\//contoso.com/foo.html
-   http:\//contoso.com/bar.html

Gli URL seguenti sono le entità Origin diversa rispetto al precedente due:

-   http:\//contoso.net - Different domain
-   http:\//contoso.com:9000/foo.html - porta diversa
-   https:\//contoso.com/foo.html - schema differente
-   http:\//www.contoso.com/foo.html - Different subdomain

Criterio della stessa origine impedisce alle app di accedere alle risorse da altre origini, a meno che non usano le intestazioni di controllo di accesso corretti. Se le intestazioni CORS sono assenti o non corrette, le richieste multiorigine esito negativo. 

È possibile identificare i problemi CORS con gli strumenti di debug del browser:

1. Avviare il browser e passare all'app web.
1. Premere **F12** per visualizzare la console di debug.
1. Provare a riprodurre la transazione ed esaminare il messaggio della console. Una violazione di condivisione CORS produce un errore di console relative all'origine.

Nello screenshot seguente, selezionando il **Provalo** pulsante ha causato un messaggio di errore CORS tale https:\//corswebclient-contoso.msappproxy.net non è stato trovato nell'intestazione Access-Control-Allow-Origin.

![Problema di CORS](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>Sfide CORS con Proxy dell'applicazione

Nell'esempio seguente viene illustrato un tipico scenario di Azure AD Application Proxy CORS. Gli host interno del server una **CORSWebService** controller API web e una **CORSWebClient** che chiama **CORSWebService**. È una richiesta AJAX dal **CORSWebClient** al **CORSWebService**.

![Richiesta di corrispondenza dell'origine in locale](./media/application-proxy-understand-cors-issues/image1.png)

L'app CORSWebClient funziona quando si ospita un'istanza locale, ma non riesce a carico né genera errori quando pubblicate tramite Proxy applicazione Azure AD. Se è stato pubblicato l'App CORSWebClient e CORSWebService separatamente come App differenti tramite il Proxy di applicazione, due App si trovano in domini diversi. Una richiesta AJAX da CORSWebClient a CORSWebService è una richiesta multiorigine e si verifica un errore.

![Richiesta CORS Proxy di applicazione](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>Soluzioni per problemi di Application Proxy CORS

È possibile risolvere il problema CORS precedente in uno dei diversi modi.

### <a name="option-1-set-up-a-custom-domain"></a>Opzione 1: Configurare un dominio personalizzato

Usare un Proxy di applicazione Azure AD [dominio personalizzato](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) pubblicare dall'origine stessa, senza dover apportare modifiche per le origini di app, codice o le intestazioni. 

### <a name="option-2-publish-the-parent-directory"></a>Opzione 2: Pubblicare la directory padre

Pubblicare la directory padre di entrambe le app. Questa soluzione funziona particolarmente bene se si hanno solo due App sul server web. Anziché pubblicare separatamente ogni app, è possibile pubblicare la directory padre comune, che comporta la stessa origine.

Gli esempi seguenti illustrano il pagina di Azure AD Application Proxy per l'app CORSWebClient portale.  Quando la **URL interno** è impostata su *contoso.com/CORSWebClient*, l'app non può effettuare richieste riuscite per il *contoso.com/CORSWebService* directory, perché si tratta di cross-origin. 

![Pubblicare app singolarmente](./media/application-proxy-understand-cors-issues/image4.png)

Al contrario, impostare il **URL interno** per pubblicare la directory padre, che include sia la *CORSWebClient* e *CORSWebService* directory:

![Directory padre di pubblicazione](./media/application-proxy-understand-cors-issues/image5.png)

Gli URL app risultante risolvano efficacemente il problema CORS:

- https:\//corswebclient-contoso.msappproxy.net/CORSWebService
- https:\//corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>Opzione 3: Aggiornare le intestazioni HTTP

Aggiungere un'intestazione di risposta HTTP personalizzata nel servizio web in base alla richiesta di origine. Per i siti Web in esecuzione in Internet Information Services (IIS), utilizzare Gestione IIS per modificare l'intestazione:

![Aggiungere un'intestazione di risposta personalizzata in Gestione IIS](./media/application-proxy-understand-cors-issues/image6.png)

Questa modifica non richiede modifiche al codice. È possibile verificarlo nelle tracce Fiddler:

**Registra l'aggiunta di intestazione**\
PROTOCOLLO HTTP/1.1 200 OK\
Cache-Control: no-cache\
Pragma: no-cache\
Content-Type: text/plain; charset = utf-8\
Scadenza:-1 \
Variare: Encoding\ accettare
Server:  Microsoft-IIS/8.5 Microsoft-HTTPAPI/2.0\
**Access-Control-Allow-Origin: https://corswebclient-contoso.msappproxy.net** \
X-AspNet-Version: 4.0.30319\
X-Powered-By: ASP.NET\
Lunghezza del contenuto: 17

### <a name="option-4-modify-the-app"></a>Opzione 4: Modifica dell'app

È possibile modificare l'app per il supporto CORS aggiungendo l'intestazione Access-Control-Allow-Origin, con i valori appropriati. La modalità per aggiungere l'intestazione dipende dal linguaggio del codice dell'app. La modifica del codice è l'opzione meno consigliata, perché richiede il massimo impegno.

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>Opzione 5: Estendere la durata del token di accesso

Alcuni problemi di condivisione CORS non possono essere risolti, ad esempio quando l'app reindirizza *login.microsoftonline.com* per l'autenticazione, e il token di accesso scade. La condivisione CORS, chiamare quindi ha esito negativo. Soluzione alternativa per questo scenario consiste nell'estendere la durata del token di accesso, per evitare che scada durante una sessione utente. Per altre informazioni su come eseguire questa operazione, vedere [durata dei token configurabili in Azure AD](../develop/active-directory-configurable-token-lifetimes.md).

## <a name="see-also"></a>Vedere anche
- [Esercitazione: Aggiungere un'applicazione in locale per l'accesso remoto tramite il Proxy di applicazione in Azure Active Directory](application-proxy-add-on-premises-application.md) 
- [Pianificare una distribuzione di Proxy applicazione Azure AD](application-proxy-deployment-plan.md) 
- [Accesso remoto alle applicazioni locali tramite Azure Active Directory Application Proxy](application-proxy.md) 
