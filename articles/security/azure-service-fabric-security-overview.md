---
title: Panoramica della sicurezza di Azure Service Fabric | Microsoft Docs
description: Questo articolo offre una panoramica della sicurezza di Azure Service Fabric.
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: 4cbd2791649c6d2dd005521cedb44c17aa874073
ms.contentlocale: it-it
ms.lasthandoff: 08/11/2017

---
# <a name="azure-service-fabric-security-overview"></a>Panoramica della sicurezza di Azure Service Fabric
[Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) è una piattaforma di sistemi distribuiti che semplifica la creazione di pacchetti, la distribuzione e la gestione di microservizi scalabili e affidabili. Service Fabric fa fronte alle principali problematiche correlate allo sviluppo e alla gestione delle applicazioni cloud. Gli sviluppatori e gli amministratori non devono più occuparsi di risolvere complessi problemi di infrastruttura e possono concentrarsi sull'implementazione di carichi di lavoro cruciali e impegnativi, con la certezza di assicurare scalabilità, affidabilità e gestibilità.

Questa panoramica della sicurezza di Azure Service Fabric è incentrata sulle aree seguenti:

-   Protezione del cluster
-   Monitoraggio e diagnostica
-   Protezione con certificati
-   Controllo degli accessi in base al ruolo
-   Protezione di un cluster con la sicurezza di Windows
-   Configurazione della sicurezza dell'applicazione in Service Fabric
-   Protezione della comunicazione per i servizi nella sicurezza di Azure Service Fabric

## <a name="securing-your-cluster"></a>Protezione del cluster
Azure Service Fabric è un sistema di orchestrazione di servizi in un cluster di computer. Per impedire a utenti non autorizzati di connettersi ai cluster, è necessario proteggere i cluster, in particolare quando sono in esecuzione carichi di lavoro di produzione. La creazione di cluster non protetti, anche se possibile, consente a utenti anonimi di connettersi a un cluster che espone gli endpoint di gestione a Internet pubblico.

Questo articolo offre una panoramica degli scenari di sicurezza per i cluster autonomi o in esecuzione in Azure e delle varie tecnologie usate per implementare tali scenari. Gli scenari di sicurezza del cluster sono:

-   Sicurezza da nodo a nodo
-   Sicurezza da client a nodo

### <a name="node-to-node-security"></a>Sicurezza da nodo a nodo
Protegge la comunicazione tra le VM o i computer del cluster. Assicura che solo i computer autorizzati a connettersi al cluster possono partecipare all'hosting di applicazioni e servizi nel cluster.

