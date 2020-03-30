---
title: Componenti a disponibilità elevata in Azure HDInsightHigh availability components in Azure HDInsight
description: Panoramica dei vari componenti a disponibilità elevata usati dai cluster HDInsight.Overview of the various high availability components used by HDInsight clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 38fb45fd339b5e2c7cab6f66a1ed6c0df73fb29e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74069635"
---
# <a name="high-availability-services-supported-by-azure-hdinsight"></a>Servizi a disponibilità elevata supportati da Azure HDInsightHigh availability services supported by Azure HDInsight

 Per fornire livelli ottimali di disponibilità per i componenti di analisi, HDInsight è stato sviluppato con un'architettura unica per garantire la disponibilità elevata dei servizi critici. Alcuni componenti di questa architettura sono stati sviluppati da Microsoft per fornire il failover automatico. Altri componenti sono componenti Apache standard distribuiti per supportare servizi specifici. Questo articolo illustra l'architettura del modello di servizio a disponibilità elevata in HDInsight, come HDInsight supporta il failover per i servizi a disponibilità elevata e le procedure consigliate per il ripristino da altre interruzioni del servizio.

## <a name="high-availability-infrastructure"></a>Infrastruttura ad alta disponibilità

HDInsight fornisce un'infrastruttura personalizzata per garantire che quattro servizi primari siano a disponibilità elevata con funzionalità di failover automatico:HDInsight provides customized infrastructure to ensure that four primary services are high availability with automatic failover capabilities:

- Server Apache Ambari
- Server della sequenza temporale dell'applicazione per Apache YARN
- Server di cronologia dei processi per Hadoop MapReduce
- Apache Livy

Questa infrastruttura è costituita da una serie di servizi e componenti software, alcuni dei quali sono progettati da Microsoft. I componenti seguenti sono univoci per la piattaforma HDInsight:The following components are unique to the HDInsight platform:

- Controller di failover slave
- Controller di failover master
- Servizio di disponibilità elevata Slave
- Servizio di disponibilità elevata master

![infrastruttura ad alta disponibilità](./media/hdinsight-high-availability-components/high-availability-architecture.png)

Esistono anche altri servizi a disponibilità elevata, supportati da componenti di affidabilità Apache open source. Questi componenti sono presenti anche nei cluster HDInsight:These components are also present on HDInsight clusters:

- Hadoop File System (HDFS) NameNode
- YARN ResourceManager
- HBase Master

Le sezioni seguenti forniscono maggiori dettagli sul funzionamento di questi servizi.

## <a name="hdinsight-high-availability-services"></a>Servizi a disponibilità elevata HDInsightHDInsight high availability services

Microsoft fornisce il supporto per i quattro servizi Apache nella tabella seguente nei cluster HDInsight.Microsoft provides support for the four Apache services in the following table in HDInsight clusters. Per distinguerli dai servizi a disponibilità elevata supportati dai componenti da Apache, sono denominati *servizi AHA HDInsight*.

| Service | Nodi del cluster | Tipi di cluster | Scopo |
|---|---|---|---|
| Server Apache Ambari| Nodo head attivo | Tutti | Monitora e gestisce il cluster.|
| Server della sequenza temporale dell'applicazione per Apache YARN | Nodo head attivo | Tutti tranne Kafka | Gestisce le informazioni di debug sui processi YARN in esecuzione nel cluster.|
| Server di cronologia dei processi per Hadoop MapReduce | Nodo head attivo | Tutti tranne Kafka | Mantiene i dati di debug per i processi MapReduce.|
| Apache Livy | Nodo head attivo | Spark | Consente una facile interazione con un cluster Spark su un'interfaccia REST |

>[!Note]
> I cluster HDInsight Enterprise Security Package (ESP) attualmente forniscono solo la disponibilità elevata del server Ambari.

### <a name="architecture"></a>Architecture

Ogni cluster HDInsight dispone di due headnodes in modalità attiva e standby, rispettivamente. I servizi HInsight HA vengono eseguiti solo nei nodi headnode. Questi servizi devono essere sempre in esecuzione sul nodo head attivo e arrestati e messi in modalità di manutenzione sul nodo head di standby.

Per mantenere gli stati corretti dei servizi a disponibilità ha e fornire un failover rapido, HDInsight utilizza Apache , un servizio di coordinamento per le applicazioni distribuite, per condurre elezioni headnode attive. HDInsight esegue anche il provisioning di alcuni processi Java in background, che coordinano la procedura di failover per i servizi A HA HDInsight.HDInsight also provisions a few background Java processes, which coordinate the failover procedure for HDInsight HA services. Questi servizi sono i seguenti: il controller di failover del master, il controller di failover slave, il *master-ha-service*e il *servizio slave.*

