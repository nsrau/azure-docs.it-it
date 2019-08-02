---
title: I processi di Apache Spark streaming importano più tempo del consueto per l'elaborazione in Azure HDInsight
description: I processi di Apache Spark streaming importano più tempo del consueto per l'elaborazione in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 7c7baaaa5fd08a102b3b55c38fb2c4bf892480c5
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679432"
---
# <a name="scenario-apache-spark-streaming-jobs-take-longer-than-usual-to-process-in-azure-hdinsight"></a>Scenario: I processi di Apache Spark streaming importano più tempo del consueto per l'elaborazione in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di Apache Spark componenti nei cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Si noterà che alcuni dei processi di Apache Spark streaming sono lenti o richiedono più tempo del consueto per l'elaborazione. Per le applicazioni Spark streaming, ogni batch di messaggi corrisponde a un processo inviato a Spark. Se un processo richiede in genere X secondi per l'elaborazione, può occasionalmente richiedere 2-3 minuti più del solito.

## <a name="cause"></a>Causa

Una delle cause possibili è che il produttore Apache Kafka impiega più di 2 minuti per completare la scrittura nel cluster Kafka. Per eseguire ulteriormente il debug del problema Kafka, è possibile aggiungere alcune registrazioni al codice che usa un Producer Kafka per inviare messaggi e correlare tale problema con i log del cluster Kafka.

Un'altra possibile cause è che le letture e le scritture frequenti in WASB possono causare un ritardo nei micro batch successivi. L'implementazione di WASB `Filesystem.listStatus` di è molto lenta a causa `O(n!)` di un algoritmo per la rimozione dei duplicati. Usa una quantità eccessiva di memoria a causa della conversione aggiuntiva `FileMetadata` da `FileStatus` `BlobListItem` a a. Ad esempio, l'algoritmo impiega più di 30 minuti per elencare i file 700.000. Quindi, `ListBlobs` se viene chiamato in modo aggressivo da SparkSQL ogni microbatch, comporterà un ritardo dei micro batch successivi che comporterebbe la generazione di ritardi di pianificazione elevati. [Questa patch](https://issues.apache.org/jira/browse/HADOOP-15547) corregge il problema, ma se non è presente nell'ambiente, `ListBlobs` si verificherà una latenza elevata. Inoltre, anche se si eliminano file ogni ora, l'elenco nel back-end deve eseguire l'iterazione su tutte le righe (incluso eliminato) perché Garbage Collection processo non è stato ancora completato. Sebbene la patch possa risolvere alcuni problemi, il Garbage Collection problema potrebbe ancora causare un ritardo nell'elaborazione dei flussi di batch.

## <a name="resolution"></a>Risoluzione

Applicare la correzione [Hadoop-15547](https://issues.apache.org/jira/browse/HADOOP-15547) . Se ciò non è possibile, è possibile usare HDFS come percorso del punto di controllo. Impostare `checkpointDirectory` su un valore simile `hdfs://mycluster/checkpoint`al seguente:.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto tecnico di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
