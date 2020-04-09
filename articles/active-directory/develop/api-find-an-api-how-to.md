---
title: Trovare un'API per un'app sviluppata su misura Azure
description: Informazioni su come configurare le autorizzazioni necessarie per accedere a un'API specifica nell'applicazione Azure AD personalizzata
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.openlocfilehash: cd3b21050c6a442284647212fdf7c5707943ffc1
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885617"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Come trovare un'API specifica necessaria per un'applicazione personalizzata

L'accesso alle API richiede la configurazione di ruoli e ambiti di accesso. Se si desidera esporre le API Web dell'applicazione della risorsa alle applicazioni client, è necessario configurare gli ambiti e i ruoli di accesso per l'API. Se si desidera che un'applicazione client acceda a un'API Web, è necessario configurare le autorizzazioni per accedere all'API nella registrazione dell'app.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Configurazione di un'applicazione della risorsa per esporre le API Web

Quando si espone l'API Web, l'API viene visualizzata nell'elenco **Selezionare un'API** quando si aggiungono le autorizzazioni alla registrazione di un'applicazione. Per aggiungere ambiti di accesso, seguire i passaggi descritti in [Configurare un'applicazione per esporre le API Web](quickstart-configure-app-expose-web-apis.md).

## <a name="configuring-a-client-application-to-access-web-apis"></a>Configurazione di un'applicazione client per accedere alle API Web

Quando si aggiungono le autorizzazioni per la registrazione dell'app, è possibile **aggiungere l'accesso all'API** per esporre le API Web. Per accedere alle API Web, seguire i passaggi descritti in [Configurare un'applicazione client per l'accesso alle API Web](quickstart-configure-app-access-web-apis.md).

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sul manifesto dell'applicazione in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)
