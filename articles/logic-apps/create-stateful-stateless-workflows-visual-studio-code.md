---
title: Creare flussi di lavoro di automazione (anteprima) in Visual Studio Code
description: Creare flussi di lavoro di automazione senza stato o con stato con l'estensione app per la logica di Azure (anteprima) in Visual Studio Code per integrare app, dati, servizi cloud e sistemi locali
services: logic-apps
ms.suite: integration
ms.reviewer: deli, rohitha, vikanand, hongzili, sopai, absaafan, logicappspm
ms.topic: conceptual
ms.date: 09/26/2020
ms.openlocfilehash: cc52358af203bafc87c5f9ac3ae1f237c0c7ae6c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91597794"
---
# <a name="create-stateful-or-stateless-workflows-in-visual-studio-code-with-the-azure-logic-apps-preview-extension"></a>Creare flussi di lavoro con stato o senza stato in Visual Studio Code con l'estensione app per la logica di Azure (anteprima)

> [!IMPORTANT]
> Questa funzionalità è disponibile in anteprima pubblica, viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Per creare flussi di lavoro di app per la logica che si integrano tra app, dati, servizi cloud e sistemi, è possibile usare Visual Studio Code e l'estensione app per la logica di Azure (anteprima) per compilare ed eseguire localmente flussi di lavoro di app per la logica con [ *stato* e senza *stateless* stato](#stateful-stateless) nell'ambiente di sviluppo.

