---
title: Come trovare un'API specifica necessaria per un'applicazione personalizzata | Microsoft Docs
description: Informazioni su come configurare le autorizzazioni necessarie per accedere a un'API specifica nell'applicazione Azure AD personalizzata
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2492a9346585698132e7fd9cfcde068ffd60ebc5
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476158"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Come trovare un'API specifica necessaria per un'applicazione personalizzata

L'accesso alle API richiede la configurazione di ruoli e ambiti di accesso. Se si desidera esporre le API Web dell'applicazione della risorsa alle applicazioni client, è necessario configurare gli ambiti e i ruoli di accesso per l'API. Se si desidera che un'applicazione client acceda a un'API Web, è necessario configurare le autorizzazioni per accedere all'API nella registrazione dell'app.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Configurazione di un'applicazione della risorsa per esporre le API Web

Quando si espone l'API Web, l'API viene visualizzata nell'elenco **Selezionare un'API** quando si aggiungono le autorizzazioni alla registrazione di un'applicazione. Per aggiungere gli ambiti di accesso, seguire i passaggi descritti in [configurare un'applicazione per esporre le API web](quickstart-configure-app-expose-web-apis.md).

## <a name="configuring-a-client-application-to-access-web-apis"></a>Configurazione di un'applicazione client per accedere alle API Web

Quando si aggiungono le autorizzazioni per la registrazione dell'app, è possibile **aggiungere l'accesso all'API** per esporre le API Web. Per accedere alle API web, seguire i passaggi descritti in [configurare un'applicazione client per accedere alle API web](quickstart-configure-app-access-web-apis.md).

## <a name="next-steps"></a>Passaggi successivi

-   [Informazioni sul manifesto dell'applicazione in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)


