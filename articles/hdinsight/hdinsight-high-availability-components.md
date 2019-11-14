---
title: Componenti a disponibilità elevata in Azure HDInsight
description: Panoramica dei vari componenti di disponibilità elevata usati dai cluster HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/11/2019
ms.openlocfilehash: 38fb45fd339b5e2c7cab6f66a1ed6c0df73fb29e
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74069635"
---
# <a name="high-availability-services-supported-by-azure-hdinsight"></a>Servizi a disponibilità elevata supportati da Azure HDInsight

 Per offrire livelli ottimali di disponibilità per i componenti di analisi, HDInsight è stato sviluppato con un'architettura univoca per garantire la disponibilità elevata dei servizi critici. Alcuni componenti di questa architettura sono stati sviluppati da Microsoft per fornire il failover automatico. Altri componenti sono componenti Apache standard distribuiti per supportare servizi specifici. Questo articolo illustra l'architettura del modello di servizio a disponibilità elevata in HDInsight, il modo in cui HDInsight supporta il failover per i servizi a disponibilità elevata e le procedure consigliate per il ripristino da altre interruzioni del servizio.

## <a name="high-availability-infrastructure"></a>Infrastruttura a disponibilità elevata

HDInsight fornisce un'infrastruttura personalizzata per garantire che quattro servizi primari siano a disponibilità elevata con funzionalità di failover automatico:

- Server Apache Ambari
- Server Sequenza temporale applicazione per Apache YARN
- Server di cronologia processo per Hadoop MapReduce
- Apache Livy

Questa infrastruttura è costituita da diversi componenti software e servizi, alcuni dei quali sono progettati da Microsoft. I componenti seguenti sono univoci per la piattaforma HDInsight:

- Controller di failover slave
- Controller di failover Master
- Servizio slave a disponibilità elevata
- Servizio master a disponibilità elevata

![Infrastruttura a disponibilità elevata](./media/hdinsight-high-availability-components/high-availability-architecture.png)

Sono disponibili anche altri servizi a disponibilità elevata, supportati dai componenti di affidabilità open source di Apache. Questi componenti sono presenti anche nei cluster HDInsight:

- Hadoop file System (HDFS) NameNode
- ResourceManager YARN
- HBase Master

Nelle sezioni seguenti vengono fornite informazioni più dettagliate sul funzionamento combinato di questi servizi.

## <a name="hdinsight-high-availability-services"></a>Servizi a disponibilità elevata HDInsight

Microsoft fornisce supporto per i quattro servizi Apache nella tabella seguente nei cluster HDInsight. Per distinguerli dai servizi a disponibilità elevata supportati dai componenti di Apache, sono denominati servizi a disponibilità elevata *HDInsight*.

| Servizio | Nodi del cluster | Tipi di cluster | Scopo |
|---|---|---|---|
| Server Apache Ambari| Nodo head attivo | Tutti | Monitora e gestisce il cluster.|
| Server Sequenza temporale applicazione per Apache YARN | Nodo head attivo | Tutti tranne Kafka | Mantiene le informazioni di debug sui processi YARN in esecuzione nel cluster.|
| Server di cronologia processo per Hadoop MapReduce | Nodo head attivo | Tutti tranne Kafka | Mantiene i dati di debug per i processi MapReduce.|
| Apache Livy | Nodo head attivo | Spark | Consente una facile interazione con un cluster Spark su un'interfaccia REST |

>[!Note]
> I cluster HDInsight Enterprise Security Package (ESP) attualmente forniscono solo la disponibilità elevata del server Ambari.

### <a name="architecture"></a>Architettura

Ogni cluster HDInsight ha due nodi head in modalità attivo e standby, rispettivamente. I servizi a disponibilità elevata HDInsight vengono eseguiti solo in nodi head. Questi servizi devono essere sempre in esecuzione sul nodo head attivo e arrestati e messi in modalità di manutenzione sulla nodo head di standby.

Per mantenere gli stati corretti dei servizi a disponibilità elevata e garantire un failover rapido, HDInsight utilizza Apache ZooKeeper, un servizio di coordinamento per le applicazioni distribuite, per condurre l'elezione nodo head attiva. HDInsight fornisce anche alcuni processi Java in background, che coordinano la procedura di failover per i servizi a disponibilità elevata HDInsight. Questi servizi sono i seguenti: il controller di failover Master, il controller di failover slave, il *Master-ha-Service*e il *servizio slave-ha*.

