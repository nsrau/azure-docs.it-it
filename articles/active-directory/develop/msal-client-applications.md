---
title: Applicazioni client (Microsoft Authentication Library) | Azure
description: Informazioni sulle applicazioni client pubbliche e riservate in Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/25/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c854cc34a1ea50f37428cfc18146618d516de7d
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "69532970"
---
# <a name="public-client-and-confidential-client-applications"></a>Client pubblici e applicazioni client riservate
Microsoft Authentication Library (MSAL) definisce due tipi di client: client pubblici e client riservati. I due tipi di client sono distinti dalla loro capacità di eseguire l'autenticazione in modo sicuro con il server di autorizzazione e di mantenere la riservatezza delle proprie credenziali client. Al contrario, Autenticazione di Azure AD Library (ADAL) usa il cosiddetto *contesto di autenticazione* , ovvero una connessione a Azure ad.

- **Le applicazioni client riservate** sono app eseguite su server (app Web, app per le API Web o anche app di servizio/daemon). Sono considerati difficili da accedere e per questo motivo è in grado di mantenere un segreto dell'applicazione. I client riservati possono conservare i segreti in fase di configurazione. Ogni istanza del client ha una configurazione distinta (inclusi ID client e segreto client). Questi valori sono difficili da estrarre per gli utenti finali. Un'app Web è il client riservato più comune. L'ID client viene esposto tramite il Web browser, ma il segreto viene passato solo nel canale indietro e mai esposto direttamente.

    App client riservate: <BR>
    ![Web app ](media/msal-client-applications/web-app.png) ![Web API ](media/msal-client-applications/web-api.png) ![Daemon/Service ](media/msal-client-applications/daemon-service.png)

- **Le applicazioni client pubbliche** sono app eseguite su dispositivi o computer desktop o in un Web browser. Non sono attendibili per la conservazione sicura dei segreti dell'applicazione, quindi accedono solo alle API Web per conto dell'utente. Supportano solo i flussi client pubblici. I client pubblici non possono contenere segreti in fase di configurazione, quindi non hanno segreti client.

    App client pubbliche: <BR>
    ![Desktop app ](media/msal-client-applications/desktop-app.png) ![Browserless API ](media/msal-client-applications/browserless-app.png) ![Mobile app ](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> In MSAL. js non esiste alcuna separazione tra app client pubbliche e riservate.  MSAL. js rappresenta le app client come app basate su agenti utente, client pubblici in cui il codice client viene eseguito in un agente utente come un Web browser. Questi client non archiviano i segreti perché il contesto del browser è accessibile apertamente.

## <a name="comparing-the-client-types"></a>Confronto tra i tipi di client
Di seguito sono riportate alcune analogie e differenze tra client pubblici e app client riservate:

- Entrambi i tipi di app gestiscono una cache dei token utente e possono acquisire un token in modo invisibile all'utente (quando il token si trova già nella cache dei token). Le app client riservate hanno anche una cache dei token dell'app per i token per l'app stessa.
- Entrambi i tipi di app gestiscono gli account utente e possono ottenere un account dalla cache dei token utente, ottenere un account dal relativo identificatore oppure rimuovere un account.
- Le app client pubbliche hanno quattro modi per acquisire un token (quattro flussi di autenticazione). Le app client riservate hanno tre modi per acquisire un token (e un modo per calcolare l'URL dell'endpoint di autorizzazione del provider di identità). Per ulteriori informazioni, vedere [acquisizione di token](msal-acquire-cache-tokens.md).

Se è stato usato ADAL, è possibile notare che, a differenza del contesto di autenticazione di ADAL, in MSAL l'ID client (noto anche come ID *applicazione* o *ID app*) viene passato una volta alla costruzione dell'applicazione. Non è necessario passare di nuovo quando l'app acquisisce un token. Questo vale sia per le app client pubbliche che riservate. Ai costruttori di app client riservate vengono inoltre passate le credenziali client: il segreto che condividono con il provider di identità.

## <a name="next-steps"></a>Passaggi successivi
Vengono fornite informazioni su:
- [Opzioni di configurazione dell'applicazione client](msal-client-application-configuration.md)
- [Creazione di istanze di applicazioni client tramite MSAL.NET](msal-net-initializing-client-applications.md)
- [Creazione di istanze di applicazioni client tramite MSAL. js](msal-js-initializing-client-applications.md)
