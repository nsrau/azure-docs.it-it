---
title: Configurare l'iscrizione e l'accesso con un account Facebook tramite Azure Active Directory B2C | Microsoft Docs
description: Consentire l'iscrizione e l'accesso ai clienti con account Facebook alle applicazioni da Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 985285b463d66770f97a431705d5b9198b632592
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344607"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account Facebook tramite Azure Active Directory B2C

## <a name="create-a-facebook-application"></a>Creare un'applicazione Facebook

Per usare un account Facebook come provider di identità in Azure Active Directory (Azure AD) B2C, è necessario creare nel tenant un'applicazione che lo rappresenti. Se non si possiede già un account Facebook, è possibile crearlo sul sito [https://www.facebook.com/](https://www.facebook.com/).

1. Accedere al [sito Web Facebook for developers](https://developers.facebook.com/) con le credenziali dell'account Facebook.
2. Se non è ancora stato fatto, è necessario registrarsi come sviluppatore Facebook. A tale scopo, selezionare **Registrati** nell'angolo in alto a destra della pagina, accettare le condizioni di Facebook e completare la procedura di registrazione.
3. Selezionare **My Apps** (App personali) e quindi **Add a new App** (Aggiungi una nuova app). 
4. Inserire un **nome visualizzato** e una **email di contatto** valida.
5. Fare clic su **Crea ID app**. Potrebbe essere necessario accettare i criteri della piattaforma Facebook e completare un controllo di sicurezza online.
6. Selezionare **Impostazioni** > **Base**.
7. Nella parte inferiore della pagina, selezionare **Aggiungi piattaforma**, quindi selezionare **Sito Web**.
8. Immettere `https://{tenantname}.b2clogin.com/` nell'**URL sito**. Immettere un URL per l'**URL dell'Informativa sulla privacy**, ad esempio `http://www.contoso.com`.
9. Selezionare **Save changes** (Salva modifiche).
11. Nella parte superiore della pagina copiare l'**ID app**. 
12. Fare clic su **Show** (Mostra) e copiare il valore **App Secret** (Segreto app). Sono necessari entrambi per configurare Facebook come provider di identità nel tenant. **App Segreta** è una credenziale di sicurezza importante.
13. Selezionare **Prodotti**, quindi selezionare **Configura** sotto **Facebook Login** (Accesso a Facebook).
14. Selezionare **Impostazioni** sotto **Facebook Login** (Accesso a Facebook).
15. Immettere `https://{tenantname}.b2clogin.com/te/{tenant}.onmicrosoft.com/oauth2/authresp` in **Valid OAuth redirect URIs** (URI di reindirizzamento OAuth valide). Sostituire **{tenant}** con il nome del tenant, ad esempio contosob2c. Fare clic su **Salva le modifiche** nella parte inferiore della pagina.
16. Per rendere disponibile l'applicazione di Facebook per Azure AD B2C, selezionare **App Review** (Revisione app), impostare **Make My Application public?** (Rendere pubblica l'applicazione?) su **Sì**, scegliere una categoria, ad esempio `Business and Pages`e quindi fare clic su **Conferma**.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Configurare un account Facebook come provider di identità

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
2. Assicurarsi di usare la directory contenente il tenant Azure AD B2C passando a tale directory nell'angolo in alto a destra del portale di Azure. Selezionare le informazioni sulla sottoscrizione e quindi selezionare **Cambia directory**. 

    ![Passare al tenant di Azure AD B2C](./media/active-directory-b2c-setup-fb-app/switch-directories.png)

    Scegliere la directory contenente il tenant.

    ![Selezionare la directory](./media/active-directory-b2c-setup-fb-app/select-directory.png)

3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
4. Selezionare **Provider di identità** e quindi selezionare **Aggiungi**.
5. Immettere un **Nome**. Ad esempio, immettere *Facebook*.
6. Fare clic su **Identity provider type** (Tipo di provider di identità), selezionare **Facebook** e scegliere **OK**.
7. Selezionare **Set up this identity provider** (Configura provider di identità), immettere l'ID app registrato in precedenza in **ID Client** e immettere il segreto Client registrato come **Segreto client** dell'applicazione Facebook creata in precedenza.
8. Fare clic su **OK** e quindi su **Create** (Crea) per salvare la configurazione di Facebook.