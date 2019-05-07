---
title: App desktop di chiamate di web API (registrazione dell'app) - piattaforma delle identità Microsoft
description: Informazioni su come creare un'app Desktop di chiamate di web API (registrazione dell'app)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5da934709274d90668d94dfea3a9c223e191d032
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076061"
---
# <a name="desktop-app-that-calls-web-apis---app-registration"></a>App desktop che chiama l'API - registrazione dell'app web

Questo articolo contiene le caratteristiche di registrazione di app per un'applicazione desktop.

## <a name="supported-accounts-types"></a>Tipi di account supportati

I tipi di account supportati nell'applicazione desktop variano a seconda dell'esperienza che si desidera light-up, e pertanto sui flussi da usare.

### <a name="audience-for-interactive-token-acquisition"></a>Destinatari di acquisizione dei token interattiva

Se l'applicazione desktop utilizza l'autenticazione interattiva, è possibile accedere gli utenti da qualsiasi [tipo di account](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)

### <a name="audience-for-desktop-app-silent-flows"></a>Gruppo di destinatari per i flussi invisibile all'utente di app desktop

- Se si prevede di usare l'autenticazione Windows integrata oppure nome utente/password, l'applicazione deve eseguire l'accesso agli utenti nel proprio tenant (sviluppatore Line) oppure delle organizzazioni Azure Active directory (scenario ISV). Questi flussi di autenticazione non sono supportati per gli account personali Microsoft
- Se si desidera utilizzare il flusso del codice di dispositivo, non è possibile accedere gli utenti con i propri account personali Microsoft ancora
- Se si accede agli utenti con identità di social networking passando un'autorità di B2C e criteri, è possibile utilizzare solo l'autenticazione interattiva e username-password.

## <a name="redirect-uris"></a>URI di reindirizzamento

Anche in questo caso l'URI da usare nell'applicazione desktop di reindirizzamento dipende il flusso da usare.

- Se si usa l'autenticazione interattiva, è opportuno usare `https://login.microsoftonline.com/common/oauth2/nativeclient`. È possibile ottenere questa configurazione facendo l'URL corrispondente nel **autenticazione** sezione per l'applicazione
  
  > [!IMPORTANT]
  > Oggi MSAL.NET utilizza un altro URI di reindirizzamento per impostazione predefinita nelle applicazioni desktop in esecuzione su Windows (`urn:ietf:wg:oauth:2.0:oob`). In futuro si voglia modificare il valore predefinito e pertanto è consigliabile usare `https://login.microsoftonline.com/common/oauth2/nativeclient`

- Se l'app Usa solo l'autenticazione Windows integrata, nome utente/Password o flusso del codice di dispositivo, non devi registrare un URI di reindirizzamento per l'applicazione. In effetti, questi flussi non un round trip all'endpoint v2.0 di piattaforma di identità Microsoft e l'applicazione non verrà richiamato su un URI specifico. Per poter distinguere da un flusso dell'applicazione client riservato, che non dispone di URI di reindirizzamento entrambi (il flusso di credenziali client usato nelle applicazioni daemon), è necessario esprimere che l'applicazione è un'applicazione client pubblica. Questa configurazione viene ottenuta passando il **autenticazione** sezione per l'applicazione e, il **impostazioni avanzate** sottosezione, scegliere **Sì**, alla domanda **Trattare dell'applicazione come un client pubblico** (nelle **tipo di client predefinito** paragrafo)

  ![Consenti client pubblici](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>Autorizzazioni delle API

Applicazioni desktop di chiamano le API per conto dell'utente connesso. È necessario richiedere autorizzazioni delegate. Essi non possono richiedere autorizzazioni per l'applicazione (che vengono gestiti solo [applicazioni daemon](scenario-daemon-overview.md))

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [App desktop - configurazione delle app](scenario-desktop-app-configuration.md)
