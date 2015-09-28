<properties
	pageTitle="Anteprima di Azure Active Directory B2C: configurazione di Facebook | Microsoft Azure"
	description="Fornire la registrazione e l’accesso agli utenti con account su Facebook nelle applicazioni protette da Azure Active Directory B2C"
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

# Anteprima di Azure Active Directory B2C: fornire la registrazione e l’accesso agli utenti con account su Facebook

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Creare un'applicazione Facebook

Per utilizzare Facebook come provider di identità in Azure Active Directory (AD) B2C, è innanzitutto necessario creare un'applicazione Facebook e inserire i parametri corretti. È necessario un account di Facebook per questo scopo. Se non si dispone di uno, è possibile ottenerlo all'indirizzo [https://www.facebook.com/](https://www.facebook.com/).

1. Passare al [sito Web Facebook Developers](https://developers.facebook.com/) e accedere con le proprie credenziali dell'account Facebook.
2. Se non è ancora stato fatto, fare clic su **Apps** e quindi su **Register as a Developer**, accettare le condizioni e seguire la procedura di registrazione.
3. Fare clic su **app** e poi su **Aggiungere una nuova app**. Poi scegliere **sito Web** come piattaforma, e fare clic su **Salta e crea ID App**.

    ![FB - Aggiungere una nuova app](./media/active-directory-b2c-setup-fb-app/fb-add-new-app.png)

    ![FB - Aggiungere una nuova app - sito Web](./media/active-directory-b2c-setup-fb-app/fb-add-new-app-website.png)

    ![FB - Creare ID app](./media/active-directory-b2c-setup-fb-app/fb-new-app-skip.png)

4. Nel modulo, fornire un **Nome visualizzato**, scegliere la **Categoria** appropriata e fare clic su **Crea ID App**. Nota: per questo è necessario accettare i criteri della piattaforma Facebook e completare un controllo di sicurezza online.

    ![FB - Creare ID app](./media/active-directory-b2c-setup-fb-app/fb-create-app-id.png)

5. Fare clic su **Impostazioni** nella barra di spostamento a sinistra. Immettere un valore valido **Email di contatto**.
6. Fare clic su **+ Aggiungi piattaforma** e quindi selezionare **sito Web**.

    ![FB - Impostazioni](./media/active-directory-b2c-setup-fb-app/fb-settings.png)

    ![FB - Impostazioni](./media/active-directory-b2c-setup-fb-app/fb-website.png)

7. Immettere [https://login.microsoftonline.com/](https://login.microsoftonline.com/) nel campo **URL del sito** e quindi fare clic su **Salva modifiche**.
8. Copiare il valore di **ID App**. Fare clic su **Mostra** e copiare il valore di **App segreta**. Sono necessari entrambi per configurare Facebook come provider di identità nella directory.

    > [AZURE.NOTE]**App Segreta**è una credenziale di sicurezza importante.

    ![FB - URL sito](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

9. Fare clic sulla scheda **Avanzate** nella parte superiore e quindi immettere [https://login.microsoftonline.com/te/ {directory} / oauth2/authresp](https://login.microsoftonline.com/te/{directory}/oauth2/authresp) nel campo **URI di reindirizzamento valido OAuth** (nella sezione **Protezione**), dove **{directory}** deve essere sostituita con il nome della directory (ad esempio, contosob2c.onmicrosoft.com). Fare clic su **Salva le modifiche** nella parte inferiore della pagina.

    ![FB - URI di reindirizzamento OAuth](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)

10. Per rendere l'applicazione Facebook utilizzabile da AD B2C di Azure, è necessario renderla pubblicamente disponibile. Per riuscirci fare clic su **Stato & revisione** nel riquadro di navigazione a sinistro e impostare l'opzione nella parte superiore della pagina su **Sì**. Fare clic su **Conferma**.

    ![FB - URI di reindirizzamento OAuth](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## Configurare Facebook come Provider di identità nella Directory

1. [Passare al pannello delle funzionalità B2C nel portale di anteprima di Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Nel pannello delle funzionalità di B2C, fare clic su **Provider di identità Social**.
3. Fare clic su **+Aggiungi** nella parte superiore del pannello.
4. Fornire un **Nome** per la configurazione del provider di identità. Ad esempio, immettere "FB".
5. Fare clic su **Tipo di provider di identità**, selezionare **Facebook** e fare clic su **OK**.
6. Fare clic su **Impostare il provider di identità** e immettere l’**ID App** e l’**App segreta** dell'applicazione Facebook creata in precedenza rispettivamente nei campi’**ID Client** e **Client segreto**.
7. Fare clic su **OK** e poi su **Crea** per salvare la configurazione di Facebook.

<!---HONumber=Sept15_HO3-->