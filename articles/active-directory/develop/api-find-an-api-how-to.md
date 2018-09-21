---
title: Come trovare un'API specifica necessaria per un'applicazione personalizzata | Microsoft Docs
description: Informazioni su come configurare le autorizzazioni necessarie per accedere a un'API specifica nell'applicazione Azure AD personalizzata
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.openlocfilehash: 8d0b9f219d7a0bc61e3d12acfaae6015963401f9
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2018
ms.locfileid: "44723760"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Come trovare un'API specifica necessaria per un'applicazione personalizzata

L'accesso alle API richiede la configurazione di ruoli e ambiti di accesso. Se si desidera esporre le API Web dell'applicazione della risorsa alle applicazioni client, è necessario configurare gli ambiti e i ruoli di accesso per l'API. Se si desidera che un'applicazione client acceda a un'API Web, è necessario configurare le autorizzazioni per accedere all'API nella registrazione dell'app.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Configurazione di un'applicazione della risorsa per esporre le API Web

Quando si espone l'API Web, l'API viene visualizzata nell'elenco **Selezionare un'API** quando si aggiungono le autorizzazioni alla registrazione di un'applicazione. Per aggiungere gli ambiti di accesso, seguire la procedura descritta in [Aggiunta di ambiti di accesso all'applicazione della risorsa](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-access-scopes-to-your-resource-application).

## <a name="configuring-a-client-application-to-access-web-apis"></a>Configurazione di un'applicazione client per accedere alle API Web

Quando si aggiungono le autorizzazioni per la registrazione dell'app, è possibile **aggiungere l'accesso all'API** per esporre le API Web. Per accedere alle API Web, seguire la procedura descritta nella sezione [Per aggiungere credenziali o autorizzazioni per accedere alle API Web](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#to-add-credentials-or-permissions-to-access-web-apis).

## <a name="next-steps"></a>Passaggi successivi

-   [Integrazione di applicazioni con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

-   [Informazioni sul manifesto dell'applicazione in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)


