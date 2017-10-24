---
title: Guida introduttiva per gli sviluppatori in Azure | Microsoft Docs
description: Questo argomento offre informazioni essenziali per gli sviluppatori che vogliono iniziare a usare la piattaforma Microsoft Azure per le proprie esigenze di sviluppo.
services: 
cloud: 
documentationcenter: 
author: ggailey777
manager: erikre
ms.assetid: 
ms.service: na
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2017
ms.author: glenga
ms.openlocfilehash: f80f9f0e6d3fde438d27949a3fea001f37d59ee4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-guide-for-azure-developers"></a>Guida introduttiva per gli sviluppatori in Azure

## <a name="what-is-azure"></a>Cos'è Azure?

Azure è una piattaforma cloud completa in grado di ospitare le applicazioni esistenti, semplificare lo sviluppo di nuove applicazioni e persino migliorare le applicazioni locali. Azure integra i servizi cloud necessari per sviluppare, testare, distribuire e gestire le applicazioni, sfruttando nello stesso tempo l'efficienza del cloud computing.

L'hosting delle applicazioni in Azure consente di iniziare da un'infrastruttura di dimensioni ridotte, ridimensionando l'applicazione con facilità man mano che la richiesta da parte dei clienti aumenta. Azure garantisce anche l'affidabilità necessaria per le applicazioni a disponibilità elevata, offrendo funzionalità di failover persino tra aree diverse. Il [Portale di Azure](https://portal.azure.com) consente di gestire facilmente tutti i servizi di Azure. È anche possibile gestire i servizi a livello di codice usando API e modelli specifici del servizio.

**Destinatari di questa guida**: questa guida è un'introduzione alla piattaforma di Azure per gli sviluppatori di applicazioni e offre le linee guida e le indicazioni necessarie per iniziare a creare nuove applicazioni o eseguire la migrazione delle applicazioni esistenti in Azure.

## <a name="where-do-i-start"></a>Dove iniziare?

Con tutti i servizi offerti da Azure, può essere complesso capire quali di questi siano necessari per supportare l'architettura della soluzione. Questa sezione evidenzia i servizi di Azure usati più frequentemente dagli sviluppatori. Per un elenco di tutti i servizi di Azure, vedere la [documentazione di Azure](../../index.md).

Prima di tutto, è necessario decidere come ospitare l'applicazione in Azure. È necessario gestire l'intera infrastruttura come una macchina virtuale (VM)? È possibile usare le funzionalità di gestione della piattaforma fornite da Azure? È necessario un framework senza server che ospiti solo l'esecuzione del codice?

L'applicazione ha bisogno di archiviazione cloud, per la quale Azure offre diverse opzioni. È possibile sfruttare i vantaggi dell'autenticazione aziendale di Azure. In Azure sono disponibili anche strumenti per lo sviluppo e il monitoraggio basati sul cloud. La maggior parte dei servizi di hosting, poi, offre l'integrazione DevOps.

Ecco alcuni dei servizi specifici che è consigliabile prendere in esame per le applicazioni.

### <a name="application-hosting"></a>Hosting di applicazioni

Azure è in grado di offrire diverse soluzioni basate sul cloud per l'esecuzione di applicazioni. Non è quindi necessario occuparsi dei dettagli relativi all'infrastruttura. Man mano che l'utilizzo delle risorse da parte delle applicazioni aumenta, è possibile aumentare o scalare orizzontalmente le risorse con facilità.

Azure offre servizi a supporto dello sviluppo di applicazioni e per la soddisfazione di qualsiasi esigenza di hosting, oltre a mettere a disposizione strumenti IaaS ( Infrastructure-as-a-Service, infrastruttura distribuita come servizio) che consentono il controllo completo dell'hsting delle applicazioni. Le offerte PaaS (Platform-as-a-Service, piattaforma distribuita come servizio) prevedono servizi completamente gestiti, indispensabili per garantire il massimo rendimento delle app. In Azure è disponibile persino un tipo di hosting realmente senza server in cui è necessario solo scrivere codice.

![Opzioni di hosting di applicazioni offerte da Azure](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>Servizio app di Azure 

Se si vuole usare il percorso più rapido per la pubblicazione di progetti basati sul Web, prendere in considerazione il servizio app di Azure. Il servizio App rende più semplice estendere le app Web per il supporto di client per dispositivi mobili e pubblicare API REST facilmente utilizzabili. Questa piattaforma consente l'autenticazione tramite provider basati su social network, il ridimensionamento automatico in base al traffico, l'esecuzione di test nell'ambiente di produzione e la distribuzione continua e basata sul contenitore.

È possibile creare app Web, back-end di app per dispositivi mobili e app per le API.

Poiché tutti e tre i tipi di app condividono il runtime del servizio app, è possibile ospitare un sito Web, supportare i client per dispositivi mobili ed esporre le API in Azure nello stesso progetto o nella stessa soluzione. Per altre informazioni sulle app Web del servizio app di Azure, vedere [Che cos'è App Web di Azure](../../app-service/app-service-web-overview.md).

Il servizio app, progettato su misura per DevOps, supporta vari strumenti per la pubblicazione e la distribuzione di integrazione continua, tra cui webhook GitHub, Jenkins, Visual Studio Team Services e TeamCity.

È possibile eseguire la migrazione delle applicazioni esistenti nel servizio app tramite lo [strumento di migrazione online](https://www.migratetoazure.net/).

>**Uso consigliato**: usare il servizio app per eseguire la migrazione in Azure di applicazioni Web esistenti, se per queste è necessaria una piattaforma di hosting gestita. È anche possibile usare il servizio app se l'app deve supportare client per dispositivi mobili o esporre API REST.

>**Come iniziare**: con il servizio app è facile creare e distribuire un'[app Web](../../app-service/app-service-web-get-started-dotnet.md), un'[app per dispositivi mobili](../../app-service-mobile/app-service-mobile-ios-get-started.md) o un'[app per le API](../../app-service/app-service-web-tutorial-rest-api.md) fin dalla prima volta.

>**Prova adesso**: il servizio app consente di eseguire il provisioning di un'app di breve durata per provare la piattaforma senza dover effettuare la registrazione per la creazione di un account Azure. È possibile provare la piattaforma e [creare un'app del servizio app di Azure](https://tryappservice.azure.com/).

#### <a name="azure-virtual-machines"></a>Macchine virtuali di Azure

Come provider di un'infrastruttura distribuita come servizio (IaaS, Infrastructure-as-a-Service), Azure consente di distribuire le applicazioni o di eseguirne la migrazione all'interno di macchine virtuali Windows o Linux. Insieme a Rete virtuale di Azure, Macchine virtuali di Azure supporta la distribuzione di VM (Virtual Machine, macchina virtuale) di Windows o Linux in Azure. Con le VM è possibile avere il controllo totale sulla configurazione delle macchine stesse. Quando si usano VM, l'utente è responsabile di tutte le operazioni di installazione, configurazione e manutenzione del software server, nonché dell'applicazione di patch del sistema operativo.

Dato il livello di controllo disponibile per le VM, in Azure è possibile eseguire un'ampia gamma di carichi di lavoro server non adatti a un modello PaaS, ad esempio server di database, Windows Server Active Directory e Microsoft SharePoint. Per altre informazioni, vedere la documentazione relativa alle macchine virtuali [Linux](/azure/virtual-machines/linux/) o [Windows](/azure/virtual-machines/windows/).

>**Uso consigliato**: usare macchine virtuali se si vuole il controllo completo sull'infrastruttura dell'applicazione o se è necessario eseguire la migrazione in Azure di carichi di lavoro delle applicazioni locali senza dover apportare modifiche.

>**Come iniziare**: creare una [VM Linux](../../virtual-machines/virtual-machines-linux-quick-create-portal.md) o una [VM Windows](../../virtual-machines/virtual-machines-windows-hero-tutorial.md) dal Portale di Azure.

#### <a name="azure-functions-serverless"></a>Funzioni di Azure (senza server)

Come sarebbe se, invece di occuparsi della creazione e della gestione di un'intera applicazione o dell'infrastruttura necessaria per eseguire il codice, si potesse semplicemente scrivere il codice ed eseguirlo in risposta a eventi specifici o in base a una pianificazione?  [Funzioni di Azure](../../azure-functions/functions-overview.md) è un'offerta di tipo "senza server" che consente di scrivere solo il codice che serve. Con Funzioni di Azure l'esecuzione del codice viene attivata da richieste HTTP, webhook o eventi del servizio cloud oppure in base a una pianificazione. È possibile scrivere il codice nel linguaggio preferito, ad esempio C\#, F\#, Node.js, Python o PHP. Con la fatturazione in base al consumo, si paga solo il tempo di esecuzione del codice e Azure si ridimensiona in base alle esigenze.

>**Uso consigliato**: usare Funzioni di Azure quando il codice viene attivato da altri servizi di Azure, da eventi basati sul Web o in base a una pianificazione. È possibile usare Funzioni di Azure anche quando non è necessario sovraccaricare l'infrastruttura con un progetto completamente ospitato o quando si vuole pagare solo per il tempo in cui il codice viene eseguito. Per altre informazioni, vedere [Introduzione a Funzioni di Azure](../../azure-functions/functions-overview.md).

>**Come iniziare**: eseguire l'esercitazione introduttiva di Funzioni di Azure nel portale per [creare la prima funzione](../../azure-functions/functions-create-first-azure-function.md).

>**Prova adesso**: Funzioni di Azure consente di eseguire il codice senza richiedere la registrazione per la creazione di un account di Azure. È possibile provare subito a [creare la prima funzione di Azure](https://tryappservice.azure.com/).

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Azure Service Fabric è una piattaforma di sistemi distribuiti che semplifica lo sviluppo, la creazione del pacchetto, la distribuzione e la gestione di microservizi scalabili e affidabili. Fornisce inoltre una gamma completa di funzionalità di gestione per il provisioning, la distribuzione, il monitoraggio, l'aggiornamento, l'esecuzione di patch e l'eliminazione di applicazioni distribuite. Le app, eseguite in un pool condiviso di computer, possono essere inizialmente di piccole dimensioni e ridimensionarsi in seguito su centinaia o migliaia di computer in base alle esigenze.

Service Fabric supporta API Web con Open Web Interface for .NET (OWIN) e ASP.NET Core e mette a disposizione SDK per la compilazione di servizi su Linux in .NET Core e Java. Per altre informazioni su Service Fabric, vedere il [percorso di apprendimento di Service Fabric](https://azure.microsoft.com/documentation/learning-paths/service-fabric/).

>**Uso consigliato:**Service Fabric è una scelta valida quando si deve creare una nuova applicazione o riscrivere un'applicazione esistente per l'architettura dei microservizi. È consigliabile usare Service Fabric se è necessario un controllo più approfondito sull'infrastruttura sottostante o l'accesso diretto a questa.

>**Come iniziare:** [creare la prima applicazione Azure Service Fabric](../../service-fabric/service-fabric-create-your-first-application-in-visual-studio.md).

### <a name="enhance-your-applications-with-azure-services"></a>Migliorare le applicazioni con i servizi di Azure

Oltre all'hosting di applicazioni, Azure mette a disposizione servizi in grado di migliorare lo sviluppo, il funzionamento e la manutenzione delle applicazioni, sia nel cloud che locali.

#### <a name="hosted-storage-and-data-access"></a>Archiviazione ospitata e accesso ai dati

La maggior parte delle applicazioni deve archiviare dati. Pertanto, indipendentemente dalla modalità con cui si decide di ospitare l'applicazione in Azure, è necessario prendere in considerazione uno o più dei servizi seguenti di archiviazione dei dati.

-   **Database SQL di Azure**: versione basata su Azure del motore di Microsoft SQL Server per l'archiviazione di dati tabulari relazionali nel cloud. Il database SQL offre prestazioni prevedibili, scalabilità senza tempi di inattività, continuità aziendale e protezione dei dati.

    >**Uso consigliato**: quando per l'applicazione è necessaria l'archiviazione dei dati con integrità referenziale, nonché il supporto delle transazioni e delle query T-SQL.

    >**Come iniziare**: [creare un database SQL in pochi minuti usando il Portale di Azure](../../sql-database/sql-database-get-started.md).

-   **Archiviazione di Azure**: offre risorse di archiviazione durevoli e a disponibilità elevata per BLOB, code, file e altri tipi di dati non relazionali. Queste risorse rappresentano la base dell'archiviazione per le VM.

    >**Uso consigliato**: quando l'app esegue l'archiviazione di dati non relazionali, ad esempio di coppie chiave-valore (tabelle), BLOB, condivisioni file o messaggi (code).

    >**Come iniziare**: scegliere uno dei tipi di archiviazione seguenti: [BLOB](../../storage/blobs/storage-dotnet-how-to-use-blobs.md), [tabelle](../../cosmos-db/table-storage-how-to-use-dotnet.md), [code](../../storage/queues/storage-dotnet-how-to-use-queues.md) o [file](../../storage/files/storage-dotnet-how-to-use-files.md).

-   **Azure DocumentDB**: servizio di database NoSQL completamente gestito e scalabile con funzioni di query SQL sui dati di oggetto. È possibile accedere a DocumentDB tramite i driver di MongoDB esistenti.
    >**Uso consigliato:** quando l'applicazione deve essere in grado di eseguire query SQL su documenti JSON o se si usa MongoDB.

    >**Come iniziare**: [creare un'applicazione console DocumentDB in C#](../../documentdb/documentdb-get-started.md). Per gli sviluppatori MongoDB è consigliabile vedere [Supporto del protocollo di DocumentDB per MongoDB](../../documentdb/documentdb-protocol-mongodb.md).

È possibile usare [Azure Data Factory](../../data-factory/introduction.md) per spostare in Azure dati locali esistenti. Se non è ancora possibile spostare i dati nel cloud, [Connessioni ibride](../../biztalk-services/integration-hybrid-connection-overview.md) in Servizi BizTalk consente di connettere le app ospitate del servizio app a risorse locali. È anche possibile connettersi ai servizi dati e di archiviazione di Azure dalle applicazioni locali.

#### <a name="docker-support"></a>Supporto di Docker

I contenitori Docker, un tipo di virtualizzazione del sistema operativo, consente di distribuire le applicazioni in modo più efficiente e prevedibile. Un'applicazione all'interno di un contenitore funziona in produzione allo stesso modo dei sistemi di sviluppo e test. È possibile gestire i contenitori tramite gli strumenti di Docker standard. Per distribuire e gestire le applicazioni basate su contenitore in Azure, è possibile usare le competenze esistenti e gli strumenti open source più diffusi.

In Azure sono disponibili diverse modalità d'uso dei contenitori nelle applicazioni.

-   **Estensione VM di Azure Docker**: consente di usare gli strumenti di Docker per configurare una VM come host Docker.

    >**Uso consigliato**: quando si vuole generare distribuzioni di contenitori coerenti per le applicazioni in una VM o usare [Docker Compose](https://docs.docker.com/compose/overview/).

    >**Come iniziare**: [creare un ambiente Docker in Azure tramite l'estensione VM di Docker](../../virtual-machines/virtual-machines-linux-dockerextension.md).

-   **Servizio contenitore di Azure**: consente di creare, configurare e gestire un cluster di macchine virtuali preconfigurate per l'esecuzione delle applicazioni all'interno di contenitori. Per altre informazioni sul servizio contenitore, vedere [Azure Container Service introduction](../../container-service/container-service-intro.md) (Introduzione al servizio contenitore di Azure).

    >**Uso consigliato**: quando è necessario creare ambienti scalabili e pronti per la produzione che offrano strumenti di pianificazione e gestione aggiuntivi o quando si vuole distribuire un cluster Docker Swarm.

    >**Come iniziare**: [distribuire un cluster di contenitori Docker](../../container-service/dcos-swarm/container-service-deployment.md).

-   **Docker Machine**: consente di installare e gestire Docker Engine in host virtuali tramite comandi di Docker Machine.

    >**Uso consigliato**: quando è necessario creare rapidamente il prototipo di un'app tramite la creazione di un unico host Docker.

-   **Immagine Docker personalizzata per il servizio app**: consente di distribuire un'app Web in Linux usando contenitori Docker di un registro contenitori o il contenitore di un cliente.

    >**Uso consigliato**: quando si distribuisce un'app Web in un'immagine Docker in Linux.

    >**Come iniziare**: [usare un'immagine Docker personalizzata per il servizio app in Linux](../../app-service/containers/quickstart-custom-docker-image.md).

### <a name="authentication"></a>Autenticazione

È fondamentale non solo sapere chi sta usando le applicazioni, ma anche impedire l'accesso non autorizzato alle risorse. In Azure sono disponibili diversi modi per autenticare i client delle app.

-   **Azure Active Directory (Azure AD)**: il servizio Microsoft multi-tenant e basato sul cloud per la gestione delle identità e dell'accesso. Tramite l'integrazione con Azure AD è possibile aggiungere la funzionalità Single-Sign-On (SSO) alle applicazioni. È possibile accedere alle proprietà delle directory usando direttamente l'API di Azure AD Graph o l'API di Microsoft Graph. È possibile eseguire l'integrazione con il supporto di Azure AD per il framework di autorizzazione OAuth 2.0 e OpenID Connect usando endpoint HTTP/REST nativi e le librerie di autenticazione di Azure AD multipiattaforma.

    >**Uso consigliato**: quando si vuole offrire un'esperienza SSO, usare dati basati su Graph o autenticare utenti in base al dominio.

    >**Come iniziare**: per altre informazioni, vedere la [guida per gli sviluppatori di Azure Active Directory](../../active-directory/develop/active-directory-developers-guide.md).

-   **Autenticazione del servizio app**: quando si sceglie di ospitare l'app nel servizio app, si ottiene anche il supporto dell'autenticazione predefinita per Azure AD, insieme ai provider di identità basati su social network, ad esempio Facebook, Google, Microsoft e Twitter.

    >**Uso consigliato**: quando si vuole abilitare l'autenticazione in un'app del servizio App tramite Azure AD, provider di identità basati su social network o entrambi.

    >**Come iniziare**: per altre informazioni sull'autenticazione nel servizio app, vedere [Autenticazione e autorizzazione nel servizio app di Azure](../../app-service/app-service-authentication-overview.md).

Per altre informazioni sulle procedure consigliate per la sicurezza in Azure, vedere [Procedure consigliate e modelli per la sicurezza di Azure](../../security/security-best-practices-and-patterns.md).

### <a name="monitoring"></a>Monitoraggio

Quando l'applicazione è in esecuzione e operativa in Azure, è necessario essere in grado di monitorarne le prestazioni, controllare la presenza di problemi e verificare in che modo i clienti usano l'app. Azure offre diverse opzioni di monitoraggio.

-   **Visual Studio Application Insights**: servizio di analisi estendibile ospitato in Azure che si integra con Visual Studio per il monitoraggio delle applicazioni Web in tempo reale. Questo servizio mette a disposizione i dati necessari per ottenere un miglioramento costante delle prestazioni e dell'usabilità delle applicazioni, indipendentemente dal fatto che siano ospitate in Azure o meno.

    >**Come iniziare**: seguire l'[esercitazione di Application Insights](../../application-insights/app-insights-overview.md).

-   **Monitoraggio di Azure**: servizio che consente di visualizzare, instradare e archiviare metriche e log generati dall'infrastruttura e dalle risorse di Azure, nonché di eseguire query e operazioni su tali metriche e log. Monitoraggio di Azure, da cui provengono le visualizzazioni dati presentate nel Portale di Azure, rappresenta un'origine singola per il monitoraggio delle risorse di Azure.
 
    >**Come iniziare**: [Introduzione al monitoraggio di Azure](../../monitoring-and-diagnostics/monitoring-get-started.md).

### <a name="devops-integration"></a>Integrazione di strumenti DevOps

Sia che si tratti di eseguire il provisioning di VM o di pubblicare app Web con integrazione continua, Azure si integra con la maggior parte degli strumenti DevOps più diffusi. Grazie al supporto di strumenti quali, tra gli altri, Jenkins, GitHub, Puppet, Chef, TeamCity, Ansible, Visual Studio Team Services, è possibile usare gli strumenti già disponibili e ottimizzare l'esperienza esistente.

>**Prova adesso:** [alcune integrazioni di DevOps](https://azure.microsoft.com/try/devops/).

>**Come iniziare**: per visualizzare le opzioni di DevOps per le app del servizio app, vedere [Distribuzione continua nel servizio app di Azure](../../app-service/app-service-continuous-deployment.md).


## <a name="azure-regions"></a>Aree di Azure

Azure è una piattaforma cloud globale disponibile a livello generale in molte aree del mondo. Quando si esegue il provisioning di un servizio, di un'applicazione o di una VM in Azure, viene chiesto di selezionare un'area, che rappresenta il centro dati specifico in cui viene eseguita l'applicazione o in cui sono archiviati i dati. Tali aree corrispondono a località specifiche, pubblicate nella pagina [Aree di Azure](https://azure.microsoft.com/regions/).

### <a name="choose-the-best-region-for-your-application-and-data"></a>Scegliere l'area più adatta per l'applicazione e i dati

Uno dei vantaggi dell'uso di Azure è la possibilità di distribuire le applicazioni in più centri dati in tutto il mondo. La scelta dell'area può influire sulle prestazioni dell'applicazione. È preferibile, ad esempio, scegliere l'area più vicina alla maggior parte dei clienti, per ridurre la latenza nelle richieste di rete. È anche necessario selezionare un'area in base ai requisiti legali per la distribuzione dell'app in determinati paesi. È sempre da considerare procedura consigliata l'archiviazione dei dati delle applicazioni nello stesso centro dati che ospita la relativa applicazione o nel centro dati più vicino possibile.

### <a name="multi-region-apps"></a>App con più aree

Nonostante sia improbabile, è possibile che un intero centro dati venga disconnesso a causa di un evento, ad esempio una calamità naturale o un guasto su Internet. La procedura consigliata prevede che le applicazioni aziendali di importanza critica vengano ospitate in più centri dati per garantirne la massima disponibilità. L'uso di più aree può anche ridurre la latenza per gli utenti globali e offrire altre opportunità per una maggiore flessibilità durante l'aggiornamento delle applicazioni.

Alcuni servizi, ad esempio Macchina virtuale e Servizi app, usano [Gestione traffico di Azure](../../traffic-manager/traffic-manager-overview.md) per abilitare il supporto di più aree con failover tra le aree per garantire il funzionamento delle applicazioni aziendali a disponibilità elevata. Per un esempio, vedere [Azure reference architecture: Web application with high availability](../../guidance/guidance-web-apps-multi-region.md) (Architettura di riferimento per Azure: applicazione Web a disponibilità elevata).

>**Uso consigliato**: in presenza di applicazioni aziendali a disponibilità elevata che traggono vantaggio dalla replica e dal failover.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Come gestire le applicazioni e i progetti

Azure offre un'ampia gamma di esperienze per la creazione e la gestione delle risorse, delle applicazioni e dei progetti di Azure, sia a livello di codice che nel [Portale di Azure](https://portal.azure.com/).

### <a name="command-line-interfaces-and-powershell"></a>Interfacce della riga di comando e PowerShell

Azure offre due modi per gestire le applicazioni e i servizi dalla riga di comando tramite Bash, Terminal, prompt dei comandi o lo strumento da riga di comando preferito. In genere, è possibile eseguire le stesse attività, ad esempio la creazione e la configurazione di macchine virtuali, reti virtuali, app Web e altri servizi, sia dalla riga di comando che dal Portale di Azure.

-   [Interfaccia della riga di comando di Azure](../../xplat-cli-install.md): consente di connettersi a una sottoscrizione di Azure e di programmare varie attività per le risorse di Azure dalla riga di comando.

-   [Azure PowerShell](../../powershell-install-configure.md): mette a disposizione un set di moduli con cmdlet che consentono di gestire le risorse di Azure tramite Windows PowerShell.

### <a name="azure-portal"></a>Portale di Azure

Il Portale di Azure è un'applicazione basata sul Web che è possibile usare per creare, gestire e rimuovere risorse e servizi di Azure. Il Portale di Azure, disponibile all'indirizzo <https://portal.azure.com>, include un dashboard personalizzabile, strumenti per la gestione delle risorse di Azure e l'accesso alle impostazioni della sottoscrizione e alle informazioni di fatturazione. Per altre informazioni, vedere la [Panoramica del portale di Azure](../../azure-portal-overview.md).

### <a name="rest-apis"></a>API REST

Azure si basa su un set di API REST che supportano l'interfaccia utente del Portale Azure. La maggior parte di queste API REST è supportata anche per consentire il provisioning e la gestione a livello di codice delle risorse e delle applicazioni di Azure da qualsiasi dispositivo abilitato per Internet. Per il set completo della documentazione dell'API REST, vedere [Azure REST SDK reference](https://docs.microsoft.com/rest/api/) (Informazioni di riferimento per Azure REST SDK).

### <a name="apis"></a>API

Oltre alle API REST, molti servizi di Azure consentono di gestire a livello di codice le risorse dalle applicazioni tramite Azure SDK specifici di ogni piattaforma, tra cui gli SDK per le piattaforme di sviluppo seguenti:

-   [.NET](https://go.microsoft.com/fwlink/?linkid=834925)
-   [Node.JS](http://azure.github.io/azure-sdk-for-node/)
-   [Java](https://docs.microsoft.com/java/api/)
-   [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)

Per servizi quali [App per dispositivi mobili](../../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md) e [Servizi multimediali di Azure](../../media-services/media-services-dotnet-how-to-use.md) sono disponibili SDK lato client per l'accesso ai servizi dal Web e da app client per dispositivi mobili.

### <a name="azure-resource-manager"></a>Gestione risorse di Azure 
    
L'esecuzione di app in Azure implica probabilmente l'uso di più servizi di Azure, che seguono tutti lo stesso ciclo di vita e possono essere considerati come un'unità logica. Ad esempio, un'app Web può usare i servizi app Web, database SQL, Archiviazione, Cache Redis di Azure e Rete di distribuzione dei contenuti di Microsoft Azure. [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) consente di usare le risorse dell'applicazione come gruppo. È possibile distribuire, aggiornare o eliminare tutte le risorse con un'unica operazione coordinata.

Oltre al raggruppamento e alla gestione logica di risorse correlate, Azure Resource Manager include funzionalità di distribuzione che consentono di personalizzare la distribuzione e la configurazione delle risorse correlate stesse. Usando Resource Manager, ad esempio, è possibile distribuire e configurare un'applicazione costituita da più macchine virtuali, da un sistema di bilanciamento del carico e da un database SQL di Azure come unità singola.

Per sviluppare questo tipo di distribuzioni si usa un modello di Azure Resource Manager, che è un documento in formato JSON. I modelli consentono di definire la distribuzione e di gestire le applicazioni tramite modelli dichiarativi, anziché tramite script. I modelli possono funzionare in ambienti diversi, ad esempio negli ambienti di test, staging e produzione. Tramite i modelli, ad esempio, è possibile aggiungere un pulsante che consenta, con un solo clic, di passare a un repository di GitHub e di distribuire il codice presente all'interno di questo in un set di servizi di Azure.

>**Uso consigliato**: usare modelli di Resource Manager quando si vuole distribuire l'app con una distribuzione basata su modello gestibile a livello di programmazione tramite le API REST, l'interfaccia della riga di comando di Azure e Azure PowerShell.

>**Come iniziare**: per iniziare a usare i modelli, vedere [Creazione di modelli di Azure Resource Manager](../../resource-group-authoring-templates.md).

## <a name="understanding-accounts-subscriptions-and-billing"></a>Informazioni sugli account, sulle sottoscrizioni e sulla fatturazione

Gli sviluppatori vogliono dedicarsi subito allo sviluppo di codice e iniziare il più rapidamente possibile a creare ed eseguire applicazioni. Microsoft vuole incoraggiare l'uso di Azure da parte degli sviluppatori e per rendere più facile acquisire familiarità con Azure, offre una [versione di prova gratuita](https://azure.microsoft.com/free/). Per alcuni servizi, ad esempio [Servizio app di Azure](https://tryappservice.azure.com/), esiste anche una funzionalità di prova gratuita, che non richiede neanche la creazione di un account. Farsi coinvolgere dalla stesura del codice e distribuire la propria applicazione in Azure è sicuramente entusiasmante. È tuttavia importante dedicare del tempo alla comprensione del funzionamento di Azure dal punto di vista degli account utente, delle sottoscrizioni e della fatturazione.

### <a name="what-is-an-azure-account"></a>Che cos'è un account di Azure?

Per poter creare o usare una sottoscrizione di Azure, è necessario un account di Azure. Un account di Azure è semplicemente un'identità in Azure AD o in una directory, ad esempio in un'organizzazione aziendale o in un istituto di istruzione, considerata attendibile da Azure AD. Se si non appartiene ad alcuna organizzazione di questo tipo, è comunque possibile creare una sottoscrizione tramite l'account Microsoft, che è considerato attendibile da Azure AD. Per altre informazioni sull'integrazione di Windows Server Active Directory locale con Azure AD, vedere [Integrazione delle identità locali con Azure Active Directory](../../active-directory/active-directory-aadconnect.md).

Ogni sottoscrizione di Azure ha una relazione di trust con un'istanza di Azure AD. Ciò significa che considera attendibile quella directory per l'autenticazione di utenti, servizi e dispositivi. Più sottoscrizioni possono considerare attendibile la stessa directory, ma una sottoscrizione considera attendibile una sola directory. Per altre informazioni, vedere [Associare le sottoscrizioni di Azure ad Azure Active Directory](../../active-directory/active-directory-how-subscriptions-associated-directory.md).

Oltre ad account identità di Azure singoli, detti anche *utenti*, in Azure AD è possibile definire *gruppi*. La creazione di gruppi di utenti è un metodo efficiente per la gestione dell'accesso alle risorse in una sottoscrizione tramite il controllo degli accessi in base al ruolo. Per informazioni sulla creazione di gruppi, vedere [Creare un gruppo in Azure Active Directory (anteprima)](../../active-directory/active-directory-groups-create-azure-portal.md). È possibile creare e gestire gruppi anche [tramite PowerShell](../../active-directory/active-directory-accessmanagement-groups-settings-v2-cmdlets.md).

### <a name="manage-your-subscriptions"></a>Gestire le sottoscrizioni

Una sottoscrizione è un'unità logica di servizi di Azure collegata a un account di Azure. In una sottoscrizione ogni account associato ha un ruolo. La fatturazione per i servizi di Azure si basa sulla sottoscrizione. Per un elenco per tipo delle offerte di sottoscrizione disponibili , vedere [Dettagli delle offerte per Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/).

#### <a name="administrator-roles"></a>Ruoli di amministratore

In una sottoscrizione di Azure sono disponibili più ruoli di amministratore account, che è possibile assegnare in qualsiasi momento.

-   **Amministratore account** : questo ruolo ha il controllo completo sulla sottoscrizione ed è responsabile della fatturazione.

-   **Amministratore del servizio**: questo ruolo ha il controllo su tutti i servizi nella sottoscrizione. Per impostazione predefinita, questo account corrisponde all'amministratore account.

-   **Coamministratore**: questo ruolo ha gli stessi diritti di accesso dell'amministratore del servizio, ma non può modificare l'associazione della sottoscrizione a una directory di Azure.

Per altre informazioni sui ruoli di amministratore, vedere [Come aggiungere o modificare i ruoli di amministratore di Azure](../../billing/billing-add-change-azure-subscription-administrator.md#add-an-admin-for-a-subscription).

#### <a name="resource-groups"></a>Gruppi di risorse

Quando si esegue il provisioning di nuovi servizi di Azure, questa operazione viene eseguita nell'ambito di una sottoscrizione specifica. Servizi di Azure singoli, detti anche risorse, vengono creati nel contesto di un gruppo di risorse. I gruppi di risorse semplificano la distribuzione e la gestione delle risorse dell'applicazione. Un gruppo di risorse deve contenere tutte le risorse per l'applicazione che si vuole che funzionino come un'unità singola. È possibile spostare le risorse tra gruppi di risorse e persino tra sottoscrizioni. Per informazioni sullo spostamento di risorse, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../../resource-group-move-resources.md).

Azure Resource Explorer è un ottimo strumento per la visualizzazione delle risorse già create nella sottoscrizione. Per altre informazioni, vedere [Usare Azure Resource Explorer per visualizzare e modificare risorse](../../resource-manager-resource-explorer.md).

#### <a name="grant-access-to-resources"></a>Concedere l'accesso alle risorse

Quando si consente l'accesso alle risorse di Azure, la procedura sempre consigliata è di concedere agli utenti i privilegi minimi necessari per eseguire un'attività specifica.

-   **Controllo degli accessi in base al ruolo**: in Azure è possibile concedere l'accesso agli account utente (entità di sicurezza) per un ambito specifico: sottoscrizione, gruppo di risorse o risorse singole. Il controllo degli accessi in base al ruolo consente di distribuire un set di risorse in un gruppo di risorse e di concedere autorizzazioni a un utente o a un gruppo specifico. Questo controllo consente anche di limitare l'accesso alle risorse appartenenti al gruppo di risorse di destinazione. È anche possibile concedere l'accesso a un'unica risorsa, ad esempio a una macchina virtuale o a una rete virtuale. Per concedere l'accesso, si assegna un ruolo all'utente, al gruppo o all'entità servizio. Sono disponibili molti ruoli predefiniti ed è anche possibile definire ruoli personalizzati.

    >**Uso consigliato**: quando è necessaria una gestione degli accessi specifica per utenti e gruppi.

    >**Come iniziare**: per altre informazioni, vedere [Introduzione alla gestione degli accessi nel Portale di Azure](../../active-directory/role-based-access-control-what-is.md).

-   **Oggetti entità servizio**: oltre a concedere l'accesso a entità utente e a gruppi, è possibile concedere lo stesso tipo di accesso a un'entità servizio.

    > **Uso consigliato**: quando si gestiscono le risorse di Azure o si concede l'accesso per le applicazioni a livello di codice. Per altre informazioni, vedere [Creare un'applicazione e un'entità servizio di Azure Active Directory](../../resource-group-create-service-principal-portal.md).

#### <a name="tags"></a>Tag

Azure Resource Manager consente di assegnare tag personalizzati a risorse singole. I tag sono coppie chiave-valore che possono essere utili quando è necessario organizzare le risorse per la fatturazione o il monitoraggio. I tag rappresentano un metodo per tenere traccia delle risorse attraverso più gruppi di risorse. È possibile assegnare tag nel portale, nel modello di Azure Resource Manager o a livello di codice usando l'API REST, l'interfaccia della riga di comando di Azure o PowerShell. È possibile assegnare più tag a ogni risorsa. Per altre informazioni, vedere [Uso dei tag per organizzare le risorse di Azure](../../resource-group-using-tags.md).

### <a name="billing"></a>Fatturazione

Durante lo spostamento da elaborazione locale a servizi ospitati nel cloud, la stima e la verifica dell'utilizzo dei servizi e dei costi correlati rappresentano problematiche significative. È importante essere in grado di stimare il costo dell'esecuzione di nuove risorse su base mensile. È anche necessario essere in grado di eseguire proiezioni di fatturazione per un mese specifico in base alla spesa corrente.

#### <a name="get-resource-usage-data"></a>Ottenere dati sull'utilizzo delle risorse

Azure offre un set di API REST di fatturazione che consentono l'accesso alle informazioni sull'utilizzo delle risorse e ai metadati per le sottoscrizioni di Azure. Queste API di fatturazione offrono la possibilità di stimare e gestire in modo più preciso i costi di Azure. È possibile tenere traccia della spesa e analizzarla in incrementi di un'ora, creare avvisi relativi alla spesa e stimare la fatturazione futura in base alle tendenze di utilizzo correnti.

>**Come iniziare**: per altre informazioni sull'uso delle API di fatturazione, vedere [Panoramica delle API di utilizzo della fatturazione e delle API RateCard di Azure](../../billing-usage-rate-card-overview.md).

#### <a name="predict-future-costs"></a>Stimare i costi futuri

Anche se è difficile stimare i costi in anticipo, Azure offre un [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/) che è possibile usare quando si esegue la stima del costo delle risorse distribuite. Per stimare i costi futuri in base al consumo corrente, è anche possibile usare il pannello Fatturazione nel portale e le API REST di fatturazione.

>**Come iniziare**: vedere [Panoramica delle API di utilizzo della fatturazione e delle API RateCard di Azure](../../billing-usage-rate-card-overview.md).

#### <a name="set-up-billing-alerts"></a>Impostare avvisi di fatturazione per le sottoscrizioni Microsoft Azure

Dopo aver distribuito l'applicazione o una soluzione in Azure, è possibile creare avvisi per l'invio di messaggi di posta elettronica all'approssimarsi dei limiti di spesa definiti per ogni avviso.

>**Come iniziare**: per altre informazioni, vedere [Impostare avvisi di fatturazione per le sottoscrizioni di Microsoft Azure](../../billing-set-up-alerts.md).
