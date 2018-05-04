---
title: Configurazione di Twitter per Azure AD B2C | Microsoft Docs
description: Consentire la registrazione e l'accesso ai clienti con account Twitter alle applicazioni protette da Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 4/17/2018
ms.author: davidmu
ms.openlocfilehash: 40e4c5549414765dabc6f37c5ffb5aea519ae673
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2018
---
# <a name="provide-sign-up-and-sign-in-to-consumers-with-twitter-accounts-using-azure-ad-b2c"></a>Consentire la registrazione e l'accesso ai consumer con account Twitter tramite Azure AD B2C

## <a name="create-a-twitter-application"></a>Creare un'applicazione Twitter
Per usare Twitter come provider di identità in Azure Active Directory (Azure AD) B2C, si deve creare un'applicazione Twitter e inserire i parametri corretti. Per eseguire questa operazione è necessario un account Twitter. Se non si ha un account, è possibile crearlo nel sito [https://twitter.com/signup](https://twitter.com/signup).

1. Passare a [Twitter Apps](https://apps.twitter.com/) e accedere con le proprie credenziali.
2. Fare clic su **Create New App**. 
3. Nel modulo immettere i valori desiderati per **Nome**, **Descrizione** e **Sito Web**.
4. Per **URL callback** immettere `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`. Accertarsi di sostituire **{tenant}** con il nome del tenant, ad esempio contosob2c.onmicrosoft.com.
5. Selezionare la casella per accettare il **contratto per gli sviluppatori** e fare clic su **Create your Twitter application** (Crea applicazione Twitter).
6. Dopo che l'app è stata creata, fare clic su **Keys and Access Tokens** (Chiavi e token di accesso).
7. Copiare il valore di **Chiave utente** e **Segreto consumer**. Sono necessari entrambi per configurare Twitter come provider di identità nel tenant.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Configurare Twitter come provider di identità nel tenant,
1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C. 
2. Per passare al tenant di Azure AD B2C, selezionare la directory Azure AD B2C nell'angolo superiore destro del portale.
3. Fare clic su **Tutti i servizi** e quindi selezionare **Azure AD B2C** dall'elenco dei servizi in **Sicurezza e identità**.
4. Fare clic su **Provider di identità**.
4. Fornire un **Nome** per la configurazione del provider di identità. ad esempio, immettere "Twitter".
5. Fare clic su **Tipo di provider di identità**, selezionare **Twitter (anteprima)** e fare clic su **OK**.
6. Fare clic su **Configura questo provider di identità** e immettere la **Chiave utente** di Twitter per **ID client** e il **Segreto consumer** di Twitter per **Segreto client**.
7. Fare clic su **OK** e su **Crea** per salvare la configurazione di Twitter.

## <a name="next-steps"></a>Passaggi successivi

Creare o modificare un [criterio predefinito](active-directory-b2c-reference-policies.md) e aggiungere Twitter come provider di identità.