---
title: "Avvio rapido: Configurare un'app per l'accesso a un'API Web | Azure"
titleSuffix: Microsoft identity platform
description: Questo argomento di avvio rapido illustra come configurare una registrazione di app che rappresenta un'API Web in Microsoft Identity Platform per consentire alle applicazioni client di accedere alle risorse con ambito tramite autorizzazioni.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, contperfq1
ms.reviewer: lenalepa, aragra, sureshja
ms.openlocfilehash: fc2f3202ac88e3ee6c24db21dd9072a13a8deef9
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89442285"
---
# <a name="quickstart-configure-a-client-application-to-access-a-web-api"></a>Avvio rapido: Configurare un'applicazione client per l'accesso a un'API Web

Questo argomento di avvio rapido illustra come fornire a un'app client registrata con Microsoft Identity Platform l'accesso con ambito basato su autorizzazioni all'API Web personalizzata. Viene spiegato anche come fornire l'accesso all'app client a Microsoft Graph.

Specificando gli ambiti di un'API Web nella registrazione dell'app client, l'app client può ottenere da Microsoft Identity Platform un token di accesso che contiene gli ambiti. All'interno del codice, l'API Web può quindi fornire accesso basato su autorizzazioni alle relative risorse in base agli ambiti trovati nel token di accesso.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva ([creare un account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))
* Completamento di [Avvio rapido: Registrare un'applicazione](quickstart-register-app.md)
* Completamento di [Avvio rapido: Configurare un'applicazione per esporre un'API Web](quickstart-configure-app-expose-web-apis.md)

## <a name="add-permissions-to-access-your-web-api"></a>Aggiungere autorizzazioni per accedere all'API Web

Nel primo scenario si concede a un'app client l'accesso all'API Web personalizzata. Entrambi devono essere registrate come previsto nei prerequisiti. Se non si hanno ancora un'app client e un'API Web, completare la procedura descritta nei due articoli inclusi nei [prerequisiti](#prerequisites).

Questo diagramma mostra la correlazione tra le due registrazioni dell'app. In questa sezione vengono aggiunte le autorizzazioni per la registrazione dell'app client.

:::image type="content" source="media/quickstart-configure-app-access-web-apis/diagram-01-app-permission-to-api-scopes.svg" alt-text="Diagramma a linee che mostra un'API Web con ambiti esposti a destra e un'app client a sinistra con gli ambiti selezionati come autorizzazioni" border="false":::

Dopo aver registrato sia l'app client che l'API Web e aver esposto l'API creando gli ambiti, seguire questa procedura per configurare le autorizzazioni del client per l'API:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Se si accede a più tenant, usare il filtro **Directory e sottoscrizione** :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false"::: nel menu in alto e selezionare il tenant che contiene la registrazione dell'app client.
1. Selezionare **Azure Active Directory** > **Registrazioni app** e quindi selezionare l'applicazione client (*non* l'API Web).
1. Selezionare **Autorizzazioni API** > **Aggiungi un'autorizzazione** > **Le mie API**.
1. Selezionare l'API Web registrata come parte dei prerequisiti.

    L'opzione **Autorizzazioni delegate** è selezionata per impostazione predefinita. Le autorizzazioni delegate sono appropriate per le app client che accedono a un'API Web come utente connesso e il cui accesso deve essere limitato alle autorizzazioni selezionate nel passaggio successivo. Per questo esempio lasciare selezionata l'opzione **Autorizzazioni delegate**.

    Usare **Autorizzazioni applicazione** per applicazioni di tipo servizio o daemon che devono accedere a un'API Web automaticamente, senza richiedere l'intervento dell'utente per l'accesso o il consenso. A meno che non siano stati definiti ruoli applicazione per l'API Web, questa opzione è disabilitata.
1. In **Selezionare le autorizzazioni** espandere la risorsa con gli ambiti definiti per l'API Web e selezionare le autorizzazioni che l'app client deve avere per conto dell'utente che ha eseguito l'accesso.

    Se sono stati usati i nomi di ambito di esempio specificati nell'argomento di avvio rapido precedente, i valori visualizzati dovrebbero essere **Employees.Read.All** e **Employees.Write.All**.
    Selezionare **Employees.Read.All** o un'altra autorizzazione eventualmente creata durante il completamento dei prerequisiti.
1. Selezionare **Aggiungi autorizzazioni** per completare il processo.

Dopo aver aggiunto le autorizzazioni per l'API, le autorizzazioni selezionate dovrebbero essere visualizzate in **Autorizzazioni configurate**. L'immagine seguente illustra l'autorizzazione delegata di esempio *Employees.Read.All* aggiunta alla registrazione dell'app client.

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-02-configured-permissions-pane.png" alt-text="Riquadro Autorizzazioni configurate nel portale di Azure che mostra l'autorizzazione appena aggiunta":::

