---
title: Comprendere e risolvere i problemi CORS del proxy di applicazione di Azure ADUnderstand and solve Azure AD Application Proxy CORS issues
description: Viene fornita una conoscenza di CORS nel proxy di applicazione di Azure AD e su come identificare e risolvere i problemi CORS.
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
ms.openlocfilehash: c49535ad11139ac5145d4f283374bf9cc6d71f52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72025779"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>Comprendere e risolvere i problemi CORS del proxy di applicazione di Azure Active DirectoryUnderstand and solve Azure Active Directory Application Proxy CORS issues

[La condivisione di risorse tra origini (CORS, Cross-Origin Resource Sharing)](https://www.w3.org/TR/cors/) può talvolta rappresentare una sfida per le app e le API pubblicate tramite il proxy di applicazione di Azure Active Directory.Cross-origin resource sharing (CORS) can sometimes present challenges for the apps and APIs you publish through the Azure Active Directory Application Proxy. Questo articolo illustra i problemi e le soluzioni CORS del proxy di applicazione di Azure AD.

La sicurezza del browser impedisce in genere a una pagina Web di effettuare richieste AJAX a un altro dominio. Questa restrizione è denominata criterio della *stessa origine*e impedisce a un sito dannoso di leggere dati sensibili da un altro sito. Tuttavia, a volte potresti voler consentire ad altri siti di chiamare l'API Web. CORS è uno standard W3C che consente a un server di allentare i criteri della stessa origine e consentire alcune richieste tra origini rifiutandone altre.

## <a name="understand-and-identify-cors-issues"></a>Comprendere e identificare i problemi relativi a CORS

Due URL hanno la stessa origine se hanno schemi, host e porte identici ([RFC 6454](https://tools.ietf.org/html/rfc6454)), ad esempio:

-   http:\//contoso.com/foo.html
-   http:\//contoso.com/bar.html

Gli URL seguenti hanno origini diverse rispetto ai due precedenti:

-   http:\//contoso.net - Dominio diverso
-   http:\//contoso.com:9000/foo.html - Porta diversa
-   https:\//contoso.com/foo.html - Schema diverso
-   http:\//www.contoso.com/foo.html - sottodominio diverso

I criteri della stessa origine impediscono alle app di accedere alle risorse di altre origini, a meno che non usino le intestazioni di controllo di accesso corrette. Se le intestazioni CORS sono assenti o non corrette, le richieste tra origini hanno esito negativo. 

È possibile identificare i problemi CORS utilizzando gli strumenti di debug del browser:You can identify CORS issues by using browser debug tools:

1. Avviare il browser e passare all'app Web.
1. Premere **F12** per visualizzare la console di debug.
1. Provare a riprodurre la transazione ed esaminare il messaggio della console. Una violazione CORS genera un errore della console sull'origine.

Nella schermata seguente, la selezione del pulsante **Prova** \/in esso ha causato un messaggio di errore CORS che https: /corswebclient-contoso.msappproxy.net non è stato trovato nell'intestazione Access-Control-Allow-Origin.

![Problema di CORS](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>Sfide CORS con il proxy di applicazione

L'esempio seguente mostra un tipico scenario DI criteri di ricerca del proxy di applicazione di Azure AD. Il server interno ospita un controller API Web **CORSWebService** e un **CORSWebClient** che chiama **CORSWebService**. È presente una richiesta AJAX da **CORSWebClient** a **CORSWebService**.

![Richiesta della stessa origine locale](./media/application-proxy-understand-cors-issues/image1.png)

L'app CORSWebClient funziona quando la si ospita in locale, ma non viene caricata o non viene eseguito errori quando viene pubblicata tramite il proxy di applicazione di Azure AD. Se le app CORSWebClient e CORSWebService sono state pubblicate separatamente come app diverse tramite il proxy di applicazione, le due app sono ospitate in domini diversi. Una richiesta AJAX da CORSWebClient a CORSWebService è una richiesta tra origini e ha esito negativo.

![Richiesta CORS proxy di applicazione](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>Soluzioni per i problemi CORS del proxy di applicazione

È possibile risolvere il problema CORS precedente in uno dei diversi modi.

### <a name="option-1-set-up-a-custom-domain"></a>Opzione 1: Configurare un dominio personalizzatoOption 1: Set up a custom domain

Usare un dominio [personalizzato](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) del proxy di applicazione di Azure AD per pubblicare dalla stessa origine, senza dover apportare modifiche alle origini, al codice o alle intestazioni dell'app. 

### <a name="option-2-publish-the-parent-directory"></a>Opzione 2: Pubblicare la directory padreOption 2: Publish the parent directory

Pubblicare la directory padre di entrambe le app. Questa soluzione funziona particolarmente bene se si dispone solo di due applicazioni sul server web. Invece di pubblicare ogni app separatamente, puoi pubblicare la directory padre comune, che genera la stessa origine.

Gli esempi seguenti mostrano la pagina proxy di applicazione di Azure AD del portale per l'app CORSWebClient.  Quando **l'URL interno** è impostato su *contoso.com/CORSWebClient*, l'app non può effettuare richieste riuscite alla directory *contoso.com/CORSWebService,* perché sono di origine incrociata. 

![Pubblica app singolarmente](./media/application-proxy-understand-cors-issues/image4.png)

Impostare invece **l'URL interno** per pubblicare la directory padre, che include entrambe le directory *CORSWebClient* e *CORSWebService:*

![Pubblica directory padre](./media/application-proxy-understand-cors-issues/image5.png)

Gli URL delle app risultanti risolvono in modo efficace il problema CORS:

- https:\//corswebclient-contoso.msappproxy.net/CORSWebService
- https:\//corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>Opzione 3: Aggiornare le intestazioni HTTPOption 3: Update HTTP headers

Aggiungere un'intestazione di risposta HTTP personalizzata nel servizio Web in modo che corrisponda alla richiesta di origine. Per i siti Web in esecuzione in Internet Information Services (IIS), utilizzare Gestione IIS per modificare l'intestazione:

![Aggiungere un'intestazione di risposta personalizzata in Gestione IIS](./media/application-proxy-understand-cors-issues/image6.png)

Questa modifica non richiede modifiche al codice. È possibile verificarlo nelle tracce Fiddler:You can verify it in the Fiddler traces:

**Registrare l'aggiunta di testate**\
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset:utf-8
Scade: -1
Varia: Accept-Encoding
Server: Microsoft-IIS/8.5 Microsoft-HTTPAPI/2.0
**Access-Control-Allow-Origin:\:https //corswebclient-contoso.msappproxy.net**\
X-AspNet-Versione: 4.0.30319
X-Powered By: ASP.NET
Content-Length: 17

### <a name="option-4-modify-the-app"></a>Opzione 4: modificare l'app

Puoi modificare l'app per supportare CORS aggiungendo l'intestazione Access-Control-Allow-Origin con i valori appropriati. La modalità di aggiunta dell'intestazione dipende dal linguaggio del codice dell'app. La modifica del codice è l'opzione meno consigliata, perché richiede il massimo sforzo.

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>Opzione 5: estendere la durata del token di accessoOption 5: Extend the lifetime of the access token

Alcuni problemi corS non possono essere risolti, ad esempio quando l'app reindirizza a *login.microsoftonline.com* per l'autenticazione e il token di accesso scade. La chiamata CORS ha quindi esito negativo. Una soluzione alternativa per questo scenario consiste nell'estendere la durata del token di accesso, per evitare che scada durante la sessione di un utente. Per altre informazioni su come eseguire questa operazione, vedere Durata dei [token configurabili in Azure AD.](../develop/active-directory-configurable-token-lifetimes.md)

## <a name="see-also"></a>Vedere anche
- [Esercitazione: Aggiungere un'applicazione locale per l'accesso remoto tramite il proxy di applicazione in Azure Active DirectoryTutorial: Add an on-premises application for remote access through Application Proxy in Azure Active Directory](application-proxy-add-on-premises-application.md) 
- [Pianificare una distribuzione del proxy di applicazione di Azure ADPlan an Azure AD Application Proxy deployment](application-proxy-deployment-plan.md) 
- [Accesso remoto alle applicazioni locali tramite il proxy di applicazione di Azure Active Directory](application-proxy.md) 
