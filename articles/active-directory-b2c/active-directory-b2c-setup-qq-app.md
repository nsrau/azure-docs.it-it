---
title: 'Azure Active Directory B2C: configurazione di QQ | Documentazione Microsoft'
description: Fornire l'iscrizione e l'accesso agli utenti con account QQ nelle applicazioni protette da Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: mtillman
editor: parakhj
ms.assetid: 18c2cf94-8004-4de1-81c2-e45be65ce12d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/26/2017
ms.author: parakhj
ms.openlocfilehash: d4cc26d4f206baf9137feae0825b1f9fa5a7c8d6
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-qq-accounts"></a>Azure Active Directory B2C: fornire l'iscrizione e l'accesso agli utenti con account QQ

> [!NOTE]
> Questa funzionalità è in anteprima.
> 

## <a name="create-a-qq-application"></a>Creare un'applicazione QQ

Per usare QQ come provider di identità in Azure Active Directory (Azure AD) B2C, è necessario creare un'applicazione QQ e inserire i parametri corretti. Per eseguire questa operazione è necessario disporre di un account QQ. Se non si ha un account, è possibile ottenerne uno nel sito [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033).

### <a name="register-for-the-qq-developer-program"></a>Registrazione per il programma per sviluppatori QQ

1. Passare al [portale per sviluppatori QQ](http://open.qq.com) e accedere con le credenziali dell'account QQ.
2. Dopo l'accesso, passare a [http://open.qq.com/reg](http://open.qq.com/reg) per registrarsi come sviluppatore.
3. Nel menu selezionare**个人** (Singolo sviluppatore).
4. Immettere le informazioni necessarie nel modulo e fare clic su**下一步** (Passaggio successivo).
5. Questa operazione completerà il processo di verifica email.

> [!NOTE]
> È necessario attendere l'approvazione per alcuni giorni dopo la registrazione come sviluppatore. 

### <a name="register-a-qq-application"></a>Registrare un'applicazione QQ

1. Passare a [https://connect.qq.com/index.html](https://connect.qq.com/index.html).
2. Fare clic su **应用管理** (Gestione app).
3. Fare clic su **创建应用** (Crea app).
4. Immettere le informazioni di connessione necessarie.
5. Fare clic su **创建应用** (Crea app).
6. Immettere le informazioni necessarie.
7. Per il campo **授权回调域** (URL di callback) immettere `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp`. Ad esempio, se `tenant_name` è contoso.onmicrosoft.com, impostare l'URL a `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
8. Fare clic su **创建应用** (Crea app).
9. Nella pagina di conferma fare clic su**应用管理** (Gestione app) per tornare alla pagina di gestione delle app.
10. Fare clic su **查看**(Visualizza) accanto all'app appena creata.
11. Fare clic su **修改** (Modifica).
12. Nella parte superiore della pagina copiare l'**ID APP** e la **CHIAVE APP**.

## <a name="configure-qq-as-an-identity-provider-in-your-tenant"></a>Configurare QQ come provider di identità nel tenant
1. Seguire questa procedura per [passare al pannello delle funzionalità B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) nel portale di Azure.
2. Nel pannello delle funzionalità di B2C, fare clic su **Provider di identità**.
3. Fare clic su **+Aggiungi** nella parte superiore del pannello.
4. Fornire un **Nome** per la configurazione del provider di identità. Ad esempio, immettere "QQ".
5. Fare clic su **Tipo di provider di identità**, selezionare **QQ** e fare clic su **OK**.
6. Fare clic su **Configura questo provider di identità**
7. Immettere la **chiave app** copiata in precedenza come **ID client**.
8. Immettere il **segreto app** copiato in precedenza come **segreto client**.
9. Fare clic su **OK** e su **Crea** per salvare la configurazione di QQ.

