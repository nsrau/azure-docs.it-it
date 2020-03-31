---
title: Piattaforma di identità Microsoft & flusso di asserzione di portatore SAML Azure
description: Informazioni su come recuperare i dati da Microsoft Graph senza richiedere all'utente le credenziali utilizzando il flusso di asserzione di connessione SAML.
services: active-directory
documentationcenter: ''
author: umeshbarapatre
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 16f30473ded5f1de5dc94c1cff9da96165b1a01c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76700210"
---
# <a name="microsoft-identity-platform-and-oauth-20-saml-bearer-assertion-flow"></a>Piattaforma di identità Microsoft e flusso di asserzione bearer SAML OAuth 2.0
Il flusso di asserzione bearer SAML OAuth 2.0 consente di richiedere un token di accesso OAuth utilizzando un'asserzione SAML quando un client deve utilizzare una relazione di trust esistente. La firma applicata all'asserzione SAML fornisce l'autenticazione dell'app autorizzata. Un'asserzione SAML è un token di sicurezza XML rilasciato da un provider di identità e utilizzato da un provider di servizi. Il fornitore di servizi si basa sul suo contenuto per identificare l'oggetto dell'asserzione per scopi relativi alla sicurezza.

L'asserzione SAML viene inviata all'endpoint del token OAuth.  L'endpoint elabora l'asserzione ed emette un token di accesso in base all'approvazione preventiva dell'app. Non è necessario che il client disponga o memorizzi un token di aggiornamento, né il segreto client debba essere passato all'endpoint del token.

Il flusso di asserzione Bearer SAML è utile quando si recuperano dati dalle API di Microsoft Graph (che supportano solo le autorizzazioni delegate) senza richiedere le credenziali all'utente. In questo scenario la concessione delle credenziali client, preferita per i processi in background, non funziona.

Per le applicazioni che eseguono l'accesso interattivo basato su browser per ottenere un'asserzione SAML e quindi desiderano aggiungere l'accesso a un'API protetta OAuth (ad esempio Microsoft Graph), è possibile effettuare una richiesta OAuth per ottenere un token di accesso per l'API. Quando il browser viene reindirizzato ad Azure AD per autenticare l'utente, il browser ritirerà la sessione dall'accesso SAML e l'utente non dovrà immettere le proprie credenziali.

Il flusso di asserzione oAuth SAML Bearer è supportato anche per gli utenti che eseguono l'autenticazione con provider di identità, ad esempio Active Directory Federation Services (ADFS) federati in Azure Active Directory.  L'asserzione SAML ottenuta da ADFS può essere utilizzata in un flusso OAuth per autenticare l'utente.

![Flusso OAuth](./media/v2-saml-bearer-assertion/1.png)

## <a name="call-graph-using-saml-bearer-assertion"></a>Grafico delle chiamate tramite l'asserzione dell'portatore SAML
Ora cerchiamo di capire su come possiamo effettivamente recuperare l'asserzione SAML a livello di programmazione. Questo approccio viene testato con ADFS. Tuttavia, questo funziona con qualsiasi provider di identità che supporta la restituzione dell'asserzione SAML a livello di codice a livello di codice. Il processo di base è: ottenere un'asserzione SAML, ottenere un token di accesso e accedere a Microsoft Graph.The basic process is: get a SAML assertion, get an access token, and access Microsoft Graph.

### <a name="prerequisites"></a>Prerequisiti

