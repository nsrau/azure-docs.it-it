---
title: Eseguire la migrazione di carichi di lavoro Azure HDInsight 3.6 Hive a HDInsight 4.0
description: Informazioni su come eseguire la migrazione di carichi di lavoro Apache Hive in HDInsight 3,6 in HDInsight 4,0.
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/13/2019
ms.openlocfilehash: 26dfe8d134f9f38d8272895583ba2eff614d78e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91308385"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Eseguire la migrazione di carichi di lavoro Azure HDInsight 3.6 Hive a HDInsight 4.0

Questo documento illustra come eseguire la migrazione di carichi di lavoro Apache Hive e LLAP in HDInsight 3,6 a HDInsight 4,0. HDInsight 4,0 fornisce le più recenti funzionalità hive e LLAP, ad esempio viste materializzate e Caching risultati query. Quando si esegue la migrazione dei carichi di lavoro a HDInsight 4,0, è possibile usare molte funzionalità più recenti di hive 3 che non sono disponibili in HDInsight 3,6.

In questo articolo vengono trattati gli argomenti seguenti:

* Migrazione di metadati hive a HDInsight 4,0
* Migrazione sicura di tabelle ACID e non ACID
* Conservazione dei criteri di sicurezza hive tra le versioni di HDInsight
* Esecuzione di query e debug da HDInsight 3,6 a HDInsight 4,0

Un vantaggio di hive è la possibilità di esportare metadati in un database esterno (denominato Metastore hive). Il **Metastore hive** è responsabile dell'archiviazione delle statistiche della tabella, inclusi il percorso di archiviazione delle tabelle, i nomi delle colonne e le informazioni sull'indice della tabella. HDInsight 3,6 e HDInsight 4,0 richiedono schemi di Metastore diversi e non possono condividere un singolo Metastore. Il metodo consigliato per aggiornare la metastore Hive in modo sicuro consiste nell'aggiornare una copia anziché l'originale nell'ambiente di produzione corrente. Questo documento richiede che i cluster originali e quelli nuovi abbiano accesso allo stesso account di archiviazione. Pertanto, non copre la migrazione dei dati in un'altra area.

## <a name="migrate-from-external-metastore"></a>Eseguire la migrazione da un Metastore esterno

### <a name="1-run-major-compaction-on-acid-tables-in-hdinsight-36"></a>1. eseguire la compattazione principale sulle tabelle ACID in HDInsight 3,6

Le tabelle ACID HDInsight 3,6 e HDInsight 4,0 comprendono i Delta ACID in modo diverso. L'unica azione necessaria prima della migrazione consiste nell'eseguire la compattazione "MAJOR" su ogni tabella ACID nel cluster 3,6. Per informazioni dettagliate sulla compattazione, vedere il [manuale della lingua di hive](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTable/Partition/Compact) .

