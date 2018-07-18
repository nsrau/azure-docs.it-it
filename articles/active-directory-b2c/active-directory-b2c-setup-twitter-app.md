---
title: Configurazione di Twitter per Azure Active Directory B2C | Microsoft Docs
description: Consentire la registrazione e l'accesso ai clienti con account Twitter alle applicazioni protette da Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 4/17/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 68b9e9edbacda08bc98b6b7af5d944d3df9edca1
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34709581"
---
# <a name="provide-sign-up-and-sign-in-to-consumers-with-twitter-accounts-using-azure-ad-b2c"></a>Consentire la registrazione e l'accesso ai consumer con account Twitter tramite Azure AD B2C

## <a name="create-a-twitter-application"></a>Creare un'applicazione Twitter
Per usare Twitter come provider di identità in Azure Active Directory (Azure AD) B2C, si deve creare un'applicazione Twitter e inserire i parametri corretti. Per eseguire questa operazione è necessario un account Twitter. Se non si ha un account, è possibile crearlo nel sito [https://twitter.com/signup](https://twitter.com/signup).

1. Passare a [Twitter Apps](https://apps.twitter.com/) e accedere con le proprie credenziali.
2. Fare clic su **Create New App**. 
3. Nel modulo immettere i valori desiderati per **Nome**, **Descrizione** e **Sito Web**.
4. Per **URL callback** immettere `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`. Accertarsi di sostituire **{tenant}** con il nome del tenant, ad esempio contosob2c.onmicrosoft.com.
5. Selezionare la casella per accettare il **contratto per gli sviluppatori** e fare clic su **Create your Twitter application** (Crea applicazione Twitter).
6. Dopo aver creato l'app, selezionarla nell'elenco, quindi selezionare la scheda **Impostazioni**.
7. Deselezionare la casella **Enable Callback Locking** (Attiva blocco callback), quindi fare clic su **Aggiorna impostazioni**.
8. Selezionare la scheda **Keys and Access Tokens** .
9. Copiare il valore di **Chiave utente** e **Segreto consumer**. Sono necessari entrambi per configurare Twitter come provider di identità nel tenant.

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