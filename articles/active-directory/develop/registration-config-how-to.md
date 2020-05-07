---
title: Configurare gli endpoint
description: Come trovare gli endpoint di autenticazione per un'applicazione personalizzata che si sta sviluppando o registrando con Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ms.openlocfilehash: 043dc4f6e57620f58a1cf5f76db755703421800f
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82778737"
---
# <a name="how-to-configure-endpoints"></a>Come configurare gli endpoint

È possibile trovare gli endpoint di autenticazione per l'applicazione nel [portale di Azure](https://portal.azure.com).

-   Passare al [portale di Azure](https://portal.azure.com).

-   Nel riquadro di spostamento sinistro fare clic su **Azure Active Directory**.

-   Fare clic su **Registrazioni per l'app** e scegliere **Endpoint**.

-   Verrà visualizzata la pagina **Endpoint** in cui sono elencati tutti gli endpoint di autenticazione per il tenant.

-   Per creare la richiesta di autenticazione specifica per l'applicazione, usare l'endpoint specifico del protocollo di autenticazione in uso, in combinazione con l'ID applicazione.

I **cloud nazionali** (ad esempio Azure ad Cina, Germania e governo degli Stati Uniti) hanno il proprio portale di registrazione delle app e Azure ad endpoint di autenticazione. Per altre informazioni, vedere [Panoramica dei cloud nazionali](authentication-national-cloud.md).

## <a name="next-steps"></a>Passaggi successivi
[Guida per gli sviluppatori di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)
