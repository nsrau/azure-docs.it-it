---
title: Usare l'autenticazione di Azure AD per accedere all'API Servizi multimediali di Microsoft Azure con REST | Microsoft Docs
description: Informazioni su come accedere all'API Servizi multimediali di Azure con l'autenticazione di Azure Active Directory tramite REST.
services: media-services
documentationcenter: 
author: willzhan
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.author: willzhan;juliako;johndeu
ms.openlocfilehash: e5d7a5ec1c28a552420aba5e2cd6c8c7bbf4213d
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2017
---
# <a name="use-azure-ad-authentication-to-access-the-azure-media-services-api-with-rest"></a>Usare l'autenticazione di Azure AD per accedere all'API Servizi multimediali di Microsoft Azure con REST

Il team di Servizi multimediali di Azure ha rilasciato il supporto dell'autenticazione di Azure Active Directory (Azure AD) per l'accesso a Servizi multimediali di Azure. Il team ha annunciato anche il piano per deprecare l'autenticazione del servizio Controllo di accesso di Azure per l'accesso a Servizi multimediali. Dato che tutte le sottoscrizioni di Azure e tutti gli account di Servizi multimediali sono collegati a un tenant di Azure AD, il supporto dell'autenticazione di Azure AD offre numerosi vantaggi dal punto di vista della sicurezza. Per informazioni dettagliate su questo cambiamento e sulla migrazione, nel caso in cui per l'app si usi Media Services .NET SDK, vedere i post di blog e gli articoli seguenti:

