---
title: Configurare l'iscrizione e l'accesso con un account Facebook-Azure Active Directory B2C
description: Consentire l'iscrizione e l'accesso ai clienti con account Facebook alle applicazioni da Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 77b2fccaeba3cd1d164a3ce428c04083fd69fb5a
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71264209"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con un account Facebook tramite Azure Active Directory B2C

## <a name="create-a-facebook-application"></a>Creare un'applicazione Facebook

Per usare un account Facebook come [provider di identità](active-directory-b2c-reference-oauth-code.md) in Azure Active Directory B2C (Azure ad B2C), è necessario creare un'applicazione nel tenant che la rappresenta. Se non si ha già un account Facebook, è possibile iscriversi all' [https://www.facebook.com/](https://www.facebook.com/)indirizzo.

1. Accedere al [sito Web Facebook for developers](https://developers.facebook.com/) con le credenziali dell'account Facebook.
1. Se non è ancora stato fatto, è necessario registrarsi come sviluppatore Facebook. A tale scopo **, selezionare inizia nell'angolo** superiore destro della pagina, accettare i criteri di Facebook e completare la procedura di registrazione.
1. Selezionare **app personali** e quindi **Crea app**.
1. Inserire un **nome visualizzato** e una **email di contatto** valida.
1. Selezionare **Crea ID app**. Potrebbe essere necessario accettare i criteri della piattaforma Facebook e completare un controllo di sicurezza online.
1. Selezionare **Impostazioni** > **Base**.
1. Scegliere una **Categoria**, ad esempio `Business and Pages`. Questo valore è richiesto da Facebook, ma non usato per Azure AD B2C.
1. Nella parte inferiore della pagina, selezionare **Aggiungi piattaforma**, quindi selezionare **Sito Web**.
1. In **URL sito**, immettere `https://your-tenant-name.b2clogin.com/` sostituendo `your-tenant-name` con il nome del tenant. Immettere un URL per l'**URL dell'Informativa sulla privacy**, ad esempio `http://www.contoso.com`. L'URL del criterio è una pagina da mantenere per fornire informazioni sulla privacy per l'applicazione.
1. Selezionare **Save changes** (Salva modifiche).
1. Nella parte superiore della pagina copiare l'**ID app**.
1. Selezionare **Mostra** e copiare il valore di **Secret app**. Sono necessari entrambi per configurare Facebook come provider di identità nel tenant. **App Segreta** è una credenziale di sicurezza importante.
1. Selezionare il segno più accanto a **prodotti**, quindi selezionare **Configura** in account di **accesso di Facebook**.
1. In **account di accesso di Facebook**selezionare **Impostazioni**.
1. In **Valid OAuth redirect URIs** (URI di reindirizzamento OAuth valide) immettere `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Sostituire `your-tenant-name` con il nome del tenant. Selezionare **Save Changes (Salva modifiche** ) nella parte inferiore della pagina.
1. Per rendere disponibile l'applicazione Facebook per Azure AD B2C, selezionare il selettore di stato nella parte superiore destra della pagina e **attivarlo per rendere** pubblica l'applicazione, quindi selezionare **modalità switch**.  A questo punto lo stato dovrebbe cambiare da **Sviluppo** a **Live**.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Configurare un account Facebook come provider di identità

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
1. Selezionare **provider di identità**e quindi fare clic su **Facebook**.
1. Immettere un **Nome**. Ad esempio, *Facebook*.
1. Per **ID client**, immettere l'ID app dell'applicazione Facebook creata in precedenza.
1. Per il **segreto client**, immettere il segreto dell'app registrato.
1. Selezionare **Salva**.
