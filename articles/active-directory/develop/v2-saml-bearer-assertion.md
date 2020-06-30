---
title: Microsoft Identity Platform e flusso dell'asserzione SAML Bearer | Azure
description: Informazioni su come recuperare dati da Microsoft Graph senza richiedere le credenziali all'utente mediante il flusso dell'asserzione SAML Bearer.
services: active-directory
author: umeshbarapatre
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 1cd79b1f9e4cd3afadee250da0c184c0c5b8ac07
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80886178"
---
# <a name="microsoft-identity-platform-and-oauth-20-saml-bearer-assertion-flow"></a>Microsoft Identity Platform e flusso dell'asserzione SAML Bearer di OAuth 2.0
Il flusso dell'asserzione SAML Bearer di OAuth 2.0 consente di richiedere un token di accesso OAuth tramite un'asserzione SAML quando un client deve usare una relazione di trust esistente. La firma applicata all'asserzione SAML fornisce l'autenticazione dell'app autorizzata. Un'asserzione SAML è un token di sicurezza XML rilasciato da un provider di identità e usato da un provider di servizi. Il provider di servizi si basa sul contenuto del token per identificare l'oggetto dell'asserzione per finalità correlate alla sicurezza.

L'asserzione SAML viene inviata all'endpoint del token OAuth.  L'endpoint elabora l'asserzione e rilascia un token di accesso basato sulla precedente approvazione dell'app. Non è necessario che il client abbia o archivi un token di aggiornamento, così come non è necessario che il segreto client venga passato all'endpoint del token.

Il flusso dell'asserzione SAML Bearer è utile per recuperare dati da API Microsoft Graph (che supportano solo autorizzazioni delegate) senza richiedere le credenziali all'utente. In questo scenario la concessione delle credenziali client, che è preferibile per i processi in background, non funziona.

Per le applicazioni che eseguono l'accesso interattivo basato su browser per ottenere un'asserzione SAML e quindi intendono aggiungere l'accesso a un'API protetta OAuth, ad esempio Microsoft Graph, è possibile creare una richiesta OAuth per ottenere un token di accesso per l'API. Quando viene reindirizzato ad Azure AD per autenticare l'utente, il browser rileva la sessione dall'accesso SAML e l'utente non deve immettere le proprie credenziali.

Il flusso dell'asserzione SAML Bearer di OAuth è supportato anche per gli utenti che eseguono l'autenticazione con provider di identità, ad esempio Active Directory Federation Services (AD FS), federati per Azure Active Directory.  L'asserzione SAML ottenuta da AD FS può essere usata in un flusso OAuth per autenticare l'utente.

![Flusso OAuth](./media/v2-saml-bearer-assertion/1.png)

## <a name="call-graph-using-saml-bearer-assertion"></a>Chiamare Graph con l'asserzione SAML Bearer
A questo punto si passerà al recupero effettivo dell'asserzione SAML a livello di programmazione. Questo approccio viene testato con AD FS, ma funziona con qualsiasi provider di identità che supporti la restituzione dell'asserzione SAML a livello di programmazione. Il processo di base consiste nell'ottenere un'asserzione SAML, ottenere un token di accesso e accedere a Microsoft Graph.

### <a name="prerequisites"></a>Prerequisiti

Stabilire una relazione di trust tra il server/ambiente di autorizzazione (Microsoft 365) e il provider di identità o l'autorità di certificazione dell'asserzione SAML 2.0 Bearer (AD FS). Per configurare AD FS per l'accesso Single Sign-On e come provider di identità, è possibile fare riferimento a [questo articolo](https://blogs.technet.microsoft.com/canitpro/2015/09/11/step-by-step-setting-up-ad-fs-and-enabling-single-sign-on-to-office-365/).

