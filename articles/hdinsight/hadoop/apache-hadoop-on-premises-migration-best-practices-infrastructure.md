---
title: Eseguire la migrazione di cluster Apache Hadoop locali ad Azure HDInsight - Procedure consigliate per l'infrastruttura
description: Informazioni sulle procedure consigliate per l'infrastruttura relative alla migrazione di cluster Hadoop locali ad Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: 614fdae1865f008bdbc2cb8d5e8b96c0addcc112
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2018
ms.locfileid: "51036924"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---infrastructure-best-practices"></a>Eseguire la migrazione di cluster Apache Hadoop locali ad Azure HDInsight - Procedure consigliate per l'infrastruttura

Questo articolo offre indicazioni per la gestione dell'infrastruttura dei cluster Azure HDInsight. L'articolo fa parte di una serie di documenti che descrivono le procedure consigliate per facilitare la migrazione di sistemi Apache Hadoop locali ad Azure HDInsight.

## <a name="plan-well-for-the-capacity-needed-for-hdinsight-clusters"></a>Predisporre in modo adeguato la capacità necessaria per i cluster HDInsight

Le opzioni principali per la pianificazione della capacità dei cluster HDInsight sono:

- **Scegliere l'area** - L'area di Azure determina il luogo in cui viene fisicamente eseguito il provisioning del cluster. Per ridurre al minimo la latenza di lettura e scrittura, è opportuno che il cluster si trovi nella stessa area dei dati.
- **Scegliere dimensioni e posizione di archiviazione** -- L'archiviazione predefinita deve essere nella stessa area del cluster. Per un cluster a 48 nodi è consigliabile avere da 4 a 8 account di archiviazione. Anche se è possibile che lo spazio di archiviazione complessivo sia già sufficiente, ogni account di archiviazione fornisce larghezza di banda di rete aggiuntiva per i nodi di calcolo. In caso di più account di archiviazione, usare un nome casuale per ognuno di essi, senza prefisso. La denominazione casuale ha lo scopo di diminuire le probabilità di colli di bottiglia di archiviazione (limitazione) e di errori di modo comune negli account. Per prestazioni ottimali, usare solo un contenitore per ogni account archiviazione.
- **Scegliere tipo e dimensioni della VM (ora supportata la serie G)** - Ogni tipo di cluster contiene un set di tipi di nodo, a cui sono associate opzioni specifiche per il tipo e la dimensione della macchina virtuale. Il tipo e le dimensioni della macchina virtuale variano in base alla potenza di elaborazione della CPU, alle dimensioni della RAM e alla latenza di rete. È possibile usare un carico di lavoro simulato per determinare il tipo e le dimensioni ottimali della macchina virtuale per ogni tipo di nodo.
- **Scegliere il numero di nodi del ruolo di lavoro** - Il numero iniziale di nodi del ruolo di lavoro può essere determinato mediante i carichi di lavoro simulati. I cluster possono essere ridimensionati in un secondo momento aggiungendo più nodi del ruolo di lavoro per soddisfare le richieste di picchi di carico. Il cluster può essere nuovamente ridimensionato in un secondo momento quando non sono richiesti nodi del ruolo di lavoro aggiuntivi.

Per altre informazioni, vedere l'articolo [Pianificazione della capacità per cluster HDInsight](../hdinsight-capacity-planning.md)

## <a name="use-the-recommended-virtual-machine-types-for-cluster-nodes"></a>Usare i tipi di macchine virtuali consigliati per i nodi del cluster

Vedere [Configurazione del nodo predefinito e dimensioni della macchina virtuale per i cluster](../hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) per i tipi di macchine virtuali consigliati per ogni tipo di cluster HDInsight.

## <a name="check-the-availability-of-hadoop-components-in-hdinsight"></a>Verificare la disponibilità dei componenti Hadoop in HDInsight

Ogni versione di HDInsight è una distribuzione cloud di una versione di Hortonworks Data Platform (HDP) ed è costituita da un set di componenti dell'ecosistema Hadoop. Vedere l'articolo su [controllo delle versioni dei componenti HDInsight](../hdinsight-component-versioning.md) per informazioni dettagliate su tutti i componenti HDInsight e sulle versioni correnti.

