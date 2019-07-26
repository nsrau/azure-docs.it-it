---
title: Comprendere e risolvere Azure AD problemi di CORS del proxy di applicazione
description: Fornisce informazioni su CORS in Azure AD proxy di applicazione e su come identificare e risolvere i problemi di CORS.
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
ms.openlocfilehash: 265458066a528246cbfa7876bf61b02a0382581b
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499614"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>Comprendere e risolvere Azure Active Directory Application Proxy problemi CORS

[La condivisione di risorse tra le origini (CORS)](https://www.w3.org/TR/cors/) può talvolta presentare problemi per le app e le API pubblicate tramite il Azure Active Directory Application Proxy. Questo articolo illustra Azure AD il proxy di applicazione CORS problemi e soluzioni.

La protezione del browser in genere impedisce a una pagina Web di effettuare richieste AJAX a un altro dominio. Questa restrizione è detta *criterio della stessa origine*e impedisce a un sito dannoso di leggere dati sensibili da un altro sito. Tuttavia, in alcuni casi potrebbe essere necessario lasciare che altri siti chiamino l'API Web. CORS è uno standard W3C che consente a un server di ridurre i criteri della stessa origine e di consentire alcune richieste tra le origini rifiutando altri utenti.

## <a name="understand-and-identify-cors-issues"></a>Comprendere e identificare i problemi di CORS

Due URL hanno la stessa origine se hanno schemi, host e porte identici ([RFC 6454](https://tools.ietf.org/html/rfc6454)), ad esempio:

-   http:\//contoso.com/foo.html
-   http:\//contoso.com/bar.html

Gli URL seguenti hanno origini diverse rispetto alle due precedenti:

-   http:\//contoso.NET-dominio diverso
-   http:\//contoso.com:9000/foo.html-porta diversa
-   https:\//contoso.com/foo.html-schema diverso
-   http:\//www.contoso.com/foo.html-sottodominio diverso

I criteri della stessa origine impediscono alle app di accedere alle risorse da altre origini a meno che non usino le intestazioni di controllo di accesso corrette. Se le intestazioni CORS sono assenti o non corrette, le richieste tra origini hanno esito negativo. 

È possibile identificare i problemi CORS usando gli strumenti di debug del browser:

1. Avviare il browser e passare all'app Web.
1. Premere **F12** per visualizzare la console di debug.
1. Provare a riprodurre la transazione ed esaminare il messaggio della console. Una violazione di CORS genera un errore della console relativa all'origine.

Nella schermata seguente, se si seleziona il pulsante **prova, è** stato generato un messaggio di errore\/CORS che https:/corswebclient-contoso.msappproxy.NET non è stato trovato nell'intestazione Access-Control-Allow-Origin.

![Problema CORS](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>Problemi di CORS con il proxy di applicazione

Nell'esempio seguente viene illustrato un tipico scenario di CORS proxy di applicazione Azure AD. Il server interno ospita un controller API Web **CORSWebService** e un **CORSWebClient** che chiama **CORSWebService**. Esiste una richiesta AJAX da **CORSWebClient** a **CORSWebService**.

![Richiesta della stessa origine locale](./media/application-proxy-understand-cors-issues/image1.png)

L'app CORSWebClient funziona quando viene ospitata in locale, ma non è possibile caricarla o generarne errori quando viene pubblicata tramite Azure AD proxy di applicazione. Se le app CORSWebClient e CORSWebService sono state pubblicate separatamente come app diverse tramite il proxy di applicazione, le due app sono ospitate in domini diversi. Una richiesta AJAX da CORSWebClient a CORSWebService è una richiesta tra origini e ha esito negativo.

![Richiesta CORS del proxy di applicazione](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>Soluzioni per i problemi di CORS del proxy di applicazione

È possibile risolvere il problema CORS precedente in uno dei diversi modi.

### <a name="option-1-set-up-a-custom-domain"></a>Opzione 1: Configurare un dominio personalizzato

Usare un Azure AD [dominio personalizzato](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) del proxy di applicazione per pubblicare dalla stessa origine, senza dover apportare modifiche alle origini, al codice o alle intestazioni dell'app. 

### <a name="option-2-publish-the-parent-directory"></a>Opzione 2: Pubblicare la directory padre

Pubblicare la directory padre di entrambe le app. Questa soluzione funziona soprattutto se nel server Web sono presenti solo due app. Invece di pubblicare separatamente ogni app, è possibile pubblicare la directory padre comune, che produce la stessa origine.

Gli esempi seguenti illustrano la pagina del proxy di applicazione Azure AD portale per l'app CORSWebClient.  Quando l' **URL interno** è impostato su *contoso.com/CORSWebClient*, l'app non può eseguire le richieste con esito positivo alla directory *contoso.com/CORSWebService* perché sono tra origini. 

![Pubblica l'app singolarmente](./media/application-proxy-understand-cors-issues/image4.png)

Impostare invece l' **URL interno** per pubblicare la directory padre, che include le directory *CORSWebClient* e *CORSWebService* :

![Pubblica directory padre](./media/application-proxy-understand-cors-issues/image5.png)

Gli URL delle app risultanti risolvano efficacemente il problema CORS:

- https:\//corswebclient-contoso.msappproxy.NET/CORSWebService
- https:\//corswebclient-contoso.msappproxy.NET/CORSWebClient

### <a name="option-3-update-http-headers"></a>Opzione 3: Aggiorna intestazioni HTTP

Aggiungere un'intestazione di risposta HTTP personalizzata nel servizio Web in modo che corrisponda alla richiesta di origine. Per i siti Web in esecuzione in Internet Information Services (IIS), utilizzare Gestione IIS per modificare l'intestazione:

![Aggiungi intestazione risposta personalizzata in Gestione IIS](./media/application-proxy-understand-cors-issues/image6.png)

Questa modifica non richiede alcuna modifica del codice. È possibile verificarlo nelle tracce di Fiddler:

**Pubblica l'aggiunta dell'intestazione**\
HTTP/1.1 200 OK \
Cache-Control: No-cache \
Pragma: No-cache \
Content-Type: testo/normale; charset = UTF-8 \
Scadenza:-1 \
Variare Accept-Encoding \
Server: Microsoft-IIS/8.5 Microsoft-HTTPAPI/2.0 \
**Access-Control-Allow-Origin: https\://corswebclient-contoso.msappproxy.NET**\
X-AspNet-Version: 4.0.30319
X-alimentato da: ASP.NET\
Lunghezza contenuto: 17

### <a name="option-4-modify-the-app"></a>Opzione 4: Modificare l'app

È possibile modificare l'app per il supporto di CORS aggiungendo l'intestazione Access-Control-Allow-Origin con i valori appropriati. Il modo in cui aggiungere l'intestazione dipende dal linguaggio del codice dell'app. Modificare il codice è l'opzione meno consigliata, perché richiede il massimo sforzo.

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>Opzione 5: Estendere la durata del token di accesso

Non è possibile risolvere alcuni problemi di CORS, ad esempio quando l'app reindirizza a *login.microsoftonline.com* per l'autenticazione e il token di accesso scade. La chiamata a CORS ha esito negativo. Una soluzione alternativa per questo scenario consiste nell'estendere la durata del token di accesso, in modo da impedirne la scadenza durante la sessione di un utente. Per altre informazioni su come eseguire questa operazione, vedere [durata dei token configurabili in Azure ad](../develop/active-directory-configurable-token-lifetimes.md).

## <a name="see-also"></a>Vedere anche
- [Esercitazione: Aggiungere un'applicazione locale per l'accesso remoto tramite il proxy di applicazione in Azure Active Directory](application-proxy-add-on-premises-application.md) 
- [Pianificare una distribuzione del proxy di applicazione Azure AD](application-proxy-deployment-plan.md) 
- [Accesso remoto alle applicazioni locali tramite Azure Active Directory Application Proxy](application-proxy.md) 
