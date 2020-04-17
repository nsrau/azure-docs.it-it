---
title: Serializzazione della cache dei token personalizzata (MSAL Python) Azure
titleSuffix: Microsoft identity platform
description: Scopri come serializzare la cache dei token per MSAL per Python
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
ms.openlocfilehash: 9c6edd0b3cfd6620f04553f9f6dfe89f1c7b7024
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536200"
---
# <a name="custom-token-cache-serialization-in-msal-for-python"></a>Serializzazione della cache token personalizzata in MSAL per PythonCustom token cache serialization in MSAL for Python

In MSAL Python, una cache di token in memoria che viene mantenuta per la durata della sessione dell'app, viene fornita per impostazione predefinita quando si crea un'istanza di [ClientApplication](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication).

La serializzazione della cache dei token, in modo che diverse sessioni dell'app possano accedervi, non viene fornita "fuori dagli schemi". Questo perché MSAL Python può essere usato nei tipi di app che non hanno accesso al file system, ad esempio le app Web. Per avere una cache di token persistente in un'app Python MSAL, è necessario fornire la serializzazione della cache dei token personalizzata.

Le strategie per la serializzazione della cache dei token variano a seconda che si stia scrivendo un'applicazione client pubblica (Desktop) o un'applicazione client riservata (app Web, API Web o app daemon).

## <a name="token-cache-for-a-public-client-application"></a>Cache dei token per un'applicazione client pubblica

Le applicazioni client pubbliche vengono eseguite nel dispositivo di un utente e gestiscono i token per un singolo utente. In questo caso, è possibile serializzare l'intera cache in un file. Ricordati di fornire il blocco dei file se la tua app o un'altra app può accedere contemporaneamente alla cache. Per un semplice esempio di come serializzare una cache di token in un file senza blocco, vedere l'esempio nella documentazione di riferimento della classe [SerializableTokenCache.For](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) a simple example of how to serialize a token cache to a file without locking, see the example in the SerializableTokenCache class reference documentation.

## <a name="token-cache-for-a-web-app-confidential-client-application"></a>Cache di token per un'app Web (applicazione client riservata)

Per le app Web o le API Web, è possibile usare la sessione, una cache Redis o un database per archiviare la cache dei token. Deve essere presente una cache di token per utente (per account), quindi assicurarsi di serializzare la cache di token per ogni account.

## <a name="next-steps"></a>Passaggi successivi

Vedere ms-identity-python-webapp per un esempio di come usare la cache dei token per un'app Web o un'API Web Windows o Linux.See [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py#L64-L72) for an example of how to use the token cache for a Windows or Linux Web app or web API. The example is for a web app that calls the Microsoft Graph API.
