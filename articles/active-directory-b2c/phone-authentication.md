---
title: Iscrizione e accesso al telefono con criteri personalizzati (anteprima)
titleSuffix: Azure AD B2C
description: Inviare password monouso (OTP) nei messaggi di testo ai telefoni degli utenti dell'applicazione con criteri personalizzati in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: eadac0e973b361b1fdee63dcc9cfa848a0b2bacb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183959"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c-preview"></a>Configurare l'iscrizione e l'accesso al telefono con criteri personalizzati in Azure AD B2C (anteprima)Set up phone sign-up and sign-in with custom policies in Azure AD B2C (Preview)

L'iscrizione e l'accesso telefonico in Azure Active Directory B2C (Azure AD B2C) consentono agli utenti di iscriversi e accedere alle applicazioni usando una password monouso (OTP) inviata in un messaggio di testo al telefono. Le password monouso possono ridurre al minimo il rischio che gli utenti dimentichino o le compromesse vengano compromesse.

Seguire i passaggi descritti in questo articolo per usare i criteri personalizzati per consentire ai clienti di iscriversi e accedere alle applicazioni utilizzando una password monouso inviata al telefono.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="pricing"></a>Prezzi

Le password monouso vengono inviate agli utenti tramite SMS e potrebbe essere addebitato alcun costo per ogni messaggio inviato. Per informazioni sui prezzi, vedere la sezione **Addebiti separati** di [Prezzi B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/)di Azure Active Directory.

## <a name="prerequisites"></a>Prerequisiti

Prima di configurare OTP, sono necessarie le risorse seguenti.

* [Tenant di Azure AD B2C](tutorial-create-tenant.md)
* [Applicazione Web registrata](tutorial-register-applications.md) nel tenant
* [Criteri personalizzati](custom-policy-get-started.md) caricati nel tenant

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>Ottenere lo starter pack per l'iscrizione al telefono & di accesso

Iniziare aggiornando i file di criteri personalizzati di iscrizione al telefono e di accesso per l'utilizzo con il tenant B2C di Azure AD.

I passaggi seguenti presuppongono che [i prerequisiti](#prerequisites) siano stati completati e che il repository del [pacchetto starter pack dei criteri personalizzato][starter-pack] sia già stato clonato nel computer locale.

1. Trova i file di criteri personalizzati di [iscrizione e accesso][starter-pack-phone] al telefono nel clone locale del repository starter pack oppure scaricali direttamente. I file dei criteri XML si trovano nella seguente directory:

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. In ogni file sostituire `yourtenant` la stringa con il nome del tenant B2C di Azure AD. Ad esempio, se il nome del tenant B2C è `yourtenant.onmicrosoft.com` *contosob2c*, tutte le istanze di become `contosob2c.onmicrosoft.com`.

1. Completare i passaggi descritti nella sezione [Aggiungere ID applicazione alla](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) sezione Criteri personalizzati di Introduzione [ai criteri personalizzati in Azure Active Directory B2C.](custom-policy-get-started.md) In questo caso, `/phone-number-passwordless/` **`Phone_Email_Base.xml`** eseguire l'aggiornamento con gli **ID dell'applicazione (client)** delle due applicazioni registrate durante il completamento dei prerequisiti, *IdentityExperienceFramework* e *ProxyIdentityExperienceFramework*.

## <a name="upload-the-policy-files"></a>Caricare i file di criteri

1. Accedere al [portale](https://portal.azure.com) di Azure e passare al tenant B2C di Azure AD.
1. In **Criteri**selezionare **Framework di esperienza identità**.
1. Selezionare **Carica criterio personalizzato**.
1. Caricare i file di criteri nel seguente ordine:
    1. *Phone_Email_Base.xml*
    1. *SignUpOrSignInWithPhone.xml*
    1. *SignUpOrSignInWithPhoneOrEmail.xml*
    1. *ProfileEditPhoneOnly.xml*
    1. *ProfileEditPhoneEmail.xml*
    1. *ChangePhoneNumber.xml*
    1. *PasswordResetEmail.xml*

Quando si carica ogni file, `B2C_1A_`Azure aggiunge il prefisso .

## <a name="test-the-custom-policy"></a>Testare i criteri personalizzati

1. In **Criteri personalizzati**selezionare **B2C_1A_SignUpOrSignInWithPhone**.
1. In **Seleziona applicazione**selezionare l'applicazione *webapp1* registrata al completamento dei prerequisiti.
1. Per Seleziona url `https://jwt.ms`di **risposta**scegliere .
1. Seleziona **Esegui ora** e iscriviti utilizzando un indirizzo e-mail o un numero di telefono.
1. Selezionare **Esegui di** nuovo e accedere con lo stesso account per verificare di disporre della configurazione corretta.

## <a name="get-user-account-by-phone-number"></a>Ottenere l'account utente in base al numero di telefono

Un utente che si iscrive a un numero di telefono ma non fornisce un indirizzo di posta elettronica di ripristino viene registrato nella directory B2C di Azure AD con il proprio numero di telefono come nome di accesso. Se l'utente desidera modificare il proprio numero di telefono, l'help desk o il team di supporto deve prima trovare il proprio account e quindi aggiornare il proprio numero di telefono.

È possibile trovare un utente in base al numero di telefono (nome di accesso) utilizzando [Microsoft Graph:](manage-user-accounts-graph-api.md)

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+{phone number}' and c/issuer eq '{tenant name}.onmicrosoft.com')
```

Ad esempio:

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+450334567890' and c/issuer eq 'contosob2c.onmicrosoft.com')
```

## <a name="next-steps"></a>Passaggi successivi

Puoi trovare il pacchetto starter personalizzato per l'iscrizione al telefono e l'accesso (e altri starter pack) su GitHub:

[Azure-Samples/active-directory-b2c-custom-policy-starterpack/scenarios/phone-number-passwordless][starter-pack-phone]

I file dei criteri dello starter pack utilizzano profili tecnici per l'autenticazione a più fattori e trasformazioni delle attestazioni dei numeri di telefono:

* [Definire un profilo tecnico di Azure Multi-Factor AuthenticationDefine an Azure Multi-Factor Authentication technical profile](multi-factor-auth-technical-profile.md)
* [Definire le trasformazioni delle attestazioni del numero di telefonoDefine phone number claims transformations](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
