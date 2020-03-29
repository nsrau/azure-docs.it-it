---
title: Configurare l'iscrizione e l'accesso con un account QQ tramite Azure Active Directory B2C
description: Consentire l'iscrizione e l'accesso ai clienti con account QQ alle applicazioni da Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b0f15124c64e5cca54112987d486ddadaca79452
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187988"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account QQ tramite Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-qq-application"></a>Creare un'applicazione QQ

Per usare un account QQ come provider di identità in Azure Active Directory B2C (Azure AD B2C), è necessario creare un'applicazione nel tenant che lo rappresenta. Se non si dispone già di un account QQ, è possibile iscriversi a [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033).

### <a name="register-for-the-qq-developer-program"></a>Registrazione per il programma per sviluppatori QQ

1. Accedere al [portale per sviluppatori QQ](http://open.qq.com) con le credenziali dell'account QQ.
1. Dopo aver effettuato [https://open.qq.com/reg](https://open.qq.com/reg) l'accesso, vai a registrarti come sviluppatore.
1. Selezionare **个人** (sviluppatore singolo).
1. Immettere le informazioni necessarie e quindi fare clic su **下一步** (passaggio successivo).
1. Questa operazione completerà il processo di verifica email. È necessario attendere l'approvazione per alcuni giorni dopo la registrazione come sviluppatore.

### <a name="register-a-qq-application"></a>Registrare un'applicazione QQ

1. Andare [https://connect.qq.com/index.html](https://connect.qq.com/index.html)a .
1. Selezionare **应用管理** (gestione app).
1. Selezionare**创建应用**(crea app) e immettere le informazioni necessarie.
1. Immettere `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp` per **授权回调域** (URL callback). Ad esempio, se `tenant_name` è contoso, impostare l'URL su `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
1. Selezionare **创建应用** (crea app).
1. Nella pagina di conferma selezionare **应用管理** (gestione app) per tornare alla pagina di gestione delle app.
1. Selezionare **查看** (visualizza) accanto all'app appena creata.
1. Selezionare**修改**(modifica).
1. Copiare l'**ID APP** e la **CHIAVE APP**. Entrambi questi valori sono necessari per aggiungere il provider di identità nel tenant.

## <a name="configure-qq-as-an-identity-provider"></a>Configurare QQ come provider di identità

1. Accedere al [portale](https://portal.azure.com/)di Azure .
1. Selezionare l'icona **Directory - Sottoscrizione** nella barra degli strumenti del portale e quindi selezionare la directory che contiene il tenant B2C di Azure AD.
1. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
1. Selezionare **Provider di identità**, quindi **QQ (Anteprima)**.
1. Immettere un **Nome**. Ad esempio, *QQ*.
1. Per ID **client**immettere l'ID APP dell'applicazione QQ creata in precedenza.
1. Per il **segreto client**, immettere la chiave APP registrata.
1. Selezionare **Salva**.