Registrare l'applicazione nel [portale](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade):
1. Accedere al [pannello di registrazione app del portale](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade). Si noti che vengono usati gli endpoint della versione 2.0 per l'API Graph e quindi è necessario registrare l'applicazione in questo portale, altrimenti sarebbe stato possibile usare le registrazioni in Azure Active Directory. 
1. Selezionare **Nuova registrazione**.
1. Nella pagina **Registra un'applicazione** visualizzata immettere le informazioni di registrazione dell'applicazione. 
    1. **Nome**: immettere un nome di applicazione significativo, che verrà visualizzato agli utenti dell'app.
    1. **Tipi di account supportati**: selezionare gli account che dovranno essere supportati dall'applicazione.
    1. **URI di reindirizzamento (facoltativo)** : selezionare il tipo di app che si sta creando, ovvero Web o Client pubblico (per dispositivi mobili e desktop), e quindi immettere l'URI di reindirizzamento (o l'URL di risposta) per l'applicazione.
    1. Al termine, selezionare **Registra**.
1. Prendere nota dell'ID (client) dell'applicazione.
1. Nel riquadro sinistro selezionare **Certificati e segreti**. Fare clic su **Nuovo segreto client** nella sezione **Segreti client**. Copiare il nuovo segreto client. Non sarà possibile recuperarlo quando si esce dal pannello.
1. Nel riquadro sinistro selezionare **Autorizzazioni API** e quindi **Aggiungi un'autorizzazione**. Selezionare **Microsoft Graph**, quindi **Autorizzazioni delegate** e infine **Tasks.read** perché si prevede di usare l'API Graph di Outlook. 

Installare [Postman](https://www.getpostman.com/), uno strumento necessario per testare le richieste di esempio.  Successivamente sarà possibile convertire le richieste in codice.

### <a name="get-the-saml-assertion-from-adfs"></a>Ottenere l'asserzione SAML da AD FS
Creare una richiesta POST per l'endpoint AD FS usando SOAP Envelope per recuperare l'asserzione SAML:

![Ottenere l'asserzione SAML](./media/v2-saml-bearer-assertion/2.png)

Valori delle intestazioni:

![Valori delle intestazioni](./media/v2-saml-bearer-assertion/3.png)

Corpo della richiesta AD FS:

![Corpo della richiesta AD FS](./media/v2-saml-bearer-assertion/4.png)

Dopo che la richiesta è stata inviata correttamente, si riceverà un'asserzione SAML da AD FS. Sono necessari solo i dati di tag **SAML:Assertion**. Convertirli nella codifica Base64 per usarli in altre richieste.

### <a name="get-the-oauth2-token-using-the-saml-assertion"></a>Ottenere il token OAuth2 con l'asserzione SAML 
In questo passaggio recuperare un token OAuth2 mediante la risposta dell'asserzione AD FS.

1. Creare una richiesta POST come illustrato di seguito con i valori delle intestazioni:

    ![Richiesta POST](./media/v2-saml-bearer-assertion/5.png)
1. Nel corpo della richiesta sostituire **client_id**, **client_secret** e **assertion** (l'asserzione SAML con codifica Base64 ottenuta nel passaggio precedente):

    ![Corpo della richiesta](./media/v2-saml-bearer-assertion/6.png)
1. Se la richiesta ha esito positivo, si riceverà un token di accesso da Azure Active Directory.

### <a name="get-the-data-with-the-oauth-token"></a>Ottenere i dati con il token OAuth

Dopo aver ricevuto il token di accesso, chiamare le API Graph (attività di Outlook in questo esempio). 

1. Creare una richiesta GET con il token di accesso recuperato nel passaggio precedente:

    ![Richiesta GET](./media/v2-saml-bearer-assertion/7.png)

1. Se la richiesta ha esito positivo, si riceverà una risposta JSON.

## <a name="next-steps"></a>Passaggi successivi

Vedere altre informazioni sui [flussi di autenticazione e gli scenari di applicazioni](authentication-flows-app-scenarios.md).