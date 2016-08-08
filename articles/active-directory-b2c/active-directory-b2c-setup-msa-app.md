<properties
	pageTitle="Azure Active Directory B2C: Configurazione dell'account Microsoft | Microsoft Azure"
	description="Fornire la registrazione e l'accesso agli utenti con account Microsoft nelle applicazioni protette da Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/24/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C: Fornire la registrazione e l'accesso agli utenti con account Microsoft

## Creare un'applicazione di account Microsoft

Per usare un account Microsoft come provider di identità in Azure Active Directory (Azure AD) B2C, è necessario creare un'applicazione di account Microsoft e inserire i parametri corretti. Per eseguire questa operazione è necessario disporre di un account Microsoft. Se non si ha un account, è possibile crearlo nel sito [https://www.live.com/](https://www.live.com/).

1. Passare al [portale di registrazione delle applicazioni Microsoft](https://apps.dev.microsoft.com) e accedere con le credenziali del proprio account Microsoft.
2. Fare clic su **Aggiungi un'app**.

    ![Account Microsoft - Aggiungere una nuova app](./media/active-directory-b2c-setup-msa-app/msa-add-new-app.png)

3. Specificare un **Nome** per l'applicazione e fare clic su **Crea applicazione**.

    ![Account Microsoft - Nome applicazione](./media/active-directory-b2c-setup-msa-app/msa-app-name.png)

4. Copiare il valore di **ID applicazione**. Sarà necessario per configurare un account Microsoft come provider di identità nel tenant.

    ![Account Microsoft - ID applicazione](./media/active-directory-b2c-setup-msa-app/msa-app-id.png)

5. Fare clic su **Aggiungi piattaforma** e scegliere **Web**.

	![Account Microsoft - Aggiungi piattaforma](./media/active-directory-b2c-setup-msa-app/msa-add-platform.png)

	![Account Microsoft - Web](./media/active-directory-b2c-setup-msa-app/msa-web.png)

6. Immettere `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` nel campo **URI di reindirizzamento**. Sostituire **{tenant}** con il nome del tenant, ad esempio contosob2c.onmicrosoft.com.

    ![Account Microsoft - URL di reindirizzamento](./media/active-directory-b2c-setup-msa-app/msa-redirect-url.png)

7. Fare clic su **Genera nuova password** nella sezione **Segreti applicazione**. Copiare la nuova password visualizzata sullo schermo. Sarà necessario per configurare un account Microsoft come provider di identità nel tenant. La password è una credenziale di sicurezza importante.

	![Account Microsoft - Genera nuova password](./media/active-directory-b2c-setup-msa-app/msa-generate-new-password.png)

	![Account Microsoft - Nuova password](./media/active-directory-b2c-setup-msa-app/msa-new-password.png)

8. Selezionare la casella **Supporto Live SDK** nella sezione **Opzioni avanzate**. Fare clic su **Save**.

    ![Account Microsoft - Supporto Live SDK](./media/active-directory-b2c-setup-msa-app/msa-live-sdk-support.png)

## Configurare un account Microsoft come provider di identità nel tenant

1. Seguire questa procedura per [passare al pannello delle funzionalità B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) nel portale di Azure.
2. Nel pannello delle funzionalità di B2C, fare clic su **Provider di identità**.
3. Fare clic su **+Aggiungi** nella parte superiore del pannello.
4. Fornire un **Nome** per la configurazione del provider di identità. Ad esempio, immettere "MSA".
5. Fare clic su **Identity provider type** (Tipo di provider di identità), selezionare **Account Microsoft** e fare clic su **OK**.
6. Fare clic su **Set up this identity provider** (Configura il provider di identità) e immettere l'ID applicazione e la password dell'applicazione dell'account Microsoft creato in precedenza.
7. Fare clic su **OK** e quindi su **Crea** per salvare la configurazione dell'account Microsoft.

<!---HONumber=AcomDC_0727_2016-->