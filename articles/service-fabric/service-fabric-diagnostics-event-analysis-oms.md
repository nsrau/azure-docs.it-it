---
title: Analisi di eventi di Azure Service Fabric con OMS | Microsoft Docs
description: Informazioni sulla visualizzazione e l'analisi di eventi con OMS per il monitoraggio e la diagnostica dei cluster di Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/26/2017
ms.author: dekapur
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: f52988f2a67f2cafe93a7e0a358c599658d068b2
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="event-analysis-and-visualization-with-oms"></a>Analisi e visualizzazione degli eventi con OMS

Operations Management Suite (OMS) è una raccolta di servizi di gestione che consentono di monitorare e diagnosticare le applicazioni e i servizi ospitati nel cloud. Per avere una panoramica più dettagliata di OMS e sui vantaggi offerti, leggere [Informazioni su Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md)

## <a name="log-analytics-and-the-oms-workspace"></a>Area di lavoro di OMS e Log Analytics

Log Analytics raccoglie i dati dalle risorse gestite, tra cui una tabella di archiviazione o un agente di Azure, e li gestisce in un repository centrale. I dati possono essere quindi usati per analisi, avvisi e visualizzazioni o altre esportazioni. Log Analytics supporta i dati sulle prestazioni, sugli eventi o altri dati personalizzati.

Dopo aver configurato OMS, sarà possibile accedere a un'*area di lavoro OMS* specifica, da dove è possibile eseguire le query sui dati o visualizzarli nei dashboard.

Dopo la ricezione dei dati da Log Analytics, OMS dispone di numerose *soluzioni di gestione*, ovvero soluzioni predefinite per monitorare i dati in ingresso, personalizzati in base a diversi scenari. Sono inclusi una soluzione di *Analisi Service Fabric* e una soluzione *contenitori*, le due soluzioni di diagnostica e monitoraggio più importanti se si usano i cluster di Service Fabric. Ce ne sono anche altre che vale la pena esplorare e OMS consente la creazione di soluzioni personalizzate, di cui è possibile avere maggiori informazioni [qui](../operations-management-suite/operations-management-suite-solutions.md). Ogni soluzione che si desidera usare per un cluster verrà configurata nella stessa area di lavoro di OMS, insieme a Log Analytics. Le aree di lavoro consentono di avere dashboard e visualizzazioni personalizzate dei dati, oltre alle modifiche ai dati che si desidera raccogliere, elaborare e analizzare.

## <a name="setting-up-an-oms-workspace-with-the-service-fabric-solution"></a>Configurazione di un'area di lavoro di OMS con la soluzione Service Fabric

È consigliabile includere la soluzione Service Fabric nell'area di lavoro OMS, in quanto offre un dashboard utile che mostra i diversi canali di log in ingresso a livello piattaforma e applicazione ed è in grado di eseguire query sui registri specifici di Service Fabric. Ecco una soluzione di Service Fabric relativamente semplice, con una singola applicazione distribuita nel cluster:

![Soluzione OMS SF](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-solution.png)

Esistono due modi per eseguire il provisioning e configurare un'area di lavoro OMS, tramite un modello di Resource Manager o direttamente da Azure Marketplace. Usare il primo quando si distribuisce un cluster e il secondo se il cluster è già stato distribuito e la diagnostica è attiva.

### <a name="deploying-oms-using-a-resource-management-template"></a>Distribuzione di OMS con il modello di gestione risorse

Ciò si verifica in fase di creazione del cluster, quando si distribuisce un cluster con un modello di Resource Manager, il modello può anche creare una nuova area di lavoro OMS, aggiungervi la soluzione di Service Fabric e configurarlo per leggere i dati dalle tabelle di archiviazione appropriate.

>[!NOTE]
>A tale scopo, è necessario abilitare la diagnostica in modo che le tabelle di archiviazione di Azure possano essere usate da OMS/Log Analytics per leggere altre informazioni.

