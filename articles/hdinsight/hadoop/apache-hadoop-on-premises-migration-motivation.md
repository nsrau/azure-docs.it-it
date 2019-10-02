---
title: 'Eseguire la migrazione di Apache Hadoop locali ad Azure HDInsight: motivazione e vantaggi'
description: Motivazione e vantaggi per eseguire la migrazione di cluster Hadoop locali ad Azure HDInsight.
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: a65b775a516bfccac2dee5ce00bc7d6495df256d
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71718367"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---motivation-and-benefits"></a>Eseguire la migrazione di cluster Apache Hadoop locali ad Azure HDInsight - Motivazione e vantaggi

Questo articolo è il primo di una serie sulle procedure consigliate per la migrazione di distribuzioni locali dell'ecosistema Apache Hadoop ad Azure HDInsight. Questa serie di articoli è destinata ai responsabili della progettazione, distribuzione e migrazione di soluzioni Apache Hadoop ad Azure HDInsight. I ruoli che possono trarre vantaggio da questi articoli includono gli architetti cloud, gli amministratori di Hadoop e i tecnici di DevOps. Anche sviluppatori di software, data engineer e data scientist possono trarre vantaggio dalla spiegazione dei vari tipi di cluster operativi nel cloud.

## <a name="why-to-migrate-to-azure-hdinsight"></a>Perché eseguire la migrazione ad Azure HDInsight

Azure HDInsight è una distribuzione cloud dei componenti di Hadoop. Azure HDInsight rende semplice, rapida ed economicamente conveniente l'elaborazione di grandi quantità di dati. HDInsight include i framework open source più diffusi, ad esempio:

- Apache Hadoop
- Apache Spark
- Apache Hive con LLAP
- Apache Kafka
- Apache Storm
- Apache HBase
- R

## <a name="azure-hdinsight-advantages-over-on-premises-hadoop"></a>Vantaggi di Azure HDInsight rispetto a Hadoop in locale

- **Basso costo** - È possibile ridurre i costi [creando cluster su richiesta](../hdinsight-hadoop-create-linux-clusters-adf.md) e pagando solo in base al consumo. La separazione delle risorse di calcolo e archiviazione garantisce flessibilità, mantenendo il volume di dati indipendente dalle dimensioni del cluster.

- **Creazione automatica del cluster** - La creazione automatica del cluster richiede passaggi minimi di configurazione e installazione. È possibile usare l'automazione per i cluster su richiesta.

- **Hardware e configurazione gestiti** - Non è necessario preoccuparsi dell'hardware fisico o dell'infrastruttura con un cluster HDInsight. È sufficiente specificare la configurazione del cluster, che viene configurato automaticamente in Azure.

- **Scalabilità semplificata** - HDInsight consente di  [aumentare o ridurre](../hdinsight-administer-use-portal-linux.md) i carichi di lavoro. Azure ripete la distribuzione dei dati e il bilanciamento del carico di lavoro senza interrompere i processi di elaborazione dei dati.

