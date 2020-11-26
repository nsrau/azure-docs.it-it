---
title: API REST di Azure Active Directory-autenticazione
description: Usare Azure Active Directory per eseguire l'autenticazione a app Azure configurazione usando l'API REST
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 73d951b3e85c170cf068693bb9b374349516ca8f
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183482"
---
# <a name="azure-active-directory-authentication"></a>Autenticazione di Azure Active Directory

È possibile autenticare le richieste HTTP usando lo `Bearer` schema di autenticazione con un token acquisito da Azure Active Directory (Azure ad). È necessario trasmettere queste richieste tramite Transport Layer Security (TLS).

## <a name="prerequisites"></a>Prerequisiti

È necessario assegnare l'entità utilizzata per richiedere un token di Azure AD a uno dei [ruoli di configurazione app Azure](./rest-api-authorization-azure-ad.md)applicabili.

Fornire ogni richiesta con tutte le intestazioni HTTP necessarie per l'autenticazione. Ecco il requisito minimo:

|  Intestazione della richiesta | Descrizione  |
| --------------- | ------------ |
| `Authorization` | Informazioni di autenticazione richieste dallo `Bearer` schema. |

**Esempio:**

```http
Host: {myconfig}.azconfig.io
Authorization: Bearer {{AadToken}}
```

## <a name="azure-ad-token-acquisition"></a>Acquisizione di token Azure AD

Prima di acquisire un token di Azure AD, è necessario identificare l'utente di cui si desidera eseguire l'autenticazione, i destinatari per i quali si richiede il token e il Azure AD endpoint (autorità) da utilizzare.

### <a name="audience"></a>Destinatari

Richiedere il token di Azure AD con un gruppo di destinatari appropriato. Per app Azure configurazione, usare uno dei seguenti destinatari. I destinatari possono anche essere definiti come la *risorsa* per cui viene richiesto il token.

- {configurationStoreName}. azconfig. io
- *. azconfig.io

> [!IMPORTANT]
> Quando i destinatari richiesti sono `{configurationStoreName}.azconfig.io` , deve corrispondere esattamente all' `Host` intestazione della richiesta (con distinzione tra maiuscole e minuscole) usata per inviare la richiesta.

### <a name="azure-ad-authority"></a>Autorità Azure AD

L'autorità Azure AD è l'endpoint usato per acquisire un token di Azure AD. È nel formato `https://login.microsoftonline.com/{tenantId}` . Il `{tenantId}` segmento si riferisce al Azure ad ID tenant a cui appartiene l'utente o l'applicazione che tenta di autenticarsi.

### <a name="authentication-libraries"></a>Librerie di autenticazione

Azure fornisce un set di librerie, denominate Azure Active Directory librerie di autenticazione, per semplificare il processo di acquisizione di un token di Azure AD. Azure compila queste librerie per più linguaggi. Per altre informazioni, vedere la [documentazione](../active-directory/azuread-dev/active-directory-authentication-libraries.md).

## <a name="errors"></a>Errors

Potrebbero verificarsi i seguenti errori.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**Motivo:** Non è stata specificata l'intestazione della richiesta di autorizzazione con lo `Bearer` schema.

**Soluzione:** Specificare un' `Authorization` intestazione di richiesta HTTP valida.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="Authorization token failed validation"
```

**Motivo:** Il token Azure AD non è valido.

**Soluzione:** Acquisire un token Azure AD dall'autorità Azure AD e assicurarsi di aver usato i destinatari appropriati.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="The access token is from the wrong issuer. It must match the AD tenant associated with the subscription to which the configuration store belongs. If you just transferred your subscription and see this error message, please try back later."
```

**Motivo:** Il token Azure AD non è valido.

**Soluzione:** Acquisire un token di Azure AD dall'autorità di Azure AD. Verificare che il tenant di Azure AD sia quello associato alla sottoscrizione a cui appartiene l'archivio di configurazione. Questo errore può essere visualizzato se l'entità appartiene a più di un tenant di Azure AD.