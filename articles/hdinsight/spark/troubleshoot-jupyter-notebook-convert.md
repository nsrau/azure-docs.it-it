---
title: Non è possibile creare il notebook di Jupyter in Azure HDInsight
description: Informazioni sulle procedure di risoluzione dei problemi e sulle possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/11/2020
ms.openlocfilehash: 50a77e240315796d0d4c98fc8fba8edfc9cd2b7d
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545532"
---
# <a name="unable-to-create-jupyter-notebook-in-azure-hdinsight"></a>Non è possibile creare il notebook di Jupyter in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Quando si avvia un notebook di Jupyter, viene visualizzato un messaggio di errore che contiene:

```error
Cannot convert notebook to v5 because that version doesn't exist
```

## <a name="cause"></a>Causa

Versione non corrispondente.

## <a name="resolution"></a>Soluzione

1. Usare il [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) per connettersi al cluster. Modificare il comando seguente sostituendo CLUSTERNAME con il nome del cluster in uso e quindi immettere il comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Aprire `_version.py` eseguendo il comando seguente:

    ```bash
    sudo nano /usr/bin/anaconda/lib/python2.7/site-packages/nbformat/_version.py
    ```

1. Modificare **5** in **4** in modo che la riga modificata appaia come segue:

    ```python
    version_info = (4, 0, 3)
    ```

    Salvare le modifiche immettendo **CTRL + X** , **Y** , **invio** .

1. In un Web browser passare a `https://CLUSTERNAME.azurehdinsight.net/#/main/services/JUPYTER` dove `CLUSTERNAME` è il nome del cluster.

1. Riavviare il servizio Jupyter.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ricevere risposte dagli esperti di Azure tramite la pagina [Supporto della community per Azure](https://azure.microsoft.com/support/community/).

* Contattare [@AzureSupport](https://twitter.com/azuresupport), l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Mette in contatto la community di Azure con le risorse giuste: risposte, supporto ed esperti.

* Se serve ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **Supporto** nella barra dei menu o aprire l'hub **Guida e supporto** . Per informazioni più dettagliate, vedere [Come creare una richiesta di supporto in Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). L'accesso al supporto per la gestione delle sottoscrizioni e la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).