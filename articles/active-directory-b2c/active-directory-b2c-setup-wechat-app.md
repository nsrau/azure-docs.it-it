---
title: Configurare l'iscrizione e l'accesso con un account WeChat tramite Azure Active Directory B2C | Microsoft Docs
description: Consentire l'iscrizione e l'accesso ai clienti con account WeChat alle applicazioni da Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e88187c5035abc28ca9deecaf8517e8a21e38d1d
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952335"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account WeChat tramite Azure Active Directory B2C

> [!NOTE]
> Questa funzionalità è in anteprima.
> 

## <a name="create-a-wechat-application"></a>Creare un'applicazione WeChat

Per usare un account WeChat come provider di identità in Azure Active Directory (Azure AD) B2C, è necessario creare nel tenant un'applicazione che lo rappresenti. Se non si possiede già un account WeChat, è possibile ottenere informazioni sul sito [http://kf.qq.com/faq/161220Brem2Q161220uUjERB.html](http://kf.qq.com/faq/161220Brem2Q161220uUjERB.html).

### <a name="register-a-wechat-application"></a>Registrare un'applicazione WeChat

1. Accedere a [https://open.weixin.qq.com/](https://open.weixin.qq.com/) con le credenziali di WeChat.
2. Selezionare **管理中心** (centro di gestione).
3. Seguire i passaggi per registrare una nuova applicazione.
4. Immettere `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp` per **授权回调域** (URL callback). Ad esempio, se `tenant_name` è contoso.onmicrosoft.com, impostare l'URL a `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
5. Copiare l'**ID APP** e la **CHIAVE APP**, necessari per aggiungere il provider di identità nel tenant.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Configurare WeChat come provider di identità nel tenant,

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
2. Assicurarsi di usare la directory contenente il tenant Azure AD B2C passando a tale directory nell'angolo in alto a destra del portale di Azure. Selezionare le informazioni sulla sottoscrizione e quindi selezionare **Cambia directory**. 

    ![Passare al tenant di Azure AD B2C](./media/active-directory-b2c-setup-wechat-app/switch-directories.png)

    Scegliere la directory contenente il tenant.

    ![Selezionare la directory](./media/active-directory-b2c-setup-wechat-app/select-directory.png)

3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
4. Selezionare **Provider di identità** e quindi selezionare **Aggiungi**.
5. Specificare un **Nome**. Ad esempio, immettere *WeChat*.
6. Fare clic su **Tipo di provider di identità**, selezionare **WeChat (anteprima)** e fare clic su **OK**.
7. Selezionare **Set up this identity provider** (Configura provider di identità), immettere l'ID APP registrato in precedenza in **ID Client** e immettere la CHIAVE APP registrata come **Segreto client** dell'applicazione WeChat creata in precedenza.
8. Fare clic su **OK** e su **Crea** per salvare la configurazione di WeChat.

