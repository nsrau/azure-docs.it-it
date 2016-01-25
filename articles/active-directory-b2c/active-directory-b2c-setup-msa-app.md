<properties
	pageTitle="Anteprima di Azure Active Directory B2C: configurazione dell'account Microsoft | Microsoft Azure"
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
	ms.date="01/12/2016"
	ms.author="swkrish"/>

# Anteprima di Azure Active Directory B2C: fornire la registrazione e l'accesso agli utenti con account Microsoft

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Creare un'applicazione di un account Microsoft

Per usare un account Microsoft come provider di identità in Azure Active Directory (AD) B2C, è innanzitutto necessario creare un'applicazione di un account Microsoft e inserire i parametri corretti. Per questa operazione, è necessario un account Microsoft. Se non si dispone di un account Microsoft, è possibile ottenerlo all'indirizzo [https://www.live.com](https://www.live.com/).

1. Passare al [Centro per sviluppatori degli account Microsoft](https://account.live.com/developers/applications) e accedere con le credenziali del proprio account Microsoft.
2. Fare clic su **Crea applicazione**.

    ![MSA - Aggiunta di una nuova app](./media/active-directory-b2c-setup-msa-app/msa-add-new-app.png)

3. Specificare un valore in **Nome applicazione** e fare clic su **Accetto**. Nota: per questa operazione è necessario accettare le condizioni per l'uso dei servizi Microsoft.

    ![MSA - Nome dell'app](./media/active-directory-b2c-setup-msa-app/msa-app-name.png)

4. Fare clic su **Impostazioni API** a sinistra. Immettere un valore valido **Email di contatto**.

    ![MSA - Impostazioni API](./media/active-directory-b2c-setup-msa-app/msa-api-settings.png)

5. Immettere `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` nel campo **URL di reindirizzamento**, dove **{tenant}** deve essere sostituito con il nome del tenant, ad esempio contosob2c.onmicrosoft.com. Fare clic su **Save** nella parte inferiore della pagina.

    ![MSA - URL di reindirizzamento](./media/active-directory-b2c-setup-msa-app/msa-redirect-url.png)

6. Fare clic su **Impostazioni app** a sinistra. Copiare i valori **Client ID** e **Segreto client**. Sono necessari entrambi per configurare un account Microsoft come provider di identità nel tenant. Nota: il **segreto client** è un'importante credenziale di sicurezza.

    ![MSA - Segreto client](./media/active-directory-b2c-setup-msa-app/msa-client-secret.png)

## Configurare un account Microsoft come provider di identità nel tenant

1. [Seguire questa procedura per passare al pannello delle funzionalità B2C nel portale di Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Nel pannello delle funzionalità di B2C, fare clic su **Provider di identità**.
3. Fare clic su **+Aggiungi** nella parte superiore del pannello.
4. Fornire un **Nome** per la configurazione del provider di identità. Ad esempio, immettere "MSA".
5. Fare clic su **Tipo di provider di identità**, selezionare **Account Microsoft** e fare clic su **OK**.
6. Fare clic su **Imposta provider di identità** e immettere i valori di **ID client** e **Chiave privata client** dell'applicazione dell'account Microsoft creata in precedenza.
7. Fare clic su **OK** e quindi su **Crea** per salvare la configurazione dell'account Microsoft.

<!---HONumber=AcomDC_0114_2016-->