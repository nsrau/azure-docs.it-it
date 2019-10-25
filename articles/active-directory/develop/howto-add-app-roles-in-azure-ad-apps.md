---
title: Aggiungere i ruoli dell'app nell'app registrata Azure Active Directory e riceverli nel token
titleSuffix: Microsoft identity platform
description: Informazioni su come aggiungere ruoli dell'app in un'applicazione registrata in Azure Active Directory, assegnare utenti e gruppi a questi ruoli e riceverli nell'attestazione `roles` del token.
services: active-directory
documentationcenter: ''
author: kkrishna
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87660c6ef8266d3ebfbad1b7a8a7cb98b936e9c6
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803368"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>Procedura: Aggiungere ruoli dell'app in un'applicazione e riceverli nel token

Il controllo degli accessi in base al ruolo è uno dei metodi più comuni per introdurre il processo di autorizzazione nelle applicazioni. Quando si usa il controllo degli accessi in base al ruolo, l'amministratore concede le autorizzazioni ai ruoli e non a singoli utenti o gruppi. L'amministratore può quindi assegnare i ruoli a utenti e gruppi per determinare chi può accedere a specifici contenuti e funzionalità.

Usando il controllo degli accessi in base al ruolo con i ruoli applicazione e le attestazioni dei ruoli, gli sviluppatori possono applicare un sistema di autorizzazione nelle proprie app in modo semplice e sicuro.

Un altro approccio consiste nell'usare i gruppi di Azure AD e le attestazioni di gruppo, come illustrato in [WebApp-GroupClaims-DotNet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet). I gruppi di Azure AD e i ruoli applicazione non si escludono a vicenda; possono essere usati contestualmente per fornire un controllo degli accessi con granularità maggiore.

## <a name="declare-roles-for-an-application"></a>Dichiarare i ruoli per un'applicazione

