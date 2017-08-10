---
title: 'Azure Active Directory B2C: configurazione di WeChat | Documentazione Microsoft'
description: Fornire l'iscrizione e l'accesso agli utenti con account WeChat nelle applicazioni protette da Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: d2424c66-ba68-4d82-847e-d137683527b0
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/26/2017
ms.author: parakhj
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: a54aec23d951610118246e9f70cdd27752ef39a6
ms.contentlocale: it-it
ms.lasthandoff: 08/09/2017

---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-wechat-accounts"></a>Azure Active Directory B2C: fornire l'iscrizione e l'accesso agli utenti con account WeChat

> [!NOTE]
> Questa funzionalità è in anteprima.
> 

## <a name="create-a-wechat-application"></a>Creare un'applicazione WeChat

Per usare WeChat come provider di identità in Azure Active Directory (Azure AD) B2C, è necessario creare un'applicazione WeChat e inserire i parametri corretti. Per eseguire questa operazione è necessario un account WeChat. Se non si dispone di un account, è possibile ottenerne uno eseguendo l'iscrizione tramite una delle App per dispositivi mobili o usano il numero QQ. Ottenere quindi l'account registrato con il programma per sviluppatori WeChat. Altre informazioni sono disponibili [qui](http://kf.qq.com/faq/161220Brem2Q161220uUjERB.html).

### <a name="register-a-wechat-application"></a>Registrare un'applicazione WeChat

1. Passare a [https://open.weixin.qq.com/](https://open.weixin.qq.com/) ed eseguire l'accesso.
2. Fare clic su **管理中心** (Centro di gestione).
3. Seguire i passaggi necessari per registrare una nuova applicazione.
4. Per **授权回调域** (URL di callback) immettere `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`. Ad esempio, se `tenant_name` è contoso.onmicrosoft.com, impostare l'URL a `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
5. Trovare e copiare l'**ID APP** e la **CHIAVE APP**, che verranno usati in seguito.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Configurare WeChat come provider di identità nel tenant,
1. Seguire questa procedura per [passare al pannello delle funzionalità B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) nel portale di Azure.
2. Nel pannello delle funzionalità di B2C, fare clic su **Provider di identità**.
3. Fare clic su **+Aggiungi** nella parte superiore del pannello.
4. Fornire un **Nome** per la configurazione del provider di identità. ad esempio, immettere "WeChat".
5. Fare clic su **Tipo di provider di identità**, selezionare **WeChat** e fare clic su **OK**.
6. Fare clic su **Configura questo provider di identità**
7. Immettere la **chiave app** copiata in precedenza come **ID client**.
8. Immettere il **segreto app** copiato in precedenza come **segreto client**.
9. Fare clic su **OK** e su **Crea** per salvare la configurazione di WeChat.