### <a name="apache-zookeeper"></a>Apache Portiere dello zoo

Apache zooKeeper è un servizio di coordinamento ad alte prestazioni per applicazioni distribuite. Nell'ambiente di produzione, in genere, il controllo di controllo viene eseguito in modalità replica, in cui un gruppo replicato di server zookeeper forma un quorum. Ogni cluster HDInsight dispone di tre nodi zooKeeper che consentono a tre server zoooKeeper di formare un quorum. HDInsight dispone di due quorum di </a0> in esecuzione in parallelo. Un quorum decide il nodo head attivo in un cluster in cui devono essere eseguiti i servizi A HA HDInsight.One quorum decides the active headnode in a cluster on which HDInsight HA services should run. Un altro quorum viene utilizzato per coordinare i servizi HA forniti da Apache, come descritto in dettaglio nelle sezioni successive.

### <a name="slave-failover-controller"></a>Controller di failover slave

Il controller di failover slave viene eseguito su ogni nodo in un cluster HDInsight.The slave failover controller runs on every node in an HDInsight cluster. Questo controller è responsabile dell'avvio dell'agente Ambari e del *servizio slave* in ogni nodo. Interroga periodicamente il primo quorum di  in base al nodo head attivo. Quando i nodi headnode attivi e standby cambiano, il controller di failover slave esegue le operazioni seguenti:

1. Aggiorna il file di configurazione host.
1. Riavvia l'agente Ambari.

Il *servizio slave* è responsabile dell'arresto dei servizi A HA HDInsight (ad eccezione del server Ambari) nel nodo headnode di standby.

### <a name="master-failover-controller"></a>Controller di failover master

Un controller di failover master viene eseguito su entrambi gli headnodes. Entrambi i controller di failover master comunicano con il primo quorum di  per nominare il nodo head su cui sono in esecuzione come nodo head attivo.

Ad esempio, se il controller di failover master sul nodo head 0 vince le elezioni, si verificano le seguenti modifiche:

1. Headnode 0 diventa attivo.
1. Il controller di failover master avvia il server Ambari e il *servizio master* su headnode 0.
1. L'altro controller di failover master arresta il server Ambari e il *servizio master su* headnode 1.

Il master-ha-service viene eseguito solo sul nodo head attivo, arresta i servizi HA HDInsight (ad eccezione del server Ambari) sul nodo headdisattivo e li avvia sul nodo head attivo.

### <a name="the-failover-process"></a>Il processo di failover

![Processo di failover](./media/hdinsight-high-availability-components/failover-steps.png)

Un monitoraggio dello stato viene eseguito su ogni nodo head insieme al controller di failover master per inviare notifiche di hearbeat al quorum di . Il nodo head è considerato come un servizio HA in questo scenario. Il monitoraggio dello stato controlla se ogni servizio ad alta disponibilità è integro e se è pronto a partecipare alle elezioni per la leadership. Se sì, questo nodo di testa si sfiderà alle elezioni. In caso contrario, lascerà le elezioni fino a quando non si preparerà di nuovo.

Se il nodo headnode standby raggiunge la leadership e diventa attivo (ad esempio nel caso di un errore con il nodo attivo precedente), il controller di failover master avvierà tutti i servizi DI SICUREZZA HDInsight su di esso. Il controller di failover master arresterà anche questi servizi nell'altro nodo head.

Per gli errori del servizio A HDInsight, ad esempio un servizio inattivo o non integro, il controller di failover master deve riavviare o arrestare automaticamente i servizi in base allo stato headnode. Gli utenti non devono avviare manualmente i servizi HInsight HA su entrambi i nodi head. Consentire invece il failover automatico o manuale per facilitare il ripristino del servizio.

### <a name="inadvertent-manual-intervention"></a>Intervento manuale involontario

I servizi A HA HDInsight devono essere eseguiti solo sul nodo head attivo e verranno riavviati automaticamente quando necessario. Poiché i singoli servizi di disponibilità del dati ha non dispongono di un proprio monitoraggio dello stato, il failover non può essere attivato a livello del singolo servizio. Il failover viene garantito a livello di nodo e non a livello di servizio.

### <a name="some-known-issues"></a>Alcuni problemi noti

