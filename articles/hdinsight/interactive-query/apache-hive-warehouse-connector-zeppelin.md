---
title: Hive Warehouse Connector - Apache Zeppelin con Livy - Azure HDInsight
description: Informazioni su come integrare Hive Warehouse Connector con Apache Zeppelin in Azure HDInsight.
author: nis-goel
ms.author: nisgoel
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: 1f9d2d9bd2a58fa4c6f14db8ffd067bb39fc1553
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83853713"
---
# <a name="integrate-apache-zeppelin-with-hive-warehouse-connector-in-azure-hdinsight"></a>Integrare Apache Zeppelin con Hive Warehouse Connector in Azure HDInsight

I cluster HDInsight Spark includono i notebook Apache Zeppelin con diversi interpreti. Questo articolo illustra solo l'interprete Livy per accedere alle tabelle di Hive da Spark usando Hive Warehouse Connector.

## <a name="prerequisite"></a>Prerequisito

Completare la procedura di [configurazione di Hive Warehouse Connector](apache-hive-warehouse-connector.md#hive-warehouse-connector-setup).

## <a name="getting-started"></a>Introduzione

1. Usare il [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) per connettersi al cluster Apache Spark. Modificare il comando seguente sostituendo CLUSTERNAME con il nome del cluster in uso e quindi immettere il comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Nella sessione ssh eseguire il comando seguente per prendere nota delle versioni per `hive-warehouse-connector-assembly` e `pyspark_hwc`:

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

    Salvare l'output per usarlo in seguito durante la configurazione di Apache Zeppelin.

## <a name="configure-livy"></a>Configurare Livy

Le configurazioni seguenti sono necessarie per accedere alle tabelle di Hive da Zeppelin con l'interprete Livy.

### <a name="interactive-query-cluster"></a>Cluster Interactive Query

1. In un Web browser passare a `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HDFS/configs`, dove LLAPCLUSTERNAME è il nome del cluster Interactive Query.

1. Passare a **Advanced (Avanzate)**  > **Custom core-site** (Impostazioni core-site personalizzate). Selezionare **Add Property...** (Aggiungi proprietà...) per aggiungere le configurazioni seguenti:

    | Configurazione                 | valore |
    | ----------------------------- |-------|
    | hadoop.proxyuser.livy.groups  | *     |
    | hadoop.proxyuser.livy.hosts   | *     |

1. Salvare le modifiche e riavviare tutti i componenti interessati.

### <a name="spark-cluster"></a>Cluster Spark

1. In un Web browser passare a `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs`, dove CLUSTERNAME è il nome del cluster Apache Spark.

1. Espandere **Custom livy2-conf** (Configurazione livy2 personalizzata). Selezionare **Add Property...** (Aggiungi proprietà...) per aggiungere le configurazioni seguenti:

    | Configurazione                 | valore                                      |
    | ----------------------------- |------------------------------------------  |
    | livy.file.local-dir-whitelist | /usr/hdp/current/hive_warehouse_connector/ |

1. Salvare le modifiche e riavviare tutti i componenti interessati.

### <a name="configure-livy-interpreter-in-zeppelin-ui-spark-cluster"></a>Configurare l'interprete Livy nell'interfaccia utente di Zeppelin (cluster Spark)

1. In un Web browser passare a `https://CLUSTERNAME.azurehdinsight.net/zeppelin/#/interpreter`, dove `CLUSTERNAME` è il nome del cluster Apache Spark.

1. Passare a **livy2**.

1. Aggiungere le configurazioni seguenti:

    | Configurazione                 | valore                                      |
    | ----------------------------- |:------------------------------------------:|
    | livy.spark.hadoop.hive.llap.daemon.service.hosts | @llap0 |
    | livy.spark.security.credentials.hiveserver2.enabled | true |
    | livy.spark.sql.hive.llap | true |
    | livy.spark.yarn.security.credentials.hiveserver2.enabled | true |
    | livy.superusers | livy,zeppelin |
    | livy.spark.jars | `file:///usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-VERSION.jar`.<br>Sostituire VERSION con il valore ottenuto nella [Guida introduttiva](#getting-started) precedente. |
    | livy.spark.submit.pyFiles | `file:///usr/hdp/current/hive_warehouse_connector/pyspark_hwc-VERSION.zip`.<br>Sostituire VERSION con il valore ottenuto nella [Guida introduttiva](#getting-started) precedente. |
    | livy.spark.sql.hive.hiveserver2.jdbc.url | Impostarlo sull'URL JDBC interattivo per HiveServer2 del cluster Interactive query. |
    | spark.security.credentials.hiveserver2.enabled | true |

1. Solo per i cluster ESP, aggiungere la configurazione seguente:

    | Configurazione| valore|
    |---|---|
    | livy.spark.sql.hive.hiveserver2.jdbc.url.principal | `hive/<headnode-FQDN>@<AAD-Domain>` |

    Sostituire `<headnode-FQDN>` con il nome di dominio completo del nodo head del cluster Interactive query.
    Sostituire `<AAD-DOMAIN>` con il nome di Azure Active Directory (AAD) a cui viene aggiunto il cluster. Usare una stringa in maiuscolo per il valore `<AAD-DOMAIN>` per trovare le credenziali. Se necessario, selezionare `/etc/krb5.conf` per i nomi dell'area di autenticazione.

1. Salvare le modifiche e riavviare l'interprete Livy.

Se l'interprete Livy non è accessibile, modificare il file `shiro.ini` presente nel componente Zeppelin in Ambari. Per altre informazioni, vedere [Configurare la sicurezza di Apache Zeppelin](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.0.1/configuring-zeppelin-security/content/enabling_access_control_for_interpreter__configuration__and_credential_settings.html).  


## <a name="running-queries-in-zeppelin"></a>Esecuzione di query in Zeppelin 

Avviare un notebook Zeppelin con l'interprete Livy ed eseguire le operazioni seguenti

```python
%livy2

import com.hortonworks.hwc.HiveWarehouseSession
import com.hortonworks.hwc.HiveWarehouseSession._
import org.apache.spark.sql.SaveMode

# Initialize the hive context
val hive = HiveWarehouseSession.session(spark).build()

# Create a database
hive.createDatabase("hwc_db",true)
hive.setDatabase("hwc_db")

# Create a Hive table
hive.createTable("testers").ifNotExists().column("id", "bigint").column("name", "string").create()

val dataDF = Seq( (1, "foo"), (2, "bar"), (8, "john")).toDF("id", "name")

# Validate writes to the table
dataDF.write.format("com.hortonworks.spark.sql.hive.llap.HiveWarehouseConnector").mode("append").option("table", "hwc_db.testers").save()

# Validate reads
hive.executeQuery("select * from testers").show()

```

## <a name="next-steps"></a>Passaggi successivi

* [Operazioni HWC e Apache Spark](./apache-hive-warehouse-connector-operations.md)
* [Integrazione di HWC con Apache Spark e Apache Hive](./apache-hive-warehouse-connector.md)
* [Usare Interactive Query in HDInsight](./apache-interactive-query-get-started.md)