---
title: Serializzazione della cache dei token personalizzati in MSAL per Python | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come serializzare la cache dei token per MSAL per Python
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/13/2019
ms.author: rayluo
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb77d05070543e35ac0addae933c5ca864e68dbc
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74915366"
---
# <a name="custom-token-cache-serialization-in-msal-for-python"></a>Serializzazione della cache dei token personalizzati in MSAL per Python

In MSAL Python, una cache dei token in memoria che viene salvata in modo permanente per la durata della sessione dell'app, viene fornita per impostazione predefinita quando si crea un'istanza di [ClientApplication](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication).

La serializzazione della cache dei token, in modo che diverse sessioni dell'app possano accedervi, non viene fornita "predefinita". Questo perché MSAL Python può essere usato in tipi di app che non hanno accesso al file system, ad esempio le app Web. Per avere una cache di token persistente in un'app Python MSAL, è necessario fornire la serializzazione personalizzata della cache del token.

Le strategie per la serializzazione della cache dei token variano a seconda che si scriva un'applicazione client pubblica (desktop) o un'applicazione client riservata (app Web, API Web o app daemon).

## <a name="token-cache-for-a-public-client-application"></a>Cache dei token per un'applicazione client pubblica

Le applicazioni client pubbliche vengono eseguite sul dispositivo di un utente e gestiscono i token per un singolo utente. In questo caso, è possibile serializzare l'intera cache in un file. Ricordarsi di fornire un blocco di file se l'app, o un'altra app, può accedere alla cache simultaneamente. Per un esempio semplice di come serializzare una cache di token in un file senza blocco, vedere l'esempio nella documentazione di riferimento della classe [SerializableTokenCache](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) .

## <a name="token-cache-for-a-web-app-confidential-client-application"></a>Cache dei token per un'app Web (applicazione client riservata)

Per le app Web o le API Web, è possibile usare la sessione o una cache Redis o un database per archiviare la cache dei token. Deve essere presente una cache di token per utente (per account), quindi assicurarsi di serializzare la cache dei token per ogni account.

## <a name="next-steps"></a>Passaggi successivi

Vedere [MS-Identity-Python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py#L64-L72) per un esempio di come usare la cache dei token per un'app Web Windows o Linux o un'API Web. L'esempio è per un'app Web che chiama l'API Microsoft Graph.
