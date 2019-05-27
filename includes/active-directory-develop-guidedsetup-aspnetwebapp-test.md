---
title: File di inclusione
description: File di inclusione
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 8795c9ab0a4dbb76327d0ead48ed33fb0cff9e86
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121707"
---
## <a name="test-your-code"></a>Testare il codice

Per eseguire test dell'applicazione in Visual Studio, premere **F5** per eseguire il progetto. Il browser viene aperto sulla posizione http://<span></span>localhost:{porta} e viene visualizzato il pulsante **Accedi con Microsoft**. Selezionare il pulsante per avviare il processo di accesso.

Quando si è pronti per eseguire il test, usare un account Microsoft Azure Active Directory (Azure AD) (aziendale o dell'istituto di istruzione) o un account Microsoft personale (<span>live.</span>com oppure <span>outlook.</span>com) per accedere.

![Accedi con Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Accedere all'account Microsoft](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>Visualizzare i risultati dell'applicazione

Dopo l'accesso, l'utente viene reindirizzato alla home page del sito Web. La home page è l'URL HTTPS specificato nelle informazioni di registrazione dell'applicazione nel portale di registrazione delle applicazioni Microsoft. La home page include un messaggio di benvenuto di tipo *"Hello \<Utente>"*, un collegamento per la disconnessione e un collegamento per la visualizzazione delle attestazioni dell'utente. Il collegamento per le attestazioni dell'utente porta al controller *Attestazioni* creato in precedenza.

### <a name="browse-to-see-the-users-claims"></a>Esplorare per visualizzare le attestazioni dell'utente

Per visualizzare le attestazioni dell'utente, selezionare il collegamento per passare alla visualizzazione dei controller disponibile solo per gli utenti autenticati.

#### <a name="view-the-claims-results"></a>Visualizzare i risultati delle attestazioni

Dopo il passaggio alla visualizzazione dei controller, dovrebbe essere visualizzata una tabella contenente le proprietà di base per l'utente:

|Proprietà |Valore |DESCRIZIONE |
|---|---|---|
|**Nome** |Nome e cognome dell'utente | Nome e cognome dell'utente.
|**Nome utente** |utente<span>@domain.com</span> | Nome utente usato per identificare l'utente.
|**Oggetto** |Oggetto |Stringa che identifica in modo univoco l'utente sul Web.|
|**ID tenant** |Guid | **GUID** che rappresenta in modo univoco l'organizzazione di Azure AD dell'utente.|

Dovrebbe essere visualizzata anche una tabella di tutte le attestazioni disponibili nella richiesta di autenticazione. Per altre informazioni, vedere l'[elenco di attestazioni disponibili in un token ID di Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Eseguire test dell'accesso a un metodo con attributo Authorize (facoltativo)

Per testare l'accesso come utente anonimo a un controller protetto con l'attributo `Authorize`, eseguire la procedura seguente:

1. Selezionare il collegamento per la disconnessione dell'utente e completare il processo di disconnessione.
2. Nel browser digitare http://<span></span>localhost:{port}/claims per accedere al controller protetto con l'attributo `Authorize`.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Risultati previsti dopo l'accesso a un controller protetto

Viene richiesta l'autenticazione per usare la visualizzazione protetta dei controller.

## <a name="advanced-options"></a>Advanced Options

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Proteggere l'intero sito Web
Per proteggere l'intero sito Web, nel file **Global.asax** aggiungere l'attributo `AuthorizeAttribute` al filtro `GlobalFilters` nel metodo `Application_Start`:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>Limitare l'accesso all'applicazione

Per impostazione predefinita, quando viene compilata l'applicazione creata con la guida, questa sarà in grado di accettare accessi sia di account personali (ad esempio, outlook.com, live.com e altri) sia di account aziendali o di istituti di istruzione di proprietà di aziende od organizzazioni con Azure Active Directory integrato. Si tratta di un'opzione consigliata per le applicazioni SaaS.

Per limitare l'accesso degli utenti all'applicazione, sono disponibili diverse opzioni:

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>Opzione 1: Limitare l'accesso all'applicazione solo agli utenti di una specifica istanza di Active Directory dell'organizzazione (tenant singolo)

Questa opzione è uno scenario comune per le *applicazioni line-of-business*: Se si vuole che l'applicazione accetti gli accessi solo da account appartenenti a una specifica istanza di Azure Active Directory (inclusi gli *account guest* dell'istanza), procedere come segue:

1. Nel file **web.config**, modificare il valore del parametro `Tenant` da `Common` con il nome del tenant dell'organizzazione, ad esempio `contoso.onmicrosoft.com`.
2. Nella [classe OWIN Startup](#configure-the-authentication-pipeline) impostare l'argomento `ValidateIssuer` su `true`.

#### <a name="option-2-restrict-access-to-your-application-to-users-in-a-specific-list-of-organizations"></a>Opzione 2: limitare l'accesso all'applicazione ai soli utenti contenuti in un elenco specifico di organizzazioni

È possibile limitare l'accesso ai soli account utente inclusi in un'organizzazione di Azure AD che fa parte dell'elenco di organizzazioni consentite:
1. Nella [classe OWIN Startup](#configure-the-authentication-pipeline) impostare l'argomento `ValidateIssuer` su `true`.
2. Impostare il valore del parametro `ValidIssuers` sull'elenco di organizzazioni consentite.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>Opzione 3: Usare un metodo personalizzato per convalidare le autorità di certificazione

È possibile implementare un metodo personalizzato per convalidare le autorità di certificazione usando il parametro **IssuerValidator**. Per altre informazioni su come usare questo parametro, vedere la [classe TokenValidationParameters class](/previous-versions/visualstudio/dn464192(v=vs.114)).