### <a name="2-copy-sql-database"></a>2. copiare il database SQL
Creare una nuova copia del Metastore esterno. Se si usa un Metastore esterno, uno dei modi più semplici e sicuri per creare una copia del Metastore consiste nel [ripristinare il database](../../azure-sql/database/recovery-using-backups.md#point-in-time-restore) con un nome diverso usando la `RESTORE` funzione.  Vedere [usare gli archivi di metadati esterni in Azure HDInsight](../hdinsight-use-external-metadata-stores.md) per altre informazioni sul fissaggio di un Metastore esterno a un cluster HDInsight.

### <a name="3-upgrade-metastore-schema"></a>3. aggiornare lo schema del Metastore
Una volta completata la **copia** del Metastore, eseguire uno script di aggiornamento dello schema nell' [azione script](../hdinsight-hadoop-customize-cluster-linux.md) nel cluster HDInsight 3,6 esistente per aggiornare il nuovo Metastore allo schema hive 3. Per questo passaggio non è necessario che il nuovo Metastore sia connesso a un cluster. Ciò consente di collegare il database come Metastore HDInsight 4,0.

Usare i valori nella tabella più avanti. Sostituire `SQLSERVERNAME DATABASENAME USERNAME PASSWORD` con i valori appropriati per la **copia**Metastore hive, separati da spazi. Non includere ". database.windows.net" quando si specifica il nome del server SQL.

|Proprietà | Valore |
|---|---|
|Tipo di script|- Personalizzato|
|Nome|Aggiornamento hive|
|URI script Bash|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|Tipo/i di nodo|Head|
|Parametri|PASSWORD NOME UTENTE DATASERVERNAME DATABASENAME|

> [!Warning]  
> L'aggiornamento che converte lo schema di metadati HDInsight 3,6 nello schema HDInsight 4,0 non può essere annullato.

Per verificare l'aggiornamento, è possibile eseguire la query SQL seguente sul database:

```sql
select * from dbo.version
```

### <a name="4-deploy-a-new-hdinsight-40-cluster"></a>4. distribuire un nuovo cluster HDInsight 4,0

1. Specificare il Metastore aggiornato come metastore Hive del nuovo cluster.

1. Tuttavia, i dati effettivi delle tabelle non sono accessibili fino a quando il cluster non ha accesso agli account di archiviazione necessari.
Assicurarsi che gli account di archiviazione delle tabelle hive nel cluster HDInsight 3,6 siano specificati come account di archiviazione primario o secondario del nuovo cluster HDInsight 4,0.
Per altre informazioni sull'aggiunta di account di archiviazione ai cluster HDInsight, vedere [aggiungere altri account di archiviazione a HDInsight](../hdinsight-hadoop-add-storage.md).

### <a name="5-complete-migration-with-a-post-upgrade-tool-in-hdinsight-40"></a>5. completare la migrazione con uno strumento di post-aggiornamento in HDInsight 4,0

Per impostazione predefinita, le tabelle gestite devono essere conformi a ACID in HDInsight 4,0. Dopo aver completato la migrazione del Metastore, eseguire uno strumento di post-aggiornamento per rendere le tabelle gestite precedentemente non ACID compatibili con il cluster HDInsight 4,0. Questo strumento applicherà la seguente conversione:

|3,6 |4.0 |
|---|---|
|Tabelle esterne|Tabelle esterne|
|Tabelle gestite non ACID|Tabelle esterne con proprietà' External. Table. Purge ' =' true '|
|Tabelle gestite da ACID|Tabelle gestite da ACID|

Eseguire lo strumento di post-aggiornamento hive dal cluster HDInsight 4,0 usando la shell SSH:

1. Connettersi al cluster nodo head usando SSH. Per istruzioni, vedere [connettersi a HDInsight tramite SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. Aprire una shell di accesso come utente hive eseguendo `sudo su - hive`
1. Eseguire il comando seguente dalla Shell.

    ```bash
    STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
    /usr/hdp/$STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
    ```

Al termine dello strumento, il warehouse di hive sarà pronto per HDInsight 4,0.

## <a name="migrate-from-internal-metastore"></a>Eseguire la migrazione da un Metastore interno

Se il cluster HDInsight 3,6 usa un metastore Hive interno, seguire questa procedura per eseguire uno script che genera query hive per esportare le definizioni degli oggetti dal Metastore.

I cluster HDInsight 3,6 e 4,0 devono usare lo stesso account di archiviazione.

> [!NOTE]
>
> * Nel caso di tabelle ACID, viene creata una nuova copia dei dati sotto la tabella.
>
> * Questo script supporta solo la migrazione di database, tabelle e partizioni hive. Gli altri oggetti di metadati, ad esempio visualizzazioni, UDF e vincoli di tabella, dovranno essere copiati manualmente.
>
> * Una volta completato lo script, si presuppone che il cluster precedente non verrà più utilizzato per l'accesso a una delle tabelle o dei database a cui si fa riferimento nello script.
>
> * Tutte le tabelle gestite diventeranno transazionali in HDInsight 4,0. Facoltativamente, Mantieni la tabella non transazionale esportando i dati in una tabella esterna con la proprietà' External. Table. Purge ' =' true '. Ad esempio,
>
>    ```SQL
>    create table tablename_backup like tablename;
>    insert overwrite table tablename_backup select * from tablename;
>    create external table tablename_tmp like tablename;
>    insert overwrite table tablename_tmp select * from tablename;
>    alter table tablename_tmp set tblproperties('external.table.purge'='true');
>    drop table tablename;
>    alter table tablename_tmp rename to tablename;
>    ```

1. Connettersi al cluster HDInsight 3,6 usando un [client Secure Shell (SSH)](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Dalla sessione SSH aperta, scaricare il file di script seguente per generare un file denominato **AllTables. HQL**.

    ```bash
    wget https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/exporthive_hdi_3_6.sh
    chmod 755 exporthive_hdi_3_6.sh
    ```

    * Per un cluster HDInsight normale, senza ESP, è sufficiente eseguire `exporthive_hdi_3_6.sh` .

    * Per un cluster con ESP, kinit e modificare gli argomenti in deeline: eseguire il comando seguente, definendo utente e dominio per Azure AD utente con autorizzazioni hive complete.

        ```bash
        USER="USER"  # replace USER
        DOMAIN="DOMAIN"  # replace DOMAIN
        DOMAIN_UPPER=$(printf "%s" "$DOMAIN" | awk '{ print toupper($0) }')
        kinit "$USER@$DOMAIN_UPPER"
        ```

        ```bash
        hn0=$(grep hn0- /etc/hosts | xargs | cut -d' ' -f4)
        BEE_CMD="beeline -u 'jdbc:hive2://$hn0:10001/default;principal=hive/_HOST@$DOMAIN_UPPER;auth-kerberos;transportMode=http' -n "$USER@$DOMAIN" --showHeader=false --silent=true --outputformat=tsv2 -e"
        ./exporthive_hdi_3_6.sh "$BEE_CMD"
        ```

1. Chiudere la sessione SSH. Immettere quindi un comando SCP per scaricare **AllTables. HQL** in locale.

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.hql c:/hdi
    ```

1. Caricare **AllTables. HQL** nel *nuovo* cluster HDInsight.

    ```bash
    scp c:/hdi/alltables.hql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. Quindi usare SSH per connettersi al *nuovo* cluster HDInsight 4,0. Eseguire il codice seguente da una sessione SSH a questo cluster:

    Senza ESP:

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -f alltables.hql
    ```

    Con ESP:

    ```bash
    USER="USER"  # replace USER
    DOMAIN="DOMAIN"  # replace DOMAIN
    DOMAIN_UPPER=$(printf "%s" "$DOMAIN" | awk '{ print toupper($0) }')
    kinit "$USER@$DOMAIN_UPPER"
    ```

    ```bash
    hn0=$(grep hn0- /etc/hosts | xargs | cut -d' ' -f4)
    beeline -u "jdbc:hive2://$hn0:10001/default;principal=hive/_HOST@$DOMAIN_UPPER;auth-kerberos;transportMode=http" -n "$USER@$DOMAIN" -f alltables.hql
    ```

Lo strumento di post-aggiornamento per la migrazione di Metastore esterni non si applica qui, poiché le tabelle non ACID gestite da HDInsight 3,6 vengono convertite in tabelle gestite ACID in HDInsight 4,0.

> [!Important]  
> Le tabelle gestite in HDInsight 4,0 (incluse le tabelle migrate da 3,6) non devono essere accessibili da altri servizi o applicazioni, inclusi i cluster HDInsight 3,6.

## <a name="secure-hive-across-hdinsight-versions"></a>Sicurezza hive tra le versioni di HDInsight

Dal momento che HDInsight 3,6, HDInsight si integra con Azure Active Directory usando HDInsight Enterprise Security Package (ESP). ESP USA Kerberos e Apache Ranger per gestire le autorizzazioni di risorse specifiche all'interno del cluster. È possibile eseguire la migrazione dei criteri Ranger distribuiti in hive in HDInsight 3,6 in HDInsight 4,0 con i passaggi seguenti:

1. Passare al pannello Service Manager Ranger nel cluster HDInsight 3,6.
2. Passare al criterio denominato **hive** ed esportare il criterio in un file JSON.
3. Assicurarsi che tutti gli utenti a cui viene fatto riferimento nel file JSON dei criteri esportati esistano nel nuovo cluster. Se a un utente viene fatto riferimento nel codice JSON dei criteri ma non esiste nel nuovo cluster, aggiungere l'utente al nuovo cluster o rimuovere il riferimento dal criterio.
4. Passare al pannello **Service Manager Ranger** nel cluster HDInsight 4,0.
5. Passare al criterio denominato **hive** e importare il codice JSON dei criteri Ranger dal passaggio 2.

## <a name="check-compatibility-and-modify-codes-as-needed-in-test-app"></a>Controllare la compatibilità e modificare i codici in base alle esigenze nell'app di test

Quando si esegue la migrazione di carichi di lavoro, ad esempio programmi e query esistenti, controllare le note sulla versione e la documentazione per le modifiche e applicare le modifiche secondo necessità. Se il cluster HDInsight 3,6 usa una Spark condivisa e una metastore Hive, è necessaria una [configurazione aggiuntiva con il connettore di hive warehouse](./apache-hive-warehouse-connector.md) .

## <a name="deploy-new-app-for-production"></a>Distribuire una nuova app per la produzione

Per passare al nuovo cluster, ad esempio, è possibile installare una nuova applicazione client e usarla come nuovo ambiente di produzione oppure è possibile aggiornare l'applicazione client esistente e passare a HDInsight 4,0.

## <a name="switch-hdinsight-40-to-the-production"></a>Passare HDInsight 4,0 alla produzione

Se durante il testing sono state create differenze nel Metastore, sarà necessario aggiornare le modifiche immediatamente prima del cambio. In questo caso, è possibile esportare & importare il Metastore e quindi eseguire di nuovo l'aggiornamento.

## <a name="remove-the-old-production"></a>Rimuovere la produzione precedente

Una volta confermato che la versione è completa e completamente operativa, è possibile rimuovere la versione 3,6 e il Metastore precedente. Verificare che sia stata eseguita la migrazione di tutti gli elementi prima di eliminare l'ambiente.

## <a name="query-execution-across-hdinsight-versions"></a>Esecuzione di query tra versioni di HDInsight

Esistono due modi per eseguire ed eseguire il debug di query hive/LLAP all'interno di un cluster HDInsight 3,6. HiveCLI offre un'esperienza da riga di comando e la visualizzazione [Tez/hive](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-use-hive-ambari-view) fornisce un flusso di lavoro basato su GUI.

In HDInsight 4,0, HiveCLI è stato sostituito da. La visualizzazione Tez/hive fornisce un flusso di lavoro basato su GUI. HiveCLI è un client di parsimonia per del 1 e è un client JDBC che fornisce l'accesso a del 2. Può inoltre essere utilizzato per connettersi a qualsiasi altro endpoint del database compatibile con JDBC. È possibile usare l'installazione predefinita di HDInsight 4,0 senza alcuna installazione necessaria.

## <a name="next-steps"></a>Passaggi successivi

* [Annuncio HDInsight 4,0](../hdinsight-version-release.md)
* [Approfondimento su HDInsight 4,0](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Tabelle ACID hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