### <a name="apache-zookeeper"></a>Apache ZooKeeper

Apache ZooKeeper è un servizio di coordinamento a prestazioni elevate per le applicazioni distribuite. In produzione, ZooKeeper viene in genere eseguito in modalità replicata in cui un gruppo replicato di server ZooKeeper forma un quorum. Ogni cluster HDInsight dispone di tre nodi ZooKeeper che consentono a tre server ZooKeeper di formare un quorum. HDInsight ha due quorum ZooKeeper eseguiti in parallelo tra loro. Un quorum decide il nodo head attivo in un cluster in cui devono essere eseguiti i servizi a disponibilità elevata di HDInsight. Un altro quorum viene usato per coordinare i servizi a disponibilità elevata forniti da Apache, come descritto in dettaglio nelle sezioni successive.

### <a name="slave-failover-controller"></a>Controller di failover slave

Il controller di failover slave viene eseguito in ogni nodo in un cluster HDInsight. Questo controller è responsabile dell'avvio dell'agente Ambari e del *servizio slave-ha* su ogni nodo. Viene periodicamente eseguita una query sul primo quorum ZooKeeper relativo al nodo head attivo. Quando il nodi head attivo e quello di standby cambiano, il controller di failover slave esegue le operazioni seguenti:

1. Aggiorna il file di configurazione dell'host.
1. Riavvia l'agente Ambari.

Il *servizio slave-ha* è responsabile dell'arresto dei servizi a disponibilità elevata HDInsight (ad eccezione del server Ambari) nel nodo head standby.

### <a name="master-failover-controller"></a>Controller di failover Master

Un controller di failover master viene eseguito in entrambi i nodi head. Entrambi i controller di failover Master comunicano con il primo quorum ZooKeeper per nominare il nodo head in cui sono in esecuzione come nodo head attivo.

Se, ad esempio, il controller di failover Master su nodo Head 0 vince l'elezione, si verificano le modifiche seguenti:

1. Nodo Head 0 diventa attivo.
1. Il controller di failover Master avvia Ambari server e *Master-ha-Service* su nodo Head 0.
1. L'altro controller di failover Master interrompe il server Ambari e il *servizio master-disponibilità* su nodo Head 1.

Master-ha-Service viene eseguito solo sul nodo head attivo, arresta i servizi a disponibilità elevata HDInsight (ad eccezione del server Ambari) in standby nodo Head e li avvia nella nodo head attiva.

### <a name="the-failover-process"></a>Processo di failover

![processo di failover](./media/hdinsight-high-availability-components/failover-steps.png)

Un Health Monitor viene eseguito in ogni nodo head insieme al controller di failover master per inviare notifiche heartbeat al quorum Zookeeper. In questo scenario, nodo head viene considerato come servizio a disponibilità elevata. Il monitoraggio dell'integrità verifica se ogni servizio a disponibilità elevata è integro e se è pronto per partecipare all'elezione della leadership. In caso affermativo, il nodo head parteciperà alle elezioni. In caso contrario, l'elezione verrà chiusa fino a quando non diventa nuovamente pronta.

Se il nodo head di standby raggiunge sempre la leadership e diventa attivo (ad esempio in caso di errore con il nodo attivo precedente), il controller di failover Master avvierà tutti i servizi a disponibilità elevata di HDInsight. Il controller di failover Master arresterà anche questi servizi negli altri nodo head.

Per gli errori dei servizi a disponibilità elevata di HDInsight, ad esempio un servizio inattivo o non integro, il controller di failover Master deve riavviare o arrestare automaticamente i servizi in base allo stato nodo head. Gli utenti non devono avviare manualmente i servizi a disponibilità elevata HDInsight in entrambi i nodi head. Consentire invece il failover automatico o manuale per consentire il ripristino del servizio.

### <a name="inadvertent-manual-intervention"></a>Intervento manuale involontario

I servizi a disponibilità elevata HDInsight devono essere eseguiti solo nel nodo head attivo e verranno riavviati automaticamente quando necessario. Poiché i singoli servizi a disponibilità elevata non dispongono di un proprio Health Monitor, il failover non può essere attivato a livello del singolo servizio. Il failover viene assicurato a livello di nodo e non a livello di servizio.

### <a name="some-known-issues"></a>Alcuni problemi noti

