---
title: Guida introduttiva per gli sviluppatori in Azure | Microsoft Docs
description: This article provides essential information for developers looking to get started using the Microsoft Azure platform for their development needs.
services: ''
cloud: ''
documentationcenter: ''
author: ggailey777
manager: erikre
ms.assetid: ''
ms.service: azure
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2019
ms.author: glenga
ms.openlocfilehash: c8ab954471e597cfea5c6f56cd45b2191aa73242
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424073"
---
# <a name="get-started-guide-for-azure-developers"></a>Guida introduttiva per gli sviluppatori Azure

## <a name="what-is-azure"></a>Che cos'è Azure?

Azure is a complete cloud platform that can host your existing applications and streamline new application development. Azure can even enhance on-premises applications. Azure integrates the cloud services that you need to develop, test, deploy, and manage your applications, all while taking advantage of the efficiencies of cloud computing.

L'hosting delle applicazioni in Azure consente di iniziare da un'infrastruttura di dimensioni ridotte, ridimensionando l'applicazione con facilità man mano che la richiesta da parte dei clienti aumenta. Azure garantisce anche l'affidabilità necessaria per le applicazioni a disponibilità elevata, offrendo funzionalità di failover persino tra aree diverse. Il [Portale di Azure](https://portal.azure.com) consente di gestire facilmente tutti i servizi di Azure. È anche possibile gestire i servizi a livello di codice usando API e modelli specifici del servizio.

questa guida è un'introduzione alla piattaforma di Azure per gli sviluppatori di applicazioni e offre le linee guida e le indicazioni necessarie per iniziare a creare nuove applicazioni o eseguire la migrazione delle applicazioni esistenti in Azure.

## <a name="where-do-i-start"></a>Dove iniziare?

With all the services that Azure offers, it can be an intimidating task to figure out which services you need to support your solution architecture. Questa sezione evidenzia i servizi di Azure usati più frequentemente dagli sviluppatori. Per un elenco di tutti i servizi di Azure, vedere la [documentazione di Azure](../../index.md).

Prima di tutto, è necessario decidere come ospitare l'applicazione in Azure. È necessario gestire l'intera infrastruttura come una macchina virtuale (VM)? È possibile usare le funzionalità di gestione della piattaforma fornite da Azure? È necessario un framework senza server che ospiti solo l'esecuzione del codice?

L'applicazione ha bisogno di archiviazione cloud, per la quale Azure offre diverse opzioni. È possibile sfruttare i vantaggi dell'autenticazione aziendale di Azure. In Azure sono disponibili anche strumenti per lo sviluppo e il monitoraggio basati sul cloud. La maggior parte dei servizi di hosting, poi, offre l'integrazione DevOps.

Ecco alcuni dei servizi specifici che è consigliabile prendere in esame per le applicazioni.

### <a name="application-hosting"></a>Hosting di applicazioni

Azure è in grado di offrire diverse soluzioni basate sul cloud per l'esecuzione di applicazioni. Non è quindi necessario occuparsi dei dettagli relativi all'infrastruttura. Man mano che l'utilizzo delle risorse da parte delle applicazioni aumenta, è possibile aumentare o scalare orizzontalmente le risorse con facilità.

Azure offre servizi a supporto dello sviluppo di applicazioni e per la soddisfazione di qualsiasi esigenza di hosting, Azure offre soluzioni IaaS (Infrastructure-as-a-Service, infrastruttura distribuita come servizio) per fornire il controllo completo sull'hosting delle applicazioni. Azure's Platform as a Service (PaaS) offerings provide the fully managed services needed to power your apps. There's even true serverless hosting in Azure where all you need to do is write your code.

![Opzioni di hosting di applicazioni offerte da Azure](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>Servizio app di Azure 

Se si vuole usare il percorso più rapido per la pubblicazione di progetti basati sul Web, prendere in considerazione il servizio app di Azure. Il servizio App rende più semplice estendere le app Web per il supporto di client per dispositivi mobili e pubblicare API REST facilmente utilizzabili. Questa piattaforma consente l'autenticazione tramite provider basati su social network, il ridimensionamento automatico in base al traffico, l'esecuzione di test nell'ambiente di produzione e la distribuzione continua e basata sul contenitore.

È possibile creare app Web, back-end di app per dispositivi mobili e app per le API.

Poiché tutti e tre i tipi di app condividono il runtime del servizio app, è possibile ospitare un sito Web, supportare i client per dispositivi mobili ed esporre le API in Azure nello stesso progetto o nella stessa soluzione. Per altre informazioni sulle app Web del servizio app di Azure, vedere [Che cos'è App Web di Azure](../../app-service/overview.md).

Il servizio app, progettato su misura per DevOps, It supports various tools for publishing and continuous integration deployments. These tools include GitHub webhooks, Jenkins, Azure DevOps, TeamCity, and others.

È possibile eseguire la migrazione delle applicazioni esistenti nel servizio app tramite lo [strumento di migrazione online](https://www.migratetoazure.net/).

> **When to use**: Use App Service when you’re migrating existing web applications to Azure, and when you need a fully-managed hosting platform for your web apps. È anche possibile usare il servizio app se l'app deve supportare client per dispositivi mobili o esporre API REST.
> 
> **Come iniziare**: con il servizio app è facile creare e distribuire un'[app Web](../../app-service/app-service-web-get-started-dotnet.md), un'[app per dispositivi mobili](../../app-service-mobile/app-service-mobile-ios-get-started.md) o un'[app per le API](../../app-service/app-service-web-tutorial-rest-api.md) fin dalla prima volta.
> 
> **Prova adesso**: il servizio app consente di eseguire il provisioning di un'app di breve durata per provare la piattaforma senza dover effettuare la registrazione per la creazione di un account Azure. È possibile provare la piattaforma e [creare un'app del servizio app di Azure](https://tryappservice.azure.com/).

#### <a name="azure-virtual-machines"></a>Macchine virtuali di Azure

Ai provider IaaS Azure permette di eseguire la distribuzione o la migrazione dell'applicazione in macchine virtuali Windows o Linux. Insieme a Rete virtuale di Azure, Macchine virtuali di Azure supporta la distribuzione di VM (Virtual Machine, macchina virtuale) di Windows o Linux in Azure. Con le VM è possibile avere il controllo totale sulla configurazione delle macchine stesse. Quando si usano VM, l'utente è responsabile di tutte le operazioni di installazione, configurazione e manutenzione del software server, nonché dell'applicazione di patch del sistema operativo.

Dato il livello di controllo disponibile per le VM, in Azure è possibile eseguire un'ampia gamma di carichi di lavoro server non adatti a un modello PaaS, ad esempio server di database, Windows Server Active Directory e Microsoft SharePoint. Per altre informazioni, vedere la documentazione relativa alle macchine virtuali [Linux](/azure/virtual-machines/linux/) o [Windows](/azure/virtual-machines/windows/).

> **Uso consigliato**: usare macchine virtuali se si vuole il controllo completo sull'infrastruttura dell'applicazione o se è necessario eseguire la migrazione in Azure di carichi di lavoro delle applicazioni locali senza dover apportare modifiche.
> 
> **Come iniziare**: creare una [VM Linux](../../virtual-machines/virtual-machines-linux-quick-create-portal.md) o una [VM Windows](../../virtual-machines/virtual-machines-windows-hero-tutorial.md) dal Portale di Azure.

#### <a name="azure-functions-serverless"></a>Funzioni di Azure (senza server)

Rather than worrying about building out and managing a whole application or the infrastructure to run your code, what if you could just write your code and have it run in response to events or on a schedule?  [Funzioni di Azure](../../azure-functions/functions-overview.md) è un'offerta di tipo "senza server" che consente di scrivere solo il codice che serve. With Functions, you can trigger code execution with HTTP requests, webhooks, cloud service events, or on a schedule. È possibile scrivere il codice nel linguaggio preferito, ad esempio C\#, F\#, Node.js, Python o PHP. Con la fatturazione in base al consumo, si paga solo il tempo di esecuzione del codice e Azure si ridimensiona in base alle esigenze.

> **Uso consigliato**: usare Funzioni di Azure quando il codice viene attivato da altri servizi di Azure, da eventi basati sul Web o in base a una pianificazione. È possibile usare Funzioni di Azure anche quando non è necessario sovraccaricare l'infrastruttura con un progetto completamente ospitato o quando si vuole pagare solo per il tempo in cui il codice viene eseguito. Per altre informazioni, vedere [Introduzione a Funzioni di Azure](../../azure-functions/functions-overview.md).
> 
> **Come iniziare**: eseguire l'esercitazione introduttiva di Funzioni di Azure nel portale per [creare la prima funzione](../../azure-functions/functions-create-first-azure-function.md).
> 
> **Prova adesso**: Funzioni di Azure consente di eseguire il codice senza richiedere la registrazione per la creazione di un account di Azure. È possibile provare subito a [creare la prima funzione di Azure](https://tryappservice.azure.com/).

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Azure Service Fabric is a distributed systems platform. This platform makes it easy to build, package, deploy, and manage scalable and reliable microservices. It also provides comprehensive application management capabilities such as: 

* Provisioning
* Distribuzione 
* Monitorare
* Upgrading/patching
* Deleting 

Le app, eseguite in un pool condiviso di computer, possono essere inizialmente di piccole dimensioni e ridimensionarsi in seguito su centinaia o migliaia di computer in base alle esigenze.

Service Fabric supporta API Web con Open Web Interface for .NET (OWIN) e ASP.NET Core e mette a disposizione SDK per la compilazione di servizi su Linux in .NET Core e Java. Per altre informazioni su Service Fabric, vedere la [documentazione di Service Fabric](https://docs.microsoft.com/azure/service-fabric/).

> **Uso consigliato:** Service Fabric è una scelta valida quando si deve creare una nuova applicazione o riscrivere un'applicazione esistente per l'architettura dei microservizi. È consigliabile usare Service Fabric se è necessario un controllo più approfondito sull'infrastruttura sottostante o l'accesso diretto a questa.
> 
> **Come iniziare:** [creare la prima applicazione Azure Service Fabric](../../service-fabric/service-fabric-create-your-first-application-in-visual-studio.md).

### <a name="enhance-your-applications-with-azure-services"></a>Migliorare le applicazioni con i servizi di Azure

Along with application hosting, Azure provides service offerings that can enhance the functionality. Azure can also improve the development and maintenance of your applications, both in the cloud and on-premises.

#### <a name="hosted-storage-and-data-access"></a>Archiviazione ospitata e accesso ai dati

Most applications must store data, so however you decide to host your application in Azure, consider one or more of the following storage and data services.

- **Azure Cosmos DB**: A globally distributed, multi-model database service. This database enables you to elastically scale throughput and storage across any number of geographical regions with a comprehensive SLA. 
  
  > **Uso consigliato**: quando l'applicazione necessita di database di grafici, tabelle o documenti, inclusi i database di MongoDB, con più modelli di coerenza ben definiti. 
  > 
  > **Come iniziare**: [creare un'app Web di Azure Cosmos DB](../../cosmos-db/create-sql-api-dotnet.md). Se si è uno sviluppatore di MongoDB, vedere come [creare un'app Web di MongoDB con Azure Cosmos DB](../../cosmos-db/create-mongodb-dotnet.md).

- **Archiviazione di Azure**: offre risorse di archiviazione durevoli e a disponibilità elevata per BLOB, code, file e altri tipi di dati non relazionali. Queste risorse rappresentano la base dell'archiviazione per le VM.

  > **Uso consigliato**: quando l'app esegue l'archiviazione di dati non relazionali, ad esempio di coppie chiave-valore (tabelle), BLOB, condivisioni file o messaggi (code).
  > 
  > **Come iniziare**: scegliere uno dei tipi di archiviazione seguenti: [BLOB](../../storage/blobs/storage-dotnet-how-to-use-blobs.md), [tabelle](../../cosmos-db/table-storage-how-to-use-dotnet.md), [code](../../storage/queues/storage-dotnet-how-to-use-queues.md) o [file](../../storage/files/storage-dotnet-how-to-use-files.md).

- **Database SQL di Azure**: versione basata su Azure del motore di Microsoft SQL Server per l'archiviazione di dati tabulari relazionali nel cloud. Il database SQL offre prestazioni prevedibili, scalabilità senza tempi di inattività, continuità aziendale e protezione dei dati.

  > **Uso consigliato**: quando per l'applicazione è necessaria l'archiviazione dei dati con integrità referenziale, nonché il supporto delle transazioni e delle query T-SQL.
  > 
  > **Come iniziare**: [creare un database SQL in pochi minuti usando il Portale di Azure](../../sql-database/sql-database-get-started.md).


È possibile usare [Azure Data Factory](../../data-factory/introduction.md) per spostare in Azure dati locali esistenti. Se non è ancora possibile spostare i dati nel cloud, [Connessioni ibride](../../biztalk-services/integration-hybrid-connection-overview.md) in Servizi BizTalk consente di connettere le app ospitate del servizio app a risorse locali. È anche possibile connettersi ai servizi dati e di archiviazione di Azure dalle applicazioni locali.

#### <a name="docker-support"></a>Supporto di Docker

I contenitori Docker, un tipo di virtualizzazione del sistema operativo, consente di distribuire le applicazioni in modo più efficiente e prevedibile. Un'applicazione all'interno di un contenitore funziona in produzione allo stesso modo dei sistemi di sviluppo e test. È possibile gestire i contenitori tramite gli strumenti di Docker standard. Per distribuire e gestire le applicazioni basate su contenitore in Azure, è possibile usare le competenze esistenti e gli strumenti open source più diffusi.

In Azure sono disponibili diverse modalità d'uso dei contenitori nelle applicazioni.

- **Estensione VM di Azure Docker**: consente di usare gli strumenti di Docker per configurare una VM come host Docker.

  > **Uso consigliato**: quando si vuole generare distribuzioni di contenitori coerenti per le applicazioni in una VM o usare [Docker Compose](https://docs.docker.com/compose/overview/).
  > 
  > **Come iniziare**: [creare un ambiente Docker in Azure tramite l'estensione VM di Docker](../../virtual-machines/virtual-machines-linux-dockerextension.md).

- **servizio Azure Container**: consente di creare, configurare e gestire un cluster di macchine virtuali preconfigurate per l'esecuzione delle applicazioni all'interno di contenitori. Per altre informazioni sul servizio contenitore, vedere [Azure Container Service introduction](../../container-service/container-service-intro.md) (Introduzione al servizio Azure Container).

  > **Uso consigliato**: quando è necessario creare ambienti scalabili e pronti per la produzione che offrano strumenti di pianificazione e gestione aggiuntivi o quando si vuole distribuire un cluster Docker Swarm.
  > 
  > **Come iniziare**: [distribuire un cluster di contenitori Docker](../../container-service/dcos-swarm/container-service-deployment.md).

- **Docker Machine**: consente di installare e gestire Docker Engine in host virtuali tramite comandi di Docker Machine.

  >**Uso consigliato**: quando è necessario creare rapidamente il prototipo di un'app tramite la creazione di un unico host Docker.

- **Immagine Docker personalizzata per il servizio app**: consente di distribuire un'app Web in Linux usando contenitori Docker di un registro contenitori o il contenitore di un cliente.

  > **Uso consigliato**: quando si distribuisce un'app Web in un'immagine Docker in Linux.
  > 
  > **Come iniziare**: [usare un'immagine Docker personalizzata per il servizio app in Linux](../../app-service/containers/quickstart-docker-go.md).

### <a name="authentication"></a>Authentication

È fondamentale non solo sapere chi sta usando le applicazioni, ma anche impedire l'accesso non autorizzato alle risorse. In Azure sono disponibili diversi modi per autenticare i client delle app.

- **Azure Active Directory (Azure AD)** : il servizio Microsoft multi-tenant e basato sul cloud per la gestione delle identità e dell'accesso. Tramite l'integrazione con Azure AD è possibile aggiungere la funzionalità Single-Sign-On (SSO) alle applicazioni. È possibile accedere alle proprietà delle directory usando direttamente l'API di Azure AD Graph o l'API di Microsoft Graph. È possibile eseguire l'integrazione con il supporto di Azure AD per il framework di autorizzazione OAuth 2.0 e OpenID Connect usando endpoint HTTP/REST nativi e le librerie di autenticazione di Azure AD multipiattaforma.

  > **Uso consigliato**: quando si vuole offrire un'esperienza SSO, usare dati basati su Graph o autenticare utenti in base al dominio.
  > 
  > **Come iniziare**: per altre informazioni, vedere la [guida per gli sviluppatori di Azure Active Directory](../../active-directory/develop/v1-overview.md).

- **Autenticazione del servizio app**: quando si sceglie di ospitare l'app nel servizio app, si ottiene anche il supporto dell'autenticazione predefinita per Azure AD, insieme ai provider di identità basati su social network, ad esempio Facebook, Google, Microsoft e Twitter.

  > **Uso consigliato**: quando si vuole abilitare l'autenticazione in un'app del servizio App tramite Azure AD, provider di identità basati su social network o entrambi.
  > 
  > **Come iniziare**: per altre informazioni sull'autenticazione nel servizio app, vedere [Autenticazione e autorizzazione nel servizio app di Azure](../../app-service/overview-authentication-authorization.md).

Per altre informazioni sulle procedure consigliate per la sicurezza in Azure, vedere [Procedure consigliate e modelli per la sicurezza di Azure](../../security/fundamentals/best-practices-and-patterns.md).

### <a name="monitoring"></a>Monitorare

With your application up and running in Azure, you need to monitor performance, watch for issues, and see how customers are using your app. Azure offre diverse opzioni di monitoraggio.

-   **Visual Studio Application Insights**: servizio di analisi estendibile ospitato in Azure che si integra con Visual Studio per il monitoraggio delle applicazioni Web in tempo reale. It gives you the data that you need to improve the performance and usability of your apps continuously. This improvement occurs whether you host your applications on Azure or not.

    >**Come iniziare**: seguire l'[esercitazione di Application Insights](../../azure-monitor/app/app-insights-overview.md).

-   **Azure Monitor**: A service that helps you to visualize, query, route, archive, and act on the metrics and logs that you generate with your Azure infrastructure and resources. Monitor is a single source for monitoring Azure resources and provides the data views that you see in the Azure portal.
 
    >**Come iniziare**: [Introduzione al monitoraggio di Azure](../../monitoring-and-diagnostics/monitoring-get-started.md).

### <a name="devops-integration"></a>Integrazione con DevOps

Sia che si tratti di eseguire il provisioning di VM o di pubblicare app Web con integrazione continua, Azure si integra con la maggior parte degli strumenti DevOps più diffusi. You can work with the tools that you already have and maximize your existing experience with support for tools like: 

* Jenkins 
* GitHub 
* Puppet
* Chef
* TeamCity
* Ansible
* Azure DevOps

> **Come iniziare**: per visualizzare le opzioni di DevOps per le app del servizio app, vedere [Distribuzione continua nel servizio app di Azure](../../app-service/deploy-continuous-deployment.md).
> 
> **Prova adesso:** [alcune integrazioni di DevOps](https://azure.microsoft.com/try/devops/).


## <a name="azure-regions"></a>Aree di Azure

Azure è una piattaforma cloud globale disponibile a livello generale in molte aree del mondo. When you provision a service, application, or VM in Azure, you're asked to select a region. This region represents a specific datacenter where your application runs or where your data is stored. Tali aree corrispondono a località specifiche, pubblicate nella pagina [Aree di Azure](https://azure.microsoft.com/regions/).

### <a name="choose-the-best-region-for-your-application-and-data"></a>Scegliere l'area più adatta per l'applicazione e i dati

Uno dei vantaggi dell'uso di Azure è la possibilità di distribuire le applicazioni in più centri dati in tutto il mondo. La scelta dell'area può influire sulle prestazioni dell'applicazione. È preferibile, ad esempio, scegliere l'area più vicina alla maggior parte dei clienti, per ridurre la latenza nelle richieste di rete. You might also want to select your region to meet the legal requirements for distributing your app in certain countries/regions. È sempre da considerare procedura consigliata l'archiviazione dei dati delle applicazioni nello stesso centro dati che ospita la relativa applicazione o nel centro dati più vicino possibile.

### <a name="multi-region-apps"></a>App con più aree

Nonostante sia improbabile, è possibile che un intero centro dati venga disconnesso a causa di un evento, ad esempio una calamità naturale o un guasto su Internet. La procedura consigliata prevede che le applicazioni aziendali di importanza critica vengano ospitate in più centri dati per garantirne la massima disponibilità. L'uso di più aree può anche ridurre la latenza per gli utenti globali e offrire altre opportunità per una maggiore flessibilità durante l'aggiornamento delle applicazioni.

Alcuni servizi, ad esempio Macchina virtuale e Servizi app, usano [Gestione traffico di Azure](../../traffic-manager/traffic-manager-overview.md) per abilitare il supporto di più aree con failover tra le aree per garantire il funzionamento delle applicazioni aziendali a disponibilità elevata. Per un esempio, vedere [Azure reference architecture: Run a web application in multiple regions](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region) (Architettura di riferimento per Azure: Eseguire un'applicazione Web in più aree geografiche).

>**Uso consigliato**: in presenza di applicazioni aziendali a disponibilità elevata che traggono vantaggio dalla replica e dal failover.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Come gestire le applicazioni e i progetti

Azure offre un'ampia gamma di esperienze per la creazione e la gestione delle risorse, delle applicazioni e dei progetti di Azure, sia a livello di codice che nel [Portale di Azure](https://portal.azure.com/).

### <a name="command-line-interfaces-and-powershell"></a>Interfacce della riga di comando e PowerShell

Azure provides two ways to manage your applications and services from the command line. You can use tools like Bash, Terminal, the command prompt, or your command-line tool of choice. Usually, you can do the same tasks from the command line as in the Azure portal—such as creating and configuring virtual machines, virtual networks, web apps, and other services.

-   [Interfaccia della riga di comando di Azure](../../xplat-cli-install.md): consente di connettersi a una sottoscrizione di Azure e di programmare varie attività per le risorse di Azure dalla riga di comando.

-   [Azure PowerShell](../../powershell-install-configure.md): mette a disposizione un set di moduli con cmdlet che consentono di gestire le risorse di Azure tramite Windows PowerShell.

### <a name="azure-portal"></a>Portale di Azure

The [Azure portal](https://portal.azure.com) is a web-based application. You can use the Azure portal to create, manage, and remove Azure resources and services. Sono inclusi:

* A configurable dashboard
* Azure resource management tools
* Access to subscription settings and billing information. Per altre informazioni, vedere la [Panoramica del portale di Azure](../../azure-portal-overview.md).

### <a name="rest-apis"></a>API REST

Azure si basa su un set di API REST che supportano l'interfaccia utente del Portale Azure. La maggior parte di queste API REST è supportata anche per consentire il provisioning e la gestione a livello di codice delle risorse e delle applicazioni di Azure da qualsiasi dispositivo abilitato per Internet. Per il set completo della documentazione dell'API REST, vedere [Azure REST SDK reference](https://docs.microsoft.com/rest/api/) (Informazioni di riferimento per Azure REST SDK).

### <a name="apis"></a>API

Along with REST APIs, many Azure services also let you programmatically manage resources from your applications by using platform-specific Azure SDKs, including SDKs for the following development platforms:

-   [.NET](https://go.microsoft.com/fwlink/?linkid=834925)
-   [Node.js](https://docs.microsoft.com/azure/javascript/)
-   [Java](https://docs.microsoft.com/java/azure)
-   [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [Python](/azure/python/)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
-   [Go](https://docs.microsoft.com/azure/go)

Per servizi quali [App per dispositivi mobili](../../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md) e [Servizi multimediali di Azure](../../media-services/previous/media-services-dotnet-how-to-use.md) sono disponibili SDK lato client per l'accesso ai servizi dal Web e da app client per dispositivi mobili.

### <a name="azure-resource-manager"></a>Azure Resource Manager 
    
Running your app on Azure likely involves working with multiple Azure services. These services follow the same life cycle and can be thought of as a logical unit. Ad esempio, un'app Web può usare i servizi app Web, database SQL, Archiviazione, Cache Redis di Azure e Rete di distribuzione dei contenuti di Microsoft Azure. [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) consente di usare le risorse dell'applicazione come gruppo. È possibile distribuire, aggiornare o eliminare tutte le risorse con un'unica operazione coordinata.

Along with logically grouping and managing related resources, Azure Resource Manager includes deployment capabilities that let you customize the deployment and configuration of related resources. For example, you can use Resource Manager deploy and configure an application. This application can consist of multiple virtual machines, a load balancer, and an Azure SQL database as a single unit.

Per sviluppare questo tipo di distribuzioni si usa un modello di Azure Resource Manager, che è un documento in formato JSON. I modelli consentono di definire la distribuzione e di gestire le applicazioni tramite modelli dichiarativi, anziché tramite script. I modelli possono funzionare in ambienti diversi, ad esempio negli ambienti di test, staging e produzione. For example, you can use templates to add a button to a GitHub repo that deploys the code in the repo to a set of Azure services with a single click.

> **Uso consigliato**: usare modelli di Resource Manager quando si vuole distribuire l'app con una distribuzione basata su modello gestibile a livello di programmazione tramite le API REST, l'interfaccia della riga di comando di Azure e Azure PowerShell.
> 
> **Come iniziare**: per iniziare a usare i modelli, vedere [Creazione di modelli di Azure Resource Manager](../../resource-group-authoring-templates.md).

## <a name="understanding-accounts-subscriptions-and-billing"></a>Informazioni sugli account, sulle sottoscrizioni e sulla fatturazione

Gli sviluppatori vogliono dedicarsi subito allo sviluppo di codice e iniziare il più rapidamente possibile a creare ed eseguire applicazioni. Microsoft vuole incoraggiare l'uso di Azure da parte degli sviluppatori e per rendere più facile acquisire familiarità con Azure, offre una [versione di prova gratuita](https://azure.microsoft.com/free/). Per alcuni servizi, ad esempio [Servizio app di Azure](https://tryappservice.azure.com/), esiste anche una funzionalità di prova gratuita, che non richiede neanche la creazione di un account. As fun as it is to dive into coding and deploying your application to Azure, it's also important to take some time to understand how Azure works. Specifically,  you should understand how it works from a standpoint of user accounts, subscriptions, and billing.

### <a name="what-is-an-azure-account"></a>Che cos'è un account di Azure?

To create or work with an Azure subscription, you must have an Azure account. An Azure account is simply an identity in Azure AD or in a directory, such as a work or school organization, that Azure AD trusts. Se si non appartiene ad alcuna organizzazione di questo tipo, è comunque possibile creare una sottoscrizione tramite l'account Microsoft, che è considerato attendibile da Azure AD. Per altre informazioni sull'integrazione di Windows Server Active Directory locale con Azure AD, vedere [Integrazione delle identità locali con Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md).

Ogni sottoscrizione di Azure ha una relazione di trust con un'istanza di Azure AD. Ciò significa che considera attendibile quella directory per l'autenticazione di utenti, servizi e dispositivi. Più sottoscrizioni possono considerare attendibile la stessa directory, ma una sottoscrizione considera attendibile una sola directory. Per altre informazioni, vedere [Associare le sottoscrizioni di Azure ad Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

As well as defining individual Azure account identities, also called *users*, you can define *groups* in Azure AD. La creazione di gruppi di utenti è un metodo efficiente per la gestione dell'accesso alle risorse in una sottoscrizione tramite il controllo degli accessi in base al ruolo. Per informazioni sulla creazione di gruppi, vedere [Creare un gruppo in Azure Active Directory (anteprima)](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). È possibile creare e gestire gruppi anche [tramite PowerShell](../../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md).

### <a name="manage-your-subscriptions"></a>Gestire le sottoscrizioni

Una sottoscrizione è un raggruppamento logico di servizi di Azure collegato a un account di Azure. Un singolo account di Azure può contenere più sottoscrizioni. La fatturazione per i servizi di Azure si basa sulla sottoscrizione. Per un elenco per tipo delle offerte di sottoscrizione disponibili , vedere [Dettagli delle offerte per Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/). Azure subscriptions have an Account Administrator who has full control over the subscription. They also have a Service Administrator who has control over all services in the subscription. Per informazioni sugli amministratori delle sottoscrizione classiche, vedere [Aggiungere o modificare gli amministratori delle sottoscrizioni di Azure](../../billing/billing-add-change-azure-subscription-administrator.md). Individual accounts can be granted detailed control of Azure resources using [role-based access control (RBAC)](../../role-based-access-control/overview.md).

#### <a name="resource-groups"></a>Gruppi di risorse

Quando si esegue il provisioning di nuovi servizi di Azure, questa operazione viene eseguita nell'ambito di una sottoscrizione specifica. Servizi di Azure singoli, detti anche risorse, vengono creati nel contesto di un gruppo di risorse. I gruppi di risorse semplificano la distribuzione e la gestione delle risorse dell'applicazione. Un gruppo di risorse deve contenere tutte le risorse per l'applicazione che si vuole che funzionino come un'unità singola. È possibile spostare le risorse tra gruppi di risorse e persino tra sottoscrizioni. Per informazioni sullo spostamento di risorse, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../../resource-group-move-resources.md).

Azure Resource Explorer è un ottimo strumento per la visualizzazione delle risorse già create nella sottoscrizione. Per altre informazioni, vedere [Usare Azure Resource Explorer per visualizzare e modificare risorse](../../resource-manager-resource-explorer.md).

#### <a name="grant-access-to-resources"></a>Concedere l'accesso alle risorse

When you allow access to Azure resources, it’s always a best practice to provide users with the least privilege that’s required to do a given task.

- **Controllo degli accessi in base al ruolo**: in Azure è possibile concedere l'accesso agli account utente (entità di sicurezza) per un ambito specifico: sottoscrizione, gruppo di risorse o risorse singole. RBAC lets you deploy resources into a resource group and grant permissions to a specific user or group. It also lets you limit access to only the resources that belong to the target resource group. È anche possibile concedere l'accesso a un'unica risorsa, ad esempio a una macchina virtuale o a una rete virtuale. Per concedere l'accesso, si assegna un ruolo all'utente, al gruppo o all'entità servizio. Sono disponibili molti ruoli predefiniti ed è anche possibile definire ruoli personalizzati. Per altre informazioni, vedere [Che cos'è il controllo degli accessi in base al ruolo?](../../role-based-access-control/overview.md)

  > **Uso consigliato**: quando è necessaria una gestione degli accessi con granularità fine per utenti e gruppi o quando è necessario impostare un utente come proprietario di una sottoscrizione.
  > 
  > **Introduzione**: per altre informazioni, vedere [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](../../role-based-access-control/role-assignments-portal.md).

- **Service principal objects**: Along with providing access to user principals and groups, you can grant the same access to a service principal.

  > **Uso consigliato**: quando si gestiscono le risorse di Azure o si concede l'accesso per le applicazioni a livello di codice. Per altre informazioni, vedere [Creare un'applicazione e un'entità servizio di Azure Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="tags"></a>Tag

Azure Resource Manager consente di assegnare tag personalizzati a risorse singole. I tag sono coppie chiave-valore che possono essere utili quando è necessario organizzare le risorse per la fatturazione o il monitoraggio. I tag rappresentano un metodo per tenere traccia delle risorse attraverso più gruppi di risorse. You can assign tags the following ways:

* In the portal 
* In the Azure Resource Manager template 
* Utilizzo dell'API REST
* Uso dell'interfaccia della riga di comando di Azure
* Con PowerShell 

È possibile assegnare più tag a ogni risorsa. Per altre informazioni, vedere [Uso dei tag per organizzare le risorse di Azure](../../resource-group-using-tags.md).

### <a name="billing"></a>Fatturazione

Durante lo spostamento da elaborazione locale a servizi ospitati nel cloud, la stima e la verifica dell'utilizzo dei servizi e dei costi correlati rappresentano problematiche significative. It’s important to estimate what new resources cost to run on a monthly basis. You can also project how the billing looks for a given month based on the current spending.

#### <a name="get-resource-usage-data"></a>Ottenere dati sull'utilizzo delle risorse

Azure offre un set di API REST di fatturazione che consentono l'accesso alle informazioni sull'utilizzo delle risorse e ai metadati per le sottoscrizioni di Azure. Queste API di fatturazione offrono la possibilità di stimare e gestire in modo più preciso i costi di Azure. You can track and analyze spending in hourly increments and create spending alerts. You can also predict future billing based on current usage trends.

>**Come iniziare**: per altre informazioni sull'uso delle API di fatturazione, vedere [Panoramica delle API di utilizzo della fatturazione e delle API RateCard di Azure](../../billing-usage-rate-card-overview.md).

#### <a name="predict-future-costs"></a>Stimare i costi futuri

Although it's challenging to estimate costs ahead of time, Azure has tools that can help. It has a [pricing calculator](https://azure.microsoft.com/pricing/calculator/) to help estimate the cost of deployed resources. You can also use the Billing resources in the portal and the Billing REST APIs to estimate future costs, based on current consumption.

>**Come iniziare**: vedere [Panoramica delle API di utilizzo della fatturazione e delle API RateCard di Azure](../../billing-usage-rate-card-overview.md).
