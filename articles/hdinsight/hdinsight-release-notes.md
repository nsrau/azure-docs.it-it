---
title: Note sulla versione di Azure HDInsight
description: Note sulla versione più recente di Azure HDInsight. Ottenere suggerimenti e dettagli sullo sviluppo per Hadoop, Spark, R Server, Hive e altro.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: b7489c49b7469feacfd5b982615419741d286998
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849706"
---
# <a name="release-notes"></a>Note sulla versione

Questo articolo include informazioni sugli aggiornamenti di versione di Azure HDInsight **più recenti**. Per informazioni sulle versioni precedenti, vedere [Archivio delle note di versione di HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Summary

Azure HDInsight è uno dei servizi più diffusi fra i clienti enterprise per analisi open source in Azure.

## <a name="release-date-01092020"></a>Data di rilascio: 09/01/2020

Questa versione è valida per HDInsight 3.6 e 4.0. La versione di HDInsight è resa disponibile per tutte le aree in diversi giorni. La data di release riportata indica la data di rilascio di release della prima area. Se non vengono visualizzate le modifiche riportate di seguito, attendere che la versione venga rilasciata nella propria area dopo alcuni giorni.

> [!IMPORTANT]  
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere l'[articolo sul controllo delle versioni di HDInsight](hdinsight-component-versioning.md).

## <a name="new-features"></a>Nuove funzionalità
### <a name="tls-12-enforcement"></a>Imposizione di TLS 1.2
Transport Layer Security (TLS) e Secure Sockets Layer (SSL) sono protocolli di crittografia che consentono di proteggere le comunicazioni su una rete di computer. Altre informazioni su [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0). HDInsight usa TLS 1.2 sugli endpoint HTTPs pubblici, ma TLS 1.1 è ancora supportato per la compatibilità con le versioni precedenti. 

Con questa versione, i clienti possono scegliere TLS 1.2 solo per tutte le connessioni tramite l'endpoint del cluster pubblico. Per supportare questa operazione, è stata introdotta la nuova proprietà **minSupportedTlsVersion**, che può essere specificata durante la creazione del cluster. Se la proprietà non è impostata, il cluster supporterà comunque TLS 1.0, 1.1 e 1.2, come avviene attualmente. I clienti possono impostare il valore di questa proprietà su "1.2", il che significa che il cluster supporterà solo TLS 1.2 e versioni successive. Per altre informazioni, vedere [Transport Layer Security](./transport-layer-security.md).

### <a name="bring-your-own-key-for-disk-encryption"></a>Bring your own key per la crittografia su disco
Tutti i dischi gestiti in HDInsight sono protetti con Crittografia del servizio di archiviazione di Azure. Per impostazione predefinita, i dati su tali dischi vengono crittografati usando chiavi gestite da Microsoft. A partire da questa versione, è possibile usare Bring Your Own Key (BYOK) per la crittografia del disco e gestirla con Azure Key Vault. La crittografia BYOK è un processo di configurazione costituito da un singolo passaggio durante la creazione del cluster senza alcun costo aggiuntivo. È sufficiente registrare HDInsight come identità gestita con Azure Key Vault e aggiungere la chiave di crittografia quando si crea il cluster. Per altre informazioni, vedere [Crittografia dischi con chiavi gestite dal cliente](https://docs.microsoft.com/azure/hdinsight/disk-encryption).

## <a name="deprecation"></a>Deprecazione
Nessuna deprecazione per questa versione. Per prepararsi a deprecazioni future, vedere [Prossime modifiche](#upcoming-changes).

## <a name="behavior-changes"></a>Modifiche del comportamento
Nessuna modifica del comportamento per questa versione. Per prepararsi a modifiche future, vedere [Prossime modifiche](#upcoming-changes).

## <a name="upcoming-changes"></a>Modifiche imminenti
Nelle versioni future verranno apportate le modifiche seguenti. 

### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>Deprecazione di Spark 2.1 e 2.2 in un cluster Spark HDInsight 3.6
A partire dal 1° luglio 2020, i clienti non potranno creare nuovi cluster Spark con Spark 2.1 e 2.2 su HDInsight 3.6. I cluster esistenti verranno eseguiti così come sono, senza il supporto di Microsoft. Valutare il passaggio a Spark 2.3 in HDInsight 3.6 entro il 30 giugno 2020 per evitare potenziali interruzioni del sistema o del supporto.

### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>Deprecazione di Spark 2.3 nel cluster Spark HDInsight 4.0
A partire dal 1° luglio 2020, i clienti non potranno creare nuovi cluster Spark con Spark 2.3 in HDInsight 4.0. I cluster esistenti verranno eseguiti così come sono, senza il supporto di Microsoft. Valutare il passaggio a Spark 2.4 in HDInsight 4.0 entro il 30 giugno 2020 per evitare potenziali interruzioni del sistema o del supporto.

### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>Deprecazione di Kafka 1.1 nel cluster Kafka di HDInsight 4.0
A partire dal 1° luglio 2020, i clienti non potranno creare nuovi cluster Kafka con Kafka 1.1 in HDInsight 4.0. I cluster esistenti verranno eseguiti così come sono, senza il supporto di Microsoft. Valutare il passaggio a Kafka 2.1 in HDInsight 4.0 entro il 30 giugno 2020 per evitare potenziali interruzioni del sistema o del supporto.

### <a name="hbase-20-to-216"></a>HBase da 2.0 a 2.1.6
Nella prossima release di HDInsight 4.0, la versione di HBase verrà aggiornata dalla 2.0 alla 2.1.6

### <a name="spark-240-to-244"></a>Spark da 2.4.0 a 2.4.4
Nella prossima release di HDInsight 4.0, la versione di Spark verrà aggiornata dalla 2.4.0 alla 2.4.4

### <a name="kafka-210-to-211"></a>Kafka da 2.1.0 a 2.1.1
Nella prossima release di HDInsight 4.0, la versione di Kafka verrà aggiornata dalla 2.1.0 alla 2.1.1

### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>Per il nodo Head è necessaria una macchina virtuale con almeno 4 core 
Per il nodo Head è necessaria una macchina virtuale con almeno 4 core per garantire la disponibilità elevata e l'affidabilità dei cluster HDInsight. A partire dal 6 aprile 2020, i clienti possono scegliere solo macchine virtuali con 4 core o più come nodo head per i nuovi cluster HDInsight. I cluster esistenti continueranno a funzionare come previsto. 

### <a name="esp-spark-cluster-node-size-change"></a>Modifica delle dimensioni del nodo del cluster ESP Spark 
Nella prossima release, le dimensioni minime del nodo consentite per il cluster ESP Spark verranno modificate in Standard_D13_V2. Le macchine virtuali della serie A possono causare problemi con il cluster ESP a causa di una capacità di CPU e memoria relativamente bassa. Le macchine virtuali della serie A saranno deprecate per la creazione di nuovi cluster ESP.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Passaggio a set di scalabilità di macchine virtuali
HDInsight usa ora macchine virtuali di Azure per eseguire il provisioning del cluster. Nella prossima release, HDInsight userà invece set di scalabilità di macchine virtuali di Azure. Altre informazioni sui set di scalabilità di macchine virtuali di Azure.

## <a name="bug-fixes"></a>Correzioni di bug
HDInsight continua a migliorare l'affidabilità e le prestazioni del cluster. 

## <a name="component-version-change"></a>Modifica della versione dei componenti
Questa release non prevede alcuna modifica della versione dei componenti. Le versioni dei componenti correnti per HDInsight 4.0 e HDInsight 3.6 sono consultabili qui.

