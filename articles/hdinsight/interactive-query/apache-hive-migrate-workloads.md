---
title: Eseguire la migrazione dei carichi di lavoro di Azure HDInsight 3.6 Hive a HDInsight 4.0Migrate Azure HDInsight 3.6 Hive workloads to HDInsight 4.0
description: Informazioni su come eseguire la migrazione dei carichi di lavoro di Apache Hive in HDInsight 3.6 a HDInsight 4.0.Learn how to migrate Apache Hive workloads on HDInsight 3.6 to HDInsight 4.0.
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: ec96189185a06c1fcbd95eed6216ade47f3089c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214653"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Eseguire la migrazione dei carichi di lavoro di Azure HDInsight 3.6 Hive a HDInsight 4.0Migrate Azure HDInsight 3.6 Hive workloads to HDInsight 4.0

Questo documento illustra come eseguire la migrazione dei carichi di lavoro Apache Hive e LLAP su HDInsight 3.6 a HDInsight 4.0.This document shows you how to migrate Apache Hive and LLAP workloads on HDInsight 3.6 to HDInsight 4.0. HDInsight 4.0 fornisce funzionalità Hive e LLAP più recenti, ad esempio viste materializzate e memorizzazione nella cache dei risultati delle query. Quando si esegue la migrazione dei carichi di lavoro a HDInsight 4.0, è possibile usare molte funzionalità più recenti di Hive 3 che non sono disponibili in HDInsight 3.6.When you migrate your workloads to HDInsight 4.0, you can use many newer features of Hive 3 that aren't available on HDInsight 3.6.

In questo articolo vengono trattati i seguenti argomenti:

* Migrazione dei metadati Hive in HDInsight 4.0Migration of Hive metadata to HDInsight 4.0
* Migrazione sicura di tavoli ACID e non ACID
* Conservazione dei criteri di sicurezza di Hive nelle versioni di HDInsight
* Esecuzione di query e debug da HDInsight 3.6 a HDInsight 4.0

Uno dei vantaggi di Hive è la possibilità di esportare i metadati in un database esterno (denominato Hive Metastore). Hive **Metastore** è responsabile dell'archiviazione delle statistiche delle tabelle, inclusi il percorso di archiviazione delle tabelle, i nomi delle colonne e le informazioni sull'indice delle tabelle. Lo schema del database del metastore è diverso tra le versioni di Hive.The metastore database schema differs between Hive versions. Il modo consigliato per aggiornare il metastore Hive in modo sicuro consiste nel creare una copia e aggiornare la copia anziché l'ambiente di produzione corrente.

## <a name="copy-metastore"></a>Copia metastore

HDInsight 3.6 e HDInsight 4.0 richiedono schemi di metastore diversi e non possono condividere un singolo metastore.

### <a name="external-metastore"></a>Metaarchivio esterno

