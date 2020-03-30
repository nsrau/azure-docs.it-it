---
title: Impossibile leggere il log Apache Yarn in Azure HDInsight
description: Passaggi per la risoluzione dei problemi e possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.Troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.
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
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>Scenario: Unable to read Apache Yarn log in Azure HDInsight

Questo articolo descrive i passaggi per la risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.

## <a name="issue"></a>Problema

I registri Apache Yarn trovati dall'account di archiviazione non sono leggibili. Il parser del file non funziona e genera il seguente messaggio di errore:

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>Causa

Il log Apache Yarn `IndexFile` è aggregato in formato, che non è supportato dal parser di file.

## <a name="resolution"></a>Risoluzione

1. Da un Web browser `https://CLUSTERNAME.azurehdinsight.net`passare `CLUSTERNAME` a , dove è il nome del cluster.

1. Dall'interfaccia utente di Ambari, accedere a **YARN** > **Configs** > **Advanced** > **Advanced yarn-site**.

1. Per l'archiviazione WASB: `yarn.log-aggregation.file-formats` `IndexedFormat,TFile`il valore predefinito per è . Modificare il `TFile`valore in .

1. Per l'archiviazione ADLS: `yarn.nodemanager.log-aggregation.compression-type` `gz`il valore predefinito per è . Modificare il `none`valore in .

1. Salvare la modifica e riavviare tutti i servizi interessati.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con - l'account ufficiale di Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere Come creare una richiesta di supporto di Azure.For more detailed information, review [How to create an Azure support request](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)
