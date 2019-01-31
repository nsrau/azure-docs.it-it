---
title: Configurare l'iscrizione e l'accesso con un account Google tramite Azure Active Directory B2C | Microsoft Docs
description: Consentire l'iscrizione e l'accesso ai clienti con account Google alle applicazioni da Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: af8a727bf9733b744b1ed429420cd8fde6e3e6f1
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55176132"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account Google tramite Azure Active Directory B2C

## <a name="create-a-google-application"></a>Creazione di un'applicazione Google

Per usare un account Google come provider di identità in Azure Active Directory (Azure AD) B2C, è necessario creare nel tenant un'applicazione che lo rappresenti. Se non si possiede già un account Google, è possibile crearlo sul sito [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Accedere alla [Google Developers Console](https://console.developers.google.com/) con le credenziali dell'account Google.
2. Selezionare **Crea progetto** e quindi fare clic su **Crea**. Se sono stati già creati altri progetti, selezionare l'elenco dei progetti e quindi selezionare **Nuovo progetto**.
3. Immettere un **Nome progetto**, fare clic su **Crea**e quindi assicurarsi di usare il nuovo progetto.
3. Selezionare **Credenziali** nel menu a sinistra e quindi selezionare **Crea credenziali di accesso** > **ID client Oauth**.
4. Selezionare **Configure consent screen** (Configura schermata di consenso).
5. Selezionare o specificare un **indirizzo di posta elettronica**, inserire il **nome del prodotto visualizzato agli utenti**, aggiungere `b2clogin.com` in **Domini autorizzati** e fare clic su **Salva**.
6. In **Tipo di applicazione** selezionare **Applicazione Web**.
7. Fornire un **Nome** per l'applicazione, immettere `https://your-tenant-name.b2clogin.com` nel campo **Origini JavaScript autorizzate** e `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` nel campo **URI di reindirizzamento autorizzati**. Sostituire `your-tenant-name` con il nome del tenant. È necessario usare lettere minuscole quando si immette il nome del tenant, anche se questo viene definito con lettere maiuscole in Azure AD B2C.
8. Fare clic su **Create**(Crea).
9. Copiare i valori **ID client** e **Segreto client**. Sono necessari entrambi per configurare Google come provider di identità nel tenant. **Segreto client** è una credenziale di sicurezza importante.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Configurare un account Google come provider di identità

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul **filtro delle directory e delle sottoscrizioni** nel menu in alto e scegliere la directory che contiene il tenant.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
4. Selezionare **Provider di identità** e quindi selezionare **Aggiungi**.
5. Immettere un **Nome**. Ad esempio, immettere *Google*.
6. Fare clic su **Tipo di provider di identità**, selezionare **Google** e fare clic su **OK**.
7. Selezionare **Set up this identity provider** (Configura provider di identità), immettere l'ID Client registrato in precedenza in **ID Client** e immettere il segreto Client registrato come **Segreto client** dell'applicazione Google creata in precedenza.
8. Fare clic su **OK** e su **Crea** per salvare la configurazione di Google.