Notare anche l'autorizzazione *User.Read* per l'API Microsoft Graph. Questa autorizzazione viene aggiunta automaticamente quando si registra un'app nel portale di Azure.

## <a name="add-permissions-to-access-microsoft-graph"></a>Aggiungere autorizzazioni per accedere a Microsoft Graph

Oltre ad accedere all'API Web personalizzata per conto dell'utente connesso, l'applicazione potrebbe anche dover accedere o modificare i dati dell'utente (o di altro tipo) archiviati in Microsoft Graph. Potrebbe anche essere presente un'app di servizio o daemon che deve accedere a Microsoft Graph automaticamente, eseguendo operazioni senza alcuna interazione utente.

### <a name="delegated-permission-to-microsoft-graph"></a>Autorizzazione delegata per Microsoft Graph

Configurare l'autorizzazione delegata per Microsoft Graph per consentire all'applicazione client di eseguire operazioni per conto dell'utente connesso, ad esempio la lettura della posta elettronica o la modifica del profilo. Per impostazione predefinita, quando eseguono l'accesso, agli utenti dell'app client viene richiesto di fornire il consenso alle autorizzazioni delegate configurate.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Se si accede a più tenant, usare il filtro **Directory e sottoscrizione** :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false"::: nel menu in alto e selezionare il tenant che contiene la registrazione dell'app client.
1. Selezionare **Azure Active Directory** > **Registrazioni app** e quindi selezionare l'applicazione client.
1. Selezionare **Autorizzazioni API** > **Aggiungi un'autorizzazione** > **Microsoft Graph**.
1. Selezionare **Autorizzazioni delegate**. Microsoft Graph espone numerose autorizzazioni. Quella usata più di frequente è visualizzata all'inizio dell'elenco.
1. In **Selezionare le autorizzazioni** selezionare le autorizzazioni seguenti:

    | Autorizzazione       | Descrizione                                         |
    |------------------|-----------------------------------------------------|
    | `email`          | Visualizzare l'indirizzo di posta elettronica degli utenti                           |
    | `offline_access` | Conservazione dell'accesso ai dati per cui è stato autorizzato l'accesso |
    | `openid`         | Concedere l'accesso agli utenti                                       |
    | `profile`        | Visualizzare il profilo di base degli utenti                           |
1. Selezionare **Aggiungi autorizzazioni** per completare il processo.

Quando si configurano le autorizzazioni, agli utenti dell'app che eseguono l'accesso viene richiesto di fornire il consenso per consentire all'app di accedere all'API di risorse per conto dell'utente.

Gli amministratori possono anche concedere il consenso per conto di *tutti* gli utenti in modo che non venga visualizzata alcuna richiesta di consenso. Il consenso amministratore è illustrato più avanti nella sezione [Altre informazioni sulle autorizzazioni per le API e sul consenso amministratore](#more-on-api-permissions-and-admin-consent) di questo articolo.

### <a name="application-permission-to-microsoft-graph"></a>Autorizzazione dell'applicazione per Microsoft Graph

Configurare le autorizzazioni dell'applicazione per un'applicazione che deve eseguire l'autenticazione automaticamente senza alcuna interazione o consenso dell'utente. Le autorizzazioni dell'applicazione vengono in genere usate da servizi in background o da app daemon che accedono a un'API in modalità "headless" e da API Web che accedono a un'altra API (downstream).

Nella procedura seguente viene concessa, ad esempio, l'autorizzazione *Files.Read.All* di Microsoft Graph.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Se si accede a più tenant, usare il filtro **Directory e sottoscrizione** :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false"::: nel menu in alto e selezionare il tenant che contiene la registrazione dell'app client.
1. Selezionare **Azure Active Directory** > **Registrazioni app** e quindi selezionare l'applicazione client.
1. Selezionare **Autorizzazioni API** > **Aggiungi un'autorizzazione** > **Microsoft Graph** > **Autorizzazioni applicazione**.
1. Tutte le autorizzazioni esposte da Microsoft Graph sono visualizzate in **Selezionare le autorizzazioni**.
1. Selezionare l'autorizzazione o le autorizzazioni da concedere all'applicazione. È ad esempio possibile che sia presente un'app daemon che analizza i file dell'organizzazione, inviando avvisi relativi a un nome o un tipo di file specifico.

    In **Selezionare le autorizzazioni** espandere **File** e quindi selezionare l'autorizzazione *Files.Read.All*.
1. Selezionare **Aggiungi autorizzazioni**.

Per alcune autorizzazioni, come quella *Files.Read.All* di Microsoft Graph, è richiesto il consenso amministratore. Per concedere il consenso dell'amministratore, selezionare il pulsante **Fornisci il consenso amministratore**, illustrato più avanti nella sezione [Pulsante Consenso amministratore](#admin-consent-button).

### <a name="configure-client-credentials"></a>Configurare le credenziali del client

Le app che usano le autorizzazioni dell'applicazione eseguono l'autenticazione con le proprie credenziali, senza richiedere alcuna interazione utente. Prima che l'applicazione (o l'API) possa accedere a Microsoft Graph, a un'API Web o a un'altra API usando le autorizzazioni dell'applicazione, è necessario configurare le credenziali dell'app client.

