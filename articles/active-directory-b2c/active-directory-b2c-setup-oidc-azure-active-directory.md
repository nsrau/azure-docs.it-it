---
title: Configurare l'accesso per un'organizzazione Azure Active Directory - Azure Active Directory B2C | Microsoft Docs
description: Configurare l'accesso per un'organizzazione Azure Active Directory specifica in Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 0da27c5ce872d1b4e1b4c63f6f4207bb5ca4d6ef
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60316358"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Configurare l'accesso per un'organizzazione Azure Active Directory specifica in Azure Active Directory B2C

>[!NOTE]
> Questa funzionalità è disponibile in anteprima pubblica. Non utilizzare la funzionalità negli ambienti di produzione.

Per usare un account di Azure Active Directory (Azure AD) come [provider di identità](active-directory-b2c-reference-oauth-code.md) in Azure AD B2C, è necessario creare un'applicazione che lo rappresenti. Questo articolo illustra come consentire l'accesso agli utenti di una specifica organizzazione di Azure Active Directory (Azure AD) usando un flusso utente in Azure Active Directory (Azure AD) B2C.

## <a name="create-an-azure-ad-app"></a>Creare un'app di Azure AD

Per abilitare l'accesso agli utenti da una specifica organizzazione di Azure AD, è necessario registrare un'applicazione all'interno del tenant aziendale di Azure AD che non corrisponde al tenant di Azure AD B2C.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Assicurarsi di usare la directory contenente il tenant di Azure AD facendo clic sul filtro Directory e sottoscrizione nel menu in alto e scegliendo tale directory.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Registrazioni per l'app**.
4. Selezionare **Registrazione nuova applicazione**.
5. Immettere un nome per l'applicazione. Ad esempio: `Azure AD B2C App`.
6. In **Tipo di applicazione** selezionare `Web app / API`.
7. In **URL di accesso** immettere l'URL seguente, interamente in lettere minuscole, sostituendo `your-B2C-tenant-name` con il nome del tenant di Azure AD B2C. Ad esempio, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`:

    ```
    https://your-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Tutti gli URL dovrebbero ora usare [b2clogin.com](b2clogin.md).

8. Fare clic su **Create**(Crea). Copiare l'**ID applicazione**, che dovrà essere usato in seguito.
9. Selezionare l'applicazione e quindi **Impostazioni**.
10. Selezionare **Chiavi**, immettere la descrizione della chiave, selezionare una durata e quindi fare clic su **Salva**. Copiare il valore della chiave visualizzato, che dovrà essere usato in seguito.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Configurare Azure AD come provider di identità

1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul **filtro delle directory e delle sottoscrizioni** nel menu in alto e scegliere la directory che contiene il tenant di Azure AD B2C.
2. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
3. Selezionare **Provider di identità** e quindi selezionare **Aggiungi**.
4. Immettere un **Nome**. Ad esempio, immettere "Contoso Azure AD".
5. Fare clic su **Tipo di provider di identità**, selezionare **OpenID Connect (anteprima)** e quindi fare clic su **OK**.
6. Fare clic su **Configura questo provider di identità**
7. In **URL dei metadati** immettere l'URL seguente, sostituendo `your-AD-tenant-domain` con il nome di dominio del tenant di Azure AD. Ad esempio, `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

8. In **ID client** e **Segreto client** immettere rispettivamente l'ID applicazione e il valore della chiave registrati in precedenza.
9. Facoltativamente immettere un valore per **Domain_hint**. Ad esempio: `ContosoAD`. Si tratta del valore da usare quando si fa riferimento a questo provider di identità specificando *domain_hint* nella richiesta. 
10. Fare clic su **OK**.
11. Selezionare **Esegui mapping delle attestazioni di questo provider di identità** e impostare le attestazioni seguenti:
    
    - Per **ID utente** immettere `oid`.
    - Per **nome visualizzato** immettere `name`.
    - Per **Nome** immettere `given_name`.
    - Per **Cognome** immettere `family_name`.
    - In **Posta elettronica** immettere `unique_name`.

12. Fare clic su **OK** e quindi su **Crea** per salvare la configurazione.
