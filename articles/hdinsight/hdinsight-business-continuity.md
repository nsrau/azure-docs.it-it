---
title: Continuità aziendale di Azure HDInsight
description: Questo articolo offre una panoramica delle procedure consigliate, della disponibilità in singole aree e delle opzioni di ottimizzazione per la pianificazione della continuità aziendale di Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: alta disponibilità di hadoop
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: beb3c54a0ab7f6f063232a1ad49744d99746c589
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893646"
---
# <a name="azure-hdinsight-business-continuity"></a>Continuità aziendale di Azure HDInsight

I cluster Azure HDInsight dipendono da molti servizi di Azure, ad esempio archiviazione, database, Active Directory, Active Directory Domain Services, rete e Key Vault. Un'applicazione di analisi ben progettata, a disponibilità elevata e a tolleranza di errore deve essere progettata con una ridondanza sufficiente per resistere a interruzioni locali o locali in uno o più di questi servizi. Questo articolo fornisce una panoramica delle procedure consigliate, della disponibilità di singole aree e delle opzioni di ottimizzazione per la pianificazione della continuità aziendale.

## <a name="general-best-practices"></a>Procedure consigliate generali

In questa sezione vengono illustrate alcune procedure consigliate da prendere in considerazione durante la pianificazione della continuità aziendale.

* Determinare le funzionalità di business minime necessarie in caso di emergenza. Valutare, ad esempio, se sono necessarie funzionalità di failover per il livello di trasformazione dati (visualizzato in giallo) *e* il livello di servizio dati (visualizzato in blu) o se è necessario solo il failover per il livello del servizio dati.

   :::image type="content" source="media/hdinsight-business-continuity/data-layers.png" alt-text="trasformazione dei dati e livelli di servizio dati":::

* Segmentare i cluster in base al carico di lavoro, al ciclo di vita di sviluppo e ai reparti. La presenza di più cluster riduce le probabilità di un singolo errore di grandi dimensioni che interessano più processi aziendali diversi.

* Rendere le aree secondarie di sola lettura. Le aree di failover con funzionalità di lettura e scrittura possono causare architetture complesse.

* I cluster temporanei sono più facili da gestire quando si verifica un'emergenza. Progettare i carichi di lavoro in modo che sia possibile ciclare i cluster e che lo stato non venga mantenuto nei cluster.

* Spesso i carichi di lavoro non sono finiti se si verifica un'emergenza ed è necessario riavviarli nella nuova area. Progettare i carichi di lavoro in modo che siano idempotente di natura.

* Usare l'automazione durante le distribuzioni del cluster e assicurarsi che le impostazioni di configurazione del cluster siano scritte per quanto possibile per garantire una distribuzione rapida e completamente automatizzata in caso di emergenza.

* Usare gli strumenti di monitoraggio di Azure in HDInsight per rilevare un comportamento anomalo nel cluster e impostare le notifiche di avviso corrispondenti. È possibile distribuire le soluzioni di gestione specifiche per i cluster HDInsight preconfigurate che raccolgono importanti metriche delle prestazioni del tipo di cluster specifico. Per altre informazioni, vedere [monitoraggio di Azure per HDInsight](./hdinsight-hadoop-oms-log-analytics-tutorial.md).  