Stabilire una relazione di trust tra il server/ambiente di autorizzazione (Microsoft 365) e il provider di identità o l'autorità emittente dell'asserzione di connessione SAML 2.0 (ADFS). Per configurare ADFS per Single Sign-On e come provider di identità è possibile fare riferimento a [questo articolo](https://blogs.technet.microsoft.com/canitpro/2015/09/11/step-by-step-setting-up-ad-fs-and-enabling-single-sign-on-to-office-365/).

Registrare l'applicazione nel [portale](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade):
1. Accedere al [pannello di registrazione dell'app del portale](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) (si prega di usare gli endpoint v2.0 per l'API Graph e quindi è necessario registrare l'applicazione in questo portale. In caso contrario, avremmo potuto usare le registrazioni in Azure Active Directory. 
1. Selezionare **Nuova registrazione**.
1. Quando viene visualizzata la pagina **Registra un'applicazione,** immettere le informazioni di registrazione dell'applicazione:When the Register an application page appears, enter your application's registration information: 
    1. **Nome**: immettere un nome di applicazione significativo, che verrà visualizzato agli utenti dell'app.
    1. **Tipi di account supportati**: selezionare gli account che dovranno essere supportati dall'applicazione.
    1. **URI di reindirizzamento (facoltativo):** selezionare il tipo di app che si sta creando, Web o client pubblico (mobile & desktop) e quindi immettere l'URI di reindirizzamento (o l'URL di risposta) per l'applicazione.
    1. Al termine, selezionare **Registra**.
1. Prendere nota dell'ID dell'applicazione (client).
1. Nel riquadro sinistro selezionare **Certificati & segreti**. Fare clic su **Nuovo segreto client** nella sezione Segreti **client.** Copiare il nuovo segreto client, non sarà possibile recuperare quando si lascia il pannello.
1. Nel riquadro sinistro selezionare **Autorizzazioni API** e quindi **Aggiungi un'autorizzazione**. Selezionare **Microsoft Graph**, quindi **autorizzazioni delegate**, quindi **Attività.lettura** poiché si intende utilizzare l'API Grafico di Outlook. 

Installare [Postman](https://www.getpostman.com/), uno strumento necessario per testare le richieste di esempio.  Successivamente, è possibile convertire le richieste in codice.

### <a name="get-the-saml-assertion-from-adfs"></a>Ottenere l'asserzione SAML da ADFSGet the SAML assertion from ADFS
Creare una richiesta POST all'endpoint ADFS utilizzando busta SOAP per recuperare l'asserzione SAML:

![Ottenere l'asserzione SAMLGet SAML assertion](./media/v2-saml-bearer-assertion/2.png)

Valori intestazione:

![Valori di intestazione](./media/v2-saml-bearer-assertion/3.png)

Corpo della richiesta ADFS:

![Corpo della richiesta ADFS](./media/v2-saml-bearer-assertion/4.png)

Una volta che questa richiesta viene pubblicata correttamente, si dovrebbe ricevere un'asserzione SAML da ADFS. Sono necessari solo i dati del tag **SAML:Assertion,** convertiti nella codifica base64 da utilizzare in ulteriori richieste.

### <a name="get-the-oauth2-token-using-the-saml-assertion"></a>Ottenere il token OAuth2 utilizzando l'asserzione SAMLGet the OAuth2 token using the SAML assertion 
In questo passaggio, recuperare un token OAuth2 utilizzando la risposta di asserzione ADFS.

1. Creare una richiesta POST come illustrato di seguito con i valori di intestazione:Create a POST request as shown below with the header values:

    ![POST, richiesta](./media/v2-saml-bearer-assertion/5.png)
1. Nel corpo della richiesta sostituire **client_id**, **client_secret**e **asserzione** (l'asserzione SAML codificata in base 64 ha ottenuto il passaggio precedente):

    ![Corpo della richiesta](./media/v2-saml-bearer-assertion/6.png)
1. Su richiesta riuscita, si riceverà un token di accesso da Azure Active Directory.

### <a name="get-the-data-with-the-oauth-token"></a>Ottenere i dati con il token OauthGet the data with the Oauth token

Dopo aver ricevuto il token di accesso, chiamare le API Graph (attività di Outlook in questo esempio). 

1. Creare una richiesta GET con il token di accesso recuperato nel passaggio precedente:Create a GET request with the access token fetched in the previous step:

    ![Richiesta GET](./media/v2-saml-bearer-assertion/7.png)

1. Alla richiesta riuscita, si riceverà una risposta JSON.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sui diversi flussi di [autenticazione e sugli scenari dell'applicazione.](authentication-flows-app-scenarios.md)