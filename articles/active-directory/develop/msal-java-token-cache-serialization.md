---
title: Serializzazione della cache token personalizzata (MSAL4j)Custom token cache serialization (MSAL4j)
titleSuffix: Microsoft identity platform
description: Informazioni su come serializzare la cache dei token per MSAL per Java
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: bcb34d83365112b97769186ad74dfd762b05c2e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696164"
---
# <a name="custom-token-cache-serialization-in-msal-for-java"></a>Serializzazione della cache token personalizzata in MSAL per JavaCustom token cache serialization in MSAL for Java

Per rendere persistente la cache dei token tra istanze dell'applicazione, è necessario personalizzare la serializzazione. Le classi java e le interfacce coinvolte nella serializzazione della cache dei token sono le seguenti:

- [ITokenCache:](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCache.html)interfaccia che rappresenta la cache dei token di sicurezza.
- [ITokenCacheAccessAspect](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessAspect.html): interfaccia che rappresenta l'operazione di esecuzione del codice prima e dopo l'accesso. Si @Override sarebbe *beforeCacheAccess* e *afterCacheAccess* con la logica responsabile della serializzazione e deserializzazione della cache.
- [ITokenCacheContext:](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessContext.html)interfaccia che rappresenta il contesto in cui si accede alla cache dei token. 

Di seguito è riportata un'implementazione ingenua della serializzazione personalizzata della serializzazione/deserializzazione della cache dei token. Non copiarlo e incollarlo in un ambiente di produzione.

```Java
static class TokenPersistence implements ITokenCacheAccessAspect {
String data;

TokenPersistence(String data) {
        this.data = data;
}

@Override
public void beforeCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        iTokenCacheAccessContext.tokenCache().deserialize(data);
}

@Override
public void afterCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        data = iTokenCacheAccessContext.tokenCache().serialize();
}
```

```Java
// Loads cache from file
String dataToInitCache = readResource(this.getClass(), "/cache_data/serialized_cache.json");

ITokenCacheAccessAspect persistenceAspect = new TokenPersistence(dataToInitCache);

// By setting *TokenPersistence* on the PublicClientApplication, MSAL will call *beforeCacheAccess()* before accessing the cache and *afterCacheAccess()* after accessing the cache. 
PublicClientApplication app = 
PublicClientApplication.builder("my_client_id").setTokenCacheAccessAspect(persistenceAspect).build();
```

## <a name="learn-more"></a>Altre informazioni

Informazioni su [Ottenere e rimuovere account dalla cache dei token utilizzando MSAL per Java](msal-java-get-remove-accounts-token-cache.md).