* Sottoscrivere gli avvisi di integrità di Azure per ricevere notifiche relative a problemi di servizio, manutenzione pianificata, avvisi di integrità e sicurezza per una sottoscrizione, un servizio o un'area. Le notifiche di integrità che includono la causa del problema e l'ETA risoluto consentono di eseguire al meglio failover e i failback. Per altre informazioni, vedere la [documentazione sull'integrità dei servizi di Azure](/azure/service-health/).

## <a name="single-region-availability"></a>Disponibilità in una singola area

Un sistema HDInsight di base presenta i componenti seguenti. Tutti i componenti hanno meccanismi di tolleranza di errore a singola area.

* Calcolo (macchine virtuali): cluster Azure HDInsight
* Metastore: database SQL di Azure
* Archiviazione: Azure Data Lake Gen2 o archiviazione BLOB
* Autenticazione: Azure Active Directory, Azure Active Directory Domain Services, Enterprise Security Package
* Risoluzione dei nomi di dominio: DNS di Azure

Sono disponibili altri servizi facoltativi che è possibile usare, ad esempio Azure Key Vault e Azure Data Factory.

:::image type="content" source="media/hdinsight-business-continuity/hdinsight-components.png" alt-text="trasformazione dei dati e livelli di servizio dati":::

### <a name="azure-hdinsight-cluster-compute"></a>Cluster HDInsight di Azure (calcolo)

HDInsight offre un contratto di contratto di disponibilità pari al 99,9%. Per garantire un'elevata disponibilità in una singola distribuzione, HDInsight è accompagnato da molti servizi in modalità a disponibilità elevata per impostazione predefinita. I meccanismi di tolleranza di errore in HDInsight sono forniti dai servizi di disponibilità elevata dell'ecosistema OSS Microsoft e Apache.

I servizi seguenti sono progettati per essere a disponibilità elevata:

#### <a name="infrastructure"></a>Infrastruttura

* Nodi head attivo e standby
* Più nodi del gateway
* Tre nodi quorum Zookeeper
* Nodi di lavoro distribuiti da domini di errore e di aggiornamento

#### <a name="service"></a>Servizio

* Server Apache Ambari
* Sequenza temporale applicazione per YARN
* Server di cronologia processo per Hadoop MapReduce
* Apache Livy
* HDFS
* Gestione risorse YARN
* HBase Master

Per altre informazioni, vedere la documentazione sui [Servizi a disponibilità elevata supportati da Azure HDInsight](./hdinsight-high-availability-components.md) .

Non è sempre necessario un evento irreversibile per influito sulle funzionalità aziendali. Gli eventi imprevisti del servizio in uno o più dei servizi seguenti in una singola area possono anche causare la perdita delle funzionalità aziendali previste.

### <a name="hdinsight-metastore"></a>Metastore HDInsight

HDInsight usa il [database SQL di Azure](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/) come Metastore, che fornisce un contratto di contratto del 99,99%. Tre repliche dei dati vengono mantenute all'interno di un data center con la replica sincrona. Se si verifica una perdita di replica, una replica alternativa viene gestita in modo uniforme. La [replica geografica attiva](../azure-sql/database/active-geo-replication-overview.md) è supportata in modo predefinito, con un massimo di quattro Data Center. Quando si verifica un failover, manuale o data center, la prima replica nella gerarchia diventerà automaticamente in grado di eseguire la lettura e scrittura. Per altre informazioni, vedere [continuità aziendale del database SQL di Azure](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md).

### <a name="hdinsight-storage"></a>Archiviazione HDInsight

HDInsight consiglia Azure Data Lake Storage Gen2 come livello di archiviazione sottostante. [Archiviazione di Azure](https://azure.microsoft.com/support/legal/sla/storage/v1_5/), tra cui Azure Data Lake storage Gen2, fornisce un contratto di contratto del 99,9%. HDInsight usa il servizio con ridondanza locale in cui tre repliche dei dati vengono mantenute all'interno di una data center e la replica è sincrona. Quando si verifica una perdita di replica, una replica viene servita facilmente.

### <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/) fornisce un contratto di contratto del 99,9%. Active Directory è un servizio globale con più livelli di ridondanza interna e ripristino automatico. Per ulteriori informazioni, vedere la pagina relativa al modo [in cui Microsoft migliora costantemente l'affidabilità del Azure Active Directory](https://azure.microsoft.com/blog/advancing-azure-active-directory-availability/).

### <a name="azure-active-directory-domain-services-ad-ds"></a>Azure Active Directory Domain Services (AD DS)

[Azure Active Directory Domain Services](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/) fornisce un contratto di contratto del 99,9%. Azure AD DS è un servizio a disponibilità elevata ospitato in data center distribuiti a livello globale. I set di repliche sono una funzionalità di anteprima in Azure AD DS che consente il ripristino di emergenza geografico se un'area di Azure passa alla modalità offline. Per ulteriori informazioni, vedere [la pagina relativa ai concetti e alle funzionalità dei set di repliche per Azure Active Directory Domain Services](../active-directory-domain-services/concepts-replica-sets.md) per ulteriori informazioni.  

### <a name="azure-dns"></a>DNS di Azure

[DNS di Azure](https://azure.microsoft.com/support/legal/sla/dns/v1_1/) offre un contratto di servizio del 100%. HDInsight usa il servizio DNS di Azure in varie posizioni per la risoluzione dei nomi di dominio.

## <a name="multi-region-cost-and-complexity-optimizations"></a>Ottimizzazioni di costi e complessità tra più aree

Il miglioramento della continuità aziendale tramite il ripristino di emergenza tra aree a disponibilità elevata richiede progetti di architettura di maggiore complessità e costi più elevati. Le tabelle seguenti illustrano in dettaglio alcune aree tecniche che possono aumentare il costo totale di proprietà.

### <a name="cost-optimizations"></a>Ottimizzazioni dei costi

|Area|Motivo dell'escalation dei costi|Strategie di ottimizzazione|
|----|------------------------|-----------------------|
|Archiviazione dati|Duplicazione di dati/tabelle primari in un'area secondaria|Replicare solo i dati curati|
|Dati in uscita|I trasferimenti di dati in uscita tra aree sono a prezzo. Esaminare le linee guida sui prezzi|Replicare solo i dati curati per ridurre il footprint in uscita dell'area|
|Calcolo cluster|Cluster HDInsight aggiuntivi nell'area secondaria|Usare gli script automatici per distribuire il calcolo secondario dopo l'errore primario. Usare la scalabilità automatica per ridurre al minimo le dimensioni del cluster secondario. Usare SKU di VM più convenienti. Creare database secondari nelle aree in cui gli SKU delle VM possono essere scontati.|
|Authentication |Gli scenari multiutente nell'area secondaria comporteranno ulteriori configurazioni di Azure AD DS|Evitare le configurazioni multiutente nell'area secondaria.|

### <a name="complexity-optimizations"></a>Ottimizzazioni di complessità

|Area|Motivo dell'escalation della complessità|Strategie di ottimizzazione|
|----|------------------------|-----------------------|
|Modelli di lettura/scrittura |Richiesta di lettura e scrittura per la replica primaria e secondaria abilitata |Progettare il database secondario in modo che sia di sola lettura|
|RPO zero & RTO |Richiesta di perdita di dati pari a zero (RPO = 0) e zero tempo di inattività (RTO = 0) |Progettare RPO e RTO in modi per ridurre il numero di componenti di cui è necessario eseguire il failover.|
|Funzionalità di business |Richiesta di funzionalità aziendali complete del database primario nel database secondario |Valutare se è possibile eseguire con un subset minimo critico della funzionalità di business nel database secondario.|
|Connettività |Richiedere a tutti i sistemi upstream e downstream dal database primario di connettersi anche al database secondario|Limitare la connettività secondaria a un subset minimo essenziale.|

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sugli elementi trattati in questo articolo, vedere:

* [Architetture di continuità aziendale di Azure HDInsight](./hdinsight-business-continuity-architecture.md)
* [Architettura della soluzione a disponibilità elevata di Azure HDInsight case study](./hdinsight-high-availability-case-study.md)
* [Cosa sono Apache Hive e HiveQL in Azure HDInsight](./hadoop/hdinsight-use-hive.md)
