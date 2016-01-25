<properties
	pageTitle="Anteprima di Azure Active Directory B2C: configurazione Amazon | Microsoft Azure"
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
	ms.date="01/12/2016"
	ms.author="swkrish"/>

# Anteprima di Azure Active Directory B2C: fornire la registrazione e l’accesso agli utenti con account Amazon

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Creare un'applicazione Amazon

Per utilizzare Amazon come provider di identità in Azure Active Directory (AD) B2C, è innanzitutto necessario creare un'applicazione Amazon e inserire i parametri corretti. È necessario un account di Amazon per questo scopo. Se non si dispone di uno, è possibile ottenerlo all'indirizzo [http://www.amazon.com/](http://www.amazon.com/).

1. Visitare il [Centro sviluppatori Amazon](https://login.amazon.com/) e accedere con le credenziali dell'account Amazon.
2. Se non è già stato fatto, fare clic su **Sign Up**, seguire la procedura di registrazione per sviluppatori e accettare le condizioni.
3. Fare clic su **Registra nuova applicazione**.

    ![Amazon - Nuova applicazione](./media/active-directory-b2c-setup-amzn-app/amzn-new-app.png)

4. Fornire informazioni sull'applicazione (**Nome**, **Descrizione** e **URL dell’informativa sulla Privacy**) e fare clic su **Salva**.

    ![Amazon - Registra app](./media/active-directory-b2c-setup-amzn-app/amzn-register-app.png)

5. Nella sezione **Impostazioni Web** copiare i valori di **ID Client** e **Segreto client** (sarà necessario fare clic sul pulsante **Mostra segreto** per visualizzarlo). Sono necessari entrambi per configurare Amazon come provider di identità nel tenant. Fare clic su **Modifica** nella parte inferiore della sezione. Nota: il **segreto client** è un'importante credenziale di sicurezza.

    ![Amazon - Segreto client](./media/active-directory-b2c-setup-amzn-app/amzn-client-secret.png)

6. Immettere [https://login.microsoftonline.com](https://login.microsoftonline.com) nel campo **Origini JavaScript consentite** e `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` nel campo **URL restituiti consentiti**, dove **{tenant}** deve essere sostituito con il nome del tenant, ad esempio contoso.onmicrosoft.com. Fare clic su **Save**. Nota: il valore **{tenant}** distingue tra maiuscole e minuscole.

    ![Amazon - URL](./media/active-directory-b2c-setup-amzn-app/amzn-urls.png)

## Configurare Amazon come provider di identità nel tenant

1. [Seguire questa procedura per passare al pannello delle funzionalità B2C nel Portale di Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Nel pannello delle funzionalità di B2C, fare clic su **Provider di identità**.
3. Fare clic su **+Aggiungi** nella parte superiore del pannello.
4. Fornire un **Nome** per la configurazione del provider di identità. Ad esempio, immettere "Amzn".
5. Fare clic su **tipo di provider di identità**, selezionare **Amazon** e fare clic su **OK**.
6. Fare clic su **Impostare il provider di identità** e immettere **ID Client** e **Client segreto** dell'applicazione Amazon creata in precedenza.
7. Fare clic su **OK** e quindi **Crea** per salvare la configurazione di Amazon.

<!---HONumber=AcomDC_0114_2016-->