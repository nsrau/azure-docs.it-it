---
title: Le condizioni del servizio e informativa sulla privacy per le app | Azure
description: Informazioni su come configurare le condizioni per l'utilizzo del servizio e l'informativa sulla privacy per le app registrate per l'uso di Azure AD.
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
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviwer: lenalepa, sureshja
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 399f72c7819378ddc1566c2bdde94801b2c71687
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235310"
---
# <a name="how-to-configure-terms-of-service-and-privacy-statement-for-an-app"></a>Procedura: Configurare le condizioni del servizio e informativa sulla privacy per un'app

Gli sviluppatori che creano e gestiscono app integrate con Azure Active Directory (Azure AD) e account Microsoft devono includere collegamenti alle condizioni per l'utilizzo del servizio e all'informativa sulla privacy dell'app. Le condizioni per l'utilizzo del servizio e l'informativa sulla privacy vengono presentate agli utenti tramite l'esperienza di consenso dell'utente e consentono agli utenti di sapere che possono considerare attendibile l'app. Le condizioni per l'utilizzo del servizio e l'informativa sulla privacy sono particolarmente importanti per le app multi-tenant rivolte all'utente, ovvero le app che vengono usate da più directory o sono disponibili per qualsiasi account Microsoft.

L'autore dell'app è responsabile della creazione dei documenti delle condizioni per l'utilizzo del servizio e dell'informativa sulla privacy per la propria app e della specifica degli URL per questi documenti. Per le app multi-tenant che non forniscono questi collegamenti, l'esperienza di consenso dell'utente per l'app visualizzerà un avviso, che potrebbe demotivare gli utenti a fornire il consenso per l'uso dell'app.

> [!NOTE]
> * Per le app a tenant singolo non verrà visualizzato un avviso.
> * Se mancano uno o entrambi i collegamenti, l'app visualizzerà un avviso.

## <a name="user-consent-experience"></a>Esperienza di autorizzazione utente

Gli esempi seguenti mostrano l'esperienza di consenso dell'utente quanto le condizioni per l'utilizzo del servizio e l'informativa sulla privacy sono configurate e quando questi collegamenti non sono configurati.

![Screenshot con e senza condizioni per l'utilizzo del servizio e informativa sulla privacy](./media/howto-add-terms-of-service-privacy-statement/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>Formattazione dei collegamenti ai documenti delle condizioni per l'utilizzo del servizio e dell'informativa sulla privacy

Prima di aggiungere collegamenti ai documenti delle condizioni per l'utilizzo del servizio e dell'informativa sulla privacy, assicurarsi che gli URL rispettino queste linee guida.

| Linee guida     | Descrizione                           |
|---------------|---------------------------------------|
| Format        | URL valido                             |
| Schemi validi | HTTP e HTTPS<br/>Si consiglia HTTPS |
| Lunghezza massima    | 2048 caratteri                       |

Esempi: `https://myapp.com/terms-of-service` e `https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>Aggiunta di collegamenti per le condizioni per l'utilizzo del servizio e l'informativa sulla privacy

Quando le condizioni per l'utilizzo del servizio e l'informativa sulla privacy sono pronte, è possibile aggiungere collegamenti a questi documenti nell'app con uno dei metodi seguenti:

* [Tramite il portale di Azure](#azure-portal)
* [Con il codice JSON dell'oggetto app](#app-object-json)
* [Con la versione beta dell'API REST MSGraph](#msgraph-beta-rest-api)

### <a name="azure-portal"></a>Nel portale di Azure
Seguire questi passaggi nel portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Passare alla sezione **Registrazioni per l'app** e selezionare l'app.
3. Aprire il **Branding** riquadro.
4. Completare i campi **URL delle condizioni d'uso** e **URL dell'informativa sulla privacy**.
5. Salvare le modifiche.

    ![Sezione delle proprietà dell'app con gli URL delle condizioni per l'utilizzo del servizio e dell'informativa sulla privacy](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="app-object-json"></a>Con il codice JSON dell'oggetto app

Se si preferisce modificare direttamente il codice JSON dell'oggetto app, è possibile usare l'editor del manifesto nel portale di Azure o nel portale di registrazione delle applicazioni per includere i collegamenti alle condizioni per l'utilizzo del servizio e all'informativa sulla privacy.

```json
    "informationalUrls": { 
        "termsOfService": "<your_terms_of_service_url>", 
        "privacy": "<your_privacy_statement_url>" 
    }
```

### <a name="msgraph-beta-rest-api"></a>Con la versione beta dell'API REST MSGraph

Per aggiornare tutte le app a livello di programmazione, è possibile usare la versione beta dell'API REST MSGraph per aggiornare tutte le app per includere collegamenti ai documenti delle condizioni per l'utilizzo del servizio e dell'informativa sulla privacy.

```
PATCH https://graph.microsoft.com/beta/applications/{application id}
{ 
    "appId": "{your application id}", 
    "info": { 
        "termsOfServiceUrl": "<your_terms_of_service_url>", 
        "supportUrl": null, 
        "privacyStatementUrl": "<your_privacy_statement_url>", 
        "marketingUrl": null, 
        "logoUrl": null 
    }
}
```

> [!NOTE]
> * Prestare attenzione a non sovrascrivere eventuali valori pre-esistenti assegnati a uno di questi campi: `supportUrl`, `marketingUrl` e `logoUrl`
> * La versione beta dell'API REST MSGraph funzionerà solo quando si accede con un account Azure AD. Gli account Microsoft personali non sono supportati.
