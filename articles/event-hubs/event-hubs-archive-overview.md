---
title: Archivio di Hub eventi di Azure | Documentazione Microsoft
description: "Panoramica della funzionalità di archivio di Hub eventi di Azure."
services: event-hubs
documentationcenter: 
author: djrosanova
manager: timlt
editor: 
ms.assetid: e53cdeea-8a6a-474e-9f96-59d43c0e8562
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2016
ms.author: darosa;sethm
translationtype: Human Translation
ms.sourcegitcommit: 539c04ef95804cc0af9924db8b1d2d1f2ea6eef3
ms.openlocfilehash: 37d8ff4c2e95fddc1daefa650f2407236bc9cda5


---
# <a name="azure-event-hubs-archive"></a>Archivio di Hub eventi di Azure
L'archivio di Hub eventi di Azure consente di distribuire automaticamente i dati in streaming di Hub eventi in un account di archiviazione BLOB di Azure scelto con l'aggiunta della flessibilità necessaria per specificare l'intervallo di tempo o di dimensioni preferito. La configurazione dell'archivio è rapida, non sono previsti costi amministrativi per l'esecuzione e viene eseguito il ridimensionamento automatico con le [unità elaborate](event-hubs-overview.md#capacity-and-security)in Hub eventi. L'archivio di Hub eventi è il modo più semplice per caricare i dati in streaming in Azure e consente di concentrarsi sull'elaborazione dei dati anziché sull'acquisizione.

L'archivio di Hub eventi di Azure consente di elaborare pipeline in tempo reale e basate su batch nello stesso flusso. In questo modo è possibile compilare soluzioni che si adattano alle esigenze nel corso del tempo. Sia che si debbano compilare oggi sistemi basati su batch con lo sguardo rivolto alla futura elaborazione in tempo reale o che si voglia aggiungere un percorso a freddo efficiente a una soluzione in tempo reale esistente, l'archivio di Hub eventi semplifica l'uso dei dati in streaming.

## <a name="how-event-hubs-archive-works"></a>Come funziona l'archivio di Hub eventi
Hub eventi è un buffer permanente di conservazione nel tempo per l'ingresso della telemetria, simile a un log distribuito. La chiave per ridurre il numero di istanze di Hub eventi è il [modello di consumer partizionato](event-hubs-overview.md#partition-key). Ogni partizione è un segmento di dati indipendente e viene utilizzata in modo indipendente. Nel corso del tempo questi dati diventano obsoleti, a seconda del periodo di conservazione configurabile. Un determinato hub eventi quindi non sarà mai "troppo pieno".

L'archivio di Hub eventi consente di specificare l'account di archiviazione BLOB di Azure e il contenitore che verranno usati per salvare i dati archiviati. Questo account può trovarsi nella stessa area dell'hub eventi o in un'altra area, aumentando così la flessibilità della funzionalità dell'archivio di Hub eventi.

I dati archiviati vengono scritti in formato [Apache Avro][Apache Avro] , un formato compatto, rapido, binario che offre strutture di dati avanzate con lo schema inline. Questo formato è largamente usato nell'ecosistema Hadoop, oltre che dall'analisi di flusso e da Azure Data Factory. Altre informazioni sull'uso di Avro sono disponibili più avanti in questo articolo.

### <a name="archive-windowing"></a>Windowing dell'archivio
L'archivio di Hub eventi consente di configurare una finestra per controllare l'archiviazione. Questa finestra è una dimensione minima e una configurazione a tempo con un "criterio basato sulla precedenza", ovvero il primo trigger rilevato darà avvio a un'operazione di archiviazione. Se si ha una finestra di archivio di quindici minuti/100 MB e si invia 1 MB/s, la finestra della dimensione verrà attivata prima della finestra temporale. Ogni partizione archivia in modo indipendente e scrive un BLOB in blocchi completo al momento dell'archiviazione, denominato in base all'ora in cui è stato rilevato l'intervallo di archiviazione. La convenzione di denominazione è la seguente:

```
[Namespace]/[EventHub]/[Partition]/[YYYY]/[MM]/[DD]/[HH]/[mm]/[ss]
```

