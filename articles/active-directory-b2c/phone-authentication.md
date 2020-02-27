---
title: Iscrizione e accesso tramite telefono con criteri personalizzati (anteprima)
titleSuffix: Azure AD B2C
description: Inviare password monouso (OTP) negli SMS ai telefoni degli utenti dell'applicazione con criteri personalizzati in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 50e7d66fef67e2728c95790947393de8d58398c2
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77647519"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c-preview"></a>Configurare l'iscrizione e l'accesso tramite telefono con criteri personalizzati in Azure AD B2C (anteprima)

L'iscrizione e l'accesso tramite telefono in Azure Active Directory B2C (Azure AD B2C) consente agli utenti di iscriversi e accedere alle applicazioni usando una password monouso (OTP) inviata in un messaggio di testo al telefono. Le password monouso consentono di ridurre al minimo il rischio che gli utenti dimentichino o abbiano compromessa la loro password.

Attenersi alla procedura descritta in questo articolo per usare i criteri personalizzati per consentire ai clienti di iscriversi e accedere alle applicazioni usando una password monouso inviata al telefono.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="pricing"></a>Prezzi

Le password monouso vengono inviate agli utenti tramite SMS ed è possibile che vengano addebitati i costi per ogni messaggio inviato. Per informazioni sui prezzi, vedere la sezione relativa agli **addebiti separati** di [Azure Active Directory B2C prezzi](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="prerequisites"></a>Prerequisites

Prima di configurare OTP è necessario disporre delle risorse seguenti.

* [Tenant di Azure AD B2C](tutorial-create-tenant.md)
* [Applicazione Web registrata](tutorial-register-applications.md) nel tenant
* [Criteri personalizzati](custom-policy-get-started.md) caricati nel tenant

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>Ottenere il pacchetto Starter & per l'iscrizione al telefono

Per iniziare, aggiornare i file di criteri personalizzati per l'iscrizione e l'accesso tramite telefono per l'uso con il tenant Azure AD B2C.

Nei passaggi seguenti si presuppone che siano stati completati i [prerequisiti](#prerequisites) e che sia già stato clonato il repository [Starter Pack dei criteri personalizzati][starter-pack] nel computer locale.

1. Individuare i [file di criteri personalizzati per l'iscrizione e l'accesso tramite telefono][starter-pack-phone] nel clone locale del repository Starter Pack oppure scaricarli direttamente. I file dei criteri XML si trovano nella seguente directory:

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. In ogni file sostituire la stringa `yourtenant` con il nome del tenant del Azure AD B2C. Ad esempio, se il nome del tenant B2C è *contosob2c*, tutte le istanze di `yourtenant.onmicrosoft.com` diventano `contosob2c.onmicrosoft.com`.

1. Completare i passaggi della sezione [aggiungere ID applicazione ai criteri personalizzati](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) di [Introduzione ai criteri personalizzati in Azure Active Directory B2C](custom-policy-get-started.md). In questo caso, aggiornare `/phone-number-passwordless/` **`Phone_Email_Base.xml`** con gli **ID dell'applicazione (client)** delle due applicazioni registrate durante il completamento dei prerequisiti, *IdentityExperienceFramework* e *ProxyIdentityExperienceFramework*.

## <a name="upload-the-policy-files"></a>Caricare i file dei criteri

1. Accedere al [portale di Azure](https://portal.azure.com) e passare al tenant di Azure ad B2C.
1. In **criteri**selezionare **Framework esperienza di identità**.
1. Selezionare **carica criteri personalizzati**.
1. Caricare i file dei criteri nell'ordine seguente:
    1. *Phone_Email_Base. XML*
    1. *SignUpOrSignInWithPhone. XML*
    1. *SignUpOrSignInWithPhoneOrEmail. XML*
    1. *ProfileEditPhoneOnly. XML*
    1. *ProfileEditPhoneEmail. XML*
    1. *ChangePhoneNumber. XML*
    1. *PasswordResetEmail. XML*

Quando si carica ogni file, Azure aggiunge il prefisso `B2C_1A_`.

## <a name="test-the-custom-policy"></a>Testare i criteri personalizzati

1. In **criteri personalizzati**selezionare **B2C_1A_SignUpOrSignInWithPhone**.
1. In **Seleziona applicazione**selezionare l'applicazione *app Web 1* registrata al termine dei prerequisiti.
1. Per **Seleziona URL di risposta**scegliere `https://jwt.ms`.
1. Selezionare **Esegui adesso** e iscriversi usando un indirizzo di posta elettronica o un numero di telefono.
1. Selezionare **Esegui** ancora una volta e accedere con lo stesso account per confermare di avere la configurazione corretta.

## <a name="get-user-account-by-phone-number"></a>Ottieni l'account utente per numero di telefono

Un utente che si iscrive con un numero di telefono, ma non fornisce un indirizzo di posta elettronica di ripristino, viene registrato nella directory Azure AD B2C con il proprio numero di telefono come nome di accesso. Se l'utente desidera modificare il numero di telefono, il help desk o il team di supporto deve prima trovare il proprio account, quindi aggiornare il numero di telefono.

È possibile trovare un utente con il proprio numero di telefono (nome di accesso) usando [Microsoft Graph](manage-user-accounts-graph-api.md):

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+{phone number}' and c/issuer eq '{tenant name}.onmicrosoft.com')
```

Ad esempio:

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+450334567890' and c/issuer eq 'contosob2c.onmicrosoft.com')
```

## <a name="next-steps"></a>Passaggi successivi

È possibile trovare lo Starter Pack per l'iscrizione e l'accesso ai criteri personalizzati (e altri Starter Pack) su GitHub:

[Azure-Samples/Active-Directory-B2C-Custom-Policy-Starterpack/Scenarios/Phone-number-password][starter-pack-phone]

I file dei criteri dello Starter Pack usano i profili tecnici di autenticazione a più fattori e le trasformazioni delle attestazioni del numero di telefono:

* [Definire un profilo tecnico Multi-Factor Authentication di Azure](multi-factor-auth-technical-profile.md)
* [Definire le trasformazioni delle attestazioni del numero di telefono](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
