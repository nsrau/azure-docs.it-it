---
title: "Avvio rapido: Registrare un'app in Microsoft Identity Platform | Azure"
description: Questo argomento di avvio rapido illustra come registrare un'applicazione con Microsoft Identity Platform.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, identityplatformtop40, contperfq1
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 5f34215d57bd5dae8c9a5e6e8f4630b7ed0c827e
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89436724"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Guida introduttiva: Registrare un'applicazione con Microsoft Identity Platform

Questo argomento di avvio rapido illustra come registrare un'app nel portale di Azure in modo che Microsoft Identity Platform possa fornire servizi di autenticazione e autorizzazione per l'applicazione e i relativi utenti.

Ogni applicazione per cui si vuole che Microsoft Identity Platform esegua la gestione delle identità e degli accessi deve essere registrata. Sia che si tratti di un'applicazione client, come un'app Web o per dispositivi mobili, oppure di un'API Web a supporto di un'app client, la registrazione consente di stabilire una relazione di trust tra l'applicazione e il provider di identità, ovvero Microsoft Identity Platform.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva ([creare un account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))
* Completamento di [Avvio rapido: Configurare un tenant](quickstart-create-new-tenant.md)

## <a name="register-an-application"></a>Registrare un'applicazione

La registrazione dell'applicazione consente di stabilire una relazione di trust tra l'app e Microsoft Identity Platform. La relazione di trust è unidirezionale, ovvero l'app considera attendibile Microsoft Identity Platform e non viceversa.

Per creare la registrazione dell'app, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Se si accede a più tenant, usare il filtro **Directory e sottoscrizione** :::image type="icon" source="./media/quickstart-register-app/portal-01-directory-subscription-filter.png" border="false"::: nel menu in alto e selezionare il tenant in cui si vuole registrare un'applicazione.
1. Cercare e selezionare **Azure Active Directory**.
1. In **Gestisci** selezionare **Registrazioni app** e quindi **Nuova registrazione**.
1. Immettere un **nome** per l'applicazione. Tale nome, che potrebbe essere visualizzato dagli utenti dell'app, può essere modificato in un secondo momento.
1. Specificare chi può usare l'applicazione, noto in genere come *destinatari di accesso*.

    | Tipi di account supportati | Descrizione |
    |-------------------------|-------------|
    | **Account solo in questa directory organizzativa** | Selezionare questa opzione se si intende creare un'applicazione utilizzabile solo da utenti (o guest) nel tenant *personale*.<br><br>Nota anche come applicazione *line-of-business* (LOB), si tratta di un'applicazione a **singolo tenant** in Microsoft Identity Platform. |
    | **Account in qualsiasi directory organizzativa** | Selezionare questa opzione se si vuole che gli utenti in *qualsiasi* tenant di Azure AD possano usare l'applicazione. Questa opzione è appropriata se, ad esempio, si sta creando un'applicazione SaaS (Software-As-A-Service) che si intende fornire a più organizzazioni.<br><br>È nota come applicazione **multi-tenant** in Microsoft Identity Platform. |
    | **Account in qualsiasi directory organizzativa e account Microsoft personali** | Selezionare questa opzione per includere il set più ampio possibile di clienti.<br><br>Selezionando questa opzione, si registra un'applicazione **multi-tenant** che può supportare anche utenti con **account Microsoft** personali. |
    | **Account Microsoft personali** | Selezionare questa opzione se si intende creare un'applicazione utilizzabile solo da utenti con account Microsoft personali. Gli account Microsoft personali includono gli account Skype, Xbox, Live e Hotmail. |

1. Non immettere alcun valore per **URI di reindirizzamento (facoltativo)** perché ne verrà configurato uno nella sezione successiva.
1. Selezionare **Registra** per completare la registrazione iniziale dell'app.

    :::image type="content" source="media/quickstart-register-app/portal-02-app-reg-01.png" alt-text="Screenshot del portale di Azure in un Web browser che mostra il riquadro Registra un'applicazione.":::

