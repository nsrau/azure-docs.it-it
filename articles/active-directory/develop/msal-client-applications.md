---
title: App client pubbliche e riservate (MSAL) Azure
titleSuffix: Microsoft identity platform
description: Informazioni sulle applicazioni client pubbliche e riservate nella libreria di autenticazione Microsoft (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/25/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 9c3292a31e5f750c16933acf94509e0ad226080a
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534313"
---
# <a name="public-client-and-confidential-client-applications"></a>Applicazioni client pubbliche e confidenziali
Microsoft Authentication Library (MSAL) definisce due tipi di client: client pubblici e client riservati. I due tipi di client si distinguono per la capacità di eseguire l'autenticazione in modo sicuro con il server di autorizzazione e mantenere la riservatezza delle credenziali client. Al contrario, Azure AD Authentication Library (ADAL) usa il cosiddetto contesto di *autenticazione* (che è una connessione ad Azure AD).

- **Le applicazioni client riservate** sono app eseguite su server (app Web, app API Web o persino app di servizi/daemon). Sono considerati difficili da accedere e per questo motivo sono in grado di mantenere un segreto dell'applicazione. I client riservati possono contenere segreti in fase di configurazione. Ogni istanza del client ha una configurazione distinta (inclusi l'ID client e il segreto client). Questi valori sono difficili da estrarre per gli utenti finali. Un'app Web è il client riservato più comune. L'ID client viene esposto tramite il browser Web, ma il segreto viene passato solo nel canale posteriore e non viene mai esposto direttamente.

    App client riservate: <BR>
    ![Daemon/servizio dell'API](media/msal-client-applications/web-app.png) ![](media/msal-client-applications/web-api.png) ![Web dell'app Web](media/msal-client-applications/daemon-service.png)

- **Le applicazioni client pubbliche** sono app eseguite su dispositivi o computer desktop o in un Web browser. Non sono considerati attendibili per mantenere in modo sicuro i segreti dell'applicazione, pertanto accedono alle API Web solo per conto dell'utente. (Supportano solo flussi di client pubblici.) I client pubblici non possono contenere segreti in fase di configurazione, pertanto non hanno segreti client.

    App client pubbliche: <BR>
    ![App](media/msal-client-applications/desktop-app.png) ![browserless](media/msal-client-applications/browserless-app.png) ![App mobile per l'app desktop Browserless](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> In MSAL.js, non vi è alcuna separazione delle applicazioni client pubbliche e riservate.  MSAL.js rappresenta le app client come app basate su agenti utente, client pubblici in cui il codice client viene eseguito in un agente utente come un Web browser. Questi client non archiviano segreti perché il contesto del browser è apertamente accessibile.

## <a name="comparing-the-client-types"></a>Confronto dei tipi di client
Di seguito sono riportate alcune analogie e differenze tra le app client pubbliche e quelle riservate:

- Entrambi i tipi di app mantengono una cache dei token utente e possono acquisire un token in modo invisibile all'utente (quando il token è già presente nella cache dei token). Le app client riservate dispongono anche di una cache dei token dell'app per i token che sono per l'app stessa.
- Entrambi i tipi di app gestiscono gli account utente e possono ottenere un account dalla cache dei token utente, ottenere un account dal relativo identificatore o rimuovere un account.
- Le app client pubbliche hanno quattro modi per acquisire un token (quattro flussi di autenticazione). Le app client riservate hanno tre modi per acquisire un token (e un modo per calcolare l'URL dell'endpoint di autorizzazione del provider di identità). Per ulteriori informazioni, vedere [Acquisizione di token](msal-acquire-cache-tokens.md).

Se si utilizza ADAL, è possibile notare che, a differenza del contesto di autenticazione di ADAL, in MSAL l'ID client (denominato anche *ID applicazione* o *ID applicazione)* viene passato una volta alla costruzione dell'applicazione. Non è necessario passare nuovamente quando l'app acquisisce un token. Questo vale sia per le app client pubbliche che per quelle riservate. Ai costruttori di app client riservate vengono inoltre passate le credenziali client: il segreto che condividono con il provider di identità.

## <a name="next-steps"></a>Passaggi successivi
Vengono fornite informazioni su:
- [Opzioni di configurazione dell'applicazione client](msal-client-application-configuration.md)
- [Creazione di istanze di applicazioni client tramite MSAL.NET](msal-net-initializing-client-applications.md)
- [Creazione di un'istanza di applicazioni client tramite MSAL.js](msal-js-initializing-client-applications.md)
