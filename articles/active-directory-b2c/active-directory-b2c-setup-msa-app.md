---
title: 'Azure Active Directory B2C: Configurazione dell&quot;account Microsoft | Documentazione Microsoft'
description: Fornire la registrazione e l&quot;accesso agli utenti con account Microsoft nelle applicazioni protette da Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 06407322-142c-4cb3-9106-a8d752c4c853
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: e37c48d6c92a8a2cd480458abdff0a3a1ca9338f
ms.openlocfilehash: 866d2b54b0b517cb9984fd5e98840fe3a6c69965


---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-microsoft-accounts"></a>Azure Active Directory B2C: Fornire la registrazione e l'accesso agli utenti con account Microsoft
## <a name="create-a-microsoft-account-application"></a>Creare un'applicazione di account Microsoft
Per usare un account Microsoft come provider di identità in Azure Active Directory (Azure AD) B2C, è necessario creare un'applicazione di account Microsoft e inserire i parametri corretti. Per eseguire questa operazione è necessario disporre di un account Microsoft. Se non si ha un account, è possibile crearlo nel sito [https://www.live.com/](https://www.live.com/).

1. Entrare nel [portale di registrazione delle applicazioni Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) e accedere con le credenziali del proprio account Microsoft.
2. Fare clic su **Aggiungi un'app**.
   
    ![Account Microsoft - Aggiungere una nuova app](./media/active-directory-b2c-setup-msa-app/msa-add-new-app.png)
3. Specificare un **Nome** per l'applicazione e fare clic su **Crea applicazione**.
   
    ![Account Microsoft - Nome applicazione](./media/active-directory-b2c-setup-msa-app/msa-app-name.png)
4. Copiare il valore di **ID applicazione**. Sarà necessario per configurare un account Microsoft come provider di identità nel tenant.
   
    ![Account Microsoft - ID applicazione](./media/active-directory-b2c-setup-msa-app/msa-app-id.png)
5. Fare clic su **Aggiungi piattaforma** e scegliere **Web**.
   
    ![Account Microsoft - Aggiungi piattaforma](./media/active-directory-b2c-setup-msa-app/msa-add-platform.png)
   
    ![Account Microsoft - Web](./media/active-directory-b2c-setup-msa-app/msa-web.png)
6. Immettere `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` nel campo **URI di reindirizzamento** . Sostituire **{tenant}** con il nome del tenant, ad esempio contosob2c.onmicrosoft.com.
   
    ![Account Microsoft - URL di reindirizzamento](./media/active-directory-b2c-setup-msa-app/msa-redirect-url.png)
7. Fare clic su **Genera nuova password** nella sezione **Segreti applicazione**. Copiare la nuova password visualizzata sullo schermo. Sarà necessario per configurare un account Microsoft come provider di identità nel tenant. La password è una credenziale di sicurezza importante.
   
    ![Account Microsoft - Genera nuova password](./media/active-directory-b2c-setup-msa-app/msa-generate-new-password.png)
   
    ![Account Microsoft - Nuova password](./media/active-directory-b2c-setup-msa-app/msa-new-password.png)
8. Selezionare la casella **Supporto Live SDK** nella sezione **Opzioni avanzate**. Fare clic su **Save**.
   
    ![Account Microsoft - Supporto Live SDK](./media/active-directory-b2c-setup-msa-app/msa-live-sdk-support.png)

## <a name="configure-microsoft-account-as-an-identity-provider-in-your-tenant"></a>Configurare un account Microsoft come provider di identità nel tenant
1. Seguire questa procedura per [passare al pannello delle funzionalità B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) nel portale di Azure.
2. Nel pannello delle funzionalità di B2C, fare clic su **Provider di identità**.
3. Fare clic su **+Aggiungi** nella parte superiore del pannello.
4. Fornire un **Nome** per la configurazione del provider di identità. Ad esempio, immettere "MSA".
5. Fare clic su **Tipo di provider di identità**, selezionare **Account Microsoft** e fare clic su **OK**.
6. Fare clic su **Set up this identity provider** (Configura il provider di identità) e immettere l'ID applicazione e la password dell'applicazione dell'account Microsoft creato in precedenza.
7. Fare clic su **OK** e quindi su **Crea** per salvare la configurazione dell'account Microsoft.




<!--HONumber=Dec16_HO4-->


