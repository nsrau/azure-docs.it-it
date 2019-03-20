---
title: Aggregazione di eventi di Azure Service Fabric con Diagnostica di Azure per Linux | Microsoft Docs
description: Informazioni sull'aggregazione e la raccolta di eventi con LAD per il monitoraggio e la diagnostica dei cluster di Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: ca6be2b08c9a38813b2c98eef7e5696bd61fd074
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57893034"
---
# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>Aggregazione e raccolta di eventi con Diagnostica di Azure per Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Quando si esegue un cluster Azure Service Fabric, è consigliabile raccogliere i log da tutti i nodi in una posizione centrale. Il salvataggio dei log in una posizione centrale semplifica l'analisi e la risoluzione di eventuali problemi nel cluster o nelle applicazioni e nei servizi in esecuzione nel cluster.

Un modo per caricare e raccogliere i registri consiste nell'usare l'estensione Diagnostica di Azure per Linux, ovvero LAD, che consente di caricare i registri nell'Archiviazione di Azure e di inviarli ad Azure Application Insights o Hub eventi. È anche possibile usare un processo esterno per leggere gli eventi di archiviazione e inserirli in una piattaforma di analisi come [monitoraggio di Azure registra](../log-analytics/log-analytics-service-fabric.md) o un'altra soluzione di analisi di log.

## <a name="log-and-event-sources"></a>Origini di log ed eventi

### <a name="service-fabric-platform-events"></a>Eventi della piattaforma Service Fabric
Service Fabric emette alcuni registri pronti all'uso tramite [LTTng](https://lttng.org), inclusi gli eventi operativi o gli eventi di runtime. Questi log vengono archiviati nel percorso specificato dal cluster del modello di Resource Manager. Per ottenere e impostare i dettagli dell'account di archiviazione, cercare il tag **AzureTableWinFabETWQueryable** e quindi **StoreConnectionString**.

### <a name="application-events"></a>Eventi dell'applicazione
 Eventi generati dal codice delle applicazioni e dei servizi come specificato dall'utente durante la strumentazione del software. È possibile usare qualsiasi soluzione di registrazione che scriva file di log basati su testo, ad esempio LTTng. Per altre informazioni, vedere la documentazione di LTTng relativa alla traccia dell'applicazione.

[Monitorare e diagnosticare servizi in una configurazione di sviluppo con computer locale](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

## <a name="deploy-the-diagnostics-extension"></a>Distribuire l'estensione Diagnostica
Il primo passaggio per la raccolta dei log consiste nel distribuire l'estensione Diagnostica in ogni VM del cluster Service Fabric. Questa estensione raccoglie i log in ogni VM e li carica nell'account di archiviazione specificato. 

Per distribuire l'estensione di diagnostica nelle VM del cluster come parte della creazione del cluster, impostare **Diagnostica** su **Attiva**. Dopo aver creato il cluster, non è possibile modificare questa impostazione tramite il portale. Apportare le modifiche necessarie con il modello di Gestione risorse.

In questo modo si configura l'agente LAD per monitorare i file di log specificati. Ogni volta che viene aggiunta una nuova riga al file, l'agente crea una voce syslog che viene inviata alla risorsa di archiviazione (tabella) specificata dall'utente.


## <a name="next-steps"></a>Passaggi successivi

1. Per comprendere più nel dettaglio gli eventi da esaminare durante la risoluzione dei problemi, vedere la [documentazione di LTTng](https://lttng.org/docs) e l'articolo relativo all'[uso dell'estensione Diagnostica di Azure per Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/diagnostics-linux).
2. [Configurare l'agente di Log Analytics](service-fabric-diagnostics-event-analysis-oms.md) per semplificare la raccolta di metriche, monitorare i contenitori distribuiti nel cluster e visualizzare i log. 
