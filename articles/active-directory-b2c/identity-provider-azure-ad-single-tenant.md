---
title: Configurare l'accesso per un'organizzazione Azure AD
titleSuffix: Azure AD B2C
description: Configurare l'accesso per un'organizzazione Azure Active Directory specifica in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: a079cfe155119a6afe8575767dd3e7c09a564f9a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445624"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Configurare l'accesso per un'organizzazione Azure Active Directory specifica in Azure Active Directory B2C

Per usare un account di Azure Active Directory (Azure AD) come [provider di identità](authorization-code-flow.md) in Azure AD B2C, è necessario creare un'applicazione che lo rappresenti. Questo articolo illustra come consentire l'accesso agli utenti di una specifica organizzazione di Azure Active Directory (Azure AD) usando un flusso utente in Azure Active Directory (Azure AD) B2C.

[!INCLUDE [active-directory-b2c-identity-provider-azure-ad](../../includes/active-directory-b2c-identity-provider-azure-ad.md)]

## <a name="configure-azure-ad-as-an-identity-provider"></a>Configurare Azure AD come provider di identità

1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory contenente il tenant di Azure AD B2C.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.
1. Selezionare **Provider di identità** e quindi selezionare **Nuovo provider di OpenID Connect**.
1. Immettere un **Nome**. Ad esempio, immettere *Contoso Azure AD*.
1. In **URL dei metadati** immettere l'URL seguente, sostituendo `{tenant}` con il nome di dominio del tenant di Azure AD:

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    Ad esempio: `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.
    Ad esempio: `https://login.microsoftonline.com/contoso.com/v2.0/.well-known/openid-configuration`.

1. Per **ID client**, immettere l'ID applicazione registrato in precedenza.
1. In **Segreto client** immettere il segreto client registrato in precedenza.
1. Per l' **ambito**, immettere `openid profile` .
1. Lasciare i valori predefiniti per il **tipo di risposta** e la modalità di **risposta**.
1. Opzionale Per l' **hint di dominio**, immettere `contoso.com` . Per altre informazioni, vedere [Configurare l'accesso diretto tramite Active Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. In **mapping attestazioni provider di identità** selezionare le seguenti attestazioni:

    * **ID utente**: *oid*
    * **Nome visualizzato**: *name*
    * **Nome**: *given_name*
    * **Cognome**: *family_name*
    * **Posta elettronica**: *preferred_username*

1. Selezionare **Salva**.
