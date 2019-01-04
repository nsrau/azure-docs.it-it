---
title: Configurare l'iscrizione e l'accesso con un account Twitter tramite Azure Active Directory B2C | Microsoft Docs
description: Consentire l'iscrizione e l'accesso ai clienti con account Twitter alle applicazioni da Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: f0f0b8e0cbb5fbab81a07a28a9d4a2c264be6545
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2018
ms.locfileid: "52719862"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account Twitter tramite Azure Active Directory B2C

## <a name="create-an-application"></a>Creare un'applicazione

Per usare Twitter come provider di identità in Azure AD B2C, è necessario creare un'applicazione Twitter. Se non si possiede già un account Twitter, è possibile crearlo sul sito [https://twitter.com/signup](https://twitter.com/signup).

1. Accedere al [sito Web di sviluppatori Twitter](https://developer.twitter.com/en/apps) con le credenziali dell'account Twitter.
2. Selezionare **Crea un'app**.
3. Immettere un  **Nome app** e una **Descrizione applicazione**.
4. In **URL del sito Web**, immettere `https://your-tenant.b2clogin.com`. Sostituire `your-tenant` con il nome del tenant. Ad esempio: https://contosob2c.b2clogin.com.
5. Per **URL callback** immettere `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`. Sostituire `your-tenant` con il nome del nome tenant e `your-user-flow-Id` con l'identificatore del flusso utente. Ad esempio: `b2c_1A_signup_signin_twitter`. È necessario usare lettere minuscole quando si immette il nome del tenant, anche se questo viene definito con lettere maiuscole in Azure AD B2C.
6. Nella parte inferiore della pagina leggere e accettare le condizioni, quindi fare clic su **Crea**.
7. Nella pagina **Dettagli app**, selezionare **Modifica > Modifica dettagli**, selezionare la casella **Enable Sign in with Twitter** (Consenti accesso con Twitter) e quindi selezionare **Salva**.
8. Selezionare **Keys and tokens** (Chiavi e token) e registrare i valori **Chiave API Consumer** e **Consumer API secret key** (Chiave segreto API Consumer) da usare in un secondo momento.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Configurare Twitter come provider di identità nel tenant,

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul **filtro delle directory e delle sottoscrizioni** nel menu in alto e scegliere la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
4. Selezionare **Provider di identità** e quindi selezionare **Aggiungi**.
5. Specificare un **Nome**. Ad esempio, immettere *Twitter*.
6. Fare clic su **Tipo di provider di identità**, selezionare **Twitter** e fare clic su **OK**.
7. Fare clic su **Configura questo provider di identità** e immettere la Chiave API per l'**ID client** e il Segreto API per **Segreto client**.
8. Fare clic su **OK** e su **Crea** per salvare la configurazione di Twitter.