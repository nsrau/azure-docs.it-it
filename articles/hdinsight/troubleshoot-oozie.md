---
title: Risolvere i problemi di Apache oozie in Azure HDInsight
description: Risolvere alcuni errori di Apache oozie in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.openlocfilehash: edbe5274de8576fccb29e1e69d260a6531d4ab05
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287404"
---
# <a name="troubleshoot-apache-oozie-in-azure-hdinsight"></a>Risolvere i problemi di Apache oozie in Azure HDInsight

Con l'interfaccia utente di Apache oozie è possibile visualizzare i log di oozie. Contiene anche collegamenti ai log di JobTracker per le attività di MapReduce avviate dal flusso di lavoro. Il modello per la risoluzione dei problemi deve essere il seguente:

1. Visualizzare il processo nell'interfaccia utente Web di Oozie.

2. Se si verifica un errore o un errore per un'azione specifica, selezionare l'azione per verificare se nel campo del **messaggio di errore** sono disponibili ulteriori informazioni sull'errore.

3. Se disponibile, usare l'URL dall'azione per visualizzare altri dettagli relativi all'azione, ad esempio i log di JobTracker.

Di seguito sono riportati gli errori specifici che si potrebbero incontrare e come risolverli.

## <a name="ja009-cant-initialize-cluster"></a>JA009: Impossibile inizializzare il cluster

### <a name="issue"></a>Problema

lo stato del processo cambia in **SUSPENDED**. I dettagli del processo mostrano lo stato di `RunHiveScript` come **START_MANUAL**. Selezionando l'azione verrà visualizzato il messaggio di errore seguente:

```output
JA009: Cannot initialize Cluster. Please check your configuration for map
```

### <a name="cause"></a>Causa

gli indirizzi di archiviazione BLOB di Azure usati nel file **job.xml** non includono il contenitore di archiviazione o il nome dell'account di archiviazione. Il formato dell'indirizzo di archiviazione BLOB deve essere `wasbs://containername@storageaccountname.blob.core.windows.net`.

### <a name="resolution"></a>Soluzione

modificare gli indirizzi di archiviazione BLOB usati dal processo.

---

## <a name="ja002-oozie-isnt-allowed-to-impersonate-ltusergt"></a>JA002: oozie non è autorizzato a rappresentare l' &lt; utente&gt;

### <a name="issue"></a>Problema

lo stato del processo cambia in **SUSPENDED**. I dettagli del processo mostrano lo stato di `RunHiveScript` come **START_MANUAL**. Se si seleziona l'azione, viene visualizzato il messaggio di errore seguente:

```output
JA002: User: oozie is not allowed to impersonate <USER>
```

### <a name="cause"></a>Causa

le impostazioni di autorizzazione correnti non consentono a Oozie di rappresentare l'account utente specificato.

### <a name="resolution"></a>Soluzione

Oozie può rappresentare gli utenti nel **`users`** gruppo. Usare `groups USERNAME` per visualizzare i gruppi di cui l'account utente è membro. Se l'utente non è membro del **`users`** gruppo, usare il comando seguente per aggiungere l'utente al gruppo:

```bash
sudo adduser USERNAME users
```

> [!NOTE]  
> Potrebbero essere necessari alcuni minuti prima che HDInsight riconosca che l'utente è stato aggiunto al gruppo.

---

## <a name="launcher-error-sqoop"></a>ERRORE dell'utilità di avvio (Sqoop)

### <a name="issue"></a>Problema

lo stato del processo cambia in **KILLED**. I dettagli del processo mostrano lo stato di `RunSqoopExport` come **ERROR**. Se si seleziona l'azione, viene visualizzato il messaggio di errore seguente:

```output
Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]
```

### <a name="cause"></a>Causa

Sqoop non è in grado di caricare il driver del database necessario per accedere al database.

### <a name="resolution"></a>Soluzione

quando si usa Sqoop da un processo Oozie, è necessario includere il driver del database con le altre risorse usate dal processo, ad esempio workflow.xml. Fare inoltre riferimento all'archivio contenente il driver del database dalla sezione `<sqoop>...</sqoop>` di workflow.xml.

Per l'esempio di processo da usare i [flussi](hdinsight-use-oozie-linux-mac.md)di lavoro oozie di Hadoop, ad esempio, è possibile usare i passaggi seguenti:

1. Copiare il `mssql-jdbc-7.0.0.jre8.jar` file nella directory **nella/Tutorials/useoozie** :

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc-7.0.0.jre8.jar /tutorials/useoozie/mssql-jdbc-7.0.0.jre8.jar
    ```

2. Modificare `workflow.xml` per aggiungere il codice XML seguente in una nuova riga sopra `</sqoop>`:

    ```xml
    <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
    ```

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]