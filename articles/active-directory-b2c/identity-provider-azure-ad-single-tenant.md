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
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 5b21fcd2d3ec5560b01352b112e9ed1bb2404766
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678058"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Configurare l'accesso per un'organizzazione Azure Active Directory specifica in Azure Active Directory B2C

Per usare un account di Azure Active Directory (Azure AD) come [provider di identità](authorization-code-flow.md) in Azure AD B2C, è necessario creare un'applicazione che lo rappresenti. Questo articolo illustra come consentire l'accesso agli utenti di una specifica organizzazione di Azure Active Directory (Azure AD) usando un flusso utente in Azure Active Directory (Azure AD) B2C.

[!INCLUDE [active-directory-b2c-identity-provider-azure-ad](../../includes/active-directory-b2c-identity-provider-azure-ad.md)]

## <a name="configure-azure-ad-as-an-identity-provider"></a>Configurare Azure AD come provider di identità

1. Assicurarsi di usare la directory che contiene il tenant B2C di Azure AD. Nel menu superiore, selezionare il filtro **Directory e sottoscrizione** e scegliere la directory che contiene il tenant B2C di Azure AD.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Provider di identità**, quindi **nuovo provider OpenID Connect**.
1. Immettere un **Nome**. Ad esempio, immettere *Contoso Azure AD*.
1. Per **URL metadati**immettere `{tenant}` l'URL seguente sostituendo con il nome di dominio del tenant di Azure AD:

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    Ad esempio: `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.

1. Per **ID client**immettere l'ID applicazione registrato in precedenza.
1. Per **Segreto client**immettere il segreto client registrato in precedenza.
1. Per **l'ambito** `openid profile`, immettere il file .
1. Lasciare i valori predefiniti per **Tipo di risposta**e **Modalità risposta**.
1. (Facoltativo) Per **l'hint** `contoso.com`dominio , immettere . Per altre informazioni, vedere [Configurare l'accesso diretto tramite Active Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. In **Mapping attestazioni provider**di identità selezionare le attestazioni seguenti:

    * **ID utente**: *oid*
    * **Nome visualizzato**: *nome*
    * **Nome dato**: *given_name*
    * **Cognome**: *family_name*
    * **E-mail**: *unique_name*

1. Selezionare **Salva**.
