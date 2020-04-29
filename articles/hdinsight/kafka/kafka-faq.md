---
title: Domande frequenti sulle Apache Kafka in Azure HDInsight
description: Risposte alle domande frequenti su Apache Kafka in Azure HDInsight, un servizio cloud Hadoop gestito.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: d27c648980338b3a9e12bd618eb4620fe9988fd7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80436895"
---
# <a name="frequently-asked-questions-about-apache-kafka-in-azure-hdinsight"></a>Domande frequenti su Apache Kafka in Azure HDInsight

Questo articolo risponde ad alcune domande comuni sull'uso di Apache Kafka in Azure HDInsight.

## <a name="what-kafka-versions-are-supported-by-hdinsight"></a>Quali versioni Kafka sono supportate da HDInsight?

Per ulteriori informazioni sulle versioni dei componenti di HDInsight ufficialmente supportate [, vedere quali sono i componenti e le versioni di Apache Hadoop disponibili con HDInsight?](../hdinsight-component-versioning.md#supported-hdinsight-versions). È consigliabile usare sempre la versione più recente per garantire le migliori prestazioni e l'esperienza utente possibili.

## <a name="what-resources-are-provided-in-an-hdinsight-kafka-cluster-and-what-resources-am-i-charged-for"></a>Quali risorse vengono fornite in un cluster HDInsight Kafka e quali risorse sono addebitate?

Un cluster HDInsight Kafka include le risorse seguenti:

* Nodi head
* Nodi Zookeeper
* Nodi broker (Worker) 
* Azure Managed Disks collegato ai nodi broker
* Nodi gateway

Tutte queste risorse vengono addebitate in base al [modello di determinazione prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/), ad eccezione dei nodi del gateway. Non vengono addebitati i costi per i nodi gateway.

Per una descrizione più dettagliata dei vari tipi di nodo, vedere [architettura di rete virtuale di Azure HDInsight](../hdinsight-virtual-network-architecture.md). I prezzi sono basati sull'utilizzo del nodo al minuto. I prezzi variano in base alle dimensioni del nodo, al numero di nodi, al tipo di disco gestito usato e all'area.

## <a name="do-apache-kafka-apis-work-with-hdinsight"></a>Le API Apache Kafka funzionano con HDInsight?

Sì, HDInsight usa le API Kafka native. Non è necessario modificare il codice dell'applicazione client. Vedere [esercitazione: usare le API Producer e consumer Apache Kafka](./apache-kafka-producer-consumer-api.md) per vedere come è possibile usare le API producer/consumer basate su Java con il cluster.

## <a name="can-i-change-cluster-configurations"></a>È possibile modificare le configurazioni del cluster?

Sì, tramite il portale Ambari. Ogni componente nel portale include una sezione **configs** , che può essere usata per modificare le configurazioni dei componenti. Alcune modifiche potrebbero richiedere il riavvio del broker.

## <a name="what-type-of-authentication-does-hdinsight-support-for-apache-kafka"></a>Quale tipo di autenticazione supporta HDInsight per Apache Kafka?

Usando [Enterprise Security Package (ESP)](../domain-joined/apache-domain-joined-architecture.md), è possibile ottenere la sicurezza a livello di argomento per i cluster Kafka. Per ulteriori informazioni, vedere [esercitazione: configurare i criteri di Apache Kafka in HDInsight con Enterprise Security Package (anteprima)](../domain-joined/apache-domain-joined-run-kafka.md).

## <a name="is-my-data-encrypted-can-i-use-my-own-keys"></a>I dati sono crittografati? Posso usare le mie chiavi?

Tutti i messaggi Kafka nei dischi gestiti vengono crittografati con [Azure crittografia del servizio di archiviazione (SSE)](../../storage/common/storage-service-encryption.md). I dati in transito (ad esempio, i dati trasmessi dai client ai broker e viceversa) non sono crittografati per impostazione predefinita. È possibile crittografare tale traffico [configurando TLS autonomamente](./apache-kafka-ssl-encryption-authentication.md). HDInsight consente inoltre di gestire le proprie chiavi per crittografare i dati inattivi. Per ulteriori informazioni, vedere [crittografia del disco della chiave gestita dal cliente](../disk-encryption.md).

## <a name="how-do-i-connect-clients-to-my-cluster"></a>Ricerca per categorie connettere i client al cluster?

Per consentire ai client Kafka di comunicare con i broker Kafka, questi devono essere in grado di raggiungere i broker attraverso la rete. Per i cluster HDInsight, la rete virtuale (VNet) è il limite di sicurezza. Di conseguenza, il modo più semplice per connettere i client al cluster HDInsight consiste nel creare client all'interno della stessa VNet del cluster. Altri scenari includono:

* Connessione dei client in un'altra VNet di Azure: peer the cluster VNet and the client VNet e Configure the cluster for [IP Advertising](apache-kafka-connect-vpn-gateway.md#configure-kafka-for-ip-advertising). Quando si usa la pubblicità IP, i client Kafka devono usare indirizzi IP broker per connettersi ai broker, anziché nomi di dominio completi (FQDN).

* Connessione di client locali: uso di una rete VPN e configurazione di server DNS personalizzati come descritto in [pianificare una rete virtuale per Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md).

* Creazione di un endpoint pubblico per il servizio Kafka: se i requisiti di sicurezza aziendali lo consentono, è possibile distribuire un endpoint pubblico per i broker Kafka o un endpoint REST Open Source autonomo con un endpoint pubblico.

## <a name="can-i-add-more-disk-space-on-an-existing-cluster"></a>È possibile aggiungere altro spazio su disco in un cluster esistente?

Per aumentare la quantità di spazio disponibile per i messaggi Kafka, è possibile aumentare il numero di nodi. Attualmente, l'aggiunta di altri dischi a un cluster esistente non è supportata.

## <a name="can-a-kafka-cluster-work-with-databricks"></a>Un cluster Kafka può funzionare con databricks? 

Sì, i cluster Kafka possono funzionare con databricks purché si trovino nello stesso VNet. Per usare un cluster Kafka con databricks, creare una VNet con un cluster HDInsight Kafka, quindi specificare VNet quando si crea l'area di lavoro di databricks e si usa VNet injection. Per altre informazioni, vedere [Distribuire Azure Databricks nella rete virtuale di Azure (inserimento in rete virtuale)](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject). Quando si crea l'area di lavoro di databricks, sarà necessario fornire i nomi del broker di bootstrap del cluster Kafka. Per informazioni sul recupero dei nomi del broker Kafka, vedere [ottenere le informazioni sull'host di Apache Zookeeper e broker](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started#getkafkainfo).

## <a name="how-can-i-have-maximum-data-durability"></a>Come è possibile ottenere la durabilità massima dei dati?

La durabilità dei dati consente di ottenere il rischio più basso di perdita dei messaggi. Per ottenere la massima durabilità dei dati, è consigliabile usare le impostazioni seguenti:

* usare un fattore di replica minimo di 3 nella maggior parte delle aree
* usare un fattore di replica minimo di 4 in aree con solo due domini di errore
* disabilitare le elezioni leader non pulite
* imposta **min. InSync. repliche** su 2 o più: modifica il numero di repliche che devono essere completamente sincronizzate con il leader prima che una scrittura possa continuare
* impostare la proprietà **ACK** su **All** . questa proprietà richiede che tutte le repliche riconosca tutti i messaggi

La configurazione di Kafka per una maggiore coerenza dei dati influiscono sulla disponibilità di broker per la produzione di richieste.

## <a name="can-i-replicate-my-data-to-multiple-clusters"></a>È possibile replicare i dati in più cluster?

Sì, i dati possono essere replicati in più cluster usando Kafka MirrorMaker. Vedere i dettagli sulla configurazione di MirrorMaker sono disponibili in [Mirror Apache Kafka argomenti](apache-kafka-mirroring.md). Sono inoltre disponibili altre tecnologie e fornitori open source autogestiti che consentono di ottenere la replica in più cluster, ad esempio [Brooklin](https://github.com/linkedin/Brooklin/).

## <a name="can-i-upgrade-my-cluster-how-should-i-upgrade-my-cluster"></a>È possibile aggiornare il cluster? Come si aggiorna il cluster?

Attualmente non sono supportati gli aggiornamenti della versione del cluster sul posto. Per aggiornare il cluster a una versione di Kafka superiore, creare un nuovo cluster con la versione desiderata ed eseguire la migrazione dei client Kafka per l'uso del nuovo cluster.

## <a name="how-do-i-monitor-my-kafka-cluster"></a>Ricerca per categorie monitorare il cluster Kafka?

Usare monitoraggio di Azure per analizzare i [log di Kafka](./apache-kafka-log-analytics-operations-management.md).

## <a name="next-steps"></a>Passaggi successivi

* [Configurare la crittografia e l'autenticazione TLS per Apache Kafka in Azure HDInsight](./apache-kafka-ssl-encryption-authentication.md)
* [Usare MirrorMaker per replicare gli argomenti di Apache Kafka con Kafka in HDInsight](./apache-kafka-mirroring.md)
