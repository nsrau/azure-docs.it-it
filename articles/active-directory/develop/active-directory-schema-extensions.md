---
title: Usare gli attributi di estensione dello schema Azure AD nelle attestazioni
titleSuffix: Microsoft identity platform
description: Viene descritto come utilizzare gli attributi di estensione dello schema di directory per l'invio di dati utente alle applicazioni nelle attestazioni del token.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 07/29/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 8861e641f5ee6a10576425a7702ba02da297a0bf
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631274"
---
# <a name="using-directory-schema-extension-attributes-in-claims"></a>Utilizzo degli attributi di estensione dello schema di directory nelle attestazioni

Gli attributi di estensione dello schema di directory consentono di archiviare dati aggiuntivi in Azure Active Directory sugli oggetti utente e altri oggetti directory, ad esempio gruppi, dettagli del tenant, entità servizio.  Solo gli attributi di estensione sugli oggetti utente possono essere utilizzati per la creazione di attestazioni per le applicazioni. Questo articolo descrive come usare gli attributi di estensione dello schema di directory per l'invio di dati utente alle applicazioni nelle attestazioni del token.

> [!NOTE]
> Microsoft Graph fornisce altri due meccanismi di estensione per personalizzare gli oggetti Graph. Queste sono note come Microsoft Graph estensioni Open e Microsoft Graph estensioni dello schema. Per informazioni dettagliate, vedere la [documentazione Microsoft Graph](/graph/extensibility-overview) . I dati archiviati in Microsoft Graph oggetti che usano queste funzionalità non sono disponibili come origini per le attestazioni nei token.

Gli attributi dell'estensione dello schema di directory sono sempre associati a un'applicazione nel tenant e fanno riferimento al *ApplicationID* dell'applicazione nel nome.

L'identificatore per un attributo di estensione dello schema di directory è nel formato *Extension_xxxxxxxxx_AttributeName*.  Dove *xxxxxxxxx* è il *ApplicationID* dell'applicazione per cui è stata definita l'estensione.

## <a name="registering-and-using-directory-schema-extensions"></a>Registrazione e utilizzo delle estensioni dello schema della directory
Gli attributi di estensione dello schema di directory possono essere registrati e popolati in uno dei due modi seguenti:

- Configurando AD Connect per crearli e sincronizzarvi i dati da AD locale. Vedere [Azure ad Connect le estensioni della directory di sincronizzazione](../hybrid/how-to-connect-sync-feature-directory-extensions.md).
- Utilizzando Microsoft Graph per la registrazione, impostare i valori di e leggere dalle [estensioni dello schema](/graph/extensibility-overview). Sono disponibili anche i [cmdlet di PowerShell](/powershell/azure/active-directory/using-extension-attributes-sample) .

### <a name="emitting-claims-with-data-from-directory-schema-extension-attributes-created-with-ad-connect"></a>Creazione di attestazioni con dati da attributi di estensione dello schema di directory creati con AD Connect
Gli attributi di estensione dello schema di directory creati e sincronizzati con AD Connect sono sempre associati all'ID applicazione usato da AD Connect. Possono essere usate come origine per le attestazioni configurate come attestazioni nella configurazione delle **applicazioni aziendali** nell'interfaccia utente del portale per le applicazioni SAML registrate tramite la raccolta o nell'esperienza di configurazione dell'applicazione non della raccolta in **applicazioni aziendali**e tramite un criterio di mapping delle attestazioni per le applicazioni registrate tramite l'esperienza di registrazione dell'applicazione.  Quando un attributo di estensione della directory creato tramite AD Connect si trova nella directory, verrà visualizzato nell'interfaccia utente di configurazione delle attestazioni SSO SAML.

### <a name="emitting-claims-with-data-from-directory-schema-extension-attributes-created-for-an-application-using-graph-or-powershell"></a>Creazione di attestazioni con dati da attributi di estensione dello schema di directory creati per un'applicazione con Graph o PowerShell
Se un attributo dell'estensione dello schema di directory viene registrato per un'applicazione che usa Microsoft Graph o PowerShell (tramite un'installazione iniziale o un passaggio di provisioning di applicazioni), è possibile configurare la stessa applicazione in Azure Active Directory per ricevere dati in tale attributo da un oggetto utente in un'attestazione quando l'utente esegue l'accesso.  L'applicazione può essere configurata in modo da ricevere i dati nelle estensioni dello schema di directory registrate nella stessa applicazione utilizzando [attestazioni facoltative](active-directory-optional-claims.md#configuring-directory-extension-optional-claims).  Questi possono essere impostati nel manifesto dell'applicazione.  Questo consente a un'applicazione multi-tenant di registrare gli attributi dell'estensione dello schema di directory per uso personale. Quando viene eseguito il provisioning dell'applicazione in un tenant, le estensioni dello schema di directory associate diventano disponibili per essere impostate sugli utenti in tale tenant e per essere utilizzate.  Una volta configurata nel tenant e concessa, può essere usata per archiviare e recuperare i dati tramite Graph e per eseguire il mapping alle attestazioni nei token che Microsoft Identity Platform emette per le applicazioni.

Gli attributi di estensione dello schema di directory possono essere registrati e popolati per qualsiasi applicazione.

Se un'applicazione deve inviare attestazioni con dati da un attributo di estensione registrato in un'altra applicazione, è necessario usare un [criterio di mapping delle attestazioni](active-directory-claims-mapping.md) per eseguire il mapping dell'attributo di estensione all'attestazione.  Un modello comune per la gestione degli attributi delle estensioni dello schema di directory consiste nel creare un'applicazione in modo specifico come punto di registrazione per tutte le estensioni dello schema necessarie.  Non è necessario che sia un'applicazione reale e questa tecnica significa che tutte le estensioni hanno lo stesso ID applicazione nel nome.

Ecco ad esempio un criterio di mapping delle attestazioni per generare una singola attestazione da un attributo di estensione dello schema di directory in un token OAuth/OIDC:

```json
{
    "ClaimsMappingPolicy": {
        "Version": 1,
        "IncludeBasicClaimSet": "false",
        "ClaimsSchema": [{
                "Source": "User",
                "ExtensionID": "extension_xxxxxxx_test",
                "JWTClaimType": "http://schemas.contoso.com/identity/claims/exampleclaim"
            },
        ]
    }
}
```

Dove *xxxxxxx* è l'ID applicazione con cui è stata registrata l'estensione.

> [!TIP]
> La coerenza del case è importante quando si impostano gli attributi di estensione della directory sugli oggetti  I nomi degli attributi di estensione non fanno distinzione tra maiuscole e minuscole durante la configurazione, ma fanno distinzione tra maiuscole e minuscole durante la lettura dalla directory dal servizio token.  Se un attributo di estensione è impostato su un oggetto utente denominato "LegacyId" e su un altro oggetto utente con il nome "LegacyID", quando viene eseguito il mapping dell'attributo a un'attestazione utilizzando il nome "LegacyId" i dati verranno recuperati correttamente e l'attestazione inclusa nel token per il primo utente, ma non il secondo.
>
> Il parametro "ID" nello schema Claims usato per gli attributi di directory predefiniti è "ExtensionID" per gli attributi di estensione della directory.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [aggiungere attestazioni personalizzate o aggiuntive ai token SAML 2,0 e JSON Web Tokens (JWT)](active-directory-optional-claims.md).
- Informazioni su come [personalizzare le attestazioni emesse nei token per un'app specifica](active-directory-claims-mapping.md).