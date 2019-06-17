---
title: Eseguire la migrazione di carichi di lavoro di Hive di Azure HDInsight 3.6 alla versione 4.0 di HDInsight
description: Informazioni su come eseguire la migrazione di carichi di lavoro di Apache Hive in HDInsight 3.6 di HDInsight 4.0.
ms.service: hdinsight
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.topic: howto
ms.date: 04/24/2019
ms.openlocfilehash: b39279e560cb1738ff9b33ec587562efd2ed4e8d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65800942"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Eseguire la migrazione di carichi di lavoro di Hive di Azure HDInsight 3.6 alla versione 4.0 di HDInsight

Questo documento illustra come eseguire la migrazione di carichi di lavoro di Apache Hive e LLAP in HDInsight 3.6 di HDInsight 4.0. HDInsight 4.0 fornisce funzionalità di Hive e LLAP più recenti, ad esempio le viste materializzate e memorizzazione nella cache di risultati di query. Quando si esegue la migrazione dei carichi di lavoro alla versione 4.0 di HDInsight, è possibile usare molte funzionalità più recenti di Hive 3 che non sono disponibili in HDInsight 3.6.

Questo articolo illustra gli argomenti seguenti:

* Migrazione dei metadati Hive di HDInsight 4.0
* Migrazione sicura di ACID e le tabelle non ACID
* Conservazione dei criteri di sicurezza di Hive in tutte le versioni di HDInsight
* L'esecuzione di query e il debug da HDInsight 3.6 di HDInsight 4.0

## <a name="migrate-apache-hive-metadata-to-hdinsight-40"></a>Eseguire la migrazione dei metadati di Apache Hive in HDInsight 4.0

Uno dei vantaggi di Hive è la possibilità di esportare i metadati in un database esterno (detto il Metastore Hive). Il **Metastore Hive** è responsabile della memorizzazione delle statistiche, incluso il percorso di archiviazione di tabella, nomi di colonna e informazioni sugli indici di tabella. Lo schema del database metastore differisce tra le versioni di Hive. Eseguire il comando seguente per eseguire l'aggiornamento di un Metastore Hive di HDInsight 3.6 in modo che siano compatibile con HDInsight 4.0.

1. Creare una nuova copia del metastore esterno. HDInsight 3.6 e 4.0 di HDInsight richiedono metastore diversi schemi e non possono condividere un singolo metastore. Visualizzare [usare gli archivi di metadati esterni in Azure HDInsight](../hdinsight-use-external-metadata-stores.md) per altre informazioni sul collegamento di un metastore esterno a un cluster HDInsight. 
2. Avviare un'azione di script sul cluster HDI 3.6, con "Nodi Head" come tipo di nodo per l'esecuzione. Incollare l'URI seguente nella casella di testo contrassegnate "URI Script Bash": https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh. Nella casella di testo contrassegnata "Argomenti", immettere il servername, database, nome utente e password per il **copiati** Hive metastore, separati da spazi. Non includere ". database.windows.net" quando si specifica il nome server.

> [!Warning]
> L'aggiornamento che consente di convertire lo schema dei metadati di HDInsight 3.6 allo schema 4.0 di HDInsight, non può essere annullata.

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Eseguire la migrazione di tabelle Hive in HDInsight 4.0

