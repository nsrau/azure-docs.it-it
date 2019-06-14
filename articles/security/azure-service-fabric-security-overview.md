---
title: Panoramica della sicurezza di Azure Service Fabric | Microsoft Docs
description: Questo articolo offre una panoramica della sicurezza di Azure Service Fabric.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.openlocfilehash: c5b5f80a43530fe6d0b90e65c3aef89a815157e4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60610901"
---
# <a name="azure-service-fabric-security-overview"></a>Panoramica della sicurezza di Azure Service Fabric
[Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) è una piattaforma di sistemi distribuiti che semplifica la creazione di pacchetti, la distribuzione e la gestione di microservizi scalabili e affidabili. Service Fabric fa fronte alle problematiche correlate allo sviluppo e alla gestione delle applicazioni cloud. Gli sviluppatori e gli amministratori non devono più occuparsi di risolvere complessi problemi di infrastruttura e possono concentrarsi sull'implementazione di carichi di lavoro cruciali e impegnativi, scalabili e affidabili.

Questo articolo offre una panoramica delle considerazioni sulla sicurezza per una distribuzione di Service Fabric.

## <a name="secure-your-cluster"></a>Garantire la sicurezza del cluster
Azure Service Fabric orchestra i servizi in un cluster di computer. Per impedire a utenti non autorizzati di connettersi ai cluster, è necessario proteggere i cluster, in particolare quando sono in esecuzione carichi di lavoro di produzione. La creazione di cluster non protetti, anche se possibile, potrebbe consentire a utenti anonimi di connettersi al cluster se questo espone endpoint di gestione a Internet pubblico.

Per i cluster che vengono eseguiti in modalità autonoma o in Azure, due scenari da considerare sono la sicurezza da nodo a nodo e la sicurezza da client a nodo. È possibile usare varie tecnologie per implementare tali scenari.

### <a name="node-to-node-security"></a>Sicurezza da nodo a nodo
La sicurezza da nodo a nodo si applica alla comunicazione tra le VM o i computer del cluster. Con la sicurezza da nodo a nodo solo i computer autorizzati a connettersi al cluster possono partecipare all'hosting di applicazioni e servizi nel cluster.