Al termine della registrazione, nel portale di Azure viene visualizzato il riquadro **Panoramica** della registrazione dell'app, che include il relativo valore di **ID applicazione (client)** . Noto anche solo come *ID client*, questo valore identifica in modo univoco l'applicazione in Microsoft Identity Platform.

L'ID client viene usato anche nel codice dell'applicazione o, in genere, in una libreria di autenticazione usata nell'applicazione, come fattore per la convalida dei token di sicurezza ricevuti dalla piattaforma di gestione delle identità.

:::image type="content" source="media/quickstart-register-app/portal-03-app-reg-02.png" alt-text="Screenshot del portale di Azure in un Web browser che mostra il riquadro Panoramica della registrazione dell'app.":::

## <a name="add-a-redirect-uri"></a>Aggiungere un URI di reindirizzamento

Un URI di reindirizzamento corrisponde alla posizione a cui Microsoft Identity Platform reindirizza il client di un utente e invia i token di sicurezza dopo l'autenticazione.

In un'applicazione Web di produzione, ad esempio, l'URI di reindirizzamento è spesso un endpoint pubblico in cui viene eseguita l'app, ad esempio `https://contoso.com/auth-response`. Durante lo sviluppo capita di aggiungere l'endpoint in cui l'app viene eseguita in locale, ad esempio `https://127.0.0.1/auth-response`.

Per aggiungere e modificare gli URI di reindirizzamento per le applicazioni registrate, configurarne le [impostazioni della piattaforma](#configure-platform-settings).

### <a name="configure-platform-settings"></a>Configurare le impostazioni della piattaforma

Le impostazioni per ogni tipo di applicazione, inclusi gli URI di reindirizzamento, vengono configurate in **Configurazioni della piattaforma** nel portale di Azure. Con alcune piattaforme, ad esempio le applicazioni **Web** e **a pagina singola**, è necessario specificare manualmente un URI di reindirizzamento. Per altre piattaforme, ad esempio per applicazioni desktop e per dispositivi mobili, è possibile selezionare uno degli URI di reindirizzamento generati quando si configurano le altre impostazioni di tali piattaforme.

Per configurare le impostazioni dell'applicazione in base alla piattaforma o al dispositivo di destinazione:

1. Selezionare l'applicazione in **Registrazioni app** nel portale di Azure.
1. In **Gestisci** selezionare **Autenticazione**.
1. In **Configurazioni della piattaforma** selezionare **Aggiungi una piattaforma**.
1. In **Configura le piattaforme** selezionare il riquadro relativo al tipo di applicazione (piattaforma) per configurarne le impostazioni.

    :::image type="content" source="media/quickstart-register-app/portal-04-app-reg-03-platform-config.png" alt-text="Screenshot del riquadro Configurazione della piattaforma nel portale di Azure" border="false":::

    | Piattaforma | Impostazioni di configurazione |
    | -------- | ---------------------- |
    | **Web** | Immettere un **URI di reindirizzamento** per l'app, ovvero la posizione a cui Microsoft Identity Platform reindirizza il client di un utente e invia i token di sicurezza dopo l'autenticazione.<br/><br/>Selezionare questa piattaforma per le applicazioni Web standard eseguite in un server. |
    | **Applicazione a pagina singola** | Immettere un **URI di reindirizzamento** per l'app, ovvero la posizione a cui Microsoft Identity Platform reindirizza il client di un utente e invia i token di sicurezza dopo l'autenticazione.<br/><br/>Selezionare questa piattaforma se si sta creando un'app Web lato client in JavaScript o con un framework come Angular, Vue.js, React.js o Blazor WebAssembly. |
    | **iOS/macOS** | Immettere l'**ID bundle** dell'app, disponibile nell'ambiente XCode nel file *Info.plist* o nelle impostazioni di compilazione.<br/><br/>Quando si specifica un ID bundle, viene generato automaticamente un URI di reindirizzamento. |
    | **Android** | Immettere il **nome del pacchetto** dell'app, disponibile nel file *AndroidManifest.xml* e quindi generare e immettere l'**hash della firma**.<br/><br/>Quando si specificano queste impostazioni, viene generato automaticamente un URI di reindirizzamento. |
    | **Applicazioni per dispositivi mobili e desktop** | Selezionare uno dei valori in **URI di reindirizzamento suggeriti** o specificare un **URI di reindirizzamento personalizzato**.<br/>Per le applicazioni desktop è consigliabile:<br/>`https://login.microsoftonline.com/common/oauth2/nativeclient`<br/><br/>Selezionare questa piattaforma per le applicazioni per dispositivi mobili che non usano la libreria MSAL (Microsoft Authentication Library) più recente o che non usano un broker. Selezionare questa piattaforma anche per le applicazioni desktop. |
1. Selezionare **Configura** per completare la configurazione della piattaforma.

### <a name="redirect-uri-restrictions"></a>Restrizioni relative agli URI di reindirizzamento

Sono previste diverse restrizioni relative al formato degli URI di reindirizzamento aggiunti a una registrazione dell'app. Per informazioni dettagliate su queste restrizioni, vedere [Restrizioni e limitazioni degli URI di reindirizzamento (URL di risposta)](reply-url.md).

## <a name="add-credentials"></a>Aggiungere le credenziali

Le credenziali vengono usate dalle applicazioni client riservate che accedono a un'API Web. Esempi di client riservati sono le app Web, altri tipi di API Web oppure applicazioni di tipo servizio e daemon. Le credenziali consentono all'applicazione di eseguire l'autenticazione in modo autonomo, senza richiedere alcuna interazione utente in fase di esecuzione.

È possibile aggiungere sia certificati che segreti client (una stringa) come credenziali della registrazione dell'app client riservata.

:::image type="content" source="media/quickstart-register-app/portal-05-app-reg-04-credentials.png" alt-text="Screenshot del portale di Azure che mostra il riquadro Certificati e segreti in una registrazione dell'app":::

### <a name="add-a-certificate"></a>Aggiungere un certificato

Noti talvolta come *chiave pubblica*, i certificati sono il tipo di credenziale consigliato perché offrono un livello di garanzia superiore rispetto a un segreto client.

1. Selezionare l'applicazione in **Registrazioni app** nel portale di Azure.
1. Selezionare **Certificati e segreti** > **Carica certificato**.
1. Selezionare il file da caricare. Il tipo di file deve essere uno dei seguenti: .cer, .pem, .crt.
1. Selezionare **Aggiungi**.

### <a name="add-a-client-secret"></a>Aggiungere un segreto client

Il segreto client, noto anche come *password dell'applicazione*, è un valore stringa che l'app può usare al posto di un certificato per identificarsi. È tra i due tipi di credenziali quello più facile da usare e viene spesso usato in fase di sviluppo, ma è considerato meno sicuro di un certificato. È infatti consigliabile usare i certificati nelle applicazioni in esecuzione nell'ambiente di produzione.

1. Selezionare l'applicazione in **Registrazioni app** nel portale di Azure.
1. Selezionare **Certificati e segreti** >  **Nuovo segreto client**.
1. Aggiungere una descrizione per il segreto client.
1. Selezionare una durata.
1. Selezionare **Aggiungi**.
1. **Prendere nota del valore del segreto** per usarlo nel codice dell'applicazione client in quando *non verrà più visualizzato* dopo che si chiude questa pagina.

## <a name="next-steps"></a>Passaggi successivi

Le applicazioni client devono in genere accedere alle risorse in un'API Web. Oltre a proteggere l'applicazione client con Microsoft Identity Platform, è possibile usare la piattaforma per autorizzare l'accesso con ambito basato su autorizzazioni all'API Web.

Passare all'argomento di avvio rapido successivo della serie per creare un'altra registrazione dell'app per l'API Web ed esporne gli ambiti.

> [!div class="nextstepaction"]
> [Configurare un'applicazione per esporre un'API Web](quickstart-configure-app-expose-web-apis.md)
