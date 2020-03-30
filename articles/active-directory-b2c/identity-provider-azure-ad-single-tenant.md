---
title: Configurare l'accesso per un'organizzazione di Azure ADSet up sign-in for an Azure AD organization
titleSuffix: Azure AD B2C
description: Configurare l'accesso per un'organizzazione Azure Active Directory specifica in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 35fc4e1d64fa7df392fa878db14c0464da7dccf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188308"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Configurare l'accesso per un'organizzazione Azure Active Directory specifica in Azure Active Directory B2C

Per usare un account di Azure Active Directory (Azure AD) come [provider di identità](authorization-code-flow.md) in Azure AD B2C, è necessario creare un'applicazione che lo rappresenti. Questo articolo illustra come consentire l'accesso agli utenti di una specifica organizzazione di Azure Active Directory (Azure AD) usando un flusso utente in Azure Active Directory (Azure AD) B2C.

## <a name="create-an-azure-ad-app"></a>Creare un'app di Azure AD

Per abilitare l'accesso agli utenti da una specifica organizzazione di Azure AD, è necessario registrare un'applicazione all'interno del tenant aziendale di Azure AD che non corrisponde al tenant di Azure AD B2C.

1. Accedere al [portale](https://portal.azure.com)di Azure .
2. Assicurarsi di usare la directory che contiene il tenant di Azure AD. Nel menu superiore, selezionare il filtro **Directory e sottoscrizione** e scegliere la directory che contiene il tenant di Azure AD. Non si tratta dello stesso tenant del tenant B2C di Azure AD.
3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Registrazioni per l'app**.
4. Selezionare **Nuova registrazione**.
5. Immettere un nome per l'applicazione. Ad esempio: `Azure AD B2C App`.
6. Accettare la selezione di **Account in questa directory dell'organizzazione solo** per questa applicazione.
7. Per l'URI di **reindirizzamento**, accettare il valore di **Web** `your-B2C-tenant-name` e immettere l'URL seguente in lettere minuscole, dove viene sostituito con il nome del tenant B2C di Azure AD. Ad esempio, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`:

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Tutti gli URL dovrebbero ora usare [b2clogin.com](b2clogin.md).

8. Fare clic su **Registra**. Copiare **l'ID applicazione (client)** da utilizzare in un secondo momento.
9. Selezionare **Certificati & segreti** nel menu dell'applicazione, quindi nuovo **segreto client**.
10. Immettere un nome per il segreto client. Ad esempio: `Azure AD B2C App Secret`.
11. Selezionare il periodo di scadenza. Per questa applicazione, accettare la selezione di **In 1 anno**.
12. Selezionare **Aggiungi** e copiare il valore del nuovo segreto client che viene visualizzato per essere utilizzato in un secondo momento.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Configurare Azure AD come provider di identità

1. Assicurarsi di usare la directory che contiene il tenant B2C di Azure AD. Nel menu superiore, selezionare il filtro **Directory e sottoscrizione** e scegliere la directory che contiene il tenant B2C di Azure AD.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Provider di identità**, quindi **nuovo provider OpenID Connect**.
1. Immettere un **Nome**. Ad esempio, immettere *Contoso Azure AD*.
1. Per **URL metadati**immettere `your-AD-tenant-domain` l'URL seguente sostituendo con il nome di dominio del tenant di Azure AD:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Ad esempio: `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

    **Non** usare l'endpoint dei metadati di `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`Azure AD v2.0, ad esempio . In questo modo si `AADB2C: A claim with id 'UserId' was not found, which is required by ClaimsTransformation 'CreateAlternativeSecurityId' with id 'CreateAlternativeSecurityId' in policy 'B2C_1_SignUpOrIn' of tenant 'contoso.onmicrosoft.com'` verifica un errore simile a quando si tenta di accedere.

1. Per **ID client**immettere l'ID applicazione registrato in precedenza.
1. Per **Segreto client**immettere il segreto client registrato in precedenza.
1. Lasciare i valori predefiniti per **Ambito**, **Tipo di risposta**e **Modalità risposta**.
1. (Facoltativo) Immettere un valore per **Domain_hint**. Ad esempio, *ContosoAD*. Si tratta del valore da usare quando si fa riferimento a questo provider di identità specificando *domain_hint* nella richiesta.
1. In **Mapping attestazioni provider**di identità immettere i valori di mapping delle attestazioni seguenti:

    * **ID utente**: *oid*
    * **Nome visualizzato**: *nome*
    * **Nome dato**: *given_name*
    * **Cognome**: *family_name*
    * **E-mail**: *unique_name*

1. Selezionare **Salva**.
