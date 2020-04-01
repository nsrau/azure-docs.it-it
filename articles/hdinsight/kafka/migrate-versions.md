---
title: Eseguire la migrazione dei carichi di lavoro di Apache Kafka ad Azure HDInsight 4.0Migrate Apache Kafka workloads to Azure HDInsight 4.0
description: Informazioni su come eseguire la migrazione dei carichi di lavoro di Apache Kafka in HDInsight 3.6 a HDInsight 4.0.Learn how to migrate Apache Kafka workloads on HDInsight 3.6 to HDInsight 4.0.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: 0a31c6cf32222277e033aacf7d04622c54aef9ea
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437008"
---
# <a name="migrate-apache-kafka-workloads-to-azure-hdinsight-40"></a>Eseguire la migrazione dei carichi di lavoro di Apache Kafka ad Azure HDInsight 4.0Migrate Apache Kafka workloads to Azure HDInsight 4.0

Azure HDInsight 4.0 offre i componenti open source più recenti con miglioramenti significativi in termini di prestazioni, connettività e sicurezza. Questo documento spiega come eseguire la migrazione dei carichi di lavoro di Apache Kafka su HDInsight 3.6 a HDInsight 4.0.This document explains how to migrate Apache Kafka workloads on HDInsight 3.6 to HDInsight 4.0. Dopo aver eseguito la migrazione dei carichi di lavoro a HDInsight 4.0, è possibile usare molte delle nuove funzionalità che non sono disponibili in HDInsight 3.6.

## <a name="hdinsight-36-kafka-migration-paths"></a>Percorsi di migrazione HDInsight 3.6 KafkaHDInsight 3.6 Kafka migration paths

HDInsight 3.6 supporta due versioni di Kafka: 1.0.0 e 1.1.0. HDInsight 4.0 supporta le versioni 1.1.0 e 2.1.0.HDInsight 4.0 supports versions 1.1.0 and 2.1.0. A seconda della versione di Kafka e della versione di HDInsight che si desidera eseguire, sono disponibili più percorsi di migrazione supportati. Questi percorsi sono illustrati di seguito e illustrati nel diagramma seguente.

* **Eseguire sia Kafka che HDInsight nelle versioni più recenti (consigliato):** eseguire la migrazione di un'applicazione HDInsight 3.6 e Kafka 1.0.0 o 1.1.0 a HDInsight 4.0 con Kafka 2.1.0 (percorsi D ed E riportati di seguito).
* **Eseguire HDInsight sulla versione più recente, ma Kafka solo su una versione più recente:** eseguire la migrazione di un'applicazione HDInsight 3.6 e Kafka 1.0.0 a HDInsight 4.0 con Kafka 1.1.0 (percorso B riportato di seguito).
* **Eseguire HDInsight sulla versione più recente, mantenere la versione Kafka**: eseguire la migrazione di un'applicazione HDInsight 3.6 e Kafka 1.1.0 a HDInsight 4.0 con Kafka 1.1.0 (percorso C riportato di seguito).
* **Eseguire Kafka su una versione più recente, mantenere la versione HDInsight**: eseguire la migrazione di un'applicazione Kafka 1.0.0 alla versione 1.1.0 e rimanere su HDInsight 3.6 (percorso A riportato di seguito). Si noti che questa opzione richiederà comunque la distribuzione di un nuovo cluster. L'aggiornamento della versione Kafka in un cluster esistente non è supportato. Dopo aver creato un cluster con la versione desiderata, eseguire la migrazione dei client Kafka per utilizzare il nuovo cluster.

![Percorsi di aggiornamento per Apache Kafka su 3.6](./media/upgrade-threesix-to-four/apache-kafka-upgrade-path.png)

## <a name="apache-kafka-versions"></a>Versioni Apache Kafka

### <a name="kafka-110"></a>Kafka 1.1.0
  
Se si esegue la migrazione da Kafka 1.0.0 a 1.1.0, è possibile usufruire delle seguenti nuove funzionalità:

