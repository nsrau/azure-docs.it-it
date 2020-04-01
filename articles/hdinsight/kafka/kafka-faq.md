---
title: Domande frequenti su Apache Kafka in Azure HDInsightFAQ about Apache Kafka in Azure HDInsight
description: Risposte a domande comuni su Apache Kafka in Azure HDInsight, un servizio cloud Hadoop gestito.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: d27c648980338b3a9e12bd618eb4620fe9988fd7
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436895"
---
# <a name="frequently-asked-questions-about-apache-kafka-in-azure-hdinsight"></a>Domande frequenti su Apache Kafka in Azure HDInsight

Questo articolo illustra alcune domande comuni sull'uso di Apache Kafka in Azure HDInsight.This article addresses some common questions about using Apache Kafka on Azure HDInsight.

## <a name="what-kafka-versions-are-supported-by-hdinsight"></a>Quali versioni di Kafka sono supportate da HDInsight?

Per ulteriori informazioni sulle versioni dei componenti supportate ufficialmente da HDInsight, vedere Quali sono i componenti e le [versioni Apache Hadoop disponibili con HDInsight?](../hdinsight-component-versioning.md#supported-hdinsight-versions). Si consiglia di utilizzare sempre la versione più recente per garantire le migliori prestazioni possibili e l'esperienza utente.

## <a name="what-resources-are-provided-in-an-hdinsight-kafka-cluster-and-what-resources-am-i-charged-for"></a>Quali risorse vengono fornite in un cluster HDInsight Kafka e per quali risorse vengono addebitate?

Un cluster Kafka HDInsight include le risorse seguenti:A HDInsight Kafka cluster includes the following resources:

* Nodi head
* Nodi Zookeeper
* Nodi broker (lavoratore) 
* Dischi gestiti di Azure collegati ai nodi brokerAzure Managed Disks attached to the broker nodes
* Nodi gateway

Tutte queste risorse vengono addebitate in base al modello di [determinazione dei prezzi HDInsight,](https://azure.microsoft.com/pricing/details/hdinsight/)ad eccezione dei nodi gateway. Non viene addebitato alcun costo per i nodi gateway.

Per una descrizione più dettagliata dei vari tipi di nodo, vedere Architettura della [rete virtuale di Azure HDInsight.For](../hdinsight-virtual-network-architecture.md)a more detailed description of various node types, see Azure HDInsight virtual network architecture . I prezzi si basano sull'utilizzo del nodo al minuto. I prezzi variano a seconda delle dimensioni del nodo, del numero di nodi, del tipo di disco gestito utilizzato e dell'area.

## <a name="do-apache-kafka-apis-work-with-hdinsight"></a>Le API Apache Kafka funzionano con HDInsight?

Sì, HDInsight usa LE API Kafka native. Il codice dell'applicazione client non deve essere modificato. Vedere [Esercitazione: Usare le API Consumer e Apache Kafka](./apache-kafka-producer-consumer-api.md) per vedere come è possibile usare le API producer/consumer basate su Java con il cluster.

## <a name="can-i-change-cluster-configurations"></a>È possibile modificare le configurazioni del cluster?

Sì, attraverso il portale Ambari. Ogni componente nel portale dispone di una sezione **di configurazioni,** che può essere utilizzata per modificare le configurazioni dei componenti. Alcune modifiche potrebbero richiedere il riavvio del broker.

## <a name="what-type-of-authentication-does-hdinsight-support-for-apache-kafka"></a>Che tipo di autenticazione supporta HDInsight per Apache Kafka?

Utilizzando [Enterprise Security Package (ESP)](../domain-joined/apache-domain-joined-architecture.md)è possibile ottenere la protezione a livello di argomento per i cluster Kafka. Per ulteriori informazioni, vedere [Esercitazione: Configurare i criteri Apache Kafka in HDInsight con Enterprise Security Package (anteprima).](../domain-joined/apache-domain-joined-run-kafka.md)

## <a name="is-my-data-encrypted-can-i-use-my-own-keys"></a>I miei dati sono crittografati? Posso usare le mie chiavi?

Tutti i messaggi Kafka sui dischi gestiti vengono crittografati con Crittografia servizio [di archiviazione di Azure (SSE).](../../storage/common/storage-service-encryption.md) I dati in transito (ad esempio, i dati trasmessi dai client ai broker e viceversa) non sono crittografati per impostazione predefinita. E 'possibile crittografare tale traffico [impostando TLS da soli](./apache-kafka-ssl-encryption-authentication.md). Inoltre, HDInsight consente di gestire le proprie chiavi per crittografare i dati inattivi. Per ulteriori informazioni, vedere [Crittografia del disco di chiavi gestita dal cliente.](../disk-encryption.md)

## <a name="how-do-i-connect-clients-to-my-cluster"></a>Come si connettono i client al cluster?

Per i client Kafka di comunicare con i broker Kafka, devono essere in grado di raggiungere i broker attraverso la rete. Per i cluster HDInsight, la rete virtuale (VNet) è il limite di sicurezza. Di conseguenza, il modo più semplice per connettere i client al cluster HDInsight consiste nel creare client all'interno della stessa rete virtuale del cluster. Altri scenari includono:

* Connessione dei client in una rete virtuale di Azure diversa: peerizzare la rete virtuale del cluster e la rete virtuale client e configurare il cluster per [IP Advertising](apache-kafka-connect-vpn-gateway.md#configure-kafka-for-ip-advertising). Quando si utilizza la pubblicità IP, i client Kafka devono utilizzare gli indirizzi IP del broker per connettersi con i broker, anziché i nomi di dominio completi (FQDN).

* Connessione di client locali: utilizzo di una rete VPN e configurazione di server DNS personalizzati come descritto in [Pianificare una rete virtuale per Azure HDInsight.Connecting](../hdinsight-plan-virtual-network-deployment.md)on-premises clients – Using a VPN network and setting up custom DNS servers as described in Plan a virtual network for Azure HDInsight .

* Creazione di un endpoint pubblico per il servizio Kafka: se i requisiti di sicurezza aziendali lo consentono, è possibile distribuire un endpoint pubblico per i broker Kafka o un endpoint finale REST open source autogestito con un endpoint pubblico.

## <a name="can-i-add-more-disk-space-on-an-existing-cluster"></a>È possibile aggiungere ulteriore spazio su disco in un cluster esistente?

Per aumentare la quantità di spazio disponibile per i messaggi Kafka, è possibile aumentare il numero di nodi. Attualmente, l'aggiunta di altri dischi a un cluster esistente non è supportata.

## <a name="can-a-kafka-cluster-work-with-databricks"></a>Un cluster Kafka può funzionare con Databricks? 

Sì, i cluster Kafka possono funzionare con Databricks purché si trovino nella stessa rete virtuale. Per usare un cluster Kafka con Databricks, creare una rete virtuale contenente un cluster HDInsight Kafka, quindi specificare tale rete quando si crea l'area di lavoro Databricks e si usa l'inserimento della rete virtuale. Per altre informazioni, vedere [Distribuire Azure Databricks nella rete virtuale di Azure (inserimento in rete virtuale)](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject). È necessario fornire i nomi del broker bootstrap del cluster Kafka durante la creazione dell'area di lavoro Databricks. Per informazioni sul recupero dei nomi dei broker Kafka, vedere [Ottenere le informazioni sull'host apache di e](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started#getkafkainfo)broker.

## <a name="how-can-i-have-maximum-data-durability"></a>Come posso avere la massima durata dei dati?

La durata dei dati consente di ottenere il minor rischio di perdita di messaggi. Per ottenere la massima durabilità dei dati, si consigliano le seguenti impostazioni:

* utilizzare un fattore di replica minimo di 3 nella maggior parte delle aree
* utilizzare un fattore di replica minimo pari a 4 nelle aree con solo due domini di errore
* disabilitare le elezioni leader impuri
* impostare **min.insync.replicas** su 2 o più - questo cambia il numero di repliche che devono essere completamente sincronizzate con il leader prima di una scrittura può procedere
* impostare la proprietà **acks** su **all** - questa proprietà richiede che tutte le repliche riconoscano tutti i messaggi

La configurazione di Kafka per una maggiore coerenza dei dati influisce sulla disponibilità dei broker per la produzione di richieste.

## <a name="can-i-replicate-my-data-to-multiple-clusters"></a>È possibile replicare i dati in più cluster?

Sì, i dati possono essere replicati in più cluster utilizzando Kafka MirrorMaker. Vedere i dettagli sull'impostazione di MirrorMaker sono disponibili negli argomenti di [Mirror Apache Kafka](apache-kafka-mirroring.md). Inoltre, esistono altre tecnologie open source autogestite e fornitori che consentono di ottenere la replica in più cluster, ad esempio [Brooklin](https://github.com/linkedin/Brooklin/).

## <a name="can-i-upgrade-my-cluster-how-should-i-upgrade-my-cluster"></a>È possibile aggiornare il cluster? Come è necessario aggiornare il cluster?

Al momento non sono supportati gli aggiornamenti delle versioni del cluster sul posto. Per aggiornare il cluster a una versione Kafka superiore, creare un nuovo cluster con la versione desiderata ed eseguire la migrazione dei client Kafka per l'utilizzo del nuovo cluster.

## <a name="how-do-i-monitor-my-kafka-cluster"></a>Come si monitora il cluster Kafka?

Usare Monitoraggio di Azure per analizzare [i log Kafka.](./apache-kafka-log-analytics-operations-management.md)

## <a name="next-steps"></a>Passaggi successivi

* [Configurare la crittografia e l'autenticazione TLS per Apache Kafka in Azure HDInsightSet up TLS encryption and authentication for Apache Kafka in Azure HDInsight](./apache-kafka-ssl-encryption-authentication.md)
* [Usare MirrorMaker per replicare gli argomenti di Apache Kafka con Kafka in HDInsight](./apache-kafka-mirroring.md)
