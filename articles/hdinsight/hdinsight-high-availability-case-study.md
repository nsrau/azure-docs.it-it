---
title: Architettura della soluzione a disponibilità elevata di Azure HDInsight case study
description: Questo articolo è un case study fittizio di un'architettura di soluzione a disponibilità elevata di Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
keywords: alta disponibilità di hadoop
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: a77dba165d76cc131f7a2a25a4b2f62e945a3089
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91843901"
---
# <a name="azure-hdinsight-highly-available-solution-architecture-case-study"></a>Architettura della soluzione a disponibilità elevata di Azure HDInsight case study

I meccanismi di replica di Azure HDInsight possono essere integrati in un'architettura di soluzione a disponibilità elevata. In questo articolo viene usato un case study fittizio per contoso retail per spiegare i possibili approcci per il ripristino di emergenza a disponibilità elevata, le considerazioni sui costi e le progettazioni corrispondenti.

Le raccomandazioni per il ripristino di emergenza a disponibilità elevata possono avere molte permutazioni e combinazioni. Queste soluzioni devono arrivare dopo aver deliberato i vantaggi e gli svantaggi di ogni opzione. Questo articolo illustra solo una possibile soluzione.

## <a name="customer-architecture"></a>Architettura del cliente

Nell'immagine seguente viene illustrata l'architettura primaria di Contoso retail. L'architettura è costituita da un carico di lavoro di streaming, da un carico di lavoro batch, dal livello di utilizzo, dal livello di utilizzo, dal livello di archiviazione e

:::image type="content" source="./media/hdinsight-high-availability-case-study/contoso-architecture.png" alt-text="Architettura di Contoso Retail":::

### <a name="streaming-workload"></a>Carico di lavoro di streaming

Dispositivi e sensori producono dati in HDInsight Kafka, che costituisce il Framework di messaggistica. HDInsight An di consumer Spark legge dagli argomenti Kafka. Spark trasforma i messaggi in ingresso e li scrive in un cluster HBase di HDInsight sul livello di servizio.

### <a name="batch-workload"></a>Carico di lavoro batch

HDInsight An cluster Hadoop che esegue hive e MapReduce inserisce dati da sistemi transazionali locali. I dati non elaborati trasformati da hive e MapReduce vengono archiviati nelle tabelle hive in una partizione logica del data Lake supportato da Azure Data Lake Storage Gen2. I dati archiviati nelle tabelle hive vengono resi disponibili anche per Spark SQL, che esegue trasformazioni batch prima di archiviare i dati curati in HBase per servire.

### <a name="serving-layer"></a>Livello di servizio

HDInsight An cluster HBase con Apache Phoenix viene usato per gestire i dati per le applicazioni Web e i dashboard di visualizzazione. HDInsight An cluster LLAP viene usato per soddisfare i requisiti di Reporting interni.

### <a name="consumption-layer"></a>Livello di utilizzo

Un livello di gestione API e app per le API di Azure torna a una pagina Web pubblica. I requisiti per la creazione di report interni sono soddisfatti dal Power BI.

### <a name="storage-layer"></a>Livello di archiviazione

Il Azure Data Lake Storage Gen2 partizionato logicamente viene usato come Enterprise Data Lake. I Metastore HDInsight sono supportati dal database SQL di Azure.

### <a name="version-control-system"></a>Sistema di controllo della versione

Sistema di controllo della versione integrato in un Azure Pipelines e ospitato all'esterno di Azure.

## <a name="customer-business-continuity-requirements"></a>Requisiti di continuità aziendale dei clienti

È importante determinare la funzionalità di business minima necessaria in caso di emergenza.

### <a name="contoso-retails-business-continuity-requirements"></a>Requisiti di continuità aziendale di Contoso Retail

* È necessario essere protetti da un errore a livello di area o da un problema di integrità del servizio regionale.
* I clienti non devono mai visualizzare un errore 404. Il contenuto pubblico deve sempre essere servito. (RTO = 0)  
* Per la maggior parte dell'anno, è possibile visualizzare il contenuto pubblico non aggiornato di 5 ore. (RPO = 5 ore)
* Durante la stagione festiva, il contenuto pubblico deve essere sempre aggiornato. (RPO = 0)
* I requisiti per la creazione di report interni non sono considerati essenziali per la continuità aziendale.
* Ottimizzare i costi di continuità aziendale.

## <a name="proposed-solution"></a>Soluzione proposta

La figura seguente illustra l'architettura di ripristino di emergenza a disponibilità elevata di Contoso retail.

:::image type="content" source="./media/hdinsight-high-availability-case-study/contoso-solution.png" alt-text="Architettura di Contoso Retail":::

**Kafka** usa la replica [attiva-passiva](hdinsight-business-continuity-architecture.md#apache-kafka) per eseguire il mirroring degli argomenti Kafka dall'area primaria all'area secondaria. Un'alternativa alla replica Kafka potrebbe essere la produzione a Kafka in entrambe le aree.

**Hive e Spark** usano i modelli [di replica secondaria attiva, su richiesta,](hdinsight-business-continuity-architecture.md#apache-spark) durante i periodi normali. Il processo di replica hive viene eseguito periodicamente e accompagna la replica dell'account di archiviazione hive e del Metastore hive di Azure SQL. L'account di archiviazione Spark viene periodicamente replicato tramite DistCP di ADF. La natura temporanea di questi cluster consente di ottimizzare i costi. Le repliche vengono pianificate ogni 4 ore per giungere a un RPO entro il requisito di cinque ore.

La replica di **HBase** usa il modello [leader – follower](hdinsight-business-continuity-architecture.md#apache-hbase) durante i periodi normali per garantire che i dati vengano sempre serviti indipendentemente dall'area e che RPO sia zero.

Se si verifica un errore a livello di area nell'area primaria, la pagina Web e il contenuto back-end vengono serviti dall'area secondaria per 5 ore con un certo grado di obsolescenza. Se il dashboard di integrità dei servizi di Azure non indica un'ETA di ripristino nella finestra di cinque ore, contoso Retail creerà il livello di trasformazione hive e Spark nell'area secondaria, quindi punterà tutte le origini dati upstream nell'area secondaria. Rendere scrivibile l'area secondaria potrebbe causare un processo di failback che prevede la replica nel database primario.

Durante una stagione di picco, l'intera pipeline secondaria è sempre attiva e in esecuzione. I produttori Kafka producono in entrambe le aree e la replica HBase viene modificata da Leader-Follower a Leader-Leader per garantire che il contenuto pubblico sia sempre aggiornato.

Non è necessario progettare alcuna soluzione di failover per la creazione di report interni poiché non è fondamentale per la continuità aziendale.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sugli elementi trattati in questo articolo, vedere:

* [Riferimento REST Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Installare e configurare l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)
* [Installare e configurare Azure PowerShell modulo AZ](/powershell/azure/)
* [Gestire HDInsight tramite Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [provisioning di cluster HDInsight basati su Linux](hdinsight-hadoop-provision-linux-clusters.md)