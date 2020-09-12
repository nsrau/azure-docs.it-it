---
title: Enterprise Security Package per Azure HDInsight
description: Informazioni sui componenti e le versioni di Enterprise Security Package in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 91fa6a8da555d0b0cc79b262a83306c1f72aa68a
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89567096"
---
# <a name="enterprise-security-package-for-azure-hdinsight"></a>Enterprise Security Package per Azure HDInsight

Si tratta di un pacchetto opzionale che è possibile aggiungere al cluster HDInsight come parte del flusso di lavoro di creazione del cluster. Il Pacchetto di sicurezza aziendale supporta:

* Integrazione con Active Directory per l'autenticazione.

    In passato è stato creato un cluster HDInsight con un utente amministratore locale e un utente SSH locale. L'utente amministratore locale poteva accedere a tutti i file, cartelle, tabelle e colonne.  Con Enterprise Security Package, è possibile abilitare il controllo degli accessi in base al ruolo integrando HDInsight con il Azure Active Directory Domain Services.

    Per altre informazioni, vedere:

    * [Introduzione alla sicurezza Apache Hadoop con i cluster HDInsight aggiunti al dominio](./domain-joined/hdinsight-security-overview.md)

    * [Pianificare cluster Apache Hadoop aggiunti a un dominio di Azure in HDInsight](./domain-joined/apache-domain-joined-architecture.md)

    * [Configurare l'ambiente sandbox aggiunto al dominio](./domain-joined/apache-domain-joined-configure.md)

    * [Configurare cluster HDInsight aggiunti al dominio usando Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

* Autorizzazione per i dati

  * Integrazione con Apache Ranger per l'autorizzazione per code Yarn, Hive e Spark SQL.
  * È possibile impostare il controllo di accesso per file e cartelle.

    Per altre informazioni, vedere [configurare i criteri di Apache hive in HDInsight aggiunto al dominio](./domain-joined/apache-domain-joined-run-hive.md)

* Visualizzare i log di controllo per monitorare gli accessi e i criteri configurati.

## <a name="supported-cluster-types"></a>Tipi di cluster supportati

Attualmente solo i tipi di cluster seguenti supportano il Pacchetto di sicurezza aziendale:

* Hadoop (solo HDInsight 3.6)
* Spark
* Kafka
* hbase
* Interactive Query

## <a name="support-for-azure-data-lake-storage"></a>Supporto per Azure Data Lake Storage

Enterprise Security Package supporta l'uso di Azure Data Lake Storage come risorsa di archiviazione sia primaria che aggiuntiva.

## <a name="pricing-and-service-level-agreement-sla"></a>Prezzi e contratto di servizio (SLA)

Per informazioni su prezzi e contratto di servizio per il Pacchetto di sicurezza aziendale, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Passaggi successivi

* [Configurazione del cluster per Apache Hadoop, Spark e altre informazioni su HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Lavorare da un computer Windows in Apache Hadoop su HDInsight](hdinsight-hadoop-windows-tools.md)
* [Note sulla versione di Hortonworks associate alle versioni di Azure HDInsight](./hortonworks-release-notes.md)
* [Componenti Apache in HDInsight](./hdinsight-component-versioning.md)
