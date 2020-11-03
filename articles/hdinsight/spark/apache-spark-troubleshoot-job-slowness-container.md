---
title: Apache Spark lento quando l'archiviazione HDInsight di Azure contiene molti file
description: Apache Spark processo viene eseguito lentamente quando il contenitore di archiviazione di Azure contiene molti file in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/21/2019
ms.openlocfilehash: f14e9bfad959d7f40b2d364c702983c31e0e3cef
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289767"
---
# <a name="apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>Il processo di Apache Spark viene eseguito lentamente quando il contenitore di archiviazione di Azure contiene molti file in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di componenti Apache Spark nei cluster Azure HDInsight.

## <a name="issue"></a>Problema

Quando si esegue un cluster HDInsight, il processo di Apache Spark che scrive nel contenitore di archiviazione di Azure diventa lento quando sono presenti molti file/sottocartelle. Ad esempio, durante la scrittura in un nuovo contenitore sono necessari 20 secondi, ma circa 2 minuti durante la scrittura in un contenitore con 200 file.

## <a name="cause"></a>Causa

Si tratta di un problema di Spark noto. La lentezza deriva dalle `ListBlob` operazioni e `GetBlobProperties` durante l'esecuzione del processo Spark.

Per tenere traccia delle partizioni, Spark deve mantenere un `FileStatusCache` che contiene informazioni sulla struttura di directory. Con questa cache, Spark può analizzare i percorsi e tenere presenti le partizioni disponibili. Il vantaggio delle partizioni di rilevamento è che Spark tocca solo i file necessari durante la lettura dei dati. Per rendere aggiornate queste informazioni, quando si scrivono nuovi dati, Spark deve elencare tutti i file nella directory e aggiornare la cache.

In Spark 2,1, sebbene non sia necessario aggiornare la cache dopo ogni operazione di scrittura, Spark verificherà se una colonna di partizione esistente corrisponde a quella proposta nella richiesta di scrittura corrente, quindi condurrà anche l'elenco delle operazioni all'inizio di ogni operazione di scrittura.

In Spark 2,2, durante la scrittura di dati con la modalità Append, questo problema di prestazioni deve essere risolto.

## <a name="resolution"></a>Soluzione

Quando si crea un set di dati partizionato, è importante usare uno schema di partizionamento che limiti il numero di file che devono essere elencati da Spark per aggiornare `FileStatusCache` .

Per ogni ennesimo batch micro, dove N %100 = = 0 (100 è solo un esempio), spostare i dati esistenti in un'altra directory, che può essere caricata da Spark.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]