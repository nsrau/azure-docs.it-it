---
title: API REST di configurazione di app Azure-autenticazione
description: Pagine di riferimento per l'autenticazione tramite l'API REST di configurazione app Azure
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 56416009395ebf8270ad0fa8d141277424dd6d9a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183465"
---
# <a name="authentication"></a>Authentication

È necessario autenticare tutte le richieste HTTP. Sono supportati i seguenti schemi di autenticazione.

## <a name="hmac"></a>HMAC

[L'autenticazione HMAC](./rest-api-authentication-hmac.md) usa un segreto generato in modo casuale per firmare i payload della richiesta. Per informazioni dettagliate su come sono autorizzate le richieste che usano questo metodo di autenticazione, vedere la sezione relativa all' [autorizzazione HMAC](./rest-api-authorization-hmac.md) .

## <a name="azure-active-directory"></a>Azure Active Directory

Con [l'autenticazione Azure Active Directory (Azure ad)](../active-directory/authentication/overview-authentication.md) viene utilizzata una Bearer token ottenuta da Azure Active Directory per autenticare le richieste. Per informazioni dettagliate su come sono autorizzate le richieste che usano questo metodo di autenticazione, vedere la sezione [autorizzazione Azure ad](./rest-api-authorization-azure-ad.md) .