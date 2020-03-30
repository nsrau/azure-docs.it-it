---
title: Configurare l'iscrizione e l'accesso con un account WeChat
titleSuffix: Azure AD B2C
description: Consentire l'iscrizione e l'accesso ai clienti con account WeChat alle applicazioni da Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c53210939358255b20d0e976df9c4bff88580a80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184435"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account WeChat tramite Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-wechat-application"></a>Creare un'applicazione WeChat

Per usare un account WeChat come provider di identità in Azure Active Directory B2C (Azure AD B2C), è necessario creare un'applicazione nel tenant che lo rappresenta. Se non si dispone già di un account WeChat, è possibile ottenere informazioni su [https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html](https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html).

### <a name="register-a-wechat-application"></a>Registrare un'applicazione WeChat

1. Accedi con [https://open.weixin.qq.com/](https://open.weixin.qq.com/) le tue credenziali WeChat.
1. Selezionare **管理中心** (centro di gestione).
1. Seguire i passaggi per registrare una nuova applicazione.
1. Immettere `https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` per **授权回调域** (URL callback). Ad esempio, se il nome del tenant è contoso, impostare l'URL su `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
1. Copiare l'**ID APP** e la **CHIAVE APP**. necessari per aggiungere il provider di identità nel tenant.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Configurare WeChat come provider di identità nel tenant,

1. Accedere al [portale](https://portal.azure.com/) di Azure come amministratore globale del tenant B2C di Azure AD.
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
1. Seleziona Provider di **identità**, quindi **WeChat (anteprima)**.
1. Immettere un **Nome**. Ad esempio, *WeChat*.
1. Per **l'ID client,** immettere l'ID APP dell'applicazione WeChat creata in precedenza.
1. Per il **segreto client**, immettere la chiave APP registrata.
1. Selezionare **Salva**.
