---
title: 'Azure Active Directory B2C: configurazione di Google+ | Documentazione Microsoft'
description: "Fornire la registrazione e l’accesso agli utenti con account Google+ nelle applicazioni protette da Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 4dcca66f-29e4-4b4d-8840-50baad736bd7
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: e37c48d6c92a8a2cd480458abdff0a3a1ca9338f
ms.openlocfilehash: dbc544ad4dbb651aeb24faf2f89074c7aa59cf2a


---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-google-accounts"></a>Azure Active Directory B2C: fornire l'iscrizione e l'accesso agli utenti con account Google+
## <a name="create-a-google-application"></a>Creazione di un'applicazione Google+
Per usare Google+ come provider di identità in Azure Active Directory (Azure AD) B2C, è necessario creare un'applicazione Google+ e inserire i parametri corretti. Per eseguire questa operazione è necessario disporre di un account Google+. Se non è disponibile un account, è possibile crearlo nel sito [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Visitare [Google Developers Console](https://console.developers.google.com/) e accedere con le credenziali dell'account Google+.
2. Fare clic su **Crea progetto**, immettere un nome in **Nome progetto**e fare clic su **Crea**.
   
    ![Google+ - Introduzione](./media/active-directory-b2c-setup-goog-app/google-get-started.png)
   
    ![Google+ - Nuovo progetto](./media/active-directory-b2c-setup-goog-app/google-new-project.png)
3. Fare clic su **Gestione API** e su **Credenziali** nel riquadro di spostamento sinistro.
4. Fare clic sulla scheda **Schermata consenso OAuth** nella parte superiore.
   
    ![Google+ - Credenziali](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)
5. Selezionare o specificare un **Indirizzo e-mail** valido, fornire un **Nome prodotto** e fare clic su **Salva**.
   
    ![Google+ - Schermata consenso OAuth](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)
6. Fare clic su **Nuove credenziali** e scegliere **ID client OAuth**.
   
    ![Google+ - Schermata consenso OAuth](./media/active-directory-b2c-setup-goog-app/google-add-oauth2-client-id.png)
7. In **Tipo di applicazione** selezionare **Applicazione Web**.
   
    ![Google+ - Schermata consenso OAuth](./media/active-directory-b2c-setup-goog-app/google-web-app.png)
8. Fornire un **Nome** per l'applicazione, immettere `https://login.microsoftonline.com` nel campo **Origini JavaScript Autorizzate** e `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` nel campo **URI di reindirizzamento autorizzati**. Sostituire **{tenant}** con il nome del tenant, ad esempio contosob2c.onmicrosoft.com. Il valore **{tenant}** distingue tra maiuscole e minuscole. Fare clic su **Create**(Crea).
   
    ![Google+ - Creare ID client](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)
9. Copiare i valori **ID client** e **Segreto client**. Sono necessari entrambi per configurare Google+ come provider di identità nel tenant. **Segreto client** è una credenziale di sicurezza importante.
   
    ![Google+ - Segreto client](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## <a name="configure-google-as-an-identity-provider-in-your-tenant"></a>Configurare Google+ come provider di identità nel tenant
1. Seguire questa procedura per [passare al pannello delle funzionalità B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) nel portale di Azure.
2. Nel pannello delle funzionalità di B2C, fare clic su **Provider di identità**.
3. Fare clic su **+Aggiungi** nella parte superiore del pannello.
4. Fornire un **Nome** per la configurazione del provider di identità. Ad esempio, immettere "G+".
5. Fare clic su **Tipo di provider di identità**, selezionare **Google** e fare clic su **OK**.
6. Fare clic su **Impostare il provider di identità** e immettere l'ID Client e il segreto client dell'applicazione Google+ creata in precedenza.
7. Fare clic su **OK** e su **Crea** per salvare la configurazione di Google+.




<!--HONumber=Dec16_HO4-->


