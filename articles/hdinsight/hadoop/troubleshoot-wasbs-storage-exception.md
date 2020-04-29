---
title: L'account a cui si accede non supporta l'errore HTTP in Azure HDInsight
description: Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: 14c43e4557275d6a425127acfee7495f68d1d354
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77165553"
---
# <a name="the-account-being-accessed-does-not-support-http-error-in-azure-hdinsight"></a>L'account a cui si accede non supporta l'errore HTTP in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Viene ricevuto il messaggio di errore seguente:

```
com.microsoft.azure.storage.StorageException: The account being accessed does not support http.
```

## <a name="cause"></a>Causa

Esistono diversi motivi per cui viene ricevuto il messaggio di errore:

* Per l'account di archiviazione è abilitato il [trasferimento sicuro](../../storage/common/storage-require-secure-transfer.md) e viene usato lo [schema URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) errato.

* Un cluster è stato creato con un account di archiviazione in cui è stato *disabilitato*il trasferimento sicuro. In seguito, il trasferimento sicuro è stato abilitato nell'account di archiviazione.

## <a name="resolution"></a>Soluzione

Se il trasferimento sicuro è abilitato per archiviazione di Azure o Data Lake Storage Gen2, l'URI `wasbs://` è `abfss://`rispettivamente o.  Vedere anche l'articolo sul [trasferimento sicuro](../../storage/common/storage-require-secure-transfer.md).

Per i nuovi cluster, usare un account di archiviazione che disponga già dell'impostazione di trasferimento sicuro desiderata. Non modificare l'impostazione di trasferimento sicuro per un account di archiviazione usato da un cluster esistente.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
