<properties
	pageTitle="Anteprima di Azure Active Directory B2C: configurazione Google+ | Microsoft Azure"
	description="Fornire la registrazione e l’accesso agli utenti con account su Google+ nelle applicazioni protette da Azure Active Directory B2C"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="curtand"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/15/2015"
	ms.author="swkrish"/>

# Anteprima di Azure Active Directory B2C: fornire la registrazione e l’accesso agli utenti con account su Google+

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Creazione di un'applicazione Google+

Per utilizzare Google+ come provider di identità in Azure Active Directory (AD) B2C, è innanzitutto necessario creare un'applicazione Google+ e inserire i parametri corretti. È necessario un account di Google+ per questo scopo. Se non si dispone di uno, è possibile ottenerlo all'indirizzo [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Visitare [Google Developers Console](https://console.developers.google.com/) e accedere con le credenziali dell'account Google+.
2. Fare clic su **Crea progetto**, immettere un **Nome progetto**, accettare le condizioni del servizio e quindi fare clic su **Crea**.

    ![G+ - Introduzione](./media/active-directory-b2c-setup-goog-app/google-get-started.png)

    ![G+ - Nuovo progetto](./media/active-directory-b2c-setup-goog-app/google-new-project.png)

3. Fare clic su **APIs & auth** e quindi su **Credentials** nella struttura a sinistra.
4. Fare clic sulla scheda **Schermata consenso OAuth** nella parte superiore.

    ![G+ - Credenziali](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)

5. Selezionare o specificare un **indirizzo di posta elettronica** valido, fornire un **nome prodotto** e fare clic su **Salva**.

    ![G+ - Schermata consenso OAuth](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)

6. In **Tipo di applicazione** selezionare **Applicazione Web**.
7. Fornire un **Nome** per l’applicazione, inserire [https://login.microsoftonline.com](https://login.microsoftonline.com) nel campo **Authorized redirect URIs** e [https://login.microsoftonline.com/te/{directory}/oauth2/authresp](https://login.microsoftonline.com/te/{directory}/oauth2/authresp) nel campo **Authorized redirect URIs**, dove **{directory}** deve essere sostituito con il nome della directory (per esempio, contosob2c.onmicrosoft.com). Fare clic su **Create**.

    > [AZURE.NOTE]Il valore **{directory}** fa distinzione tra maiuscole e minuscole.

    ![Google - Crea ID client](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)

8. Copiare i valori **Client ID** e **Segreto client**. Sono necessari entrambi per configurare Google+ come provider di identità nella directory.

    > [AZURE.NOTE]**Segreto client** è una credenziale di sicurezza importante.

    ![G+ - Segreto client](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## Configurare Google+ come Provider di identità nella Directory

1. Passare al pannello delle funzionalità B2C nel [portale di anteprima di Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Nel pannello delle funzionalità di B2C, fare clic su **Provider di identità Social**.
3. Fare clic su **+Aggiungi** nella parte superiore del pannello.
4. Fornire un **Nome** per la configurazione del provider di identità. Ad esempio, immettere "G+".
5. Fare clic su **tipo di provider di identità**, selezionare **Google** e fare clic su **OK**.
6. Fare clic su **Impostare il provider di identità** e immettere il **ID Client** e **Client segreto** dell'applicazione Google+ creato in precedenza.
7. Fare clic su **OK** e quindi **Crea** per salvare la configurazione di Google+.

<!---HONumber=Sept15_HO3-->