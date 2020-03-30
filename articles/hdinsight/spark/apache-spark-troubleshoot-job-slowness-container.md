---
title: Apache Spark lento quando l'archiviazione di Azure HDInsight contiene molti fileApache Spark slow when Azure HDInsight storage has many files
description: Apache Spark job runs slowly when the Azure storage container contains many files in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/21/2019
ms.openlocfilehash: e389c05a6de85287bc86eff510e137f470837e56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894334"
---
# <a name="apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>Il processo di Apache Spark viene eseguito lentamente quando il contenitore di archiviazione di Azure contiene molti file in Azure HDInsight

Questo articolo descrive la procedura di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso dei componenti Apache Spark nei cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when using Apache Spark components in Azure HDInsight clusters.

## <a name="issue"></a>Problema

Quando si esegue un cluster HDInsight, il processo Apache Spark che scrive nel contenitore di archiviazione di Azure diventa lento quando sono presenti molti file/sottocartelle. Ad esempio, sono necessari 20 secondi per la scrittura in un nuovo contenitore, ma circa 2 minuti quando si scrive in un contenitore con 200k file.

## <a name="cause"></a>Causa

Si tratta di un problema noto di Spark. La lentezza deriva `ListBlob` `GetBlobProperties` dalle operazioni e durante l'esecuzione del processo Spark.

Per tenere traccia delle partizioni, `FileStatusCache` Spark deve mantenere un che contiene informazioni sulla struttura di directory. Utilizzando questa cache, Spark può analizzare i percorsi ed essere a conoscenza delle partizioni disponibili. Il vantaggio del rilevamento delle partizioni è che Spark tocca solo i file necessari quando si leggono i dati. Per mantenere aggiornate queste informazioni, quando si scrivono nuovi dati, Spark deve elencare tutti i file nella directory e aggiornare la cache.

In Spark 2.1, anche se non è necessario aggiornare la cache dopo ogni scrittura, Spark controllerà se una colonna di partizione esistente corrisponde a quella proposta nella richiesta di scrittura corrente, quindi porterà anche alle operazioni di elenco all'inizio di ogni scrittura.

In Spark 2.2, quando si scrivono dati con modalità di aggiunta, questo problema di prestazioni deve essere risolto.

## <a name="resolution"></a>Risoluzione

Quando si crea un set di dati partizionato, è importante utilizzare uno schema di partizionamento `FileStatusCache`che limiti il numero di file che Spark deve elencare per aggiornare il file .

Per ogni nisma micro batch in cui N % 100 è uguale a 0 (100 è solo un esempio), spostare i dati esistenti in un'altra directory, che può essere caricata da Spark.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere [Come creare](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)una richiesta di supporto di Azure . L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
