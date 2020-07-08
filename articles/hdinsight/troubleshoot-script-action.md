---
title: Risolvere i problemi relativi alle azioni script in Azure HDInsight
description: Procedura generale per la risoluzione dei problemi per le azioni script in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: e8585779a263f4ff5dbdd998bbf065c6a4e1acdf
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86079260"
---
# <a name="troubleshoot-script-actions-in-azure-hdinsight"></a>Risolvere i problemi relativi alle azioni script in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="viewing-logs"></a>Visualizzazione dei log

È possibile usare l'interfaccia utente Web di Apache Ambari per visualizzare le informazioni registrate dalle azioni script. Se lo script ha esito negativo durante la creazione del cluster, i log si trovano nell'account di archiviazione del cluster predefinito. Questa sezione include informazioni su come recuperare i log usando entrambe le opzioni.

### <a name="apache-ambari-web-ui"></a>Interfaccia utente Web di Apache Ambari

1. In un Web browser passare a `https://CLUSTERNAME.azurehdinsight.net` dove `CLUSTERNAME` è il nome del cluster.

1. Nella barra nella parte superiore della pagina fare clic sulla voce **ops**. Viene visualizzato un elenco delle operazioni correnti e precedenti eseguite nel cluster tramite Ambari.

    ![Barra nell'interfaccia utente di Ambari con selezionato ops](./media/troubleshoot-script-action/hdi-apache-ambari-nav.png)

1. Trovare le voci con **run\_customscriptaction** nella colonna **Operations**. Queste voci vengono create quando si eseguono le azioni script.

    ![Operazioni di azione script di Apache Ambari](./media/troubleshoot-script-action/ambari-script-action.png)

    Selezionare la voce **run\customscriptaction** ed eseguire il drill-down dei collegamenti per visualizzare l'output di **STDOUT** e **STDERR**. Questo output viene generato all'esecuzione dello script e potrebbe contenere informazioni utili.

### <a name="default-storage-account"></a>Account di archiviazione predefinito

Se la creazione del cluster non riesce a causa di un errore nello script, i log vengono conservati nell'account di archiviazione cluster.

* I log di archiviazione sono disponibili in `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`.

    ![Script per log delle azioni](./media/troubleshoot-script-action/script-action-logs-in-storage.png)

    In questa directory i log sono organizzati per **nodi head**, **nodi di lavoro** e **nodi Zookeeper**. Vedere gli esempi seguenti:

    * **Nodo head**:`<ACTIVE-HEADNODE-NAME>.cloudapp.net`

    * **Nodo**del ruolo di lavoro:`<ACTIVE-WORKERNODE-NAME>.cloudapp.net`

    * **Nodo Zookeeper**:`<ACTIVE-ZOOKEEPERNODE-NAME>.cloudapp.net`

* Tutti i file **stdout** e **stderr** dell'host corrispondente vengono caricati nell'account di archiviazione. Per ogni azione script esiste un file **output-\*.txt** e un file **errors-\*.txt**. Il file **output-*.txt** contiene informazioni relative all'URI dello script che è stato eseguito nell'host. Il testo seguente è un esempio di queste informazioni:

    ```output
    'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'
    ```

* È possibile creare più volte un cluster dell'azione script con lo stesso nome. In questo caso, è possibile distinguere i log corrispondenti in base al nome della cartella **DATE**. Ad esempio, la struttura di cartelle per un cluster, **mycluster**, creato in diverse date sarà simile alle voci di log seguenti:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Se in uno stesso giorno si creano più cluster dell'azione script con lo stesso nome, è possibile usare il prefisso univoco per identificare i file di registro corrispondenti.

* Se si crea un cluster verso le 00.00 (mezzanotte), è possibile che i file di log si estendano su due giorni. In questo caso, per lo stesso cluster vengono visualizzate due diverse cartelle della data.

* Il caricamento dei file di log nel contenitore predefinito può richiedere fino a cinque minuti, soprattutto per i cluster di grandi dimensioni. Se si vuole accedere ai log, quindi, è opportuno non eliminare immediatamente il cluster in caso di esito negativo di un'azione script.

## <a name="ambari-watchdog"></a>Watchdog Ambari

Non modificare la password del watchdog Ambari (hdinsightwatchdog) nel cluster HDInsight basato su Linux. Una modifica della password interrompe la possibilità di eseguire nuove azioni script nel cluster HDInsight.

## <a name="cant-import-name-blobservice"></a>Non è possibile importare il nome BlobService

__Sintomi__. l'azione script non riesce. Quando si visualizza l'operazione in Ambari, viene visualizzato un errore simile al seguente:

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__Causa__. Questo errore si verifica se si aggiorna il client di archiviazione di Azure Python incluso con il cluster HDInsight. HDInsight prevede l'uso della versione 0.20.0 del client di archiviazione di Azure.

__Risoluzione__. Per risolvere questo errore, connettersi manualmente a ogni nodo del cluster tramite `ssh`. Eseguire il comando seguente per reinstallare la versione del client di archiviazione corretta:

```bash
sudo pip install azure-storage==0.20.0
```

Per informazioni sulla connessione al cluster con SSH, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>La cronologia non mostra gli script usati durante la creazione di un cluster

Se il cluster è stato creato prima del 15 marzo 2016, potrebbe non essere visualizzata una voce nella cronologia delle azioni script. Ridimensionando il cluster, gli script verranno visualizzati nella cronologia delle azioni script.

Sussistono due eccezioni:

* Il cluster è stato creato prima del 1° settembre 2015. Le azioni script sono state introdotte in questa data. Per i cluster creati prima di tale data non possono quindi essere state usate le azioni script.

* Sono state usate più azioni script durante la creazione del cluster. Oppure è stato usato lo stesso nome per più script oppure lo stesso nome e lo stesso URI ma parametri diversi per più script. In questi casi si riceve l'errore seguente:

    ```
    No new script actions can be run on this cluster because of conflicting script names in existing scripts. Script names provided at cluster creation must be all unique. Existing scripts are run on resize.
    ```

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ricevere risposte dagli esperti di Azure tramite la pagina [Supporto della community per Azure](https://azure.microsoft.com/support/community/).

* Contattare [@AzureSupport](https://twitter.com/azuresupport), l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Mette in contatto la community di Azure con le risorse giuste: risposte, supporto ed esperti.

* Se serve ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **Supporto** nella barra dei menu o aprire l'hub **Guida e supporto**. Per informazioni più dettagliate, vedere [Come creare una richiesta di supporto in Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione delle sottoscrizioni e la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
