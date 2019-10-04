---
title: TrustFrameworkPolicy - Azure Active Directory B2C | Microsoft Docs
description: Specificare l'elemento TrustFrameworkPolicy di un criterio personalizzato in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 673807377914aabad5b90d1ac2ecc16623870d30
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063362"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Un criterio personalizzato è rappresentato come uno o più file in formato XML che fanno riferimento l'uno all'altro in una catena gerarchica. Gli elementi XML definiscono gli elementi del criterio, come lo schema delle attestazioni, le trasformazioni delle attestazioni, le definizioni di contenuto, i provider di attestazioni, i profili tecnici, il percorso utente e le procedure di orchestrazione. Ogni file dei criteri è definito all'interno dell'elemento di primo livello **TrustFrameworkPolicy** di un file di criteri.

```XML
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
  ...
```


L'elemento **TrustFrameworkPolicy** contiene gli attributi seguenti:

| Attributo | Obbligatorio | Descrizione |
|---------- | -------- | ----------- |
| PolicySchemaVersion | Sì | La versione dello schema che deve essere usata per eseguire il criterio. Il valore deve essere `0.3.0.0`. |
| TenantObjectId | No | Identificatore di oggetto univoco del tenant di Azure Active Directory B2C (Azure AD B2C). |
| TenantId | Sì | L'identificatore univoco del tenant a cui appartiene questo criterio. |
| `PolicyId` | Yes | L'identificatore univoco del criterio. Questo identificatore deve essere preceduto da *B2C_1A_* |
| PublicPolicyUri | Sì | L'URI per il criterio, ovvero la combinazione dell'ID del tenant e l'ID del criterio. |
| DeploymentMode | No | I valori possibili sono: `Production`, `Debugging` o `Development`. `Production` è l'impostazione predefinita. Usare questa proprietà per eseguire il debug del criterio. Per altre informazioni, vedere [Raccolta dei log](active-directory-b2c-troubleshoot-custom.md). |
| UserJourneyRecorderEndpoint | No | L'endpoint che viene usata quando **DeploymentMode** è impostato su `Development`. Il valore deve essere `urn:journeyrecorder:applicationinsights`. Per altre informazioni, vedere [Raccolta dei log](active-directory-b2c-troubleshoot-custom.md). |


Nell'esempio seguente viene illustrato come specificare l'elemento **TrustFrameworkPolicy**:

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkBase"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

## <a name="inheritance-model"></a>Modello di ereditarietà

Questi tipi di file dei criteri vengono in genere utilizzati in un percorso utente:

- un file di **base** che contiene la maggior parte delle definizioni. Si consiglia di apportare un numero minimo di modifiche a questo file per contribuire alla risoluzione dei problemi e alla manutenzione a lungo termine dei criteri.
- Un file di **estensioni** che contiene le modifiche di configurazione univoche per il tenant. Questo file dei criteri è derivato dal file di base. Usare questo file per aggiungere nuove funzionalità o eseguire l'override delle funzionalità esistenti. Ad esempio, usare questo file per la federazione con nuovi provider di identità.
- Un file **Relying Party (RP)** è l'unico file incentrato sulle attività che viene chiamato direttamente dell'applicazione relying party, come applicazioni Web, mobili o desktop. Ogni attività univoca, ad esempio l'iscrizione, l'accesso, la reimpostazione della password o la modifica del profilo richiede un proprio file di criteri di relying party. Questo file di criteri è derivato dal file di estensioni.

Un'applicazione relying party chiama il file di criteri relying party per l'esecuzione di un'attività specifica. Ad esempio, per avviare il flusso di accesso. Il Framework di esperienza di identità in Azure AD B2C aggiunge tutti gli elementi prima dal file di base, poi dal file delle estensioni e infine dal file di criteri relying party per assemblare i criteri correnti in vigore. Gli elementi dello stesso tipo e nome nel file relying party eseguono l'override nelle estensioni e le estensioni eseguono l'override nel file di base. Il diagramma seguente mostra la relazione tra i file di criteri e le applicazioni relying party.

![Diagramma che mostra il modello di ereditarietà dei criteri di Framework attendibilità](./media/trustframeworkpolicy/custom-policy-Inheritance-model.png)

