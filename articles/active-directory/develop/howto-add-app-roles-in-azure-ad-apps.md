---
title: Aggiungere ruoli dell'app e riceverli da un token | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come aggiungere i ruoli dell'app a un'applicazione registrata in Azure Active Directory, assegnare utenti e gruppi a questi ruoli e riceverli nell'attestazione "Roles" nel token.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 11/13/2020
ms.author: kkrishna
ms.reviewer: marsma, kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 96c52c46a75d6d5810dfddf91439c275d14e85f1
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94616138"
---
# <a name="how-to-add-app-roles-to-your-application-and-receive-them-in-the-token"></a>Procedura: aggiungere i ruoli dell'app all'applicazione e riceverli nel token

Il controllo degli accessi in base al ruolo è uno dei metodi più comuni per introdurre il processo di autorizzazione nelle applicazioni. Quando si usa il controllo degli accessi in base al ruolo, l'amministratore concede le autorizzazioni ai ruoli e non a singoli utenti o gruppi. L'amministratore può quindi assegnare i ruoli a utenti e gruppi per determinare chi può accedere a specifici contenuti e funzionalità.

Usando il controllo degli accessi in base al ruolo con i ruoli applicazione e le attestazioni di ruolo, gli sviluppatori possono applicare in modo sicuro l'autorizzazione nelle app

