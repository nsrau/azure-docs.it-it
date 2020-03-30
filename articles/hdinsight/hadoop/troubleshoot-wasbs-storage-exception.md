---
title: L'account a cui si accede non supporta l'errore http in Azure HDInsightThe account being accessed does not support http error in Azure HDInsight
description: Questo articolo descrive i passaggi per la risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 14c43e4557275d6a425127acfee7495f68d1d354
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77165553"
---
# <a name="the-account-being-accessed-does-not-support-http-error-in-azure-hdinsight"></a>L'account a cui si accede non supporta l'errore http in Azure HDInsightThe account being accessed does not support http error in Azure HDInsight

Questo articolo descrive i passaggi per la risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.

## <a name="issue"></a>Problema

Viene visualizzato il seguente messaggio di errore:

```
com.microsoft.azure.storage.StorageException: The account being accessed does not support http.
```

## <a name="cause"></a>Causa

Esistono diversi motivi per cui viene ricevuto il messaggio di errore:

* L'account di archiviazione ha il [trasferimento sicuro](../../storage/common/storage-require-secure-transfer.md) abilitato e viene usato lo [schema URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) non corretto.

* È stato creato un cluster con un account di archiviazione per il trasferimento sicuro *disabilitato.* Successivamente, il trasferimento sicuro è stato abilitato nell'account di archiviazione.

## <a name="resolution"></a>Risoluzione

Se il trasferimento sicuro è abilitato per Archiviazione di Azure `wasbs://` `abfss://`o Data Lake Storage Gen2, l'URI sarà rispettivamente o , ovvero .  Vedere anche l'articolo sul [trasferimento sicuro](../../storage/common/storage-require-secure-transfer.md).

Per i nuovi cluster, usare un account di archiviazione che dispone già dell'impostazione di trasferimento sicuro desiderata. Non modificare l'impostazione di trasferimento sicuro per un account di archiviazione utilizzato da un cluster esistente.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con - l'account ufficiale di Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere Come creare una richiesta di supporto di Azure.For more detailed information, review [How to create an Azure support request](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
