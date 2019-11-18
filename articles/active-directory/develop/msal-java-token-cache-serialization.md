---
title: Serializzazione della cache dei token personalizzati in MSAL per Java
titleSuffix: Microsoft identity platform
description: Informazioni su come serializzare la cache dei token per MSAL per Java
services: active-directory
documentationcenter: dev-center-name
author: sangonzal
manager: henrikm
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7274a1b6b5ebf5b55c2cad4b52dfe4c997e26314
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74145896"
---
# <a name="custom-token-cache-serialization-in-msal-for-java-msal4j"></a>Serializzazione della cache dei token personalizzati in MSAL per Java (MSAL4J)

Per salvare in modo permanente la cache dei token tra le istanze dell'applicazione, sarà necessario personalizzare la serializzazione. Le classi e le interfacce Java necessarie per la serializzazione della cache dei token sono le seguenti:

- [ITokenCache](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCache.html): interfaccia che rappresenta la cache del token di sicurezza.
- [ITokenCacheAccessAspect](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessAspect.html): interfaccia che rappresenta l'operazione di esecuzione del codice prima e dopo l'accesso. Si @Override *beforeCacheAccess* e *afterCacheAccess* con la logica responsabile della serializzazione e deserializzazione della cache.
- [ITokenCacheContext](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessContext.html): interfaccia che rappresenta il contesto in cui viene eseguito l'accesso alla cache del token. 

Di seguito è riportata un'implementazione Naive della serializzazione personalizzata della serializzazione/deserializzazione della cache dei token. Non copiarlo e incollarlo in un ambiente di produzione.

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

Informazioni su [come ottenere e rimuovere gli account dalla cache dei token usando MSAL per Java](msal-java-get-remove-accounts-token-cache.md).
