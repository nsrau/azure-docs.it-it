---
title: API REST di Azure Active Directory-autenticazione
description: Usare Azure Active Directory per eseguire l'autenticazione a app Azure configurazione usando l'API REST
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: fb3d00fb79c55e29d578f5e068e4ae025414a935
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424193"
---
# <a name="azure-active-directory-authentication"></a>Autenticazione di Azure Active Directory

È possibile autenticare le richieste HTTP usando lo schema di autenticazione della **porta** con un token acquisito da Azure Active Directory (Azure ad). Queste richieste devono essere trasmesse su TLS.

## <a name="prerequisites"></a>Prerequisiti

L'entità che verrà usata per richiedere un token di Azure AD deve essere assegnata a uno dei [ruoli di configurazione delle app](./rest-api-authorization-azure-ad.md) applicabili

Fornire ogni richiesta con tutte le intestazioni HTTP necessarie per l'autenticazione. Il valore minimo richiesto è:

|  Intestazione della richiesta | Descrizione  |
| --------------- | ------------ |
| **Autorizzazione** | Informazioni di autenticazione richieste dallo schema di **porta** . Il formato e i dettagli sono descritti di seguito. |

**Esempio:**

```http
Host: {myconfig}.azconfig.io
Authorization: Bearer {{AadToken}}
```

## <a name="azure-active-directory-token-acquisition"></a>Acquisizione di token Azure Active Directory

Prima di acquisire un token di Azure AD, è necessario identificare l'utente per cui si desidera eseguire l'autenticazione, i destinatari per i quali viene richiesto il token e il Azure AD endpoint (autorità) che devono utilizzare.

### <a name="audience"></a>Destinatari

Il token di Azure AD deve essere richiesto con un gruppo di destinatari appropriato. Per la configurazione di app Azure, è necessario specificare uno dei seguenti destinatari quando si richiede un token. I destinatari possono anche essere chiamati "risorsa" per cui viene richiesto il token.

- {configurationStoreName}. azconfig. io
- *. azconfig.io

> [!IMPORTANT]
> Quando i destinatari richiesti sono {configurationStoreName}. azconfig. io, devono corrispondere esattamente all'intestazione della richiesta "host" (distinzione tra maiuscole e minuscole) usata per inviare la richiesta.

### <a name="azure-ad-authority"></a>Autorità Azure AD

L'autorità Azure AD è l'endpoint usato per acquisire un token di Azure AD. È nel formato `https://login.microsoftonline.com/{tenantId}` . Il `{tenantId}` segmento si riferisce al Azure Active Directory ID tenant a cui appartiene l'utente o l'applicazione che tenta di autenticarsi.

### <a name="authentication-libraries"></a>Librerie di autenticazione

Azure fornisce un set di librerie denominate Azure Active Directory Authentication Libraries (ADAL) per semplificare il processo di acquisizione di un token di Azure AD. Queste librerie sono compilate per più linguaggi. La documentazione è disponibile [qui](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries).

## <a name="errors"></a>**Errori**

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**Motivo:** Non è stata specificata l'intestazione della richiesta di autorizzazione con lo schema di porta.
**Soluzione:** Specificare un' ```Authorization``` intestazione di richiesta HTTP valida

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="Authorization token failed validation"
```

**Motivo:** Il token di Azure AD non è valido.
**Soluzione:** Acquisire un token Azure AD dall'autorità Azure AD e assicurarsi che venga usato il gruppo di destinatari appropriato.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="The access token is from the wrong issuer. It must match the AD tenant associated with the subscription to which the configuration store belongs. If you just transferred your subscription and see this error message, please try back later."
```

**Motivo:** Il token di Azure AD non è valido.
**Soluzione:** Acquisire un token di Azure AD dall'autorità Azure AD e assicurarsi che il tenant Azure AD sia quello associato alla sottoscrizione a cui appartiene l'archivio di configurazione. Questo errore può essere visualizzato se l'entità appartiene a più di un tenant di Azure AD.
