---
title: Configurare l'iscrizione e l'accesso con un account LinkedIn - Azure Active Directory B2C | Microsoft Docs
description: Consentire l'iscrizione e l'accesso ai clienti con account LinkedIn alle applicazioni da Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5ffe75a58a8cfb2409af3a65ff822023d7ccf5d4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508479"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account LinkedIn tramite Azure Active Directory B2C

## <a name="create-a-linkedin-application"></a>Creare un'applicazione su LinkedIn

Per usare un account LinkedIn come [provider di identità](active-directory-b2c-reference-oauth-code.md) in Azure Active Directory (Azure AD) B2C, è necessario creare nel tenant un'applicazione che lo rappresenti. Se non si possiede già un account LinkedIn, è possibile crearlo sul sito [https://www.linkedin.com/](https://www.linkedin.com/).

1. Accedere al [sito Web di sviluppatori LinkedIn](https://www.developer.linkedin.com/) con le credenziali dell'account LinkedIn.
2. Selezionare **My Apps** (App personali) e quindi **Crea applicazione**.
3. Immettere **Nome società**, **Nome applicazione**, **Descrizione applicazione**, **Logo applicazione**, **Uso applicazione** , **URL del sito Web**, **Indirizzo di posta elettronica aziendale** e **Telefono (uff.)** .
4. Accettare il documento **LinkedIn API Terms of Use** (Condizioni d'uso dell'API LinkedIn) e fare clic su **Submit** (Invia).
5. Copiare i valori **ID client** e **Segreto client**. Si trovano nella sezione **Authentication Keys** (Chiavi di autenticazione) Sono necessari entrambi per configurare LinkedIn come provider di identità nel tenant. **Client Segreto** è un'importante credenziale di sicurezza.
6. Immettere `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` negli **URL di reindirizzamento autorizzati**. Sostituire `your-tenant-name` con il nome del tenant. È necessario usare lettere minuscole quando si immette il nome del tenant, anche se questo viene definito con lettere maiuscole in Azure AD B2C. Fare clic su **Aggiungi** e quindi su **Aggiorna**.

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>Configurare un account LinkedIn come provider di identità

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul **filtro delle directory e delle sottoscrizioni** nel menu in alto e scegliere la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
4. Selezionare **Provider di identità** e quindi selezionare **Aggiungi**.
5. Specificare un **Nome**. Immettere ad esempio *LinkedIn*.
6. Fare clic su **Tipo di provider di identità**, selezionare **LinkedIn** e fare clic su **OK**.
7. Selezionare **Set up this identity provider** (Configura provider di identità), immettere l'ID Client registrato in precedenza in **ID Client** e immettere il segreto Client registrato come **Segreto client** dell'applicazione dell'account LinkedIn creata in precedenza.
8. Fare clic su **OK** e quindi su **Create** (Crea) per salvare la configurazione dell'account LinkedIn.

## <a name="migration-from-v10-to-v20"></a>Migrazione da v1.0, v2.0

LinkedIn recentemente [aggiornate le API da v1.0 v2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Come parte della migrazione, Azure AD B2C è solo in grado di ottenere il nome completo dell'utente LinkedIn durante l'iscrizione. Se un indirizzo di posta elettronica è uno degli attributi che vengono raccolti durante l'iscrizione, l'utente deve manualmente immettere l'indirizzo di posta elettronica e convalidarlo.