- [Azure Media Services announces support for Azure AD and deprecation of Access Control authentication](https://azure.microsoft.com/blog/azure%20media%20service%20aad%20auth%20and%20acs%20deprecation) (Servizi multimediali di Azure annuncia il supporto per Azure AD e dichiara deprecata l'autenticazione del servizio Controllo di accesso)
- [Access Azure Media Services API by using Azure AD authentication](media-services-use-aad-auth-to-access-ams-api.md) (Accedere all'API Servizi multimediali di Microsoft Azure tramite l'autenticazione di Azure AD)
- [Use Azure AD authentication to access Azure Media Services API by using Microsoft .NET](media-services-dotnet-get-started-with-aad.md) (Usare l'autenticazione di Azure AD per accedere all'API Servizi multimediali di Microsoft Azure con Microsoft .NET)
- [Getting started with Azure AD authentication by using the Azure portal](media-services-portal-get-started-with-aad.md) (Introduzione all'autenticazione di Azure AD tramite il Portale di Azure)

Alcuni clienti devono sviluppare soluzioni di Servizi multimediali in presenza dei vincoli seguenti:

*   Uso di un linguaggio di programmazione diverso da Microsoft .NET o da Visual C# o ambiente di runtime diverso da Windows.
*   Indisponibilità di librerie AD, ad esempio delle librerie per l'autenticazione di Active Directory, per il linguaggio di programmazione usato o impossibilità di usarle con l'ambiente di runtime in uso.

Alcuni clienti hanno sviluppato applicazioni tramite l'API REST sia per l'autenticazione del servizio Controllo di accesso che per l'accesso a Servizi multimediali di Microsoft Azure. Per questi clienti è necessario individuare un modo che consenta di usare solo l'API REST per eseguire l'autenticazione di Azure AD e il successivo accesso a Servizi multimediali di Microsoft Azure, senza dover fare affidamento su alcuna delle librerie di Azure AD o su Media Services .NET SDK. Questo articolo descrive una soluzione e offre codice di esempio per questo scenario. Poiché all'interno del codice sono presenti solo chiamate all'API REST, senza alcuna dipendenza da librerie di AD Azure o di Servizi multimediali di Microsoft Azure, il codice può essere traslato facilmente in qualsiasi altro linguaggio di programmazione.

> [!IMPORTANT]
> Attualmente Servizi multimediali supporta il modello di autenticazione dei servizi di Controllo di accesso di Azure. L'autenticazione di Controllo di accesso, tuttavia, verrà dichiarata deprecata il 1° giugno 2018. È consigliabile eseguire la migrazione all'autenticazione di Azure AD il più presto possibile.


## <a name="design"></a>Progettazione

In questo articolo viene usata la progettazione di autenticazione e autorizzazione seguente:

*  Protocollo di autorizzazione: OAuth 2.0. OAuth 2.0 è uno standard di sicurezza Web che riguarda sia l'autenticazione che l'autorizzazione. Questo standard, supportato da Google, Microsoft, Facebook e PayPal, è stato ratificato nell'ottobre 2012. Microsoft supporta stabilmente gli standard OAuth 2.0 e OpenID Connect in diversi servizi e in più librerie client, tra cui Azure Active Directory, Open Web Interface for .NET (OWIN) Katana e le librerie di Azure AD.
*  Tipo di concessione: credenziali client. Credenziali client è uno dei quattro tipi di concessione in OAuth 2.0 e viene spesso usato per l'accesso all'API Microsoft Graph di Azure AD.
*  Modalità di autenticazione: entità servizio. L'altra modalità di autenticazione è l'autenticazione utente o interattiva.

Nel flusso di autenticazione e autorizzazione di Azure AD per l'uso di Servizi multimediali sono coinvolti in tutto quattro applicazioni o servizi. Il flusso, le applicazioni e i servizi sono descritti nella tabella seguente:

|Tipo di applicazione |Applicazione |Flusso|
|---|---|---|
|Client | App o soluzione del cliente | Questa app o, in realtà, il suo proxy viene registrato nel tenant di Azure AD in cui risiedono la sottoscrizione di Azure e l'account del servizio multimediale. L'entità servizio dell'app registrata viene quindi concessa con il ruolo Owner o Contributor nel Controllo di accesso (IAM, Identity and Access Management, gestione delle identità a degli accessi) dell'account del servizio multimediale. L'entità servizio è rappresentata dall'ID client e dal segreto client dell'app. |
|Provider di identità (IDP) | Azure AD come IDP | L'entità servizio dell'app registrata (ID client e segreto client) viene autenticata da Azure AD come IDP. Questa autenticazione viene eseguita internamente e in modo implicito. Come nel flusso delle credenziali client, viene autenticato il client anziché l'utente. |
|Servizio token di sicurezza/Server OAuth | Azure AD come servizio token di sicurezza | Dopo l'autenticazione da parte del provider di identità (o del server OAuth, in termini di OAuth 2.0), Azure AD, in quanto servizio token di sicurezza/server OAuth, emette un token di accesso o un token JSON Web (JWT) per l'accesso alla risorsa di livello intermedio, in questo caso l'endpoint API REST di Servizi multimediali. |
|Risorsa | API REST di Servizi multimediali | Ogni chiamata API REST di Servizi multimediali viene autorizzata da un token di accesso emesso da Azure AD come servizio token di sicurezza o server OAuth. |

Se si esegue il codice di esempio e si acquisisce un token JWT o un token di accesso, il token JWT ha gli attributi seguenti:

    aud: "https://rest.media.azure.net",

    iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",

    iat: 1497146280,

    nbf: 1497146280,
    exp: 1497150180,

    aio: "Y2ZgYDjuy7SptPzO/muf+uRu1B+ZDQA=",

    appid: "02ed1e8e-af8b-477e-af3d-7e7219a99ac6",

    appidacr: "1",

    idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",

    oid: "a938cfcc-d3de-479c-b0dd-d4ffe6f50f7c",

    sub: "a938cfcc-d3de-479c-b0dd-d4ffe6f50f7c",

    tid: "72f988bf-86f1-41af-91ab-2d7cd011db47",

Ecco i mapping tra gli attributi del token JWT e le quattro applicazioni o servizi della tabella precedente:

|Tipo di applicazione |Applicazione |Attributo JWT |
|---|---|---|
|Client |App o soluzione del cliente |appid: "02ed1e8e-af8b-477e-af3d-7e7219a99ac6". L'ID client dell'applicazione che verrà registrata in Azure AD nella prossima sezione. |
|Provider di identità (IDP) | Azure AD come IDP |idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/" Il GUID corrisponde all'ID del tenant Microsoft (microsoft.onmicrosoft.com). Ogni tenant ha un proprio ID univoco. |
|Servizio token di sicurezza/Server OAuth |Azure AD come servizio token di sicurezza | iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/". Il GUID corrisponde all'ID del tenant Microsoft (microsoft.onmicrosoft.com). |
|Risorsa | API REST di Servizi multimediali |aud: "https://rest.media.azure.net". Destinatario o gruppo di destinatari del token di accesso. |

## <a name="steps-for-setup"></a>Procedura per la configurazione

Per registrare e configurare un'applicazione Azure Active Directory (AAD) e per ottenere le chiavi per chiamare l'endpoint API REST di Servizi multimediali di Azure, vedere l'articolo [Introduzione all'autenticazione di Azure AD tramite il portale di Azure](media-services-portal-get-started-with-aad.md)


## <a name="info-to-collect"></a>Informazioni da raccogliere

Per preparare la codifica di REST, raccogliere i punti dati seguenti da includere nel codice:

*   Azure AD come endpoint servizio token di sicurezza: https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token. Da questo endpoint si richiede un token di accesso JWT. Oltre a svolgere la funzione di provider di identità, Azure AD funge da servizio token di sicurezza. Azure AD emette un token JWT per l'accesso alle risorse (token di accesso). Un token JWT ha varie attestazioni.
*   API REST di Servizi multimediali di Microsoft come risorsa o gruppo di destinatari: https://rest.media.azure.net.
*   ID client: vedere il passaggio 2 in [Procedura per la configurazione](#steps-for-setup).
*   Segreto client: vedere il passaggio 2 in [Procedura per la configurazione](#steps-for-setup).
*   Endpoint API REST dell'account Servizi multimediali nel formato seguente:

    https://[media_service_account_name].restv2.[data_center].media.azure.net/API 

    Si tratta dell'endpoint verso il quale vengono eseguite tutte le chiamate all'API REST di Servizi multimediali nell'applicazione, ad esempio, https://willzhanmswjapan.restv2.japanwest.media.azure.net/API.

È quindi possibile usare questi cinque parametri nel codice inserendoli nel file web.config o app.config.

## <a name="sample-code"></a>Codice di esempio

È possibile trovare il codice di esempio in [Azure AD Authentication for Azure Media Services Access: Both via REST API](https://github.com/willzhan/WAMSRESTSoln) (Autenticazione di Azure AD per l'accesso a Servizi multimediali di Microsoft Azure: entrambi tramite REST).

Il codice di esempio è costituito da due parti:

*   Un progetto di libreria DLL che contiene tutto il codice API REST per l'autenticazione e l'autorizzazione di Azure AD. Contiene anche un metodo per eseguire chiamate all'endpoint API REST di Servizi multimediali con il token di accesso.
*   Un client console di test, che avvia l'autenticazione di Azure AD e chiama un'API REST di Servizi multimediali diversa.

Il progetto di esempio ha tre funzionalità:

*   Autenticazione di AD Azure tramite concessione di credenziali client tramite la sola API REST.
*   Accesso a Servizi multimediali di Microsoft Azure tramite la sola API REST.
*   Accesso all'archiviazione di Azure tramite la sola API REST, analogamente alla creazione di un account di Servizi multimediali tramite l'API REST.


## <a name="where-is-the-refresh-token"></a>Dov'è il token di aggiornamento?

Alcuni lettori potrebbero chiedersi dove sia il token di aggiornamento e perché non venga usato in questo caso.

Lo scopo di un token di aggiornamento non è di aggiornare un token di accesso. È progettato per ottenere comunque un token di accesso valido quando scade un token precedente, pur ignorando l'autenticazione dell'utente finale. Un nome più appropriato per un token di aggiornamento potrebbe essere qualcosa di simile a "token di rinnovo dell'accesso con bypass dell'utente".

Se si usa il flusso di concessione dell'autorizzazione OAuth 2.0 (nome utente e password per conto dell'utente), un token di aggiornamento consente di ottenere il rinnovo del token di accesso senza l'intervento dell'utente. Tuttavia, per il flusso di concessione di credenziali client di OAuth 2.0 descritto in questo articolo il client agisce per proprio conto. L'intervento dell'utente non è necessario. Non è quindi necessario che il server di autorizzazione conceda un token di aggiornamento. Se si esegue il debug del metodo **GetUrlEncodedJWT**, si nota che la risposta dall'endpoint di token contiene un token di accesso ma non un token di aggiornamento.

## <a name="next-steps"></a>Passaggi successivi

Introduzione al [caricamento di file nell'account](media-services-dotnet-upload-files.md).
