---
title: Eseguire la migrazione di carichi di lavoro hive di Azure HDInsight 3,6 a HDInsight 4,0
description: Informazioni su come eseguire la migrazione di carichi di lavoro Apache Hive in HDInsight 3,6 in HDInsight 4,0.
ms.service: hdinsight
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: eb6b42a88e47e3fcb35700ec542c8ca642e17a6a
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815799"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Eseguire la migrazione di carichi di lavoro hive di Azure HDInsight 3,6 a HDInsight 4,0

Questo documento illustra come eseguire la migrazione di carichi di lavoro Apache Hive e LLAP in HDInsight 3,6 a HDInsight 4,0. HDInsight 4,0 fornisce le più recenti funzionalità hive e LLAP, ad esempio viste materializzate e Caching risultati query. Quando si esegue la migrazione dei carichi di lavoro a HDInsight 4,0, è possibile usare molte funzionalità più recenti di hive 3 che non sono disponibili in HDInsight 3,6.

In questo articolo vengono trattati gli argomenti seguenti:

* Migrazione di metadati hive a HDInsight 4,0
* Migrazione sicura di tabelle ACID e non ACID
* Conservazione dei criteri di sicurezza hive tra le versioni di HDInsight
* Esecuzione di query e debug da HDInsight 3,6 a HDInsight 4,0

## <a name="migrate-apache-hive-metadata-to-hdinsight-40"></a>Migrare i metadati di Apache Hive a HDInsight 4,0

Un vantaggio di hive è la possibilità di esportare metadati in un database esterno (denominato Metastore hive). Il **Metastore hive** è responsabile dell'archiviazione delle statistiche della tabella, inclusi il percorso di archiviazione delle tabelle, i nomi delle colonne e le informazioni sull'indice della tabella. Lo schema del database del Metastore è diverso tra le versioni di hive. Eseguire le operazioni seguenti per aggiornare un Metastore hive HDInsight 3,6 in modo che sia compatibile con HDInsight 4,0.

1. Creare una nuova copia del Metastore esterno. HDInsight 3,6 e HDInsight 4,0 richiedono schemi di Metastore diversi e non possono condividere un singolo Metastore. Vedere [usare gli archivi di metadati esterni in Azure HDInsight](../hdinsight-use-external-metadata-stores.md) per altre informazioni sul fissaggio di un Metastore esterno a un cluster HDInsight. 
2. Avviare un'azione script sul cluster HDI 3,6, con "nodi head" come tipo di nodo per l'esecuzione. Incollare l'URI seguente nella casella di testo contrassegnata come "bash script https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh URI":. Nella casella di testo contrassegnata con "Arguments" immettere il nome del database, il nome utente e la password per il metastore Hive **copiato** , separati da spazi. Non includere ". database.windows.net" quando si specifica servername.

> [!Warning]
> L'aggiornamento che converte lo schema di metadati HDInsight 3,6 nello schema HDInsight 4,0 non può essere annullato.

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Eseguire la migrazione di tabelle hive a HDInsight 4,0

Dopo aver completato il set di passaggi precedente per eseguire la migrazione del Metastore hive a HDInsight 4,0, le tabelle e i database registrati nel Metastore saranno visibili all'interno del cluster HDInsight 4,0 eseguendo `show tables` o `show databases` dall'interno del cluster. . Per informazioni sull'esecuzione di query nei cluster HDInsight 4,0, vedere [esecuzione di query tra versioni di HDInsight](#query-execution-across-hdinsight-versions) .

Tuttavia, i dati effettivi delle tabelle non sono accessibili fino a quando il cluster non ha accesso agli account di archiviazione necessari. Per assicurarsi che il cluster HDInsight 4,0 possa accedere agli stessi dati del cluster HDInsight 3,6 precedente, completare i passaggi seguenti:

1. Determinare l'account di archiviazione di Azure della tabella o del database usando Descrivi formattato.
2. Se il cluster HDInsight 4,0 è già in esecuzione, allegare l'account di archiviazione di Azure al cluster tramite Ambari. Se non è ancora stato creato il cluster HDInsight 4,0, verificare che l'account di archiviazione di Azure sia specificato come account di archiviazione del cluster primario o secondario. Per altre informazioni sull'aggiunta di account di archiviazione ai cluster HDInsight, vedere [aggiungere altri account di archiviazione a HDInsight](../hdinsight-hadoop-add-storage.md).

> [!Note]
> Le tabelle sono gestite in modo diverso in HDInsight 3,6 e HDInsight 4,0. Per questo motivo, non è possibile condividere le stesse tabelle per i cluster di versioni diverse. Se si desidera utilizzare HDInsight 3,6 allo stesso tempo di HDInsight 4,0, è necessario disporre di copie separate dei dati per ogni versione.

Il carico di lavoro hive può includere una combinazione di tabelle ACID e non ACID. Una differenza fondamentale tra hive in HDInsight 3,6 (hive 2) e hive in HDInsight 4,0 (hive 3) è la conformità ACID per le tabelle. In HDInsight 3,6, per abilitare la conformità ACID hive è necessaria una configurazione aggiuntiva, ma nelle tabelle HDInsight 4,0 sono conformi agli ACID per impostazione predefinita. L'unica azione necessaria prima della migrazione consiste nell'eseguire una compattazione principale sulla tabella ACID nel cluster 3,6. Dalla vista hive o da l'oggetto con cui eseguire la query seguente:

```bash
alter table myacidtable compact 'major';
```

Questa compattazione è necessaria perché le tabelle ACID HDInsight 3,6 e HDInsight 4,0 comprendono i Delta ACID in modo diverso. La compattazione impone uno Slate pulito che garantisce la coerenza. La sezione 4 della [documentazione relativa alla migrazione di hive](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html) contiene informazioni aggiuntive per la compattazione delle tabelle HDINSIGHT 3,6 acid.

Una volta completati i passaggi per la migrazione e la compressione dei Metastore, è possibile eseguire la migrazione del warehouse effettivo. Dopo aver completato la migrazione del warehouse di hive, il warehouse HDInsight 4,0 avrà le proprietà seguenti:

* Le tabelle esterne in HDInsight 3,6 saranno tabelle esterne in HDInsight 4,0
* Le tabelle gestite non transazionali in HDInsight 3,6 saranno tabelle esterne in HDInsight 4,0
* Le tabelle gestite transazionali in HDInsight 3,6 saranno tabelle gestite in HDInsight 4,0

Prima di eseguire la migrazione, potrebbe essere necessario modificare le proprietà del data warehouse. Se, ad esempio, si prevede che una tabella sarà accessibile da terze parti, ad esempio un cluster HDInsight 3,6, tale tabella deve essere esterna al termine della migrazione. In HDInsight 4,0 tutte le tabelle gestite sono transazionali. Pertanto, le tabelle gestite in HDInsight 4,0 devono essere accessibili solo dai cluster HDInsight 4,0.

Una volta impostate correttamente le proprietà della tabella, eseguire lo strumento di migrazione del warehouse di hive da uno dei nodi head del cluster usando la shell SSH:

