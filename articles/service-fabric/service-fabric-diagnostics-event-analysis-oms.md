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
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 977c5d64a32157b39aa6b618196dde20c4c3cc8e
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2017
---
# <a name="event-analysis-and-visualization-with-oms"></a>Analisi e visualizzazione degli eventi con OMS

Operations Management Suite (OMS) è una raccolta di servizi di gestione che consentono di monitorare e diagnosticare le applicazioni e i servizi ospitati nel cloud. Per avere una panoramica più dettagliata di OMS e sui vantaggi offerti, leggere [Informazioni su Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md)

## <a name="log-analytics-and-the-oms-workspace"></a>Area di lavoro di OMS e Log Analytics

Log Analytics raccoglie i dati dalle risorse gestite, tra cui una tabella di archiviazione o un agente di Azure, e li gestisce in un repository centrale. I dati possono essere quindi usati per analisi, avvisi e visualizzazioni o altre esportazioni. Log Analytics supporta i dati sulle prestazioni, sugli eventi o altri dati personalizzati.

Dopo aver configurato OMS, sarà possibile accedere a un'*area di lavoro OMS* specifica, da dove è possibile eseguire le query sui dati o visualizzarli nei dashboard.

Dopo la ricezione dei dati da Log Analytics, OMS dispone di numerose *soluzioni di gestione*, ovvero soluzioni predefinite per monitorare i dati in ingresso, personalizzati in base a diversi scenari. Sono inclusi una soluzione di *Analisi Service Fabric* e una soluzione *contenitori*, le due soluzioni di diagnostica e monitoraggio più importanti se si usano i cluster di Service Fabric. Ce ne sono anche altre che vale la pena esplorare e OMS consente la creazione di soluzioni personalizzate, di cui è possibile avere maggiori informazioni [qui](../operations-management-suite/operations-management-suite-solutions.md). Ogni soluzione che si sceglie di usare per un cluster può essere configurata nella stessa area di lavoro OMS, insieme a Log Analytics. Le aree di lavoro consentono di avere dashboard e visualizzazioni personalizzate dei dati, oltre alle modifiche ai dati che si desidera raccogliere, elaborare e analizzare.

## <a name="setting-up-an-oms-workspace-with-the-service-fabric-analytics-solution"></a>Configurazione di un'area di lavoro OMS con la soluzione Analisi Service Fabric
È consigliabile includere la soluzione Service Fabric nell'area di lavoro OMS, in quanto offre un dashboard che mostra i diversi canali di log in ingresso a livello di piattaforma e di applicazione e consente di eseguire query su log specifici di Service Fabric. Ecco una soluzione di Service Fabric relativamente semplice, con una singola applicazione distribuita nel cluster:

![Soluzione OMS SF](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-solution.png)

Vedere [Configurare Log Analytics di OMS](service-fabric-diagnostics-oms-setup.md) per iniziare con questa soluzione per il cluster.

## <a name="using-the-oms-agent"></a>Uso dell'agente OMS

È consigliabile usare EventFlow e WAD come soluzioni di aggregazione, in quanto consentono un approccio più modulare alla diagnostica e al monitoraggio. Ad esempio, se si desidera modificare l'output da EventFlow, non è necessaria alcuna modifica per la strumentazione effettiva, solo una semplice modifica al file di configurazione. Se, tuttavia, si decide di usare Log Analytics di OMS, è necessario impostare l'[agente di OMS](../log-analytics/log-analytics-windows-agent.md). È anche necessario usare l'agente OMS durante la distribuzione di contenitori nel cluster, come descritto di seguito. 

Per altre informazioni su questa procedura, vedere [Aggiungere l'agente di OMS a un cluster](service-fabric-diagnostics-oms-agent.md).

I vantaggi sono i seguenti:

* Dati più ricchi sul lato dei contatori e delle metriche delle prestazioni
* Metriche facili da configurare raccolte dal cluster e senza la necessità di aggiornare la configurazione del cluster. Le modifiche alle impostazioni dell'agente possono essere effettuate dal portale di OMS e l'agente viene riavviato automaticamente per applicare la configurazione necessaria. Per configurare l'agente OMS per prelevare contatori delle prestazioni specifici andare nella **home page dell'area di lavoro > Impostazioni > Dati > Contatori delle prestazioni di Windows** e scegliere i dati che si desidera raccogliere
* I dati vengono visualizzati più velocemente di quando vengono archiviati prima di essere prelevati da OMS/Log Analytics
* Il monitoraggio dei contenitori è molto più semplice, perché consente di scegliere log Docker (stdout, stderror) e statistiche (metriche delle prestazioni a livello di contenitori e nodi)

In questo caso la considerazione principale è che, poiché l'agente verrà distribuito nel cluster insieme a tutte le applicazioni, potrebbe esserci un impatto sulle prestazioni delle applicazioni nel cluster.

## <a name="monitoring-containers"></a>Monitoraggio di contenitori

Durante la distribuzione de contenitori in un cluster di Service Fabric, è consigliabile configurare il cluster con l'agente OMS e che la soluzione dei contenitori sia stata aggiunta all'area di lavoro OMS per abilitare il monitoraggio e la diagnostica. Ecco l'aspetto della soluzione dei contenitori in un'area di lavoro:

![Dashboard OMS di base](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

L'agente consente la raccolta di diversi log specifici per il contenitore che possono essere sottoposti a query in OMS o usati per gli indicatori di prestazioni visualizzati. I tipi di log raccolti sono:

* ContainerInventory: informazioni su posizione, nome e immagini dei contenitori
* ContainerImageInventory: informazioni sulle immagini distribuite, inclusi ID o dimensioni
* ContainerLog: log degli errori specifici, log Docker (stdout e così via) e altre voci
* ContainerServiceLog: comandi del daemon Docker che sono stati eseguiti
* Perf: contatori delle prestazioni, inclusi cpu, memoria, traffico di rete, I/O del disco e metriche personalizzate dei contenitori dai computer host

L'articolo [Monitorare i contenitori con Log Analytics di OMS](service-fabric-diagnostics-oms-containers.md) illustra i passaggi necessari per configurare il monitoraggio dei contenitori per il cluster. Per altre informazioni sulla soluzione Contenitori di OMS, vedere la relativa [documentazione](../log-analytics/log-analytics-containers.md).

## <a name="next-steps"></a>Passaggi successivi

È possibile esaminare gli strumenti e le opzioni OMS seguenti per personalizzare un'area di lavoro in base alle proprie esigenze:

* Per i cluster locali, OMS offre un Gateway, ovvero un proxy di inoltro HTTP, che può essere usato per inviare i dati a OMS. Per altre informazioni leggere [Connettere computer senza accesso a Internet a OMS usando il gateway OMS](../log-analytics/log-analytics-oms-gateway.md)
* Configurare OMS per gli [avvisi automatizzati](../log-analytics/log-analytics-alerts.md) per semplificare il rilevamento e la diagnostica
* Acquisire familiarità con le funzionalità di [ricerca log e query](../log-analytics/log-analytics-log-searches.md) incluse in Log Analytics