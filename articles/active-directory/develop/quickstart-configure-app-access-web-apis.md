---
title: "Guida introduttiva: Accedere alle API Web per l'app - Microsoft Identity Platform | Azure"
description: Questo argomento di avvio rapido illustra come configurare un'app registrata con Microsoft Identity Platform in modo da includere gli URI di reindirizzamento, le credenziali o le autorizzazioni per l'accesso ad API Web.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/22/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, aragra, sureshja
ms.openlocfilehash: 210ed5b8ad53fd59a46e160fe5fc72633d115d44
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082323"
---
# <a name="quickstart-configure-a-client-application-to-access-web-apis"></a>Guida introduttiva: Configurare un'applicazione client per accedere alle API Web

In questo argomento di avvio rapido si aggiungono gli URI di reindirizzamento, le credenziali o le autorizzazioni per l'accesso alle API Web per l'applicazione. Per consentire a un'applicazione client Web o riservata di partecipare a un flusso di concessione di autorizzazioni che richiede l'autenticazione, è necessario definire credenziali sicure. Il metodo di autenticazione predefinito supportato dal portale di Azure è ID client + chiave privata. Durante questo processo, l'app ottiene un token di accesso.

Prima che un client possa accedere a un'API Web esposta dall'applicazione di una risorsa, ad esempio l'API Microsoft Graph, il framework di consenso assicura che il client ottenga la concessione necessaria delle autorizzazioni richieste. Per impostazione predefinita, tutte le applicazioni possono richiedere autorizzazioni all'API Microsoft Graph.

## <a name="prerequisites"></a>Prerequisiti

