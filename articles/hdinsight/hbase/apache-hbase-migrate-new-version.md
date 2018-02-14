---
title: Eseguire la migrazione di un cluster HBase a una nuova versione - Azure HDInsight | Microsoft Docs
description: Come eseguire la migrazione di cluster HBase a una nuova versione.
services: hdinsight
documentationcenter: 
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: 15d23d0ccf816ca355103ad7fd0d6124f1c5c226
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="migrate-an-hbase-cluster-to-a-new-version"></a>Eseguire la migrazione di cluster HBase a una nuova versione

I cluster basati su processo, ad esempio Spark e Hadoop, sono semplici da aggiornare. Vedere [Eseguire l'aggiornamento del cluster HDInsight a una versione più recente](../hdinsight-upgrade-cluster.md):

1. Eseguire il backup dei dati temporanei (archiviati in locale).
2. Eliminare il cluster esistente.
3. Creare un nuovo cluster nella stessa subnet di rete virtuale.
4. Importare i dati temporanei.
5. Avviare i processi e continuare l'elaborazione nel nuovo cluster.

Per aggiornare un cluster HBase sono necessari alcuni passaggi aggiuntivi, come descritto in questo articolo.

> [!NOTE]
> Il tempo di inattività durante l'aggiornamento dovrebbe essere minimo, nell'ordine di minuti. Questo tempo di inattività è dovuto ai passaggi per scaricare tutti i dati in memoria e quindi al tempo necessario per configurare e riavviare i servizi nel nuovo cluster. I risultati variano a seconda del numero di nodi, della quantità di dati e di altre variabili.

## <a name="review-hbase-compatibility"></a>Esaminare la compatibilità di HBase

Prima di aggiornare HBase, verificare che le versioni di HBase nel cluster di origine e in quello di destinazione siano compatibili. Per altre informazioni, vedere [Componenti e versioni di Hadoop disponibili in HDInsight](../hdinsight-component-versioning.md).

> [!NOTE]
> È consigliabile esaminare la matrice di compatibilità delle versioni nel [manuale di HBase](https://hbase.apache.org/book.html#upgrading).

Di seguito è riportata una matrice di compatibilità delle versioni di esempio, dove S indica la compatibilità e N indica una potenziale incompatibilità:

| Tipo di compatibilità | Versione principale| Versione secondaria | Patch |
| --- | --- | --- | --- |
| Compatibilità di rete client-server | N | S | S |
| Compatibilità server-server | N | S | S |
| Compatibilità tra formati di file | N | S | S |
| Compatibilità API client | N | S | S |
| Compatibilità binaria client | N | N | S |
| **Compatibilità API limitata sul lato server** |  |  |  |
| Stabile | N | S | S |
| In evoluzione | N | N | S |
| Instabile | N | N | N |
| Compatibilità tra dipendenze | N | S | S |
| Compatibilità operativa | N | N | S |

> [!NOTE]
> Eventuali incompatibilità di rilievo sono indicate nelle note sulla versione di HBase.

## <a name="upgrade-with-same-hbase-major-version"></a>Eseguire l'aggiornamento con la stessa versione principale di HBase

Lo scenario seguente si riferisce all'aggiornamento da HDInsight 3.4 a 3.6 (entrambi con Apache HBase 1.1.2) con la stessa versione principale di HBase. Altri aggiornamenti di versione sono simili, a condizione che non ci siano problemi di compatibilità tra la versione di origine e quella di destinazione.

1. Assicurarsi che l'applicazione sia compatibile con la nuova versione, come illustrato nella matrice di compatibilità e nelle note sulla versione di HBase. Testare l'applicazione in un cluster che esegue la versione di destinazione di HDInsight e HBase.

2. [Configurare un nuovo cluster di destinazione HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) usando lo stesso account di archiviazione, ma con un nome di contenitore diverso:

    ![Usare lo stesso account di archiviazione, ma creare un contenitore diverso](./media/apache-hbase-migrate-new-version/same-storage-different-container.png)

3. Scaricare il cluster HBase di origine. Si tratta del cluster da cui si esegue l'aggiornamento. HBase scrive i dati in ingresso in un archivio in memoria, detto _memstore_. Dopo che l'archivio memstore raggiunge una determinata dimensione, viene scaricato su disco per l'archiviazione a lungo termine nell'account di archiviazione del cluster. Quando si elimina il cluster precedente, gli archivi memstore vengono riciclati, con la potenziale perdita di dati. Per scaricare manualmente l'archivio memstore per ogni tabella su disco, eseguire lo script seguente. La versione più recente di questo script è in [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh) di Azure.

    ```bash
    #!/bin/bash
    
    #-------------------------------------------------------------------------------#
    # SCRIPT TO FLUSH ALL HBASE TABLES.
    #-------------------------------------------------------------------------------#
    
    LIST_OF_TABLES=/tmp/tables.txt
    HBASE_SCRIPT=/tmp/hbase_script.txt
    TARGET_HOST=$1
    
    usage ()
    {
        if [[ "$1" == "-h" ]] || [[ "$1" == "--help" ]]
        then
            cat << ...
    
    Usage: 
    
    $0 [hostname]
    
    Providing hostname is optional and not required when the script is executed within HDInsight cluster with access to 'hbase shell'.
    
    However hostname should be provided when executing the script as a script-action from HDInsight portal.
    
    For Example:
    
        1.  Executing script inside HDInsight cluster (where 'hbase shell' is 
            accessible):
    
            $0 
    
            [No need to provide hostname]
    
        2.  Executing script from HDinsight Azure portal:
    
            Provide Script URL.
    
            Provide hostname as a parameter (i.e. hn0, hn1 or wn2 etc.).
    ...
            exit
        fi
    }
    
    validate_machine ()
    {
        THIS_HOST=`hostname`
    
        if [[ ! -z "$TARGET_HOST" ]] && [[ $THIS_HOST  != $TARGET_HOST* ]]
        then
            echo "[INFO] This machine '$THIS_HOST' is not the right machine ($TARGET_HOST) to execute the script."
            exit 0
        fi
    }
    
    get_tables_list ()
    {
    hbase shell << ... > $LIST_OF_TABLES 2> /dev/null
        list
        exit
    ...
    }
    
    add_table_for_flush ()
    {
        TABLE_NAME=$1
        echo "[INFO] Adding table '$TABLE_NAME' to flush list..."
        cat << ... >> $HBASE_SCRIPT
            flush '$TABLE_NAME'
    ...
    }
    
    clean_up ()
    {
        rm -f $LIST_OF_TABLES
        rm -f $HBASE_SCRIPT
    }
    
    ########
    # MAIN #
    ########
    
    usage $1
    
    validate_machine
    
    clean_up
    
    get_tables_list
    
    START=false
    
    while read LINE 
    do 
        if [[ $LINE == TABLE ]] 
        then
            START=true
            continue
        elif [[ $LINE == *row*in*seconds ]]
        then
            break
        elif [[ $START == true ]]
        then
            add_table_for_flush $LINE
        fi
    
    done < $LIST_OF_TABLES
    
    cat $HBASE_SCRIPT
    
    hbase shell $HBASE_SCRIPT << ... 2> /dev/null
    exit
    ...
    
    ```
    
4. Arrestare l'inserimento nel cluster HBase precedente.
5. Per assicurarsi che i dati recenti nell'archivio memstore vengano scaricati, eseguire di nuovo lo script precedente.
6. Accedere ad Ambari nel cluster precedente (https://NOMECLUSTERPRECEDENTE.azurehdidnsight.net) e arrestare i servizi HBase. Quando viene chiesto di confermare di voler arrestare i servizi, selezionare la casella per attivare la modalità manutenzione per HBase. Per altre informazioni sulla connessione ad Ambari e sul suo uso, vedere [Gestire i cluster HDInsight con l'interfaccia utente Web Ambari](../hdinsight-hadoop-manage-ambari.md).

    ![In Ambari fare clic sulla scheda Services (Servizi), quindi su HBase nel menu a sinistra e quindi su Stop (Arresta) in Service Actions (Azioni servizio)](./media/apache-hbase-migrate-new-version/stop-hbase-services.png)

    ![Selezionare la casella di controllo per attivare la modalità manutenzione per HBase e quindi confermare](./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png)

7. Accedere ad Ambari nel nuovo cluster HDInsight. Modificare l'impostazione HDFS `fs.defaultFS` in modo che punti al nome del contenitore usato dal cluster originale. Questa impostazione si trova in **HDFS > Configs > Advanced > Advanced core-site** (HDFS > Configurazioni > Avanzate > Impostazioni avanzate core-site).

    ![In Ambari fare clic sulla scheda Services (Servizi), quindi su HDFS nel menu a sinistra, successivamente sulla scheda Configs (Configurazioni) e infine sulla scheda Advanced (Avanzate) nella parte inferiore](./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png)

    ![In Ambari modificare il nome del contenitore](./media/apache-hbase-migrate-new-version/change-container-name.png)

8. Salvare le modifiche.
9. Riavviare tutti i servizi richiesti, come indicato da Ambari.
10. Impostare l'applicazione in modo che punti al nuovo cluster.

    > [!NOTE]
    > Il DNS statico per l'applicazione cambia quando si esegue l'aggiornamento. Invece di impostare come hardcoded il DNS, è possibile configurare un record CNAME nelle impostazioni DNS del nome di dominio che punta al nome del cluster. Un'altra opzione consiste nell'usare un file di configurazione per l'applicazione che è possibile aggiornare senza eseguire di nuovo la distribuzione.

11. Avviare l'operazione di inserimento per verificare se tutto funziona come previsto.
12. Se il nuovo cluster è soddisfacente, eliminare il cluster originale.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su HBase e sull'aggiornamento dei cluster HDInsight, vedere gli articoli seguenti:

* [Eseguire l'aggiornamento del cluster HDInsight a una versione più recente](../hdinsight-upgrade-cluster.md)
* [Gestire i cluster HDInsight con l'interfaccia utente Web Ambari](../hdinsight-hadoop-manage-ambari.md)
* [Componenti e versioni di Hadoop](../hdinsight-component-versioning.md)
* [Ottimizzare le configurazioni con Ambari](../hdinsight-changing-configs-via-ambari.md#hbase-optimization-with-the-ambari-web-ui)
