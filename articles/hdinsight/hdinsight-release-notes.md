---
title: Note sulla versione di Azure HDInsight
description: Note sulla versione più recente di Azure HDInsight. Ottenere suggerimenti e dettagli sullo sviluppo per Hadoop, Spark, R Server, Hive e altro.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/27/2020
ms.openlocfilehash: feb186fbe216305039fcc0a23a10419c44fd0483
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91535611"
---
# <a name="azure-hdinsight-release-notes"></a>Note sulla versione di Azure HDInsight

Questo articolo include informazioni sugli aggiornamenti di versione di Azure HDInsight **più recenti**. Per informazioni sulle versioni precedenti, vedere [Archivio delle note di versione di HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Summary

Azure HDInsight è uno dei servizi più diffusi fra i clienti enterprise per analisi open source in Azure.

## <a name="release-date-09282020"></a>Data di rilascio: 09/28/2020

Questa versione è valida sia per HDInsight 3,6 che per HDInsight 4,0. La versione di HDInsight è resa disponibile per tutte le aree in diversi giorni. La data di release riportata indica la data di rilascio di release della prima area. Se non vengono visualizzate le modifiche riportate di seguito, attendere che la versione risieda nella propria area in diversi giorni.

## <a name="new-features"></a>Nuove funzionalità
### <a name="autoscale-for-interactive-query-with-hdinsight-40-is-now-generally-available"></a>La scalabilità automatica per la query interattiva con HDInsight 4,0 è ora disponibile a livello generale
La scalabilità automatica per il tipo di cluster Interactive query è ora disponibile a livello generale (GA) per HDInsight 4,0. Tutti i cluster Interactive query 4,0 creati dopo il 27 agosto 2020 avranno il supporto per la scalabilità automatica.

### <a name="hbase-cluster-supports-premium-adls-gen2"></a>Il cluster HBase supporta ADLS Gen2 Premium
HDInsight supporta ora ADLS Gen2 Premium come account di archiviazione primario per i cluster HDInsight HBase 3,6 e 4,0. Insieme alle [Scritture accelerate](./hbase/apache-hbase-accelerated-writes.md), è possibile ottenere prestazioni migliori per i cluster HBase.

### <a name="kafka-partition-distribution-on-azure-fault-domains"></a>Distribuzione della partizione Kafka nei domini di errore di Azure
Un dominio di errore è un raggruppamento logico dell'hardware sottostante in un data center di Azure. Ogni dominio di errore condivide una fonte di alimentazione e un commutatore di rete comuni. Prima che HDInsight Kafka possa archiviare tutte le repliche di partizione nello stesso dominio di errore. A partire da questa versione, HDInsight supporta ora la distribuzione automatica delle partizioni Kafka basate sui domini di errore di Azure. 

### <a name="encryption-in-transit"></a>Crittografia in transito
I clienti possono abilitare la crittografia in transito tra i nodi del cluster usando la crittografia IPSec con chiavi gestite dalla piattaforma. Questa opzione può essere abilitata al momento della creazione del cluster. Vedere altri dettagli su [come abilitare la crittografia in transito](./domain-joined/encryption-in-transit.md).

### <a name="encryption-at-host"></a>Crittografia a livello di host
Quando si Abilita la crittografia in host, i dati archiviati nell'host della macchina virtuale vengono crittografati a riposo e i flussi vengono crittografati nel servizio di archiviazione. Da questa versione, è possibile **abilitare la crittografia nell'host nel disco dati temporaneo** durante la creazione del cluster. La crittografia nell'host è supportata solo in [determinati SKU di macchine virtuali in aree limitate](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-host-based-encryption-portal). HDInsight supporta la [configurazione e gli SKU del nodo seguenti](./hdinsight-supported-node-configuration.md). Vedere altri dettagli su [come abilitare la crittografia nell'host](https://docs.microsoft.com/azure/hdinsight/disk-encryption#encryption-at-host-using-platform-managed-keys).

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Passaggio a set di scalabilità di macchine virtuali
HDInsight usa ora macchine virtuali di Azure per eseguire il provisioning del cluster. A partire da questa versione, il servizio eseguirà gradualmente la migrazione ai [set di scalabilità di macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview). L'intero processo può richiedere mesi. Dopo la migrazione delle aree e delle sottoscrizioni, i cluster HDInsight appena creati verranno eseguiti nei set di scalabilità di macchine virtuali senza azioni del cliente. Non è prevista alcuna modifica di rilievo.

## <a name="deprecation"></a>Deprecazione
Nessuna deprecazione per questa versione.

## <a name="behavior-changes"></a>Modifiche del comportamento
Nessuna modifica del comportamento per questa versione.

## <a name="upcoming-changes"></a>Modifiche imminenti
Nelle versioni future verranno apportate le modifiche seguenti.

### <a name="ability-to-select-different-zookeeper-sku-for-spark-hadoop-and-ml-services"></a>Possibilità di selezionare uno SKU Zookeeper diverso per i servizi Spark, Hadoop e ML
HDInsight attualmente non supporta la modifica dello SKU Zookeeper per i tipi di cluster Spark, Hadoop e ML Services. USA A2_v2 SKU/a2 per i nodi Zookeeper e i clienti non vengono addebitati. Nella prossima versione i clienti possono modificare lo SKU Zookeeper per i servizi Spark, Hadoop e ML in base alle esigenze. Verranno addebitati i nodi Zookeeper con SKU diversi da A2_v2/a2. Lo SKU predefinito sarà ancora A2_V2/a2 e senza costi aggiuntivi.

## <a name="bug-fixes"></a>Correzioni di bug
HDInsight continua a migliorare l'affidabilità e le prestazioni del cluster. 

## <a name="component-version-change"></a>Modifica della versione dei componenti
Questa release non prevede alcuna modifica della versione dei componenti. È possibile trovare le versioni dei componenti correnti per HDInsight 4,0 e HDInsight 3,6 in [questo documento](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions).