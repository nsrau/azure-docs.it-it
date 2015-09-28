<properties
	pageTitle="Anteprima di Azure Active Directory B2C: configurazione LinkedIn | Microsoft Azure"
	description="Fornire la registrazione e l’accesso agli utenti con account su LinkedIn nelle applicazioni protette da Azure Active Directory B2C"
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

# Anteprima di Azure Active Directory (AD) B2C: fornire la registrazione e l’accesso agli utenti con account su LinkedIn

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Creare un'applicazione su LinkedIn

Per utilizzare LinkedIn come provider di identità in Azure Active Directory (AD) B2C, è innanzitutto necessario creare un'applicazione LinkedIn e inserire i parametri corretti. È necessario un account di LinkedIn per questo scopo. Se non si dispone di uno, è possibile ottenerlo all'indirizzo [https://www.linkedin.com/](https://www.linkedin.com/).

1. Visitare il [sito Web di sviluppatori LinkedIn](https://www.developer.linkedin.com/) e accedere con le credenziali dell'account LinkedIn.
2. Fare clic su **Le mie applicazioni** nella barra dei menu superiore e quindi **Creare applicazione**.

    ![LinkedIn - Nuova app](./media/active-directory-b2c-setup-li-app/linkedin-new-app.png)

3. Nel modulo **Creazione di una nuova applicazione**, immettere le informazioni pertinenti (**Nome azienda**, **Nome**, **Descrizione**, **URL del Logo dell'applicazione**, **Utilizzo dell'applicazione**, **URL del sito Web**, **Email aziendale** e **Telefono ufficio**).
4. Accettare i Termini d’uso dell’API di LinkedIn e fare clic su **Invia**.

    ![LinkedIn - Registro app](./media/active-directory-b2c-setup-li-app/linkedin-register-app.png)

5. Copiare i valori di **ID Client** e **Client segreto** (che si trovano sotto la sezione **chiavi di autenticazione**). Sono necessari entrambi per configurare LinkedIn come provider di identità nella directory.

    > [AZURE.NOTE]**Client Segreto** è un'importante credenziale di sicurezza.

6. Immettere [https://login.microsoftonline.com/te/ {directory} / oauth2/authresp](https://login.microsoftonline.com/te/{directory}/oauth2/authresp) nel campo **URL di reindirizzamento autorizzati** (sotto la sezione **OAuth 2.0**), dove **{directory}** deve essere sostituito con il nome della directory (ad esempio, contoso.onmicrosoft.com) e fare clic su **Aggiungi**. Fare clic su **Aggiorna**.

    ![LinkedIn - Installazione app](./media/active-directory-b2c-setup-li-app/linkedin-setup.png)

## Configurare LinkedIn come Provider di identità nella Directory

1. [Passare al pannello delle funzionalità B2C nel portale di anteprima di Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Nel pannello delle funzionalità di B2C, fare clic su **provider di identità Social**.
3. Fare clic su **+Aggiungi** nella parte superiore del pannello.
4. Fornire un **Nome** per la configurazione del provider di identità. Ad esempio, immettere "LI".
5. Fare clic su **tipo di provider di identità**, selezionare **LinkedIn** e fare clic su **OK**.
6. Fare clic su **Impostare il provider di identità** e immettere il **ID Client** e **Client segreto** dell'applicazione LinkedIn creato in precedenza.
7. Fare clic su **OK** e quindi **Crea** per salvare la configurazione di LinkedIn.

<!---HONumber=Sept15_HO3-->