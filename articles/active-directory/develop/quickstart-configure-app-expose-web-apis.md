---
title: Configurare un'applicazione per l'esposizione di API Web (anteprima) | Azure
description: Informazioni su come configurare un'applicazione in modo da esporre un nuovo ambito/autorizzazione e un nuovo ruolo e renderla così disponibile per applicazioni client.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/25/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: 3f38162fd4d95127ddf7797638bc9af21e6aaf20
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2019
ms.locfileid: "55095695"
---
# <a name="quickstart-configure-an-application-to-expose-web-apis-preview"></a>Guida introduttiva: Configurare un'applicazione per l'esposizione di API Web (anteprima)

È possibile sviluppare un'API Web e renderla disponibile per applicazioni client esponendo [ambiti/autorizzazioni](developer-glossary.md#scopes) e [ruoli](developer-glossary.md#roles). Un'API Web correttamente configurata viene resa disponibile come le altre API Web Microsoft, tra cui l'API Graph e le API di Office 365.

In questa guida introduttiva verrà illustrato come configurare un'applicazione in modo da esporre un nuovo ambito e renderla così disponibile per applicazioni client.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, assicurarsi di soddisfare questi prerequisiti:

* Acquisire informazioni su [autorizzazioni supportate e consenso](v2-permissions-and-consent.md), la cui conoscenza è importante per creare applicazioni che dovranno essere usate da altri utenti o applicazioni.
* Avere un tenant in cui sono registrate applicazioni.
  * Se non si hanno app registrate, vedere come [registrare applicazioni con Microsoft Identity Platform](quickstart-register-app.md).
* Acconsentire esplicitamente all'esperienza di anteprima per le registrazioni di app nel portale di Azure. Le procedure illustrate in questa guida introduttiva corrispondono alla nuova interfaccia utente e funzionano solo se si è acconsentito esplicitamente all'esperienza di anteprima.

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Accedere al portale di Azure e selezionare l'app