I cluster eseguiti in Azure o i cluster autonomi eseguiti in Windows possono usare la [sicurezza basata su certificati](https://msdn.microsoft.com/library/ff649801.aspx) o la [sicurezza di Windows](https://msdn.microsoft.com/library/ff649396.aspx) per computer Windows Server.

**Sicurezza basata su certificati da nodo a nodo**

Service Fabric usa i certificati server X.509 specificati durante le configurazioni del tipo di nodo quando si crea un cluster. Questo articolo include una rapida panoramica di questi certificati e di [come è possibile acquisirli o crearli](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates).

La sicurezza basata su certificati viene configurata durante la creazione del cluster tramite il portale di Azure, i modelli di Azure Resource Manager o un modello JSON autonomo. È possibile specificare un certificato primario e un certificato secondario facoltativo che viene usato per i rollover dei certificati. I certificati primario e secondario specificati devono essere diversi dai certificati client di amministrazione e dai certificati client di sola lettura specificati per la [sicurezza da client a nodo](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-security).

### <a name="client-to-node-security"></a>Sicurezza da client a nodo
La sicurezza da client a nodo viene configurata usando le identità dei client. Per stabilire l'attendibilità tra un client e il cluster, è necessario configurare il cluster in modo che riconosca quali identità di client può considerare attendibili. È possibile, a questo scopo, eseguire una delle due operazioni seguenti:

-   Specificare gli utenti del gruppo di dominio che possono connettersi.
-   Specificare gli utenti del nodo del dominio che possono connettersi.

Service Fabric supporta due tipi di controllo di accesso diversi per i client connessi a un cluster di Service Fabric:

-   Amministratore
-   Utente

La funzionalità di controllo di accesso consente all'amministratore del cluster di limitare l'accesso a determinati tipi di operazioni del cluster per diversi gruppi di utenti, rendendo il cluster più sicuro. Gli amministratori hanno accesso completo alle funzionalità di gestione, incluse funzionalità di lettura/scrittura. Gli utenti, per impostazione predefinita, hanno solo l'accesso in lettura alle funzionalità di gestione, ad esempio funzionalità di query, e la possibilità di risolvere applicazioni e servizi.

**Sicurezza basata su certificati da client a nodo**

La sicurezza basata su certificati da client a nodo viene configurata durante la creazione del cluster tramite il portale di Azure, i modelli di Resource Manager o un modello JSON autonomo specificando un certificato client di amministrazione e/o un certificato client utente. I certificati client di amministrazione e i certificati client utente specificati devono essere diversi dai certificati primario e secondario specificati per la sicurezza da nodo a nodo.

I client che si connettono al cluster con il certificato di amministrazione hanno accesso completo alle funzionalità di gestione. I client che si connettono al cluster con il certificato client utente di sola lettura hanno solo l'accesso in lettura alle funzionalità di gestione. Questi certificati vengono, in altre parole, usati per il controllo degli accessi in base al ruolo.

Per informazioni su come configurare la sicurezza basata su certificati in un cluster per Azure, vedere [Configurare un cluster di Service Fabric usando un modello di Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) .

**Sicurezza di Azure Active Directory (AAD) da client a nodo in Azure**

I cluster eseguiti in Azure possono inoltre proteggere l'accesso agli endpoint di gestione usando Azure Active Directory (AAD). Per informazioni su come creare i necessari elementi di AAD, su come popolarli durante la creazione dei cluster e su come connettersi a tali cluster in seguito, vedere [Configurare un cluster di Service Fabric usando un modello di Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) .

AAD consente alle organizzazioni, note come tenant, di gestire l'accesso degli utenti alle applicazioni, che si dividono in applicazioni con un'interfaccia utente di accesso basata sul Web e applicazioni con un'esperienza client nativa.

Un cluster di Service Fabric offre numerosi punti di ingresso alle relative funzionalità di gestione, tra cui Service Fabric Explorer basato sul Web e Visual Studio. Verranno quindi create due applicazioni AAD per controllare l'accesso al cluster, un'applicazione Web e un'applicazione nativa.
Per i cluster di Azure è consigliabile usare la sicurezza di AAD per l'autenticazione dei client e dei certificati per la sicurezza da nodo a nodo.

Per i cluster di Windows Server autonomi è consigliabile usare la sicurezza di Windows con account gestiti di gruppo, se sono presenti Windows Server 2012 R2 e Active Directory. In caso contrario, usare comunque la sicurezza di Windows con account di Windows.

## <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Monitoraggio e diagnostica in Azure Service Fabric
Il [monitoraggio e la diagnostica](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) sono essenziali per lo sviluppo, il test e la distribuzione di applicazioni e servizi in qualsiasi ambiente. Le soluzioni di Service Fabric funzionano meglio quando si pianifica e si implementa il monitoraggio e la diagnostica che consentono di verificare che le applicazioni e i servizi funzionino come previsto in un ambiente di sviluppo locale o in fase di produzione.

Dal punto di vista della sicurezza, gli obiettivi principali del monitoraggio e della diagnostica sono:

-   Rilevare e diagnosticare i problemi di infrastruttura e hardware che potrebbero essere generati da un evento di sicurezza.
-   Rilevare i problemi di app e software che potrebbero presentare indicatori di compromissione (IoC).
-   Analizzare l'utilizzo delle risorse per prevenire eventi involontari di tipo denial of service.

Il flusso di lavoro generale di monitoraggio e diagnostica è costituito da tre passaggi:

-   **Generazione di eventi**: sono inclusi eventi, ovvero log, tracce, eventi personalizzati, sia a livello di infrastruttura, cioè cluster, che a livello di applicazione/servizio. Per comprendere ciò che viene offerto e come aggiungere altri strumenti leggere altre informazioni su [eventi a livello di infrastruttura](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-infra) ed [eventi a livello di applicazione](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app).
-   **Aggregazione di eventi**: gli eventi generati devono essere raccolti e aggregati prima di poter essere visualizzati. È in genere consigliabile usare [Diagnostica di Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) (più simile a una raccolta di log basati su agenti) o [EventFlow](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-eventflow) (la raccolta di log in-process).
-   **Analisi**: gli eventi devono essere visualizzati e accessibili in alcuni formati, per consentirne l'analisi e la visualizzazione in base alle esigenze specifiche. Per quanto riguarda l'analisi e la visualizzazione dei dati di monitoraggio e diagnostica, nel mercato esistono diverse piattaforme molto funzionali. I due consigliati sono [OMS](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-oms) e [Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights) perché si integrano meglio con Service Fabric.

È possibile usare [Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) per monitorare molte delle risorse di Azure in cui viene creato un cluster di Service Fabric.

Un watchdog è un servizio separato che può controllare l'integrità e il carico tra servizi e creare report di integrità per l'intera gerarchia di modello. Questo consente di evitare errori che non verrebbero rilevati visualizzando un singolo servizio. Anche i watchdog sono utili per ospitare il codice che esegue azioni correttive senza l'intervento dell'utente, ad esempio, la cancellazione dei file di log nell'archiviazione a determinati intervalli di tempo. È possibile trovare un'implementazione di esempio del servizio watchdog [qui](https://azure.microsoft.com/resources/samples/service-fabric-watchdog-service/).

## <a name="secure-using-certificates"></a>Protezione con certificati
Questo articolo descrive come proteggere la comunicazione tra i diversi nodi del cluster di Windows autonomo e come autenticare i client che si connettono a questo cluster usando certificati X.509. In questo modo, solo gli utenti autorizzati possano accedere al cluster e alle applicazioni distribuite ed eseguire attività di gestione. La sicurezza basata su certificati deve essere abilitata nel cluster durante la creazione del cluster.

### <a name="x509-certificates-and-service-fabric"></a>Certificati X.509 e Service Fabric
I certificati digitali X509 vengono comunemente usati per autenticare client e server e per crittografare e firmare digitalmente i messaggi.

La tabella seguente include un elenco dei certificati necessari per la configurazione del cluster:

|Impostazione Informazioni sul certificato |Descrizione|
|-------------------------------|-----------|
|ClusterCertificate|    Questo certificato è necessario per proteggere la comunicazione tra i nodi di un cluster. È possibile usare due diversi certificati, uno primario e uno secondario per l'aggiornamento.|
|ServerCertificate| Questo certificato viene presentato al client quando tenta di connettersi al cluster. È possibile usare due diversi certificati del server, uno primario e uno secondario per l'aggiornamento.|
|ClientCertificateThumbprints|  Si tratta di un set di certificati che da installare nei client autenticati.|
|ClientCertificateCommonNames|  Impostare il nome comune del primo certificato client per CertificateCommonName. CertificateIssuerThumbprint è l'identificazione personale dell'autorità emittente del certificato.|
|ReverseProxyCertificate|   Si tratta di un certificato facoltativo che è possibile specificare se si vuole proteggere il [proxy inverso](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy).|

Per altre informazioni sulla protezione dei certificati, [fare clic qui](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security).

## <a name="role-based-access-control-rbac"></a>Controllo degli accessi in base al ruolo
Il Controllo di accesso consente all'amministratore del cluster di limitare l'accesso a determinate operazioni del cluster per diversi gruppi di utenti, rendendo più sicuro il cluster. Per i client che si connettono a un cluster, sono supportati due tipi di controllo di accesso diversi: il ruolo di amministratore e il ruolo utente.

Gli amministratori hanno accesso completo alle funzionalità di gestione, incluse funzionalità di lettura/scrittura. Gli utenti, per impostazione predefinita, hanno solo l'accesso in lettura alle funzionalità di gestione, ad esempio funzionalità di query, e la possibilità di risolvere applicazioni e servizi.

I ruoli client amministratore e utente vengono specificati al momento della creazione del cluster fornendo per ognuno identità separate, ad esempio certificati, AAD e così via. Per altre informazioni sulle impostazioni predefinite del controllo di accesso e su come modificarle, vedere [Controllo di accesso basato sui ruoli per i client di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

## <a name="secure-standalone-cluster-using-windows-security"></a>Protezione di un cluster con la sicurezza di Windows
Per impedire l'accesso non autorizzato a un cluster di Service Fabric, è necessario proteggere il cluster. La sicurezza è importante soprattutto quando il cluster esegue carichi di lavoro di produzione. Questo articolo descrive come configurare la sicurezza da nodo a nodo e da client a nodo usando la sicurezza di Windows nel file ClusterConfig.JSON.

**Configurare la sicurezza di Windows usando l'approccio account del servizio gestito di gruppo**

La sicurezza da nodo a nodo viene configurata impostando [ClustergMSAIdentity](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security) quando l'infrastruttura di servizi deve essere eseguita nell'account del servizio gestito di gruppo. Per creare relazioni di trust tra i nodi, è necessario che si riconoscano.

La sicurezza da client a nodo viene configurata tramite le identità dei client. Per stabilire un trust tra un client e il cluster, è necessario configurare il cluster in modo che riconosca quali identità dei client può considerare attendibili.

**Configurare la sicurezza di Windows usando un gruppo di computer**

La sicurezza da nodo a nodo viene configurata con ClusterIdentity se si vuole usare un gruppo di computer all'interno di un dominio di Active Directory. Per altre informazioni, vedere l'articolo su come [Creare un gruppo di computer in Active Directory](https://msdn.microsoft.com/library/aa545347).

La sicurezza da client a nodo viene configurata tramite le identità dei client. Per stabilire una relazione di trust tra un client e il cluster, è necessario configurare il cluster in modo che riconosca le identità dei client che il cluster può considerare attendibili. È possibile stabilire una relazione di trust in due modi diversi:

-   Specificare gli utenti del gruppo di dominio che possono connettersi.
-   Specificare gli utenti del nodo del dominio che possono connettersi.

## <a name="configure-application-security-in-service-fabric"></a>Configurazione della sicurezza dell'applicazione in Service Fabric
### <a name="managing-secrets-in-service-fabric-applications"></a>Gestione dei segreti nelle applicazioni di Service Fabric
Questa modalità consente di gestire i segreti in un'applicazione di Service Fabric. I segreti possono essere informazioni riservate, ad esempio le stringhe di connessione di archiviazione, le password o altri valori che non devono essere gestiti in testo normale.

Questo approccio usa [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) per gestire chiavi e segreti. L'uso di segreti in un'applicazione è tuttavia indipendente dalla piattaforma cloud e ciò consente alle applicazioni di essere distribuite in un cluster ospitato in un punto qualsiasi. In questo flusso sono presenti quattro passaggi principali:

-   Ottenere un certificato di crittografia dei dati.
-   Installare il certificato nel cluster.
-   Crittografare i valori dei segreti quando si distribuisce un'applicazione con il certificato e inserirli nel file di configurazione Settings.xml del servizio.
-   Leggere i valori crittografati risultati da Settings. XML eseguendo la decrittografia con lo stesso certificato di crittografia.

>[!Note]
>Per altre informazioni, vedere [Gestione dei segreti nelle applicazioni di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="configure-security-policies-for-your-application"></a>Configurare i criteri di sicurezza per l'applicazione
La sicurezza di Azure Service Fabric aiuta a proteggere le applicazioni che vengono eseguite nel cluster con account utente diversi. La sicurezza di Service Fabric aiuta anche a proteggere le risorse che vengono usate dalle applicazioni in fase di distribuzione con l'account utente, ad esempio file, directory e certificati. In questo modo le applicazioni in esecuzione, anche in un ambiente ospitato condiviso, sono reciprocamente protette.
I passaggi includono:

-   Configurare i criteri per il punto di ingresso dell'installazione del servizio.
-   Avviare i comandi di PowerShell da un punto di ingresso dell'installazione.
-   Usare il reindirizzamento della console per il debug locale.
-   Configurare i criteri per i pacchetti di codice del servizio.
-   Assegnare criteri di accesso di sicurezza per gli endpoint HTTP e HTTPS.

## <a name="secure-communication-for-services-in-azure-service-fabric-security"></a>Protezione della comunicazione per i servizi nella sicurezza di Azure Service Fabric
La sicurezza è uno degli aspetti essenziali delle comunicazioni. Il framework delle applicazioni Reliable Services offre alcuni strumenti e stack di comunicazione predefiniti che è possibile usare per migliorare la sicurezza.

-   [Proteggere un servizio quando si usa la comunicazione remota dei servizi](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication).
-   [Proteggere un servizio quando si usa uno stack di comunicazione basato su WCF](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication#help-secure-a-service-when-youre-using-a-wcf-based-communication-stack).

## <a name="next-steps"></a>Passaggi successivi
- Per informazioni concettuali sulla protezione del cluster, vedere [Creare un cluster di Service Fabric usando Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) e il [Creare un cluster di Service Fabric in Azure tramite il portale di Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal).
- Per altre informazioni, vedere [Scenari di sicurezza di un cluster di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).

