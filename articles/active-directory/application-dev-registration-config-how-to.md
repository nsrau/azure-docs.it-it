---
title: Come selezionare le autorizzazioni per un'API specifica | Microsoft Docs
description: Come trovare gli endpoint di autenticazione per un'applicazione personalizzata che si sta sviluppando o registrando con Azure AD.
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 6966cf145375bf3d830d476564c428502ae40fd4
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="how-to-select-permissions-for-a-given-api"></a>Come selezionare le autorizzazioni per un'API specifica

È possibile trovare gli endpoint di autenticazione per l'applicazione nel [portale di Azure](https://portal.azure.com).

-   Passare al [portale di Azure](https://portal.azure.com).

-   Nel riquadro di spostamento sinistro fare clic su **Azure Active Directory**.

-   Fare clic su **Registrazioni per l'app** e scegliere **Endpoint**.

-   Verrà visualizzata la pagina **Endpoint** in cui sono elencati tutti gli endpoint di autenticazione per il tenant.

-   Per creare la richiesta di autenticazione specifica per l'applicazione, usare l'endpoint specifico del protocollo di autenticazione in uso, in combinazione con l'ID applicazione.

## <a name="next-steps"></a>Passaggi successivi
[Guida per gli sviluppatori di Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-developers-guide#authentication-and-authorization-protocols)
