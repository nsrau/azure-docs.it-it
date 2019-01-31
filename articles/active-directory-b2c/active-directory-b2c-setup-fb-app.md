---
title: Configurare l'iscrizione e l'accesso con un account Facebook tramite Azure Active Directory B2C | Microsoft Docs
description: Consentire l'iscrizione e l'accesso ai clienti con account Facebook alle applicazioni da Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: ea1afb83c3e381c33b1c29c224d1ce0cc1d3d080
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55163042"
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
7. Scegliere una **Categoria**, ad esempio `Business and Pages`. Questo valore è richiesto da Facebook, ma non usato per Azure AD B2C.
8. Nella parte inferiore della pagina, selezionare **Aggiungi piattaforma**, quindi selezionare **Sito Web**.
9. In **URL sito**, immettere `https://your-tenant-name.b2clogin.com/` sostituendo `your-tenant-name` con il nome del tenant. Immettere un URL per l'**URL dell'Informativa sulla privacy**, ad esempio `http://www.contoso.com`. L'URL del criterio è una pagina da mantenere per fornire informazioni sulla privacy per l'applicazione.
10. Selezionare **Save changes** (Salva modifiche).
11. Nella parte superiore della pagina copiare l'**ID app**. 
12. Fare clic su **Show** (Mostra) e copiare il valore **App Secret** (Segreto app). Sono necessari entrambi per configurare Facebook come provider di identità nel tenant. **App Segreta** è una credenziale di sicurezza importante.
13. Selezionare **Prodotti**, quindi selezionare **Configura** sotto **Facebook Login** (Accesso a Facebook).
14. Selezionare **Impostazioni** sotto **Facebook Login** (Accesso a Facebook).
15. In **Valid OAuth redirect URIs** (URI di reindirizzamento OAuth valide) immettere `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Sostituire `your-tenant-name` con il nome del tenant. Fare clic su **Salva le modifiche** nella parte inferiore della pagina.
16. Per rendere disponibile l'applicazione Facebook per Azure AD B2C, fare clic sul selettore di stato nella parte superiore destra della pagina, **attivarlo** per rendere pubblica l'applicazione e quindi fare clic su **Conferma**.  A questo punto lo stato dovrebbe cambiare da **Sviluppo** a **Live**.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Configurare un account Facebook come provider di identità

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul **filtro delle directory e delle sottoscrizioni** nel menu in alto e scegliere la directory che contiene il tenant. 
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
4. Selezionare **Provider di identità** e quindi selezionare **Aggiungi**.
5. Immettere un **Nome**. Ad esempio, immettere *Facebook*.
6. Fare clic su **Identity provider type** (Tipo di provider di identità), selezionare **Facebook** e scegliere **OK**.
7. Selezionare **Set up this identity provider** (Configura provider di identità), immettere l'ID app registrato in precedenza in **ID Client** e immettere il segreto Client registrato come **Segreto client** dell'applicazione Facebook creata in precedenza.
8. Fare clic su **OK** e quindi su **Create** (Crea) per salvare la configurazione di Facebook.
