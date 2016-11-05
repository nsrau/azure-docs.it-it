---
title: Raccogliere log con Diagnostica di Azure per Linux | Microsoft Docs
description: Questo articolo descrive come configurare Diagnostica di Azure per raccogliere log da un cluster Linux di Service Fabric in esecuzione in Azure.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/28/2016
ms.author: subramar

---
# <a name="collect-logs-by-using-azure-diagnostics"></a>Raccogliere log con Diagnostica di Azure
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
> * [Linux](service-fabric-diagnostics-how-to-setup-lad.md)
> 
> 

Quando si esegue un cluster Azure Service Fabric, è consigliabile raccogliere i log da tutti i nodi in una posizione centrale. Il salvataggio dei log in una posizione centrale semplifica l'analisi e la risoluzione di eventuali problemi nei servizi, nelle applicazioni e nel cluster stesso. Uno dei modi per caricare e raccogliere i log consiste nell'usare l'estensione Diagnostica di Azure, che carica i log nell'archiviazione di Azure. È possibile leggere gli eventi dalla risorsa di archiviazione e inserirli in un prodotto come [ElasticSearch](service-fabric-diagnostic-how-to-use-elasticsearch.md) o un'altra soluzione di analisi di log.

## <a name="log-sources-that-you-might-want-to-collect"></a>Origini di log da raccogliere
* **Log di Service Fabric**: generati dalla piattaforma tramite [LTTng](http://lttng.org) e caricati nell'account di archiviazione. I log possono essere eventi operativi o eventi di runtime generati dalla piattaforma. Questi log vengono archiviati nel percorso specificato dal manifesto del cluster. Per ottenere i dettagli sull'account di archiviazione, cercare il tag **AzureTableWinFabETWQueryable** e quindi **StoreConnectionString**.
* **Eventi dell'applicazione:** generati dal codice dei servizi. È possibile usare qualsiasi soluzione di registrazione che scriva file di log basati su testo, ad esempio LTTng. Per altre informazioni, vedere la documentazione di LTTng relativa alla traccia dell'applicazione.  

## <a name="deploy-the-diagnostics-extension"></a>Distribuire l'estensione Diagnostica
Il primo passaggio per la raccolta dei log consiste nel distribuire l'estensione Diagnostica in ogni VM del cluster Service Fabric. Questa estensione raccoglie i log in ogni VM e li carica nell'account di archiviazione specificato. La procedura varia a seconda che si usi il portale di Azure oppure Azure Resource Manager.

Per distribuire l'estensione di diagnostica nelle VM del cluster come parte della creazione del cluster, impostare **Diagnostica** su **Attiva**. Dopo aver creato il cluster, è possibile modificare questa impostazione tramite il portale.

Configurare quindi l'estensione Diagnostica di Azure per Linux per raccogliere i file e inserirli nel proprio account di archiviazione. Questo processo viene illustrato nello scenario 3 ("Caricamento dei propri file di log") descritto nell'articolo relativo all' [uso dell'estensione Diagnostica di Azure per Linux per il monitoraggio e la diagnosi di VM Linux](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md). Questo processo consente di ottenere l'accesso alle tracce. È possibile anche caricare le tracce in un visualizzatore a scelta.

È anche possibile distribuire l'estensione di Diagnostica con Azure Resource Manager. Il processo è simile per Windows e per Linux ed è documentato per i cluster Windows nell'articolo [Come raccogliere log con Diagnostica di Azure](service-fabric-diagnostics-how-to-setup-wad.md).

È anche possibile usare Operations Management Suite, come descritto in [Operations Management Suite Log Analytics with Linux](https://blogs.technet.microsoft.com/hybridcloud/2016/01/28/operations-management-suite-log-analytics-with-linux/) (Analisi dei log di Operations Management Suite con Linux).

Dopo aver terminato la configurazione, l'agente di Diagnostica di Azure per Linux monitora i file di log specificati. Ogni volta che viene aggiunta una nuova riga al file, l'agente crea una voce syslog che viene inviata alla risorsa di archiviazione specificata dall'utente.

## <a name="next-steps"></a>Passaggi successivi
Per comprendere più nel dettaglio gli eventi da esaminare durante la risoluzione dei problemi, vedere la [documentazione di LTTng](http://lttng.org/docs) e l'articolo relativo all'[uso dell'estensione Diagnostica di Azure per Linux](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md).

<!--HONumber=Oct16_HO2-->


