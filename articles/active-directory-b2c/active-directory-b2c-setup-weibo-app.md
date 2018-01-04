---
title: 'Azure Active Directory B2C: configurazione di Weibo | Documentazione Microsoft'
description: Fornire l'iscrizione e l'accesso agli utenti con account Weibo nelle applicazioni protette da Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: mtillman
editor: parakhj
ms.assetid: 1860de34-94cb-4ceb-851e-102f930f7230
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/26/2017
ms.author: parakhj
ms.openlocfilehash: 320d7c57035d3b3ecdb4e03d80141a58cf429461
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-weibo-accounts"></a>Azure Active Directory B2C: fornire l'iscrizione e l'accesso agli utenti con account Weibo

> [!NOTE]
> Questa funzionalità è in anteprima. Non usare questo provider di identità nell'ambiente di produzione.
> 

## <a name="create-a-weibo-application"></a>Creare un'applicazione Weibo

Per usare Weibo come provider di identità in Azure Active Directory (Azure AD) B2C, è necessario creare un'applicazione Weibo e inserire i parametri corretti. Per eseguire questa operazione è necessario un account Weibo. Se non si ha un account, è possibile ottenerne uno nel sito [http://weibo.com/signup/signup.php?lang=en-us](http://weibo.com/signup/signup.php?lang=en-us).

### <a name="register-for-the-weibo-developer-program"></a>Registrazione per il programma per sviluppatori Weibo

1. Passare al [portale per sviluppatori Weibo](http://open.weibo.com/) e accedere con le credenziali dell'account Weibo.
2. Dopo l'accesso, fare clic sul nome visualizzato nell'angolo superiore destro.
3. Nell'elenco a discesa selezionare**编辑开发者信息** (Modifica informazioni per gli sviluppatori).
4. Immettere le informazioni necessarie nel modulo e fare clic su **下一步** (Invia).
5. Questa operazione completerà il processo di verifica email.
6. Andare alla [pagina di verifica dell'identità](http://open.weibo.com/developers/identity/edit).
7. Immettere le informazioni necessarie nel modulo e fare clic su **下一步** (Invia).

### <a name="register-a-weibo-application"></a>Registrare un'applicazione Weibo

1. Accedere alla [pagina per la registrazione di app Weibo](http://open.weibo.com/apps/new).
2. Immettere le informazioni sull'applicazione necessarie.
3. Fare clic su **创建** (Crea).
4. Copiare i valori della **chiave app** e del **segreto app**. che sarà necessario più avanti.
5. Caricare le foto necessarie e immettere le informazioni necessarie.
6. Fare clic su **保存以上信息** (Salva).
7. Fare clic su **高级信息** (Informazioni avanzate).
8. Fare clic su **编辑** (Modifica) accanto al campo per OAuth 2.0**授权设置** (URL di reindirizzamento).
9. Immettere `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp` per OAuth 2.0**授权设置** (URL di reindirizzamento). Ad esempio, se `tenant_name` è contoso.onmicrosoft.com, impostare l'URL a `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
10. Fare clic su **提交** (Invia).  

## <a name="configure-weibo-as-an-identity-provider-in-your-tenant"></a>Configurare Weibo come provider di identità nel tenant
1. Seguire questa procedura per [passare al pannello delle funzionalità B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) nel portale di Azure.
2. Nel pannello delle funzionalità di B2C, fare clic su **Provider di identità**.
3. Fare clic su **+Aggiungi** nella parte superiore del pannello.
4. Fornire un **Nome** per la configurazione del provider di identità. ad esempio, immettere "Weibo".
5. Fare clic su **Tipo di provider di identità**, selezionare **Weibo** e fare clic su **OK**.
6. Fare clic su **Configura questo provider di identità**
7. Immettere la **chiave app** copiata in precedenza come **ID client**.
8. Immettere il **segreto app** copiato in precedenza come **segreto client**.
9. Fare clic su **OK** e su **Crea** per salvare la configurazione di Weibo.

