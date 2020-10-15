---
title: Note sulla versione di Azure HDInsight
description: Note sulla versione più recente di Azure HDInsight. Ottenere suggerimenti e dettagli sullo sviluppo per Hadoop, Spark, R Server, Hive e altro.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 616e3e6c37faa3c085b8531173b557973e09fbf8
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974567"
---
# <a name="azure-hdinsight-release-notes"></a>Note sulla versione di Azure HDInsight

Questo articolo include informazioni sugli aggiornamenti di versione di Azure HDInsight **più recenti**. Per informazioni sulle versioni precedenti, vedere [Archivio delle note di versione di HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Summary

Azure HDInsight è uno dei servizi più diffusi fra i clienti enterprise per analisi open source in Azure.

## <a name="release-date-10082020"></a>Data di rilascio: 10/08/2020

Questa versione è valida sia per HDInsight 3,6 che per HDInsight 4,0. La versione di HDInsight è resa disponibile per tutte le aree in diversi giorni. La data di release riportata indica la data di rilascio di release della prima area. Se non vengono visualizzate le modifiche riportate di seguito, attendere che la versione risieda nella propria area in diversi giorni.

## <a name="new-features"></a>Nuove funzionalità
### <a name="hdinsight-private-clusters-with-no-public-ip-and-private-link-preview"></a>HDInsight i cluster privati senza indirizzo IP pubblico e collegamento privato (anteprima)
HDInsight supporta ora la creazione di cluster senza indirizzo IP pubblico e collegamento privato ai cluster in anteprima. I clienti possono usare le nuove impostazioni di rete avanzate per creare un cluster completamente isolato senza IP pubblico e usare i propri endpoint privati per accedere al cluster. 

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Passaggio a set di scalabilità di macchine virtuali
HDInsight usa ora macchine virtuali di Azure per eseguire il provisioning del cluster. A partire da questa versione, il servizio eseguirà gradualmente la migrazione ai [set di scalabilità di macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview). L'intero processo può richiedere mesi. Dopo la migrazione delle aree e delle sottoscrizioni, i cluster HDInsight appena creati verranno eseguiti nei set di scalabilità di macchine virtuali senza azioni del cliente. Non è prevista alcuna modifica di rilievo.

## <a name="deprecation"></a>Deprecazione
#### <a name="deprecation-of-hdinsight-36-ml-services-cluster"></a>Deprecazione del cluster di servizi HDInsight 3,6 ML
Il tipo di cluster di HDInsight 3,6 ML Services sarà la fine del supporto di Dec 31 2020. I clienti non creeranno nuovi cluster di 3,6 ML Services. I cluster esistenti verranno eseguiti così come sono, senza il supporto di Microsoft. Verificare la scadenza del supporto per le versioni di HDInsight e i tipi di cluster [qui](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#available-versions).

## <a name="behavior-changes"></a>Modifiche del comportamento
Nessuna modifica del comportamento per questa versione.

## <a name="upcoming-changes"></a>Modifiche imminenti
Nelle versioni future verranno apportate le modifiche seguenti.

### <a name="ability-to-select-different-zookeeper-virtual-machine-sizes-for-spark-hadoop-and-ml-services"></a>Possibilità di selezionare diverse dimensioni delle macchine virtuali Zookeeper per i servizi Spark, Hadoop e ML
HDInsight attualmente non supporta la personalizzazione delle dimensioni del nodo Zookeeper per i tipi di cluster Spark, Hadoop e ML Services. Il valore predefinito è A2_v2 dimensioni della macchina virtuale/a2, che vengono fornite gratuitamente. Nella prossima versione è possibile selezionare le dimensioni della macchina virtuale Zookeeper più appropriate per il proprio scenario. Verranno addebitati i nodi Zookeeper con dimensioni della macchina virtuale diverse da A2_v2/a2. Le macchine virtuali A2_v2 e a2 sono ancora disponibili gratuitamente.

## <a name="bug-fixes"></a>Correzioni di bug
HDInsight continua a migliorare l'affidabilità e le prestazioni del cluster. 

## <a name="component-version-change"></a>Modifica della versione dei componenti
Questa release non prevede alcuna modifica della versione dei componenti. È possibile trovare le versioni dei componenti correnti per HDInsight 4,0 e HDInsight 3,6 in [questo documento](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions).