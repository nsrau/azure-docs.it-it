---
title: Ottenere e rimuovere gli account dalla cache dei token usando MSAL per Java (MSAL4j)
titleSuffix: Microsoft identity platform
description: Informazioni su come visualizzare e rimuovere gli account dalla cache dei token usando Microsoft Authentication Library per Java.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a40c5f2e272a44727d4da91600093e8dc691a95f
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74916639"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java"></a>Ottenere e rimuovere gli account dalla cache dei token usando MSAL per Java

Per impostazione predefinita, MSAL per Java fornisce una cache dei token in memoria. La cache dei token in memoria dura la durata dell'istanza dell'applicazione.

## <a name="see-which-accounts-are-in-the-cache"></a>Vedere quali account si trovano nella cache

È possibile verificare quali account sono nella cache chiamando `PublicClientApplication.getAccounts()` come illustrato nell'esempio seguente:

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>Rimuovere gli account dalla cache

Per rimuovere un account dalla cache, trovare l'account che deve essere rimosso, quindi chiamare `PublicClientApplicatoin.removeAccount()` come illustrato nell'esempio seguente:

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>Altre informazioni.

Se si usa MSAL per Java, vedere informazioni sulla [serializzazione della cache dei token personalizzata in MSAL per Java](msal-java-token-cache-serialization.md).