* I miglioramenti apportati al controller Kafka accelsono l'arresto controllato, in modo da poter riavviare i broker e risolvere i problemi più velocemente. 
* Miglioramenti nella [logica FetchRequests](https://issues.apache.org/jira/browse/KAFKA-6254) che consentono di avere più partizioni (e quindi più argomenti) nel cluster. 
* Kafka Connect supporta [le intestazioni di record](https://issues.apache.org/jira/browse/KAFKA-5142) e le espressioni [regolari](https://issues.apache.org/jira/browse/KAFKA-3073) per gli argomenti. 

Per un elenco completo degli aggiornamenti, vedere Note sulla versione di [Apache Kafka 1.1](https://archive.apache.org/dist/kafka/1.1.0/RELEASE_NOTES.html).

### <a name="apache-kafka-210"></a>Apache Kafka 2.1.0

Se si esegue la migrazione a Kafka 2.1, è possibile usufruire delle seguenti funzionalità:

* Migliore resilienza del broker grazie a un protocollo di replica migliorato.
* Nuove funzionalità nell'API KafkaAdminClient.
* Gestione delle quote configurabile.
* Supporto per la compressione standard.

Per un elenco completo degli aggiornamenti, vedere Note sulla versione di [Apache Kafka 2.0](https://archive.apache.org/dist/kafka/2.0.0/RELEASE_NOTES.html) e Note sulla versione di [Apache Kafka 2.1](https://archive.apache.org/dist/kafka/2.1.0/RELEASE_NOTES.html).

## <a name="kafka-client-compatibility"></a>Compatibilità client Kafka

I nuovi broker Kafka supportano i clienti più anziani. [KIP-35 - La versione](https://cwiki.apache.org/confluence/display/KAFKA/KIP-35+-+Retrieving+protocol+version) del protocollo di recupero ha introdotto un meccanismo per determinare dinamicamente la funzionalità di un broker Kafka e [KIP-97: i criteri](https://cwiki.apache.org/confluence/display/KAFKA/KIP-97%3A+Improved+Kafka+Client+RPC+Compatibility+Policy) di compatibilità RPC del client Kafka migliorato hanno introdotto un nuovo criterio di compatibilità e garanzie per il client Java. In precedenza, un client Kafka doveva interagire con un broker della stessa versione o di una versione più recente. Ora, le versioni più recenti dei client Java e di `librdkafka` altri client che supportano KIP-35, ad esempio possono eseguire il rollback ai tipi di richiesta meno recenti o generare errori appropriati se la funzionalità non è disponibile.

![Aggiornare la compatibilità dei client Kafka](./media/upgrade-threesix-to-four/apache-kafka-client-compatibility.png)

Si noti che non significa che il client supporta i broker meno recenti.  Per ulteriori informazioni, vedere [Matrice di compatibilità](https://cwiki.apache.org/confluence/display/KAFKA/Compatibility+Matrix).

## <a name="general-migration-process"></a>Processo di migrazione generale

Le linee guida per la migrazione seguenti presuppongono un cluster Apache Kafka 1.0.0 o 1.1.0 distribuito in HDInsight 3.6 in una singola rete virtuale. Il broker esistente ha alcuni argomenti e viene utilizzato attivamente da produttori e consumatori.

![Attuale ambiente presunto Kafka](./media/upgrade-threesix-to-four/apache-kafka-presumed-environment.png)

Per completare la migrazione, procedere come segue:

1. **Distribuire un nuovo cluster HDInsight 4.0 e i client per il test.** Distribuire un nuovo cluster HDInsight 4.0 Kafka.Deploy a new HDInsight 4.0 Kafka cluster. Se è possibile selezionare più versioni del cluster Kafka, è consigliabile selezionare la versione più recente. Dopo la distribuzione, impostare alcuni parametri in base alle esigenze e creare un argomento con lo stesso nome dell'ambiente esistente. Inoltre, impostare la crittografia TLS e bring-your-own-key (BYOK) in base alle esigenze. Verificare quindi se funziona correttamente con il nuovo cluster.

    ![Distribuire nuovi cluster HDInsight 4.0Deploy new HDInsight 4.0 clusters](./media/upgrade-threesix-to-four/deploy-new-hdinsight-clusters.png)

1. **Cambiare il cluster per l'applicazione producer e attendere che tutti i dati della coda vengano utilizzati dai consumer correnti.** Quando il nuovo cluster HDInsight 4.0 Kafka è pronto, passare la destinazione producer esistente al nuovo cluster. Lasciarlo così com'è fino a quando l'app consumer esistente ha utilizzato tutti i dati dal cluster esistente.

    ![Cambia cluster per l'app producer](./media/upgrade-threesix-to-four/switch-cluster-producer-app.png)

1. **Cambiare il cluster nell'applicazione consumer.** Dopo aver verificato che l'applicazione consumer esistente ha terminato di utilizzare tutti i dati del cluster esistente, passare la connessione al nuovo cluster.

    ![Passare da un cluster all'altro nell'app consumer](./media/upgrade-threesix-to-four/switch-cluster-consumer-app.png)

1. **Rimuovere il cluster precedente e le applicazioni di test in base alle esigenze.** Una volta che lo switch è completo e funziona correttamente, rimuovere il vecchio cluster HDInsight 3.6 Kafka e i produttori e i consumer utilizzati nel test in base alle esigenze.

## <a name="next-steps"></a>Passaggi successivi

* [Ottimizzazione delle prestazioni per i cluster Apache Kafka HDInsight](apache-kafka-performance-tuning.md)
* [Guida introduttiva: Creare un cluster Apache Kafka in Azure HDInsight usando il portale di AzureQuickstart: Create Apache Kafka cluster in Azure HDInsight using Azure portal](apache-kafka-get-started.md)
