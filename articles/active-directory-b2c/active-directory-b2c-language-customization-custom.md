---
title: 'Azure Active Directory B2C: personalizzazione della lingua nei criteri personalizzati| Microsoft Docs'
description: "Informazioni su come usare il contenuto da localizzare nei criteri personalizzati per più lingue"
services: active-directory-b2c
documentationcenter: 
author: sammak
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 11/13/2017
ms.author: sama
ms.openlocfilehash: 4ed9791d6590e3982a1bc79b96f8592995bc315c
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
#<a name="language-customization-in-custom-policies"></a>Personalizzazione della lingua nei criteri personalizzati

> [!NOTE]
> Questa funzionalità è disponibile in anteprima pubblica.
> 

Nei criteri personalizzati, la personalizzazione della lingua funziona esattamente come nei criteri predefiniti.  Vedere la [documentazione](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-language-customization) predefinita che descrive il comportamento nel modo in cui una lingua viene scelta in base ai parametri e alle impostazioni del browser.

##<a name="enable-supported-languages"></a>Abilitare le lingue supportate
Se il parametro ui-locales non è stato specificato e il browser dell'utente chiede una di queste lingue, l'utente visualizza le lingue supportate.  

Le lingue supportate sono definite in `<BuildingBlocks>` nel formato seguente:

```XML
<BuildingBlocks>
  <Localization>
    <SupportedLanguages DefaultLanguage="en">
      <SupportedLanguage>qps-ploc</SupportedLanguage>
      <SupportedLanguage>en</SupportedLanguage>
    </SupportedLanguages>
  </Localization>
</BuildingBlocks>
```

La lingua predefinita e le lingue supportate si comportano allo stesso modo dei criteri predefiniti.

##<a name="enable-custom-language-strings"></a>Abilitare le stringhe delle lingue personalizzate

La creazione delle stringhe delle lingue personalizzate richiede due passaggi:
1. Modificare la `<ContentDefinition>` della pagina per specificare un ID di risorsa per le lingue desiderate
2. Creare le `<LocalizedResources>` con ID corrispondenti nei `<BuildingBlocks>`

Tenere presente che è possibile inserire una `<ContentDefinition>` e un `<BuildingBlock>` sia nel file di estensione che nel file dei criteri di relying a seconda che si desideri inserire le modifiche in tutti i criteri ereditanti o meno.

###<a name="edit-the-contentdefinition-for-the-page"></a>Modificare ContentDefinition per la pagina

Per ogni pagina che si desidera localizzare, nella `<ContentDefinition>` è possibile specificare le risorse di lingua da ricercare per ogni codice lingua.

```XML
<ContentDefinition Id="api.signuporsignin">
      <LocalizedResourcesReferences>
        <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="fr" />
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="en" />
      </LocalizedResourcesReferences>
</ContentDefinition>
```

In questo esempio, le stringhe personalizzate in francese (fr) e inglese (en) vengono aggiunte alla pagina unificata per l'iscrizione o l'accesso.  Il `LocalizedResourcesReferenceId` per ogni `LocalizedResourcesReference` è lo stesso delle impostazioni locali, ma è possibile usare qualsiasi stringa come ID.  Per ogni combinazione di lingua e di pagina, è necessario creare un `<LocalizedResources>` corrispondente illustrato di seguito.


###<a name="create-the-localizedresources"></a>Creare le LocalizedResources

Le sostituzioni sono contenute nei `<BuildingBlocks>` ed è presente una `<LocalizedResources>` per ogni pagina e lingua specificata nella `<ContentDefinition>` per ogni pagina.  Ogni sostituzione viene specificata come una `<LocalizedString>` come nell'esempio seguente:

```XML
<BuildingBlocks>
  <Localization>
    <LocalizedResources Id="en">
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimsProvider" StringId="SignInWithLogonNameExchange">Local Account Sign-in</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="DisplayName">Username</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="UserHelpText">Username used for signing in.</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="PatternHelpText">The username you provided is not valid.</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_signin">Sign In Now</LocalizedString>
        <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">Your password is incorrect.</LocalizedString>
      </LocalizedStrings>
    </LocalizedResources>
  </Localization>
</BuildingBlocks>
```

Sono disponibili quattro tipi di elementi stringa nella pagina:

**ClaimsProvider**: etichette per i provider di identità (Facebook, Google, Azure AD e così via). **ClaimType**: etichette per gli attributi e il relativo testo della guida, o errori di convalida del campo **UxElement**: altri elementi stringa nella pagina presenti per impostazione predefinita, ad esempio i pulsanti, i collegamenti o il testo **ErrorMessage**: messaggi di errore di convalida modulo

Verificare che `StringId` corrispondano per la pagina in cui si stanno usando le sostituzioni o in caso contrario viene bloccata dalla convalida dei criteri al momento del caricamento.  