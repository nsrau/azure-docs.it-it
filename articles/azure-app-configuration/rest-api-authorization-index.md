---
title: API REST di configurazione app Azure-autorizzazione
description: Pagine di riferimento per l'autorizzazione con l'API REST di configurazione app Azure
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 54f9cfab1f49837a3765c978de6deeb9e5e7d644
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424180"
---
# <a name="authorization"></a>Autorizzazione

L'autorizzazione si riferisce alla procedura utilizzata per determinare le autorizzazioni di cui dispone un chiamante durante l'esecuzione di una richiesta. Sono disponibili più modelli di autorizzazione. Il modello di autorizzazione utilizzato per una richiesta dipende dal metodo di [autenticazione](./rest-api-authentication-index.md) utilizzato. I modelli di autorizzazione sono elencati di seguito.

## <a name="hmac"></a>HMAC

Il modello di [modello di autorizzazione](./rest-api-authorization-hmac.md) associato all'autenticazione HMAC suddivide le autorizzazioni in sola lettura o in lettura/scrittura. Per informazioni dettagliate, vedere la pagina relativa all' [autorizzazione HMAC](./rest-api-authorization-hmac.md) .

## <a name="azure-active-directory"></a>Azure Active Directory

Il [modello di autorizzazione](./rest-api-authorization-azure-ad.md) associato all'autenticazione Azure Active Directory (Azure ad) usa il controllo degli accessi in base al ruolo di Azure. Per informazioni dettagliate, vedere la pagina relativa all' [autorizzazione Azure ad](./rest-api-authorization-azure-ad.md) .
