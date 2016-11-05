---
title: 'Azure Active Directory B2C: configurazione di Facebook | Microsoft Docs'
description: Fornire la registrazione e l’accesso agli utenti con account su Facebook nelle applicazioni protette da Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: swkrish
manager: msmbaldwin
editor: bryanla

ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2016
ms.author: swkrish

---
# Azure Active Directory B2C: fornire l'iscrizione e l'accesso agli utenti con account Facebook
## Creare un'applicazione Facebook
Per utilizzare Facebook come provider di identità in Azure Active Directory (Azure AD) B2C, è necessario creare un'applicazione Facebook e inserire i parametri corretti. Per eseguire questa operazione è necessario disporre di un account Facebook. Se non si ha un account, è possibile crearlo nel sito [https://www.facebook.com/](https://www.facebook.com/).

1. Passare al [sito Web Facebook for developers](https://developers.facebook.com/) e accedere con le credenziali dell'account Facebook.
2. Se non è ancora stato fatto, è necessario registrarsi come sviluppatore Facebook. A tale scopo, fare clic su **Registrati** nell'angolo in alto a destra della pagina, accettare le condizioni di Facebook e completare la procedura di registrazione.
3. Fare clic su **My Apps** (App personali) e quindi su **Add a new App** (Aggiungi una nuova app). Scegliere **Website** (Sito Web) come piattaforma e fare clic su **Skip and Create App ID** (Ignora e crea ID app).
   
    ![Facebook - Aggiungere una nuova app](./media/active-directory-b2c-setup-fb-app/fb-add-new-app.png)
   
    ![Facebook - Aggiungere una nuova app - sito Web](./media/active-directory-b2c-setup-fb-app/fb-add-new-app-website.png)
   
    ![Facebook - Creare ID app](./media/active-directory-b2c-setup-fb-app/fb-new-app-skip.png)
4. Nel modulo fornire un **Display Name** (Nome visualizzato), un **Contact Email** (Indirizzo di posta elettronica di contatto) valido, una **Category** (Categoria) appropriata e fare clic su **Create App ID** (Crea ID app). È necessario accettare i criteri della piattaforma Facebook e completare un controllo di sicurezza online.
   
    ![Facebook - Creare un nuovo ID app](./media/active-directory-b2c-setup-fb-app/fb-create-app-id.png)
5. Fare clic su **Settings** (Impostazioni) nel riquadro di spostamento sinistro.
6. Fare clic su **+Add Platform** (+Aggiungi piattaforma) e quindi selezionare **Website** (Sito Web).
   
    ![Facebook - Impostazioni](./media/active-directory-b2c-setup-fb-app/fb-settings.png)
   
    ![Facebook - Impostazioni - sito Web](./media/active-directory-b2c-setup-fb-app/fb-website.png)
7. Immettere [https://login.microsoftonline.com/](https://login.microsoftonline.com/) nel campo **URL del sito** e quindi fare clic su **Salva modifiche**.
   
    ![Facebook - URL del sito](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)
8. Copiare il valore di **ID App**. Fare clic su **Mostra** e copiare il valore di **App segreta**. Sono necessari entrambi per configurare Facebook come provider di identità nel tenant. **App Segreta**è una credenziale di sicurezza importante.
   
    ![Facebook - ID app e segreto app](./media/active-directory-b2c-setup-fb-app/fb-app-id-app-secret.png)
9. Fare clic su **+ Aggiungi prodotto** nella riquadro di spostamento sinistro, quindi sul pulsante **Get Started** (Informazioni di base) accanto a **Facebook Login** (Accesso a Facebook).
   
    ![Facebook - Accesso a Facebook](./media/active-directory-b2c-setup-fb-app/fb-login.png)
10. Immettere `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` nel campo **Valid OAuth redirect URIs** (URI di reindirizzamento OAuth validi) nella sezione **Client OAuth Settings** (Impostazioni OAuth client). Sostituire **{tenant}** con il nome del tenant, ad esempio contosob2c.onmicrosoft.com. Fare clic su **Salva le modifiche** nella parte inferiore della pagina.
    
    ![Facebook - URI di reindirizzamento OAuth](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)
11. Per rendere l'applicazione Facebook utilizzabile da AD B2C di Azure, è necessario renderla pubblicamente disponibile. A questo scopo, fare clic su **App Review** (Controllo app) nel riquadro di spostamento sinistro, impostare l'opzione nella parte superiore della pagina su **SÌ** e quindi fare clic su **Confirm** (Conferma).
    
    ![Facebook - App pubblica](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## Configurare Facebook come provider di identità nel tenant
1. Seguire questa procedura per [passare al pannello delle funzionalità B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) nel portale di Azure.
2. Nel pannello delle funzionalità di B2C, fare clic su **Provider di identità**.
3. Fare clic su **+Aggiungi** nella parte superiore del pannello.
4. Fornire un **Nome** per la configurazione del provider di identità. Ad esempio, immettere "FB".
5. Fare clic su **Tipo di provider di identità**, selezionare **Facebook** e scegliere **OK**.
6. Fare clic su **Configura questo provider di identità**, immettere l'ID app e il segreto app dell'applicazione Facebook creata in precedenza rispettivamente nei campi **ID client** e **Segreto client**.
7. Fare clic su **OK** e quindi su **Crea** per salvare la configurazione di Facebook.

<!---HONumber=AcomDC_0727_2016-->