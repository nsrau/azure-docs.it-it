---
title: Configurare l'iscrizione e l'accesso con un account Google-Azure Active Directory B2C
description: Consentire l'iscrizione e l'accesso ai clienti con account Google alle applicazioni da Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7286bf1fd51883587aa41dc69d5dae0a3e6fb824
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622419"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account Google tramite Azure Active Directory B2C

## <a name="create-a-google-application"></a>Creazione di un'applicazione Google

Per usare un account Google come [provider di identità](active-directory-b2c-reference-oauth-code.md) in Azure Active Directory (Azure AD) B2C, è necessario creare nel tenant un'applicazione che lo rappresenti. Se non si ha già un account Google, è possibile iscriversi [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp)all'indirizzo.

1. Accedere alla [Google Developers Console](https://console.developers.google.com/) con le credenziali dell'account Google.
1. Nell'angolo superiore sinistro della pagina selezionare l'elenco progetto, quindi selezionare **nuovo progetto**.
1. Immettere un **Nome progetto**, fare clic su **Crea**e quindi assicurarsi di usare il nuovo progetto.
1. Selezionare **Credenziali** nel menu a sinistra e quindi selezionare **Crea credenziali di accesso** > **ID client Oauth**.
1. In **Tipo di applicazione** selezionare **Applicazione Web**.
1. Fornire un **Nome** per l'applicazione, immettere `https://your-tenant-name.b2clogin.com` nel campo **Origini JavaScript autorizzate** e `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` nel campo **URI di reindirizzamento autorizzati**. Sostituire `your-tenant-name` con il nome del tenant. È necessario usare lettere minuscole quando si immette il nome del tenant, anche se questo viene definito con lettere maiuscole in Azure AD B2C.
1. Fare clic su **Create**(Crea).
1. Copiare i valori **ID client** e **Segreto client**. Sono necessari entrambi per configurare Google come provider di identità nel tenant. **Segreto client** è una credenziale di sicurezza importante.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Configurare un account Google come provider di identità

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C selezionando il filtro **directory + sottoscrizione** nel menu in alto e scegliendo la directory che contiene il tenant.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
1. Selezionare **provider di identità**, quindi selezionare **Google**.
1. Immettere un **Nome**. Ad esempio, *Google*.
1. Per **ID client**, immettere l'ID client dell'applicazione Google creata in precedenza.
1. Per il **segreto client**, immettere il segreto client registrato.
1. Selezionare **Salva**.
