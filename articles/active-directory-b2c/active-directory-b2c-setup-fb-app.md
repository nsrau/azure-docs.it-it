---
title: 'Azure Active Directory B2C: configurazione di Facebook | Documentazione Microsoft'
description: "Fornire la registrazione e l’accesso agli utenti con account su Facebook nelle applicazioni protette da Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: sromeroz
manager: mtillman
editor: sromeroz
ms.assetid: b875f235-a1d2-4abb-b9f0-b89beac38a32
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/7/2017
ms.author: sromeroz
ms.openlocfilehash: 7f551a7ba9b7ddbb373e6b6418ab43fe7ce9be36
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-facebook-accounts"></a>Azure Active Directory B2C: fornire l'iscrizione e l'accesso agli utenti con account Facebook
## <a name="create-a-facebook-application"></a>Creare un'applicazione Facebook
Per utilizzare Facebook come provider di identità in Azure Active Directory (Azure AD) B2C, è necessario creare un'applicazione Facebook e inserire i parametri corretti. Per eseguire questa operazione è necessario disporre di un account Facebook. Se non si ha un account, è possibile crearlo nel sito [https://www.facebook.com/](https://www.facebook.com/).

1. Passare al [sito Web Facebook for developers](https://developers.facebook.com/) e accedere con le credenziali dell'account Facebook.
2. Se non è ancora stato fatto, è necessario registrarsi come sviluppatore Facebook. A tale scopo, fare clic su **Registrati** nell'angolo in alto a destra della pagina, accettare le condizioni di Facebook e completare la procedura di registrazione.
3. Fare clic su **My Apps** (App personali) e quindi su **Add a new App** (Aggiungi una nuova app). 
4. Nel modulo fornire un **nome visualizzato** e una **email di contatto** valida.
5. Fare clic su **Crea ID app**. Potrebbe essere necessario accettare i criteri della piattaforma Facebook e completare un controllo di sicurezza online.
6. Nella colonna di sinistra fare clic su **Impostazioni** e quindi selezionare **Base**, se non è già selezionata.
7. Selezionare una **categoria**. 
8. Fare clic su **+Add Platform** (+Aggiungi piattaforma) e selezionare **Website** (Sito Web).
   
    ![Facebook - Impostazioni](./media/active-directory-b2c-setup-fb-app/fb-settings.png)
   
    ![Facebook - Impostazioni - sito Web](./media/active-directory-b2c-setup-fb-app/fb-website.png)
9. Immettere `https://login.microsoftonline.com/` nel campo **URL sito** e quindi fare clic su **Salva modifiche** nella parte inferiore della pagina.
   
    ![Facebook - URL del sito](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

10. Copiare il valore di **ID App**. Fare clic su **Show** (Mostra) e copiare il valore **App Secret** (Segreto app). Sono necessari entrambi per configurare Facebook come provider di identità nel tenant. **App Segreta** è una credenziale di sicurezza importante.
   
    ![Facebook - ID app e segreto app](./media/active-directory-b2c-setup-fb-app/fb-app-id-app-secret.png)
11. Fare clic su **Aggiungi prodotto** nel riquadro di spostamento sinistro, quindi fare clic sul pulsante **Configurazione** per **Account di accesso di Facebook**.
   
    ![Facebook - Accesso a Facebook](./media/active-directory-b2c-setup-fb-app/fb-login.png)
12. Fare clic su **Impostazioni** nel riquadro di spostamento destro in **Account di accesso di Facebook**

    ![Facebook - Impostazioni Account di accesso di Facebook](./media/active-directory-b2c-setup-fb-app/fb-login-settings.png)
13. Immettere `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` nel campo **Valid OAuth redirect URIs** (URI di reindirizzamento OAuth validi) nella sezione **Client OAuth Settings** (Impostazioni OAuth client). Sostituire **{tenant}** con il nome del tenant, ad esempio contosob2c.onmicrosoft.com. Fare clic su **Salva le modifiche** nella parte inferiore della pagina.
    
    ![Facebook - URI di reindirizzamento OAuth](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)
14. Per rendere l'applicazione Facebook utilizzabile da AD B2C di Azure, è necessario renderla pubblicamente disponibile. A questo scopo, fare clic su **App Review** (Controllo app) nel riquadro di spostamento sinistro, impostare l'opzione nella parte superiore della pagina su **SÌ** e quindi fare clic su **Confirm** (Conferma).
    
    ![Facebook - App pubblica](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## <a name="configure-facebook-as-an-identity-provider-in-your-tenant"></a>Configurare Facebook come provider di identità nel tenant
1. Seguire questa procedura per [passare al pannello delle funzionalità B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) nel portale di Azure.
2. Nel pannello delle funzionalità di B2C, fare clic su **Provider di identità**.
3. Fare clic su **+Aggiungi** nella parte superiore del pannello.
4. Fornire un **Nome** per la configurazione del provider di identità. Ad esempio, immettere "Facebook".
5. Fare clic su **Identity provider type** (Tipo di provider di identità), selezionare **Facebook** e scegliere **OK**.
6. Fare clic su **Set up this identity provider** (Configura questo provider di identità), immettere l'ID app e il segreto app dell'applicazione Facebook creata in precedenza rispettivamente nei campi **ID client** e **Segreto client**.
7. Fare clic su **OK** e quindi su **Create** (Crea) per salvare la configurazione di Facebook.

> [!NOTE]
> L'aggiunta di un **provider di identità** al tenant non modifica i criteri esistenti. Ricordare di aggiornare i criteri includendo il provider di identità appena creato.
>