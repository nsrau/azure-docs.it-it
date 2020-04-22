---
title: Risolvere i problemi relativi alle azioni script in Azure HDInsightTroubleshoot script actions in Azure HDInsight
description: Passaggi generali per la risoluzione dei problemi per le azioni di script in Azure HDInsight.General troubleshooting steps for script actions in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/21/2020
ms.openlocfilehash: b1e6b674edc155e0aa6c88ad360eb59864eebee4
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771976"
---
# <a name="troubleshoot-script-actions-in-azure-hdinsight"></a>Risolvere i problemi relativi alle azioni script in Azure HDInsightTroubleshoot script actions in Azure HDInsight

Questo articolo descrive i passaggi per la risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'interazione con i cluster HDInsight di Azure.This article describes troubleshooting steps and possible resolutions for issues when interacting with Azure HDInsight clusters.

## <a name="viewing-logs"></a>Visualizzazione dei log

È possibile utilizzare l'interfaccia utente Web Apache Ambari per visualizzare le informazioni registrate dalle azioni di script. Se lo script non riesce durante la creazione del cluster, i log si trovano nell'account di archiviazione cluster predefinito. Questa sezione include informazioni su come recuperare i log usando entrambe le opzioni.

### <a name="apache-ambari-web-ui"></a>Interfaccia utente Web di Apache Ambari

1. Da un Web browser `https://CLUSTERNAME.azurehdinsight.net`passare `CLUSTERNAME` a , dove è il nome del cluster.

1. Nella barra nella parte superiore della pagina fare clic sulla voce **ops**. Viene visualizzato un elenco delle operazioni correnti e precedenti eseguite nel cluster tramite Ambari.

    ![Barra nell'interfaccia utente di Ambari con selezionato ops](./media/troubleshoot-script-action/hdi-apache-ambari-nav.png)

1. Trovare le voci con **run\_customscriptaction** nella colonna **Operations**. Queste voci vengono create quando si eseguono le azioni script.

    ![Operazioni di azione script Apache Ambari](./media/troubleshoot-script-action/ambari-script-action.png)

    Selezionare la voce **run\customscriptaction** ed eseguire il drill-down dei collegamenti per visualizzare l'output di **STDOUT** e **STDERR**. Questo output viene generato all'esecuzione dello script e potrebbe contenere informazioni utili.

### <a name="default-storage-account"></a>Account di archiviazione predefinito

Se la creazione del cluster non riesce a causa di un errore nello script, i log vengono conservati nell'account di archiviazione cluster.

* I log di archiviazione sono disponibili in `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`.

    ![Registri azioni script](./media/troubleshoot-script-action/script-action-logs-in-storage.png)

    In questa directory i log sono organizzati per **nodi head**, **nodi di lavoro** e **nodi Zookeeper**. Vedere gli esempi seguenti:

    * **Nodo Head**:`<ACTIVE-HEADNODE-NAME>.cloudapp.net`

    * **Nodo lavoratore**:`<ACTIVE-WORKERNODE-NAME>.cloudapp.net`

    * **Nodo di guardiano dello zoo**:`<ACTIVE-ZOOKEEPERNODE-NAME>.cloudapp.net`

* Tutti i file **stdout** e **stderr** dell'host corrispondente vengono caricati nell'account di archiviazione. Per ogni azione script esiste un file **output-\*.txt** e un file **errors-\*.txt**. Il file **output-*.txt** contiene informazioni relative all'URI dello script che è stato eseguito nell'host. Il testo seguente è un esempio di queste informazioni:

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* È possibile creare più volte un cluster dell'azione script con lo stesso nome. In questo caso, è possibile distinguere i log corrispondenti in base al nome della cartella **DATE**. Ad esempio, la struttura di cartelle per un cluster, **mycluster**, creato in diverse date sarà simile alle voci di log seguenti:

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* Se in uno stesso giorno si creano più cluster dell'azione script con lo stesso nome, è possibile usare il prefisso univoco per identificare i file di registro corrispondenti.

* Se si crea un cluster verso le 00.00 (mezzanotte), è possibile che i file di log si estendano su due giorni. In questo caso, per lo stesso cluster vengono visualizzate due diverse cartelle della data.

* Il caricamento dei file di log nel contenitore predefinito può richiedere fino a cinque minuti, soprattutto per i cluster di grandi dimensioni. Se si vuole accedere ai log, quindi, è opportuno non eliminare immediatamente il cluster in caso di esito negativo di un'azione script.

## <a name="ambari-watchdog"></a>Watchdog Ambari

Non modificare la password del watchdog Ambari (hdinsightwatchdog) nel cluster HDInsight basato su Linux. Una modifica della password interrompe la possibilità di eseguire nuove azioni di script nel cluster HDInsight.A password change breaks the ability to run new script actions on the HDInsight cluster.

## <a name="cant-import-name-blobservice"></a>Non è possibile importare il nome BlobService

__I sintomi__. l'azione script non riesce. Quando si visualizza l'operazione in Ambari, viene visualizzato un errore simile al seguente:

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

* Ottieni risposte dagli esperti di Azure tramite il supporto della community di [Azure.](https://azure.microsoft.com/support/community/)

* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con - l'account ufficiale di Microsoft Azure per migliorare l'esperienza del cliente. Connessione della community di Azure alle risorse giuste: risposte, supporto ed esperti.

* Per altre informazioni, è possibile inviare una richiesta di supporto dal portale di [Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selezionare **Supporto** dalla barra dei menu o aprire l'hub **Guida e supporto** tecnico. Per informazioni più dettagliate, vedere Come creare una richiesta di supporto di Azure.For more detailed information, review [How to create an Azure support request](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione e la fatturazione delle sottoscrizioni è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei piani di supporto di [Azure.](https://azure.microsoft.com/support/plans/)