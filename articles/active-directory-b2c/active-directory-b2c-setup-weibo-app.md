---
title: Configurare l'iscrizione e l'accesso con un account Weibo tramite Azure Active Directory B2C | Microsoft Docs
description: Consentire l'iscrizione e l'accesso ai clienti con account Weibo alle applicazioni da Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 2534e3e39d4a6dd1659dced5a1b0342798c0049e
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969130"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account Weibo tramite Azure Active Directory B2C

> [!NOTE]
> Questa funzionalità è in anteprima.
> 

## <a name="create-a-weibo-application"></a>Creare un'applicazione Weibo

Per usare un account Weibo come provider di identità in Azure Active Directory (Azure AD) B2C, è necessario creare nel tenant un'applicazione che lo rappresenti. Se non si possiede già un account Weibo, è possibile crearlo sul sito [http://weibo.com/signup/signup.php?lang=en-us](http://weibo.com/signup/signup.php?lang=en-us).

1. Accedere al [portale per sviluppatori Weibo](http://open.weibo.com/) con le credenziali dell'account Weibo.
2. Dopo l'accesso, selezionare il nome visualizzato nell'angolo superiore destro.
3. Nell'elenco a discesa selezionare **编辑开发者信息** (modifica le informazioni per sviluppatori).
4. Immettere le informazioni necessarie e quindi fare clic su **提交** (invia).
5. Questa operazione completerà il processo di verifica email.
6. Andare alla [pagina di verifica dell'identità](http://open.weibo.com/developers/identity/edit).
7. Immettere le informazioni necessarie e quindi fare clic su **提交** (invia).

### <a name="register-a-weibo-application"></a>Registrare un'applicazione Weibo

1. Accedere alla [pagina per la registrazione di app Weibo](http://open.weibo.com/apps/new).
2. Immettere le informazioni sull'applicazione necessarie.
3. Selezionare **创建** (crea).
4. Copiare i valori della **chiave app** e del **segreto app**. Entrambi questi valori sono necessari per aggiungere il provider di identità nel tenant.
5. Caricare le foto necessarie e immettere le informazioni necessarie.
6. Selezionare **保存以上信息** (salva).
7. Selezionare **高级信息** (informazioni avanzate).
8. Selezionare **编辑** (modifica) accanto al campo per OAuth2.0 **授权设置** (URL di reindirizzamento).
9. Immettere `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp` per OAuth2.0 **授权设置** (URL di reindirizzamento). Ad esempio, se `tenant_name` è contoso.onmicrosoft.com, impostare l'URL a `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
10. Selezionare **提交** (invia).  

## <a name="configure-a-weibo-account-as-an-identity-provider"></a>Configurare un account Weibo come provider di identità

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
2. Assicurarsi di usare la directory contenente il tenant Azure AD B2C passando a tale directory nell'angolo in alto a destra del portale di Azure. Selezionare le informazioni sulla sottoscrizione e quindi selezionare **Cambia directory**. 

    ![Passare al tenant di Azure AD B2C](./media/active-directory-b2c-setup-weibo-app/switch-directories.png)

    Scegliere la directory contenente il tenant.

    ![Selezionare la directory](./media/active-directory-b2c-setup-weibo-app/select-directory.png)

3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
4. Selezionare **Provider di identità** e quindi selezionare **Aggiungi**.
5. Specificare un **Nome**. Ad esempio, immettere *Weibo*.
6. Fare clic su **Tipo di provider di identità**, selezionare **Weibo (anteprima)** e fare clic su **OK**.
7. Selezionare **Set up this identity provider** (Configura provider di identità), immettere la Chiave app registrata in precedenza in **ID Client** e immettere la Chiave privata app registrata come **Segreto client** dell'applicazione Weibo creata in precedenza.
8. Fare clic su **OK** e su **Crea** per salvare la configurazione di Weibo.