* Completamento di [Avvio rapido: Registrare un'applicazione con Microsoft Identity Platform](quickstart-register-app.md).
* Vedere [Autorizzazioni e consenso nell'endpoint di Microsoft Identity Platform](v2-permissions-and-consent.md).
* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Accedere al portale di Azure e selezionare l'app

1. Accedere al [portale di Azure](https://portal.azure.com) con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Se l'account consente di accedere a più tenant, selezionare l'account nell'angolo in alto a destra. Impostare la sessione del portale sul tenant di Azure AD desiderato.
1. Cercare e selezionare **Azure Active Directory**. In **Gestisci** selezionare **Registrazioni app**.
1. Trovare e selezionare l'applicazione che si vuole configurare. Dopo la selezione dell'app, verrà visualizzata la relativa pagina **Panoramica** o la pagina di registrazione principale.

Seguire queste procedure per configurare l'applicazione per l'accesso ad API Web.

## <a name="add-redirect-uris-to-your-application"></a>Aggiungere gli URI di reindirizzamento all'applicazione

È possibile aggiungere all'applicazione gli URI di reindirizzamento personalizzati e suggeriti. Per aggiungere un URI di reindirizzamento personalizzato per applicazioni client Web e pubbliche:

1. Nella pagina **Panoramica** dell'app selezionare **Autenticazione**.
1. Individuare l'opzione **URI di reindirizzamento**. Può essere necessario selezionare **Passa all'esperienza precedente**.
1. Selezionare il tipo di applicazione da creare: **Web** o **Client pubblico/nativo (dispositivi mobili e desktop)** .
1. Immettere l'URI di reindirizzamento per l'applicazione.

   * Per le applicazioni Web, specificare l'URL di base dell'applicazione. L'URL per un'applicazione Web in esecuzione nel computer locale potrebbe essere ad esempio `http://localhost:31544`. Gli utenti useranno questo URL per accedere a un'applicazione client Web.
   * Per le applicazione pubbliche, specificare l'URI usato da Azure AD per restituire le risposte dei token. Immettere un valore specifico dell'applicazione, ad esempio: `https://MyFirstApp`.
1. Selezionare **Salva**.

Per scegliere tra gli URI di reindirizzamento suggeriti per client pubblici, seguire questa procedura:

1. Nella pagina **Panoramica** dell'app selezionare **Autenticazione**.
1. Individuare l'opzione **URI di reindirizzamento suggeriti per client pubblici (dispositivi mobili, desktop)** . Può essere necessario selezionare **Passa all'esperienza precedente**.
1. Selezionare uno o più URI di reindirizzamento per l'applicazione. È anche possibile immettere un URI di reindirizzamento personalizzato. In caso di dubbi su quale usare, vedere la documentazione della libreria.
1. Selezionare **Salva**.

Agli URI di reindirizzamento si applicano specifiche restrizioni. Per altre informazioni, vedere [Restrizioni e limitazioni degli URI di reindirizzamento/URL di risposta](https://docs.microsoft.com/azure/active-directory/develop/reply-url).

> [!NOTE]
> Provare la nuova esperienza per le Impostazioni di **autenticazione** che consente di configurare le impostazioni dell'applicazione in base alla piattaforma o al dispositivo di destinazione.
>
> Per accedere a questa visualizzazione, selezionare **Prova la nuova esperienza** nella pagina **Autenticazione**.
>
> ![Fare clic su "Prova la nuova esperienza" per accedere alla visualizzazione della configurazione della piattaforma](./media/quickstart-update-azure-ad-app-preview/authentication-try-new-experience-cropped.png)
>
> Si verrà reindirizzati alla [nuova pagina **Configurazioni della piattaforma**](#configure-platform-settings-for-your-application).

### <a name="configure-advanced-settings-for-your-application"></a>Configurare le impostazioni avanzate per l'applicazione

A seconda dell'applicazione che si sta registrando, potrebbe essere necessario configurare impostazioni aggiuntive, ad esempio:

* **URL di disconnessione**.
* Per le app a singola pagina, è possibile abilitare **Concessione implicita** e selezionare i token che dovranno essere rilasciati dall'endpoint di autorizzazione.
* Per le app desktop che acquisiscono i token tramite l'autenticazione integrata di Windows, il flusso del codice del dispositivo o il nome utente e la password nella sezione **Tipo di client predefinito**, impostare l'opzione **Consente di gestire l'applicazione come un client pubblico** su **Sì**.
* Per le app legacy che usano Live SDK per l'integrazione con il servizio account Microsoft, configurare il **Supporto Live SDK**. Per le nuove app, questa impostazione non è necessaria.
* **Tipo di client predefinito**.
* **Tipi di account supportati**.

### <a name="modify-supported-account-types"></a>Modificare i tipi di account supportati

Nei **Tipi di account supportati** sono specificati gli utenti autorizzati a usare l'applicazione o ad accedere all'API.

Se durante la registrazione dell'applicazione sono stati configurati i tipi di account supportati, è possibile cambiare questa impostazione usando l'editor del manifesto dell'applicazione solo se:

* I tipi di account vengono cambiati da **AzureADMyOrg** o **AzureADMultipleOrgs** in **AzureADandPersonalMicrosoftAccount** o viceversa.
* I tipi di account vengono cambiati da **AzureADMyOrg** in **AzureADMultipleOrgs** o viceversa.

Per cambiare i tipi di account supportati per una registrazione app esistente, aggiornare la chiave `signInAudience`. Per altre informazioni, vedere [Configurare il manifesto dell'applicazione](reference-app-manifest.md#configure-the-app-manifest).

## <a name="configure-platform-settings-for-your-application"></a>Configurare le impostazioni della piattaforma per l'applicazione

![Configurare le impostazioni per l'app in base alla piattaforma o al dispositivo](./media/quickstart-update-azure-ad-app-preview/authentication-new-platform-configurations.png)

Per configurare le impostazioni dell'applicazione in base alla piattaforma o al dispositivo di destinazione:

1. Nella pagina **Configurazioni della piattaforma** selezionare **Aggiungi una piattaforma** e scegliere una delle opzioni disponibili.

   ![Mostra la pagina Configura piattaforme](./media/quickstart-update-azure-ad-app-preview/authentication-platform-configurations-configure-platforms.png)

1. Immettere le informazioni sulle impostazioni in base alla piattaforma selezionata.

   | Piattaforma                | Impostazioni di configurazione            |
   |-------------------------|-----------------------------------|
   | **Web**              | Immettere l'**URI di reindirizzamento** per l'applicazione. |
   | **iOS/macOS**              | Immettere l'**ID bundle** dell'app, disponibile nell'ambiente XCode nel file Info.plist o nelle impostazioni di compilazione. L'aggiunta dell'ID bundle crea automaticamente un URI di reindirizzamento per l'applicazione. |
   | **Android**          | Specificare il **nome del pacchetto** dell'app, disponibile nel file AndroidManifest.xml.<br/>Generare e immettere l'**hash della firma**. L'aggiunta dell'hash della firma crea automaticamente un URI di reindirizzamento per l'applicazione.  |
   | **Applicazioni per dispositivi mobili e desktop**  | Facoltativa. Selezionare uno degli **URI di reindirizzamento suggeriti** se si creano app per desktop e dispositivi.<br/>Facoltativa. Immettere un **URI di reindirizzamento personalizzato** che costituisce la posizione a cui Azure AD reindirizzerà gli utenti in risposta alle richieste di autenticazione. Ad esempio, per le applicazioni .NET Core che richiedono interazione, usare `http://localhost`. |

   > [!NOTE]
   > In Active Directory Federation Services (AD FS) e Azure AD B2C è necessario specificare anche un numero di porta.  Ad esempio: `http://localhost:1234`. 

   > [!IMPORTANT]
   > Per le applicazioni per dispositivi mobili che non usano la libreria MSAL (Microsoft Authentication Library) più recente o che non usano un broker, è necessario configurare gli URI di reindirizzamento in **Desktop e dispositivi**.

A seconda della piattaforma scelta, potrebbe essere possibile configurare impostazioni aggiuntive. Per le app **Web**, è possibile:

* Aggiungere più URI di reindirizzamento
* Configurare la **Concessione implicita** per selezionare i token che devono essere rilasciati dall'endpoint di autorizzazione:

  * Per le app a singola pagina, selezionare sia **Token di accesso** sia **Token ID**
  * Per le app Web, selezionare **Token ID**

## <a name="add-credentials-to-your-web-application"></a>Aggiungere credenziali all'applicazione Web

Per aggiungere una credenziale all'applicazione Web, aggiungere un certificato o creare un segreto client. Per aggiungere un certificato:

1. Nella pagina **Panoramica** dell'app selezionare la sezione **Certificati e segreti**.
1. Selezionare **Carica certificato**.
1. Selezionare il file da caricare. Il tipo di file deve essere uno dei seguenti: .cer, .pem, .crt.
1. Selezionare **Aggiungi**.

Per aggiungere un segreto client:

1. Nella pagina **Panoramica** dell'app selezionare la sezione **Certificati e segreti**.
1. Selezionare **Nuovo segreto client**.
1. Aggiungere una descrizione per il segreto client.
1. Selezionare una durata.
1. Selezionare **Aggiungi**.

> [!NOTE]
> Dopo il salvataggio delle modifiche alla configurazione, nella colonna più a destra verrà visualizzato il valore del segreto client. **Assicurarsi di copiare il valore** per usarlo nel codice dell'applicazione client, perché non sarà più accessibile dopo aver lasciato la pagina.

## <a name="add-permissions-to-access-web-apis"></a>Aggiungere autorizzazioni per accedere ad API Web

L'[autorizzazione Accedi e leggi il profilo di un altro utente dell'API Graph](https://developer.microsoft.com/graph/docs/concepts/permissions_reference#user-permissions) è selezionata per impostazione predefinita. È possibile selezionare tra [due tipi di autorizzazione](developer-glossary.md#permissions) per ogni API Web:

* **Autorizzazioni dell'applicazione**. L'applicazione client deve accedere all'API Web direttamente come se stessa, senza contesto utente. Questo tipo di autorizzazione richiede il consenso dell'amministratore. Questa autorizzazione non è disponibile per le applicazioni client per dispositivi mobili e desktop.
* **Autorizzazioni delegate**. l'applicazione client deve accedere all'API Web come utente connesso, ma con accesso limitato dall'autorizzazione selezionata. Questo tipo di autorizzazione può essere concesso da un utente, a meno che l'autorizzazione richieda il consenso dell'amministratore.

  > [!NOTE]
  > L'aggiunta di un'autorizzazione delegata a un'applicazione non concede automaticamente il consenso all'utente all'interno del tenant. Gli utenti devono comunque concedere manualmente il consenso per le autorizzazioni delegate aggiuntive in fase di esecuzione, a meno che l'amministratore non conceda il consenso per conto di tutti gli utenti.

Per aggiungere autorizzazioni per l'accesso ad API di risorsa dal client:

1. Nella pagina **Panoramica** dell'app selezionare **Autorizzazioni API**.
1. In **Autorizzazioni configurate** selezionare **Aggiungi un'autorizzazione**.
1. Per impostazione predefinita, la visualizzazione consente di selezionare tra **API Microsoft**. Selezionare la sezione delle API a cui si è interessati:

    * **API Microsoft**. Consente di selezionare le autorizzazioni per API Microsoft come Microsoft Graph.
    * **API usate dall'organizzazione**. Consente di selezionare le autorizzazioni per le API che sono state esposte dall'organizzazione o con cui l'organizzazione ha eseguito l'integrazione.
    * **Le mie API**. Consente di selezionare le autorizzazioni per le API esposte dall'utente.

1. Al termine della selezione delle API, verrà visualizzata la pagina **Richiedi le autorizzazioni dell'API**. Se l'API espone autorizzazioni sia delegate che di tipo applicazione, selezionare il tipo di autorizzazione necessario all'applicazione.
1. Al termine, selezionare **Aggiungi autorizzazioni**.

Si torna nella pagina **Autorizzazioni API**. Le autorizzazioni sono state salvate e aggiunte alla tabella.

## <a name="understanding-api-permissions-and-admin-consent-ui"></a>Informazioni sulle autorizzazioni API e sull'interfaccia utente di consenso amministratore

### <a name="configured-permissions"></a>Autorizzazioni configurate

Questa sezione illustra le autorizzazioni configurate in modo esplicito nell'oggetto applicazione. Queste autorizzazioni fanno parte dell'elenco di accesso alle risorse necessarie dell'app. È possibile aggiungere o rimuovere autorizzazioni da questa tabella. L'amministratore può anche concedere o revocare il consenso amministratore per una o più autorizzazioni di un'API.

### <a name="other-permissions-granted"></a>Altre autorizzazioni concesse

Se l'applicazione è registrata in un tenant, è possibile che venga visualizzata una sezione aggiuntiva intitolata **Altre autorizzazioni concesse per Tenant**. Questa sezione illustra le autorizzazioni concesse per il tenant che non sono state configurate in modo esplicito nell'oggetto applicazione. Queste autorizzazioni sono state richieste e concesse dinamicamente. Questa sezione viene visualizzata solo se è presente almeno un'autorizzazione applicabile.

È possibile aggiungere un set di autorizzazioni o singole autorizzazioni di un'API visualizzate in questa sezione sulla sezione **Autorizzazioni configurate**. In qualità di amministratore, è anche possibile revocare il consenso amministratore per singole API o autorizzazioni in questa sezione.

### <a name="admin-consent-button"></a>Pulsante Consenso amministratore

Se l'applicazione è registrata in un tenant, viene visualizzato il pulsante **Concedi consenso amministratore per Tenant**. Se non si è un amministratore o se non sono state configurate autorizzazioni per l'applicazione, questo pulsante è disabilitato.
Il pulsante consente a un amministratore di concedere il consenso amministratore alle autorizzazioni configurate per l'applicazione. Facendo clic sul pulsante Consenso amministratore, viene avviata una nuova finestra con una richiesta di consenso che mostra tutte le autorizzazioni configurate.

> [!NOTE]
> Si verifica un ritardo tra la configurazione delle autorizzazioni per l'applicazione e la relativa visualizzazione nella richiesta di consenso. Se non vengono visualizzate tutte le autorizzazioni configurate nella richiesta di consenso, chiuderla e riavviarla.

In caso di autorizzazioni concesse ma non configurate, quando si fa clic sul pulsante del consenso amministratore, verrà chiesto di decidere come gestirle. È possibile aggiungerle alle autorizzazioni configurate o rimuoverle.

La richiesta di consenso fornisce le opzioni **Accetta** o **Annulla**. Selezionare **Accetta** per concedere il consenso amministratore. Se si seleziona **Annulla**, il consenso amministratore non viene concesso. Un messaggio di errore indica che il consenso è stato rifiutato.

> [!NOTE]
> Esiste un ritardo tra la concessione del consenso amministratore (selezionando **Accetta** nella richiesta di consenso) e lo stato del consenso amministratore riportato nell'interfaccia utente.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come esporre le API Web, passare all'articolo successivo.
> [!div class="nextstepaction"]
> [Avvio rapido: Configurare un'applicazione per l'esposizione di API Web](quickstart-configure-app-expose-web-apis.md)

* Per altre informazioni su due oggetti di Azure AD che rappresentano un'applicazione registrata e la relazione tra essi, vedere [Oggetti applicazione e oggetti entità servizio](app-objects-and-service-principals.md).

* Per altre informazioni sulle linee guida sulla personalizzazione da seguire per lo sviluppo di applicazioni con Azure Active Directory, vedere [Linee guida sulla personalizzazione delle applicazioni](howto-add-branding-in-azure-ad-apps.md).

* [Avvio rapido: Registrare un'applicazione con Microsoft Identity Platform](quickstart-register-app.md)

* [Avvio rapido: Modificare gli account supportati da un'applicazione](quickstart-modify-supported-accounts.md)

* [Avvio rapido: Rimuovere un'applicazione registrata con Microsoft Identity Platform](quickstart-remove-app.md)
