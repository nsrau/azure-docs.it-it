---
title: Configurare l'iscrizione e l'accesso con un account Weibo
titleSuffix: Azure AD B2C
description: Consentire l'iscrizione e l'accesso ai clienti con account Weibo alle applicazioni da Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3f84d1d33874ac70a21c9d596c6fa5a9e608bb84
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950307"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account Weibo tramite Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-weibo-application"></a>Creare un'applicazione Weibo

Per usare un account Weibo come provider di identità in Azure Active Directory B2C (Azure AD B2C), è necessario creare un'applicazione nel tenant che la rappresenta. Se non si dispone già di un account Weibo, è possibile iscriversi all' [https://weibo.com/signup/signup.php?lang=en-us](https://weibo.com/signup/signup.php?lang=en-us).

1. Accedere al [portale per sviluppatori Weibo](https://open.weibo.com/) con le credenziali dell'account Weibo.
1. Dopo l'accesso, selezionare il nome visualizzato nell'angolo superiore destro.
1. Nell'elenco a discesa selezionare **编辑开发者信息** (modifica le informazioni per sviluppatori).
1. Immettere le informazioni necessarie e quindi fare clic su **提交** (invia).
1. Questa operazione completerà il processo di verifica email.
1. Andare alla [pagina di verifica dell'identità](https://open.weibo.com/developers/identity/edit).
1. Immettere le informazioni necessarie e quindi fare clic su **提交** (invia).

### <a name="register-a-weibo-application"></a>Registrare un'applicazione Weibo

1. Accedere alla [pagina per la registrazione di app Weibo](https://open.weibo.com/apps/new).
1. Immettere le informazioni sull'applicazione necessarie.
1. Selezionare **创建** (crea).
1. Copiare i valori della **chiave app** e del **segreto app**. Entrambi questi valori sono necessari per aggiungere il provider di identità nel tenant.
1. Caricare le foto necessarie e immettere le informazioni necessarie.
1. Selezionare **保存以上信息** (salva).
1. Selezionare **高级信息** (informazioni avanzate).
1. Selezionare **编辑** (modifica) accanto al campo per OAuth2.0 **授权设置** (URL di reindirizzamento).
1. Immettere `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` per OAuth2.0 **授权设置** (URL di reindirizzamento). Ad esempio, se il nome del tenant è contoso, impostare l'URL su `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
1. Selezionare **提交** (invia).

## <a name="configure-a-weibo-account-as-an-identity-provider"></a>Configurare un account Weibo come provider di identità

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
1. Selezionare **provider di identità**e quindi selezionare **Weibo (anteprima)** .
1. Immettere un **Nome**. Ad esempio, *Weibo*.
1. Per **ID client**, immettere la chiave app dell'applicazione Weibo creata in precedenza.
1. Per il **segreto client**, immettere il segreto dell'app registrato.
1. Selezionare **Salva**.
