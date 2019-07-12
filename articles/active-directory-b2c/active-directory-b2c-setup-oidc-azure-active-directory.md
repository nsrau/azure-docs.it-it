---
title: Configurare l'accesso per un'organizzazione di Azure Active Directory - Azure Active Directory B2C
description: Configurare l'accesso per un'organizzazione Azure Active Directory specifica in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b867a7f9ffeab3d243c8c094830aa0984cffd04a
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67654211"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Configurare l'accesso per un'organizzazione Azure Active Directory specifica in Azure Active Directory B2C

>[!NOTE]
> Questa funzionalità è disponibile in anteprima pubblica. Non utilizzare la funzionalità negli ambienti di produzione.

Per usare un account di Azure Active Directory (Azure AD) come [provider di identità](active-directory-b2c-reference-oauth-code.md) in Azure AD B2C, è necessario creare un'applicazione che lo rappresenti. Questo articolo illustra come consentire l'accesso agli utenti di una specifica organizzazione di Azure Active Directory (Azure AD) usando un flusso utente in Azure Active Directory (Azure AD) B2C.

## <a name="create-an-azure-ad-app"></a>Creare un'app di Azure AD

Per abilitare l'accesso agli utenti da una specifica organizzazione di Azure AD, è necessario registrare un'applicazione all'interno del tenant aziendale di Azure AD che non corrisponde al tenant di Azure AD B2C.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Assicurarsi che si usa la directory contenente i tenant di Azure AD. Selezionare il **Directory e sottoscrizione filtro** nel menu in alto e scegliere la directory contenente i tenant di Azure AD. Questo non è nello stesso tenant del tenant di Azure AD B2C.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Registrazioni per l'app**.
4. Selezionare **Nuova registrazione**.
5. Immettere un nome per l'applicazione. Ad esempio `Azure AD B2C App`.
6. Accettare la selezione dei **gli account in questa directory dell'organizzazione solo** per questa applicazione.
7. Per il **URI di reindirizzamento**, accettare il valore **Web**, quindi immettere l'URL seguente in lettere minuscole, in cui `your-B2C-tenant-name` viene sostituito con il nome del tenant di Azure AD B2C. Ad esempio, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`:

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Tutti gli URL dovrebbero ora usare [b2clogin.com](b2clogin.md).

8. Fare clic su **Register**. Copia il **ID applicazione (client)** da usare in un secondo momento.
9. Selezionare **certificati e i segreti** nel menu dell'applicazione e quindi selezionare **nuovo segreto client**.
10. Immettere un nome per il segreto client. Ad esempio `Azure AD B2C App Secret`.
11. Selezionare il periodo di scadenza. Per questa applicazione, la selezione di accettare **tra 1 anno**.
12. Selezionare **Add** e copiare il valore del nuovo segreto client che viene visualizzato per l'uso in un secondo momento.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Configurare Azure AD come provider di identità

1. Assicurarsi che si usa la directory contenente i tenant di Azure AD B2C. Selezionare il **Directory e sottoscrizione filtro** nel menu in alto e scegliere la directory che contiene il tenant di Azure AD B2C.
2. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
3. Selezionare **Provider di identità** e quindi selezionare **Aggiungi**.
4. Immettere un **Nome**. Ad esempio, immettere `Contoso Azure AD`.
5. Fare clic su **Tipo di provider di identità**, selezionare **OpenID Connect (anteprima)** e quindi fare clic su **OK**.
6. Selezionare **configura questo provider di identità**
7. In **URL dei metadati** immettere l'URL seguente, sostituendo `your-AD-tenant-domain` con il nome di dominio del tenant di Azure AD. Ad esempio, `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

8. Per la **ID Client**, immettere l'ID dell'applicazione registrato in precedenza e per **privata del Client**, immettere il segreto client registrato in precedenza.
9. Facoltativamente immettere un valore per **Domain_hint**. Ad esempio `ContosoAD`. Si tratta del valore da usare quando si fa riferimento a questo provider di identità specificando *domain_hint* nella richiesta.
10. Fare clic su **OK**.
11. Selezionare **Esegui mapping delle attestazioni di questo provider di identità** e impostare le attestazioni seguenti:

    - Per **ID utente** immettere `oid`.
    - Per **nome visualizzato** immettere `name`.
    - Per **Nome** immettere `given_name`.
    - Per **Cognome** immettere `family_name`.
    - In **Posta elettronica** immettere `unique_name`.

12. Fare clic su **OK** e quindi su **Crea** per salvare la configurazione.