1. Connettersi al cluster nodo head usando SSH. Per istruzioni, vedere [connettersi a HDInsight tramite SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. Aprire una shell di accesso come utente hive eseguendo`sudo su - hive`
1. Determinare la versione `ls /usr/hdp`dello stack della piattaforma dati Hortonworks eseguendo. Verrà visualizzata una stringa di versione da usare nel comando successivo.
1. Eseguire il comando seguente dalla Shell. Sostituire `${{STACK_VERSION}}` con la stringa di versione del passaggio precedente:

```bash
/usr/hdp/${{STACK_VERSION}}/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true  -m automatic  automatic  --modifyManagedTables --oldWarehouseRoot /apps/hive/warehouse
```

Al termine dell'esecuzione dello strumento di migrazione, il warehouse di hive sarà pronto per HDInsight 4,0. 

> [!Important]
> Le tabelle gestite in HDInsight 4,0 (incluse le tabelle migrate da 3,6) non devono essere accessibili da altri servizi o applicazioni, inclusi i cluster HDInsight 3,6.

## <a name="secure-hive-across-hdinsight-versions"></a>Sicurezza hive tra le versioni di HDInsight

Dal momento che HDInsight 3,6, HDInsight si integra con Azure Active Directory usando HDInsight Enterprise Security Package (ESP). ESP USA Kerberos e Apache Ranger per gestire le autorizzazioni di risorse specifiche all'interno del cluster. È possibile eseguire la migrazione dei criteri Ranger distribuiti in hive in HDInsight 3,6 in HDInsight 4,0 con i passaggi seguenti:

1. Passare al pannello Service Manager Ranger nel cluster HDInsight 3,6.
2. Passare al criterio denominato **hive** ed esportare il criterio in un file JSON.
3. Assicurarsi che tutti gli utenti a cui viene fatto riferimento nel file JSON dei criteri esportati esistano nel nuovo cluster. Se a un utente viene fatto riferimento nel codice JSON dei criteri ma non esiste nel nuovo cluster, aggiungere l'utente al nuovo cluster o rimuovere il riferimento dal criterio.
4. Passare al pannello **Service Manager Ranger** nel cluster HDInsight 4,0.
5. Passare al criterio denominato **hive** e importare il codice JSON dei criteri Ranger dal passaggio 2.

## <a name="query-execution-across-hdinsight-versions"></a>Esecuzione di query tra versioni di HDInsight

Esistono due modi per eseguire ed eseguire il debug di query hive/LLAP all'interno di un cluster HDInsight 3,6. HiveCLI offre un'esperienza da riga di comando e la visualizzazione Tez/hive fornisce un flusso di lavoro basato su GUI.

In HDInsight 4,0, HiveCLI è stato sostituito da. HiveCLI è un client di parsimonia per del 1 e è un client JDBC che fornisce l'accesso a del 2. È inoltre possibile utilizzare l'estensione per connettersi a qualsiasi altro endpoint del database compatibile con JDBC. È possibile usare l'installazione predefinita di HDInsight 4,0 senza alcuna installazione necessaria.

In HDInsight 3,6, il client GUI per interagire con il server hive è la visualizzazione hive di Ambari. HDInsight 4,0 sostituisce la visualizzazione hive con Hortonworks Data Analytics Studio (DAS). DAS non viene fornito con cluster HDInsight predefiniti e non è un pacchetto ufficialmente supportato. Tuttavia, è possibile installare DAS nel cluster come indicato di seguito:

Avviare un'azione script nel cluster con "nodi head" come tipo di nodo per l'esecuzione. Incollare l'URI seguente nella casella di testo contrassegnata come "bash script URI": https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh

Attendere da 5 a 10 minuti, quindi avviare Data Analytics studio usando questo URL: https://\<clustername >. azurehdinsight. NET/das/

Dopo aver installato DAS, se non vengono visualizzate le query eseguite nel Visualizzatore query, seguire questa procedura:

1. Impostare le configurazioni per hive, Tez e DAS come descritto in [questa guida per la risoluzione dei problemi relativi all'installazione di das](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html).
2. Assicurarsi che le seguenti configurazioni di directory di archiviazione di Azure siano BLOB di pagine e che siano elencate in `fs.azure.page.blob.dirs`:
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. Riavviare HDFS, hive, Tez e DAS in entrambi nodi head.

## <a name="next-steps"></a>Passaggi successivi

* [Annuncio HDInsight 4,0](../hdinsight-version-release.md)
* [Approfondimento su HDInsight 4,0](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Tabelle ACID hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
