<properties
	pageTitle="Anteprima di Azure Active Directory B2C: configurazione Google+ | Microsoft Azure"
	description="Fornire la registrazione e l’accesso agli utenti con account Google+ nelle applicazioni protette da Azure Active Directory B2C."
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
	ms.date="01/12/2016"
	ms.author="swkrish"/>

# Anteprima di Azure Active Directory B2C: Fornire la registrazione e l’accesso agli utenti con account Google+

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Creazione di un'applicazione Google+

Per usare Google+ come provider di identità in Azure Active Directory (Azure AD) B2C, è necessario creare un'applicazione Google+ e inserire i parametri corretti. Per eseguire questa operazione è necessario disporre di un account Google+. Se non si dispone di un account, è possibile crearlo nel sito [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Visitare [Google Developers Console](https://console.developers.google.com/) e accedere con le credenziali dell'account Google+.
2. Fare clic su **Create project**, immettere un nome in **Project name** e quindi fare clic su **Create**.

    ![Google+ - Introduzione](./media/active-directory-b2c-setup-goog-app/google-get-started.png)

    ![Google+ - Nuovo progetto](./media/active-directory-b2c-setup-goog-app/google-new-project.png)

3. Fare clic su **API Manager** e quindi su **Credentials** nella barra di spostamento a sinistra.
4. Fare clic sulla scheda **Schermata consenso OAuth** nella parte superiore.

    ![Google+ - Credenziali](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)

5. Selezionare o specificare un **indirizzo di posta elettronica** valido, fornire un **nome prodotto** e fare clic su **Save**.

    ![Google+ - Schermata consenso OAuth](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)

6. Fare clic su **New credentials** e scegliere **OAuth client ID**.

    ![Google+ - Schermata consenso OAuth](./media/active-directory-b2c-setup-goog-app/google-add-oauth2-client-id.png)

7. In **Tipo di applicazione** selezionare **Applicazione Web**.

    ![Google+ - Schermata consenso OAuth](./media/active-directory-b2c-setup-goog-app/google-web-app.png)

8. Fornire un **nome** per l'applicazione, immettere `https://login.microsoftonline.com` nel campo **Authorized JavaScript origins** e `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` nel campo **Authorized redirect URIs**. Sostituire **{tenant}** con il nome del tenant, ad esempio contosob2c.onmicrosoft.com. Il valore **{tenant}** distingue tra maiuscole e minuscole. Fare clic su **Crea**.

    ![Google+ - Creare ID client](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)

9. Copiare i valori **Client ID** e **Segreto client**. Sono necessari entrambi per configurare Google+ come provider di identità nel tenant. **Segreto client** è una credenziale di sicurezza importante.

    ![Google+ - Segreto client](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## Configurare Google+ come provider di identità nel tenant

1. Seguire questa procedura per [passare al pannello delle funzionalità B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) nel portale di Azure.
2. Nel pannello delle funzionalità di B2C, fare clic su **Provider di identità**.
3. Fare clic su **+Aggiungi** nella parte superiore del pannello.
4. Fornire un **Nome** per la configurazione del provider di identità. Ad esempio, immettere "G+".
5. Fare clic sul **tipo di provider di identità**, selezionare **Google** e fare clic su **OK**.
6. Fare clic su **Impostare il provider di identità** e immettere l'ID Client e il client segreto dell'applicazione Google+ creata in precedenza.
7. Fare clic su **OK** e quindi **Create** per salvare la configurazione di Google+.

<!---HONumber=AcomDC_0302_2016-->