Questi ruoli applicazione sono definiti nel [portale di Azure](https://portal.azure.com) all'interno del manifesto di registrazione dell'applicazione.  Quando un utente accede all'applicazione, Azure AD emette un'attestazione `roles` per ogni ruolo che viene concesso a un utente individualmente o tramite la relativa appartenenza a un gruppo.  Le assegnazioni di utenti e gruppi ai ruoli possono essere eseguite tramite l'interfaccia utente del portale o a livello di codice usando [Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/azuread-identity-access-management-concept-overview).

### <a name="declare-app-roles-using-azure-portal"></a>Dichiarare i ruoli dell'app tramite il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nella barra superiore selezionare l'account e quindi **Cambia directory**.
1. Quando si apre il riquadro **Directory e sottoscrizione**, dall'elenco **Preferiti** o **Tutte le directory**, scegliere il tenant di Active Directory in cui si vuole registrare l'applicazione.
1. Selezionare **Tutti i servizi** nella barra di spostamento a sinistra e scegliere **Azure Active Directory**.
1. Nel riquadro **Azure Active Directory** selezionare **Registrazioni app** per visualizzare un elenco di tutte le applicazioni.

     Se l'applicazione desiderata non viene visualizzata, usare i filtri disponibili nella parte superiore dell'elenco **Registrazioni app** per limitare l'elenco o scorrerlo verso il basso fino a trovare l'applicazione.

1. Selezionare l'applicazione in cui si vuole definire i ruoli dell'app.
1. Nel pannello dell'applicazione selezionare **Manifesto**.
1. Modificare il manifesto dell'app individuando l'impostazione `appRoles` e aggiungendo tutti i ruoli applicazione desiderati.

     > [!NOTE]
     > Ogni definizione di ruolo app in questo manifesto deve avere un GUID valido diverso per la proprietà `id`. 
     > 
     > La proprietà `value` di ogni definizione di ruolo app deve corrispondere esattamente alle stringhe utilizzate nel codice dell'applicazione. La proprietà `value` non può contenere spazi. In caso contrario, si riceverà un errore quando si salva il manifesto.
     
1. Salvare il manifesto.

### <a name="examples"></a>esempi

L'esempio seguente mostra gli `appRoles` che è possibile assegnare agli `users`.

> [!NOTE]
>L'`id` deve essere un GUID univoco.

```Json
"appId": "8763f1c4-f988-489c-a51e-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "User"
      ],
      "displayName": "Writer",
      "id": "d1c2ade8-98f8-45fd-aa4a-6d06b947c66f",
      "isEnabled": true,
      "description": "Writers Have the ability to create tasks.",
      "value": "Writer"
    }
  ],
"availableToOtherTenants": false,
```

> [!NOTE]
>Il `displayName` non può contenere spazi.

È possibile definire i ruoli dell'app nei confronti di `users`, `applications` o di entrambi. Se disponibili per le `applications`, i ruoli dell'app vengono visualizzati come autorizzazioni dell'applicazione nel pannello **Autorizzazioni necessarie**. L'esempio seguente mostra un ruolo dell'app destinato a un'`Application`.

```Json
"appId": "8763f1c4-f988-489c-a51e-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "Application"
      ],
      "displayName": "ConsumerApps",
      "id": "47fbb575-859a-4941-89c9-0f7a6c30beac",
      "isEnabled": true,
      "description": "Consumer apps have access to the consumer data.",
      "value": "Consumer"
    }
  ],
"availableToOtherTenants": false,
```

Il numero di ruoli definito influiscono sui limiti del manifesto dell'applicazione. Sono stati descritti in dettaglio nella pagina relativa ai [limiti dei manifesti](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest#manifest-limits) .

### <a name="assign-users-and-groups-to-roles"></a>Assegnare i ruoli a utenti e gruppi

Dopo aver aggiunto i ruoli dell'app nell'applicazione, è possibile assegnarli a utenti e gruppi.

1. Nel riquadro **Azure Active Directory** selezionare **Applicazioni aziendali** all'interno del menu di spostamento a sinistra di **Azure Active Directory**.
1. Selezionare **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

     Se l'applicazione desiderata non viene visualizzata, usare i filtri disponibili nella parte superiore dell'elenco **Tutte le applicazioni** per limitare l'elenco o scorrerlo verso il basso fino a trovare l'applicazione.

1. Selezionare l'applicazione in cui si vuole assegnare ruoli a utenti o gruppi di sicurezza.
1. Selezionare il riquadro **Utenti e gruppi** nel menu di spostamento a sinistra dell'applicazione.
1. Nella parte superiore dell'elenco **Utenti e gruppi** selezionare il pulsante **Aggiungi utente** per aprire il riquadro **Aggiungi assegnazione**.
1. Fare clic sul selettore **Utenti e gruppi** nel riquadro **Aggiungi assegnazione**.

     Verrà visualizzato un elenco di utenti e gruppi di sicurezza, oltre a una casella di testo per cercare e trovare un determinato utente o gruppo. Questa schermata consente di selezionare contemporaneamente più utenti e gruppi.

1. Dopo aver selezionato gli utenti e i gruppi, premere il pulsante **Seleziona** nella parte inferiore della pagina per passare alla parte successiva.
1. Scegliere il selettore **Seleziona ruolo** nel riquadro **Aggiungi assegnazione**. Verranno visualizzati tutti i ruoli dichiarati in precedenza nel manifesto dell'app.
1. Scegliere un ruolo e premere il pulsante **Seleziona**.
1. Premere il pulsante **Assegna** nella parte inferiore della pagina per completare le assegnazioni di utenti e gruppi all'app.
1. Verificare che gli utenti e i gruppi aggiunti siano presenti nell'elenco **Utenti e gruppi** aggiornato.

## <a name="more-information"></a>Altre informazioni

- [Authorization in a web app using Azure AD application roles &amp; role claims (Sample)](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) (Autorizzazione in un'app Web tramite le attestazioni di ruolo e i ruoli applicazione di Azure AD - Esempio)
- [Using Security Groups and Application Roles in your apps (Video)](https://www.youtube.com/watch?v=V8VUPixLSiM) (Utilizzo dei gruppi di sicurezza e dei ruoli applicazione nelle app - Video)
- [Azure Active Directory, now with Group Claims and Application Roles](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles) (Azure Active Directory, ora con attestazioni di gruppo e ruoli applicazione)
- [Manifesto dell'app Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)
- [Token di accesso di AAD](access-tokens.md)
- [`id_tokens` di AAD](id-tokens.md)
