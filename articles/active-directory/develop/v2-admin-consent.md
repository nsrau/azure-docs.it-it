---
title: Protocolli di consenso dell'amministratore della piattaforma di identità Microsoft Documenti Microsoft
description: Descrizione dell'autorizzazione nell'endpoint della piattaforma di identità Microsoft, inclusi ambiti, autorizzazioni e consenso.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 8f98cbf0-a71d-4e34-babf-e642ad9ff423
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/3/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: b9d6ab0fc2f2bf500f17161de7e090a6f60c0feb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76700737"
---
# <a name="admin-consent-on-the-microsoft-identity-platform"></a>Consenso dell'amministratore sulla piattaforma di identità Microsoft

Alcune autorizzazioni richiedono il consenso di un amministratore prima di poter essere concesse all'interno di un tenant.  È anche possibile usare l'endpoint di consenso dell'amministratore per concedere autorizzazioni a un intero tenant.  

## <a name="recommended-sign-the-user-into-your-app"></a>Consigliato: accedi all'utente nella tua app

In genere, durante la compilazione di un'applicazione che usa l'endpoint di consenso dell'amministratore, l'app necessita di una pagina o vista che consenta all'amministratore di approvare le autorizzazioni dell'applicazione. Questa pagina può essere parte del flusso di iscrizione all'app, delle impostazioni dell'applicazione o di un flusso di "connessione" dedicato. In molti casi, è utile per l'applicazione visualizzare la pagina di "connessione" solo dopo che un utente ha eseguito l'accesso con un account di lavoro o dell'istituto di istruzione Microsoft.

L'accesso dell'utente nell'app consente di identificarne l'organizzazione di appartenenza dell'amministratore prima di richiedere l'approvazione delle autorizzazioni necessarie. Sebbene non sia strettamente necessario, questo consente di creare un'esperienza più intuitiva per gli utenti dell'organizzazione. Per accedere all'utente, seguire le esercitazioni sul protocollo della piattaforma di [identità Microsoft](active-directory-v2-protocols.md).

## <a name="request-the-permissions-from-a-directory-admin"></a>Richiedere le autorizzazioni da un amministratore di directory

Quando si è pronti a richiedere le autorizzazioni all'amministratore dell'organizzazione, è possibile reindirizzare l'utente all'endpoint di *consenso dell'amministratore*della piattaforma di identità Microsoft.

```
// Line breaks are for legibility only.
    GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
  client_id=6731de76-14a6-49ae-97bc-6eba6914391e
  &state=12345
  &redirect_uri=http://localhost/myapp/permissions
    &scope=
    https://graph.microsoft.com/calendars.read 
    https://graph.microsoft.com/mail.send
```


| Parametro     | Condizione     | Descrizione                                                                               |
|--------------:|--------------:|:-----------------------------------------------------------------------------------------:|
| `tenant` | Obbligatoria | Il tenant della directory da cui si desidera richiedere autorizzazioni. Può essere specificato in formato di GUID o nome descrittivo OPPURE con il riferimento generico `organizations` come illustrato nell'esempio. Non usare 'comune', poiché gli account personali non possono fornire il consenso dell'amministratore se non nel contesto di un tenant. Per garantire la migliore compatibilità con gli account personali che gestiscono i tenant, usare l'ID tenant quando possibile. |
| `client_id` | Obbligatoria | ID **applicazione (client)** assegnato all'app dal [portale di Azure.](https://go.microsoft.com/fwlink/?linkid=2083908) |
| `redirect_uri` | Obbligatoria |URI di reindirizzamento in cui si desidera che venga inviata la risposta per la gestione da parte dell'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale di registrazione delle applicazioni. |
| `state` | Consigliato | Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Usare questo stato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |
|`scope`        | Obbligatoria      | Definisce il set di autorizzazioni richieste dall'applicazione. Può essere un ambito statico (tramite /.default) o dinamico.  Possono essere inclusi gli ambiti`openid`OIDC ( , `profile`, `email`). | 


A questo punto, Azure AD richiede che solo un amministratore tenant possa accedere per completare la richiesta. All'amministratore viene chiesto di approvare tutte `scope` le autorizzazioni richieste nel parametro.  Se è stato utilizzato`/.default`un valore statico ( ), funzionerà come l'endpoint di consenso dell'amministratore v1.0 e richiedere il consenso per tutti gli ambiti trovati nelle autorizzazioni necessarie per l'app.

### <a name="successful-response"></a>Risposta con esito positivo

Se l'amministratore approva le autorizzazioni per l'app, la risposta con esito positivo si presenta come segue:

```
http://localhost/myapp/permissions?admin_consent=True&tenant=fa00d692-e9c7-4460-a743-29f2956fd429&state=12345&scope=https%3a%2f%2fgraph.microsoft.com%2fCalendars.Read+https%3a%2f%2fgraph.microsoft.com%2fMail.Send
```

| Parametro         | Descrizione                                                                                       |
|------------------:|:-------------------------------------------------------------------------------------------------:|
| `tenant`| Tenant della directory che ha concesso all'applicazione le autorizzazioni richieste, in formato GUID.|
| `state`           | Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Lo stato viene usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava.|
| `scope`          | Set di autorizzazioni a cui è stato concesso l'accesso per l'applicazione.|
| `admin_consent`   | Sarà impostato su `True`.|

### <a name="error-response"></a>Risposta di errore

`http://localhost/myapp/permissions?error=consent_required&error_description=AADSTS65004%3a+The+resource+owner+or+authorization+server+denied+the+request.%0d%0aTrace+ID%3a+d320620c-3d56-42bc-bc45-4cdd85c41f00%0d%0aCorrelation+ID%3a+8478d534-5b2c-4325-8c2c-51395c342c89%0d%0aTimestamp%3a+2019-09-24+18%3a34%3a26Z&admin_consent=True&tenant=fa15d692-e9c7-4460-a743-29f2956fd429&state=12345`

Aggiungendo ai parametri visti in una risposta corretta, i parametri di errore sono visti come di seguito.

| Parametro          | Descrizione                                                                                      |
|-------------------:|:-------------------------------------------------------------------------------------------------:|
| `error`            | Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli.|
| `error_description`| Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore.|
| `tenant`| Tenant della directory che ha concesso all'applicazione le autorizzazioni richieste, in formato GUID.|
| `state`           | Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Lo stato viene usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava.|
| `admin_consent`   | Verrà impostato `True` per indicare che la risposta si è verificata in un flusso di consenso dell'amministratore.|

## <a name="next-steps"></a>Passaggi successivi
- Vedere [come convertire un'app multi-tenant](howto-convert-app-to-be-multi-tenant.md)
- Informazioni su come il consenso è supportato a livello di [protocollo OAuth 2.0 durante il flusso](v2-oauth2-auth-code-flow.md#request-an-authorization-code)di concessione del codice di autorizzazione.
- Informazioni su [come un'applicazione multi-tenant può usare il framework di consenso](active-directory-devhowto-multi-tenant-overview.md) per implementare il consenso "utente" e "amministratore", supportando modelli di applicazione multilivello più avanzati.
- Informazioni sulle esperienze di [consenso alle applicazioni di Azure ADUnderstanding Azure AD application consent experiences](application-consent-experience.md)