### <a name="scaling-to-throughput-units"></a>Ridimensionamento alle unità elaborate
Il traffico di Hub eventi è controllato dalle [unità elaborate](event-hubs-overview.md#capacity-and-security). Una singola unità elaborata consente 1 MB al secondo o 1000 eventi al secondo in ingresso e il doppio in uscita. Hub eventi Standard può essere configurato con 1-20 unità elaborate e altre possono essere acquistate tramite una [richiesta di supporto][support request] per l'aumento della quota. L'uso superiore rispetto alle unità elaborate acquistate è limitato. L'archivio di Hub eventi copia i dati direttamente dalla memoria di Hub eventi interna, ignorando le quote in uscita di unità elaborate e salvando l'uscita per altri lettori di elaborazione, ad esempio l'analisi di flusso o Spark.

Dopo la configurazione, l'archivio di Hub eventi viene eseguito automaticamente non appena si invia il primo evento. L'esecuzione continua senza interruzioni. Per comunicare facilmente all'elaborazione downstream che il processo è funzionante, Hub eventi scrive file vuoti quando non sono presenti dati. Si ottengono così una cadenza prevedibile e un marcatore che possono alimentare i processori batch.

## <a name="setting-up-event-hubs-archive"></a>Configurazione dell'archivio di Hub eventi
L'archivio di Hub eventi può essere configurato durante la creazione di un hub eventi tramite il portale o Azure Resource Manager. Per abilitare l'archivio, è sufficiente fare clic sul pulsante **Sì** . Per configurare un account di archiviazione e un contenitore, fare clic sulla sezione **Contenitore** del pannello. Poiché l'archivio di Hub eventi usa l'autenticazione da servizio a servizio con la risorsa di archiviazione, non è necessario specificare una stringa di connessione di archiviazione. Il selettore risorse seleziona automaticamente l'URI della risorsa per l'account di archiviazione. Se si usa Azure Resource Manager, è necessario specificare questo URI in modo esplicito come stringa.

L'intervallo di tempo predefinito è di 5 minuti. Il valore minimo è 1, quello massimo 15. La finestra **Dimensione** ha un intervallo compreso tra 10 e 500 MB.

![][1]

## <a name="adding-archive-to-an-existing-event-hub"></a>Aggiunta dell'archivio a un hub eventi esistente
Si possono configurare archivi negli hub eventi esistenti nello spazio dei nomi Hub eventi. La funzionalità non è disponibile negli spazi dei nomi di tipo **Messaggistica** o **Misto** meno recenti. Per abilitare l'archivio in un hub eventi esistente o per modificare le impostazioni dell'archivio, fare clic sullo spazio dei nomi per caricare il pannello **Informazioni di base** , quindi fare clic sull'hub eventi per cui si vuole abilitare o modificare l'impostazione dell'archivio. Infine fare clic sulla sezione **Proprietà** del pannello aperto, come illustrato nella figura seguente.

![][2]

È anche possibile configurare l'archivio di Hub eventi tramite i modelli di Azure Resource Manager. Per altre informazioni, vedere [questo articolo](event-hubs-resource-manager-namespace-event-hub-enable-archive.md).

## <a name="exploring-the-archive-and-working-with-avro"></a>Esplorazione dell'archivio e uso di Avro
Dopo la configurazione, l'archivio di Hub eventi crea i file nell'account di archiviazione di Azure e il contenitore specificati nella finestra temporale configurata. È possibile visualizzare questi file con qualsiasi strumento, ad esempio [Azure Storage Explorer][Azure Storage Explorer]. È possibile scaricare i file in locale per usarli.

I file generati dall'archivio di Hub eventi hanno lo schema Avro seguente:

![][3]

Per esplorare facilmente i file di Avro, è possibile usare il file JAR [Avro Tools][Avro Tools] di Apache. Dopo avere scaricato questo file JAR, è possibile visualizzare lo schema di un file di Avro eseguendo il comando seguente:

```
java -jar avro-tools-1.8.1.jar getschema \<name of archive file\>
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

In Apache Avro sono disponibili guide introduttive complete per [Java][Java] e [Python][Python]. È anche possibile leggere l'articolo [Getting Started with Event Hubs Archive](event-hubs-archive-python.md) (Introduzione all'archivio di Hub eventi).

## <a name="how-event-hubs-archive-is-charged"></a>Come viene addebitato l'uso dell'archivio di Hub eventi
L'uso dell'archivio di Hub eventi viene registrato in modo simile a quello delle unità elaborate, come tariffa oraria. L'addebito è direttamente proporzionale al numero di unità elaborate acquistate per lo spazio dei nomi. Quando le unità elaborate aumentano o diminuiscono, anche l'archivio di Hub eventi aumenta o diminuisce per offrire prestazioni corrispondenti. Le misurazioni vengono effettuate in parallelo. L'addebito per l'archivio di Hub eventi è pari a $ 0,10 all'ora per ogni unità elaborata, con il 50% di sconto durante il periodo di anteprima.

L'archivio di Hub eventi è il modo più semplice per caricare i dati in Azure. Con Azure Data Lake, Azure Data Factory e Azure HDInsight, è possibile eseguire l'elaborazione batch e altre analisi a scelta usando strumenti e piattaforme familiari con la scalabilità necessaria.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni su Hub eventi visitare i collegamenti seguenti:

* Un' [applicazione di esempio completa che usa Hub eventi][sample application that uses Event Hubs].
* Esempio relativo alla [scalabilità orizzontale dell'elaborazione di eventi con l'Hub eventi][Scale out Event Processing with Event Hubs].
* [Panoramica di Hub eventi][Event Hubs overview]

[Apache Avro]: http://avro.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[1]: ./media/event-hubs-archive-overview/event-hubs-archive1.png
[2]: media/event-hubs-archive-overview/event-hubs-archive2.png
[Azure Storage Explorer]: http://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-archive-overview/event-hubs-archive3.png
[Avro Tools]: http://www-us.apache.org/dist/avro/avro-1.8.1/java/avro-tools-1.8.1.jar
[Java]: http://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: http://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-overview.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3



<!--HONumber=Dec16_HO2-->