- Quando si avvia manualmente un servizio a disponibilità elevata nel nodo head di standby, questo non verrà interrotto fino a quando non si verifica il failover successivo. Quando i servizi a disponibilità elevata sono in esecuzione in entrambi nodi head, alcuni problemi potenziali includono: l'interfaccia utente di Ambari è inaccessibile, Ambari genera errori, YARN, Spark e i processi oozie potrebbero rimanere bloccati.

- Quando un servizio a disponibilità elevata nel nodo head attivo si interrompe, non viene riavviato fino a quando non si verifica il failover successivo oppure il controller di failover Master/Master-ha-servizio non viene riavviato. Quando uno o più servizi a disponibilità elevata si arrestano sul nodo head attivo, specialmente quando il server Ambari si interrompe, l'interfaccia utente di Ambari è inaccessibile, altri problemi potenziali includono errori di processi YARN, Spark e oozie.

## <a name="apache-high-availability-services"></a>Servizi di disponibilità elevata Apache

Apache offre disponibilità elevata per HDFS NameNode, YARN ResourceManager e HBase Master, disponibili anche in cluster HDInsight. A differenza dei servizi a disponibilità elevata di HDInsight, sono supportati nei cluster ESP. I servizi a disponibilità elevata Apache comunicano con il secondo quorum ZooKeeper (descritto nella sezione precedente) per eleggere gli Stati attivi/standby ed eseguire il failover automatico. Le sezioni seguenti illustrano in dettaglio il funzionamento di questi servizi.

### <a name="hadoop-distributed-file-system-hdfs-namenode"></a>Hadoop Distributed File System (HDFS) NameNode

I cluster HDInsight basati su Apache Hadoop 2,0 o versione successiva garantiscono una disponibilità elevata di NameNode. Ci sono due NameNodes in esecuzione in nodi head, che sono configurati per il failover automatico. Il NameNodes utilizza *ZKFailoverController* per comunicare con Zookeeper per eleggere lo stato attivo/standby. Il *ZKFailoverController* viene eseguito in entrambi i nodi head e funziona allo stesso modo del controller di failover master precedente.

Il secondo quorum Zookeeper è indipendente dal primo quorum, quindi il NameNode attivo potrebbe non essere eseguito sul nodo head attivo. Quando il NameNode attivo è guasto o non integro, il NameNode di standby vince l'elezione e diventa attiva.

### <a name="yarn-resourcemanager"></a>ResourceManager YARN

I cluster HDInsight basati su Apache Hadoop 2,4 o versione successiva supportano la disponibilità elevata di YARN ResourceManager. Sono disponibili due ResourceManager, RM1 e RM2, in esecuzione rispettivamente in nodo Head 0 e nodo Head 1. Analogamente a NameNode, YARN ResourceManager viene configurato anche per il failover automatico. Un altro ResourceManager viene automaticamente eletto come attivo quando il ResourceManager attivo corrente diventa inattivo o non risponde.

YARN ResourceManager usa la *ActiveStandbyElector* incorporata come un rilevatore di errori e un elettore leader. A differenza di HDFS NameNode, YARN ResourceManager non necessita di un daemon ZKFC separato. Il ResourceManager attivo scrive i relativi stati in Apache Zookeeper.

La disponibilità elevata del ResourceManager YARN è indipendente da NameNode e da altri servizi a disponibilità elevata HDInsight. Il ResourceManager attivo non può essere eseguito nel nodo head attivo o nodo head in cui è in esecuzione il NameNode attivo. Per altre informazioni sulla disponibilità elevata di YARN ResourceManager, vedere la pagina relativa alla [disponibilità elevata di ResourceManager](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/ResourceManagerHA.html).

### <a name="hbase-master"></a>HBase Master

I cluster HBase HDInsight supportano HBase Master disponibilità elevata. A differenza di altri servizi a disponibilità elevata, eseguiti in nodi head, i master HBase vengono eseguiti nei tre nodi Zookeeper, dove uno di essi è il master attivo e gli altri due sono standby. Analogamente a NameNode, HBase Master coordina con Apache Zookeeper per l'elezione del leader ed esegue il failover automatico quando il master attivo corrente presenta problemi. Esiste solo un HBase Master attivo in qualsiasi momento.

## <a name="next-steps"></a>Passaggi successivi

- [Disponibilità e affidabilità dei cluster Apache Hadoop in HDInsight](hdinsight-high-availability-linux.md)
- [Architettura della rete virtuale di Azure HDInsight](hdinsight-virtual-network-architecture.md)
