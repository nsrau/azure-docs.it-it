---
title: Configurare la replica di HBase | Documentazione Microsoft
description: "Informazioni su come configurare la replica di HBase per bilanciamento del carico, disponibilità elevata, migrazione/aggiornamento senza tempi di inattività da una versione di HDInsight a un&quot;altra, e ripristino di emergenza."
services: hdinsight,virtual-network
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/22/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: a62cd616f0abd59c83c834bf13b4ba8549a9c73e
ms.openlocfilehash: 83aabe0e5161cd3f94caa996dec29bc020e5308b
ms.lasthandoff: 02/23/2017


---
# <a name="configure-hbase-replication"></a>Configurare la replica di HBase

Informazioni su come configurare la replica di HBase in una rete virtuale (VNet) o tra due reti virtuali.

La replica di cluster usa una metodologia con push dell'origine. Un cluster HBase può essere un'origine o una destinazione o può soddisfare entrambi i ruoli in una sola volta. La replica è asincrona e l'obiettivo della replica è la coerenza finale. Quando l'origine riceve una modifica a una famiglia di colonne con la replica abilitata, tale modifica viene propagata a tutti i cluster di destinazione. Quando i dati vengono replicati da un cluster a un altro, viene tenuta traccia del cluster di origine e di tutti i cluster che hanno già usato i dati per evitare i cicli di replica.

