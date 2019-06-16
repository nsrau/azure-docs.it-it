---
title: Le applicazioni client (Microsoft Authentication Library) | Azure
description: Informazioni sui client pubblico e client riservato applicazioni in Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/25/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d09436b9a2ac38e7b07a51f01d65769ed19d08e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66430818"
---
# <a name="public-client-and-confidential-client-applications"></a>Applicazioni client riservati e client pubblici
Microsoft Authentication Library (MSAL) definisce due tipi di client: i client riservati e client pubblici. I tipi di due client si differenziano per capacità di eseguire l'autenticazione in modo sicuro con il server di autorizzazione e mantenere la riservatezza delle proprie credenziali client. Al contrario, Azure AD Authentication Library (ADAL) usa il cosiddetto *contesto di autenticazione* (che è una connessione ad Azure AD).

- **Le applicazioni client riservato** sono App che eseguono sul server (app web, App per le API Web o anche le app di servizio/daemon). Considerate difficile per l'accesso e per questo motivo in grado di mantenere un segreto dell'applicazione. I client riservati possono contenere i segreti in fase di configurazione. Ogni istanza del client ha una configurazione di distinct (incluso l'ID client e segreto client). Questi valori sono difficili per gli utenti finali per l'estrazione. Un'app web è il più comune client riservato. L'ID client viene esposta tramite il web browser, ma la chiave privata viene passata solo il canale di supporto e mai esposti direttamente.

    App client riservato: <BR>
    ![App Web](media/msal-client-applications/web-app.png) ![API Web](media/msal-client-applications/web-api.png) ![Daemon/servizio](media/msal-client-applications/daemon-service.png)

- **Le applicazioni client pubblici** sono le app eseguite su dispositivi o computer desktop o in un web browser. Non sono attendibili per mantenere in modo sicuro i segreti dell'applicazione, in modo che accedono solo le API Web per conto dell'utente. (Supportano solo flussi client pubblico). I client pubblici non possono contenere informazioni riservate in fase di configurazione, in modo che non hanno i segreti client.

    App client pubblico: <BR>
    ![App desktop](media/msal-client-applications/desktop-app.png) ![API Browserless](media/msal-client-applications/browserless-app.png) ![app per dispositivi mobili](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> In msal. js, non vi è alcuna separazione di App client pubblici e riservati.  Msal. js rappresenta le app client come utente App basate su agente, i client pubblici in cui viene eseguito il codice client in un agente utente, ad esempio un web browser. Questi client non archiviano i segreti in quanto il contesto del browser è accessibile pubblicamente.

## <a name="comparing-the-client-types"></a>Confronto tra i tipi di client
Di seguito sono alcune analogie e differenze tra client riservati e client pubblici delle App:

- Entrambi i tipi di app mantengono una cache dei token utente e possono acquisire un token invisibile all'utente (quando il token è già nella cache dei token). Le app client riservato hanno anche una cache di token di app per i token per l'app stessa.
- Entrambi i tipi di app gestire gli account utente e possono ottenere un account dalla cache dei token utente, ottenere un account dal relativo identificatore o rimuovere un account.
- Le app client pubblico hanno quattro modi per acquisire un token (quattro flussi di autenticazione). Le app client riservati esistono tre modi per acquisire un token e un modo per calcolare l'URL del provider di identità endpoint authorize. Per altre informazioni, vedere [acquisizione dei token](msal-acquire-cache-tokens.md).

Se si usa ADAL, è possibile notare che, a differenza di contesto di autenticazione ADAL, in MSAL l'ID client (detto anche il *ID applicazione* oppure *ID app*) viene passata una sola volta in fase di creazione dell'applicazione. Non è necessario da passare nuovamente quando l'app acquisisce un token. Questo vale sia per le app client pubblici e riservati. Costruttori di App client riservati vengono passati anche le credenziali del client: il segreto che condividono il provider di identità.

## <a name="next-steps"></a>Passaggi successivi
Vengono fornite informazioni su:
- [Opzioni di configurazione dell'applicazione client](msal-client-application-configuration.md)
- [Creazione di applicazioni client tramite MSAL.NET](msal-net-initializing-client-applications.md)
- [Creazione di applicazioni client usando msal. js](msal-js-initializing-client-applications.md)
