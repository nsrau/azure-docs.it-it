---
title: Acquisire eventi di streaming - Hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce una panoramica della funzionalità Acquisizione, che consente di acquisire gli eventi in streaming attraverso Hub eventi di Azure.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: e53cdeea-8a6a-474e-9f96-59d43c0e8562
ms.service: event-hubs
ms.workload: na
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: cc74bfe9bf9e5f33b7cf05ebb19b44ab8b3bea43
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60822453"
---
# <a name="capture-events-through-azure-event-hubs-in-azure-blob-storage-or-azure-data-lake-storage"></a>Acquisire eventi tramite Hub eventi di Azure in Archiviazione BLOB di Azure o Azure Data Lake Storage
Hub eventi di Azure consente di acquisire automaticamente i dati in streaming di Hub eventi in un account di [Archiviazione BLOB di Azure](https://azure.microsoft.com/services/storage/blobs/) o [Azure Data Lake Storage](https://azure.microsoft.com/services/data-lake-store/) a scelta, con la possibilità di specificare un intervallo di tempo o di dimensioni. La configurazione di Acquisizione è rapida, non sono previsti costi amministrativi per l'esecuzione e viene ridimensionata automaticamente con le [unità elaborate](event-hubs-features.md#throughput-units) in Hub eventi. Acquisizione di Hub eventi è il modo più semplice per caricare i dati in streaming in Azure e consente di concentrarsi sull'elaborazione dei dati anziché sull'acquisizione.

Acquisizione di Hub eventi consente di elaborare pipeline in tempo reale e basate su batch nello stesso flusso. Ciò significa che è possibile compilare soluzioni che si adattano alle esigenze nel corso del tempo. Sia che si debbano compilare oggi sistemi basati su batch con lo sguardo rivolto alla futura elaborazione in tempo reale o che si voglia aggiungere un percorso a freddo efficiente a una soluzione in tempo reale esistente, Acquisizione di Hub eventi semplifica l'uso dei dati in streaming.

> [!NOTE]
> La funzionalità di acquisizione di hub eventi supporta attualmente solo generazione 1 di Azure Data Lake Store, non di generazione 2. 

## <a name="how-event-hubs-capture-works"></a>Come funziona Acquisizione di Hub eventi

Hub eventi è un buffer permanente di conservazione nel tempo per l'ingresso della telemetria, simile a un log distribuito. La chiave per ridurre il numero di istanze di Hub eventi è il [modello di consumer partizionato](event-hubs-features.md#partitions). Ogni partizione è un segmento di dati indipendente e viene utilizzata in modo indipendente. Nel corso del tempo questi dati diventano obsoleti, a seconda del periodo di conservazione configurabile. Di conseguenza, un determinato hub eventi non sarà mai "troppo pieno".

Acquisizione di Hub eventi consente di specificare un account di Archiviazione BLOB di Azure e un contenitore oppure un account Azure Data Lake Store da usare per archiviare i dati acquisiti. Questi account possono trovarsi nella stessa area dell'hub eventi o in un'altra area, aumentando così la flessibilità della funzionalità Acquisizione di Hub eventi.

I dati acquisiti vengono scritti in formato [Apache Avro][Apache Avro], un formato compatto, rapido, binario che offre strutture di dati avanzate con lo schema inline. Questo formato è largamente usato nell'ecosistema Hadoop, dall'analisi di flusso e da Azure Data Factory. Altre informazioni sull'uso di Avro sono disponibili più avanti in questo articolo.

### <a name="capture-windowing"></a>Acquisire windowing

Acquisizione di Hub eventi consente di configurare una finestra per controllare l'acquisizione. Questa finestra è una dimensione minima e una configurazione a tempo con un criterio basato sulla precedenza, ovvero il primo trigger rilevato avvia un'operazione di acquisizione. Se si ha una finestra di acquisizione di quindici minuti/100 MB e si invia 1 MB al secondo, la finestra della dimensione viene attivata prima della finestra temporale. Ogni partizione acquisisce in modo indipendente e scrive un BLOB in blocchi completo al momento dell'acquisizione, denominato in base all'ora in cui è stato rilevato l'intervallo di acquisizione. La convenzione di denominazione dell'archiviazione è la seguente:

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

Si noti che i valori di data vengono riempiti con zeri, come illustrato nel nome di file di esempio seguente:

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

### <a name="scaling-to-throughput-units"></a>Ridimensionamento alle unità elaborate

Il traffico di Hub eventi è controllato dalle [unità elaborate](event-hubs-features.md#throughput-units). Una singola unità elaborata consente 1 MB al secondo o 1000 eventi al secondo in ingresso e il doppio in uscita. Hub eventi Standard può essere configurato con 1-20 unità elaborate e altre possono essere acquistate con una [richiesta di supporto][support request] per l'aumento della quota. L'uso superiore rispetto alle unità elaborate acquistate è limitato. Acquisizione di Hub eventi copia i dati direttamente dalla memoria di Hub eventi interna, ignorando le quote in uscita di unità elaborate e salvando l'uscita per altri lettori di elaborazione, ad esempio l'analisi di flusso o Spark.

Acquisizione di Hub eventi, dopo essere stata configurata, viene eseguita automaticamente quando si invia il primo evento e continua l'esecuzione. Per comunicare facilmente all'elaborazione downstream che il processo è funzionante, Hub eventi scrive file vuoti quando non sono presenti dati. Questo processo ottiene una cadenza prevedibile e un marcatore che possono alimentare i processori batch.

## <a name="setting-up-event-hubs-capture"></a>Configurazione di Acquisizione di Hub eventi

È possibile configurare Acquisizione al momento della creazione dell'hub eventi usando il [portale di Azure](https://portal.azure.com) o i modelli di Azure Resource Manager. Per altre informazioni, vedere gli articoli seguenti:

- [Abilitare Acquisizione di Hub eventi usando il portale di Azure](event-hubs-capture-enable-through-portal.md)
- [Creare uno spazio dei nomi di Hub eventi con un hub eventi e abilitare l'acquisizione con un modello di Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


## <a name="exploring-the-captured-files-and-working-with-avro"></a>Esplorazione dei file acquisiti e dell'uso di Avro

Acquisizione di Hub eventi crea i file in formato Avro, come specificato nell'intervallo di tempo configurato. È possibile visualizzare questi file con qualsiasi strumento, ad esempio [Azure Storage Explorer][Azure Storage Explorer]. È possibile scaricare i file in locale per usarli.

I file generati da Acquisizione di Hub eventi hanno lo schema Avro seguente:

![Schema Avro][3]

Per esplorare facilmente i file di Avro, è possibile usare il file JAR [Avro Tools][Avro Tools] di Apache. È anche possibile usare [Apache Drill][Apache Drill] per un'esperienza semplice basata su SQL oppure [Apache Spark][Apache Spark] per eseguire complesse elaborazioni distribuite sui dati inseriti. 

### <a name="use-apache-drill"></a>Usare Apache Drill

[Apache Drill] [ Apache Drill] è un "motore di query SQL open-source per l'esplorazione dei Big Data", in grado di eseguire query su dati strutturati e semistrutturati ovunque si trovino. Il motore può essere eseguito come un nodo autonomo o come un cluster di grandi dimensioni per prestazioni ottimali.

È disponibile un supporto nativo per Archiviazione BLOB di Azure, che rende più semplice eseguire query sui dati in un file Avro, come descritto nella documentazione:

[Apache Drill: Plug-in per Archiviazione BLOB di Azure][Apache Drill: Azure Blob Storage Plugin]

Per eseguire con facilità query sui file acquisiti, è possibile creare ed eseguire una macchina virtuale con Apache Drill abilitata tramite un contenitore per l'accesso ad Archiviazione BLOB di Azure:

https://github.com/yorek/apache-drill-azure-blob

Un esempio completo end-to-end è disponibile nel repository Flussi scalabili:

[Flussi scalabili: Acquisizione di Hub eventi]

### <a name="use-apache-spark"></a>Usare Apache Spark

[Apache Spark][Apache Spark] è un "motore di analisi unificato per l'elaborazione di dati su larga scala". Supporta diversi linguaggi, tra cui SQL, e può accedere facilmente ad Archiviazione BLOB di Azure. Sono disponibili due opzioni per l'esecuzione di Apache Spark in Azure ed entrambe consentono un accesso semplificato ad Archiviazione BLOB di Azure:

- [HDInsight: Accedere ai file in Archiviazione di Azure][HDInsight: Address files in Azure storage]
- [Azure Databricks: Archiviazione BLOB di Azure][Azure Databricks: Azure Blob Storage]

### <a name="use-avro-tools"></a>Usare Avro Tools

[Avro Tools][Avro Tools] è disponibile come pacchetto JAR. Dopo avere scaricato il file JAR, è possibile visualizzare lo schema di un file Avro specifico eseguendo il comando seguente:

```shell
java -jar avro-tools-1.8.2.jar getschema <name of capture file>
```

Questo comando restituisce

```json
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

È anche possibile usare Avro Tools per convertire il file in formato JSON ed eseguire altre operazioni di elaborazione.

Per eseguire operazioni di elaborazione più avanzate, scaricare e installare Avro per la propria piattaforma. Al momento della stesura di questo articolo, sono disponibili implementazioni per C, C++, C\#, Java, NodeJS, Perl, PHP, Python e Ruby.

In Apache Avro sono disponibili guide introduttive complete per [Java][Java] e [Python][Python]. È anche possibile leggere l'articolo [Acquisizione di Hub eventi di Azure](event-hubs-capture-python.md).

## <a name="how-event-hubs-capture-is-charged"></a>Come viene addebitato l'uso di Acquisizione di Hub eventi

L'uso di Acquisizione di Hub eventi viene registrato in modo simile a quello delle unità elaborate, come tariffa oraria. L'addebito è direttamente proporzionale al numero di unità elaborate acquistate per lo spazio dei nomi. Quando le unità elaborate aumentano o diminuiscono, anche Acquisizione di Hub eventi aumenta o diminuisce per offrire prestazioni corrispondenti. Le misurazioni vengono eseguite in parallelo. Per i dettagli sui prezzi, vedere [Prezzi di Hub eventi](https://azure.microsoft.com/pricing/details/event-hubs/). 

## <a name="integration-with-event-grid"></a>Integrazione con Griglia di eventi 

È possibile creare una sottoscrizione di Griglia di eventi di Azure con uno spazio dei nomi di Hub eventi come origine. L'esercitazione seguente illustra come creare una sottoscrizione di Griglia di eventi con un hub eventi come origine e un'app Funzioni di Azure come sink: [Elaborare i dati di Hub eventi acquisiti ed eseguirne la migrazione a SQL Data Warehouse con Griglia di eventi e Funzioni di Azure](store-captured-data-data-warehouse.md).

## <a name="next-steps"></a>Passaggi successivi

Acquisizione di Hub eventi rappresenta il modo più facile per ottenere i dati in Azure. Con Azure Data Lake, Azure Data Factory e Azure HDInsight, è possibile eseguire l'elaborazione batch e altre analisi usando strumenti e piattaforme familiari a scelta con la scalabilità necessaria.

Per ulteriori informazioni su Hub eventi visitare i collegamenti seguenti:

* [Introduzione all'invio e alla ricezione di eventi](event-hubs-dotnet-framework-getstarted-send.md)
* [Panoramica di Hub eventi][Event Hubs overview]

[Apache Avro]: https://avro.apache.org/
[Apache Drill]: https://drill.apache.org/
[Apache Spark]: https://spark.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: https://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: https://www-us.apache.org/dist/avro/avro-1.8.2/java/avro-tools-1.8.2.jar
[Java]: https://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: https://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight: Address files in Azure storage]:https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-blob-storage#address-files-in-azure-storage
[Azure Databricks: Azure Blob Storage]:https://docs.databricks.com/spark/latest/data-sources/azure/azure-storage.html
[Apache Drill: Azure Blob Storage Plugin]:https://drill.apache.org/docs/azure-blob-storage-plugin/
[Flussi scalabili: Acquisizione di Hub eventi]:https://github.com/yorek/streaming-at-scale/tree/master/event-hubs-capture
