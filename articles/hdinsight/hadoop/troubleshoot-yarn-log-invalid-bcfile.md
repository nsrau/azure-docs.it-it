---
title: Non è possibile leggere il log di Apache yarn in Azure HDInsight
description: Procedure di risoluzione dei problemi e possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: f7861ae4980f13fbd87780ab2d26fa3376f33532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76776196"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>Scenario: non è possibile leggere il log di Apache yarn in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

I log di Apache Yarn trovati dall'account di archiviazione non sono leggibili. Il parser di file non funziona e genera il messaggio di errore seguente:

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>Causa

Il log di Apache Yarn viene aggregato `IndexFile` in formato, che non è supportato dal parser di file.

## <a name="resolution"></a>Risoluzione

1. Da un Web browser passare a `https://CLUSTERNAME.azurehdinsight.net`, dove `CLUSTERNAME` è il nome del cluster.

1. Dall'interfaccia utente di Ambariri passare a **Yarn** > **configs** > **Advanced** > Advanced**Yarn-sito**.

1. Per archiviazione WASB: il valore predefinito per `yarn.log-aggregation.file-formats` è `IndexedFormat,TFile`. Modificare il valore in `TFile`.

1. Per archiviazione ADLS: il valore predefinito per `yarn.nodemanager.log-aggregation.compression-type` è `gz`. Modificare il valore in `none`.

1. Salvare la modifica e riavviare tutti i servizi interessati.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il [supporto della community di Azure](https://azure.microsoft.com/support/community/).

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport) : l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse appropriate: risposte, supporto ed esperti.

* Se è necessaria ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [come creare una richiesta di supporto di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso alla gestione delle sottoscrizioni e al supporto per la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