Per altre informazioni sulla configurazione delle credenziali di un'app, vedere la sezione [Aggiungere le credenziali](quickstart-register-app.md#add-credentials) di [Avvio rapido: Registrare un'applicazione con Microsoft Identity Platform](quickstart-register-app.md).

## <a name="more-on-api-permissions-and-admin-consent"></a>Altre informazioni sulle autorizzazioni per le API e sul consenso amministratore

Il riquadro **Autorizzazioni API** di una registrazione dell'app contiene una tabella [Autorizzazioni configurate](#configured-permissions) e potrebbe includere anche una tabella [Altre autorizzazioni concesse](#other-permissions-granted). Entrambe le tabelle e il [pulsante Consenso amministratore](#admin-consent-button) sono descritti nelle sezioni seguenti.

### <a name="configured-permissions"></a>Autorizzazioni configurate

La tabella **Autorizzazioni configurate** nel riquadro **Autorizzazioni API** mostra l'elenco delle autorizzazioni richieste dall'applicazione per le operazioni di base, ovvero l'elenco di *accesso alle risorse necessarie*. Gli utenti o i relativi amministratori dovranno fornire il consenso per queste autorizzazioni prima di usare l'app. In un secondo momento in fase di esecuzione è possibile richiedere altre autorizzazioni facoltative (usando il consenso dinamico).

Questo è l'elenco minimo di autorizzazioni per cui gli utenti dovranno fornire il consenso per usare l'app. Le autorizzazioni potrebbero essere più numerose, ma queste rimarranno obbligatorie. Per garantire una maggiore sicurezza e per facilitare l'uso dell'app per utenti e amministratori, è preferibile chiedere il consenso solo per le autorizzazioni realmente necessarie.

Per aggiungere o rimuovere le autorizzazioni visualizzate in questa tabella o in [Altre autorizzazioni concesse](#other-permissions-granted) (descritto nella sezione successiva), usare la procedura descritta in precedenza. Gli amministratori possono fornire il consenso amministratore per il set completo di autorizzazioni di un'API visualizzate nella tabella e revocarlo per singole autorizzazioni.

### <a name="other-permissions-granted"></a>Altre autorizzazioni concesse

Nel riquadro **Autorizzazioni API** potrebbe essere visualizzata anche una tabella diritti **Altre autorizzazioni concesse per {tenant}** . La tabella **Altre autorizzazioni concesse per {tenant}** mostra le autorizzazioni concesse per il tenant che non sono state configurate in modo esplicito nell'oggetto applicazione. Queste autorizzazioni sono state richieste e concesse dinamicamente. Questa sezione viene visualizzata solo se è presente almeno un'autorizzazione applicabile.

È possibile aggiungere nella tabella **Autorizzazioni configurate** il set completo di autorizzazioni o singole autorizzazioni di un'API visualizzate in questa tabella. Gli amministratori possono revocare il consenso amministratore per le API o per singole autorizzazioni in questa sezione.

### <a name="admin-consent-button"></a>Pulsante Consenso amministratore

Il pulsante **Concedi consenso amministratore per {tenant}** consente a un amministratore di fornire il consenso amministratore alle autorizzazioni configurate per l'applicazione. Quando si seleziona il pulsante, viene visualizzata una finestra di dialogo che chiede di confermare l'azione di consenso.

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-03-grant-admin-consent-button.png" alt-text="Pulsante Concedi consenso amministratore nel riquadro Autorizzazioni configurate nel portale di Azure":::

Dopo aver fornito il consenso, le autorizzazioni che richiedono il consenso amministratore vengono visualizzate come autorizzate:

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-04-admin-consent-granted.png" alt-text="Tabella Configura le autorizzazioni nel portale di Azure che mostra il consenso amministratore concesso per l'autorizzazione Files.Read.All":::

Se non si è un amministratore o se non sono state configurate autorizzazioni per l'applicazione, il pulsante **Concedi consenso amministratore** è *disabilitato*. In caso di autorizzazioni concesse ma non ancora configurate, verrà chiesto di decidere come gestirle quando si fa clic sul pulsante del consenso amministratore. È possibile aggiungerle alle autorizzazioni configurate o rimuoverle.

## <a name="next-steps"></a>Passaggi successivi

Passare all'argomento di avvio rapido successivo della serie per informazioni su come configurare i tipi di account che possono accedere all'applicazione. È ad esempio possibile limitare l'accesso solo agli utenti dell'organizzazione (tenant singolo) o consentirlo agli utenti di altri tenant di Azure AD (multi-tenant) e a quelli con account Microsoft personali.

> [!div class="nextstepaction"]
> [Modificare gli account supportati da un'applicazione](quickstart-modify-supported-accounts.md)