Un altro approccio consiste nell'usare gruppi di Azure AD e attestazioni di gruppo, come illustrato nell'esempio di codice [Active-Directory-aspnetcore-webapp-openidconnect-V2](https://aka.ms/groupssample) su GitHub. I gruppi di Azure AD e i ruoli applicazione non si escludono a vicenda. possono essere usati in tandem per fornire un controllo di accesso con granularità fine.

## <a name="declare-roles-for-an-application"></a>Dichiarare i ruoli per un'applicazione

Per definire i ruoli dell'app, è possibile usare la [portale di Azure](https://portal.azure.com). Quando un utente accede all'applicazione, Azure AD emette un' `roles` attestazione per ogni ruolo che l'utente ha concesso singolarmente all'utente e dall'appartenenza al gruppo.

Esistono due modi per dichiarare i ruoli dell'app usando il portale di Azure:

* [Interfaccia utente dei ruoli dell'app](#app-roles-ui--preview) | Anteprima
* [Editor manifesto dell'app](#app-manifest-editor)

Il numero di ruoli aggiunti ai limiti del manifesto dell'applicazione applicati da Azure Active Directory. Per informazioni su questi limiti, vedere la sezione  [limiti del manifesto](./reference-app-manifest.md#manifest-limits) di [Azure Active Directory riferimento al manifesto dell'applicazione](reference-app-manifest.md).

### <a name="app-roles-ui--preview"></a>Interfaccia utente dei ruoli dell'app | Anteprima

> [!IMPORTANT]
> Funzionalità dell'interfaccia utente del portale dei ruoli dell'app [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

Per creare un ruolo app usando l'interfaccia utente di portale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **directory + sottoscrizione** nel menu in alto e quindi scegliere il tenant Azure Active Directory che contiene la registrazione dell'app a cui si vuole aggiungere un ruolo app.
1. Cercare e selezionare **Azure Active Directory**.
1. In **Gestisci** selezionare **registrazioni app** e quindi selezionare l'applicazione in cui si desidera definire i ruoli dell'app.
1. Selezionare i **ruoli dell'app | Anteprima** e quindi selezionare **Crea ruolo app**.

   :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-overview-pane.png" alt-text="Riquadro dei ruoli dell'app per la registrazione di un'app nel portale di Azure":::
1. Nel riquadro **Crea ruolo app** immettere le impostazioni per il ruolo. La tabella che segue l'immagine descrive ogni impostazione e i relativi parametri.

    :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-create-context-pane.png" alt-text="Il riquadro del contesto di creazione dei ruoli dell'app per la registrazione dell'app nel portale di Azure":::

    | Campo | Descrizione | Esempio |
    |-------|-------------|---------|
    | **Nome visualizzato** | Nome visualizzato per il ruolo dell'app che viene visualizzato nelle esperienze di consenso dell'amministratore e di assegnazione di app. Questo valore può contenere spazi. | `Survey Writer` |
    | **Tipi di membri consentiti** | Specifica se il ruolo app può essere assegnato a utenti, applicazioni o entrambi.<br/><br/>Se disponibile per `applications` , i ruoli applicazione vengono visualizzati come autorizzazioni dell'applicazione nella sezione **Gestisci** di una registrazione dell'app > **le autorizzazioni API > aggiungere un'autorizzazione > API personali > scegliere un'API > le autorizzazioni dell'applicazione**. | `Users/Groups` |
    | **Valore** | Specifica il valore dell'attestazione dei ruoli che l'applicazione deve aspettarsi nel token. Il valore deve corrispondere esattamente alla stringa a cui si fa riferimento nel codice dell'applicazione. Il valore non può contenere spazi. | `Survey.Create` |
    | **Descrizione** | Descrizione più dettagliata del ruolo dell'app visualizzato durante l'assegnazione dell'app di amministrazione e l'esperienza di consenso. | `Writers can create surveys.` |
    | **Abilitare questo ruolo app?** | Specifica se il ruolo app è abilitato. Per eliminare un ruolo app, deselezionare questa casella di controllo e applicare la modifica prima di provare a eseguire l'operazione di eliminazione. | *Selezionata* |

1. Fare clic su **Applica** per salvare le modifiche.

### <a name="app-manifest-editor"></a>Editor manifesto dell'app

Per aggiungere ruoli modificando direttamente il manifesto:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **directory + sottoscrizione** nel menu in alto e quindi scegliere il tenant Azure Active Directory che contiene la registrazione dell'app a cui si vuole aggiungere un ruolo app.
1. Cercare e selezionare **Azure Active Directory**.
1. In **Gestisci** selezionare **registrazioni app** e quindi selezionare l'applicazione in cui si desidera definire i ruoli dell'app.
1. In **Gestisci** selezionare **manifesto**.
1. Modificare il manifesto dell'applicazione individuando l' `appRoles` impostazione e aggiungendo i ruoli dell'applicazione. È possibile definire i ruoli dell'app destinati a `users` , `applications` o a entrambi. I frammenti di codice JSON seguenti mostrano esempi di entrambi.
1. Salvare il manifesto.

Ogni definizione di ruolo app nel manifesto deve avere un GUID univoco per il relativo `id` valore.

La proprietà `value` di ogni definizione del ruolo dell'app deve corrispondere esattamente alle stringhe usate nel codice dell'applicazione. La proprietà `value` non può contenere spazi. In caso contrario, si riceverà un errore quando si salva il manifesto.

#### <a name="example-user-app-role"></a>Esempio: ruolo app utente

Questo esempio definisce un ruolo app denominato `Writer` che è possibile assegnare a `User` :

```json
"appId": "8763f1c4-0000-0000-0000-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "User"
      ],
      "displayName": "Writer",
      "id": "d1c2ade8-0000-0000-0000-6d06b947c66f",
      "isEnabled": true,
      "description": "Writers Have the ability to create tasks.",
      "value": "Writer"
    }
  ],
"availableToOtherTenants": false,
```

#### <a name="example-application-app-role"></a>Esempio: ruolo app applicazione

Se disponibile per `applications` , i ruoli applicazione vengono visualizzati come autorizzazioni dell'applicazione nella sezione **Gestisci** di una registrazione dell'app > **le autorizzazioni API > aggiungere un'autorizzazione > API personali > scegliere un'API > le autorizzazioni dell'applicazione**.

Questo esempio mostra un ruolo app destinato a `Application` :

```json
"appId": "8763f1c4-0000-0000-0000-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "Application"
      ],
      "displayName": "ConsumerApps",
      "id": "47fbb575-0000-0000-0000-0f7a6c30beac",
      "isEnabled": true,
      "description": "Consumer apps have access to the consumer data.",
      "value": "Consumer"
    }
  ],
"availableToOtherTenants": false,
```

## <a name="assign-users-and-groups-to-roles"></a>Assegnare i ruoli a utenti e gruppi

Una volta aggiunti i ruoli dell'app nell'applicazione, è possibile assegnare utenti e gruppi ai ruoli. Le assegnazioni di utenti e gruppi ai ruoli possono essere eseguite tramite l'interfaccia utente del portale o a livello di codice usando [Microsoft Graph](/graph/api/user-post-approleassignments). Quando gli utenti assegnati ai vari ruoli dell'app possono accedere all'applicazione, i relativi token avranno i rispettivi ruoli assegnati nell' `roles` attestazione.

Per assegnare utenti e gruppi ai ruoli utilizzando la portale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. In **Azure Active Directory** selezionare **applicazioni aziendali** nel menu di spostamento a sinistra.
1. Selezionare **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni. Se l'applicazione non viene visualizzata nell'elenco, usare i filtri nella parte superiore dell'elenco **tutte le applicazioni** per limitare l'elenco o scorrere verso il basso nell'elenco per individuare l'applicazione.
1. Selezionare l'applicazione in cui si vuole assegnare ruoli a utenti o gruppi di sicurezza.
1. In **Gestisci** selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** per aprire il riquadro **Aggiungi assegnazione** .
1. Fare clic sul selettore **Utenti e gruppi** nel riquadro **Aggiungi assegnazione**. Viene visualizzato un elenco di utenti e gruppi di sicurezza. È possibile cercare un determinato utente o gruppo, nonché selezionare più utenti e gruppi visualizzati nell'elenco.
1. Dopo aver selezionato utenti e gruppi, selezionare il pulsante **Seleziona** per continuare.
1. Selezionare **selezionare un ruolo** nel riquadro **Aggiungi assegnazione** . Vengono visualizzati tutti i ruoli definiti per l'applicazione.
1. Scegliere un ruolo e selezionare il pulsante **Seleziona** .
1. Selezionare il pulsante **assign (assegna** ) per completare l'assegnazione di utenti e gruppi all'app.

Verificare che gli utenti e i gruppi aggiunti siano visualizzati nell'elenco **utenti e gruppi** .

## <a name="assign-app-roles-to-applications"></a>Assegnare i ruoli dell'app alle applicazioni

Una volta aggiunti i ruoli dell'app nell'applicazione, è possibile assegnare un ruolo app a un'app client usando il portale di Azure o a livello di codice usando [Microsoft Graph](/graph/api/user-post-approleassignments).

Quando si assegnano i ruoli dell'app a un'applicazione, si creano le *autorizzazioni dell'applicazione*. Le autorizzazioni dell'applicazione vengono in genere usate dalle app daemon o dai servizi back-end che devono eseguire l'autenticazione ed effettuare chiamate API autorizzate, senza l'interazione di un utente.

Per assegnare i ruoli dell'app a un'applicazione usando il portale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. In **Azure Active Directory** selezionare **registrazioni app** nel menu di spostamento a sinistra.
1. Selezionare **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni. Se l'applicazione non viene visualizzata nell'elenco, usare i filtri nella parte superiore dell'elenco **tutte le applicazioni** per limitare l'elenco o scorrere verso il basso nell'elenco per individuare l'applicazione.
1. Selezionare l'applicazione a cui si vuole assegnare un ruolo app.
1. Selezionare **Autorizzazioni API** > **Aggiungi un'autorizzazione**.
1. Selezionare la scheda **API personali** e quindi selezionare l'app per cui sono stati definiti i ruoli dell'app.
1. Selezionare **Autorizzazioni applicazione**.
1. Selezionare il ruolo o i ruoli che si desidera assegnare.
1. Selezionare il pulsante **Aggiungi autorizzazioni** per completare l'aggiunta dei ruoli.

I nuovi ruoli aggiunti verranno visualizzati nel riquadro **autorizzazioni API** della registrazione dell'app.

#### <a name="grant-admin-consent"></a>Concedi il consenso dell'amministratore

Poiché si tratta di autorizzazioni per le *applicazioni* , non di autorizzazioni delegate, un amministratore deve concedere il consenso per l'uso dei ruoli dell'app assegnati all'applicazione.

1. Nel riquadro **autorizzazioni API** registrazione app selezionare **concedi il consenso \<tenant name\> dell'amministratore per**.
1. Selezionare **Sì** quando viene richiesto di concedere il consenso per le autorizzazioni richieste.

La colonna **stato** deve indicare che è stato concesso il consenso **per \<tenant name\>**.

## <a name="use-app-roles-in-your-web-api"></a>Usare i ruoli dell'app nell'API Web

Dopo aver definito i ruoli dell'app e averli assegnati a un utente, un gruppo o un'applicazione, il passaggio successivo consiste nell'aggiungere codice all'API Web che verifica la presenza di tali ruoli quando viene chiamata l'API. Ovvero, quando un'app client richiede un'operazione API che è stata decisa richiede l'autorizzazione, il codice dell'API deve verificare che gli ambiti siano inclusi nel token di accesso presentato nella chiamata dell'app client.

Per informazioni su come aggiungere l'autorizzazione all'API Web, vedere [API Web protetta: verifica degli ambiti e dei ruoli dell'app](scenario-protected-web-api-verification-scope-app-roles.md).

## <a name="app-roles-vs-groups"></a>Confronto tra i ruoli dell'app e i gruppi

Sebbene sia possibile usare i ruoli o i gruppi di app per l'autorizzazione, le differenze principali tra di essi possono influenzare la scelta di usare per lo scenario.

| Ruoli dell'app                                                                          | Gruppi                                                      |
|------------------------------------------------------------------------------------|-------------------------------------------------------------|
| Sono specifici di un'applicazione e sono definiti nella registrazione dell'app. Si spostano con l'applicazione. | Non sono specifiche di un'app, ma a un tenant Azure AD. |
| I ruoli dell'app vengono rimossi quando viene rimossa la registrazione dell'app.                      | I gruppi rimangono intatti anche se l'app viene rimossa.            |
| Fornito nell' `roles` attestazione.                                                     | Fornito nell' `groups` attestazione.                                 |

Gli sviluppatori possono usare i ruoli dell'app per controllare se un utente può accedere a un'app o un'app può ottenere un token di accesso per un'API Web. Per estendere questo controllo di sicurezza ai gruppi, gli sviluppatori e gli amministratori possono anche assegnare gruppi di sicurezza ai ruoli dell'app.

I ruoli dell'app sono preferiti dagli sviluppatori quando vogliono descrivere e controllare i parametri di autorizzazione nell'app. Ad esempio, un'app che usa i gruppi per l'autorizzazione si interrompe nel tenant successivo, in quanto l'ID e il nome del gruppo potrebbero essere diversi. Un'app che usa i ruoli dell'app rimane sicura. Infatti, l'assegnazione di gruppi ai ruoli dell'app è molto diffusa con le app SaaS per gli stessi motivi.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui ruoli dell'app con le risorse seguenti.

* Esempi di codice in GitHub
  * [Aggiungere l'autorizzazione usando gruppi e attestazioni di gruppo a un'app Web ASP.NET Core](https://aka.ms/groupssample)
  * [Applicazione a singola pagina angolare (SPA) che chiama un'API Web .NET Core e usa i ruoli e i gruppi di sicurezza dell'app](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups/blob/master/README.md)
* Documentazione di riferimento
  * [Manifesto dell'app Azure AD](./reference-app-manifest.md)
  * [Token di accesso Azure AD](access-tokens.md)
  * [Token ID Azure AD](id-tokens.md)
  * [Fornire attestazioni facoltative all'app](active-directory-optional-claims.md)
* Video: [implementare l'autorizzazione nelle applicazioni con la piattaforma di identità Microsoft](https://www.youtube.com/watch?v=LRoc-na27l0) (1:01:15)