Per poter configurare l'app, seguire prima questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Se l'account consente di accedere a più tenant, selezionare l'account nell'angolo in alto a destra e impostare la sessione del portale sul tenant di Azure AD desiderato.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory** e quindi **Registrazioni app (anteprima)**.
1. Trovare e selezionare l'applicazione che si vuole configurare. Dopo la selezione dell'app verrà visualizzata la pagina **Panoramica** o la pagina di registrazione principale dell'applicazione.
1. Scegliere il metodo (interfaccia utente o manifesto dell'applicazione) che si vuole usare per esporre un nuovo ambito:
    * [Esporre un nuovo ambito tramite l'interfaccia utente](#expose-a-new-scope-through-the-ui)
    * [Esporre un nuovo ambito o ruolo tramite il manifesto dell'applicazione](#expose-a-new-scope-or-role-through-the-application-manifest)

## <a name="expose-a-new-scope-through-the-ui"></a>Esporre un nuovo ambito tramite l'interfaccia utente

[![Esporre un'API con l'interfaccia utente](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png#lightbox)

Per esporre un nuovo ambito tramite l'interfaccia utente:

1. Nella pagina **Panoramica** dell'app selezionare la sezione **Esporre un'API**.

1. Selezionare **Aggiungi un ambito**.

1. Se non è stato impostato un **URI dell'ID applicazione**, verrà richiesto di immetterne uno. Immettere l'URI dell'ID applicazione o usare quello fornito e quindi selezionare **Salva e continua**.

1. Nella pagina **Aggiungi un ambito** visualizzata immettere le informazioni dell'ambito:

    | Campo | DESCRIZIONE |
    |-------|-------------|
    | **Nome ambito** | Immettere un nome significativo per l'ambito.<br><br>Ad esempio: `Employees.Read.All`. |
    | **Utenti che possono fornire il consenso** | Selezionare se il consenso per l'ambito può essere fornito dagli utenti o è necessario il consenso amministratore. Selezionare **Solo amministratori** per autorizzazioni con privilegi più elevati. |
    | **Nome visualizzato per il consenso amministratore** | Immettere una descrizione significativa per l'ambito, che verrà visualizzata agli amministratori.<br><br>Ad esempio: `Read-only access to Employee records` |
    | **Descrizione del consenso amministratore** | Immettere una descrizione significativa per l'ambito, che verrà visualizzata agli amministratori.<br><br>Ad esempio: `Allow the application to have read-only access to all Employee data.` |

    Se gli utenti possono fornire il consenso per l'ambito, aggiungere valori anche per i campi seguenti:

    | Campo | DESCRIZIONE |
    |-------|-------------|
    | **Nome visualizzato per il consenso utente** | Immettere un nome significativo per l'ambito, che verrà visualizzato agli utenti.<br><br>Ad esempio: `Read-only access to your Employee records` |
    | **Descrizione del consenso utente** | Immettere una descrizione significativa per l'ambito, che verrà visualizzata agli utenti.<br><br>Ad esempio: `Allow the application to have read-only access to your Employee data.` |

1. Impostare **Stato** e al termine selezionare **Aggiungi l'ambito**.

1. Seguire la procedura per [verificare che l'API Web sia esposta ad altre applicazioni](#verify-the-web-api-is-exposed-to-other-applications).

## <a name="expose-a-new-scope-or-role-through-the-application-manifest"></a>Esporre un nuovo ambito o ruolo tramite il manifesto dell'applicazione

[![Esporre un nuovo ambito con la raccolta oauth2Permissions del manifesto](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png#lightbox)

Per esporre un nuovo ambito tramite il manifesto dell'applicazione:

1. Nella pagina **Panoramica** dell'app selezionare la sezione **Manifesto**. Si apre un editor di manifesto basato sul Web che consente di **modificare** il manifesto all'interno del portale. Facoltativamente è possibile selezionare **Scarica**, modificare il manifesto in locale e quindi usare **Carica** per riapplicarlo all'applicazione.
    
    L'esempio seguente illustra come esporre un nuovo ambito denominato `Employees.Read.All` nella risorsa/API aggiungendo l'elemento JSON seguente alla raccolta `oauth2Permissions`.

      ```json
      {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
      }
      ```

  > [!NOTE]
  > Il valore di `id` deve essere generato a livello di codice oppure usando uno strumento per la generazione di GUID come [guidgen](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx). `id` rappresenta un identificatore univoco per l'ambito esposto dall'API Web. Dopo che un client è stato configurato in modo appropriato con le autorizzazioni per accedere all'API Web, Azure AD rilascia al client un token di accesso OAuth 2.0. Quando il client chiama l'API Web, presenta il token di accesso che ha l'attestazione di ambito (scp) impostata sulle autorizzazioni richieste nella registrazione della relativa applicazione.
  >
  > Se necessario, è possibile esporre altri ambiti successivamente. Tenere presente che l'API Web può esporre più ambiti associati a molte funzioni diverse. La risorsa può controllare l'accesso all'API Web in fase di esecuzione, valutando le attestazioni dell'ambito (`scp`) nel token di accesso OAuth 2.0 ricevuto.

1. Al termine fare clic su **Salva**. Ora l'API Web è configurata in modo che possa essere usata da altre applicazioni nella directory.
1. Seguire la procedura per [verificare che l'API Web sia esposta ad altre applicazioni](#verify-the-web-api-is-exposed-to-other-applications).

## <a name="verify-the-web-api-is-exposed-to-other-applications"></a>Verificare che l'API Web sia esposta ad altre applicazioni

1. Tornare al tenant di Azure AD, selezionare nuovamente **Registrazioni app** e quindi trovare e selezionare l'applicazione client che si vuole configurare.
1. Ripetere la procedura illustrata in [Configurare un'applicazione client per l'accesso ad API Web](#configure-a-client-application-to-access-web-apis).
1. Quando si arriva al passaggio per **selezionare un'API**, selezionare la risorsa. Dovrebbe essere visualizzato il nuovo ambito, disponibile per le richieste di autorizzazioni dei client.

## <a name="more-on-the-application-manifest"></a>Altre informazioni sul manifesto dell'applicazione

Il manifesto dell'applicazione funge da meccanismo per l'aggiornamento dell'entità applicazione e definisce tutti gli attributi della configurazione dell'identità di un'applicazione Azure AD. Per altre informazioni sull'entità applicazione e il relativo schema, vedere la [documentazione sull'entità applicazione dell'API Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity), che include informazioni di riferimento complete sui membri dell'entità applicazione utilizzati per specificare le autorizzazioni per l'API, fra cui:  

* Il membro appRoles, che è una raccolta di entità [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type) utilizzate per definire le [Autorizzazioni applicazione](developer-glossary.md#permissions) per un'API Web.
* Il membro oauth2Permissions, che è una raccolta di entità [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type) utilizzate per definire le [Autorizzazioni delegate](developer-glossary.md#permissions) per un'API Web.

Per altre informazioni sui concetti generali relativi al manifesto dell'applicazione, vedere [Informazioni sul manifesto dell'applicazione in Azure Active Directory](reference-app-manifest.md).

## <a name="next-steps"></a>Passaggi successivi

Vedere le altre guide introduttive correlate sulla gestione delle app:

* [Registrare un'applicazione con Microsoft Identity Platform](quickstart-register-app.md)
* [Configurare un'applicazione client per l'accesso ad API Web](quickstart-configure-app-access-web-apis.md)
* [Modificare gli account supportati da un'applicazione](quickstart-modify-supported-accounts.md)
* [Rimuovere un'applicazione registrata con Microsoft Identity Platform](quickstart-remove-app.md)

Per altre informazioni su due oggetti di Azure AD che rappresentano un'applicazione registrata e la relazione tra essi, vedere [Oggetti applicazione e oggetti entità servizio](app-objects-and-service-principals.md).

Per altre informazioni sulle linee guida sulla personalizzazione da seguire per lo sviluppo di applicazioni con Azure Active Directory, vedere [Linee guida sulla personalizzazione delle applicazioni](howto-add-branding-in-azure-ad-apps.md).