È anche possibile usare l'interfaccia utente o l'API REST di Ambari per controllare i componenti di Hadoop e le versioni in HDInsight.

Le applicazioni o i componenti disponibili nei cluster locali, ma che non fanno parte dei cluster HDInsight, possono essere aggiunti in un nodo perimetrale o in una macchina virtuale nella stessa rete virtuale del cluster HDInsight. Un'applicazione Hadoop di terze parti non disponibile in Azure HDInsight può essere installata tramite l'opzione "Applicazioni" nel cluster HDInsight. Le applicazioni Hadoop personalizzate possono essere installate nel cluster HDInsight tramite "azioni script". La tabella seguente elenca alcune applicazioni comuni e le relative opzioni di integrazione di HDInsight:

|**Applicazione**|**Integrazione**
|---|---|
|Flusso d'aria|IaaS o nodo perimetrale HDInsight
|Alluxio|IaaS  
|Arcadia|IaaS 
|Atlas|Nessuna (solo HDP)
|Datameer|Nodo perimetrale HDInsight
|Datastax (Cassandra)|IaaS (CosmosDB come alternativa in Azure)
|DataTorrent|IaaS 
|Drill|IaaS 
|Ignite|IaaS
|Jethro|IaaS 
|Mapador|IaaS 
|Mongo|IaaS (CosmosDB come alternativa in Azure)
|NiFi|IaaS 
|Presto|IaaS o nodo perimetrale HDInsight
|Python 2|PaaS 
|Python 3|PaaS 
|R|PaaS 
|SAS|IaaS 
|Vertica|IaaS (SQLDW come alternativa in Azure)
|Tableau|IaaS 
|Waterline|Nodo perimetrale HDInsight
|StreamSets|Nodo HDInsight 
|Palantir|IaaS 
|Sailpoint|IaaS 

Per altre informazioni, vedere l'articolo [Componenti di Apache Hadoop disponibili con diverse versioni di HDInsight](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions)

## <a name="customize-hdinsight-clusters-using-script-actions"></a>Personalizzare i cluster HDInsight con azioni script

HDInsight offre un metodo di configurazione del cluster denominato **azione script**. Un'azione script è uno script Bash in esecuzione nei nodi di un cluster HDInsight e può essere usato per installare componenti aggiuntivi e modificare le impostazioni di configurazione.

Le azioni script devono essere archiviate in un URI accessibile dal cluster HDInsight. Possono essere usate durante o dopo la creazione del cluster e la loro esecuzione può essere limitata a determinati tipi di nodi.

Lo script può essere reso persistente o eseguito una sola volta. Gli script persistenti vengono usati per personalizzare nuovi nodi del ruolo di lavoro aggiunti al cluster tramite operazioni di ridimensionamento. Uno script persistente può anche applicare modifiche apportate a un altro tipo di nodo, ad esempio un nodo head, durante operazioni di ridimensionamento.

HDInsight include script predefiniti per installare i componenti seguenti nei cluster HDInsight:

- Aggiungere un account di archiviazione di Azure
- Installare Hue
- Installare Presto
- Installare Solr
- Installare Giraph
- Precaricare le librerie Hive
- Installare o aggiornare Mono

> [!Note]
> HDInsight non offre supporto diretto per i componenti Hadoop personalizzati o per i componenti installati tramite azioni script.

Le azioni script possono anche essere pubblicate in Azure Marketplace come applicazione HDInsight.

Per altre informazioni, vedere gli articoli seguenti:

