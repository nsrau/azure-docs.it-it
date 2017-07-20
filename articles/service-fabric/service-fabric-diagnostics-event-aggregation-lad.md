---
title: Aggregazione di eventi di Azure Service Fabric con Diagnostica di Azure per Linux | Microsoft Docs
description: Informazioni sull&quot;aggregazione e la raccolta di eventi con LAD per il monitoraggio e la diagnostica dei cluster di Azure Service Fabric.
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 1152893c4c686fa69f7e06ffa7e2d2b2272bc772
ms.contentlocale: it-it
ms.lasthandoff: 05/31/2017


---

# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>Aggregazione e raccolta di eventi con Diagnostica di Azure per Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Quando si esegue un cluster Azure Service Fabric, è consigliabile raccogliere i log da tutti i nodi in una posizione centrale. Il salvataggio dei log in una posizione centrale semplifica l'analisi e la risoluzione di eventuali problemi nel cluster o nelle applicazioni e nei servizi in esecuzione nel cluster.

Un modo per caricare e raccogliere i registri consiste nell'usare l'estensione Diagnostica di Azure per Linux, ovvero LAD, che consente di caricare i registri nell'Archiviazione di Azure e di inviarli ad Azure Application Insights o Hub eventi. È anche possibile usare un processo esterno per leggere gli eventi dalla risorsa di archiviazione e inserirli in una piattaforma di analisi come [Log Analytics di OMS](../log-analytics/log-analytics-service-fabric.md) o un'altra soluzione di analisi di registrazione.

## <a name="log-and-event-sources"></a>Origini di eventi e registri

### <a name="service-fabric-infrastructure-events"></a>Eventi dell'infrastruttura di Service Fabric
Service Fabric emette alcuni registri pronti all'uso tramite [LTTng](http://lttng.org), inclusi gli eventi operativi o gli eventi di runtime. Questi log vengono archiviati nel percorso specificato dal cluster del modello di Resource Manager. Per ottenere e impostare i dettagli dell'account di archiviazione, cercare il tag **AzureTableWinFabETWQueryable** e quindi **StoreConnectionString**.

### <a name="application-events"></a>Eventi dell'applicazione
 Eventi generati dal codice delle applicazioni e dei servizi come specificato dall'utente durante la strumentazione del software. È possibile usare qualsiasi soluzione di registrazione che scriva file di log basati su testo, ad esempio LTTng. Per altre informazioni, vedere la documentazione di LTTng relativa alla traccia dell'applicazione.

[Monitorare e diagnosticare servizi in una configurazione di sviluppo con computer locale](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

## <a name="deploy-the-diagnostics-extension"></a>Distribuire l'estensione Diagnostica
Il primo passaggio per la raccolta dei log consiste nel distribuire l'estensione Diagnostica in ogni VM del cluster Service Fabric. Questa estensione raccoglie i log in ogni VM e li carica nell'account di archiviazione specificato. La procedura varia a seconda che si usi il portale di Azure oppure Azure Resource Manager.

Per distribuire l'estensione di diagnostica nelle VM del cluster come parte della creazione del cluster, impostare **Diagnostica** su **Attiva**. Dopo aver creato il cluster, è possibile modificare questa impostazione tramite il portale.

Configurare quindi l'estensione Diagnostica di Azure per Linux per raccogliere i file e inserirli nel proprio account di archiviazione. Questo processo viene illustrato nello scenario 3 ("Caricamento dei propri file di log") descritto nell'articolo relativo all' [uso dell'estensione Diagnostica di Azure per Linux per il monitoraggio e la diagnosi di VM Linux](../virtual-machines/linux/classic/diagnostic-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json). Questo processo consente di ottenere l'accesso alle tracce. È possibile anche caricare le tracce in un visualizzatore a scelta.

È anche possibile distribuire l'estensione di Diagnostica con Azure Resource Manager. Il processo è simile per Windows e per Linux ed è documentato per i cluster Windows nell'articolo [Come raccogliere log con Diagnostica di Azure](service-fabric-diagnostics-how-to-setup-wad.md).

È anche possibile usare Operations Management Suite, come descritto in [Operations Management Suite Log Analytics with Linux](https://blogs.technet.microsoft.com/hybridcloud/2016/01/28/operations-management-suite-log-analytics-with-linux/) (Analisi dei log di Operations Management Suite con Linux).

Dopo aver terminato la configurazione, l'agente di Diagnostica di Azure per Linux monitora i file di log specificati. Ogni volta che viene aggiunta una nuova riga al file, l'agente crea una voce syslog che viene inviata alla risorsa di archiviazione specificata dall'utente.

## <a name="next-steps"></a>Passaggi successivi

Per comprendere più nel dettaglio gli eventi da esaminare durante la risoluzione dei problemi, vedere la [documentazione di LTTng](http://lttng.org/docs) e l'articolo relativo all'[uso dell'estensione Diagnostica di Azure per Linux](../virtual-machines/linux/classic/diagnostic-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).