I cluster eseguiti in Azure o i cluster autonomi eseguiti in Windows possono usare la [sicurezza basata su certificati](https://msdn.microsoft.com/library/ff649801.aspx) o la [sicurezza di Windows](https://msdn.microsoft.com/library/ff649396.aspx) per computer Windows Server.

#### <a name="node-to-node-certificate-security"></a>Sicurezza basata su certificati da nodo a nodo

Service Fabric usa i certificati server X.509 specificati quando si crea un cluster. Per una rapida panoramica di questi certificati e di come è possibile acquisirli o crearli, vedere [Utilizzo dei certificati](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates).

La sicurezza basata su certificati viene configurata durante la creazione del cluster tramite il portale di Azure, i modelli di Azure Resource Manager o un modello JSON autonomo. È possibile specificare un certificato primario e un certificato secondario facoltativo che viene usato per gli effetti di attivazione dei certificati. I certificati primario e secondario specificati devono essere diversi dai certificati client amministratore e dai certificati client di sola lettura specificati per la [sicurezza da client a nodo](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).

### <a name="client-to-node-security"></a>Sicurezza da client a nodo
La sicurezza da client a nodo viene configurata usando le identità dei client. Per stabilire l'attendibilità tra un client e un cluster, è necessario configurare il cluster in modo che riconosca quali identità di client può considerare attendibili.

Service Fabric supporta due tipi di controllo di accesso per i client connessi a un cluster di Service Fabric:

-   **Amministratore**: accesso completo alle funzionalità di gestione, incluse le funzionalità di lettura/scrittura.
-   **Utente**: accesso in sola lettura alle funzionalità di gestione, ad esempio le funzionalità di query, e alla funzionalità per risolvere applicazioni e servizi.

Tramite il controllo di accesso, gli amministratori di cluster possono limitare l'accesso a determinati tipi di operazioni del cluster. In questo modo il cluster è più sicuro.

#### <a name="client-to-node-certificate-security"></a>Sicurezza basata su certificati da client a nodo

La sicurezza basata su certificati da client a nodo viene configurata durante la creazione di un cluster tramite il portale di Azure, i modelli di Resource Manager o un modello JSON autonomo. È necessario specificare un certificato client amministratore e/o un certificato client utente. Assicurarsi che questi certificati siano diversi dai certificati primario e secondario specificati per la sicurezza da nodo a nodo.

I client che si connettono al cluster con il certificato amministratore hanno accesso completo alle funzionalità di gestione. I client che si connettono al cluster con il certificato client utente di sola lettura hanno solo l'accesso in lettura alle funzionalità di gestione. Questi certificati vengono, in altre parole, usati per il controllo di accesso in base al ruolo.

Per informazioni su come configurare la sicurezza basata su certificati in un cluster, vedere [Configurare un cluster di Service Fabric usando un modello di Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

#### <a name="client-to-node-azure-active-directory-security"></a>Sicurezza di Azure Active Directory da client a nodo

I cluster eseguiti in Azure possono proteggere anche l'accesso agli endpoint di gestione usando Azure Active Directory (Azure AD). Per informazioni su come creare i necessari elementi di Azure Active Directory, su come popolarli durante la creazione dei cluster e su come connettersi a tali cluster in seguito, vedere [Configurare un cluster di Service Fabric usando un modello di Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

Azure AD consente alle organizzazioni (note come tenant) di gestire l'accesso degli utenti alle applicazioni. Alcune applicazioni sono caratterizzate da un'interfaccia utente di accesso basata sul Web, altre invece da un'esperienza client nativa.

Un cluster di Service Fabric offre numerosi punti di ingresso alle relative funzionalità di gestione, tra cui Service Fabric Explorer basato sul Web e Visual Studio. Si creano quindi due applicazioni Azure AD per controllare l'accesso al cluster: un'applicazione Web e un'applicazione nativa.

Per i cluster di Azure si consiglia di usare la sicurezza di Azure AD per l'autenticazione dei client e dei certificati per la sicurezza da nodo a nodo.

Per i cluster di Windows Server autonomi con Windows Server 2012 R2 e Active Directory si consiglia di usare la sicurezza di Windows con account del servizio gestito di gruppo. In caso contrario, usare la sicurezza di Windows con account di Windows.

## <a name="understand-monitoring-and-diagnostics-in-service-fabric"></a>Monitoraggio e diagnostica in Azure Service Fabric
Il [monitoraggio e la diagnostica](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) sono essenziali per lo sviluppo, i test e la distribuzione di applicazioni e servizi in qualsiasi ambiente. Le soluzioni di Service Fabric funzionano meglio quando si implementa il monitoraggio e la diagnostica per garantire che le applicazioni e i servizi funzionino come previsto in un ambiente di sviluppo locale o in fase di produzione.

Dal punto di vista della sicurezza, gli obiettivi principali del monitoraggio e della diagnostica sono:

-   Rilevare e diagnosticare i problemi di infrastruttura e hardware che potrebbero essere generati da un evento di sicurezza.
-   Rilevare i problemi di app e software che potrebbero essere indicatori di compromissione (IoC).
-   Analizzare l'utilizzo delle risorse per prevenire eventi involontari di tipo denial of service.

Il flusso di lavoro relativo al monitoraggio e alla diagnostica è costituito da tre passaggi:

1.  **Generazione di eventi**: la generazione di eventi include gli eventi, ovvero log, tracce, eventi personalizzati, sia a livello di infrastruttura, cioè cluster, che a livello di applicazione/servizio. Per comprendere ciò che viene offerto e come aggiungere altri strumenti, leggere altre informazioni su [eventi a livello di infrastruttura](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-infra) ed [eventi a livello di applicazione](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app).

2.  **Aggregazione di eventi**: gli eventi generati devono essere raccolti e aggregati prima di poter essere visualizzati. È in genere consigliabile usare [Diagnostica di Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) (simile a una raccolta di log basati su agenti) o [EventFlow](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-eventflow) (raccolta di log in-process).

3.  **Analisi**: gli eventi devono essere visualizzati e accessibili in alcuni formati, per consentirne l'analisi e la visualizzazione. Esistono diverse piattaforme per l'analisi e la visualizzazione dei dati di monitoraggio e diagnostica. È consigliabile [log di monitoraggio di Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-oms) e [Azure Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights) perché si integrano bene con Service Fabric.

È possibile usare [Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) per monitorare molte delle risorse di Azure con cui viene creato un cluster di Service Fabric.

Un watchdog è un servizio separato che può controllare l'integrità e il carico tra servizi e creare report di integrità per l'intera gerarchia di modello. L'uso di un watchdog consente di evitare errori che non verrebbero rilevati visualizzando un singolo servizio. 

I watchdog sono anche adatti a ospitare codice che esegue azioni correttive senza l'intervento dell'utente. Un esempio è la pulitura dei file di log nell'archiviazione a determinati intervalli di tempo. È possibile trovare un'implementazione di esempio del servizio watchdog in [Azure Service Fabric watchdog sample](https://azure.microsoft.com/resources/samples/service-fabric-watchdog-service/) (Esempio di watchdog Azure Service Fabric).

## <a name="secure-communication-by-using-certificates"></a>Protezione delle comunicazioni mediante certificati
I certificati aiutano a proteggere la comunicazione tra i vari nodi del cluster di Windows autonomo. Se si usano i certificati X.509, è anche possibile autenticare i client che si connettono a questo cluster. In questo modo si garantisce che solo gli utenti autorizzati possano accedere al cluster. Si consiglia di abilitare un certificato nel cluster durante la creazione del cluster.

I certificati digitali X.509 vengono comunemente usati per autenticare client e server. Vengono inoltre usati per crittografare e firmare digitalmente i messaggi.

La tabella seguente include un elenco dei certificati necessari per la configurazione del cluster:

|Impostazione CertificateInformation |Descrizione|
|-------------------------------|-----------|
|ClusterCertificate|    Questo certificato è necessario per proteggere la comunicazione tra i nodi di un cluster. È possibile usare due certificati di cluster: uno primario e uno secondario per l'aggiornamento.|
|ServerCertificate| Questo certificato viene presentato al client quando tenta di connettersi al cluster. È possibile usare due certificati server: uno primario e uno secondario per l'aggiornamento.|
|ClientCertificateThumbprints|  Si tratta di un set di certificati da installare nei client autenticati.|
|ClientCertificateCommonNames|  Questo è il nome comune del primo certificato client per CertificateCommonName. CertificateIssuerThumbprint è l'identificazione personale dell'autorità emittente del certificato.|
|ReverseProxyCertificate|   Si tratta di un certificato facoltativo che è possibile specificare per proteggere il [proxy inverso](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy).|

Per altre informazioni sulla sicurezza dei certificati, vedere [Proteggere un cluster autonomo in Windows mediante certificati X.509](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security).

## <a name="understand-role-based-access-control"></a>Controllo degli accessi in base al ruolo
I ruoli client amministratore e utente si specificano al momento della creazione del cluster indicando per ognuno identità separate, inclusi i certificati. Per altre informazioni sulle impostazioni predefinite del controllo di accesso e su come modificarle, vedere [Controllo di accesso basato sui ruoli per i client di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

## <a name="secure-standalone-clusters-by-using-windows-security"></a>Proteggere i cluster autonomi mediante la sicurezza di Windows
Per impedire l'accesso non autorizzato a un cluster di Service Fabric, è necessario proteggere il cluster. La sicurezza è importante soprattutto quando il cluster esegue carichi di lavoro di produzione. Occorre configurare la sicurezza da nodo a nodo e da client a nodo usando la sicurezza di Windows nel file ClusterConfig.JSON.

Quando Service Fabric deve essere eseguito in un account del servizio gestito di gruppo, la sicurezza da nodo a nodo si configura impostando [ClustergMSAIdentity](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security). Per creare relazioni di trust tra i nodi, è necessario che si riconoscano tra loro.

Se si vuole usare un gruppo di computer all'interno di un dominio di Active Directory, la sicurezza da nodo a nodo si configura con ClusterIdentity. Per altre informazioni, vedere l'articolo su come [Creare un gruppo di computer in Active Directory](https://msdn.microsoft.com/library/aa545347).

La sicurezza da client a nodo viene configurata usando le identità dei client. È necessario configurare il cluster in modo che riconosca quali identità di client può considerare attendibili. È possibile stabilire una relazione di trust in due modi:

-   Specificare gli utenti del gruppo di dominio che possono connettersi.
-   Specificare gli utenti del nodo del dominio che possono connettersi.

## <a name="configure-application-security-in-service-fabric"></a>Configurazione della sicurezza dell'applicazione in Service Fabric
### <a name="manage-secrets-in-service-fabric-applications"></a>Gestire i segreti nelle applicazioni di Service Fabric
I segreti possono essere informazioni riservate, ad esempio le stringhe di connessione di archiviazione, le password o altri valori che non devono essere gestiti in testo normale.

È possibile usare [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) per gestire le chiavi e i segreti. L'uso dei segreti in un'applicazione non si basa tuttavia su una piattaforma cloud specifica. È possibile distribuire applicazioni a un cluster ospitato in una posizione qualsiasi. In questo flusso sono presenti quattro passaggi principali:

1.  Ottenere un certificato di crittografia dei dati.
2.  Installare il certificato nel cluster.
3.  Crittografare i valori dei segreti quando si distribuisce un'applicazione con il certificato e inserirli nel file di configurazione Settings.xml del servizio.
4.  Leggere i valori crittografati presenti nel file Settings.xml decrittandoli con lo stesso certificato di crittografia.

Per altre informazioni, vedere [Gestione dei segreti nelle applicazioni di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="configure-security-policies-for-an-application"></a>Configurare i criteri di sicurezza per un'applicazione
La sicurezza di Azure Service Fabric aiuta a proteggere le applicazioni che vengono eseguite nel cluster con account utente diversi. La sicurezza di Service Fabric aiuta anche a proteggere le risorse, ad esempio file, directory e certificati, che le applicazioni usano in fase di distribuzione con gli account utente. In questo modo le applicazioni in esecuzione, anche in un ambiente ospitato condiviso, sono più protette.

Le attività per la configurazione dei criteri di sicurezza includono:

-   Configurazione dei criteri per un punto di ingresso dell'installazione del servizio
-   Avvio dei comandi di PowerShell da un punto di ingresso dell'installazione
-   Uso del reindirizzamento della console per il debug locale
-   Configurazione dei criteri per i pacchetti di codice del servizio
-   Assegnazione dei criteri di accesso di sicurezza per gli endpoint HTTP e HTTPS

## <a name="secure-communication-for-services"></a>Proteggere le comunicazioni per i servizi
La sicurezza è uno degli aspetti essenziali della comunicazione. Il framework di applicazioni di Reliable Services offre alcuni stack e strumenti predefiniti che è possibile usare per migliorare la sicurezza. Per altre informazioni, vedere [Proteggere un servizio quando si usa la comunicazione remota dei servizi](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication).

## <a name="next-steps"></a>Passaggi successivi
- Per informazioni concettuali sulla protezione del cluster, vedere [Creare un cluster di Service Fabric usando Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) e [Creare un cluster di Service Fabric usando il portale di Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal).
- Per altre informazioni sulla protezione del cluster in Service Fabric, vedere [Scenari di sicurezza di un cluster di Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).
