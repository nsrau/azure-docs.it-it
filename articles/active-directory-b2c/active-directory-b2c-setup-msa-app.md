---
title: Configurare l'iscrizione e l'accesso con un account Microsoft tramite Azure Active Directory B2C | Microsoft Docs
description: Consentire l'iscrizione e l'accesso ai clienti con account Microsoft alle applicazioni da Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/05/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 16e4dbac4c8146b048d4d9b76544677a6111e2a5
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2018
ms.locfileid: "37900830"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account Microsoft tramite Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Creare un'applicazione di account Microsoft

Per usare un account Microsoft come provider di identità in Azure Active Directory (Azure AD) B2C, è necessario creare nel tenant un'applicazione che lo rappresenti. Se non si possiede già un account Microsoft, è possibile crearlo sul sito [https://www.live.com/](https://www.live.com/).

1. Accedere al [portale di registrazione delle applicazioni Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) con le credenziali del proprio account Microsoft.
2. Nell'angolo superiore destro selezionare **Aggiungi un'app**.
3. Specificare un **Nome** per l'applicazione e fare clic su **Crea**.
4. Nella pagina di registrazione, copiare il valore di **ID applicazione**. È necessario per configurare l'account Microsoft come provider di identità nel tenant.
5. Selezionare **Aggiungi piattaforma** e scegliere **Web**.
6. Immettere `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` in **URL di reindirizzamento**. Sostituire **{tenant}** con il nome del tenant, ad esempio contosob2c.onmicrosoft.com.
7. Fare clic su **Genera nuova password** in **Segreti applicazione**. Copiare la nuova password visualizzata sullo schermo. È necessario per configurare un account Microsoft come provider di identità nel tenant. La password è una credenziale di sicurezza importante.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Configurare un account Microsoft come provider di identità

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
2. Assicurarsi di usare la directory contenente il tenant Azure AD B2C passando a tale directory nell'angolo in alto a destra del portale di Azure. Selezionare le informazioni sulla sottoscrizione e quindi selezionare **Cambia directory**. 

    ![Passare al tenant di Azure AD B2C](./media/active-directory-b2c-setup-msa-app/switch-directories.png)

    Scegliere la directory contenente il tenant.

    ![Selezionare la directory](./media/active-directory-b2c-setup-msa-app/select-directory.png)

3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
4. Selezionare **Provider di identità** e quindi selezionare **Aggiungi**.
5. Specificare un **Nome**. Ad esempio, immettere *MSA*.
6. Fare clic su **Tipo di provider di identità**, selezionare **Account Microsoft** e fare clic su **OK**.
7. Selezionare **Set up this identity provider** (Configura provider di identità), immettere l'ID Applicazione registrato in precedenza in **ID Client** e immettere la password registrata come **Segreto client** dell'applicazione dell'account Microsoft creata in precedenza.
8. Fare clic su **OK** e quindi su **Crea** per salvare la configurazione dell'account Microsoft.