- [Installare applicazioni Hadoop di terze parti in HDInsight](../hdinsight-apps-install-applications.md)
- [Personalizzare i cluster HDInsight con azioni script](../hdinsight-hadoop-customize-cluster-linux.md)
- [Pubblicare un'applicazione HDInsight in Azure Marketplace](../hdinsight-apps-publish-applications.md)

## <a name="customize-hdinsight-configs-using-bootstrap"></a>Personalizzare configurazioni HDInsight tramite Bootstrap

È possibile apportare modifiche alle configurazioni nel file di configurazione, ad esempio `core-site.xml`, `hive-site.xml` e `oozie-env.xml`, mediante Bootstrap. Di seguito è riportato uno script di esempio con PowerShell:

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90"}

$config = New—AzureRmHDInsightClusterConfig '
    | Set—AzureRmHDInsightDefaultStorage
    —StorageAccountName "$defaultStorageAccountName.blob. core.windows.net" `
    —StorageAccountKey "defaultStorageAccountKey " `
    | Add—AzureRmHDInsightConfigValues `
        —HiveSite $hiveConfigValues

New—AzureRmHDInsightCluster `
    —ResourceGroupName $existingResourceGroupName `
    —Cluster-Name $clusterName `
    —Location $location `
    —ClusterSizeInNodes $clusterSizeInNodes `
    —ClusterType Hadoop `
    —OSType Linux `
    —Version "3.6" `
    —HttpCredential $httpCredential `
    —Config $config
```

Per altre informazioni, vedere l'articolo [Personalizzare cluster HDInsight tramite Bootstrap](../hdinsight-hadoop-customize-cluster-bootstrap.md)

## <a name="use-edge-nodes-on-hadoop-clusters-in-hdinsight-to-access-the-client-tools"></a>Usare nodi perimetrali nei cluster Hadoop in HDInsight per accedere agli strumenti client

Un nodo perimetrale vuoto è una macchina virtuale Linux con gli stessi strumenti client installati e configurati nei nodi head, ma senza servizi Hadoop in esecuzione. Un nodo perimetrale può essere usato per gli scopi seguenti:

- Accesso al cluster
- Test delle applicazioni client
- Hosting delle applicazioni client

I nodi perimetrali possono essere creati ed eliminati tramite il portale di Azure e possono essere usati durante o dopo la creazione del cluster. Dopo aver creato un nodo perimetrale, sarà possibile connettersi al nodo stesso tramite SSH ed eseguire gli strumenti client per accedere al cluster Hadoop in HDInsight. L'endpoint SSH del nodo perimetrale è `<EdgeNodeName>.<ClusterName>-ssh.azurehdinsight.net:22`.

Per altre informazioni, vedere l'articolo [Usare i nodi perimetrali vuoti sui cluster Hadoop in HDInsight](../hdinsight-apps-use-edge-node.md)

## <a name="use-the-scale-up-and-scale-down-feature-of-clusters"></a>Usare la funzionalità di aumento e riduzione dei cluster

HDInsight offre elasticità permettendo di aumentare e ridurre il numero di nodi di lavoro nei cluster. Questa funzionalità consente di ridurre un cluster nelle ore di chiusura o nei fine settimana ed espanderlo durante i picchi delle richieste aziendali.

Il ridimensionamento del cluster può essere automatizzato usando i metodi seguenti:

### <a name="powershell-cmdlet"></a>Cmdlet di PowerShell

```powershell
Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```powershell
azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>
```

### <a name="azure-portal"></a>Portale di Azure

Quando si aggiungono nodi al cluster HDInsight in esecuzione, questa operazione non ha alcun impatto sui processi in sospeso o in esecuzione. È possibile inviare nuovi processi in tutta sicurezza durante l'esecuzione del processo di ridimensionamento. Se le operazioni di ridimensionamento non riescono per qualche motivo, l'errore viene gestito normalmente, lasciando il cluster in uno stato funzionale.

Se tuttavia il cluster viene ridotto tramite la rimozione di nodi, eventuali processi in sospeso o in esecuzione avranno esito negativo al termine dell'operazione di ridimensionamento. L'errore è dovuto al riavvio di alcuni servizi durante il processo. Per risolvere questo problema, è possibile attendere il completamento dei processi prima di ridurre il cluster, terminare manualmente i processi o inviare di nuovo i processi al termine dell'operazione di ridimensionamento.

Se si riduce il cluster al numero minimo di un nodo del ruolo di lavoro, Hadoop Distributed File System può restare bloccato in modalità sicura quando i nodi dei ruoli di lavoro vengono riavviati a causa dell'applicazione di patch oppure immediatamente dopo l'operazione di ridimensionamento. È possibile eseguire il comando seguente per disattivare la modalità sicura per Hadoop Distributed File System:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

Dopo aver disattivato la modalità sicura, sarà possibile rimuovere manualmente i file temporanei o attenderne la pulizia automatica mediante Hive.

Per altre informazioni, vedere l'articolo [Ridimensionare i cluster HDInsight](../hdinsight-scaling-best-practices.md)

## <a name="use-hdinsight-with-azure-virtual-network"></a>Usare HDInsight con Rete virtuale di Azure

Le reti virtuali di Azure consentono alle risorse di Azure, ad esempio Macchine virtuali di Azure, di comunicare in modo sicuro tra loro, con Internet e con le reti locali, filtrando e instradando il traffico della rete.

Rete virtuale di Azure con HDInsight consente di implementare gli scenari seguenti:

- Connessione a HDInsight direttamente da una rete locale.
- Connessione di HDInsight ad archivi dati in una rete virtuale di Azure.
- Accesso diretto ai servizi Hadoop che non sono disponibili pubblicamente su Internet, ad esempio le API Kafka o l'API Java HBase.

HDInsight può essere aggiunto a una rete virtuale di Azure nuova o esistente. Se HDInsight viene aggiunto a una rete virtuale esistente, i gruppi di sicurezza di rete esistenti e le route definite dall'utente devono essere aggiornati per consentire l'accesso senza restrizioni a [vari indirizzi IP](../hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip-1) nel data center di Azure. Verificare di non bloccare il traffico verso le [porte](../hdinsight-extend-hadoop-virtual-network.md#hdinsight-ports) usate dai servizi di HDInsight.

> [!Note]
> HDInsight non supporta al momento il tunneling forzato. Il tunneling forzato è un'impostazione di subnet che spinge il traffico Internet in uscita verso un dispositivo per l'ispezione e la registrazione. Occorre quindi rimuovere questa funzionalità prima di installare HDInsight in una subnet oppure si può creare una nuova subnet per HDInsight. HDInsight non supporta la limitazione della connettività di rete in uscita.

Per altre informazioni, vedere gli articoli seguenti:

- [Panoramica di Rete virtuale di Azure](../../virtual-network/virtual-networks-overview.md)
- [Estendere Azure HDInsight usando Rete virtuale di Azure](../hdinsight-extend-hadoop-virtual-network.md)

## <a name="use-azure-virtual-network-service-endpoints-to-securely-connect-to-other-azure-services"></a>Usare gli endpoint del servizio Rete virtuale di Azure per la connessione sicura ad altri servizi di Azure

HDInsight supporta gli [endpoint del servizio Rete virtuale](../../virtual-network/virtual-network-service-endpoints-overview.md) che consentono di connettersi in modo sicuro ad Archiviazione BLOB di Azure, Azure Data Lake Storage Gen2, Cosmos DB e ai database SQL. Abilitando un endpoint del servizio per Azure HDInsight, il traffico attraversa una route protetta all'interno del data center di Azure. Con questo livello avanzato di sicurezza a livello di rete, è possibile bloccare gli account di archiviazione di Big Data nelle reti virtuali specificate e continuare a usare i cluster HDInsight per accedere ai dati ed elaborarli.

Per altre informazioni, vedere gli articoli seguenti:

- [Endpoint del servizio di rete virtuale](../../virtual-network/virtual-network-service-endpoints-overview.md)
- [Enhance HDInsight security with service endpoints](https://azure.microsoft.com/blog/enhance-hdinsight-security-with-service-endpoints/.md) (Ottimizzare la sicurezza di HDInsight con gli endpoint del servizio)

## <a name="connect-hdinsight-to-the-on-premises-network"></a>Connettere HDInsight alla rete locale

È possibile connettere HDInsight alla rete locale usando Rete virtuale di Azure e un gateway VPN. La procedura seguente consente di stabilire la connettività:

- Usare HDInsight in una Rete virtuale di Azure con connettività alla rete locale.
- Configurare la risoluzione dei nomi DNS tra la rete virtuale e la rete locale.
- Configurare gruppi di sicurezza di rete o route definite dall'utente per controllare il traffico di rete.

Per altre informazioni, vedere l'articolo [Connettere HDInsight alla rete locale](../connect-on-premises-network.md)

## <a name="next-steps"></a>Passaggi successivi

Leggere l'articolo successivo di questa serie:

- [Storage best practices for on-premises to Azure HDInsight Hadoop migration](apache-hadoop-on-premises-migration-best-practices-storage.md) (Procedure consigliate per lo storage relative alla migrazione da locale ad Azure HDInsight Hadoop)