---
title: Note sulla versione di Azure HDInsight
description: Note sulla versione più recente di Azure HDInsight. Ottenere suggerimenti e dettagli sullo sviluppo per Hadoop, Spark, R Server, Hive e altro.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 03874f76772d8722c7161ef43a2297c2e01b7da9
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95748836"
---
# <a name="azure-hdinsight-release-notes"></a>Note sulla versione di Azure HDInsight

Questo articolo include informazioni sugli aggiornamenti di versione di Azure HDInsight **più recenti**. Per informazioni sulle versioni precedenti, vedere [Archivio delle note di versione di HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Summary

Azure HDInsight è uno dei servizi più diffusi fra i clienti enterprise per analisi open source in Azure.

Per sottoscrivere le note sulla versione, Guarda le versioni di [questo repository GitHub](https://github.com/hdinsight/release-notes/releases).

## <a name="release-date-11182020"></a>Data di rilascio: 11/18/2020

Questa versione è valida sia per HDInsight 3,6 che per HDInsight 4,0. La versione di HDInsight è resa disponibile per tutte le aree in diversi giorni. La data di release riportata indica la data di rilascio di release della prima area. Se non vengono visualizzate le modifiche riportate di seguito, attendere che la versione risieda nella propria area in diversi giorni.

## <a name="new-features"></a>Nuove funzionalità
### <a name="auto-key-rotation-for-customer-managed-key-encryption-at-rest"></a>Rotazione automatica della chiave per la crittografia della chiave gestita dal cliente inattiva
A partire da questa versione, i clienti possono usare gli URL delle chiavi di crittografia senza versione di Azure KeyValut per la crittografia della chiave gestita dal cliente. HDInsight ruoterà automaticamente le chiavi in scadenza o sostituite con le nuove versioni. Altre informazioni sono disponibili [qui](https://docs.microsoft.com/azure/hdinsight/disk-encryption).

### <a name="ability-to-select-different-zookeeper-virtual-machine-sizes-for-spark-hadoop-and-ml-services"></a>Possibilità di selezionare diverse dimensioni delle macchine virtuali Zookeeper per i servizi Spark, Hadoop e ML
HDInsight in precedenza non supporta la personalizzazione delle dimensioni del nodo Zookeeper per i tipi di cluster Spark, Hadoop e ML Services. Il valore predefinito è A2_v2 dimensioni della macchina virtuale/a2, che vengono fornite gratuitamente. Da questa versione è possibile selezionare le dimensioni della macchina virtuale Zookeeper più appropriate per il proprio scenario. Verranno addebitati i nodi Zookeeper con dimensioni della macchina virtuale diverse da A2_v2/a2. Le macchine virtuali A2_v2 e a2 sono ancora disponibili gratuitamente.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Passaggio a set di scalabilità di macchine virtuali
HDInsight usa ora macchine virtuali di Azure per eseguire il provisioning del cluster. A partire da questa versione, il servizio eseguirà gradualmente la migrazione ai [set di scalabilità di macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview). L'intero processo può richiedere mesi. Dopo la migrazione delle aree e delle sottoscrizioni, i cluster HDInsight appena creati verranno eseguiti nei set di scalabilità di macchine virtuali senza azioni del cliente. Non è prevista alcuna modifica di rilievo.

## <a name="deprecation"></a>Deprecazione
### <a name="deprecation-of-hdinsight-36-ml-services-cluster"></a>Deprecazione del cluster di servizi HDInsight 3,6 ML
Il tipo di cluster di servizi ML di HDInsight 3,6 sarà la fine del supporto entro il 31 2020 dicembre. I clienti non creeranno nuovi cluster di 3,6 ML Services dopo il 31 2020 dicembre. I cluster esistenti verranno eseguiti così come sono, senza il supporto di Microsoft. Verificare la scadenza del supporto per le versioni di HDInsight e i tipi di cluster [qui](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#available-versions).

### <a name="disabled-vm-sizes"></a>Dimensioni VM disabilitate
A partire dal 16 2020 novembre, HDInsight bloccherà i nuovi clienti creando cluster con standand_A8, standand_A9, standand_A10 e standand_A11 le dimensioni delle macchine virtuali. I clienti esistenti che hanno usato queste dimensioni di VM negli ultimi tre mesi non saranno interessati. A partire dal 9 2021 gennaio, HDInsight bloccherà tutti i clienti che creano cluster con standand_A8, standand_A9, standand_A10 e standand_A11 le dimensioni delle macchine virtuali. I cluster esistenti vengono eseguiti così come sono. Provare a migrare a HDInsight 4,0 per evitare potenziali interruzioni del sistema o del supporto.

### <a name="behavior-changes"></a>Modifiche del comportamento
Nessuna modifica del comportamento per questa versione.

## <a name="upcoming-changes"></a>Modifiche imminenti
Nelle versioni future verranno apportate le modifiche seguenti.

### <a name="default-cluster-version-will-be-changed-to-40"></a>La versione predefinita del cluster verrà modificata in 4,0
A partire dal 2021 febbraio, la versione predefinita del cluster HDInsight verrà modificata da 3,6 a 4,0. Per ulteriori informazioni sulle versioni disponibili, vedere [versioni disponibili](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#available-versions). Altre informazioni sulle novità di [HDInsight 4,0](https://docs.microsoft.com/azure/hdinsight/hdinsight-version-release)

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>HDInsight 3,6-fine del supporto del 30 2021 giugno
HDInsight 3,6 sarà la fine del supporto. A partire dal 30 2021 giugno, i clienti non possono creare nuovi cluster HDInsight 3,6. I cluster esistenti verranno eseguiti così come sono, senza il supporto di Microsoft. Provare a migrare a HDInsight 4,0 per evitare potenziali interruzioni del sistema o del supporto.

## <a name="bug-fixes"></a>Correzioni di bug
HDInsight continua a migliorare l'affidabilità e le prestazioni del cluster. 

## <a name="component-version-change"></a>Modifica della versione dei componenti
Questa release non prevede alcuna modifica della versione dei componenti. È possibile trovare le versioni dei componenti correnti per HDInsight 4,0 e HDInsight 3,6 in [questo documento](./hdinsight-component-versioning.md).
