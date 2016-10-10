<properties
   pageTitle="Come raccogliere log con Diagnostica di Azure per Linux | Microsoft Azure"
   description="Questo articolo descrive come configurare Diagnostica di Azure per raccogliere log da un cluster Linux di Service Fabric in esecuzione in Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>  

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/28/2016"
   ms.author="subramar"/>  


# Come raccogliere log con Diagnostica di Azure

> [AZURE.SELECTOR]
- [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
- [Linux](service-fabric-diagnostics-how-to-setup-lad.md)

Quando si esegue un cluster Azure Service Fabric, è consigliabile raccogliere i log da tutti i nodi in una posizione centrale. Il salvataggio dei log in una posizione centrale semplifica l'analisi e la risoluzione di eventuali problemi nei servizi, nelle applicazioni e nel cluster stesso. Uno dei modi per caricare e raccogliere i log consiste nell'usare l'estensione Diagnostica di Azure, che carica i log nell'archiviazione di Azure. È possibile leggere gli eventi dalla risorsa di archiviazione e inserirli in un prodotto come [ElasticSearch](service-fabric-diagnostic-how-to-use-elasticsearch.md) o un'altra soluzione di analisi di log.

## Diverse origini di log da raccogliere
1. **Log di Service Fabric:** generati dalla piattaforma usando LTTng e caricati nell'account di archiviazione. Possono essere eventi operativi o eventi di runtime generati dalla piattaforma. Questi log vengono archiviati nella posizione indicata nel manifesto del cluster. Per i dettagli dell'account di archiviazione, cercare il tag "AzureTableWinFabETWQueryable" e quindi "StoreConnectionString".
2. **Eventi dell'applicazione:** eventi generati dal codice dei servizi. È possibile usare qualsiasi soluzione di registrazione che scriva file di log basati su testo, ad esempio [LTTng](http://lttng.org). Vedere la documentazione di LTTng relativa alla traccia dell'applicazione.


## Distribuzione dell'estensione di diagnostica
Il primo passaggio per la raccolta dei log consiste nel distribuire l'estensione Diagnostica in ogni VM del cluster Service Fabric. Tale estensione raccoglierà i log in ogni VM e li caricherà nell'account di archiviazione specificato. La procedura varia a seconda che si usi il portale di Azure oppure Azure Resource Manager.

### Distribuire l'estensione di diagnostica come parte della creazione del cluster 
Per distribuire l'estensione di diagnostica nelle VM del cluster come parte della creazione del cluster, impostare "Diagnostica" su **Sì**. Al termine della creazione del cluster, questa impostazione non può essere modificata usando il portale.

Configurare l'estensione Diagnostica di Azure per Linux per raccogliere i file e inserirli nell'account di archiviazione del cliente. Questo processo viene illustrato nello scenario 3 ("Caricamento dei propri file di log") descritto nell'articolo relativo all'[uso dell'estensione Diagnostica di Azure per Linux per il monitoraggio e la diagnosi di VM Linux](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md). Questo processo consente di accedere alle tracce, che possono essere caricate nel visualizzatore preferito.


È anche possibile distribuire l'estensione di diagnostica con Azure Resource Manager. Il processo è simile per Windows e per Linux ed è documentato per i cluster Windows nell'articolo [Come raccogliere log con Diagnostica di Azure](service-fabric-diagnostics-how-to-setup-wad.md).

È anche possibile usare OMS come descritto in [Operations Management Suite Log Analytics with Linux](https://blogs.technet.microsoft.com/hybridcloud/2016/01/28/operations-management-suite-log-analytics-with-linux/) (Log Analytics di Operations Management Suite con Linux).

Al termine della configurazione, l'agente di Diagnostica di Azure per Linux monitora i file di log specificati. Ogni volta che viene aggiunta una nuova riga al file, crea una voce syslog che viene inviata alla risorsa di archiviazione specificata dal cliente.


## Passaggi successivi
Per ottenere informazioni più dettagliate sugli eventi da esaminare durante la risoluzione dei problemi, vedere la [documentazione di LTTng](http://lttng.org/docs) e l'articolo relativo all'[uso dell'estensione Diagnostica di Azure per Linux](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md).

<!---HONumber=AcomDC_0928_2016-->