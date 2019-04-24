---
title: ClaimsProviders - Azure Active Directory B2C | Microsoft Docs
description: Specificare l'elemento ClaimsProvider dei criteri personalizzati in Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 2d862d07a65c3fb28b49a82692ea575f787b9750
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60313852"
---
# <a name="claimsproviders"></a>ClaimsProviders

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Un provider di attestazioni contiene un set di [profili tecnici](technicalprofiles.md). Ogni provider di attestazioni deve disporre di uno o più profili tecnici che determinano gli endpoint e i protocolli necessari per comunicare con il provider stesso. Un provider di attestazioni può avere più profili tecnici. Potrebbero essere definiti più profili tecnici, ad esempio, perché il provider di attestazioni supporta più protocolli o vari endpoint con funzionalità diverse oppure rilascia attestazioni diverse per livelli di controllo diversi. Potrebbe essere accettabile rilasciare attestazioni sensibili in un percorso utente, ma non in un altro.

```XML
<ClaimsProviders>
  <ClaimsProvider>
    <Domain>Domain name</Domain>
    <DisplayName>Display name</DisplayName>
    <TechnicalProfiles>
      </TechnicalProfile>
        ...
      </TechnicalProfile>
        ...
    </TechnicalProfiles>
  </ClaimsProvider>
  ...
</ClaimsProviders>
```

**ClaimsProviders** contiene l'elemento seguente:

| Elemento | Occorrenze | DESCRIZIONE |
| ------- | ----------- | ----------- |
| ClaimsProvider | 1:n | Provider di attestazioni accreditato che può essere sfruttato in vari percorsi utente. |

## <a name="claimsprovider"></a>ClaimsProvider

**ClaimsProvider** contiene gli elementi figlio seguenti:

| Elemento | Occorrenze | DESCRIZIONE |
| ------- | ---------- | ----------- |
| Domain | 0:1 | Stringa contenente il nome di dominio per il provider di attestazioni. Se il provider di attestazioni include il profilo tecnico Facebook, ad esempio, il nome di dominio è Facebook.com. Questo nome di dominio viene usato per tutti i profili tecnici definiti nel provider di attestazioni a meno che il profilo tecnico non ne esegua l'override. Può essere fatto riferimento al nome di dominio anche in **domain_hint**. Per altre informazioni, vedere la sezione **Reindirizzare l'accesso a un provider di social networking** in [Configurare l'accesso diretto tramite Azure Active Directory B2C](direct-signin.md). |
| DisplayName | 0:1 | Stringa contenente il nome del provider di attestazioni visualizzabile agli utenti. |
| [TechnicalProfiles](technicalprofiles.md) | 0:1 | Set di profili tecnici supportato dal provider di attestazioni. |

**ClaimsProvider** organizza correlazione tra i profili tecnici per il provider di attestazioni. L'esempio seguente illustra il provider di attestazioni Azure Active Directory con i profili tecnici di Azure Active Directory:

```XML
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AAD-Common">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      ...
    </TechnicalProfile>
      ...
    <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWritePhoneNumberUsingObjectId">
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

L'esempio seguente illustra il provider di attestazioni Facebook con il profilo tecnico **Facebook-OAUTH**.

```XML
<ClaimsProvider>
  <Domain>facebook.com</Domain>
  <DisplayName>Facebook</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Facebook-OAUTH">
      <DisplayName>Facebook</DisplayName>
      <Protocol Name="OAuth2" />
        ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