- **Disponibilità globale** - HDInsight è disponibile in più [aree](https://azure.microsoft.com/regions/services/) di qualsiasi altra offerta di analisi di Big Data. Azure HDInsight è anche disponibile in Azure per enti pubblici, Cina e Germania per soddisfare le esigenze aziendali nelle principali aree sovrane.

- **Sicurezza e conformità** - HDInsight consente di proteggere i dati aziendali usando  [Rete virtuale di Azure](../hdinsight-plan-virtual-network-deployment.md), la  [crittografia](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md) e l'integrazione con  [Azure Active Directory](../domain-joined/hdinsight-security-overview.md). HDInsight soddisfa anche i più diffusi  [standard di conformità](https://azure.microsoft.com/overview/trusted-cloud) del settore e governativi.

- **Gestione semplificata della versione** - Azure HDInsight gestisce la versione dei componenti dell'ecosistema Hadoop e li mantiene aggiornati. Gli aggiornamenti software sono in genere un processo complesso per le distribuzioni locali.

- **Cluster ridotti ottimizzati per specifici carichi di lavoro con minori dipendenze tra i componenti** - Una tipica configurazione Hadoop locale usa un singolo cluster per molte finalità. Con Azure HDInsight è possibile creare cluster specifici per carico di lavoro. La creazione di cluster per carichi di lavoro specifici rimuove gli oneri di gestione di un singolo cluster con complessità crescenti.

- **Produttività** - È possibile usare vari strumenti per Hadoop e Spark nell'ambiente di sviluppo preferito.

- **Estendibilità con strumenti personalizzati o applicazioni di terze parti** - I cluster HDInsight possono essere estesi con i componenti installati e integrati con altre soluzioni per Big Data mediante distribuzioni [con un clic](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) in Azure Marketplace.

- **Facile gestione, amministrazione e monitoraggio** : Azure HDInsight si integra con i [log di monitoraggio di Azure](../hdinsight-hadoop-oms-log-analytics-tutorial.md) per offre una singola interfaccia con cui è possibile monitorare tutti i cluster.

- **Integrazione con altri servizi di Azure** - HDInsight può essere integrato facilmente con altri popolari servizi di Azure, tra cui:

    - Azure Data Factory
    - Archivio BLOB di Azure
    - Azure Data Lake Storage Gen2
    - Azure Cosmos DB
    - Database SQL di Azure
    - Azure Analysis Services

- **Riparazione automatica di processi e i componenti** - HDInsight controlla costantemente i componenti open source e dell'infrastruttura mediante la propria infrastruttura di monitoraggio. Ripristina automaticamente anche errori critici, ad esempio l'assenza di disponibilità di nodi e componenti open source. Vengono attivati avvisi in Ambari in caso di errore in qualsiasi componente OSS.

Per altre informazioni, vedere l'articolo [Informazioni su Azure HDInsight e sullo stack di tecnologie Apache Hadoop](../hadoop/apache-hadoop-introduction.md).

## <a name="migration-planning-process"></a>Processo di pianificazione della migrazione

I passaggi seguenti sono consigliati per la pianificazione della migrazione dei cluster Hadoop locali ad Azure HDInsight:

1. Comprendere la distribuzione locale corrente e le topologie.
2. Comprendere l'ambito del progetto corrente, le sequenze temporali e le competenze del team.
3. Comprendere i requisiti di Azure.
4. Creare un piano dettagliato basato su procedure consigliate.

## <a name="gathering-details-to-prepare-for-a-migration"></a>Raccolta di dettagli per predisporre una migrazione

Questa sezione offre questionari modello per semplificare la raccolta di informazioni importanti su:

- Distribuzione locale
- Dettagli del progetto
- Requisiti di Azure

### <a name="on-premises-deployment-questionnaire"></a>Questionario di distribuzione locale

| **Domanda** | **Esempio** | **Risposta** |
|---|---|---|
|**Argomento**: **Environment**|||
|Versione di distribuzione cluster|HDP 2.6.5, CDH 5.7|
|Componenti dell'ecosistema di Big Data|Hadoop Distributed File System, Yarn, Hive, LLAP, Impala, Kudu, HBase, Spark, MapReduce, Kafka, Zookeeper, Solr, Sqoop, Oozie, Ranger, Atlas, Falcon, Zeppelin, R|
|Tipi di cluster|Hadoop, Spark, Confluent Kafka, Storm, Solr|
|Numero di cluster|4|
|Numero di nodi master|2|
|Numero di nodi del ruolo di lavoro|100|
|Numero di nodi perimetrali| 5|
|Spazio totale su disco|100 TB|
|Configurazione del nodo master|m/y, cpu, disco, ecc.|
|Configurazione dei nodi dati|m/y, cpu, disco, ecc.|
|Configurazione dei nodi perimetrali|m/y, cpu, disco, ecc.|
|Crittografia Hadoop Distributed File System?|Yes|
|Disponibilità elevata|Disponibilità elevata Hadoop Distributed File System, disponibilità elevata metastore|
|Ripristino di emergenza/backup|Cluster di backup?|  
|Sistemi che dipendono dal cluster|SQL Server, Teradata, Power BI, MongoDB|
|Integrazioni di terze parti|Tableau, GridGain, Qubole, Informatica, Splunk|
|**Argomento**: **Sicurezza**|||
|Protezione perimetrale|Firewall|
|Autenticazione e autorizzazione del cluster|Active Directory, Ambari, Cloudera Manager, nessuna autenticazione|
|Controllo di accesso Hadoop Distributed File System|  Manuale, utenti SSH|
|Autenticazione e autorizzazione degli hive|Sentry, LDAP, AD con Kerberos, Ranger|
|Controllo|Ambari, Cloudera Navigator, Ranger|
|Monitoraggio|Graphite, collectd, statsd, Telegraf, InfluxDB|
|Creazione di avvisi|Kapacitor, Prometheus, Datadog|
|Durata di conservazione dati| 3 anni, 5 anni|
|Amministratori del cluster|Un amministratore, più amministratori|

### <a name="project-details-questionnaire"></a>Questionario sui dettagli progetto

|**Domanda**|**Esempio**|**Risposta**|
|---|---|---|
|**Argomento**: **carichi di lavoro e frequenza**|||
|Processi MapReduce|10 processi due volte al giorno||
|Processi Hive|100 processi ogni ora||
|Processi batch Spark|50 processi ogni 15 minuti||
|Processi Spark Streaming|5 processi ogni 3 minuti||
|Processi Structured Streaming|5 processi ogni minuto||
|Processi di training del modello ML|2 processi una volta a settimana||
|Linguaggi di programmazione|Python, Scala, Java||
|Esecuzione script|Shell, Python||
|**Argomento**: **Dati**|||
|Origini dati|File flat, JSON, Kafka, RDBMS||
|Orchestrazione dei dati|Flussi di lavoro Oozie, Airflow||
|Ricerche in memoria|Apache Ignite, Redis||
|Destinazioni dei dati|Hadoop Distributed File System, RDBMS, Kafka, MPP ||
|**Argomento**: **Metadati**|||
|Tipo di database Hive|MySQL, Postgres||
|Numero di Metastore hive|2||
|Numero di tabelle hive|100||
|Numero di criteri Ranger|20||
|Numero di flussi di lavoro OOZIE|100||
|**Argomento**: **Ridimensionare**|||
|Volume di dati, inclusa la replica|100 TB||
|Volume di inserimento giornaliero|50 GB||
|Tasso di crescita dei dati|10% all'anno||
|Tasso di crescita dei nodi del cluster|5% all'anno
|**Argomento**: **utilizzo del cluster**|||
|% media CPU usata|60%||
|% media memoria usata|75%||
|Spazio su disco usato|75%||
|% media rete usata|25%
|**Argomento**: **Staff**|||
|Numero di amministratori|2||
|Numero di sviluppatori|10||
|Numero di utenti finali|100||
|Competenze|Hadoop, Spark||
|Numero di risorse disponibili per le attività di migrazione|2||
|**Argomento**: **Limitazioni**|||
|Limitazioni correnti|Latenza elevata||
|Sfide correnti|Problema di concorrenza||

### <a name="azure-requirements-questionnaire"></a>Questionario sui requisiti di Azure

|**Argomento**: **Infrastruttura** |||
|---|---|---|
|**Domanda**|**Esempio**|**Risposta**|
| Area preferita|Stati Uniti orientali||
|Rete virtuale preferita?|Yes||
|Disponibilità elevata/ripristino di emergenza necessari?|Yes||
|Integrazione con altri servizi cloud?|File di definizione dell'applicazione (ADF), CosmosDB||
|**Argomento**:   **Spostamento dei dati**  |||
|Preferenza di caricamento iniziale|DistCp, Data Box, ADF, WANDisco||
|Delta di trasferimento dati|DistCp, AzCopy||
|Trasferimento incrementale costante dei dati|DistCp, Sqoop||
|**Argomento**:   **monitoraggio e invio di avvisi** |||
|Uso di Monitoraggio di Azure e invio di avvisi/integrazione del monitoraggio di terze parti|Uso di Monitoraggio di Azure e invio di avvisi||
|**Argomento**:   **preferenze relative alla sicurezza** |||
|Pipeline di dati privata e protetta?|Yes||
|Cluster aggiunto a un dominio (ESP)?|     Yes||
|Sincronizzazione da AD locale al cloud?|     Yes||
|Numero di utenti di Active Directory da sincronizzare.|          100||
|OK sincronizzare le password nel cloud?|    Yes||
|Utenti solo cloud?|                 Yes||
|MFA necessaria?|                       No|| 
|Requisiti di autorizzazione dei dati?|  Yes||
|Controllo degli accessi in base al ruolo?|        Yes||
|Controllo necessario?|                  Yes||
|Crittografia dei dati inattivi?|          Yes||
|Crittografia dei dati in movimento?|       Yes||
|**Argomento**:   **preferenze di riprogettazione** |||
|Tipi di cluster singoli o specifici|Tipi di cluster specifici||
|Archiviazione remota o con percorso condiviso?|Archiviazione remota||
|Dimensioni del cluster ridotte man mano che i dati vengono archiviati in remoto?|Dimensioni del cluster ridotte||
|Uso di più cluster ridotti anziché di un unico cluster di grandi dimensioni?|Uso di più cluster ridotti||
|Uso di un metastore remoto?|Yes||
|Condivisione di metastore tra diversi cluster?|Yes||
|Decostruzione dei carichi di lavoro?|Sostituzione di processi Hive con processi Spark||
|Uso del file di definizione dell'applicazione (ADF) per l'orchestrazione dei dati?|No||

## <a name="next-steps"></a>Passaggi successivi

Leggere l'articolo successivo di questa serie:

- [Architecture best practices for on-premises to Azure HDInsight Hadoop migration](apache-hadoop-on-premises-migration-best-practices-architecture.md) (Procedure consigliate per l'architettura relative alla migrazione da locale ad Azure HDInsight Hadoop)
