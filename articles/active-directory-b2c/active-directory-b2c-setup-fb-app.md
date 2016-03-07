<properties
	pageTitle="Anteprima di Azure Active Directory B2C: configurazione di Facebook | Microsoft Azure"
	description="Fornire la registrazione e l’accesso agli utenti con account su Facebook nelle applicazioni protette da Azure Active Directory B2C."
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

# Anteprima di Azure Active Directory B2C: Fornire la registrazione e l’accesso agli utenti con account Facebook

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Creare un'applicazione Facebook

Per utilizzare Facebook come provider di identità in Azure Active Directory (Azure AD) B2C, è necessario creare un'applicazione Facebook e inserire i parametri corretti. Per eseguire questa operazione è necessario disporre di un account Facebook. Se non si dispone di un account, è possibile crearlo nel sito [https://www.facebook.com/](https://www.facebook.com/).

1. Passare al [sito Web Facebook Developers](https://developers.facebook.com/) e accedere con le proprie credenziali dell'account Facebook.
2. Se non è ancora stato fatto, è necessario registrarsi come sviluppatore Facebook. A tale scopo, fare clic su **Register** nell'angolo in alto a destra della pagina, accettare i criteri di Facebook e completare la procedura di registrazione.
3. Fare clic su **My Apps** e quindi su **Add a new App**. Poi scegliere **sito Web** come piattaforma e fare clic su **Salta e crea ID App**.

    ![Facebook - Aggiungere una nuova app](./media/active-directory-b2c-setup-fb-app/fb-add-new-app.png)

    ![Facebook - Aggiungere una nuova app - sito Web](./media/active-directory-b2c-setup-fb-app/fb-add-new-app-website.png)

    ![Facebook - Creare ID app](./media/active-directory-b2c-setup-fb-app/fb-new-app-skip.png)

4. Nel modulo, fornire un **Nome visualizzato**, scegliere la **Categoria** appropriata e fare clic su **Crea ID App**. È necessario accettare i criteri della piattaforma Facebook e completare un controllo di sicurezza online.

    ![Facebook - Creare un nuovo ID app](./media/active-directory-b2c-setup-fb-app/fb-create-app-id.png)

5. Fare clic su **Impostazioni** nella barra di spostamento a sinistra. Immettere un valore valido **Email di contatto**.
6. Fare clic su **+ Aggiungi piattaforma** e quindi selezionare **sito Web**.

    ![Facebook - Impostazioni](./media/active-directory-b2c-setup-fb-app/fb-settings.png)

    ![Facebook - Impostazioni - sito Web](./media/active-directory-b2c-setup-fb-app/fb-website.png)

7. Immettere [https://login.microsoftonline.com/](https://login.microsoftonline.com/) nel campo **URL del sito** e quindi fare clic su **Salva modifiche**.
8. Copiare il valore di **ID App**. Fare clic su **Mostra** e copiare il valore di **App segreta**. Sono necessari entrambi per configurare Facebook come provider di identità nel tenant. **App Segreta**è una credenziale di sicurezza importante.

    ![Facebook - URL del sito](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

9. Fare clic sulla scheda **Avanzate** nella parte superiore e immettere `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` nel campo **URI di reindirizzamento OAuth validi** nella sezione **Sicurezza**, dove **{tenant}** deve essere sostituito con il nome del tenant, ad esempio contosob2c.onmicrosoft.com. Fare clic su **Salva le modifiche** nella parte inferiore della pagina.

    ![Facebook - URI di reindirizzamento OAuth](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)

10. Per rendere l'applicazione Facebook utilizzabile da AD B2C di Azure, è necessario renderla pubblicamente disponibile. Per eseguire questa operazione, fare clic su **Stato e verifica** a sinistra e inviare l'applicazione per la revisione facendo clic sul pulsante **Inizia il tuo invio**. Quando l'applicazione è stata approvata da Facebook, è possibile renderla pubblica impostando l'opzione nella parte superiore della pagina su **SÌ** e facendo clic su **Conferma**.

    ![Facebook - Invio app](./media/active-directory-b2c-setup-fb-app/fb-app-submission.png)

    ![Facebook - App pubblica](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## Configurare Facebook come provider di identità nel tenant

1. Seguire questa procedura per [passare al pannello delle funzionalità B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) nel portale di Azure.
2. Nel pannello delle funzionalità di B2C, fare clic su **Provider di identità**.
3. Fare clic su **+Aggiungi** nella parte superiore del pannello.
4. Fornire un **Nome** per la configurazione del provider di identità. Ad esempio, immettere "FB".
5. Fare clic su **Tipo di provider di identità**, selezionare **Facebook** e fare clic su **OK**.
6. Fare clic su **Impostare il provider di identità** e immettere l'ID app e l'app segreta dell'applicazione Facebook creata in precedenza rispettivamente nei campi **ID Client** e **Client segreto**.
7. Fare clic su **OK** e poi su **Crea** per salvare la configurazione di Facebook.

<!---HONumber=AcomDC_0224_2016-->