Il modello di ereditarietà è come segue:

- I criteri padre e figlio sono dello stesso schema.
- Il criterio figlio a qualsiasi livello può ereditare dal criterio padre ed estenderlo aggiungendo nuovi elementi.
- Non sono previsti limiti per il numero di livelli.

Per altre informazioni, vedere [Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md).

## <a name="base-policy"></a>Criteri di base

Per ereditare un criterio da un altro criterio, un elemento **BasePolicy** deve essere dichiarato sotto l'elemento **TrustFrameworkPolicy** del file di criteri. L'elemento **BasePolicy** è un riferimento ai criteri di base da cui deriva questo criterio.

L'elemento **BasePolicy** contiene gli elementi seguenti:

| Elemento | Occorrenze | DESCRIZIONE |
| ------- | ----------- | --------|
| TenantId | 1:1 | Identificatore del tenant di Azure AD B2C. |
| `PolicyId` | 1:1 | Identificatore del criterio padre. |


L'esempio seguente mostra come specificare un criterio di base. Questo criterio **B2C_1A_TrustFrameworkExtensions** deriva dal criterio **B2C_1A_TrustFrameworkBase**.

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkExtensions"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkExtensions">

  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkBase</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

## <a name="policy-execution"></a>Esecuzione criteri

Un'applicazione relying party, ad esempio un'applicazione Web, mobile o desktop, chiama i [criteri relying party (RP)](relyingparty.md). Il file di criteri RP esegue un'attività specifica, ad esempio l'accesso, la reimpostazione di una password o la modifica di un profilo. Il criterio di relying party configura l'elenco di attestazioni che l'applicazione relying party riceve come parte del token emesso. Più applicazioni possono usare gli stessi criteri. Tutte le applicazioni ricevono lo stesso token con attestazioni e l'utente passa attraverso lo stesso percorso utente. Una singola applicazione può usare più criteri.

All'interno del file di criteri di relying party, si specifica l'elemento **DefaultUserJourney**, che fa riferimento a [UserJourney](userjourneys.md). Il percorso utente viene in genere definito nei criteri di base o nelle estensioni.

Criterio B2C_1A_signup_signin:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

B2C_1A_TrustFrameWorkBase o B2C_1A_TrustFrameworkExtensionPolicy:

```XML
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

Un percorso utente definisce la logica di business di ciò che un utente effettua. Ogni percorso utente è un set di passaggi di orchestrazione che esegue una serie di azioni, in sequenza in termini di autenticazione e raccolta di informazioni.

Il file di criteri **SocialAndLocalAccounts** nel [pacchetto starter](active-directory-b2c-get-started-custom.md#custom-policy-starter-pack) contiene i percorsi utente SignUpOrSignIn, ProfileEdit, PasswordReset. È possibile aggiungere altri percorsi utente per altri scenari, ad esempio la modifica di un indirizzo di posta elettronica o il collegamento e lo scollegamento di un account di social networking.

I passaggi di orchestrazione possono chiamare un [profilo tecnico](technicalprofiles.md). Un profilo tecnico offre un framework con un meccanismo incorporato per comunicare con diversi tipi di entità. Ad esempio, un profilo tecnico può eseguire queste azioni tra le altre:

- Eseguire il rendering di un'esperienza utente.
- Consentire agli utenti di accedere con social network o un account dell'organizzazione, ad esempio Facebook, account Microsoft, Google, Salesforce o qualsiasi altro provider di identità.
- Configurare la verifica tramite telefono per l'autenticazione a più fattori.
- Leggere e scrivere dati da e verso un archivio identità di Azure AD B2C.
- Chiamare un servizio API Restful personalizzato.

![Diagramma che mostra il flusso di esecuzione dei criteri](./media/trustframeworkpolicy/custom-policy-execution.png)

 L'elemento **TrustFrameworkPolicy** contiene gli attributi seguenti:

- BasePolicy come specificato sopra
- [BuildingBlocks](buildingblocks.md)
- [ClaimsProviders](claimsproviders.md)
- [UserJourneys](userjourneys.md)
- [RelyingParty](relyingparty.md)