Creare una nuova copia del metastore esterno. Se si utilizza un metastore esterno, uno dei modi semplici e sicuri per creare una copia del metastore consiste [nel ripristinare il Database](../../sql-database/sql-database-recovery-using-backups.md#point-in-time-restore) con un nome diverso utilizzando la funzione di ripristino del database SQL.  Per altre informazioni sul collegamento di un metastore esterno a un cluster HDInsight, vedere [Usare archivi di metadati esterni in Azure HDInsight.See](../hdinsight-use-external-metadata-stores.md) Use external metadata stores in Azure HDInsight to learn more about attaching an external metastore to an HDInsight cluster.

### <a name="internal-metastore"></a>Metaarchivio interno

Se si utilizza il metastore interno, è possibile utilizzare le query per esportare le definizioni degli oggetti nel metastore Hive e importarle in un nuovo database.

Una volta completato questo script, si presuppone che il cluster precedente non verrà più utilizzato per accedere alle tabelle o ai database a cui si fa riferimento nello script.

> [!NOTE]
> Nel caso delle tabelle ACID, verrà creata una nuova copia dei dati sotto la tabella.

1. Connettersi al cluster HDInsight utilizzando un [client Secure Shell (SSH).](../hdinsight-hadoop-linux-use-ssh-unix.md)

1. Connettersi a HiveServer2 con il [client Beeline](../hadoop/apache-hadoop-use-hive-beeline.md) dalla sessione SSH aperta immettendo il comando seguente:

    ```hiveql
    for d in `beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show databases;"`; 
    do
        echo "Scanning Database: $d"
        echo "create database if not exists $d; use $d;" >> alltables.hql; 
        for t in `beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show tables;"`;
        do
            echo "Copying Table: $t"
            ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;"`;

            echo "$ddl;" >> alltables.hql;
            lowerddl=$(echo $ddl | awk '{print tolower($0)}')
            if [[ $lowerddl == *"'transactional'='true'"* ]]; then
                if [[ $lowerddl == *"partitioned by"* ]]; then
                    # partitioned
                    raw_cols=$(beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;" | tr '\n' ' ' | grep -io "CREATE TABLE .*" | cut -d"(" -f2- | cut -f1 -d")" | sed 's/`//g');
                    ptn_cols=$(beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;" | tr '\n' ' ' | grep -io "PARTITIONED BY .*" | cut -f1 -d")" | cut -d"(" -f2- | sed 's/`//g');
                    final_cols=$(echo "(" $raw_cols "," $ptn_cols ")")

                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "create external table ext_$t $final_cols TBLPROPERTIES ('transactional'='false');";
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "insert into ext_$t select * from $t;";
                    staging_ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table ext_$t;"`;
                    dir=$(echo $staging_ddl | grep -io " LOCATION .*" | grep -m1 -o "'.*" | sed "s/'[^-]*//2g" | cut -c2-);

                    parsed_ptn_cols=$(echo $ptn_cols| sed 's/ [a-z]*,/,/g' | sed '$s/\w*$//g');
                    echo "create table flattened_$t $final_cols;" >> alltables.hql;
                    echo "load data inpath '$dir' into table flattened_$t;" >> alltables.hql;
                    echo "insert into $t partition($parsed_ptn_cols) select * from flattened_$t;" >> alltables.hql;
                    echo "drop table flattened_$t;" >> alltables.hql;
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "drop table ext_$t";
                else
                    # not partitioned
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "create external table ext_$t like $t TBLPROPERTIES ('transactional'='false');";
                    staging_ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table ext_$t;"`;
                    dir=$(echo $staging_ddl | grep -io " LOCATION .*" | grep -m1 -o "'.*" | sed "s/'[^-]*//2g" | cut -c2-);

                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "insert into ext_$t select * from $t;";
                    echo "load data inpath '$dir' into table $t;" >> alltables.hql;
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "drop table ext_$t";
                fi
            fi
            echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t;" >> alltables.hql;
        done;
    done
    ```

    Questo comando genera un file denominato **alltables.hql**.

1. Chiudere la sessione SSH. Quindi immettere un comando scp per scaricare **alltables.hql** localmente.

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.hql c:/hdi
    ```

1. Caricare alltables.hql nel nuovo cluster HDInsight.Upload **alltables.hql** to the *new* HDInsight cluster.

    ```bash
    scp c:/hdi/alltables.hql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. Usare quindi SSH per connettersi al nuovo cluster HDInsight.Then use SSH to connect to the *new* HDInsight cluster. Eseguire il codice seguente dalla sessione SSH:

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -i alltables.hql
    ```


## <a name="upgrade-metastore"></a>Metastore di aggiornamento

Al termine della **copia** del metastore, eseguire uno script di aggiornamento dello schema in [Azione](../hdinsight-hadoop-customize-cluster-linux.md) script nel cluster HDInsight 3.6 esistente per aggiornare il nuovo metastore allo schema Hive 3. In questo modo il database da collegare come HDInsight 4.0 metastore.

Utilizzare i valori nella tabella sottostante. Sostituire `SQLSERVERNAME DATABASENAME USERNAME PASSWORD` con i valori appropriati per il metastore Hive **copiato,** separati da spazi. Non includere ".database.windows.net" quando si specifica il nome del server SQL.

|Proprietà | valore |
|---|---|
|Tipo di script|- Personalizzato|
|Nome|Aggiornamento hive|
|URI script Bash|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|Tipo/i di nodo|Head|
|Parametri|SQLSERVERNAME NOMEDATABASE NOME UTENTE PASSWORD|

> [!Warning]  
> L'aggiornamento che converte lo schema di metadati HDInsight 3.6 nello schema HDInsight 4.0 non può essere invertito.

È possibile verificare l'aggiornamento eseguendo la query SQL seguente sul database:

```sql
select * from dbo.version
```

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Eseguire la migrazione delle tabelle Hive a HDInsight 4.0Migrate Hive tables to HDInsight 4.0

Dopo aver completato il set precedente di passaggi per eseguire la migrazione di Hive Metastore a HDInsight 4.0, le tabelle e `show tables` `show databases` i database registrati nel metastore saranno visibili dall'interno del cluster HDInsight 4.0 eseguendo o dall'interno del cluster. Per informazioni sull'esecuzione di query nei cluster HDInsight 4.0, [vedere Esecuzione di query tra versioni di HDInsight.See Query execution across HDInsight versions](#query-execution-across-hdinsight-versions) for information on query execution in HDInsight 4.0 clusters.

I dati effettivi delle tabelle, tuttavia, non sono accessibili fino a quando il cluster non ha accesso agli account di archiviazione necessari. Per assicurarsi che il cluster HDInsight 4.0 possa accedere agli stessi dati del cluster HDInsight 3.6 precedente, completare i passaggi seguenti:To make sure your HDInsight 4.0 cluster can access the same data as your old HDInsight 3.6 cluster, complete the following steps:

1. Determinare l'account di archiviazione di Azure della tabella o del database.

1. Se il cluster HDInsight 4.0 è già in esecuzione, collegare l'account di archiviazione di Azure al cluster tramite Ambari.If your HDInsight 4.0 cluster is already running, attach the Azure storage account to the cluster via Ambari. Se il cluster HDInsight 4.0 non è ancora stato creato, assicurarsi che l'account di archiviazione di Azure sia specificato come account di archiviazione del cluster primario o secondario. Per altre informazioni sull'aggiunta di account di archiviazione ai cluster HDInsight, vedere Aggiungere altri account di archiviazione a HDInsight.For more information about adding storage accounts to HDInsight clusters, see [Add additional storage accounts to HDInsight.](../hdinsight-hadoop-add-storage.md)

## <a name="deploy-new-hdinsight-40-and-connect-to-the-new-metastore"></a>Distribuire il nuovo HDInsight 4.0 e connettersi al nuovo metastoreDeploy new HDInsight 4.0 and connect to the new metastore

Al termine dell'aggiornamento dello schema, distribuire un nuovo cluster HDInsight 4.0 e connettere il metastore aggiornato. Se hai già distribuito la 4.0, impostala in modo da poterti connettere al metastore da Ambari.

## <a name="run-schema-migration-script-from-hdinsight-40"></a>Eseguire lo script di migrazione dello schema da HDInsight 4.0Run schema migration script from HDInsight 4.0

Le tabelle vengono trattate in modo diverso in HDInsight 3.6 e HDInsight 4.0.Tables are treated differently in HDInsight 3.6 and HDInsight 4.0. Per questo motivo, non è possibile condividere le stesse tabelle per cluster di versioni diverse. Se si vuole usare HDInsight 3.6 contemporaneamente a HDInsight 4.0, è necessario disporre di copie separate dei dati per ogni versione.

Il carico di lavoro Hive può includere una combinazione di tabelle ACID e non ACID. Una differenza fondamentale tra Hive in HDInsight 3.6 (Hive 2) e Hive in HDInsight 4.0 (Hive 3) è la conformità ACID per le tabelle. In HDInsight 3.6, l'abilitazione della conformità di Hive ACID richiede una configurazione aggiuntiva, ma in HDInsight 4.0 le tabelle sono compatibili con ACID per impostazione predefinita. L'unica azione richiesta prima della migrazione consiste nell'eseguire una compattazione principale rispetto alla tabella ACID nel cluster 3.6. Dalla vista Hive o da Beeline, eseguire la query seguente:

```sql
alter table myacidtable compact 'major';
```

Questa compattazione è necessaria perché le tabelle HDInsight 3.6 e HDInsight 4.0 ACID comprendono i delta ACID in modo diverso. La compattazione impone una lavagna pulita che garantisce coerenza. La sezione 4 della [documentazione relativa](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html) alla migrazione di Hive contiene indicazioni per la compattazione in blocco delle tabelle ACID di HDInsight 3.6.

Dopo aver completato i passaggi di migrazione e compattazione dei metastore, è possibile eseguire la migrazione del magazzino effettivo. Dopo aver completato la migrazione del magazzino Hive, il warehouse HDInsight 4.0 disporrà delle proprietà seguenti:After you complete the Hive warehouse migration, the HDInsight 4.0 warehouse will have the following properties:

|3.6 |4.0 |
|---|---|
|Tabelle esterne|Tabelle esterne|
|Tabelle gestite non transazionali|Tabelle esterne|
|Tabelle gestite transazionaliTransactional managed tables|Tabelle gestite|

Potrebbe essere necessario modificare le proprietà del magazzino prima di eseguire la migrazione. Ad esempio, se si prevede che una tabella sarà accessibile da una terza parte (ad esempio un cluster HDInsight 3.6), tale tabella deve essere esterna al termine della migrazione. In HDInsight 4.0 tutte le tabelle gestite sono transazionali. Pertanto, le tabelle gestite in HDInsight 4.0 devono essere accessibili solo dai cluster HDInsight 4.0.Therefore, managed tables in HDInsight 4.0 should only be accessed by HDInsight 4.0 clusters.

Una volta che le proprietà della tabella sono impostate correttamente, eseguire lo strumento di migrazione warehouse Hive da uno dei nodi del cluster utilizzando la shell SSH:

1. Connettersi al nodo head del cluster utilizzando SSH. Per istruzioni, vedere [Connettersi a HDInsight tramite SSHFor](../hdinsight-hadoop-linux-use-ssh-unix.md) instructions, see Connect to HDInsight using SSH
1. Aprire una shell di accesso come utente Hive eseguendo`sudo su - hive`
1. Determinare la versione dello stack `ls /usr/hdp`della piattaforma dati eseguendo . Verrà visualizzata una stringa di versione da utilizzare nel comando successivo.
1. Eseguire il comando seguente dalla shell. Sostituire `STACK_VERSION` con la stringa di versione del passaggio precedente:Replace with the version string from the previous step:

```bash
/usr/hdp/STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

Al termine dello strumento di migrazione, il magazzino Hive sarà pronto per HDInsight 4.0.After the migration tool completes, your Hive warehouse will be ready for HDInsight 4.0.

> [!Important]  
> Le tabelle gestite in HDInsight 4.0 (incluse le tabelle migrate da 3.6) non devono essere accessibili da altri servizi o applicazioni, inclusi i cluster HDInsight 3.6.

## <a name="secure-hive-across-hdinsight-versions"></a>Secure Hive tra le versioni HDInsightSecure Hive across HDInsight versions

Da HDInsight 3.6, HDInsight si integra con Azure Active Directory usando HDInsight Enterprise Security Package (ESP). ESP utilizza Kerberos e Apache Ranger per gestire le autorizzazioni di risorse specifiche all'interno del cluster. I criteri Ranger distribuiti in Hive in HDInsight 3.6 possono essere migrati a HDInsight 4.0 con i passaggi seguenti:Ranger policies deployed against Hive in HDInsight 3.6 can be migrated to HDInsight 4.0 with the following steps:

1. Passare al pannello Ranger Service Manager nel cluster HDInsight 3.6.Navigate to the Ranger Service Manager panel in your HDInsight 3.6 cluster.
2. Passare al criterio denominato **HIVE** ed esportarlo in un file json.
3. Assicurarsi che tutti gli utenti a cui si fa riferimento nel criterio esportato json esistano nel nuovo cluster. Se un utente fa riferimento nel criterio json ma non esiste nel nuovo cluster, aggiungere l'utente al nuovo cluster o rimuovere il riferimento dal criterio.
4. Passare al pannello Ranger Service Manager nel cluster HDInsight 4.0.Navigate to the **Ranger Service Manager** panel in your HDInsight 4.0 cluster.
5. Passare al criterio denominato HIVE e importare il criterio del ranger json dal passaggio 2.Navigate to the policy named **HIVE** and import the ranger policy json from step 2.

## <a name="check-compatibility-and-modify-codes-as-needed-in-test-app"></a>Verificare la compatibilità e modificare i codici in base alle esigenze nell'app di testCheck compatibility and modify codes as needed in test app

Quando si esegue la migrazione di carichi di lavoro, ad esempio programmi e query esistenti, controllare le note sulla versione e la documentazione per le modifiche e applicare le modifiche in base alle esigenze. Se il cluster HDInsight 3.6 usa un metastore condiviso Spark e Hive, è necessaria [una configurazione aggiuntiva con Hive Warehouse Connector.If](./apache-hive-warehouse-connector.md) your HDInsight 3.6 cluster is using a shared Spark and Hive metastore, additional configuration using Hive Warehouse Connector is required.

## <a name="deploy-new-app-for-production"></a>Distribuire una nuova app per l'ambiente di produzioneDeploy new app for production

Per passare al nuovo cluster, ad esempio è possibile installare una nuova applicazione client e usarla come nuovo ambiente di produzione oppure aggiornare l'applicazione client esistente e passare a HDInsight 4.0.

## <a name="switch-hdinsight-40-to-the-production"></a>Passare HDInsight 4.0 alla produzione

Se le differenze sono state create nel metastore durante il test, è necessario aggiornare le modifiche appena prima di passare. In questo caso, è possibile esportare & importare il metastore e quindi eseguire nuovamente l'aggiornamento.

## <a name="remove-the-old-production"></a>Rimuovere la vecchia produzione

Una volta confermato che il rilascio è completo e completamente operativo, è possibile rimuovere la versione 3.6 e il metastore precedente. Assicurarsi che venga eseguita la migrazione di tutti gli elementi prima di eliminare l'ambiente.

## <a name="query-execution-across-hdinsight-versions"></a>Esecuzione di query tra le versioni di HDInsightQuery execution across HDInsight versions

Esistono due modi per eseguire ed eseguire il debug di query Hive/LLAP all'interno di un cluster HDInsight 3.6.There are two ways to execute and debug Hive/LLAP queries within an HDInsight 3.6 cluster. HiveCLI offre un'esperienza della riga di comando e la visualizzazione Tez/Hive fornisce un flusso di lavoro basato su GUI.

In HDInsight 4.0, HiveCLI has been replaced with Beeline. HiveCLI è un client di base per Hiveserver 1 e Beeline è un client JDBC che fornisce l'accesso a Hiveserver 2. Beeline può essere utilizzato anche per connettersi a qualsiasi altro endpoint di database compatibile con JDBC. Beeline è disponibile out-of-box su HDInsight 4.0 senza alcuna installazione necessaria.

In HDInsight 3.6, the GUI client for interacting with Hive server is the Ambari Hive View. HDInsight 4.0 non viene fornito con Ambari View. Abbiamo fornito ai nostri clienti un modo per utilizzare Data Analytics Studio (DAS), che non è un servizio HDInsight di base. DAS non viene fornito con HDInsight cluster out-of-the-box e non è un pacchetto ufficialmente supportato. Tuttavia, DAS può essere installato nel cluster utilizzando [un'azione di script](../hdinsight-hadoop-customize-cluster-linux.md) come segue:

|Proprietà | valore |
|---|---|
|Tipo di script|- Personalizzato|
|Nome|Das|
|URI script Bash|`https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh`|
|Tipo/i di nodo|Head|

Attendi da 10 a 15 minuti, quindi `https://CLUSTERNAME.azurehdinsight.net/das/`avvia Data Analytics Studio utilizzando questo URL: .

Prima di accedere a DAS, potrebbe essere necessario un aggiornamento dell'interfaccia utente di Ambari e/o un riavvio di tutti i componenti Ambari.

Una volta installato DAS, se le query eseguite non sono state eseguite nel visualizzatore di query, eseguire la procedura seguente:

1. Impostare le configurazioni per Hive, Tez e DAS come descritto in [questa guida per la risoluzione dei problemi relativi all'installazione](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html)di DAS .
2. Assicurarsi che le configurazioni della directory di archiviazione di Azure `fs.azure.page.blob.dirs`seguenti siano BLOB di pagine e che siano elencati in:
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. Riavviare HDFS, Hive, Tez e DAS su entrambi i nodi.

## <a name="next-steps"></a>Passaggi successivi

* [Annuncio HDInsight 4.0](../hdinsight-version-release.md)
* [Immersione profonda HDInsight 4.0](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Tavoli Hive 3 ACID](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