[Qui](https://azure.microsoft.com/resources/templates/service-fabric-oms/) viene illustrato un modello di esempio che è possibile usare e modificare in base alle necessità e che esegue le azioni descritte sopra. Nel caso in cui si desideri avere maggiori opzioni, esistono altri modelli che consentono di eseguire operazioni diverse a seconda del punto del processo in cui si configura un'area di lavoro OMS: si tratta dei modelli reperibili in [Service Fabric e OMS](https://azure.microsoft.com/resources/templates/?term=service+fabric+OMS).

### <a name="deploying-oms-using-through-azure-marketplace"></a>Distribuzione di OMS tramite Azure Marketplace

Se si desidera aggiungere un'area di lavoro OMS dopo aver distribuito un cluster, passare in Azure Marketplace e cercare *"Analisi Service Fabric"*. Viene visualizzata solo una risorsa nella categoria "Monitoraggio e gestione", illustrata di seguito:

![Analisi SF OMS in Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

Se si fa clic su **Crea** viene richiesta un'area di lavoro OMS. Fare clic su  **	Selezionare un'area di lavoro** e quindi **Crea una nuova area di lavoro**. Compilare le voci necessarie: l'unico requisito è che la sottoscrizione per il cluster di Service Fabric e l'area di lavoro OMS devono essere gli stessi. Dopo aver convalidato le voci, l'area di lavoro OMS verrà distribuita in pochi minuti. Mentre viene completata la distribuzione, la creazione del pannello di soluzioni di Service Fabric rimarrà aperto. Assicurarsi che la stessa area di lavoro venga visualizzata nell'*area di lavoro OMS* e fare clic su **Crea** in basso per aggiungere la soluzione Service Fabric all'area di lavoro.

## <a name="using-the-oms-agent"></a>Uso dell'agente OMS

È consigliabile usare EventFlow e WAD come soluzioni di aggregazione, in quanto consentono un approccio più modulare alla diagnostica e al monitoraggio. Ad esempio, se si desidera modificare l'output da EventFlow, non è necessaria alcuna modifica per la strumentazione effettiva, solo una semplice modifica al file di configurazione. Se, tuttavia, si decide di investire nell'uso di OMS e si desidera continuare a usarlo per l'analisi di eventi, non essendo l'unica piattaforma usata, ma almeno una delle piattaforme, è consigliabile esplorare la configurazione dell'[agente OMS](../log-analytics/log-analytics-windows-agents.md). È anche necessario usare l'agente OMS durante la distribuzione di contenitori nel cluster, come descritto di seguito.

Il processo per eseguire questa operazione è relativamente semplice, poiché è sufficiente aggiungere l'agente come estensione del set di scalabilità di macchine virtuali al modello di Resource Manager, verificando che venga installato su ogni nodo. Un esempio di modello di Resource Manager che distribuisce l'area di lavoro di OMS con la soluzione Service Fabric, come mostrato sopra, e aggiunge l'agente ai nodi è reperibile per i cluster che eseguono [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) o [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux).

I vantaggi sono i seguenti:

* Dati più ricchi sul lato dei contatori e delle metriche delle prestazioni
* Dati facile da configurare raccolti dal cluster e modificati senza ridistribuire le applicazioni o il cluster, poiché è possibile apportare modifiche alle impostazioni dell'agente dall'area di lavoro OMS e l'agente verrà ripristinato automaticamente. Per configurare l'agente OMS per prelevare contatori delle prestazioni specifici andare nella **home page dell'area di lavoro > Impostazioni > Dati > Contatori delle prestazioni di Windows** e scegliere i dati che si desidera raccogliere
* I dati vengono visualizzati più velocemente di quando vengono archiviati prima di essere prelevati da OMS/Log Analytics
* Il monitoraggio dei contenitori è molto più semplice, poiché può prelevare log, ovvero stdout, stderror, e registri di docker, ovvero metriche di prestazioni sui livelli di contenitori e nodi

In questo caso la considerazione principale è che poiché si tratta di un agente, verrà distribuito il cluster insieme a tutte le applicazioni, pertanto l'impatto sulle prestazioni delle applicazioni nel cluster sarà minimo.

## <a name="monitoring-containers"></a>Monitoraggio di contenitori

Durante la distribuzione de contenitori in un cluster di Service Fabric, è consigliabile configurare il cluster con l'agente OMS e che la soluzione dei contenitori sia stata aggiunta all'area di lavoro OMS per abilitare il monitoraggio e la diagnostica. Ecco l'aspetto della soluzione dei contenitori in un'area di lavoro:

![Dashboard OMS di base](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

L'agente consente la raccolta di diversi log specifici per il contenitore che possono essere sottoposti a query in OMS o usati per gli indicatori di prestazioni visualizzati. I tipi di log raccolti sono:

* ContainerInventory: informazioni su posizione, nome e immagini dei contenitori
* ContainerImageInventory: informazioni sulle immagini distribuite, inclusi ID o dimensioni
* ContainerLog: log degli errori specifici, log Docker (stdout e così via) e altre voci
* ContainerServiceLog: comandi del daemon Docker che sono stati eseguiti
* Perf: contatori delle prestazioni, inclusi cpu, memoria, traffico di rete, I/O del disco e metriche personalizzate dei contenitori dai computer host

Questo articolo illustra i passaggi necessari per configurare il monitoraggio dei contenitori per il cluster. Per altre informazioni sulla soluzione Contenitori di OMS, vedere la relativa [documentazione](../log-analytics/log-analytics-containers.md).

Per configurare la soluzione del contenitore nell'area di lavoro, assicurarsi che l'agente OMS sia stato distribuito nei nodi del cluster seguendo i passaggi indicati in precedenza. Quando il cluster è pronto, distribuirvi un contenitore. Tenere presente che la prima volta che un'immagine del contenitore viene distribuita in un cluster, sono necessari alcuni minuti per scaricare l'immagine, a seconda delle dimensioni.

In Azure Marketplace cercare *Soluzione Monitoraggio contenitori* e creare il risultato **Soluzione Monitoraggio contenitori** che dovrebbe comparire nella categoria Monitoraggio e gestione.

![Aggiunta della soluzione Contenitori](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

Nel passaggio di creazione viene richiesta un'area di lavoro di OMS. Selezionare l'area creata con la distribuzione precedente. Questo passaggio aggiunge una soluzione Contenitori nell'area di lavoro di OMS e viene rilevato automaticamente dall'agente OMS distribuito dal modello. L'agente inizierà a raccogliere dati nei processi dei contenitori nel cluster e, in meno di 15 minuti, si dovrebbe vedere la soluzione con i dati come nell'immagine del dashboard precedente.


## <a name="next-steps"></a>Passaggi successivi

È possibile esaminare gli strumenti e le opzioni OMS seguenti per personalizzare un'area di lavoro in base alle proprie esigenze:

* Per i cluster locali, OMS offre un Gateway, ovvero un proxy di inoltro HTTP, che può essere usato per inviare i dati a OMS. Per altre informazioni leggere [Connettere computer senza accesso a Internet a OMS usando il gateway OMS](../log-analytics/log-analytics-oms-gateway.md)
* Configurare OMS per gli [avvisi automatizzati](../log-analytics/log-analytics-alerts.md) per semplificare il rilevamento e la diagnostica
* Acquisire familiarità con le funzionalità di [ricerca log e query](../log-analytics/log-analytics-log-searches.md) incluse in Log Analytics