Dopo avere completato i passaggi necessari per eseguire la migrazione il Metastore Hive in HDInsight 4.0 precedente, le tabelle e i database registrati nel metastore saranno visibili all'interno del cluster HDInsight 4.0 eseguendo `show tables` o `show databases` all'interno del cluster . Visualizzare [esecuzione di Query in tutte le versioni di HDInsight](#query-execution-across-hdinsight-versions) per informazioni sull'esecuzione di query nei cluster HDInsight 4.0.

I dati effettivi dalle tabelle, tuttavia, non sono accessibili fino a quando il cluster può accedere agli account di archiviazione necessaria. Per assicurarsi che il cluster HDInsight 4.0 può accedere agli stessi dati come il vecchio cluster HDInsight 3.6, completare i passaggi seguenti:

1. Determinare l'account di archiviazione di Azure della tabella o database usando descrivono formattato.
2. Se il cluster HDInsight 4.0 è già in esecuzione, collegare l'account di archiviazione di Azure al cluster tramite Ambari. Se è stato ancora creato il cluster HDInsight 4.0, assicurarsi che l'account di archiviazione di Azure viene specificato come database primario o un account di archiviazione cluster secondario. Per altre informazioni sull'aggiunta di account di archiviazione per i cluster HDInsight, vedere [aggiungere altri account di archiviazione a HDInsight](../hdinsight-hadoop-add-storage.md).

> [!Note]
> Le tabelle vengono considerate in modo diverso in HDInsight 3.6 e 4.0 di HDInsight. Per questo motivo, è possibile condividere le stesse tabelle per i cluster di diverse versioni. Se si desidera usare HDInsight 3.6 allo stesso tempo come HDInsight 4.0, è necessario disporre di copie separate dei dati per ogni versione.

Il carico di lavoro di Hive può includere una combinazione di proprietà ACID e le tabelle non ACID. Una differenza essenziale tra Hive in HDInsight 3.6 (Hive 2) e Hive in HDInsight 4.0 (Hive 3) è la conformità ACID per le tabelle. In HDInsight 3.6, abilitazione della conformità ACID Hive richiede configurazione aggiuntiva, ma in 4.0 HDInsight le tabelle sono compatibili con proprietà ACID per impostazione predefinita. L'unica azione necessaria prima della migrazione consiste nell'eseguire una compattazione principali in base alla tabella ACID nel cluster di 3,6. Dalla vista di Hive o da Beeline, eseguire la query seguente:

```bash
alter table myacidtable compact 'major';
```

Compattazione è necessaria perché le tabelle di Python 3.6 di HDInsight e HDInsight 4.0 ACID comprendere in modo diverso i delta ACID. La compattazione di dati consente di applicare uno slate pulito che assicura una coerenza. Nella sezione 4 del [Hive documentazione sulla migrazione](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html) contiene materiale sussidiario per la compressione bulk delle tabelle di HDInsight 3.6 ACID.

Dopo aver completato i passaggi di migrazione e compattare al metastore, è possibile eseguire la migrazione del warehouse effettivo. Dopo aver completato la migrazione di warehouse di Hive, il warehouse 4.0 di HDInsight sarà disponibili le proprietà seguenti:

* Le tabelle esterne in HDInsight 3.6 sarà le tabelle esterne in HDInsight 4.0
* Non transazionale tabelle gestite in HDInsight 3.6 sarà le tabelle esterne in HDInsight 4.0
* Transazionale tabelle gestite in HDInsight 3.6 saranno essere gestito in HDInsight 4.0

Potrebbe essere necessario regolare le proprietà del warehouse prima di eseguire la migrazione. Ad esempio, se si prevede che alcuni tabella eseguiranno l'accesso a terze parti (ad esempio un cluster HDInsight 3.6), tale tabella deve essere esterna al termine della migrazione. In HDInsight 4.0, tutte le tabelle gestite sono transazionali. Di conseguenza, le tabelle gestite in HDInsight 4.0 accede solo dai cluster HDInsight 4.0.

Dopo aver impostate le proprietà di tabella in modo corretto, eseguire lo strumento di migrazione warehouse di Hive da uno dei nodi head del cluster usando la shell SSH:

1. Connettersi al nodo head del cluster tramite SSH. Per istruzioni, vedere [Connetti a HDInsight tramite SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. Aprire una shell di accesso dell'utente di Hive eseguendo `sudo su - hive`
1. Determinare la versione di Hortonworks Data Platform stack eseguendo `ls /usr/hdp`. Verrà visualizzata una stringa di versione che verrà usato nel comando successivo.
1. Eseguire il comando seguente dalla shell. Sostituire `${{STACK_VERSION}}` con la stringa di versione nel passaggio precedente:

```bash
/usr/hdp/${{STACK_VERSION}}/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true  -m automatic  automatic  --modifyManagedTables --oldWarehouseRoot /apps/hive/warehouse
```

Al termine dell'esecuzione dello strumento di migrazione, il warehouse di Hive sarà pronto per HDInsight 4.0. 

> [!Important]
> Tabelle gestite in HDInsight 4.0 (le tabelle tra cui la migrazione da 3.6) non accessibile da altri servizi o applicazioni, inclusi i cluster HDInsight 3.6.

## <a name="secure-hive-across-hdinsight-versions"></a>Hive sicuro tra le versioni di HDInsight

Poiché HDInsight 3.6, HDInsight si integra con Azure Active Directory usando HDInsight Enterprise Security Package (ESP). ESP utilizza Kerberos e Apache Ranger per gestire le autorizzazioni delle risorse specifiche all'interno del cluster. I criteri di Ranger distribuiti con Hive in HDInsight 3.6 possono essere migrati a HDInsight 4.0 con i passaggi seguenti:

1. Passare al pannello di Ranger Service Manager nel cluster HDInsight 3.6.
2. Passare al criterio denominato **HIVE** ed esportare i criteri in un file json.
3. Assicurarsi che tutti gli utenti a cui il codice json di criteri esportate siano presenti nel nuovo cluster. Se un utente fa riferimento nel file json dei criteri, ma non esiste nel nuovo cluster, aggiungere l'utente al nuovo cluster oppure rimuovere il riferimento dai criteri.
4. Passare il **Ranger Service Manager** pannello del cluster HDInsight 4.0.
5. Passare al criterio denominato **HIVE** e importare il file json dei criteri di ranger dal passaggio 2.

## <a name="query-execution-across-hdinsight-versions"></a>Esecuzione di query in tutte le versioni di HDInsight

Esistono due modi per eseguire ed eseguire il debug di query Hive/LLAP all'interno di un cluster HDInsight 3.6. HiveCLI offre un'esperienza da riga di comando e la visualizzazione Hive/visualizzazione Tez fornisce un flusso di lavoro basata su GUI.

In HDInsight 4.0 HiveCLI è stata sostituita con Beeline. HiveCLI è un client thrift per Hiveserver 1 e Beeline è un client JDBC che fornisce l'accesso del server hive 2. Beeline è anche utilizzabile per connettersi a qualsiasi altro endpoint compatibile con JDBC database. Beeline è disponibile out-of-box in HDInsight 4.0 senza tutte le installazioni necessita.

In HDInsight 3.6, il client di GUI per interagire con il server Hive è la vista Hive di Ambari. HDInsight 4.0 sostituisce la visualizzazione Hive con Hortonworks Data Analitica Studio (DAS). DAS non forniti con i cluster HDInsight out-of-box e non è un pacchetto ufficialmente supportato. DAS, tuttavia, può essere installato nel cluster come indicato di seguito:

Avviare un'azione di script sul cluster, con "Nodi Head" come tipo di nodo per l'esecuzione. Nella casella di testo contrassegnata "URI Script Bash", incollare l'URI seguente: https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh

Studio Analitica dei dati può essere avviata con URL: https://<clustername>.azurehdinsight.net/das/



Dopo aver installato DAS, se non viene visualizzata la query che è stato eseguito nel Visualizzatore di query, effettuare quanto segue:

1. Impostare le configurazioni per Hive e Tez DAS, come descritto in [questa guida per la risoluzione dei problemi di installazione di DAS](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html).
2. Assicurarsi che la seguente risorsa di archiviazione Azure directory Configs (configurazioni) i BLOB di pagine, ma che sono elencati sotto `fs.azure.page.blob.dirs`:
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. Riavviare HDFS, Hive, Tez e DAS su due nodi HEAD.

## <a name="next-steps"></a>Passaggi successivi

* [Annuncio di HDInsight 4.0](../hdinsight-version-release.md)
* [Approfondimento 4.0 di HDInsight](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [3 ACID tabelle hive](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
