---
title: Microsoft Identity Platform & flusso di asserzione di porta SAML | Azure
description: Informazioni su come recuperare i dati da Microsoft Graph senza richiedere all'utente le credenziali usando il flusso di asserzione di porta SAML.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: f7f5b983a00dfc0af2e7a40571ce58fafca5914e
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964561"
---
# <a name="microsoft-identity-platform-and-oauth-20-saml-bearer-assertion-flow"></a>Microsoft Identity Platform e OAuth 2,0 SAML Bearer Flow Assertion
Il flusso di asserzione di connessione di OAuth 2,0 SAML consente di richiedere un token di accesso OAuth usando un'asserzione SAML quando un client deve usare una relazione di trust esistente. La firma applicata all'asserzione SAML fornisce l'autenticazione dell'app autorizzata. Un'asserzione SAML è un token di sicurezza XML emesso da un provider di identità e utilizzato da un provider di servizi. Il provider di servizi si basa sul contenuto per identificare l'oggetto dell'asserzione per finalità correlate alla sicurezza.

L'asserzione SAML viene inviata all'endpoint del token OAuth.  L'endpoint elabora l'asserzione e rilascia un token di accesso in base all'approvazione precedente dell'app. Il client non deve avere o archiviare un token di aggiornamento, né il segreto client deve essere passato all'endpoint del token.

Il flusso dell'asserzione di porta SAML è utile quando si recuperano dati da API Microsoft Graph (che supportano solo autorizzazioni delegate) senza richiedere le credenziali all'utente. In questo scenario la concessione delle credenziali client, che è preferibile per i processi in background, non funziona.

Per le applicazioni che eseguono l'accesso interattivo basato su browser per ottenere un'asserzione SAML e quindi vogliono aggiungere l'accesso a un'API protetta OAuth, ad esempio Microsoft Graph, è possibile creare una richiesta OAuth per ottenere un token di accesso per l'API. Quando il browser viene reindirizzato a Azure AD per autenticare l'utente, il browser rileverà la sessione dall'accesso SAML e l'utente non dovrà immettere le proprie credenziali.

Il flusso di asserzione di Bearer SAML di OAuth è supportato anche per gli utenti che eseguono l'autenticazione con provider di identità, ad esempio Active Directory Federation Services (ADFS) federati per Azure Active Directory.  L'asserzione SAML ottenuta da ADFS può essere usata in un flusso OAuth per autenticare l'utente.

![Flusso OAuth](./media/v2-saml-bearer-assertion/1.png)

## <a name="call-graph-using-saml-bearer-assertion"></a>Grafico delle chiamate con l'asserzione di porta SAML
A questo punto è possibile comprendere in che modo è possibile recuperare effettivamente l'asserzione SAML a livello. Questo approccio viene testato con ADFS. Tuttavia, funziona con qualsiasi provider di identità che supporta la restituzione dell'asserzione SAML a livello. Il processo di base è: ottenere un'asserzione SAML, ottenere un token di accesso e accedere Microsoft Graph.

### <a name="prerequisites"></a>Prerequisiti

Stabilire una relazione di trust tra il server di autorizzazione/ambiente (Microsoft 365) e il provider di identità o l'autorità emittente dell'asserzione di connessione di SAML 2,0 (ADFS). Per configurare ADFS per Single Sign-On e come provider di identità, è possibile fare riferimento a [questo articolo](https://blogs.technet.microsoft.com/canitpro/2015/09/11/step-by-step-setting-up-ad-fs-and-enabling-single-sign-on-to-office-365/).

Registrare l'applicazione nel [portale](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade):
1. Accedere al pannello [registrazione app del portale](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) . si noti che vengono usati gli endpoint della versione 2.0 per API Graph e quindi è necessario registrare l'applicazione in questo portale. In caso contrario, avremmo potuto usare le registrazioni in Azure Active Directory. 
1. Selezionare **Nuova registrazione**.
1. Nella pagina **Registra un'applicazione** visualizzata immettere le informazioni di registrazione dell'applicazione. 
    1. **Nome**: immettere un nome di applicazione significativo, che verrà visualizzato agli utenti dell'app.
    1. **Tipi di account supportati**: selezionare gli account che dovranno essere supportati dall'applicazione.
    1. **URI di reindirizzamento (facoltativo)** : selezionare il tipo di app che si sta compilando, il Web o il client pubblico (Mobile & desktop) e quindi immettere l'URI di reindirizzamento (o URL di risposta) per l'applicazione.
    1. Al termine, selezionare **Registra**.
1. Prendere nota dell'ID dell'applicazione (client).
1. Nel riquadro sinistro selezionare **certificati & segreti**. Fare clic su **nuovo segreto client** nella sezione **segreti client** . Copiare il nuovo segreto client, non sarà possibile recuperare quando si esce dal pannello.
1. Nel riquadro sinistro selezionare **autorizzazioni API** e quindi **Aggiungi un'autorizzazione**. Selezionare **Microsoft Graph**, quindi **autorizzazioni delegate**e quindi selezionare **Tasks. Read** poiché si prevede di usare il API Graph di Outlook. 

Installare il [post](https://www.getpostman.com/), uno strumento necessario per testare le richieste di esempio.  Successivamente, è possibile convertire le richieste in codice.

### <a name="get-the-saml-assertion-from-adfs"></a>Ottenere l'asserzione SAML da ADFS
Creare una richiesta POST all'endpoint ADFS usando SOAP envelope per recuperare l'asserzione SAML:

![Ottenere un'asserzione SAML](./media/v2-saml-bearer-assertion/2.png)

Valori intestazione:

![Valori di intestazione](./media/v2-saml-bearer-assertion/3.png)

Corpo della richiesta ADFS:

![Corpo della richiesta ADFS](./media/v2-saml-bearer-assertion/4.png)

Dopo che la richiesta è stata inviata correttamente, è necessario ricevere un'asserzione SAML da ADFS. Solo i dati del tag **SAML: Assertion** sono obbligatori, convertirli in codifica base64 da usare in altre richieste.

### <a name="get-the-oauth2-token-using-the-saml-assertion"></a>Ottenere il token OAuth2 usando l'asserzione SAML 
In questo passaggio, recuperare un token OAuth2 usando la risposta dell'asserzione ADFS.

1. Creare una richiesta POST come illustrato di seguito con i valori dell'intestazione:

    ![POST, richiesta](./media/v2-saml-bearer-assertion/5.png)
1. Nel corpo della richiesta sostituire **client_id**, **client_secret**e **assertion** (l'asserzione SAML con codifica Base64 ha ottenuto il passaggio precedente):

    ![Corpo della richiesta](./media/v2-saml-bearer-assertion/6.png)
1. Al completamento della richiesta, si riceverà un token di accesso da Azure Active Directory.

### <a name="get-the-data-with-the-oauth-token"></a>Ottenere i dati con il token OAuth

Dopo aver ricevuto il token di accesso, chiamare le API Graph (attività di Outlook in questo esempio). 

1. Creare una richiesta GET con il token di accesso recuperato nel passaggio precedente:

    ![Richiesta GET](./media/v2-saml-bearer-assertion/7.png)

1. Al completamento della richiesta, si riceverà una risposta JSON.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sui diversi [flussi di autenticazione e sugli scenari di applicazione](authentication-flows-app-scenarios.md).