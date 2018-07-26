---
title: Configurare l'iscrizione e l'accesso con un account Twitter tramite Azure Active Directory B2C | Microsoft Docs
description: Consentire l'iscrizione e l'accesso ai clienti con account Twitter alle applicazioni da Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: bf5ae39d83fd021775fbd18cf23d2e6b9078e748
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "37927897"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account Twitter tramite Azure Active Directory B2C

## <a name="create-a-twitter-application"></a>Creare un'applicazione Twitter

Per usare un account Twitter come provider di identità in Azure Active Directory (Azure AD) B2C, è necessario creare nel tenant un'applicazione che lo rappresenti. Se non si possiede già un account Twitter, è possibile crearlo sul sito [https://twitter.com/signup](https://twitter.com/signup).

1. Accedere alle [app Twitter](https://apps.twitter.com/) con le proprie credenziali.
2. Selezionare **Crea nuova app**.
3. Compilare i campi **Nome**, **Descrizione** e **Sito Web**.
4. Immettere `https://login.microsoftonline.com/te/{tenant}/{policyId}/oauth1/authresp` negli **URL callback**. Sostituire **{tenant}** con il nome del tenant, ad esempio contosob2c.onmicrosoft.com e **{policyId}** con l'ID criterio, ad esempio b2c_1_policy. È consigliabile aggiungere un URL callback per tutti i criteri che usano l'account Twitter. Assicurarsi di impostare `b2clogin.com` invece di ` login.microsoftonline.com` se viene usato nell'applicazione.
5. Accettare il **contratto per gli sviluppatori** e fare clic su **Create your Twitter application** (Crea applicazione Twitter).
7. Selezionare la scheda **Keys and Access Tokens** .
8. Copiare il valore di **Chiave utente** e **Segreto consumer**. Sono necessari entrambi per configurare un account Twitter come provider di identità nel tenant.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Configurare Twitter come provider di identità nel tenant,

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
2. Assicurarsi di usare la directory contenente il tenant Azure AD B2C passando a tale directory nell'angolo in alto a destra del portale di Azure. Selezionare le informazioni sulla sottoscrizione e quindi selezionare **Cambia directory**. 

    ![Passare al tenant di Azure AD B2C](./media/active-directory-b2c-setup-twitter-app/switch-directories.png)

    Scegliere la directory contenente il tenant.

    ![Selezionare la directory](./media/active-directory-b2c-setup-twitter-app/select-directory.png)

3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
4. Selezionare **Provider di identità** e quindi selezionare **Aggiungi**.
5. Specificare un **Nome**. Ad esempio, immettere *Twitter*.
6. Fare clic su **Tipo di provider di identità**, selezionare **Twitter** e fare clic su **OK**.
7. Fare clic su **Configura questo provider di identità** e immettere la Chiave utente per l'**ID client** e il **Segreto consumer** per **Segreto client**.
8. Fare clic su **OK** e su **Crea** per salvare la configurazione di Twitter.