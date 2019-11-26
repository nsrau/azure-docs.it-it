---
title: Ottenere e rimuovere gli account dalla cache dei token usando MSAL per Java (MSAL4j)
titleSuffix: Microsoft identity platform
description: Informazioni su come visualizzare e rimuovere gli account dalla cache dei token usando Microsoft Authentication Library per Java.
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
ms.openlocfilehash: fa6c4c53d04f227db7a9a83946182c109dc06d39
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452459"
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

## <a name="learn-more"></a>Altre informazioni

Se si usa MSAL per Java, vedere informazioni sulla [serializzazione della cache dei token personalizzata in MSAL per Java](msal-java-token-cache-serialization.md).