- Quando si avvia manualmente un servizio a disponibilità ha sul nodo headnode di standby, non si fermerà fino al successivo failover. Quando i servizi HA sono in esecuzione su entrambi i nodi headnodes, alcuni potenziali problemi includono: Ambari UI è inaccessibile, Ambari genera errori, YARN, Spark, e Oozie processi potrebbero bloccarsi.

- Quando un servizio di disponibilità avvalemento problemi nel nodo head attivo si arresta, non verrà riavviato fino al failover successivo o al riavvio del controller di failover master/master-ha-service. Quando uno o più servizi HA si arrestano sul nodo head attivo, soprattutto quando il server Ambari si arresta, Ambari UI è inaccessibile, altri potenziali problemi includono gli errori dei processi YARN, Spark e Oozie.

## <a name="apache-high-availability-services"></a>Servizi Apache ad alta disponibilità

Apache offre disponibilità elevata per HDFS NameNode, YARN ResourceManager e HBase Master, disponibili anche nei cluster HDInsight. A differenza dei servizi A rete SOHdInsight, sono supportati nei cluster ESP. I servizi Apache HA comunicano con il secondo quorum di  per eleggere gli stati attivi/standby ed eseguire il failover automatico. Nelle sezioni seguenti viene descritto in dettaglio il funzionamento di questi servizi.

### <a name="hadoop-distributed-file-system-hdfs-namenode"></a>Hadoop File System distribuito (HDFS) NameNode

I cluster HDInsight basati su Apache Hadoop 2.0 o versione successiva forniscono un'elevata disponibilità NameNode.HDInsight cluster based on Apache Hadoop 2.0 or higher provide NameNode high availability. Esistono due NameNode in esecuzione sui nodi head, configurati per il failover automatico. I NameNodes utilizzano *il* controller di configurazione per comunicare con il server di controllo dello zoo per scegliere lo stato attivo/standby. Il *ZKFailoverController* controller di failover master viene eseguito su entrambi gli headnodes e funziona allo stesso modo del controller di failover master precedente.

Il secondo quorum di  è indipendente dal primo quorum, pertanto il NameNode attivo potrebbe non essere eseguito sul nodo head attivo. Quando il NameNode attivo è inattivo o non integro, il NameNode di standby vince le elezioni e diventa attivo.

### <a name="yarn-resourcemanager"></a>YARN ResourceManager

I cluster HDInsight basati su Apache Hadoop 2.4 o versione successiva supportano la disponibilità elevata di YARN ResourceManager.HDInsight clusters based on Apache Hadoop 2.4 or higher, support YARN ResourceManager high availability. Ci sono due ResourceManager, rm1 e rm2, in esecuzione su headnode 0 e headnode 1, rispettivamente. Come NameNode, anche YARN ResourceManager è configurato per il failover automatico. Un altro ResourceManager viene eletto automaticamente per essere attivo quando il ResourceManager attivo corrente si arresta o non risponde.

YARN ResourceManager utilizza il suo *ActiveStandbyElector* incorporato come rilevatore di errori e leader elettore. A differenza di HDFS NameNode, YARN ResourceManager non ha bisogno di un daemon separato. Il ResourceManager attivo scrive i propri stati in Apache zookeeper.

La disponibilità elevata di YARN ResourceManager è indipendente da NameNode e da altri servizi HA HDInsight. Il ResourceManager attivo non può essere eseguito sul nodo head attivo o sul nodo head in cui è in esecuzione il NameNode attivo. Per ulteriori informazioni sulla disponibilità elevata di YARN ResourceManager, vedere [Disponibilità elevata di ResourceManager](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/ResourceManagerHA.html).

### <a name="hbase-master"></a>HBase Master

I cluster HBase HDInsight supportano la disponibilità elevata del master HBase.HDInsight HBase clusters support HBase Master high availability. A differenza di altri servizi HA, che vengono eseguiti su headnodes, HBase Masters viene eseguito sui tre nodi zoookeeper, dove uno di essi è il master attivo e gli altri due sono in standby. Come NameNode, HBase Master si coordina con Apache zookeeper per l'elezione del leader ed esegue il failover automatico quando il master attivo corrente ha problemi. È presente un solo HBase Master attivo alla volta.

## <a name="next-steps"></a>Passaggi successivi

- [Disponibilità e affidabilità dei cluster Apache Hadoop in HDInsight](hdinsight-high-availability-linux.md)
- [Architettura della rete virtuale di Azure HDInsightAzure HDInsight virtual network architecture](hdinsight-virtual-network-architecture.md)
