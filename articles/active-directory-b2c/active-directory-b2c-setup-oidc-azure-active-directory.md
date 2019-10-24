---
title: Configurare l'accesso per un'organizzazione Azure Active Directory-Azure Active Directory B2C
description: Configurare l'accesso per un'organizzazione Azure Active Directory specifica in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 0c2e368b9c12d8ab673e5b8808632501de448b9a
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755758"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Configurare l'accesso per un'organizzazione Azure Active Directory specifica in Azure Active Directory B2C

Per usare un account di Azure Active Directory (Azure AD) come [provider di identità](active-directory-b2c-reference-oauth-code.md) in Azure AD B2C, è necessario creare un'applicazione che lo rappresenti. Questo articolo illustra come consentire l'accesso agli utenti di una specifica organizzazione di Azure Active Directory (Azure AD) usando un flusso utente in Azure Active Directory (Azure AD) B2C.

## <a name="create-an-azure-ad-app"></a>Creare un'app di Azure AD

Per abilitare l'accesso agli utenti da una specifica organizzazione di Azure AD, è necessario registrare un'applicazione all'interno del tenant aziendale di Azure AD che non corrisponde al tenant di Azure AD B2C.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Assicurarsi di usare la directory che contiene il tenant del Azure AD. Selezionare il filtro **directory + sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant Azure ad. Non si tratta dello stesso tenant del tenant Azure AD B2C.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Registrazioni per l'app**.
4. Selezionare **Nuova registrazione**.
5. Immettere un nome per l'applicazione. Ad esempio `Azure AD B2C App`.
6. Accetta la selezione degli **account in questa directory organizzativa solo** per questa applicazione.
7. Per l' **URI di reindirizzamento**accettare il valore di **Web**e immettere l'URL seguente in lettere minuscole, in cui `your-B2C-tenant-name` viene sostituito con il nome del tenant di Azure ad B2C. Ad esempio, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`:

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Tutti gli URL dovrebbero ora usare [b2clogin.com](b2clogin.md).

8. Fare clic su **Register**. Copiare l' **ID applicazione (client)** da usare in un secondo momento.
9. Selezionare **certificati & segreti** nel menu applicazione e quindi selezionare **nuovo segreto client**.
10. Immettere un nome per il segreto client. Ad esempio `Azure AD B2C App Secret`.
11. Selezionare il periodo di scadenza. Per questa applicazione, accettare la selezione di **tra 1 anno**.
12. Selezionare **Aggiungi** e copiare il valore del nuovo segreto client visualizzato per essere usato in un secondo momento.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Configurare Azure AD come provider di identità

1. Assicurarsi di usare la directory che contiene Azure AD B2C tenant. Selezionare il filtro **directory + sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant Azure ad B2C.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **provider di identità**e quindi fare clic su **nuovo provider OpenID Connect**.
1. Immettere un **Nome**. Ad esempio, immettere *Contoso Azure AD*.
1. Per **URL metadati**immettere l'URL seguente sostituendo `your-AD-tenant-domain` con il nome di dominio del tenant di Azure ad:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Ad esempio `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

    **Non usare l'** endpoint dei metadati Azure ad v 2.0, ad esempio `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`. In questo modo si ottiene un errore simile a `AADB2C: A claim with id 'UserId' was not found, which is required by ClaimsTransformation 'CreateAlternativeSecurityId' with id 'CreateAlternativeSecurityId' in policy 'B2C_1_SignUpOrIn' of tenant 'contoso.onmicrosoft.com'` durante il tentativo di accesso.

1. Per **ID client**immettere l'ID applicazione registrato in precedenza.
1. Per il **segreto client**, immettere il segreto client registrato in precedenza.
1. Lasciare i valori predefiniti per **ambito**, **tipo di risposta**e **modalità di risposta**.
1. Opzionale Immettere un valore per **Domain_hint**. Ad esempio, *ContosoAD*. Si tratta del valore da usare quando si fa riferimento a questo provider di identità specificando *domain_hint* nella richiesta.
1. In **mapping delle attestazioni del provider di identità**immettere i valori di mapping delle attestazioni seguenti:

    * **ID utente**: *OID*
    * **Nome visualizzato**: *nome*
    * **Nome specificato**: *given_name*
    * **Cognome**: *family_name*
    * **Posta elettronica**: *UNIQUE_NAME*

1. Selezionare **Salva**.