In questa esercitazione si configurerà una replica origine-destinazione. Per altre topologie di cluster, vedere la [guida di riferimento relativa a Apache HBase](http://hbase.apache.org/book.html#_cluster_replication).

Casi di utilizzo della replica di HBase per una singola rete virtuale:

* Bilanciamento del carico, ad esempio esecuzione di analisi o processi MapReduce nel cluster di destinazione e inserimento di dati nel cluster di origine
* Disponibilità elevata
* Migrazione di dati da un cluster HBase a un altro
* Aggiornamento di un cluster Azure HDInsight da una versione a un'altra

Casi di utilizzo della replica di HBase per due reti virtuali:

* Ripristino di emergenza
* Bilanciamento del carico e partizionamento dell'applicazione
* Disponibilità elevata

È possibile replicare i cluster tramite gli script [Azione script](hdinsight-hadoop-customize-cluster-linux.md) disponibili in [GitHub](https://github.com/Azure/hbase-utils/tree/master/replication).

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione, è necessario disporre di un abbonamento ad Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="configure-the-environments"></a>Configurare gli ambienti

Sono disponibili tre opzioni di configurazione:

- Due cluster HBase in una rete virtuale di Azure
- Due cluster HBase in due reti virtuali differenti nella stessa area
- Due cluster HBase in due reti virtuali differenti in due aree differenti (replica geografica)

Per semplificare la configurazione degli ambienti, sono disponibili alcuni [modelli di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Se si preferisce configurare gli ambienti con altri metodi, vedere:

- [Creare cluster Hadoop basati su Linux in HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Creare cluster HBase nella rete virtuale di Azure](hdinsight-hbase-provision-vnet.md)

### <a name="configure-one-virtual-network"></a>Configurare una rete virtuale

Fare clic sull'immagine seguente per creare due cluster HBase nella stessa rete virtuale. Il modello è archiviato tra i [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-one-vnet/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-one-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

### <a name="configure-two-virtual-networks-in-the-same-region"></a>Configurare due reti virtuali nella stessa area

Fare clic sull'immagine seguente per creare due reti virtuali con peering reti virtuali e due cluster HBase nella stessa area. Il modello è archiviato tra i [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-two-vnets-same-region/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-two-vnets-same-region%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>



Questo scenario richiede il [Peering reti virtuali](../virtual-network/virtual-network-peering-overview.md). Il modello abilita il peering reti virtuali.   

La replica di HBase usa gli indirizzi IP delle macchine virtuali ZooKeeper. È necessario configurare indirizzi IP statici per i nodi ZooKeeper HBase di destinazione.

**Per configurare indirizzi IP statici**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere **Gruppi di risorse** dal menu a sinistra.
3. Fare clic sul gruppo di risorse che contiene il cluster HBase di destinazione. Si tratta del gruppo di risorse specificato quando si è usato il modello di Resource Manager per creare l'ambiente. È possibile usare il filtro per restringere l'elenco. Viene visualizzato un elenco di risorse che contiene le due reti virtuali.
4. Fare clic sulla rete virtuale che contiene il cluster HBase di destinazione. Ad esempio, fare clic su **xxxx-vnet2**. Vengono visualizzati tre dispositivi i cui nomi iniziano con **nic-zookeepermode -**. Questi dispositivi sono le tre macchine virtuali ZooKeeper.
5. Fare clic su una delle macchine virtuali ZooKeeper.
6. Fare clic su **Configurazioni IP**.
7. Nell'elenco fare clic su **ipConfig1**.
8. Fare clic su **Statico**e annotare l'indirizzo IP effettivo. L'indirizzo IP sarà richiesto quando si esegue l'azione script per abilitare la replica.

  ![Replica di HBase in HDInsight - Indirizzo IP statico di ZooKeeper](./media/hdinsight-hbase-replication/hdinsight-hbase-replication-zookeeper-static-ip.png)

9. Ripetere il passaggio 6 per impostare l'indirizzo IP statico per gli altri due nodi ZooKeeper.

Per lo scenario tra più reti virtuali, è necessario usare lo switch **-ip** quando si esegue la chiamata all'azione di script **hdi_enable_replication.sh**.

### <a name="configure-two-virtual-networks-in-two-different-regions"></a>Configurare due reti virtuali in due aree differenti

Fare clic sull'immagine seguente per creare due reti virtuali in due aree differenti. Il modello è disponibile in un contenitore BLOB di Azure pubblico.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhbaseha%2Fdeploy-hbase-geo-replication.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

Creare un gateway VPN tra le due reti virtuali. Per istruzioni, vedere [Creare una rete virtuale con una connessione da sito a sito](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

La replica di HBase usa gli indirizzi IP delle macchine virtuali ZooKeeper. È necessario configurare indirizzi IP statici per i nodi ZooKeeper HBase di destinazione. Per configurare l'indirizzo IP statico, vedere la sezione "Configurare due reti virtuali nella stessa area" in questo articolo.

Per lo scenario tra più reti virtuali, è necessario usare lo switch **-ip** quando si esegue la chiamata all'azione di script **hdi_enable_replication.sh**.

## <a name="load-test-data"></a>Dati del test di carico

Quando si esegue la replica di un cluster, è necessario specificare le tabelle da replicare. In questa sezione, alcuni dati verranno caricati nel cluster di origine. Nella sezione successiva viene abilitata la replica tra i due cluster.

Seguire le istruzioni in [Esercitazione su HBase: Introduzione all'uso di Apache HBase con Hadoop basato su Linux in HDInsight](hdinsight-hbase-tutorial-get-started-linux.md) per creare una tabella **Contatti** inserire alcuni dati nella tabella.

## <a name="enable-replication"></a>Abilitare la replica

La procedura seguente illustra come richiamare l'azione script dal portale di Azure. Per l'esecuzione di un'azione script tramite l'interfaccia della riga di comando di Azure e Azure PowerShell, vedere [Personalizzare cluster HDInsight basati su Linux tramite Azione script](hdinsight-hadoop-customize-cluster-linux.md).

**Per abilitare la replica di HBase dal portale di Azure**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Aprire il cluster HBase di origine.
3. Fare clic su **Azioni script** dal menu del cluster.
4. Fare clic su **Inviare nuova** nella parte superiore del pannello.
5. Selezionare o immettere le seguenti informazioni:

  - **Nome**: immettere **Abilitazione replica**.
  - **URL dello script Bash**: immettere **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh**.
  - **Intestazione**: Selezionata. Deselezionare gli altri tipi di nodo.
  - **Parametri**: i parametri di esempio seguenti abilitano la replica per tutte le tabelle esistenti e copiano tutti i dati dal cluster di origine al cluster di destinazione:

            -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata

6. Fare clic su **Crea**. Lo script può richiedere del tempo, soprattutto quando si utilizza l'argomento copydata.

Argomenti obbligatori:

|Nome|Descrizione|
|----|-----------|
|-s, --src-cluster | Specificare il nome DNS del cluster HBase di origine. Ad esempio: -s hbsrccluster, --src-cluster=hbsrccluster |
|-d, - dst-cluster | Specificare il nome DNS del cluster HBase di destinazione (replica). Ad esempio: -s dsthbcluster, --src-cluster=dsthbcluster |
|-sp, --src-ambari-password | Specificare la password amministratore per Ambari nel cluster HBase di origine. |
|-dp, --dst-ambari-password | Specificare la password amministratore per Ambari nel cluster HBase di destinazione.|

Argomenti facoltativi:

|Nome|Descrizione|
|----|-----------|
|-su, --src-ambari-user | Specificare il nome utente amministratore per Ambari nel cluster HBase di origine. Il valore predefinito è **admin**. |
|-du, --dst-ambari-user | Specificare il nome utente amministratore per Ambari nel cluster HBase di destinazione. Il valore predefinito è **admin**. |
|-t, --table-list | Specificare le tabelle da replicare. Ad esempio: --table-list="table1;table2;table3". Se non si specificano tabelle, vengono replicate tutte le tabelle HBase esistenti.|
|-m, --machine | Specificare il nodo head in cui verrà eseguita l'azione script. Il valore è hn1 o hn0. Poiché hn0 è usato con maggiore frequenza, è consigliabile usare hn1. Usare questa opzione quando si esegue lo script $0 come azione script dal portale di HDInsight o da Azure PowerShell.|
|-ip | Questo argomento è obbligatorio quando si vuole abilitare la replica tra due reti virtuali. L'argomento funge da opzione per usare gli indirizzi IP statici dei nodi di ZooKeeper dai cluster di replica anziché nomi di dominio completi. Gli indirizzi IP statici devono essere preconfigurati prima di abilitare la replica. |
|-cp, -copydata | Abilita la migrazione dei dati esistenti nelle tabelle in cui è abilitata la replica. |
|-rpm, -replicate-phoenix-meta | Abilita la replica sulle tabelle di sistema Phoenix. <br><br>*Questa opzione deve essere usata con attenzione.* È consigliabile ricreare le tabelle di Phoenix nei cluster di replica prima di usare questo script. |
|-h, --help | Visualizza le informazioni sull'utilizzo. |

La sezione print_usage() dello [script](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) contiene una spiegazione dettagliata dei parametri.

Dopo aver distribuito correttamente l'azione script, è possibile usare SSH per connettersi al cluster HBase di destinazione e verificare che i dati siano stati replicati.

### <a name="replication-scenarios"></a>Scenari di replica

L'elenco seguente illustra alcuni casi di utilizzo generale e le relative impostazioni dei parametri:

- **Abilitare la replica su tutte le tabelle tra i due cluster**. Questo scenario non richiede la copia e la migrazione dei dati esistenti nelle tabelle e non usa le tabelle di Phoenix. È possibile usare i parametri seguenti:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **Abilitare la replica su tabelle specifiche**. Usare i parametri seguenti per abilitare la replica su table1, table2 e table3:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **Abilitare la replica su tabelle specifiche e copiare i dati esistenti**. Usare i parametri seguenti per abilitare la replica su table1, table2 e table3:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **Abilitare la replica in tutte le tabelle con la replica dei metadati di Phoenix dall'origine alla destinazione**. La replica dei metadati di Phoenix non è perfetta e deve essere usata con cautela.

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>Copia e migrazione dei dati

Sono disponibili due script azione script distinti per la copia o la migrazione dei dati dopo aver abilitato la replica:

- [Script per tabelle di piccole dimensioni](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (pochi gigabyte di dimensione, l'esecuzione della copia completa richiede meno di un'ora)

- [Script per tabelle di grandi dimensioni](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (l'esecuzione della copia può richiedere più di un'ora)

È possibile eseguire la stessa procedura adottata per [abilitare la replica](#enable-replication) per richiamare l'azione script con i parametri seguenti:

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

La sezione print_usage() dello [script](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) contiene una descrizione dettagliata dei parametri.

### <a name="scenarios"></a>Scenari

- **Copiare tabelle specifiche (test1, test2 e test3) per tutte le righe modificate finora (timestamp corrente)**:

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  oppure

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **Copiare tabelle specifiche con un intervallo di tempo specificato**:

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>Disabilitare la replica

Per disabilitare la replica usare un altro script Azione script disponibile in [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh). È possibile eseguire la stessa procedura adottata per [abilitare la replica](#enable-replication) per richiamare l'azione script con i parametri seguenti:

    -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari Password> <-all|-t "table1;table2;...">  

La sezione print_usage() dello [script](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) contiene una spiegazione dettagliata dei parametri.

### <a name="scenarios"></a>Scenari

- **Disabilitare la replica in tutte le tabelle**:

        -m hn1 -s <source cluster DNS name> -sp Mypassword\!789 -all
  oppure

        --src-cluster=<source cluster DNS name> --dst-cluster=<destination cluster DNS name> --src-ambari-user=<source cluster Ambari username> --src-ambari-password=<source cluster Ambari password>

- **Disabilitare la replica in tabelle specifiche (table1, table2 e table3)**:

        -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> -t "table1;table2;table3"

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come configurare la replica di HBase in due data center. Per altre informazioni su HDInsight e HBase, vedere:

* [Introduzione ad Apache HBase in HDInsight][hdinsight-hbase-get-started]
* [Panoramica su HBase di HDInsight][hdinsight-hbase-overview]
* [Creare cluster HBase nella rete virtuale di Azure][hdinsight-hbase-provision-vnet]
* [Analisi dei sentimenti Twitter in tempo reale con HBase][hdinsight-hbase-twitter-sentiment]
* [Analisi dei dati dei sensori con Storm e HBase in HDInsight (Hadoop)][hdinsight-sensor-data]

[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[hdinsight-hbase-geo-replication-dns]: ../hdinsight-hbase-geo-replication-configure-VNet.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication/HDInsight.HBase.Replication.Network.diagram.png

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started-linux.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[hdinsight-sensor-data]: hdinsight-storm-sensor-data-analysis.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md

