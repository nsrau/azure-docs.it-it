---
title: Apache Spark lento quando l'archiviazione HDInsight di Azure contiene molti file
description: Apache Spark processo viene eseguito lentamente quando il contenitore di archiviazione di Azure contiene molti file in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/21/2019
ms.openlocfilehash: 82280532fa91923bd08d8ff3164dc841282c392c
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241184"
---
# <a name="apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>Apache Spark processo viene eseguito lentamente quando il contenitore di archiviazione di Azure contiene molti file in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di Apache Spark componenti nei cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Quando si esegue un cluster HDInsight, il processo di Apache Spark che scrive nel contenitore di archiviazione di Azure diventa lento quando sono presenti molti file/sottocartelle. Ad esempio, durante la scrittura in un nuovo contenitore sono necessari 20 secondi, ma circa 2 minuti durante la scrittura in un contenitore con 200 file.

## <a name="cause"></a>Causa

Si tratta di un problema di Spark noto. Il rallentamento deriva dalla `ListBlob` e `GetBlobProperties` operazioni durante l'esecuzione del processo Spark.

Per tenere traccia delle partizioni, Spark deve gestire un `FileStatusCache` che contiene informazioni sulla struttura di directory. Con questa cache, Spark può analizzare i percorsi e tenere presenti le partizioni disponibili. Il vantaggio delle partizioni di rilevamento è che Spark tocca solo i file necessari durante la lettura dei dati. Per rendere aggiornate queste informazioni, quando si scrivono nuovi dati, Spark deve elencare tutti i file nella directory e aggiornare la cache.

In Spark 2,1, sebbene non sia necessario aggiornare la cache dopo ogni operazione di scrittura, Spark verificherà se una colonna di partizione esistente corrisponde a quella proposta nella richiesta di scrittura corrente, quindi condurrà anche l'elenco delle operazioni all'inizio di ogni operazione di scrittura.

In Spark 2,2, durante la scrittura di dati con la modalità Append, questo problema di prestazioni deve essere risolto.

## <a name="resolution"></a>Risoluzione

Quando si crea un set di dati partizionato, è importante usare uno schema di partizionamento che limiti il numero di file che devono essere elencati da Spark per aggiornare la `FileStatusCache`.

Per ogni ennesimo batch micro, dove N %100 = = 0 (100 è solo un esempio), spostare i dati esistenti in un'altra directory, che può essere caricata da Spark.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* È possibile connettersi con [@AzureSupport](https://twitter.com/azuresupport) , l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto tecnico di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
