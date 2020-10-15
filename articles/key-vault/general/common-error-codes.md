---
title: Codici di errore comuni per Azure Key Vault | Microsoft Docs
description: Codici di errore comuni per Azure Key Vault
services: key-vault
author: sebansal
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 09/29/2020
ms.author: mbaldwin
ms.openlocfilehash: 25c79229a09db912903fba825f0d48f571880745
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876998"
---
# <a name="common-error-codes-for-azure-key-vault"></a>Codici di errore comuni per Azure Key Vault

I codici di errore elencati nella tabella seguente possono essere restituiti da un'operazione in Azure Key Vault

| Codice di errore | Messaggio utente |
|--|--|
| VaultAlreadyExists |  L'insieme di credenziali delle chiavi specificato esiste già (in stato di eliminazione temporanea o in un'altra sottoscrizione). |
| VaultNameNotValid |  Il nome dell'insieme di credenziali deve essere di 24 caratteri, alfanumerici e iniziare con un alfabeto |
| AccessDenied |  Per eseguire questa operazione, è possibile che non siano disponibili autorizzazioni nei criteri di accesso. |
| ForbiddenByFirewall |  L'indirizzo client non è autorizzato e il chiamante non è un servizio attendibile. |
| ConflictError |  Vengono richieste più operazioni sullo stesso elemento.  |
| RegionNotSupported |  L'area di Azure specificata non è supportata per questa risorsa. |
| SkuNotSupported |  Il tipo di SKU specificato non è supportato per questa risorsa. |
| ResourceNotFound |  La risorsa di Azure specificata non è stata trovata. |
| CertificateExpired |  Controllare la data di scadenza e il periodo di validità del certificato. |


## <a name="next-steps"></a>Passaggi successivi

- Vedere la [Guida per gli sviluppatori per Azure Key Vault](developers-guide.md)
- Vedere altre informazioni in [Eseguire l'autenticazione con Azure Key Vault](authentication.md)
