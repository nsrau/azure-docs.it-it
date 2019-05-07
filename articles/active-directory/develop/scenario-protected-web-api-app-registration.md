---
title: API web - registrazione di app protetta | Azure
description: Informazioni su come compilare un'API Web protetta e le informazioni necessarie per registrare l'app.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1d8c8fcf84cd008957fcdb7cd14c4a07d9f3643
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074906"
---
# <a name="protected-web-api---app-registration"></a>API - registrazione di app web protetta

Questo articolo illustra le specifiche di registrazione di app per un'API web protetta.

Vedere [Avvio rapido: Registrare un'applicazione con la piattaforma delle identità Microsoft](quickstart-register-app.md) per i comuni passaggi su come registrare l'applicazione.

## <a name="accepted-token-version"></a>Versione del token accettato

L'endpoint di Microsoft identity platform può rilasciare due tipi di token: token v1.0 e v2.0 token. Altre informazioni su questi token nel [token di accesso](access-tokens.md). La versione del token accettata varia a seconda le **tipi di account supportati** scelto durante la creazione dell'applicazione:

- Se il valore di **tipi di account supportati** viene **gli account in qualsiasi directory dell'organizzazione e gli account Microsoft personali (ad esempio Skype, Xbox, Outlook.com)**, la versione accettata token sarà v2.0.
- In caso contrario, la versione accettata token sarà v2.0.

Dopo aver creato l'applicazione, è possibile modificare la versione del token accettata, seguire questi passaggi:

1. Nel portale di Azure, selezionare l'app e quindi selezionare il **manifesto** per l'app.
2. Nel manifesto, cercare **"accessTokenAcceptedVersion"** e riscontrerebbe che il relativo valore è **2**. Questa proprietà consente ad Azure AD sa che l'API web accetta token v2.0. Se si tratta **null**, la versione accettata token sarà v1.0.
3. Selezionare **Salva**.

> [!NOTE]
> Spetta all'API web per decidere quale token versione (versione 1.0 o 2.0) accetta. Quando i client richiedono un token per l'API web usando l'endpoint 2.0 di Microsoft identity platform, si otterrà un token che indica quale versione è stata accettata dall'API web.

## <a name="no-redirect-uri"></a>Nessun URI di reindirizzamento

Le API Web non necessario registrare un URI di reindirizzamento, perché nessun utente è connesso in modo interattivo.

## <a name="expose-an-api"></a>Esporre un'API

Un'altra impostazione specifica per le API web è l'API esposta e sugli ambiti esposti.

### <a name="resource-uri-and-scopes"></a>URI della risorsa e gli ambiti

Gli ambiti sono in genere nel formato `resourceURI/scopeName`. Dispongono di tali tasti, ad esempio per Microsoft Graph, gli ambiti `User.Read`, ma questa stringa è solo una scorciatoia per `https://graph.microsoft.com/user.read`.

Durante la registrazione dell'app, è necessario definire i parametri seguenti:

- Si consiglia una risorsa URI - per impostazione predefinita il portale di registrazione dell'applicazione che è possibile usare `api://{clientId}`. Questo URI di risorsa sia univoco, ma non umano leggibile. È possibile modificarla, ma assicurarsi che sia univoco.
- Uno o più ambiti

Gli ambiti vengono inoltre visualizzati nella schermata di consenso che viene presentata agli utenti finali che usano l'applicazione. Pertanto, è necessario fornire le stringhe corrispondenti che descrivono l'ambito:

- Stessa forma visualizzata all'utente finale
- Come illustrato dall'amministratore tenant, che può concedere il consenso dell'amministratore

### <a name="how-to-expose-the-api"></a>Come esporre l'API

1. Selezionare il **esporre un'API** sezione nella registrazione dell'applicazione, e:
   1. Selezionare **Aggiungi un ambito**.
   1. Accettare l'URI dell'ID applicazione proposta (api :// {clientId}) selezionando **Salva e continua**.
   1. Immettere i parametri seguenti:
      - Per la **nome ambito**, usare `access_as_user`.
      - Per **che può fornire il consenso**, assicurarsi che le **gli amministratori e utenti** opzione è selezionata.
      - Nelle **nome visualizzazione il consenso dell'amministratore**, tipo `Access TodoListService as a user`.
      - Nelle **descrizione del consenso dell'amministratore**, tipo `Accesses the TodoListService Web API as a user`.
      - Nelle **nome visualizzato dell'utente il consenso**, tipo `Access TodoListService as a user`.
      - Nelle **descrizione del consenso dell'utente**, tipo `Accesses the TodoListService Web API as a user`.
      - Mantieni **lo stato** impostata su **abilitato**.
      - Selezionare **Aggiungi ambito**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurazione del codice dell'App](scenario-protected-web-api-app-configuration.md)
