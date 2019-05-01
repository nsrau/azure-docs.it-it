---
title: Configurare l'iscrizione e l'accesso con un account QQ tramite Azure Active Directory B2C | Microsoft Docs
description: Consentire l'iscrizione e l'accesso ai clienti con account QQ alle applicazioni da Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 6eba804fc96a91d17644c903e1462c31c0fc9149
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64704170"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account QQ tramite Azure Active Directory B2C

> [!NOTE]
> Questa funzionalità è in anteprima.
> 

## <a name="create-a-qq-application"></a>Creare un'applicazione QQ

Per usare un account QQ come provider di identità in Azure Active Directory (Azure AD) B2C, è necessario creare nel tenant un'applicazione che lo rappresenti. Se non si possiede già un account QQ, è possibile crearlo sul sito [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033).

### <a name="register-for-the-qq-developer-program"></a>Registrazione per il programma per sviluppatori QQ

1. Accedere al [portale per sviluppatori QQ](http://open.qq.com) con le credenziali dell'account QQ.
2. Dopo l'accesso passare a [http://open.qq.com/reg](http://open.qq.com/reg) per registrarsi come sviluppatore.
3. Selezionare **个人** (sviluppatore singolo).
4. Immettere le informazioni necessarie e quindi fare clic su **下一步** (passaggio successivo).
5. Questa operazione completerà il processo di verifica email. È necessario attendere l'approvazione per alcuni giorni dopo la registrazione come sviluppatore. 

### <a name="register-a-qq-application"></a>Registrare un'applicazione QQ

1. Passare a [https://connect.qq.com/index.html](https://connect.qq.com/index.html).
2. Selezionare **应用管理** (gestione app).
5. Selezionare**创建应用**(crea app) e immettere le informazioni necessarie.
7. Immettere `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp` per **授权回调域** (URL callback). Ad esempio, se `tenant_name` è contoso, impostare l'URL su `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
8. Selezionare **创建应用** (crea app).
9. Nella pagina di conferma selezionare **应用管理** (gestione app) per tornare alla pagina di gestione delle app.
10. Selezionare **查看** (visualizza) accanto all'app appena creata.
11. Selezionare**修改**(modifica).
12. Copiare l'**ID APP** e la **CHIAVE APP**. Entrambi questi valori sono necessari per aggiungere il provider di identità nel tenant.

## <a name="configure-qq-as-an-identity-provider"></a>Configurare QQ come provider di identità

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul **filtro delle directory e delle sottoscrizioni** nel menu in alto e scegliere la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
4. Selezionare **Provider di identità** e quindi selezionare **Aggiungi**.
5. Specificare un **Nome**. Ad esempio, immettere *QQ*.
6. Fare clic su **Tipo di provider di identità**, selezionare **QQ (anteprima)** e fare clic su **OK**.
7. Selezionare **Set up this identity provider** (Configura provider di identità), immettere l'ID APP registrato in precedenza in **ID Client** e immettere la Chiave APP registrata come **Segreto client** dell'applicazione QQ creata in precedenza.
8. Fare clic su **OK** e su **Crea** per salvare la configurazione di QQ.

