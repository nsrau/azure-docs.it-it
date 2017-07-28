---
title: Panoramica di Acquisizione di Hub eventi di Azure | Microsoft Docs
description: Acquisire i dati di telemetria con Acquisizione di Hub eventi
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: e53cdeea-8a6a-474e-9f96-59d43c0e8562
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: sethm;darosa
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 748031e4177dea1b8a2dbf2f9a4e4d5aea1a0981
ms.contentlocale: it-it
ms.lasthandoff: 07/04/2017


---

# <a name="azure-event-hubs-capture"></a>Acquisizione di Hub eventi di Azure

Acquisizione di Hub eventi di Azure consente di recapitare automaticamente i dati in streaming di Hub eventi nell'account di archiviazione BLOB scelto, con l'aggiunta della flessibilità data dalla possibilità di specificare l'intervallo di tempo o di dimensioni preferito. La configurazione di Acquisizione è rapida, non sono previsti costi amministrativi per l'esecuzione e viene ridimensionata automaticamente con le [unità elaborate](event-hubs-features.md#capacity) in Hub eventi. Acquisizione di Hub eventi è il modo più semplice per caricare i dati in streaming in Azure e consente di concentrarsi sull'elaborazione dei dati anziché sull'acquisizione.

Acquisizione di Hub eventi consente di elaborare pipeline in tempo reale e basate su batch nello stesso flusso. Ciò significa che è possibile compilare soluzioni che si adattano alle esigenze nel corso del tempo. Sia che si debbano compilare oggi sistemi basati su batch con lo sguardo rivolto alla futura elaborazione in tempo reale o che si voglia aggiungere un percorso a freddo efficiente a una soluzione in tempo reale esistente, Acquisizione di Hub eventi semplifica l'uso dei dati in streaming.

## <a name="how-event-hubs-capture-works"></a>Come funziona Acquisizione di Hub eventi

Hub eventi è un buffer permanente di conservazione nel tempo per l'ingresso della telemetria, simile a un log distribuito. La chiave per ridurre il numero di istanze di Hub eventi è il [modello di consumer partizionato](event-hubs-features.md#partitions). Ogni partizione è un segmento di dati indipendente e viene utilizzata in modo indipendente. Nel corso del tempo questi dati diventano obsoleti, a seconda del periodo di conservazione configurabile. Di conseguenza, un determinato hub eventi non sarà mai "troppo pieno".

Acquisizione di Hub eventi consente di specificare l'account di archiviazione BLOB di Azure e il contenitore che vengono usati per salvare i dati acquisiti. Questo account può trovarsi nella stessa area dell'hub eventi o in un'altra, aumentando così la flessibilità della funzionalità di Acquisizione di Hub eventi.

I dati acquisiti vengono scritti in formato [Apache Avro][Apache Avro], un formato compatto, rapido, binario che offre strutture di dati avanzate con lo schema inline. Questo formato è largamente usato nell'ecosistema Hadoop, dall'analisi di flusso e da Azure Data Factory. Altre informazioni sull'uso di Avro sono disponibili più avanti in questo articolo.

### <a name="capture-windowing"></a>Acquisire windowing

Acquisizione di Hub eventi consente di configurare una finestra per controllare l'archiviazione. Questa finestra è una dimensione minima e una configurazione a tempo con un criterio basato sulla precedenza, ovvero il primo trigger rilevato avvia un'operazione di acquisizione. Se si ha una finestra di acquisizione di quindici minuti/100 MB e si invia 1 MB al secondo, la finestra della dimensione viene attivata prima della finestra temporale. Ogni partizione acquisisce in modo indipendente e scrive un BLOB in blocchi completo al momento dell'acquisizione, denominato in base all'ora in cui è stato rilevato l'intervallo di acquisizione. La convenzione di denominazione dell'archiviazione è la seguente:

```
[namespace]/[event hub]/[partition]/[YYYY]/[MM]/[DD]/[HH]/[mm]/[ss]
```

### <a name="scaling-to-throughput-units"></a>Ridimensionamento alle unità elaborate

Il traffico di Hub eventi è controllato dalle [unità elaborate](event-hubs-features.md#capacity). Una singola unità elaborata consente 1 MB al secondo o 1000 eventi al secondo in ingresso e il doppio in uscita. Hub eventi Standard può essere configurato con 1-20 unità elaborate e altre possono essere acquistate con una [richiesta di supporto][support request] per l'aumento della quota. L'uso superiore rispetto alle unità elaborate acquistate è limitato. Acquisizione di Hub eventi copia i dati direttamente dalla memoria di Hub eventi interna, ignorando le quote in uscita di unità elaborate e salvando l'uscita per altri lettori di elaborazione, ad esempio l'analisi di flusso o Spark.

Acquisizione di Hub eventi, dopo essere stata configurata, viene eseguita automaticamente quando si invia il primo evento e continua l'esecuzione. Per comunicare facilmente all'elaborazione downstream che il processo è funzionante, Hub eventi scrive file vuoti quando non sono presenti dati. Questo processo ottiene una cadenza prevedibile e un marcatore che possono alimentare i processori batch.

## <a name="setting-up-event-hubs-capture"></a>Configurazione di Acquisizione di Hub eventi

È possibile configurare Acquisizione al momento della creazione dell'hub eventi usando il [portale di Azure](https://portal.azure.com) o i modelli di Azure Resource Manager. Per altre informazioni, vedere gli articoli seguenti:

- [Abilitare Acquisizione di Hub eventi usando il portale di Azure](event-hubs-capture-enable-through-portal.md)
- [Creare uno spazio dei nomi di Hub eventi con un hub eventi e abilitare l'acquisizione con un modello di Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


## <a name="exploring-the-captured-files-and-working-with-avro"></a>Esplorazione dei file acquisiti e dell'uso di Avro

Acquisizione di Hub eventi crea i file nell'account di archiviazione di Azure e il contenitore specificati nella finestra temporale configurata. È possibile visualizzare questi file con qualsiasi strumento, ad esempio [Azure Storage Explorer][Azure Storage Explorer]. È possibile scaricare i file in locale per usarli.

I file generati da Acquisizione di Hub eventi hanno lo schema Avro seguente:

![][3]

Per esplorare facilmente i file di Avro, è possibile usare il file JAR [Avro Tools][Avro Tools] di Apache. Dopo avere scaricato questo file JAR, è possibile visualizzare lo schema di un file di Avro eseguendo il comando seguente:

```
java -jar avro-tools-1.8.2.jar getschema \<name of capture file\>
```

Questo comando restituisce

```
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

In Apache Avro sono disponibili guide introduttive complete per [Java][Java] e [Python][Python]. È anche possibile leggere l'articolo [Getting Started with Event Hubs Capture](event-hubs-archive-python.md) (Introduzione ad Acquisizione di Hub eventi).

## <a name="how-event-hubs-capture-is-charged"></a>Come viene addebitato l'uso di Acquisizione di Hub eventi

L'uso di Acquisizione di Hub eventi viene registrato in modo simile a quello delle unità elaborate, come tariffa oraria. L'addebito è direttamente proporzionale al numero di unità elaborate acquistate per lo spazio dei nomi. Quando le unità elaborate aumentano o diminuiscono, anche Acquisizione di Hub eventi aumenta o diminuisce per offrire prestazioni corrispondenti. Le misurazioni vengono eseguite in parallelo. Per i dettagli sui prezzi, vedere [Prezzi di Hub eventi](https://azure.microsoft.com/pricing/details/event-hubs/). 

## <a name="next-steps"></a>Passaggi successivi

Acquisizione di Hub eventi rappresenta il modo più facile per ottenere i dati in Azure. Con Azure Data Lake, Azure Data Factory e Azure HDInsight, è possibile eseguire l'elaborazione batch e altre analisi usando strumenti e piattaforme familiari a scelta con la scalabilità necessaria.

Per ulteriori informazioni su Hub eventi visitare i collegamenti seguenti:

* Un' [applicazione di esempio completa che usa Hub eventi][sample application that uses Event Hubs].
* Esempio relativo alla [scalabilità orizzontale dell'elaborazione di eventi con l'Hub eventi][Scale out Event Processing with Event Hubs].
* [Panoramica di Hub eventi][Event Hubs overview]

[Apache Avro]: http://avro.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: http://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: http://www-us.apache.org/dist/avro/avro-1.8.2/java/avro-tools-1.8.2.jar
[Java]: http://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: http://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3

