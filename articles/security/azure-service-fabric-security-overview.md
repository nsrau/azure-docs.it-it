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
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 908bdaf002e42035567974b204f5b39e73e82024
ms.contentlocale: it-it
ms.lasthandoff: 08/29/2017

---
# <a name="azure-service-fabric-security-overview"></a>Panoramica della sicurezza di Azure Service Fabric
[Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) è una piattaforma di sistemi distribuiti che semplifica la creazione di pacchetti, la distribuzione e la gestione di microservizi scalabili e affidabili. Service Fabric fa fronte alle principali problematiche correlate allo sviluppo e alla gestione delle applicazioni cloud. Gli sviluppatori e gli amministratori non devono più occuparsi di risolvere complessi problemi di infrastruttura e possono concentrarsi sull'implementazione di carichi di lavoro cruciali e impegnativi, con la certezza di assicurare scalabilità, affidabilità e gestibilità.

Questa panoramica della sicurezza di Azure Service Fabric è incentrata sulle aree seguenti:

-   Protezione del cluster
-   Il monitoraggio e la diagnostica
-   Creazione di ambienti più sicuri usando i certificati
-   Uso del controllo degli accessi in base al ruolo di Azure
-   Protezione dei cluster mediante la sicurezza di Windows
-   Configurazione della sicurezza dell'applicazione in Service Fabric
-   Protezione delle comunicazioni per i servizi in Azure Service Fabric 

## <a name="secure-your-cluster"></a>Garantire la sicurezza del cluster
Azure Service Fabric orchestra i servizi in un cluster di computer. Per impedire a utenti non autorizzati di connettersi ai cluster, è necessario proteggerli, in particolare quando sono in esecuzione carichi di lavoro di produzione. La creazione di cluster non protetti, anche se possibile, potrebbe consentire a utenti anonimi di connettersi a un cluster che espone gli endpoint di gestione a Internet pubblico.

Questa sezione fornisce una panoramica degli scenari di sicurezza per i cluster eseguiti in modo autonomo o in Azure. Descrive inoltre le varie tecnologie che consentono di implementare questi scenari. Gli scenari di sicurezza del cluster sono:

-   Sicurezza da nodo a nodo
-   Sicurezza da client a nodo

### <a name="node-to-node-security"></a>Sicurezza da nodo a nodo
La sicurezza da nodo a nodo protegge la comunicazione tra le VM o i computer del cluster. Con la sicurezza da nodo a nodo solo i computer autorizzati a connettersi al cluster possono partecipare all'hosting di applicazioni e servizi nel cluster.

