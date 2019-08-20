---
title: Configurare l'iscrizione e l'accesso con un account QQ tramite Azure Active Directory B2C
description: Consentire l'iscrizione e l'accesso ai clienti con account QQ alle applicazioni da Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 11fc41374089c0ab7258dd191a255387713836eb
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622312"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account QQ tramite Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-qq-application"></a>Creare un'applicazione QQ

Per usare un account QQ come provider di identità in Azure Active Directory (Azure AD) B2C, è necessario creare nel tenant un'applicazione che lo rappresenti. Se non si ha già un account QQ, è possibile iscriversi all' [https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033)indirizzo.

### <a name="register-for-the-qq-developer-program"></a>Registrazione per il programma per sviluppatori QQ

1. Accedere al [portale per sviluppatori QQ](http://open.qq.com) con le credenziali dell'account QQ.
1. Dopo l'accesso passare a [http://open.qq.com/reg](http://open.qq.com/reg) per registrarsi come sviluppatore.
1. Selezionare **个人** (sviluppatore singolo).
1. Immettere le informazioni necessarie e quindi fare clic su **下一步** (passaggio successivo).
1. Questa operazione completerà il processo di verifica email. È necessario attendere l'approvazione per alcuni giorni dopo la registrazione come sviluppatore.

### <a name="register-a-qq-application"></a>Registrare un'applicazione QQ

1. Passare a [https://connect.qq.com/index.html](https://connect.qq.com/index.html).
1. Selezionare **应用管理** (gestione app).
1. Selezionare**创建应用**(crea app) e immettere le informazioni necessarie.
1. Immettere `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp` per **授权回调域** (URL callback). Ad esempio, se `tenant_name` è contoso, impostare l'URL su `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.
1. Selezionare **创建应用** (crea app).
1. Nella pagina di conferma selezionare **应用管理** (gestione app) per tornare alla pagina di gestione delle app.
1. Selezionare **查看** (visualizza) accanto all'app appena creata.
1. Selezionare**修改**(modifica).
1. Copiare l'**ID APP** e la **CHIAVE APP**. Entrambi questi valori sono necessari per aggiungere il provider di identità nel tenant.

## <a name="configure-qq-as-an-identity-provider"></a>Configurare QQ come provider di identità

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C selezionando il filtro **directory + sottoscrizione** nel menu in alto e scegliendo la directory che contiene il tenant.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
1. Selezionare **provider di identità**, quindi selezionare **QQ (anteprima)** .
1. Immettere un **Nome**. Ad esempio, *QQ*.
1. Per **ID client**immettere l'ID app dell'applicazione QQ creata in precedenza.
1. Per il **segreto client**, immettere la chiave dell'app registrata.
1. Selezionare **Salva**.
