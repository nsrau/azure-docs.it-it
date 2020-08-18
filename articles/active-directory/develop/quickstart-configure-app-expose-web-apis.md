---
title: "Avvio rapido: Configurare un'app per esporre un'API Web | Azure"
titleSuffix: Microsoft identity platform
description: In questo argomento di avvio rapido viene illustrato come configurare un'applicazione in modo da esporre un nuovo ambito/autorizzazione e un nuovo ruolo e renderla così disponibile per le applicazioni client.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 08/05/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 93b0c3392a32a6ff18a285d34fdaede6ceea6528
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830292"
---
# <a name="quickstart-configure-an-application-to-expose-a-web-api"></a>Avvio rapido: Configurare un'applicazione per esporre un'API Web

È possibile sviluppare un'API Web e renderla disponibile per applicazioni client esponendo [ambiti/autorizzazioni](developer-glossary.md#scopes) e [ruoli](developer-glossary.md#roles). Un'API Web correttamente configurata viene resa disponibile come le altre API Web Microsoft, tra cui l'API Graph e le API di Office 365.

In questo argomento di avvio rapido viene illustrato come configurare un'applicazione in modo da esporre un nuovo ambito e renderla così disponibile per le applicazioni client.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Completamento di [Avvio rapido: Registrare un'applicazione con Microsoft Identity Platform](quickstart-register-app.md).

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Accedere al portale di Azure e selezionare l'app