I cluster eseguiti in Azure o i cluster autonomi eseguiti in Windows possono usare la [sicurezza basata su certificati](https://msdn.microsoft.com/library/ff649801.aspx) o la [sicurezza di Windows](https://msdn.microsoft.com/library/ff649396.aspx) per computer Windows Server.

**Sicurezza basata su certificati da nodo a nodo**

Service Fabric usa i certificati server X.509 specificati quando si crea un cluster. Per una rapida panoramica di questi certificati e di come è possibile acquisirli o crearli, vedere [Utilizzo dei certificati](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates).

La sicurezza basata su certificati viene configurata durante la creazione del cluster tramite il portale di Azure, i modelli di Azure Resource Manager o un modello JSON autonomo. È possibile specificare un certificato primario e un certificato secondario facoltativo che viene usato per i rollover dei certificati. I certificati primario e secondario specificati devono essere diversi dai certificati client di amministrazione e dai certificati client di sola lettura specificati per la [sicurezza da client a nodo](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-security).

### <a name="client-to-node-security"></a>Sicurezza da client a nodo
La sicurezza da client a nodo viene configurata usando le identità dei client. Per stabilire l'attendibilità tra un client e il cluster, è necessario configurare il cluster in modo che riconosca quali identità di client può considerare attendibili. È possibile, a questo scopo, eseguire una delle due operazioni seguenti:

-   Specificare gli utenti del gruppo di dominio che possono connettersi. 
-   Specificare gli utenti del nodo del dominio che possono connettersi.

Service Fabric supporta due tipi di controllo di accesso diversi per i client connessi a un cluster di Service Fabric:

-   Amministratore
-   Utente

Tramite il controllo di accesso, gli amministratori di cluster possono limitare l'accesso a determinati tipi di operazioni del cluster. In questo modo il cluster è più sicuro.

 Gli amministratori hanno accesso completo alle funzionalità di gestione, incluse funzionalità di lettura/scrittura. Gli utenti, per impostazione predefinita, hanno solo l'accesso in lettura alle funzionalità di gestione, ad esempio funzionalità di query, e la possibilità di risolvere applicazioni e servizi.

**Sicurezza basata su certificati da client a nodo**

La sicurezza basata su certificati viene configurata durante la creazione del cluster tramite il portale di Azure, i modelli di Resource Manager o un modello JSON autonomo. È necessario specificare un certificato client di amministrazione e/o un certificato client utente. 

I certificati client di amministrazione e i certificati client utente specificati devono essere diversi dai certificati primario e secondario specificati per la sicurezza da nodo a nodo.

I client che si connettono al cluster con il certificato di amministrazione hanno accesso completo alle funzionalità di gestione. I client che si connettono al cluster con il certificato client utente di sola lettura hanno solo l'accesso in lettura alle funzionalità di gestione. In altre parole, questi certificati vengono usati per il controllo degli accessi in base al ruolo.

Per informazioni su come configurare la sicurezza basata su certificati in un cluster per Azure, vedere [Configurare un cluster di Service Fabric usando un modello di Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) .

**Sicurezza di Azure Active Directory da client a nodo in Azure**

I cluster eseguiti in Azure possono inoltre proteggere l'accesso agli endpoint di gestione usando Azure Active Directory (Azure AD). Per informazioni su come creare i necessari elementi di Azure Active Directory, su come popolarli durante la creazione dei cluster e su come connettersi a tali cluster in seguito, vedere [Configurare un cluster di Service Fabric usando un modello di Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) .

Azure AD consente alle organizzazioni (note come tenant) di gestire l'accesso utenti alle applicazioni. Alcune applicazioni sono caratterizzate da un'interfaccia utente di accesso basata sul Web, altre invece da un'esperienza client nativa.

Un cluster di Service Fabric offre numerosi punti di ingresso alle relative funzionalità di gestione, tra cui Service Fabric Explorer basato sul Web e Visual Studio. Verranno quindi create due applicazioni Azure AD per controllare l'accesso al cluster, un'applicazione Web e un'applicazione nativa.

Per i cluster di Azure si consiglia di usare la sicurezza di Azure AD per l'autenticazione dei client e dei certificati per la sicurezza da nodo a nodo.

Per i cluster di Windows Server autonomi con Windows Server 2012 R2 e Active Directory si consiglia di usare la sicurezza di Windows con account gestiti di gruppo.  In caso contrario, usare comunque la sicurezza di Windows con account di Windows.

## <a name="understand-monitoring-and-diagnostics-in-azure-service-fabric"></a>Monitoraggio e diagnostica in Azure Service Fabric
Il [monitoraggio e la diagnostica](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) sono essenziali per lo sviluppo, il test e la distribuzione di applicazioni e servizi in qualsiasi ambiente. Le soluzioni di Service Fabric funzionano meglio quando si implementa il monitoraggio e la diagnostica per garantire che le applicazioni e i servizi funzionino come previsto in un ambiente di sviluppo locale o in fase di produzione.

Dal punto di vista della sicurezza, gli obiettivi principali del monitoraggio e della diagnostica sono:

-   Rilevare e diagnosticare i problemi di infrastruttura e hardware che potrebbero essere generati da un evento di sicurezza.
-   Rilevare i problemi di app e software che potrebbero essere indicatori di compromissione (IoC).
-   Analizzare l'utilizzo delle risorse per prevenire eventi involontari di tipo denial of service.

Il flusso di lavoro generale di monitoraggio e diagnostica è costituito da tre passaggi:

-   **Generazione di eventi**: la generazione di eventi include gli eventi, ovvero log, tracce, eventi personalizzati, sia a livello di infrastruttura, cioè cluster, che a livello di applicazione/servizio. Per comprendere ciò che viene offerto e come aggiungere altri strumenti leggere altre informazioni su [eventi a livello di infrastruttura](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-infra) ed [eventi a livello di applicazione](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app).

-   **Aggregazione di eventi**: gli eventi generati devono essere raccolti e aggregati prima di poter essere visualizzati. È in genere consigliabile usare [Diagnostica di Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) (simile a una raccolta di log basati su agenti) o [EventFlow](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-eventflow) (la raccolta di log in-process).

-   **Analisi**: gli eventi devono essere visualizzati e accessibili in alcuni formati, per consentirne l'analisi e la visualizzazione in base alle esigenze specifiche. Esistono diverse piattaforme per l'analisi e la visualizzazione dei dati di monitoraggio e diagnostica. Le due consigliate sono [Operations Management Suite](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-oms) e [Azure Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights) perché offrono una buona integrazione con Service Fabric.

È possibile usare [Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) per monitorare molte delle risorse di Azure in cui viene creato un cluster di Service Fabric.

Un watchdog è un servizio separato che può controllare l'integrità, il carico tra servizi e creare report di integrità per l'intera gerarchia di modello. L'uso di un watchdog consente di evitare errori che non verrebbero rilevati visualizzando un singolo servizio. 

Anche i watchdog sono utili per ospitare il codice che esegue azioni correttive senza l'intervento dell'utente, ad esempio, la cancellazione dei file di log nell'archiviazione a determinati intervalli di tempo. È possibile trovare un'implementazione di esempio del servizio watchdog in [Azure Service Fabric watchdog sample](https://azure.microsoft.com/resources/samples/service-fabric-watchdog-service/) (Esempio di watchdog Azure Service Fabric).

## <a name="understand-how-to-secure-communication-by-using-certificates"></a>Informazioni su come proteggere le comunicazioni mediante certificati
I certificati consentono di proteggere la comunicazione tra i vari nodi del cluster di Windows autonomo. Utilizzando certificati X.509 è anche possibile autenticare i client che si connettono a questo cluster. In questo modo si garantisce che solo gli utenti autorizzati possono accedere al cluster. Si consiglia di abilitare un certificato nel cluster durante la creazione.

### <a name="x509-certificates-and-service-fabric"></a>Certificati X.509 e Service Fabric
I certificati digitali X.509 vengono comunemente usati per autenticare client e server. Vengono inoltre utilizzati per crittografare e firmare digitalmente i messaggi.

La tabella seguente include un elenco dei certificati necessari per la configurazione del cluster:

|Impostazione Informazioni sul certificato |Descrizione|
|-------------------------------|-----------|
|ClusterCertificate|    Questo certificato è necessario per proteggere la comunicazione tra i nodi di un cluster. È possibile usare due diversi certificati, uno certificato primario e uno secondario per l'aggiornamento.|
|ServerCertificate| Questo certificato viene presentato al client quando tenta di connettersi al cluster. È possibile usare due diversi certificati server, uno certificato primario e uno secondario per l'aggiornamento.|
|ClientCertificateThumbprints|  Si tratta di un set di certificati da installare nei client autenticati.|
|ClientCertificateCommonNames|  Questo è il nome comune del primo certificato client per CertificateCommonName. CertificateIssuerThumbprint è l'identificazione personale dell'autorità emittente del certificato.|
|ReverseProxyCertificate|   Si tratta di un certificato facoltativo che è possibile specificare per proteggere il [proxy inverso](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy).|

Per altre informazioni sulla sicurezza dei certificati, vedere [Proteggere un cluster autonomo in Windows mediante certificati X.509](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security).

## <a name="understand-role-based-access-control"></a>Il controllo degli accessi in base al ruolo
Il Controllo di accesso consente all'amministratore del cluster di limitare l'accesso a determinate operazioni del cluster per diversi gruppi di utenti, rendendo così più sicuro il cluster. Per i client che si connettono a un cluster, sono supportati due tipi di controllo di accesso diversi: 

- Ruolo amministratore
- Ruolo utente

Gli amministratori hanno accesso completo alle funzionalità di gestione, incluse funzionalità di lettura/scrittura. Gli utenti, per impostazione predefinita, hanno solo l'accesso in lettura alle funzionalità di gestione, ad esempio funzionalità di query, e la possibilità di risolvere applicazioni e servizi.

I ruoli client amministratore e utente vengono specificati al momento della creazione del cluster fornendo per ognuno identità separate, inclusi i certificati. Per altre informazioni sulle impostazioni predefinite del controllo di accesso e su come modificarle, vedere [Controllo di accesso basato sui ruoli per i client di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

## <a name="secure-standalone-cluster-by-using-windows-security"></a>Protezione di un cluster mediante la sicurezza di Windows
Per impedire l'accesso non autorizzato a un cluster di Service Fabric, è necessario proteggere il cluster. La sicurezza è importante soprattutto quando il cluster esegue carichi di lavoro di produzione. Questo articolo descrive come configurare la sicurezza da nodo a nodo e da client a nodo usando la sicurezza di Windows nel file ClusterConfig.JSON.

**Configurare la sicurezza di Windows usando l'approccio account del servizio gestito di gruppo**

Quando l'infrastruttura di servizi deve essere eseguita nell'account del servizio gestito di gruppo, la sicurezza da nodo a nodo si configura impostando [ClustergMSAIdentity](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security). Per creare relazioni di trust tra i nodi, è necessario che si riconoscano.

La sicurezza da client a nodo viene configurata usando le identità dei client. Per stabilire l'attendibilità tra un client e il cluster, è necessario configurare il cluster in modo che riconosca quali identità di client può considerare attendibili.

**Configurare la sicurezza di Windows usando un gruppo di computer**

Se si vuole usare un gruppo di computer all'interno di un dominio di Active Directory, la sicurezza da nodo a nodo si configura con ClusterIdentity. Per altre informazioni, vedere l'articolo su come [Creare un gruppo di computer in Active Directory](https://msdn.microsoft.com/library/aa545347).

La sicurezza da client a nodo viene configurata usando le identità dei client. Per stabilire una relazione di trust tra un client e il cluster, è necessario configurare il cluster in modo che riconosca le identità dei client che il cluster può considerare attendibili. È possibile stabilire una relazione di trust in due modi diversi:

-   Specificare gli utenti del gruppo di dominio che possono connettersi.
-   Specificare gli utenti del nodo del dominio che possono connettersi.

## <a name="configure-application-security-in-service-fabric"></a>Configurazione della sicurezza dell'applicazione in Service Fabric
### <a name="manage-secrets-in-service-fabric-applications"></a>Gestire i segreti nelle applicazioni di Service Fabric
Questa modalità consente di gestire i segreti in un'applicazione di Service Fabric. I segreti possono essere informazioni riservate, ad esempio le stringhe di connessione di archiviazione, le password o altri valori che non devono essere gestiti in testo normale.

Questo approccio usa [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) per gestire chiavi e segreti. Tuttavia, l'uso dei segreti in un'applicazione è indipendente dalla piattaforma cloud. Ciò significa che le applicazioni possono essere distribuite a un cluster ospitato in un punto qualsiasi. In questo flusso sono presenti quattro passaggi principali:

-   Ottenere un certificato di crittografia dei dati.
-   Installare il certificato nel cluster.
-   Crittografare i valori dei segreti quando si distribuisce un'applicazione con il certificato e inserirli nel file di configurazione Settings.xml del servizio.
-   Leggere i valori crittografati risultati da Settings. XML descrittandoli con lo stesso certificato di crittografia.

>[!NOTE]
>Per altre informazioni, vedere [Gestione dei segreti nelle applicazioni di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="configure-security-policies-for-your-application"></a>Configurare i criteri di sicurezza per l'applicazione
La sicurezza di Azure Service Fabric aiuta a proteggere le applicazioni che vengono eseguite nel cluster con account utente diversi. La sicurezza di Service Fabric aiuta anche a proteggere le risorse che vengono usate dalle applicazioni in fase di distribuzione con l'account utente, ad esempio file, directory e certificati. In questo modo le applicazioni in esecuzione, anche in un ambiente ospitato condiviso, sono più protette.

I passaggi includono:

-   Configurazione dei criteri per il punto di ingresso dell'installazione del servizio.
-   Avvio dei comandi di PowerShell da un punto di ingresso dell'installazione.
-   Uso del reindirizzamento della console per il debug locale.
-   Configurazione dei criteri per i pacchetti di codice del servizio.
-   Assegnazione dei criteri di accesso di sicurezza per gli endpoint HTTP e HTTPS.

## <a name="secure-communication-for-services-in-azure-service-fabric-security"></a>Protezione della comunicazione per i servizi nella sicurezza di Azure Service Fabric
La sicurezza è uno degli aspetti essenziali delle comunicazioni. Il framework delle applicazioni Reliable Services offre alcuni strumenti e stack di comunicazione predefiniti che è possibile usare per migliorare la sicurezza.

-   [Proteggere un servizio quando si usa la comunicazione remota dei servizi](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication)
-   [Proteggere un servizio quando si usa uno stack di comunicazione basato su WCF](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication#help-secure-a-service-when-youre-using-a-wcf-based-communication-stack)

## <a name="next-steps"></a>Passaggi successivi
- Per informazioni concettuali sulla protezione del cluster, vedere [Creare un cluster di Service Fabric usando Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) e il [portale di Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal).
- Per altre informazioni sulla protezione del cluster in Service Fabric, vedere [Sicurezza di un cluster di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).

