---
title: Note sulla versione di Azure HDInsight
description: Note sulla versione più recente di Azure HDInsight. Ottieni suggerimenti per lo sviluppo e dettagli per Hadoop, Spark, R Server, hive e altro ancora.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 2abdae95e14ecc9dab673216a2c6aef652915988
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435438"
---
# <a name="release-notes"></a>Note sulla versione

Questo articolo include informazioni sugli aggiornamenti di versione di Azure HDInsight **più recenti**. Per informazioni sulle versioni precedenti, vedere [Archivio delle note di versione di HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Riepilogo

Azure HDInsight è uno dei servizi più diffusi fra i clienti enterprise per analisi Apache Hadoop e Apache Spark open source in Azure.

## <a name="release-date-12172019"></a>Data di rilascio: 12/17/2019

Questa versione è valida per HDInsight 3,6 e 4,0.

> [!IMPORTANT]  
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere l'[articolo sul controllo delle versioni di HDInsight](hdinsight-component-versioning.md).


## <a name="new-features"></a>Nuove funzionalità

### <a name="service-tags"></a>Tag di servizio
I tag di servizio semplificano la sicurezza per le macchine virtuali di Azure e le reti virtuali di Azure, consentendo di limitare facilmente l'accesso alla rete ai servizi di Azure. È possibile usare i tag di servizio nelle regole del gruppo di sicurezza di rete (NSG) per consentire o negare il traffico a un servizio di Azure specifico a livello globale o per area di Azure. Azure fornisce la manutenzione degli indirizzi IP sottostanti ogni tag. I tag del servizio HDInsight per i gruppi di sicurezza di rete (gruppi) sono gruppi di indirizzi IP per i servizi di gestione e integrità. Questi gruppi consentono di ridurre al minimo la complessità per la creazione delle regole di sicurezza. I clienti di HDInsight possono abilitare il tag del servizio tramite il portale di Azure, PowerShell e l'API REST. Per altre informazioni, vedere [tag di servizio del gruppo di sicurezza di rete (NSG) per Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-service-tags).

### <a name="custom-ambari-db"></a>Database Ambari personalizzato
HDInsight consente ora di usare il database SQL per Apache Ambari. È possibile configurare il database Ambari personalizzato dal portale di Azure o tramite il modello di Resource Manager.  Questa funzionalità consente di scegliere il database SQL appropriato per le proprie esigenze di elaborazione e capacità. È anche possibile aggiornare facilmente per soddisfare i requisiti di crescita aziendale. Per altre informazioni, vedere [configurare cluster HDInsight con un database Ambari personalizzato](hdinsight-custom-ambari-db.md).

![Database Ambari personalizzato](./media/hdinsight-release-notes/custom-ambari-db.png)

## <a name="deprecation"></a>Deprecazione
Nessuna deprecazione per questa versione. Per prepararsi a deprecazioni future, vedere [modifiche imminenti](#upcoming-changes).

## <a name="behavior-changes"></a>Modifiche funzionali
Nessuna modifica del comportamento per questa versione. Per preparare le modifiche di comportamento imminenti, vedere [future changes](#upcoming-changes).

## <a name="upcoming-changes"></a>Modifiche imminenti
Nelle versioni future si verificheranno le modifiche seguenti. 

### <a name="transport-layer-security-tls-12-enforcement"></a>Imposizione Transport Layer Security (TLS) 1,2
Transport Layer Security (TLS) e Secure Sockets Layer (SSL) sono protocolli di crittografia che consentono di proteggere le comunicazioni su una rete di computer. Per ulteriori informazioni, vedere [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0). Sebbene i cluster Azure HDInsight accettino connessioni TLS 1,2 su endpoint HTTPS pubblici, TLS 1,1 è ancora supportato per la compatibilità con le versioni precedenti dei client.

A partire dalla versione successiva, sarà possibile acconsentire esplicitamente e configurare i nuovi cluster HDInsight per accettare solo connessioni TLS 1,2. 

Più avanti nell'anno, a partire dalla 6/30/2020, Azure HDInsight imporrà TLS 1,2 o versioni successive per tutte le connessioni HTTPS. Si consiglia di assicurarsi che tutti i client siano pronti per gestire TLS 1,2 o versioni successive.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Passaggio ai set di scalabilità di macchine virtuali di Azure
HDInsight USA ora macchine virtuali di Azure per eseguire il provisioning del cluster. A partire da Feburary 2020 (la data esatta verrà comunicata in seguito), HDInsight utilizzerà invece i set di scalabilità di macchine virtuali di Azure. Vedere altre informazioni sui [set di scalabilità di macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).

### <a name="esp-spark-cluster-node-size-change"></a>Modifica dimensioni nodo del cluster ESP Spark 
Nella prossima versione:
- Le dimensioni minime del nodo consentito per il cluster ESP Spark verranno modificate in Standard_D13_V2. 
- Le macchine virtuali serie a saranno deprecate per la creazione di nuovi cluster ESP, in quanto le macchine virtuali della serie A possono causare problemi del cluster ESP a causa della capacità di memoria e CPU relativamente bassa.

### <a name="hbase-20-to-21"></a>Da HBase 2,0 a 2,1
Nella prossima versione di HDInsight 4,0, la versione di HBase verrà aggiornata dalla versione 2,0 alla 2,1.

## <a name="bug-fixes"></a>Correzioni di bug
HDInsight continua a migliorare l'affidabilità e le prestazioni del cluster. 

## <a name="component-version-change"></a>Modifica della versione del componente
Il supporto per HDInsight 3,6 è stato esteso al 31 dicembre 2020. Per ulteriori informazioni, vedere le [versioni supportate di HDInsight](hdinsight-component-versioning.md#supported-hdinsight-versions).

Nessuna modifica della versione del componente per HDInsight 4,0.

Apache Zeppelin in HDInsight 3,6:0.7.0--> 0.7.3. 

È possibile trovare le versioni più aggiornate dei componenti di [questo documento](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions).

## <a name="new-regions"></a>Nuove aree

### <a name="uae-north"></a>Emirati Arabi Uniti settentrionali
Gli indirizzi IP di gestione di Emirati Arabi Uniti settentrionali sono: `65.52.252.96` e `65.52.252.97`.