Per poter configurare l'app, seguire prima questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Se l'account consente di accedere a più tenant, selezionare l'account nell'angolo in alto a destra e impostare la sessione del portale sul tenant di Azure Active Directory desiderato.
1. Nel riquadro di spostamento sinistro selezionare il servizio **Azure Active Directory** e quindi **Registrazioni app**.
1. Trovare e selezionare l'applicazione che si vuole configurare. Dopo la selezione dell'app verrà visualizzata la pagina **Panoramica** o la pagina di registrazione principale dell'applicazione.
1. Scegliere il metodo (interfaccia utente o manifesto dell'applicazione) che si vuole usare per esporre un nuovo ambito:
    * [Esporre un nuovo ambito tramite l'interfaccia utente](#expose-a-new-scope-through-the-ui)
    * [Esporre un nuovo ambito o ruolo tramite il manifesto dell'applicazione](#expose-a-new-scope-or-role-through-the-application-manifest)

## <a name="expose-a-new-scope-through-the-ui"></a>Esporre un nuovo ambito tramite l'interfaccia utente

[![Mostra come esporre un'API con l'interfaccia utente](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-api-through-ui-expanded.png#lightbox)

Per esporre un nuovo ambito tramite l'interfaccia utente:

1. Nella pagina **Panoramica** dell'app selezionare la sezione **Esporre un'API**.

1. Selezionare **Aggiungi un ambito**.

1. Se non è stato impostato un **URI dell'ID applicazione**, verrà richiesto di immetterne uno. Immettere l'URI dell'ID applicazione o usare quello fornito e quindi selezionare **Salva e continua**.

1. Nella pagina **Aggiungi un ambito** visualizzata immettere le informazioni dell'ambito:

    | Campo | Descrizione |
    |-------|-------------|
    | **Nome ambito** | Immettere un nome significativo per l'ambito.<br><br>Ad esempio: `Employees.Read.All`. |
    | **Utenti che possono fornire il consenso** | Selezionare se il consenso per l'ambito può essere fornito dagli utenti o è necessario il consenso amministratore. Selezionare **Solo amministratori** per autorizzazioni con privilegi più elevati. |
    | **Nome visualizzato per il consenso amministratore** | Immettere una descrizione significativa per l'ambito, che verrà visualizzata agli amministratori.<br><br>Ad esempio, usare `Read-only access to Employee records` |
    | **Descrizione del consenso amministratore** | Immettere una descrizione significativa per l'ambito, che verrà visualizzata agli amministratori.<br><br>Ad esempio, usare `Allow the application to have read-only access to all Employee data.` |

    Se gli utenti possono fornire il consenso per l'ambito, aggiungere valori anche per i campi seguenti:

    | Campo | Descrizione |
    |-------|-------------|
    | **Nome visualizzato per il consenso utente** | Immettere un nome significativo per l'ambito, che verrà visualizzato agli utenti.<br><br>Ad esempio, usare `Read-only access to your Employee records` |
    | **Descrizione del consenso utente** | Immettere una descrizione significativa per l'ambito, che verrà visualizzata agli utenti.<br><br>Ad esempio, usare `Allow the application to have read-only access to your Employee data.` |

1. Impostare **Stato** e al termine selezionare **Aggiungi l'ambito**.

1. (Facoltativo) Per rimuovere la richiesta del consenso degli utenti dell'app per gli ambiti definiti, è possibile "pre-autorizzare" l'accesso dell'applicazione client all'API Web. È consigliabile pre-autorizzare *solo* le applicazioni client attendibili, perché gli utenti non hanno la possibilità di rifiutare il consenso.
    1. In **Applicazioni client autorizzate** selezionare **Aggiungi applicazione client**
    1. Immettere il valore di **ID applicazione (client)** dell'applicazione client da pre-autorizzare, ad esempio quello di un'applicazione Web già registrata.
    1. In **Ambiti autorizzati**selezionare gli ambiti per cui rimuovere la richiesta di consenso, quindi selezionare **Aggiungi applicazione**.

    L'app client è ora un'app client pre-autorizzata e agli utenti non verrà richiesto il consenso per l'accesso.

1. Seguire la procedura per [verificare che l'API Web sia esposta ad altre applicazioni](#verify-the-web-api-is-exposed-to-other-applications).

## <a name="expose-a-new-scope-or-role-through-the-application-manifest"></a>Esporre un nuovo ambito o ruolo tramite il manifesto dell'applicazione

Il manifesto dell'applicazione funge da meccanismo per l'aggiornamento dell'entità applicazione e definisce gli attributi di una registrazione di app Azure AD.

[![Esporre un nuovo ambito con la raccolta oauth2Permissions del manifesto](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png)](./media/quickstart-update-azure-ad-app-preview/expose-new-scope-through-app-manifest-expanded.png#lightbox)

Per esporre un nuovo ambito modificando il manifesto dell'applicazione:

1. Nella pagina **Panoramica** dell'app selezionare la sezione **Manifesto**. Si apre un editor di manifesto basato sul Web che consente di **modificare** il manifesto all'interno del portale. Facoltativamente è possibile selezionare **Scarica**, modificare il manifesto in locale e quindi usare **Carica** per riapplicarlo all'applicazione.

    L'esempio seguente illustra come esporre un nuovo ambito denominato `Employees.Read.All` nella risorsa/API aggiungendo l'elemento JSON seguente alla raccolta `oauth2Permissions`.

    Generare il valore di `id` a livello di codice oppure usando uno strumento per la generazione di GUID come [guidgen](https://www.microsoft.com/download/details.aspx?id=55984).

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

1. Al termine fare clic su **Salva**. Ora l'API Web è configurata in modo che possa essere usata da altre applicazioni nella directory.
1. Seguire la procedura per [verificare che l'API Web sia esposta ad altre applicazioni](#verify-the-web-api-is-exposed-to-other-applications).

Per altre informazioni sull'entità applicazione e sul relativo schema, vedere la documentazione di riferimento per il tipo di risorsa [applicazione][ms-graph-application] di Microsoft Graph.

Per altre informazioni sul manifesto dell'applicazione, incluse le informazioni di riferimento sullo schema, vedere [Informazioni sul manifesto dell'app Azure AD](reference-app-manifest.md).

## <a name="verify-the-web-api-is-exposed-to-other-applications"></a>Verificare che l'API Web sia esposta ad altre applicazioni

1. Tornare nel tenant di Azure AD, selezionare **Registrazioni app** e quindi trovare e selezionare l'applicazione client da configurare.
1. Ripetere la procedura illustrata in [Configurare un'applicazione client per l'accesso ad API Web](quickstart-configure-app-access-web-apis.md).
1. Quando si arriva al passaggio per [selezionare un'API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis), selezionare la risorsa, ovvero la registrazione dell'app per l'API Web.
    * Se la registrazione dell'app per l'API Web è stata creata con il portale di Azure, la risorsa API è riportata nella scheda **Le mie API**.
    * Se la registrazione dell'app per l'API Web è stata creata in Visual Studio durante la creazione del progetto, la risorsa API è riportata nella scheda **API usate dall'organizzazione**.

Dopo aver selezionato la risorsa API Web, il nuovo ambito sarà disponibile per le richieste di autorizzazioni dei client.

## <a name="using-the-exposed-scopes"></a>Uso degli ambiti esposti

Dopo che un client è stato configurato in modo appropriato con le autorizzazioni per accedere all'API Web, Azure AD può rilasciare al client un token di accesso OAuth 2.0. Quando il client chiama l'API Web, presenta il token di accesso che ha l'attestazione di ambito (`scp`) impostata sulle autorizzazioni richieste nella registrazione dell'applicazione.

Se necessario, è possibile esporre altri ambiti successivamente. Tenere presente che l'API Web può esporre più ambiti associati a molte funzioni diverse. La risorsa può controllare l'accesso all'API Web in fase di esecuzione, valutando le attestazioni dell'ambito (`scp`) nel token di accesso OAuth 2.0 ricevuto.

Nelle applicazioni il valore dell'ambito completo è una concatenazione di **URI ID applicazione** dell'API Web (la risorsa) e **Nome ambito**.

Se, ad esempio, l'URI ID applicazione dell'API Web è `https://contoso.com/api` e il nome dell'ambito è `Employees.Read.All`, l'ambito completo è:

`https://contoso.com/api/Employees.Read.All`

## <a name="next-steps"></a>Passaggi successivi

Ora che l'API Web è stata esposta configurandone gli ambiti, configurare la registrazione dell'app client con l'autorizzazione per accedere a tali ambiti.

> [!div class="nextstepaction"]
> [Configurare la registrazione di un'app per l'accesso all'API Web](quickstart-configure-app-access-web-apis.md)

<!-- REF LINKS -->
[ms-graph-application]: /graph/api/resources/application