![Screenshot che mostra Visual Studio Code e il flusso di lavoro dell'app per la logica.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-logic-apps-overview.png)

Le app per la logica create con l'estensione di anteprima pubblica usano il nuovo tipo di risorsa app per la **logica (anteprima)** e sono basate sul runtime di [funzioni di Azure](../azure-functions/functions-overview.md) nell'ambiente locale. Questo nuovo tipo di risorsa può includere più flussi di lavoro ed è simile in alcuni modi alla **app per le funzioni** tipo di risorsa, che può includere più funzioni.

Nel frattempo, il tipo di risorsa app per la **logica** originale è ancora disponibile per la creazione e l'uso in Visual Studio Code e nel portale di Azure. Tuttavia, le esperienze per il tipo di risorsa originale sono separate dal nuovo tipo di risorsa. Attualmente, **le app per la logica e i** tipi di risorse dell'app per la **logica (anteprima)** possono esistere allo stesso tempo in Visual Studio Code e nel portale di Azure. È possibile visualizzare e accedere a tutte le app per la logica distribuite nella sottoscrizione di Azure, ma vengono visualizzate e mantenute separatamente nelle rispettive categorie e sezioni.

Questo articolo fornisce una [Panoramica di alto livello su questa anteprima pubblica](#whats-new), descrive i vari aspetti relativi al tipo di risorsa app per la **logica (anteprima)** e illustra come creare questa risorsa usando Visual Studio Code:

* Il modo in cui le app per la logica [con stato e senza stato](#stateful-stateless) si differenziano.

* Come soddisfare i [requisiti di installazione](#prerequisites) e [configurare Visual Studio Code](#set-up) per l'estensione di anteprima pubblica.

* Come compilare nuovi flussi di lavoro di app per la **logica (anteprima)** [creando un progetto e selezionando un modello di flusso di lavoro](#create-project).

* Come eseguire localmente ed eseguire il debug delle nuove app per la logica in Visual Studio Code.

* Come pubblicare queste nuove app per la logica direttamente da Visual Studio Code [ad Azure](#publish-azure) o [a un contenitore Docker](#deploy-docker) che è possibile eseguire ovunque. Per altre informazioni su Docker, vedere [che cos'è Docker](/dotnet/architecture/microservices/container-docker-introduction/docker-defined)?

<a name="whats-new"></a>

## <a name="whats-in-this-public-preview"></a>Contenuto della versione di anteprima pubblica

L'estensione app per la logica di Azure (anteprima) offre numerose funzionalità di app per la logica attuali e aggiuntive per la tua esperienza di sviluppo locale in Visual Studio Code, ad esempio:

* Crea app per la logica per flussi di lavoro di integrazione e automazione da più di [390 connettori](/connectors/connector-reference/connector-reference-logicapps-connectors) per applicazioni SaaS (software-as-a-Service) e PaaS (piattaforma distribuita come servizio) e per i servizi e i connettori per i sistemi locali.

  * Alcuni connettori gestiti, ad esempio il bus di servizio di Azure, Hub eventi di Azure e SQL Server vengono eseguiti in modo analogo ai trigger nativi predefiniti e alle azioni come l'azione HTTP.

  * Creare e distribuire app per la logica che possono essere eseguite ovunque, perché il servizio app per la logica di Azure genera stringhe di connessione di firma di accesso condiviso che possono essere usate da queste app per la logica per l'invio di richieste all'endpoint di runtime della connessione cloud. Il servizio app per la logica Salva queste stringhe di connessione con altre impostazioni dell'applicazione in modo che sia possibile archiviare facilmente questi valori in Azure Key Vault durante la distribuzione in Azure.

    > [!NOTE]
    > Per impostazione predefinita, una risorsa di app per la **logica (anteprima)** ha l' [identità gestita assegnata dal sistema](../logic-apps/create-managed-service-identity.md) abilitata automaticamente per autenticare le connessioni in fase di esecuzione. Questa identità è diversa dalle credenziali di autenticazione o dalla stringa di connessione utilizzata per la creazione di una connessione. Se questa identità viene disabilitata, le connessioni non funzioneranno in fase di esecuzione.

* È possibile creare app per la logica senza stato che vengono eseguite solo in memoria, in modo che vengano completate più rapidamente, rispondono più velocemente, abbiano una velocità effettiva più elevata e costi inferiori per l'esecuzione perché le cronologie di esecuzione e i dati tra le azioni non vengono mantenuti nella risorsa Facoltativamente, è possibile abilitare la cronologia di esecuzione per semplificare il debug. Per altre informazioni, vedere [app](#stateful-stateless)per la logica con stato e senza stato.

* Eseguire ed eseguire il debug delle app per la logica in locale nell'ambiente di sviluppo Visual Studio Code.

* Pubblicare e distribuire le app per la logica da Visual Studio Code direttamente in diversi ambienti host, ad esempio [app Azure servizio](../app-service/environment/intro.md) e [contenitori Docker](/dotnet/core/docker/introduction).

> [!NOTE]
> Per informazioni sui problemi noti correnti, vedere la [pagina di GitHub problemi noti](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)dell'estensione di anteprima.

<a name="stateful-stateless"></a>

## <a name="stateful-versus-stateless-logic-apps"></a>App per la logica con stato e senza stato

* *Con stato*

  Creare app per la logica con stato quando è necessario tenere, rivedere o fare riferimento ai dati degli eventi precedenti. Queste app per la logica mantengono l'input e l'output per ogni azione e i rispettivi Stati del flusso di lavoro in un archivio esterno, che consente di esaminare i dettagli e la cronologia delle esecuzioni al termine di ogni esecuzione. Le app per la logica con stato garantiscono una resilienza elevata se o quando si verificano interruzioni. Una volta ripristinati i servizi e i sistemi, è possibile ricostruire le esecuzioni di app per la logica interrotte dallo stato salvato e rieseguire le app per la logica fino al completamento I flussi di lavoro con stato possono continuare a funzionare per un massimo di un anno.

* *Senza stato*

  Creare app per la logica senza stato quando non è necessario salvare, rivedere o fare riferimento ai dati degli eventi precedenti nell'archivio esterno per una revisione successiva. Queste app per la logica mantengono l'input e l'output per ogni azione e i relativi stati del flusso di lavoro solo in memoria, anziché trasferire tali informazioni in un archivio esterno. Di conseguenza, le app per la logica senza stato hanno esecuzioni più brevi che in genere non durano più di 5 minuti, prestazioni più veloci con tempi di risposta più rapidi, velocità effettiva più elevata e costi di esecuzione ridotti perché i dettagli e la cronologia dell'esecuzione non vengono conservati nell'archiviazione esterna. Tuttavia, se o quando si verificano interruzioni, le esecuzioni interrotte non vengono ripristinate automaticamente, quindi il chiamante deve inviare nuovamente le esecuzioni interrotte manualmente. Queste app per la logica possono essere eseguite solo in modo sincrono e per semplificare il debug, è possibile [abilitare la cronologia di esecuzione](#run-history), che ha un certo effetto sulle prestazioni.

  I flussi di lavoro senza stato attualmente supportano solo azioni per i [connettori gestiti](../connectors/apis-list.md#managed-api-connectors), non per i trigger. Per avviare il flusso di lavoro, selezionare la [richiesta predefinita, gli hub eventi o il trigger del bus di servizio](../connectors/apis-list.md#built-ins). Per ulteriori informazioni sui trigger, le azioni e i connettori non supportati, vedere [funzionalità non supportate](#unsupported).

Per informazioni sul comportamento delle app per la logica annidate in modo diverso tra app per la logica con stato e senza stato, vedere [differenze di comportamento annidate tra app per la logica con stato e senza stato](#nested-behavior)

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Modello di prezzi

Quando si distribuisce il tipo di risorsa nuova app per la **logica (anteprima)** , viene richiesto di selezionare un piano di hosting, in particolare il piano di [servizio app o il piano Premium](../azure-functions/functions-scale.md) da usare come modello di determinazione prezzi. Se si seleziona il piano di servizio app, viene anche chiesto di selezionare un piano [tariffario](../app-service/overview-hosting-plans.md). Durante l'anteprima pubblica, l'esecuzione di app per la logica nel servizio app non comporta costi *aggiuntivi* oltre il piano selezionato.

Le app per la logica con stato usano l' [archiviazione esterna](../azure-functions/functions-scale.md#storage-account-requirements), quindi il modello di prezzi di archiviazione di Azure si applica alle transazioni di archiviazione eseguite dal runtime di app per la logica di Azure Ad esempio, le code vengono utilizzate per la pianificazione, mentre le tabelle e i BLOB vengono utilizzati per l'archiviazione degli Stati del flusso di lavoro.

Per ulteriori informazioni sui modelli di determinazione prezzi applicabili a questo nuovo tipo di risorsa, consultare gli argomenti seguenti:

* [Ridimensionamento e hosting di Funzioni di Azure](../azure-functions/functions-scale.md)
* [Scalabilità verticale di un servizio app Azure](../app-service/manage-scale-up.md)
* [Dettagli prezzi di funzioni di Azure](https://azure.microsoft.com/pricing/details/functions/)
* [Dettagli prezzi del servizio app](https://azure.microsoft.com/pricing/details/app-service/windows/)
* [Dettagli prezzi di archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/)

<a name="unsupported"></a>

## <a name="unavailable-or-unsupported-capabilities"></a>Funzionalità non disponibili o non supportate

Per questa anteprima pubblica, queste funzionalità non sono disponibili o non sono supportate:

* La creazione della nuova risorsa app per la **logica (anteprima)** non è attualmente disponibile in MacOS.

* Non tutte le aree di Azure sono ancora supportate. Per le aree attualmente disponibili, controllare l' [elenco delle aree](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions).

* Per avviare il flusso di lavoro, usare la [richiesta, http, Hub eventi o il trigger del bus di servizio](../connectors/apis-list.md). Attualmente, i [connettori aziendali](../connectors/apis-list.md#enterprise-connectors), i [trigger del gateway dati locale](../connectors/apis-list.md#on-premises-connectors), i trigger basati su webhook, il trigger finestra scorrevole, i [connettori personalizzati](../connectors/apis-list.md#custom-apis-and-connectors), gli account di integrazione, i relativi elementi e [i relativi connettori](../connectors/apis-list.md#integration-account-connectors) non sono supportati in questa versione di anteprima. La funzionalità "chiama una funzione di Azure" non è disponibile. per ora, usare l' *azione* http per chiamare l'URL della richiesta per la funzione di Azure.

  I flussi di lavoro di app per la logica senza stato possono usare solo azioni per [connettori gestiti](../connectors/apis-list.md#managed-api-connectors), non per trigger. Ad eccezione dei trigger specificati in precedenza, i flussi di lavoro con stato possono usare sia trigger che azioni per i connettori gestiti.

* È possibile distribuire il nuovo tipo di risorsa app per la **logica (anteprima)** solo a un [piano di hosting del servizio app o Premium in Azure](#publish-azure) o a un [contenitore Docker](#deploy-docker)e non agli [ambienti del servizio di integrazione (ISEs)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). I piani di hosting a **consumo** non sono supportati né disponibili per la distribuzione di questo tipo di risorsa.

* Nel portale di Azure non è possibile creare nuove app per la logica con il nuovo tipo di risorsa app per la **logica (anteprima)** . È possibile creare queste app per la logica solo in Visual Studio Code. Tuttavia, dopo aver distribuito app per la logica con questo tipo di risorsa da Visual Studio Code ad Azure, è possibile [aggiungere nuovi flussi di lavoro a tali app](#add-workflows)per la logica.

## <a name="prerequisites"></a>Prerequisiti

### <a name="access-and-connectivity"></a>Accesso e connettività

* Accedere a Internet in modo da poter scaricare i requisiti, connettersi da Visual Studio Code all'account Azure e pubblicare da Visual Studio Code ad Azure, un contenitore Docker o un altro ambiente.

* Un account e una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Per compilare la stessa app per la logica di esempio in questo articolo, è necessario un account di posta elettronica di Office 365 Outlook che usa un account Microsoft aziendale o dell'Istituto di istruzione per accedere.

  Se si sceglie di usare un [connettore di posta elettronica diverso supportato da app per la logica di Azure](/connectors/), ad esempio Outlook.com o [gmail](../connectors/connectors-google-data-security-privacy-policy.md), è comunque possibile seguire l'esempio e la procedura complessiva generale è la stessa, ma l'interfaccia utente e le opzioni potrebbero variare in qualche modo. Se ad esempio si usa il connettore Outlook.com, usare il account Microsoft personale per accedere.

### <a name="storage-requirements"></a>Requisiti di archiviazione

1. Scaricare e installare l' [emulatore di archiviazione di Azure 5,10](https://go.microsoft.com/fwlink/p/?linkid=717179).

1. Per eseguire l'emulatore, è necessario disporre di un'installazione locale del database SQL, ad esempio la [versione gratuita SQL Server 2019 Express Edition](https://go.microsoft.com/fwlink/p/?linkid=866658). Per altre informazioni, vedere [usare l'emulatore di archiviazione di Azure per sviluppo e test](../storage/common/storage-use-emulator.md).

   > [!IMPORTANT]
   > Prima di aprire la finestra di progettazione dell'app per la logica per creare il flusso di lavoro dell'app per la logica, assicurarsi di avviare l'emulatore. In caso contrario, viene ricevuto un messaggio che `Workflow design time could not be started` .
   >
   > ![Screenshot che mostra l'emulatore di archiviazione di Azure in esecuzione.](./media/create-stateful-stateless-workflows-visual-studio-code/start-storage-emulator.png)

### <a name="tools"></a>Strumenti

* [Visual Studio Code 1.30.1 (gennaio 2019) o versione successiva](https://code.visualstudio.com/), che è gratuita. Inoltre, scaricare e installare questi strumenti aggiuntivi per Visual Studio Code, se non sono già presenti:

  * [Estensione dell'account Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account), che fornisce un'unica esperienza comune di accesso di Azure e di filtro delle sottoscrizioni per tutte le altre estensioni di azure in Visual Studio Code.

  * [C# per Visual Studio Code estensione](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp), che consente la funzionalità F5 per eseguire l'app per la logica.

  * [Azure Functions Core Tools](../azure-functions/functions-run-local.md), versione [3.0.2931](https://github.com/Azure/azure-functions-core-tools/releases/tag/3.0.2931) o [2.7.2936](https://github.com/Azure/azure-functions-core-tools/releases/tag/2.7.2936), tramite Microsoft Installer (MSI). Questi strumenti includono una versione dello stesso runtime che consente il runtime di funzioni di Azure in esecuzione in Visual Studio Code.

    > [!IMPORTANT]
    > Se si dispone di un'installazione precedente a queste versioni, disinstallare prima la versione o assicurarsi che la variabile di ambiente PATH punti alla versione scaricata e installata.
    >
    > Se si vuole usare l'azione di [ **codice inline** ](../logic-apps/logic-apps-add-run-inline-code.md) per l'esecuzione del codice JavaScript, è necessario usare il runtime di funzioni di Azure versione 3x perché l'azione non supporta la versione 2x. Inoltre, questa azione attualmente non è supportata nei sistemi operativi Linux.

  * [Estensione app per la logica di Azure (anteprima) per Visual Studio Code](https://go.microsoft.com/fwlink/p/?linkid=2143167). Questa estensione di anteprima pubblica fornisce la possibilità di creare app per la logica con stato e senza stato ed eseguirle localmente in Visual Studio Code.

    Attualmente, è possibile installare contemporaneamente l'estensione originale app per la **logica di Azure** e la nuova estensione app per la logica di **Azure (anteprima)** in Visual Studio Code. Selezionando l'icona di Azure sulla barra degli strumenti Visual Studio Code è possibile visualizzare tutte le app per la logica distribuite in Azure, ma ogni tipo di risorsa viene visualizzato nelle rispettive sezioni di estensione, app per la **logica** e app per la **logica di Azure (anteprima)**.

    > [!IMPORTANT]
    > Se sono state create app per la logica usando l'estensione app per la logica di **Azure (anteprima privata)** , queste app per la logica non funzioneranno con l'estensione di anteprima pubblica. Tuttavia, è possibile eseguire la migrazione di queste app per la logica disinstallando l'estensione di anteprima privata, eseguendo la pulizia richiesta e installando l'estensione di anteprima pubblica. È quindi possibile creare il nuovo progetto in Visual Studio Code e copiare il file **Workflow. Definition** di app per la logica creato in precedenza nel nuovo progetto.
    >
    > Quindi, prima di installare l'estensione di anteprima pubblica, assicurarsi di disinstallare le versioni precedenti ed eliminare gli artefatti seguenti:
    >
    > * La cartella **Microsoft. Azure. Functions. ExtensionBundle. Workflows** , che contiene i bundle di estensione precedenti e si trova lungo uno dei percorsi seguenti:
    >
    >   * `C:\Users\{userName}\AppData\Local\Temp\Functions\ExtensionBundles`
    >
    >   * `C:\Users\{userName}.azure-functions-core-tools\Functions\ExtensionBundles`
    >
    > * La cartella **Microsoft. Azure. Workflows. webjobs. Extension** , che è la cache [NuGet](/nuget/what-is-nuget) per l'estensione di anteprima privata, si trova lungo questo percorso:
    >
    >   `C:\Users\{userName}\.nuget\packages`

    Per installare l'estensione app per la **logica di Azure (anteprima)** , seguire questa procedura:

    1. In Visual Studio Code, sulla barra degli strumenti a sinistra, selezionare **estensioni**.

    1. Nella casella di ricerca estensioni immettere `azure logic apps preview` . Dall'elenco dei risultati selezionare app per la **logica di Azure (anteprima)** **>** **Install**.

       Al termine dell'installazione, l'estensione di anteprima pubblica viene visualizzata nell'elenco **estensioni: installato** .

       ![Screenshot che mostra l'elenco delle estensioni installate di Visual Studio Code con l'estensione "app per la logica di Azure (anteprima)" sottolineata.](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-extension-installed.png)

* Per testare l'app per la logica di esempio creata in questo articolo, è necessario uno strumento che può inviare chiamate al trigger di richiesta, che è il primo passaggio nell'app per la logica di esempio. Se non si dispone di uno strumento di questo tipo, è possibile scaricare, installare e usare il [post](https://www.postman.com/downloads/).

* Per semplificare la registrazione di diagnostica e la funzionalità di traccia, è possibile aggiungere e usare una risorsa [Application Insights](../azure-monitor/app/app-insights-overview.md) . È possibile creare questa risorsa durante la distribuzione dell'app per la logica o nel portale di Azure dopo la distribuzione dell'app per la logica.

<a name="set-up"></a>

## <a name="set-up-visual-studio-code"></a>Configurare Visual Studio Code

1. Per assicurarsi che tutte le estensioni siano installate correttamente, ricaricare o riavviare Visual Studio Code.

1. Abilita o verifica che Visual Studio Code trova e installa automaticamente gli aggiornamenti delle estensioni in modo che l'estensione di anteprima pubblica ottenga gli aggiornamenti più recenti.

   Per controllare questa impostazione, segui questa procedura:

   1. Nel menu **file** passare a **Preferenze** **>** **Impostazioni**.

   1. Nella scheda **utente** passare a **funzionalità** **>** **estensioni**.

   1. Verificare che sia selezionata l'opzione **Controlla automaticamente aggiornamenti** e **aggiornamento automatico** .

1. Abilitare o verificare che le impostazioni dell'estensione app per la **logica di Azure (anteprima)** siano configurate in Visual Studio Code:

   * **App per la logica di Azure V2: modalità pannello**
   * **App per la logica di Azure V2: runtime del progetto**

   1. Nel menu **file** passare a **Preferenze** **>** **Impostazioni**.

   1. Nella scheda **utente** passare a **>** **estensioni** app per la **>** **logica di Azure (anteprima)**.

   1. In **modalità pannello di app per la logica di Azure V2**verificare che sia selezionata l'opzione **Abilita modalità pannello** . In **app per la logica di Azure V2: runtime del progetto**impostare la versione su **~ 3** o **~ 2**, in base alla [versione Azure Functions Core Tools](#prerequisites) installata in precedenza.

      > [!IMPORTANT]
      > Se si vuole usare l'azione di [ **codice inline** ](../logic-apps/logic-apps-add-run-inline-code.md) per l'esecuzione del codice JavaScript, assicurarsi di usare Project Runtime versione 3 perché l'azione non supporta la versione 2. Inoltre, questa azione attualmente non è supportata nei sistemi operativi Linux.

      ![Screenshot che mostra le impostazioni di Visual Studio Code per l'estensione "app per la logica di Azure (anteprima)".](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-preview-settings.png)

<a name="connect-azure-account"></a>

## <a name="connect-to-your-azure-account"></a>Connettersi all'account di Azure

1. Sulla barra degli strumenti di Visual Studio Code selezionare l'icona di Azure.

   ![Screenshot che mostra Visual Studio Code barra degli strumenti e l'icona di Azure selezionata.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-azure-icon.png)

1. Nel riquadro di Azure, in **Azure: app per la logica (anteprima)**, selezionare **Accedi ad Azure**. Quando viene visualizzata la pagina autenticazione Visual Studio Code, accedere con l'account Azure.

   ![Screenshot che mostra il riquadro di Azure e il collegamento selezionato per l'accesso ad Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/sign-in-azure-subscription.png)

   Dopo aver eseguito l'accesso, il riquadro Azure Mostra le sottoscrizioni nell'account Azure. Se si dispone dell'estensione app per la logica rilasciata pubblicamente, è possibile trovare tutte le risorse originali per le app per la logica create usando l'estensione originale nella sezione app per la **logica** dell'estensione rilasciata, non la sezione app per la logica dell'estensione di anteprima **(anteprima** ).
   
   Se le sottoscrizioni previste non vengono visualizzate o se si desidera che il riquadro visualizzi solo sottoscrizioni specifiche, attenersi alla seguente procedura:

   1. Nell'elenco sottoscrizioni spostare il puntatore accanto alla prima sottoscrizione fino a quando non viene visualizzato il pulsante **Seleziona sottoscrizioni** (icona filtro). Selezionare l'icona del filtro.

      ![Screenshot che mostra il riquadro di Azure e l'icona del filtro selezionato.](./media/create-stateful-stateless-workflows-visual-studio-code/filter-subscription-list.png)

      In alternativa, nella barra di stato Visual Studio Code selezionare l'account Azure. 

   1. Quando viene visualizzato un elenco di sottoscrizioni, selezionare le sottoscrizioni desiderate e quindi assicurarsi di selezionare **OK**.

<a name="create-project"></a>

## <a name="create-a-local-project"></a>Creazione di un progetto locale

Prima di poter creare l'app per la logica, creare un progetto locale in modo da poter gestire e distribuire l'app per la logica da Visual Studio Code. Il progetto sottostante è simile a un progetto di funzioni di Azure, noto anche come progetto di app per le funzioni. Tuttavia, questi tipi di progetto sono separati l'uno dall'altro, quindi i flussi di lavoro e le funzioni dell'app per la logica non possono esistere nello stesso progetto.

1. Nel computer creare una cartella locale *vuota* da usare per il progetto che verrà creato in un secondo momento in Visual Studio Code.

1. In Visual Studio Code chiudere tutte le cartelle aperte.

1. Nel riquadro di Azure, accanto ad **Azure: app per la logica (anteprima)**, selezionare **Crea nuovo progetto** (icona che mostra una cartella e un fulmine).

   ![Screenshot che mostra la barra degli strumenti del riquadro di Azure con l'opzione "Crea nuovo progetto" selezionata.](./media/create-stateful-stateless-workflows-visual-studio-code/create-new-project-folder.png)

1. Se Windows Defender Firewall richiede di concedere l'accesso alla rete per `Code.exe` , che è Visual Studio Code e per `func.exe` , che è il Azure Functions Core Tools, selezionare **reti private, ad esempio la rete domestica o di lavoro** **>** **Consenti accesso**.

1. Passare al percorso in cui è stata creata la cartella del progetto, selezionare la cartella e continuare.

   ![Screenshot che mostra la finestra di dialogo "Seleziona cartella" con una cartella di progetto appena creata e il pulsante "Seleziona" selezionato.](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-folder.png)

1. Dall'elenco dei modelli visualizzato, selezionare flusso di **lavoro con stato** o **flusso di lavoro**senza stato. Questo esempio Mostra come selezionare un **flusso di lavoro con stato**.

   ![Screenshot che mostra l'elenco dei modelli di flusso di lavoro con "flusso di lavoro con stato" selezionato.](./media/create-stateful-stateless-workflows-visual-studio-code/select-stateful-stateless-workflow.png)

1. Specificare un nome per il flusso di lavoro dell'app per la logica e premere INVIO. In questo esempio viene usato `example-workflow` come nome.

   ![Screenshot che mostra la casella "crea un nuovo flusso di lavoro con stato (3/4)" e "esempio-flusso di lavoro" come nome del flusso di lavoro.](./media/create-stateful-stateless-workflows-visual-studio-code/name-your-workflow.png)

1. Dall'elenco successivo visualizzato, selezionare **Apri nella finestra corrente**.

   ![Screenshot che mostra l'elenco con l'opzione "Apri nella finestra corrente" selezionata.](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-location.png)

   Visual Studio Code ricarica, apre il riquadro di esplorazione e Mostra il progetto, che ora include i file di progetto generati automaticamente. Ad esempio, il progetto include una cartella che mostra il nome del flusso di lavoro dell'app per la logica. All'interno di questa cartella, il **workflow.jsnel** file contiene la definizione JSON sottostante del flusso di lavoro dell'app per la logica.

   ![Screenshot che mostra la finestra di esplorazione con la cartella del progetto, la cartella del flusso di lavoro e il file "workflow.json".](./media/create-stateful-stateless-workflows-visual-studio-code/local-project-created.png)

<a name="open-workflow-definition-designer"></a>

## <a name="open-the-workflow-definition-file-in-logic-app-designer"></a>Aprire il file di definizione del flusso di lavoro in progettazione app per la logica

1. Controllare le versioni installate nel computer eseguendo il comando seguente:

   `..\Users\{yourUserName}\dotnet --list-sdks`

   Se si dispone di .NET Core SDK 5. x, questa versione potrebbe impedire l'apertura della definizione del flusso di lavoro sottostante dell'app per la logica nella finestra di progettazione. Anziché disinstallare questa versione, nel percorso radice del progetto creare un **global.jssu** un file che faccia riferimento alla versione 3. x del runtime di .NET Core che è successiva a 3.1.201, ad esempio:

   ```json
   {
      "sdk": {
         "version": "3.1.8",
         "rollForward": "disable"
      }
   }
   ```

   Assicurarsi di expliclitly aggiungere il **global.jsnel** file al progetto nel percorso radice dall'interno Visual Studio Code. In caso contrario, la finestra di progettazione non verrà aperta.

1. Se Visual Studio Code è in esecuzione in Windows o Linux, assicurarsi che l'emulatore di archiviazione di Azure sia in esecuzione. Per ulteriori informazioni, esaminare i [prerequisiti](#prerequisites).

1. Espandere la cartella del progetto per il flusso di lavoro. Aprire il **workflow.js** dal menu di scelta rapida del file e selezionare **Apri in finestra di progettazione**.

   ![Screenshot che mostra il riquadro di esplorazione e la finestra di scelta rapida per il workflow.jsnel file con l'opzione "Apri in progettazione" selezionata.](./media/create-stateful-stateless-workflows-visual-studio-code/open-definition-file-in-designer.png)

   Se viene ricevuto il messaggio di errore `Workflow design time could not be started` , assicurarsi che l'emulatore di archiviazione di Azure sia in esecuzione. In caso contrario, provare gli altri suggerimenti per la risoluzione dei problemi:

   In Visual Studio Code controllare l'output dall'estensione anteprima.

   1. Scegliere **output**dal menu **Visualizza** .

   1. Dall'elenco sulla barra del titolo dell' **output** selezionare app per la **logica di Azure** in modo che sia possibile visualizzare l'output dell'estensione di anteprima, ad esempio:

      ![Screenshot che mostra la finestra di output di Visual Studio Code con "app per la logica di Azure" selezionata.](./media/create-stateful-stateless-workflows-visual-studio-code/check-outout-window-azure-logic-apps.png)

   1. Esaminare l'output e verificare se viene visualizzato il messaggio di errore seguente:

      ```text
      A host error has occurred during startup operation '{operationID}'.
      System.Private.CoreLib: The file 'C:\Users\{userName}\AppData\Local\Temp\Functions\
      ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows\1.1.1\bin\
      DurableTask.AzureStorage.dll' already exists.
      Value cannot be null. (Parameter 'provider')
      Application is shutting down...
      Initialization cancellation requested by runtime.
      Stopping host...
      Host shutdown completed.
      ```

      Questo errore può verificarsi se in precedenza si è tentato di aprire la finestra di progettazione e quindi il progetto è stato interrotto o eliminato. Per correggere l'errore, eliminare la cartella **ExtensionBundles** in questo percorso **. ..\Users \\ {your-username} \AppData\Local\Temp\Functions\ExtensionBundles**e riprovare ad aprire il **workflow.js** nel file nella finestra di progettazione.

1. Dall'elenco **Abilita connettori in Azure** selezionare **usa connettori di Azure**, che si applica a tutti i connettori gestiti disponibili nell'portale di Azure e non solo ai connettori per i servizi di Azure.

   ![Screenshot che mostra il riquadro di esplorazione con l'elenco "Abilita connettori in Azure" aperto e "usa connettori da Azure" selezionato.](./media/create-stateful-stateless-workflows-visual-studio-code/use-connectors-from-azure.png)

1. Dall'elenco gruppi di risorse selezionare **Crea nuovo gruppo di risorse**.

   ![Screenshot che mostra il riquadro di esplorazione con i gruppi di risorse elenco e il "Crea nuovo gruppo di risorse" selezionato](./media/create-stateful-stateless-workflows-visual-studio-code/create-select-resource-group.png)

1. Specificare un nome per il gruppo di risorse e premere INVIO. In questo esempio viene utilizzato `example-logic-app-preview-rg`.

   ![Screenshot che mostra il riquadro di esplorazione e il nome del gruppo di risorse.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-name-for-resource-group.png)

1. Dall'elenco percorsi trovare e selezionare un'area di [Azure supportata](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions) da usare per creare il gruppo di risorse e le risorse. Questo esempio usa **Stati Uniti centro-occidentali**.

   > [!IMPORTANT]
   > Non tutte le aree sono attualmente supportate, ma gli aggiornamenti per aggiungere altre aree sono in corso. La selezione di un'area non supportata potrebbe causare problemi, ad esempio la creazione di connessioni. Per le aree attualmente supportate, vedere la [pagina di GitHub problemi noti](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions)dell'estensione di anteprima.

   ![Screenshot che mostra il riquadro di esplorazione con l'elenco delle posizioni e la sezione "Stati Uniti centro-occidentali" selezionata.](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-region.png)

   Dopo aver eseguito questo passaggio, Visual Studio Code apre la finestra di progettazione dell'app per la logica.

   > [!NOTE]
   > Quando Visual Studio Code avvia l'API della fase di progettazione del flusso di lavoro, viene visualizzato un messaggio che l'avvio potrebbe richiedere alcuni secondi. È possibile ignorare questo messaggio oppure fare clic su **OK**.

   Quando viene visualizzata la finestra di progettazione dell'app per la logica, il prompt **scegliere un'operazione** viene visualizzato nella finestra di progettazione ed è selezionato per impostazione predefinita, che mostra il riquadro **Aggiungi un'azione** .

   ![Screenshot che mostra progettazione app per la logica.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-designer.png)

1. Aggiungere quindi [un trigger e le azioni](#add-trigger-actions) al flusso di lavoro.

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-actions"></a>Aggiungere un trigger e le azioni

Dopo aver aperto la finestra di progettazione dell'app per la logica dal menu di scelta rapida del **workflow.jsnel** menu di scelta rapida del file, il prompt **scegliere un'operazione** viene visualizzato nella finestra di progettazione ed è selezionato per impostazione predefinita È ora possibile iniziare a creare il flusso di lavoro aggiungendo un trigger e le azioni.

Il flusso di lavoro dell'app per la logica in questo esempio usa questo trigger e le azioni seguenti:

* [Trigger di richiesta](../connectors/connectors-native-reqres.md)incorporato, **quando viene ricevuta una richiesta http**, che riceve le chiamate in ingresso o le richieste e crea un endpoint che può essere chiamato da altri servizi o app per la logica.

* L' [azione Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), **Invia un messaggio di posta elettronica**.

* [Azione di risposta](../connectors/connectors-native-reqres.md)predefinita, usata per inviare una risposta e restituire i dati al chiamante.

### <a name="add-the-request-trigger"></a>Aggiungere il trigger Request

1. Accanto alla finestra di progettazione, nel riquadro **Aggiungi un trigger** , nella casella di ricerca **scegliere un'operazione** verificare che sia selezionata l'opzione **predefinito** , in modo che sia possibile selezionare un trigger che viene eseguito in modalità nativa.

1. Nella casella di ricerca **scegliere un'operazione** immettere `when a http request` e selezionare il trigger di richiesta incorporato denominato **quando viene ricevuta una richiesta http**.

   ![Screenshot che mostra la finestra di progettazione dell'app per la logica e il riquadro * * Aggiungi un trigger * * con il trigger "quando viene ricevuta una richiesta HTTP" selezionato.](./media/create-stateful-stateless-workflows-visual-studio-code/add-request-trigger.png)

   Quando il trigger viene visualizzato nella finestra di progettazione, il riquadro dei dettagli del trigger si apre per visualizzare le proprietà, le impostazioni e altre azioni del trigger.

   ![Screenshot che mostra la finestra di progettazione dell'app per la logica con il trigger "quando viene ricevuta una richiesta HTTP" e il riquadro dei dettagli del trigger è aperto.](./media/create-stateful-stateless-workflows-visual-studio-code/request-trigger-added-to-designer.png)

   > [!TIP]
   > Se il riquadro dei dettagli non viene visualizzato, verifica che il trigger sia selezionato nella finestra di progettazione.

1. Se è necessario eliminare un elemento nella finestra di progettazione, attenersi alla procedura seguente:

   1. Nella finestra di progettazione selezionare l'elemento, che consente di aprire il riquadro dettagli dell'elemento sul lato destro.

   1. Espandere la finestra di Visual Studio Code sufficientemente ampia in modo che accanto al trigger o al nome dell'azione, il pulsante con i puntini di sospensione (**...**) venga visualizzato nell'angolo in alto a destra. 

   1. Aprire il menu con i puntini di sospensione (**..**.) e selezionare **Elimina**. Per confermare l'eliminazione, fare clic su **OK**.

      ![Screenshot che mostra l'elemento selezionato nella finestra di progettazione con il riquadro dettagli aperto e con il pulsante con i puntini di sospensione selezionati e l'opzione "Elimina".](./media/create-stateful-stateless-workflows-visual-studio-code/delete-item-from-designer.png)

### <a name="add-the-office-365-outlook-action"></a>Aggiungere l'azione Office 365 Outlook

1. Nella finestra di progettazione, sotto il trigger aggiunto, selezionare **nuovo passaggio**.

   Il prompt **scegliere un'operazione** viene visualizzato nella finestra di progettazione e il **riquadro Aggiungi un'azione** viene riaperto in modo che sia possibile selezionare l'azione successiva.

1. Nella casella di ricerca **scegliere un'operazione** del riquadro **Aggiungi un'azione** selezionare **Azure** per poter trovare e selezionare un'azione per un connettore gestito distribuito in Azure.

   Questo esempio seleziona e usa l'azione Office 365 Outlook, **Invia un messaggio di posta elettronica (v2)**.

   ![Screenshot che mostra la finestra di progettazione dell'app per la logica e il riquadro * * Aggiungi un'azione * * con l'azione "Invia un messaggio di posta elettronica" di Office 365 Outlook.](./media/create-stateful-stateless-workflows-visual-studio-code/add-send-email-action.png)

1. Nel riquadro dei dettagli dell'azione selezionare **Accedi** per poter creare una connessione all'account di posta elettronica.

   ![Screenshot che mostra la finestra di progettazione dell'app per la logica e il riquadro * * Invia un messaggio di posta elettronica (v2) * * con l'opzione "Accedi".](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-sign-in.png)

   > [!NOTE]
   > Se si riceve l'errore, `Failed to create connection...` potrebbe essere stata scelta un'area attualmente non supportata per l'app per la logica. Sono in corso aggiornamenti per aggiungere altre aree. Nel frattempo, per le aree attualmente supportate, vedere la pagina di [GitHub problemi noti](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md#available-regions)dell'estensione di anteprima.

1. Quando Visual Studio Code richiede il consenso per l'accesso all'account di posta elettronica, selezionare **Apri**.

   ![Screenshot che mostra la richiesta di Visual Studio Code per consentire l'accesso.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-open-external-website.png)

   > [!TIP]
   > Per evitare richieste future, selezionare **Configura domini trusted** in modo che sia possibile aggiungere la pagina di autenticazione come dominio attendibile.

1. Seguire i prompt successivi per accedere, consentire l'accesso e consentire il ritorno a Visual Studio Code.

   > [!NOTE]
   > Se passano troppo tempo prima di completare le richieste, il processo di autenticazione si interrompe e non riesce. In questo caso, tornare alla finestra di progettazione e riprovare ad accedere per creare la connessione.

1. Quando l'estensione di anteprima di app per la logica di Azure richiede il consenso per l'accesso all'account di posta elettronica, selezionare **Apri**. Seguire la richiesta successiva per consentire l'accesso.

   ![Screenshot che mostra la richiesta di estensione di anteprima per consentire l'accesso.](./media/create-stateful-stateless-workflows-visual-studio-code/allow-preview-extension-open-uri.png)

   > [!TIP]
   > Per evitare richieste future, selezionare **non visualizzare più questa estensione**.

   Dopo che Visual Studio Code crea la connessione, alcuni connettori mostrano il messaggio che `The connection will be valid for {n} days only.` questo limite di tempo si applica solo alla durata durante la creazione dell'app per la logica nel Visual Studio Code. Dopo la distribuzione, questo limite non si applica più perché l'app per la logica è in grado di eseguire l'autenticazione in fase di esecuzione usando l' [identità gestita assegnata dal sistema](../logic-apps/create-managed-service-identity.md)abilitata automaticamente. Questa identità gestita è diversa dalle credenziali di autenticazione o dalla stringa di connessione utilizzata durante la creazione di una connessione. Se si disabilita questa identità gestita assegnata dal sistema, le connessioni non funzioneranno in fase di esecuzione.

1. Nella finestra di progettazione, se l'azione **Invia un messaggio di posta elettronica** non è selezionata, selezionare l'azione.

1. Nel riquadro dei dettagli dell'azione, nella scheda **parametri** , fornire le informazioni necessarie per l'azione, ad esempio:

   ![Screenshot che mostra la finestra di progettazione 365 dell'app per la logica con i dettagli per l'azione "Invia un messaggio di posta elettronica" di Outlook](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-details.png)

   | Proprietà | Obbligatoria | valore | Descrizione |
   |----------|----------|-------|-------------|
   | **To** | Sì | <*indirizzo-posta-elettronica*> | Destinatario del messaggio di posta elettronica, che può essere l'indirizzo di posta elettronica a scopo di test. Questo esempio usa il messaggio di posta elettronica fittizio, `sophiaowen@fabrikam.com` . |
   | **Oggetto** | Sì | `An email from your example workflow` | L'oggetto del messaggio di posta elettronica |
   | **Corpo** | Sì | `Hello from your example workflow!` | Contenuto del corpo del messaggio di posta elettronica |
   ||||

   > [!NOTE]
   > Se si desidera apportare modifiche nel riquadro dei dettagli della scheda **Impostazioni**, **Esegui dopo**o **risultato statico** , assicurarsi di selezionare **completato** per confermare le modifiche prima di passare a schede o di spostare lo stato attivo sulla finestra di progettazione. In caso contrario, Visual Studio Code non manterrà le modifiche. Per altre informazioni, vedere la pagina di GitHub relativa ai [problemi noti](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)dell'estensione di anteprima.

1. Nella finestra di progettazione selezionare **Salva**.

Successivamente, eseguire ed eseguire il debug del flusso di lavoro localmente in Visual Studio Code.

<a name="debug-test-locally"></a>

## <a name="run-and-debug-locally"></a>Eseguire ed eseguire il debug in locale

Per testare l'app per la logica, seguire questa procedura per avviare una sessione di debug e trovare l'URL per l'endpoint creato dal trigger di richiesta. Questo URL è necessario per poter inviare una richiesta a tale endpoint in un secondo momento.

1. Per semplificare il debug di un flusso di lavoro di un'app per la logica senza stato, è possibile [abilitare la cronologia di esecuzione per tale flusso di lavoro](#run-history).

1. Sulla barra degli strumenti Visual Studio Code aprire il menu **Run (Esegui** ) e selezionare **Avvia debug** (F5).

   Viene visualizzata la finestra del **terminale** in cui è possibile esaminare la sessione di debug.

1. A questo punto, trovare l'URL di callback per l'endpoint nel trigger della richiesta.

   1. Riaprire il riquadro di esplorazione per poter visualizzare il progetto.

   1. Scegliere **Panoramica**dal menu di scelta rapida del file di **workflow.js** .

      ![Screenshot che mostra il riquadro di esplorazione e la finestra di scelta rapida per il workflow.jssu file con "Overview" selezionato.](./media/create-stateful-stateless-workflows-visual-studio-code/open-workflow-overview.png)

   1. Trovare il valore di **URL callback** , simile a questo URL per il trigger di richiesta di esempio:

      `http://localhost:7071/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

      ![Screenshot che mostra la pagina Panoramica del flusso di lavoro con l'URL callback](./media/create-stateful-stateless-workflows-visual-studio-code/find-callback-url.png)

1. Per testare l'URL di callback attivando il flusso di lavoro dell'app per la logica, aprire il [post](https://www.postman.com/downloads/) o lo strumento preferito per la creazione e l'invio di richieste.

   Questo esempio continua con l'uso di postazione. Per ulteriori informazioni, vedere la pagina relativa all' [Introduzione dei post](https://learning.postman.com/docs/getting-started/introduction/).

   1. Sulla barra degli strumenti del posto, selezionare **nuovo**.

      ![Screenshot che mostra il pulsante con il pulsante nuovo selezionato](./media/create-stateful-stateless-workflows-visual-studio-code/postman-create-request.png)

   1. Nel riquadro **Crea nuovo** , in **blocchi predefiniti**, selezionare **richiesta**.

   1. Nella finestra **Salva richiesta** , in **nome richiesta**, specificare un nome per la richiesta, ad esempio `Test workflow trigger` .

   1. In **selezionare una raccolta o una cartella in cui salvare**, selezionare **Crea raccolta**.

   1. In **tutte le raccolte**specificare un nome per la raccolta da creare per organizzare le richieste, premere invio e selezionare **Salva per <*raccolta-nome* > **. In questo esempio viene usato `Logic Apps requests` come nome della raccolta.

      Viene visualizzato il riquadro delle richieste del post, in modo che sia possibile inviare una richiesta all'URL di callback per il trigger di richiesta.

      ![Screenshot che mostra il posto con il riquadro delle richieste aperto](./media/create-stateful-stateless-workflows-visual-studio-code/postman-request-pane.png)

   1. Tornare a Visual Studio Code. dalla pagina Panoramica del flusso di lavoro, copiare il valore della proprietà **URL callback** .

   1. Tornare a postazione. Nel riquadro delle richieste, accanto all'elenco dei metodi, che attualmente Mostra **Get** come metodo di richiesta predefinito, incollare l'URL di callback copiato in precedenza nella casella Address e selezionare **Send (Invia**).

      ![Screenshot che mostra l'URL dei post e callback nella casella indirizzo con il pulsante Invia selezionato](./media/create-stateful-stateless-workflows-visual-studio-code/postman-test-call-back-url.png)

      Il flusso di lavoro dell'app per la logica di esempio Invia un messaggio di posta elettronica simile all'esempio seguente:

      ![Screenshot che mostra la posta elettronica di Outlook come descritto nell'esempio](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-result-email.png)

1. In Visual Studio Code tornare alla pagina Panoramica del flusso di lavoro.

   Se è stato creato un flusso di lavoro con stato, dopo la richiesta inviata viene attivato il flusso di lavoro, nella pagina panoramica vengono visualizzati lo stato e la cronologia dell'esecuzione del flusso di lavoro. Per altre informazioni sugli stati di esecuzione, vedere [esaminare la cronologia delle esecuzioni](../logic-apps/monitor-logic-apps.md#review-runs-history).

   ![Screenshot che mostra la pagina Panoramica del flusso di lavoro con lo stato e la cronologia di esecuzione](./media/create-stateful-stateless-workflows-visual-studio-code/post-trigger-call.png)

   > [!TIP]
   > Se lo stato dell'esecuzione non viene visualizzato, provare ad aggiornare la pagina Panoramica selezionando **Aggiorna**.

1. Per esaminare gli Stati per ogni passaggio in un'esecuzione specifica e gli input e gli output del passaggio, selezionare il pulsante con i puntini di sospensione (**..**.) per l'esecuzione e selezionare **Mostra esecuzione**.

   ![Screenshot che mostra la riga della cronologia di esecuzione del flusso di lavoro con i puntini di sospensione e la selezione dell'opzione "Mostra esecuzione"](./media/create-stateful-stateless-workflows-visual-studio-code/show-run-history.png)

   Visual Studio Code Mostra gli Stati di esecuzione di ogni azione.

1. Per esaminare gli input e gli output per ogni passaggio, espandere il passaggio che si desidera controllare. Per esaminare ulteriormente gli input e gli output non elaborati per tale passaggio, selezionare **Mostra input non elaborati** o **Mostra output**non elaborati.

   ![Screenshot che mostra lo stato di ogni passaggio nel flusso di lavoro più gli input e gli output nell'azione "Invia un messaggio di posta elettronica" espanso](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details.png)

1. Per arrestare la sessione di debug, scegliere **Interrompi debug** (MAIUSC + F5) dal menu **Esegui** .

<a name="return-response"></a>

## <a name="return-a-response-to-the-caller"></a>Restituisce una risposta al chiamante

Per restituire una risposta al chiamante che ha inviato una richiesta all'app per la logica, è possibile usare l' [azione di risposta](../connectors/connectors-native-reqres.md) predefinita per un flusso di lavoro che inizia con il trigger di richiesta.

1. Nella finestra di progettazione dell'app per la logica, sotto l'azione **Invia un messaggio di posta elettronica** , selezionare **nuovo passaggio**.

   Il prompt **scegliere un'operazione** viene visualizzato nella finestra di progettazione e il **riquadro Aggiungi un'azione** viene riaperto in modo che sia possibile selezionare l'azione successiva.

1. Nella casella di ricerca **scegliere un'azione** del riquadro **Aggiungi un'azione** verificare che sia selezionata l'opzione **predefinita** . Nella casella di ricerca immettere `response` e selezionare l'azione **risposta** .

   ![Screenshot che mostra la finestra di progettazione dell'app per la logica con l'azione di risposta selezionata.](./media/create-stateful-stateless-workflows-visual-studio-code/add-response-action.png)

   Quando l'azione **risposta** viene visualizzata nella finestra di progettazione, viene automaticamente aperto il riquadro dettagli dell'azione.

   ![Screenshot che mostra la finestra di progettazione di app per la logica con il riquadro dei dettagli dell'azione "risposta" aperta e la proprietà "Body" impostata sul valore della proprietà "Body" dell'azione "Invia un messaggio di posta elettronica".](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details.png)

1. Nella scheda **parametri** fornire le informazioni necessarie per la funzione che si desidera chiamare.

   Questo esempio restituisce il valore della proprietà **Body** che è l'output dell'azione **Invia un messaggio di posta elettronica** .

   1. Fare clic all'interno della casella proprietà **corpo** per visualizzare l'elenco di contenuto dinamico e visualizzare i valori di output disponibili dal trigger precedente e le azioni nel flusso di lavoro.

      ![Screenshot che mostra il riquadro dei dettagli dell'azione "risposta" con il puntatore del mouse all'interno della proprietà "Body", in modo che venga visualizzato l'elenco di contenuto dinamico.](./media/create-stateful-stateless-workflows-visual-studio-code/open-dynamic-content-list.png)

   1. Nell'elenco contenuto dinamico, in **Invia un messaggio di posta elettronica**, selezionare **corpo**.

      ![Screenshot che mostra l'elenco di contenuto dinamico aperto. Nell'elenco, sotto l'intestazione "Invia un messaggio di posta elettronica", viene selezionato il valore di output "Body".](./media/create-stateful-stateless-workflows-visual-studio-code/select-send-email-action-body-output-value.png)

      Al termine, la proprietà **Body** dell'azione di risposta è ora impostata sul valore di output del **corpo** dell'azione **Invia un messaggio di posta elettronica** .

      ![Screenshot che mostra lo stato di ogni passaggio nel flusso di lavoro più gli input e gli output nell'azione di risposta espansa.](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details-body-property.png)

1. Nella finestra di progettazione selezionare **Salva**.

<a name="retest-workflow"></a>

## <a name="retest-your-logic-app"></a>Eseguire nuovamente il test dell'app per la logica

Dopo aver aggiornato l'app per la logica, è possibile eseguire un altro test eseguendo nuovamente il debugger in Visual Studio e inviando un'altra richiesta di attivazione dell'app per la logica aggiornata, in modo analogo ai passaggi descritti in [eseguire il debug e testare l'app per la logica](#debug-test-locally).

1. Sulla barra degli strumenti Visual Studio Code aprire il menu **Run (Esegui** ) e selezionare **Avvia debug** (F5).

1. Nel post o nello strumento per la creazione e l'invio di richieste inviare un'altra richiesta di attivazione del flusso di lavoro.

1. Se è stato creato un flusso di lavoro con stato, nella pagina Panoramica del flusso di lavoro controllare lo stato dell'esecuzione più recente. Per visualizzare lo stato, gli input e gli output per ogni passaggio di tale esecuzione, selezionare il pulsante con i puntini di sospensione (**.**..) per l'esecuzione e selezionare **Mostra esecuzione**.

   Ad esempio, di seguito è riportato lo stato dettagliato di un'esecuzione dopo che il flusso di lavoro di esempio è stato aggiornato con l'azione di risposta.

   ![Screenshot che mostra lo stato di ogni passaggio nel flusso di lavoro aggiornato più gli input e gli output nell'azione di risposta espansa.](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details-rerun.png)

1. Per arrestare la sessione di debug, scegliere **Interrompi debug** (MAIUSC + F5) dal menu **Esegui** .

<a name="publish-azure"></a>

## <a name="publish-to-azure"></a>Pubblicazione in Azure

Da Visual Studio Code è possibile distribuire il progetto direttamente in Azure, che pubblica l'app per la logica usando il nuovo tipo di risorsa app per la **logica (anteprima)** . Analogamente alla risorsa app per le funzioni in funzioni di Azure, la distribuzione per questo nuovo tipo di risorsa richiede la selezione di un [piano di hosting e di un piano tariffario](../app-service/overview-hosting-plans.md)che è possibile configurare durante la distribuzione. Per ulteriori informazioni sui piani di hosting e i prezzi, vedere gli argomenti seguenti:

* [Scalabilità verticale di un servizio app Azure](../app-service/manage-scale-up.md)
* [Ridimensionamento e hosting di Funzioni di Azure](../azure-functions/functions-scale.md)

È possibile pubblicare l'app per la logica come nuova risorsa, che crea automaticamente eventuali risorse aggiuntive necessarie, ad esempio un [account di archiviazione di Azure, in modo analogo ai requisiti dell'app per le funzioni](../azure-functions/storage-considerations.md). In alternativa, è possibile pubblicare l'app per la logica in una risorsa di app per la **logica (anteprima)** distribuita in precedenza, che sovrascrive tale app per la logica.

### <a name="publish-as-a-new-logic-app-preview-resource"></a>Pubblicare come nuova risorsa app per la logica (anteprima)

1. Sulla barra degli strumenti di Visual Studio Code selezionare l'icona di Azure.

1. Nella barra degli strumenti **Azure: app per la logica (anteprima)** selezionare **Distribuisci in app per la logica**.

   ![Screenshot che mostra il riquadro "Azure: app per la logica (anteprima)" e la barra degli strumenti del riquadro con "Distribuisci in app per la logica" selezionati.](./media/create-stateful-stateless-workflows-visual-studio-code/deploy-to-logic-app.png)

1. Dall'elenco Visual Studio Code si apre, selezionare una delle opzioni seguenti:

   * **Creare una nuova app per la logica (anteprima) in Azure** (Quick)
   * **Creare una nuova app per la logica (anteprima) in Azure Advanced**
   * Una risorsa dell' **app per la logica (anteprima)** distribuita in precedenza, se presente

   Questo esempio continua con **Crea nuova app per la logica (anteprima) in Azure Advanced**.

   ![Screenshot che mostra il riquadro "Azure: app per la logica (anteprima)" con un elenco con "Crea nuova app per la logica (anteprima) in Azure" selezionato.](./media/create-stateful-stateless-workflows-visual-studio-code/select-create-logic-app-options.png)

1. Per creare la nuova risorsa app per la **logica (anteprima)** , seguire questa procedura:

   1. Fornire un nome univoco globale per la nuova app per la logica, che è il nome da usare per la risorsa app per la **logica (anteprima)** . In questo esempio viene utilizzato `example-logic-app-preview`.

      ![Screenshot che mostra il riquadro "Azure: app per la logica (anteprima)" e un prompt per fornire un nome per la nuova app per la logica da creare.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-logic-app-name.png)

   1. Selezionare un piano di hosting per la nuova app per la logica, ovvero [**piano di servizio app**](../azure-functions/functions-scale.md#app-service-plan) o [**Premium**](../azure-functions/functions-scale.md#premium-plan). Questo esempio Mostra come selezionare il **piano di servizio app**.

      ![Screenshot che mostra il riquadro "Azure: app per la logica (anteprima)" e un prompt per selezionare "piano di servizio app" o "Premium".](./media/create-stateful-stateless-workflows-visual-studio-code/select-hosting-plan.png)

   1. Creare un nuovo piano di servizio app o selezionare un piano esistente. Questo esempio Mostra come selezionare **Crea nuovo piano di servizio app**.

      ![Screenshot che mostra il riquadro "Azure: app per la logica (anteprima)" e un prompt per "creare un nuovo piano di servizio app" o selezionare un piano di servizio app esistente.](./media/create-stateful-stateless-workflows-visual-studio-code/create-app-service-plan.png)

   1. Specificare un nome per il piano di servizio app e quindi selezionare un piano [tariffario](../app-service/overview-hosting-plans.md) per il piano. Questo esempio Mostra come selezionare il piano **F1 gratuito** .

      ![Screenshot che mostra il riquadro "Azure: app per la logica (anteprima)" e un prompt per selezionare un piano tariffario.](./media/create-stateful-stateless-workflows-visual-studio-code/select-pricing-tier.png)

   1. Per prestazioni ottimali, trovare e selezionare lo stesso gruppo di risorse del progetto per la distribuzione.

      > [!NOTE]
      > Sebbene sia possibile creare o usare un gruppo di risorse diverso, questa operazione potrebbe influire sulle prestazioni. Se si crea o si sceglie un gruppo di risorse diverso, ma si annulla dopo che viene visualizzata la richiesta di conferma, viene annullata anche la distribuzione.

   1. Per i flussi di lavoro con stato selezionare **Crea nuovo account di archiviazione** o un account di archiviazione esistente.

      ![Screenshot che mostra il riquadro "Azure: app per la logica (anteprima)" e un prompt per creare o selezionare un account di archiviazione.](./media/create-stateful-stateless-workflows-visual-studio-code/create-storage-account.png)

   1. Per semplificare la registrazione di diagnostica e la funzionalità di traccia, è possibile selezionare una risorsa Application Insights esistente. In caso contrario, è possibile selezionare **Crea nuova risorsa Application Insights**o configurare Application Insights nel portale di Azure dopo aver distribuito l'app.

      Prima di distribuire, assicurarsi di aggiungere l' `logLevel` oggetto all' `logging` oggetto nel **host.js** file esistente a livello di radice del progetto e impostare `Host.Triggers.Workflow` su `Information` , ad esempio:

      ```json
      "logLevel": {
         "Host.Triggers.Workflow": "Information"
      },
      ```

      Di seguito è illustrato il modo in cui il **host.jssu** file potrebbe apparire:

      ```json
      {
         "version": "2.0",
         "logging": {
            "applicationInsights": {
               "samplingExcludedTypes": "Request",
               "samplingSettings": {
                  "isEnabled": true
               }
            },
            "logLevel": {
               "Host.Triggers.Workflow": "Information"
            }
         }
      }
      ``` 

   Al termine, Visual Studio Code avvia la creazione e la distribuzione delle risorse necessarie per la pubblicazione dell'app per la logica.

1. Per esaminare e monitorare il processo di distribuzione, scegliere **output**dal menu **Visualizza** . Dall'elenco della barra degli strumenti della finestra di output selezionare app per la **logica di Azure**.

   ![Screenshot che mostra la finestra di output con "app per la logica di Azure" selezionata nell'elenco della barra degli strumenti con l'avanzamento e lo stato della distribuzione.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-deployment-output-window.png)

   Quando Visual Studio Code termina la distribuzione del flusso di lavoro dell'app per la logica in Azure, viene visualizzato il messaggio seguente:

   ![Screenshot che mostra un messaggio che indica che la distribuzione in Azure è stata completata correttamente.](./media/create-stateful-stateless-workflows-visual-studio-code/deployment-to-azure-completed.png)

   Congratulazioni, l'app per la logica è ora disponibile in Azure e abilitata per impostazione predefinita.

Successivamente, è possibile apprendere come eseguire queste attività:

* [Trovare l'app per la logica distribuita nell'portale di Azure](#find-manage-deployed-workflows-portal) o [in Visual Studio Code](#find-manage-deployed-workflows-vs-code).

* [Abilitare la cronologia di esecuzione in flussi di lavoro di app per la logica](#run-history)senza stato.

* [Abilitare il monitoraggio in una risorsa app per la logica distribuita **(anteprima)** ](#enable-monitoring).

<a name="find-manage-deployed-workflows-vs-code"></a>

## <a name="find-and-manage-deployed-logic-apps-in-visual-studio-code"></a>Trovare e gestire app per la logica distribuite in Visual Studio Code

In Visual Studio Code, è possibile visualizzare tutte le app per la logica distribuite nella sottoscrizione di Azure, sia che si tratti **delle app per la logica originali che** del tipo di risorsa app per la **logica (anteprima)** e selezionare le attività che consentono di gestire le app per la logica. Tuttavia, per accedere a entrambi i tipi di risorse, sono necessarie le estensioni delle app per la **logica di Azure** e delle app per la **logica di Azure (anteprima)** per Visual Studio Code.

1. Sulla barra degli strumenti a sinistra selezionare l'icona Azure. Nel riquadro **Azure: app per la logica (anteprima)** espandere la sottoscrizione, che Mostra tutte le app per la logica distribuite per tale sottoscrizione.

1. Trovare e selezionare l'app per la logica che si vuole gestire. Aprire il menu di scelta rapida dell'app per la logica e selezionare l'attività che si desidera eseguire.

   Ad esempio, è possibile selezionare attività come l'arresto, l'avvio, il riavvio o l'eliminazione dell'app per la logica distribuita.

   ![Screenshot che mostra Visual Studio Code con il riquadro dell'estensione "app per la logica di Azure (anteprima)" aperta e il flusso di lavoro distribuito.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-visual-studio-code.png)

1. Per visualizzare tutti i flussi di lavoro nell'app per la logica, espandere l'app per la logica, quindi espandere il nodo **flussi di lavoro** .

1. Per visualizzare un flusso di lavoro specifico, aprire il menu di scelta rapida del flusso di lavoro e selezionare **Apri in finestra di progettazione**per aprire il flusso di lavoro in modalità di sola lettura.

   Per modificare il flusso di lavoro, sono disponibili le opzioni seguenti:

   * In Visual Studio Code aprire il **workflow.js** del progetto in un file nella finestra di progettazione dell'app per la logica, apportare le modifiche e ridistribuire l'app per la logica in Azure.

   * Nella portale di Azure [individuare e aprire l'app per la logica](#find-manage-deployed-workflows-portal). Trovare, modificare e salvare il flusso di lavoro.

1. Per aprire l'app per la logica distribuita nel portale di Azure, aprire il menu di scelta rapida dell'app per la logica e selezionare **Apri nel portale**.

   Il portale di Azure si apre nel browser, accede automaticamente al portale se è stato eseguito l'accesso a Visual Studio Code e viene visualizzata l'app per la logica.

   ![Screenshot che mostra la pagina portale di Azure per l'app per la logica in Visual Studio Code.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

   È anche possibile accedere separatamente alla portale di Azure, usare la casella di ricerca del portale per trovare l'app per la logica e quindi selezionare l'app per la logica dall'elenco dei risultati.

   ![Screenshot che mostra il portale di Azure e la barra di ricerca con i risultati della ricerca per l'app per la logica distribuita, visualizzata come selezionata.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-azure-portal.png)

<a name="find-manage-deployed-workflows-portal"></a>

## <a name="find-and-manage-deployed-logic-apps-in-the-portal"></a>Trovare e gestire app per la logica distribuite nel portale

Nella portale di Azure è possibile visualizzare tutte le app per la logica distribuite nella sottoscrizione di Azure, sia il tipo di **risorsa app per la logica originale che** il tipo di risorsa app per la **logica (anteprima)** . Attualmente, ogni tipo di risorsa è organizzato e gestito come categorie separate in Azure.

> [!NOTE]
> Per l'anteprima pubblica, è possibile visualizzare solo le risorse dell'app per la logica distribuita **(anteprima)** nel portale di Azure, non creare nuove risorse dell'app per la **logica (anteprima)** . È possibile creare queste app per la logica solo in Visual Studio Code. Tuttavia, è possibile [aggiungere flussi di lavoro](#add-workflows) alle app per la logica distribuite con questo tipo di risorsa.

Per trovare app per la logica con il tipo di risorsa app per la **logica (anteprima)** , seguire questa procedura:

1. Nella casella di ricerca portale di Azure immettere `logic app preview` . Quando viene visualizzato l'elenco dei risultati, in **Servizi**selezionare app per la **logica (anteprima)**.

   ![Screenshot che mostra la casella di ricerca portale di Azure con il testo di ricerca "anteprima app per la logica".](./media/create-stateful-stateless-workflows-visual-studio-code/portal-find-logic-app-preview-resource.png)

1. Nel riquadro **app per la logica (anteprima)** individuare e selezionare l'app per la logica che è stata distribuita da Visual Studio Code.

   ![Screenshot che mostra le risorse portale di Azure e app per la logica (anteprima) distribuite in Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-preview-resources-pane.png)

   Il portale di Azure apre la pagina delle singole risorse per l'app per la logica selezionata.

   ![Screenshot che mostra la pagina delle risorse del flusso di lavoro dell'app per la logica nella portale di Azure.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

1. Per visualizzare i flussi di lavoro in questa app per la logica, selezionare **flussi di lavoro**dal menu dell'app per la logica.

   Il riquadro **flussi di lavoro** Mostra tutti i flussi di lavoro nell'app per la logica corrente. Questo esempio illustra il flusso di lavoro creato in Visual Studio Code.

   ![Screenshot che mostra una pagina delle risorse "app per la logica (anteprima)" con il riquadro "flussi di lavoro" aperto e il flusso di lavoro distribuito](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-logic-app-workflows-pane.png)

1. Per visualizzare un flusso di lavoro, selezionare il flusso di lavoro nel riquadro **flussi di lavoro** .

   Viene visualizzato il riquadro del flusso di lavoro con altre informazioni e attività che è possibile eseguire su tale flusso di lavoro.

   Per visualizzare, ad esempio, i passaggi nel flusso di lavoro, selezionare **finestra di progettazione**.

   ![Screenshot che mostra il riquadro "panoramica" del flusso di lavoro selezionato, mentre il menu flusso di lavoro Mostra il comando "finestra di progettazione" selezionato.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-overview-pane-select-designer.png)

   Viene aperta la finestra di progettazione dell'app per la logica che mostra il flusso di lavoro compilato in Visual Studio Code. È ora possibile apportare modifiche a questo flusso di lavoro nella portale di Azure.

   ![Screenshot che mostra la finestra di progettazione dell'app per la logica e il flusso di lavoro distribuiti da Visual Studio Code.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-workflow-designer.png)

<a name="add-workflows"></a>

## <a name="add-a-workflow-to-deployed-logic-apps"></a>Aggiungere un flusso di lavoro alle app per la logica distribuite

Tramite il portale di Azure è possibile aggiungere flussi di lavoro vuoti a una risorsa di app per la **logica (anteprima)** distribuita da Visual Studio Code e compilare tali flussi di lavoro nell'portale di Azure.

1. Nella [portale di Azure](https://portal.azure.com)trovare e selezionare la risorsa app per la logica distribuita **(anteprima)** .

1. Nel menu dell'app per la logica selezionare **flussi di lavoro**. Nel riquadro **flussi di lavoro** selezionare **Aggiungi**.

   ![Screenshot che mostra il riquadro "flussi di lavoro" dell'app per la logica selezionato e la barra degli strumenti con il comando "Aggiungi" selezionato.](./media/create-stateful-stateless-workflows-visual-studio-code/add-new-workflow.png)

1. Nel riquadro **nuovo flusso di lavoro** specificare il nome del flusso di lavoro. Selezionare Crea con stato o **senza** **stato** **>** **Create**.

   Quando Azure distribuisce il nuovo flusso di lavoro, che viene visualizzato nel riquadro **flussi di lavoro** , selezionare il flusso di lavoro per eseguire la gestione e altre attività, ad esempio aprire la finestra di progettazione dell'app per la logica o la visualizzazione codice.

   ![Screenshot che mostra il flusso di lavoro selezionato con le opzioni di gestione e revisione.](./media/create-stateful-stateless-workflows-visual-studio-code/view-new-workflow.png)

   Ad esempio, l'apertura della finestra di progettazione per un nuovo flusso di lavoro mostra un'area di disegno vuota. È ora possibile compilare il flusso di lavoro nella portale di Azure.

   ![Screenshot che mostra la finestra di progettazione dell'app per la logica e un flusso di lavoro vuoto.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-blank-workflow-designer.png)

<a name="run-history"></a>

## <a name="run-history-for-stateless-logic-app-workflows"></a>Cronologia di esecuzione per flussi di lavoro di app per la logica senza stato

Per eseguire più facilmente il debug di un flusso di lavoro di app per la logica senza stato, è possibile abilitare la cronologia di esecuzione per il flusso di lavoro in Visual Studio Code o nel portale di Azure e quindi disabilitare la cronologia di esecuzione al termine dell'operazione.

### <a name="for-a-stateless-logic-app-workflow-in-visual-studio-code"></a>Per un flusso di lavoro di app per la logica senza stato in Visual Studio Code

Se si sta lavorando ed eseguendo il flusso di lavoro dell'app per la logica senza stato localmente in Visual Studio Code, seguire questa procedura:

1. Nel progetto individuare ed espandere la cartella **Workflow-DesignTime** . Trovare e aprire il **local.settings.jssu** file.

1. Aggiungere la `Workflows.{yourWorkflowName}.operationOptions` proprietà e impostare il valore su `WithStatelessRunHistory` , ad esempio:

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflows.{yourWorkflowName}.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

1. Per disabilitare la cronologia di esecuzione al termine dell'operazione, eliminare la `Workflows.{yourWorkflowName}.OperationOptions` proprietà e il relativo valore oppure impostare la proprietà su `None` .

### <a name="for-a-stateless-logic-app-workflow-in-the-azure-portal"></a>Per un flusso di lavoro di app per la logica senza stato nel portale di Azure

Se il progetto è già stato distribuito nel portale di Azure, attenersi alla procedura seguente:

1. Nella [portale di Azure](https://portal.azure.com)trovare e aprire la risorsa dell' **app per la logica (anteprima)** .

1. Nel menu dell'app per la logica, in **Impostazioni**, selezionare **configurazione**.

1. Nella scheda **Impostazioni applicazione** selezionare **nuova impostazione applicazione**.

1. Nella casella **nome** del riquadro **Aggiungi/modifica impostazione applicazione** immettere il nome dell'opzione operazione: 

   `Workflows.{yourWorkflowName}.OperationOptions`

1. Nella casella **valore** immettere il valore seguente: `WithStatelessRunHistory`

   Ad esempio:

   ![Screenshot che mostra la risorsa portale di Azure e app per la logica (anteprima) con il riquadro "configurazione" > "nuova impostazione applicazione" < "Aggiungi/modifica impostazione applicazione" e i flussi di lavoro. {yourWorkflowName}. OperationOptions "opzione impostata su" WithStatelessRunHistory ".](./media/create-stateful-stateless-workflows-visual-studio-code/stateless-operation-options-run-history.png)

1. Al termine, fare clic su **OK**. Nel riquadro **configurazione** selezionare **Salva**.

Per abilitare il monitoraggio nella risorsa app per la logica distribuita (anteprima), passare alla sezione successiva.

<a name="enable-monitoring"></a>

## <a name="enable-monitoring-for-deployed-logic-app-preview-resources"></a>Abilitare il monitoraggio per le risorse dell'app per la logica distribuita (anteprima)

Per abilitare il monitoraggio in una risorsa app per la logica distribuita **(anteprima)** , seguire questa procedura:

1. Nella [portale di Azure](https://portal.azure.com)trovare e selezionare la risorsa app per la logica distribuita **(anteprima)** .

1. Nel menu di tale risorsa, in **API**, selezionare **CORS**.

1. Nel riquadro **CORS** , in **origini consentite**, aggiungere il carattere jolly (*).

1. Al termine, sulla barra degli strumenti di **CORS** selezionare **Salva**.

   ![Screenshot che mostra la portale di Azure con una risorsa app per la logica distribuita (anteprima). Nel menu delle risorse è selezionato "CORS" con una nuova voce per "origini consentite" impostata sul carattere jolly "*".](./media/create-stateful-stateless-workflows-visual-studio-code/enable-run-history-deployed-logic-app.png)

<a name="deploy-docker"></a>

## <a name="deploy-to-docker-container"></a>Distribuisci nel contenitore Docker

Usando lo [strumento dell'interfaccia della riga di comando di .NET Core](/dotnet/core/tools/), è possibile compilare il progetto e quindi pubblicare la compilazione. È quindi possibile compilare e usare un [contenitore Docker](/visualstudio/docker/tutorials/docker-tutorial#what-is-a-container) come destinazione per la distribuzione del flusso di lavoro dell'app per la logica. Per ulteriori informazioni, vedere gli argomenti seguenti:

* [Introduzione a contenitori e Docker](/dotnet/architecture/microservices/container-docker-introduction/)
* [Introduzione a .NET e Docker](/dotnet/core/docker/introduction)
* [Terminologia di Docker](/dotnet/architecture/microservices/container-docker-introduction/docker-terminology)
* [Esercitazione: Introduzione a Docker](/visualstudio/docker/tutorials/docker-tutorial)

1. Per compilare il progetto, aprire un prompt della riga di comando ed eseguire il comando seguente:

   `dotnet build -c release`

   Per ulteriori informazioni, vedere la pagina di riferimento per la [compilazione DotNet](/dotnet/core/tools/dotnet-build/) .

1. Pubblicare la compilazione eseguendo questo comando:

   `dotnet publish -c release`

   Per ulteriori informazioni, vedere la pagina di riferimento [DotNet Publish](/dotnet/core/tools/dotnet-publish/) .

1. Compilare un contenitore Docker usando un file Docker per un flusso di lavoro .NET ed eseguendo questo comando:

   `docker build --tag local/workflowcontainer .`

   Ad esempio, di seguito è riportato un file Docker di esempio che distribuisce un'app per la logica con stato e specifica la stringa di connessione per l'account di archiviazione di Azure usato per pubblicare l'app per la logica nella portale di Azure. Per trovare e copiare la stringa di connessione dell'account di archiviazione nell'portale di Azure, vedere [gestire le chiavi dell'account di archiviazione](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys).

   ![Screenshot che mostra la portale di Azure con le chiavi di accesso dell'account di archiviazione e la stringa di connessione copiata.](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   La stringa di connessione ha un aspetto simile all'esempio seguente:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageaccount;AccountKey={access-key};EndpointSuffix=core.windows.net`

   Ecco il formato per il file docker:


   ```text
   FROM mcr.microsoft.com/azure-functions/dotnet:3.0.14492-appservice

   ENV AzureWebJobsStorage <storage-account-connection-string>
   ENV AZURE_FUNCTIONS_ENVIRONMENT Development
   ENV AzureWebJobsScriptRoot=/home/site/wwwroot
   ENV AzureFunctionsJobHost__Logging__Console__IsEnabled=true
   ENV FUNCTIONS_V2_COMPATIBILITY_MODE=true

   COPY ./bin/Release/netcoreapp3.1/publish/ /home/site/wwwroot
   ```

   Per altre informazioni, vedere la pagina relativa alla [compilazione di Docker](https://docs.docker.com/engine/reference/commandline/build/).

1. Salvare la stringa in un punto sicuro, in modo da poter aggiungere successivamente la stringa al **local.settings.js** nei file del progetto usato per la creazione dell'app per la logica in Visual Studio Code.

1. Eseguire il contenitore localmente utilizzando questo comando:

   `docker run -e WEBSITE_HOSTNAME=localhost -p 8080:80 local/workflowcontainer`

   Per altre informazioni, vedere [Docker Run](https://docs.docker.com/engine/reference/commandline/run/).

1. Per ottenere l'URL di callback per il trigger di richiesta, inviare questa richiesta:

   `POST /runtime/webhooks/workflow/api/management/workflows/{workflow-name}/triggers/{trigger-name}/listCallbackUrl?api-version=2019-10-01-edge-preview&code={master-key}`

   Il valore della *chiave master* <> viene definito nell'account di archiviazione di Azure impostato per `AzureWebJobsStorage` nel file, **Azure-webjobs-Secrets/{Deployment-Name}/host.json**, in cui è possibile trovare il valore in questa sezione:

   ```json
   {
     <...>
     "masterKey": {
        "name": "master",
        "value": "<master-key>",
        "encrypted": false
     },
     <...>
   }
   ```

<a name="nested-behavior"></a>

## <a name="nested-behavior-differences-between-stateful-and-stateless-logic-apps"></a>Differenze di comportamento annidate tra app per la logica con stato e senza stato

È possibile [creare un flusso di lavoro di app per la logica chiamabile](../logic-apps/logic-apps-http-endpoint.md) da altri flussi di lavoro di app per la logica presenti nella stessa risorsa dell'app per la **logica (anteprima)** usando il trigger di [richiesta](../connectors/connectors-native-reqres.md) , il trigger [http webhook](../connectors/connectors-native-webhook.md) o i trigger del connettore gestito che hanno il [tipo ApiConnectionWehook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) e possono ricevere richieste HTTPS.

Ecco i modelli di comportamento che possono seguire i flussi di lavoro delle app per la logica annidati dopo che un flusso di lavoro padre chiama un flusso di lavoro figlio

* Modello di polling asincrono

  L'elemento padre non attende una risposta alla chiamata iniziale, ma controlla costantemente la cronologia di esecuzione del figlio fino a quando l'esecuzione del figlio non viene completata. Per impostazione predefinita, i flussi di lavoro con stato seguono questo modello, ideale per i flussi di lavoro figlio con esecuzione prolungata che potrebbero superare i [limiti di timeout della richiesta](../logic-apps/logic-apps-limits-and-config.md).

* Modello sincrono ("Fire and Forget")

  Il figlio riconosce la chiamata restituendo immediatamente una `202 ACCEPTED` risposta e l'elemento padre continua a eseguire l'azione successiva senza attendere i risultati dell'elemento figlio. Al contrario, l'elemento padre riceve i risultati al termine dell'esecuzione dell'elemento figlio. I flussi di lavoro con stato figlio che non includono un'azione di risposta seguono sempre il modello sincrono. Per i flussi di lavoro con stato figlio, la cronologia di esecuzione è disponibile per la revisione.

  Per abilitare questo comportamento, nella definizione JSON del flusso di lavoro impostare la `operationOptions` proprietà su `DisableAsyncPattern` . Per ulteriori informazioni, vedere [trigger e tipi di azione-opzioni operazione](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options).

* Trigger e Wait

  Per un flusso di lavoro senza stato figlio, l'elemento padre attende una risposta che restituisce i risultati dell'elemento figlio. Questo modello funziona in modo analogo all'uso del [trigger http o dell'azione](../connectors/connectors-native-http.md) incorporata per chiamare un flusso di lavoro figlio. I flussi di lavoro senza stato figlio che non includono un'azione di risposta restituiscono immediatamente una `202 ACCEPTED` risposta, ma il padre attende il completamento del figlio prima di continuare con l'azione successiva. Questi comportamenti si applicano solo ai flussi di lavoro senza stato figlio.

Questa tabella specifica il comportamento del flusso di lavoro figlio a seconda che l'elemento padre e l'elemento figlio siano con stato, senza stato o come tipi di flussi di lavoro misti:

| Flusso di lavoro padre | Flusso di lavoro figlio | Comportamento figlio |
|-----------------|----------------|----------------|
| Con stato | Con stato | Impostazione asincrona o sincrona con `"operationOptions": "DisableAsyncPattern"` |
| Con stato | Senza stato | Trigger e Wait |
| Senza stato | Con stato | Sincrono |
| Senza stato | Senza stato | Trigger e Wait |
||||

## <a name="limits"></a>Limiti

Sebbene molti [limiti esistenti per le app per la logica di Azure](../logic-apps/logic-apps-limits-and-config.md) siano gli stessi per questo tipo di risorsa, di seguito sono riportate le differenze in questa estensione di anteprima pubblica:

* Connettori gestiti: 50 richieste al minuto per connessione

* Per l'azione [di codice inline per](../logic-apps/logic-apps-add-run-inline-code.md) l'azione JavaScript, questi limiti sono stati modificati:

  * Il limite di caratteri di codice aumenta da 1.024 caratteri a 100.000 caratteri.

  * Il limite di tempo per l'esecuzione del codice aumenta da cinque secondi a 15 secondi.

## <a name="next-steps"></a>Passaggi successivi

Vorremmo ricevere informazioni sulle tue esperienze con questa estensione di anteprima pubblica.

* Per bug o problemi, [creare i problemi in GitHub](https://github.com/Azure/logicapps/issues).
* Per domande, richieste, commenti e altri commenti, [usare questo modulo di feedback](https://aka.ms/lafeedback).
