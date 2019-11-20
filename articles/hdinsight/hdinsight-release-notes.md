---
title: Note sulla versione di Azure HDInsight
description: Note sulla versione più recente di Azure HDInsight. Ottieni suggerimenti per lo sviluppo e dettagli per Hadoop, Spark, R Server, hive e altro ancora.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: 54e28a9d434500915aa8cc8e07ade3592e5aa96c
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185609"
---
# <a name="release-notes"></a>Note sulla versione

Questo articolo include informazioni sugli aggiornamenti di versione di Azure HDInsight **più recenti**. Per informazioni sulle versioni precedenti, vedere [Archivio delle note di versione di HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>summary

Azure HDInsight è uno dei servizi più diffusi fra i clienti enterprise per analisi Apache Hadoop e Apache Spark open source in Azure.

## <a name="release-date-11072019"></a>Data di rilascio: 11/07/2019

Questa versione è valida per HDInsight 3,6 e 4,0.

> [!IMPORTANT]  
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere l'[articolo sul controllo delle versioni di HDInsight](hdinsight-component-versioning.md).


## <a name="new-features"></a>Nuove funzionalità

### <a name="hdinsight-identity-broker-hib-preview"></a>Broker di identità HDInsight (HIB) (anteprima)

HDInsight Identity Broker (HIB) consente agli utenti di accedere ad Apache Ambari usando l'autenticazione a più fattori e ottenere i ticket Kerberos necessari senza che siano necessari hash delle password in Azure Active Directory Domain Services (AAD-DS). Attualmente HIB è disponibile solo per i cluster distribuiti tramite il modello ARM.

### <a name="kafka-rest-api-proxy-preview"></a>Proxy API REST Kafka (anteprima)

Il proxy dell'API REST Kafka offre una distribuzione con un clic del proxy REST a disponibilità elevata con cluster Kafka tramite l'autorizzazione AAD protetta e il protocollo OAuth. 

### <a name="auto-scale"></a>Scalabilità automatica

La scalabilità automatica per Azure HDInsight è ora disponibile a livello generale in tutte le aree per i tipi di cluster Apache Spark e Hadoop. Questa funzionalità consente di gestire i carichi di lavoro di Big Data Analytics in modo più economico e produttivo. È ora possibile ottimizzare l'uso dei cluster HDInsight e pagare solo gli elementi necessari.

In base ai requisiti specifici, puoi scegliere tra la scalabilità automatica basata su carico e basata su pianificazione. La scalabilità automatica basata sul carico consente di ridimensionare le dimensioni del cluster in base alle esigenze delle risorse correnti, mentre la scalabilità automatica basata sulla pianificazione può modificare le dimensioni del cluster in base a una pianificazione predefinita. 

Anche il supporto del ridimensionamento automatico per il carico di lavoro HBase e LLAP è un'anteprima pubblica. Per altre informazioni, vedere [ridimensionare automaticamente i cluster HDInsight di Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-autoscale-clusters).

### <a name="hdinsight-accelerated-writes-for-apache-hbase"></a>Scritture HDInsight accelerate per Apache HBase 

Le scritture accelerate usano dischi SSD gestiti Premium di Azure per migliorare le prestazioni del log WAL di Apache HBase. Per altre informazioni, vedere la pagina relativa alla [scrittura accelerata di Azure HDInsight per Apache HBase](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-accelerated-writes).

### <a name="custom-ambari-db"></a>Database Ambari personalizzato

HDInsight offre ora una nuova capacità per consentire ai clienti di usare il proprio database SQL per Ambari. Ora i clienti possono scegliere il database SQL appropriato per Ambari e aggiornarlo in modo semplice in base ai requisiti di crescita aziendale. La distribuzione viene eseguita con un modello di Azure Resource Manager. Per altre informazioni, vedere [configurare cluster HDInsight con un database Ambari personalizzato](https://docs.microsoft.com/azure/hdinsight/hdinsight-custom-ambari-db).

### <a name="f-series-virtual-machines-are-now-available-with-hdinsight"></a>Le macchine virtuali serie F sono ora disponibili con HDInsight

Le macchine virtuali (VM) della serie F sono ideali per iniziare a usare HDInsight con requisiti di elaborazione leggeri. Con un prezzo di listino orario più basso, la serie F presenta il migliore rapporto prezzo-prestazioni nel portfolio Azure basato sull'unità di elaborazione di Azure (ACU, Azure Compute Unit) per ogni vCPU. Per altre informazioni, vedere [selezione delle dimensioni di VM appropriate per il cluster HDInsight di Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-selecting-vm-size).

## <a name="deprecation"></a>Deprecazione

### <a name="g-series-virtual-machine-deprecation"></a>Deprecazione della macchina virtuale serie G
Da questa versione, le macchine virtuali della serie G non sono più disponibili in HDInsight.

### <a name="dv1-virtual-machine-deprecation"></a>Deprecazione della macchina virtuale Dv1
Da questa versione, l'uso di macchine virtuali Dv1 con HDInsight è deprecato. Tutte le richieste dei clienti per Dv1 verranno gestite automaticamente con dv2. Non esiste alcuna differenza di prezzo tra le macchine virtuali Dv1 e dv2.

## <a name="behavior-changes"></a>Modifiche del comportamento

### <a name="cluster-managed-disk-size-change"></a>Modifica delle dimensioni del disco gestito del cluster
HDInsight fornisce spazio su disco gestito con il cluster. Da questa versione, le dimensioni del disco gestito di ogni nodo nel nuovo cluster creato vengono modificate in 128 GB.

## <a name="upcoming-changes"></a>Modifiche imminenti
Nelle prossime versioni si verificheranno le modifiche seguenti. 

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Passaggio ai set di scalabilità di macchine virtuali di Azure
HDInsight USA ora macchine virtuali di Azure per eseguire il provisioning del cluster. A partire da dicembre, HDInsight utilizzerà invece i set di scalabilità di macchine virtuali di Azure. Vedere altre informazioni sui [set di scalabilità di macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).

### <a name="hbase-20-to-21"></a>Da HBase 2,0 a 2,1
Nella prossima versione di HDInsight 4,0, la versione di HBase verrà aggiornata dalla versione 2,0 alla 2,1.

### <a name="a-series-virtual-machine-deprecation-for-esp-cluster"></a>Deprecazione della macchina virtuale serie a per il cluster ESP
Le macchine virtuali della serie A possono causare problemi del cluster ESP a causa di una capacità di CPU e memoria relativamente bassa. Nella prossima versione, le VM serie A saranno deprecate per la creazione di nuovi cluster ESP.

## <a name="bug-fixes"></a>Correzioni di bug
HDInsight continua a migliorare l'affidabilità e le prestazioni del cluster. 

## <a name="component-version-change"></a>Modifica della versione del componente
Nessuna modifica della versione del componente per questa versione. È possibile trovare le versioni dei componenti correnti per HDInsight 4,0 ad HDInsight 3,6 [qui](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning).
