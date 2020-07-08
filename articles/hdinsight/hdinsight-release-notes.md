---
title: Note sulla versione di Azure HDInsight
description: Note sulla versione più recente di Azure HDInsight. Ottenere suggerimenti e dettagli sullo sviluppo per Hadoop, Spark, R Server, Hive e altro.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/11/2020
ms.openlocfilehash: a4faab9ac6d5e1c39c1120e09dae792b95892d60
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564400"
---
# <a name="release-notes"></a>Note sulla versione

Questo articolo include informazioni sugli aggiornamenti di versione di Azure HDInsight **più recenti**. Per informazioni sulle versioni precedenti, vedere [Archivio delle note di versione di HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Summary

Azure HDInsight è uno dei servizi più diffusi fra i clienti enterprise per analisi open source in Azure.

## <a name="release-date-06112020"></a>Data di rilascio: 06/11/2020

Questa versione è valida per HDInsight 3.6 e 4.0. La versione di HDInsight è resa disponibile per tutte le aree in diversi giorni. La data di release riportata indica la data di rilascio di release della prima area. Se non vengono visualizzate le modifiche riportate di seguito, attendere che la versione risieda nella propria area in diversi giorni.

## <a name="new-features"></a>Nuove funzionalità
### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Passaggio a set di scalabilità di macchine virtuali
HDInsight usa le macchine virtuali di Azure per eseguire il provisioning del cluster ora. Da questa versione, i nuovi cluster HDInsight creati per iniziare a usare il set di scalabilità di macchine virtuali di Azure. La modifica viene implementata gradualmente. È necessario prevedere nessuna modifica di rilievo. Vedere altre informazioni sui [set di scalabilità di macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).
 
### <a name="reboot-vms-in-hdinsight-cluster"></a>Riavviare le macchine virtuali nel cluster HDInsight
In questa versione è supportato il riavvio delle macchine virtuali nel cluster HDInsight per riavviare i nodi che non rispondono. Attualmente è possibile eseguire questa operazione solo tramite l'API, il supporto per PowerShell e l'interfaccia della riga di comando. Per altre informazioni sull'API, vedere [questo documento](https://github.com/Azure/azure-rest-api-specs/codeowners/master/specification/hdinsight/resource-manager/Microsoft.HDInsight/stable/2018-06-01-preview/virtualMachines.json).
 
## <a name="deprecation"></a>Deprecazione
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>Deprecazione di Spark 2.1 e 2.2 in un cluster Spark HDInsight 3.6
A partire dal 1 2020 luglio, i clienti non possono creare nuovi cluster Spark con Spark 2,1 e 2,2 in HDInsight 3,6. I cluster esistenti verranno eseguiti così come sono, senza il supporto di Microsoft. Valutare il passaggio a Spark 2.3 in HDInsight 3.6 entro il 30 giugno 2020 per evitare potenziali interruzioni del sistema o del supporto.
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>Deprecazione di Spark 2.3 nel cluster Spark HDInsight 4.0
A partire dal 1 2020 luglio, i clienti non possono creare nuovi cluster Spark con Spark 2,3 in HDInsight 4,0. I cluster esistenti verranno eseguiti così come sono, senza il supporto di Microsoft. Valutare il passaggio a Spark 2.4 in HDInsight 4.0 entro il 30 giugno 2020 per evitare potenziali interruzioni del sistema o del supporto.
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>Deprecazione di Kafka 1.1 nel cluster Kafka di HDInsight 4.0
A partire dal 1° luglio 2020, i clienti non potranno creare nuovi cluster Kafka con Kafka 1.1 in HDInsight 4.0. I cluster esistenti verranno eseguiti così come sono, senza il supporto di Microsoft. Valutare il passaggio a Kafka 2.1 in HDInsight 4.0 entro il 30 giugno 2020 per evitare potenziali interruzioni del sistema o del supporto.
 
## <a name="behavior-changes"></a>Modifiche del comportamento
### <a name="esp-spark-cluster-head-node-size-change"></a>Modifica dimensioni nodo head del cluster ESP Spark 
La dimensione minima consentita del nodo head per il cluster ESP Spark è stata modificata in Standard_D13_V2. Le VM con core Bassi e memoria come nodo head possono causare problemi del cluster ESP a causa di una capacità di CPU e memoria relativamente bassa. A partire dalla versione, usare SKU superiori a Standard_D13_V2 e Standard_E16_V3 come nodo head per i cluster ESP Spark.
 
### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>Per il nodo Head è necessaria una macchina virtuale con almeno 4 core 
Per il nodo Head è necessaria una macchina virtuale con almeno 4 core per garantire la disponibilità elevata e l'affidabilità dei cluster HDInsight. A partire dal 6 aprile 2020, i clienti possono scegliere solo macchine virtuali con 4 core o più come nodo head per i nuovi cluster HDInsight. I cluster esistenti continueranno a funzionare come previsto. 
 
### <a name="cluster-worker-node-provisioning-change"></a>Modifica del provisioning del nodo di lavoro del cluster
Quando il 80% dei nodi del ruolo di lavoro è pronto, il cluster entra in fase **operativa** . In questa fase, i clienti possono eseguire tutte le operazioni del piano dati, ad esempio l'esecuzione di script e processi. Tuttavia, i clienti non possono eseguire operazioni del piano di controllo come la scalabilità verticale o verticale. È supportata solo l'eliminazione.
 
Dopo la fase **operativa** , il cluster attende un altro 60 minuti per i rimanenti 20% di nodi del ruolo di lavoro. Alla fine di questo 60 minuti, il cluster si sposta in fase di **esecuzione** , anche se tutti i nodi del ruolo di lavoro non sono ancora disponibili. Quando un cluster entra in fase di **esecuzione** , è possibile usarlo come di consueto. Entrambe le operazioni del piano di controllo, come la scalabilità verticale e orizzontale, e le operazioni del piano dati come l'esecuzione di script e processi sono accettate Se alcuni dei nodi del ruolo di lavoro richiesti non sono disponibili, il cluster verrà contrassegnato come operazione parzialmente completata. Vengono addebitati i costi per i nodi che sono stati distribuiti correttamente. 
 
### <a name="create-new-service-principal-through-hdinsight"></a>Creare una nuova entità servizio tramite HDInsight
In precedenza, con la creazione di cluster, i clienti possono creare una nuova entità servizio per accedere all'account ADLS di generazione 1 connesso in portale di Azure. A partire dal 15 2020 giugno, i clienti non possono creare una nuova entità servizio nel flusso di lavoro di creazione HDInsight. è supportata solo l'entità servizio esistente. Vedere [creare un'entità servizio e certificati usando Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

### <a name="time-out-for-script-actions-with-cluster-creation"></a>Timeout per le azioni script con la creazione del cluster
HDInsight supporta l'esecuzione di azioni script con la creazione del cluster. Da questa versione, tutte le azioni script con la creazione del cluster devono terminare entro **60 minuti**o si sono rilevati. Le azioni script inviate ai cluster in esecuzione non sono interessate. Altre informazioni sono disponibili [qui](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux#script-action-in-the-cluster-creation-process).
 
## <a name="upcoming-changes"></a>Modifiche imminenti
Nessun imminente cambiamento di rilievo a cui è necessario prestare attenzione.
 
## <a name="bug-fixes"></a>Correzioni di bug
HDInsight continua a migliorare l'affidabilità e le prestazioni del cluster. 
 
## <a name="component-version-change"></a>Modifica della versione dei componenti
### <a name="hbase-20-to-216"></a>HBase da 2.0 a 2.1.6
La versione di HBase è stata aggiornata dalla versione 2,0 alla 2.1.6.
 
### <a name="spark-240-to-244"></a>Spark da 2.4.0 a 2.4.4
La versione di Spark è stata aggiornata dalla versione 2.4.0 a 2.4.4.
 
### <a name="kafka-210-to-211"></a>Kafka da 2.1.0 a 2.1.1
La versione Kafka viene aggiornata dalla versione 2.1.0 alla versione 2.1.1.
 
È possibile trovare le versioni dei componenti correnti per HDInsight 4,0 ad HDInsight 3,6 in [questo documento](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)

## <a name="known-issues"></a>Problemi noti

### <a name="hive-warehouse-connector-issue"></a>Problema del connettore warehouse di hive
Si è verificato un problema per il connettore del warehouse di hive in questa versione. La correzione verrà inclusa nella prossima versione. I cluster esistenti creati prima di questa versione non sono interessati. Evitare l'eliminazione e la ricreazione del cluster, se possibile. Per ulteriori informazioni, aprire il ticket di supporto.
