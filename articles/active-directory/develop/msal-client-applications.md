---
title: Le applicazioni client (Microsoft Authentication Library) | Azure
description: Informazioni sui client pubblico e client riservato applicazioni in Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
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
ms.openlocfilehash: f21aa62bae7599cf586ccf6f885ad9f58c148d1f
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077141"
---
# <a name="public-client-and-confidential-client-applications"></a>Applicazioni client riservati e client pubblici
Microsoft Authentication Library (MSAL) definisce due tipi di client: i client riservati e client pubblici. I tipi di due client si differenziano per capacità di eseguire l'autenticazione in modo sicuro con il server di autorizzazione e mantenere la riservatezza delle proprie credenziali client.  Al contrario, Azure AD Authentication Library (ADAL) ha il concetto di contesto di autenticazione (che è una connessione ad Azure AD).

- **Le applicazioni client riservato** sono applicazioni, che vengono eseguite nel server (app Web, API Web o anche le applicazioni di servizio/daemon). Questi sono considerati difficili da accesso e pertanto in grado di mantenere un segreto dell'applicazione. I client riservati sono in grado di contenere configurazione segreti monouso. Ogni istanza del client ha una configurazione di distinct (incluso l'ID client e segreto). Questi valori sono difficili per gli utenti finali per l'estrazione. Un'app web è il più comune client riservato. L'ID client viene esposta tramite il web browser, ma la chiave privata viene passata solo il canale di supporto e mai esposti direttamente.

    App client riservato: <BR>
    ![App Web](media/msal-client-applications/web-app.png) ![API Web](media/msal-client-applications/web-api.png) ![Daemon/servizio](media/msal-client-applications/daemon-service.png)

- **Le applicazioni client pubblici** sono applicazioni, che vengono eseguite su dispositivi o computer desktop o in un web browser. Non sono attendibili per mantenere in modo sicuro i segreti dell'applicazione e pertanto è possibile accedere solo le API Web per conto dell'utente (supportano solo flussi client pubblico). I client pubblici non riescono a tenere configurazione segreti monouso e di conseguenza non necessario alcun segreto client.

    Applicazioni client pubblico: <BR>
    ![App desktop](media/msal-client-applications/desktop-app.png) ![API Browserless](media/msal-client-applications/browserless-app.png) ![app per dispositivi mobili](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> In msal. js, non vi è alcuna separazione di App client pubblici e riservati.  Msal. js rappresenta le app client di App basato su agente utente, un client pubblico in cui viene eseguito il codice client in un agente utente, ad esempio un web browser.  Questi client non archiviano i segreti, poiché il contesto del browser è accessibile pubblicamente.

## <a name="comparing-the-client-types"></a>Confronto tra i tipi di client
Sono disponibili alcuni similitudini e differenze tra client riservati e pubblica applicazioni:

- Entrambi i tipi di applicazioni mantengono una cache dei token utente e possono acquisire un token in modo invisibile (nei casi in cui il token è già nella cache dei token). Le applicazioni client riservato hanno anche una cache di token di app per i token, che sono per l'app stessa.
- Entrambi gestire gli account utente e possono ottenere l'account dalla cache dei token utente, ottenere un account dal relativo identificatore o rimuovere un account.
- Le applicazioni client pubblico sono disponibili quattro metodi di acquisizione dei token (quattro flussi di autenticazione), mentre le applicazioni client riservati sono tre (ed endpoint di autorizzazione di un metodo per calcolare l'URL del provider di identità). Per altre informazioni, vedere gli scenari e l'acquisizione dei token.

Se si usa ADAL in precedenza, è possibile notare che, diversamente dal contesto di autenticazione ADAL, in MSAL client ID (denominata anche ID app o ID applicazione) viene passata una sola volta in fase di creazione dell'applicazione e non sarà più deve essere ripetuto l'acquisizione di un token. Ciò avviene sia per le applicazioni client pubblici e riservati. I costruttori delle applicazioni client riservati vengono passati anche le credenziali del client: il segreto che condividono il provider di identità.

## <a name="next-steps"></a>Passaggi successivi
Vengono fornite informazioni su:
- [Opzioni di configurazione dell'applicazione client](msal-client-application-configuration.md)
- [Creazione di applicazioni client tramite MSAL.NET](msal-net-initializing-client-applications.md).
- [Creazione di applicazioni client usando msal. js](msal-js-initializing-client-applications.md).
