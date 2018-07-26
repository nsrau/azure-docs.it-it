---
title: Configurare l'iscrizione e l'accesso con un account GitHub tramite Azure Active Directory B2C | Microsoft Docs
description: Consentire l'iscrizione e l'accesso ai clienti con account GitHub alle applicazioni da Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 88fffd28319101c112f848eebc6e8ee27f7f863e
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952019"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account GitHub tramite Azure Active Directory B2C

> [!NOTE]
> Questa funzionalità è in anteprima.
> 

Per usare un account GitHub come provider di identità in Azure Active Directory (Azure AD) B2C, è necessario creare nel tenant un'applicazione che lo rappresenti. Se non si possiede già un account GitHub, è possibile crearlo sul sito [https://www.github.com/](https://www.github.com/).

## <a name="create-a-github-oauth-application"></a>Creare un'applicazione OAuth per GitHub

1. Accedere al sito Web [GitHub Developer](https://github.com/settings/developers) con le credenziali di GitHub.
2. Selezionare **OAuth Apps** (App OAuth), quindi selezionare **Register a new application** (Registra una nuova applicazione).
3. Immettere **Application name** (Nome applicazione) e **Homepage URL** (URL della home page) personale.
4. Immettere il valore `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` in **Authorization callback URL** (URL callback di autorizzazione). Sostituire **{tenant}** con il nome del tenant di Azure AD B2C, ad esempio contosob2c.onmicrosoft.com.
5. Fare clic su **Register application**.
6. Copiare i valori **ID client** e **Segreto client**. Entrambi sono necessari per aggiungere il provider di identità nel tenant.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Configurare un account GitHub come provider di identità

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
2. Assicurarsi di usare la directory contenente il tenant Azure AD B2C passando a tale directory nell'angolo in alto a destra del portale di Azure. Selezionare le informazioni sulla sottoscrizione e quindi selezionare **Cambia directory**. 

    ![Passare al tenant di Azure AD B2C](./media/active-directory-b2c-setup-github-app/switch-directories.png)

    Scegliere la directory contenente il tenant.

    ![Selezionare la directory](./media/active-directory-b2c-setup-github-app/select-directory.png)

3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
4. Selezionare **Provider di identità** e quindi selezionare **Aggiungi**.
5. Specificare un **Nome**. Ad esempio, immettere *GitHub*.
6. Fare clic su **Tipo di provider di identità**, selezionare **GitHub (anteprima)** e fare clic su **OK**.
7. Selezionare **Set up this identity provider** (Configura provider di identità), immettere l'ID Client registrato in precedenza in **ID Client** e immettere il segreto Client registrato come **Segreto client** dell'applicazione dell'account GitHub creata in precedenza.
8. Fare clic su **OK** e su **Crea** per salvare la configurazione dell'account GitHub.