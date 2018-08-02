---
title: Come selezionare le autorizzazioni per un'API specifica | Microsoft Docs
description: Come trovare gli endpoint di autenticazione per un'applicazione personalizzata che si sta sviluppando o registrando con Azure AD.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: dcb3a8f735b72b2408e28d2f0dc61b2c634baf96
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366634"
---
# <a name="how-to-select-permissions-for-a-given-api"></a>Come selezionare le autorizzazioni per un'API specifica

È possibile trovare gli endpoint di autenticazione per l'applicazione nel [portale di Azure](https://portal.azure.com).

-   Passare al [portale di Azure](https://portal.azure.com).

-   Nel riquadro di spostamento sinistro fare clic su **Azure Active Directory**.

-   Fare clic su **Registrazioni per l'app** e scegliere **Endpoint**.

-   Verrà visualizzata la pagina **Endpoint** in cui sono elencati tutti gli endpoint di autenticazione per il tenant.

-   Per creare la richiesta di autenticazione specifica per l'applicazione, usare l'endpoint specifico del protocollo di autenticazione in uso, in combinazione con l'ID applicazione.

## <a name="next-steps"></a>Passaggi successivi
[Guida per gli sviluppatori di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide#authentication-and-authorization-protocols)
