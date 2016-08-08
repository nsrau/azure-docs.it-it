<properties
	pageTitle="Azure Active Directory B2C: configurazione di LinkedIn | Microsoft Azure"
	description="Fornire la registrazione e l’accesso agli utenti con account su LinkedIn nelle applicazioni protette da Azure Active Directory B2C"
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

# Azure Active Directory B2C: fornire l'iscrizione e l'accesso agli utenti con account LinkedIn

## Creare un'applicazione su LinkedIn

Per utilizzare LinkedIn come provider di identità in Azure Active Directory (Azure AD) B2C, è necessario creare un'applicazione LinkedIn e inserire i parametri corretti. Per eseguire questa operazione è necessario disporre di un account di LinkedIn. Se non si ha un account, è possibile crearlo nel sito [https://www.linkedin.com/](https://www.linkedin.com/).

1. Visitare il [sito Web di sviluppatori LinkedIn](https://www.developer.linkedin.com/) e accedere con le credenziali dell'account LinkedIn.
2. Fare clic su **My Apps** (App personali) sulla barra dei menu superiore e quindi fare clic su **Create application** (Crea applicazione).

    ![LinkedIn - Nuova app](./media/active-directory-b2c-setup-li-app/linkedin-new-app.png)

3. Nel modulo **Creazione di una nuova applicazione**, immettere le informazioni pertinenti (**Nome azienda**, **Nome**, **Descrizione**, **URL del Logo dell'applicazione**, **Utilizzo dell'applicazione**, **URL del sito Web**, **Email aziendale** e **Telefono ufficio**).
4. Accettare i **LinkedIn API Terms of Use** (Condizioni d'uso dell'API LinkedIn) e fare clic su **Submit** (Invia).

    ![LinkedIn - Registro app](./media/active-directory-b2c-setup-li-app/linkedin-register-app.png)

5. Copiare i valori di **Client ID** (ID client) e **Client Secret** (Segreto client), che si trovano nella sezione **Authentication Keys** (Chiavi di autenticazione). Sono necessari entrambi per configurare LinkedIn come provider di identità nel tenant.

	>[AZURE.NOTE] **Client Segreto** è un'importante credenziale di sicurezza.

6. Immettere `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` nel campo **Authorized Redirect URLs** (URL di reindirizzamento autorizzati) in **OAuth 2.0**. Sostituire **{tenant}** con il nome del tenant, ad esempio contoso.onmicrosoft.com. Fare clic su **Add** (Aggiungi) e quindi su **Update** (Aggiorna). Il valore **{tenant}** distingue tra maiuscole e minuscole.

    ![LinkedIn - Installazione app](./media/active-directory-b2c-setup-li-app/linkedin-setup.png)

## Configurare LinkedIn come provider di identità nel tenant

1. Seguire questa procedura per [passare al pannello delle funzionalità B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) nel portale di Azure.
2. Nel pannello delle funzionalità di B2C, fare clic su **Provider di identità**.
3. Fare clic su **+Aggiungi** nella parte superiore del pannello.
4. Fornire un **Nome** per la configurazione del provider di identità. Ad esempio, immettere "LI".
5. Fare clic su **Tipo di provider di identità**, selezionare **LinkedIn** e fare clic su **OK**.
6. Fare clic su **Impostare il provider di identità** e immettere l'ID client e il segreto client dell'applicazione LinkedIn creata in precedenza.
7. Fare clic su **OK** e quindi su **Crea** per salvare la configurazione di LinkedIn.

<!---HONumber=AcomDC_0727_2016-->