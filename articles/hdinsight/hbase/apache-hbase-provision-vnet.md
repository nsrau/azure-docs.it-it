---
title: Creare cluster HBase in una rete virtuale - Azure
description: Introduzione all'uso di HBase in Azure HDInsight. Informazioni su come creare cluster HBase di HDInsight in Rete virtuale di Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/23/2019
ms.openlocfilehash: 82e3374491aa119d9985ea7ef31e180c920511d3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86087742"
---
# <a name="create-apache-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Creare cluster Apache HBase su HDInsight nella rete virtuale di Azure

Informazioni su come creare cluster Apache HBase in Azure HDInsight in una [Rete virtuale di Azure](https://azure.microsoft.com/services/virtual-network/).

Grazie all'integrazione con la rete virtuale, i cluster Apache HBase possono essere distribuiti nella stessa rete virtuale delle applicazioni, consentendo così alle applicazioni di comunicare direttamente con HBase. Questo approccio offre i vantaggi seguenti:

* Connettività diretta dell'applicazione Web con i nodi del cluster HBase, che consente le comunicazioni tramite le API RPC (Remote Procedure Call) Java di HBase.
* Miglioramento delle prestazioni, poiché il traffico non deve attraversare più gateway e servizi di bilanciamento del carico.
* Possibilità di elaborare le informazioni sensibili in modo più sicuro, senza esporre un endpoint pubblico.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-apache-hbase-cluster-into-virtual-network"></a>Creare cluster Apache HBase nella rete virtuale

In questa sezione viene creato un cluster Apache HBase basato su Linux con l'account di Archiviazione di Azure dipendente in una rete virtuale di Azure tramite un [modello di Azure Resource Manager](../../azure-resource-manager/templates/deploy-powershell.md). Per altri metodi di creazione di cluster e per informazioni sulle impostazioni, vedere l'articolo sulla [creazione di cluster HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Per altre informazioni sull'uso di un modello per creare cluster Apache Hadoop in HDInsight, vedere [Creare cluster Apache Hadoop in HDInsight tramite modelli di Azure Resource Manager](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)

> [!NOTE]  
> Alcune proprietà sono state impostate come hardcoded nel modello. Ad esempio:
>
> * **Location**: Stati Uniti orientali 2
> * **Versione cluster**: 3.6
> * **Cluster worker node count**: 2
> * **Default storage account**: stringa univoca
> * **Nome rete virtuale**: clustername-VNET
> * **Spazio di indirizzi della rete virtuale**: 10.0.0.0/16
> * **Nome subnet**: Subnet1
> * **Intervallo di indirizzi subnet**: 10.0.0.0/24
>
> `CLUSTERNAME` viene sostituito con il nome del cluster fornito quando si usa il modello.

1. Selezionare l'immagine seguente per aprire il modello nel portale di Azure. Il modello è disponibile in [Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Nella finestra di dialogo **distribuzione personalizzata** selezionare **modifica modello**.

1. Alla riga 165, modificare il valore `Standard_A3` in `Standard_A4_V2` . Selezionare quindi **Salva**.

1. Completare il modello rimanente con le informazioni seguenti:

    |Proprietà |Valore |
    |---|---|
    |Subscription|selezionare una sottoscrizione di Azure usata per creare il cluster HDInsight, l'account di archiviazione dipendente e la rete virtuale di Azure.|
    Resource group|selezionare **Crea nuovo** e specificare un nuovo nome al gruppo di risorse.|
    |Location|Selezionare una posizione per il gruppo di risorse.|
    |Cluster Name|immettere un nome per il cluster Hadoop da creare.|
    |Nome utente e password di accesso al cluster|Il nome utente predefinito è **admin**. Specificare una password.|
    |Nome utente e password ssh|Il nome utente predefinito è **sshuser**.  Specificare una password.|

    Selezionare **Accetto i termini e le condizioni indicati in precedenza**.

1. Selezionare **Acquisto**. La creazione di un cluster richiede circa 20 minuti. Una volta creato il cluster, è possibile selezionare il cluster nel portale per aprirlo.

Al termine dell'articolo, potrebbe essere necessario eliminare il cluster. Con HDInsight, i dati vengono archiviati in Archiviazione di Azure ed è possibile eliminare tranquillamente un cluster quando non viene usato. Vengono addebitati i costi anche per i cluster HDInsight che non sono in uso. Poiché i costi per il cluster sono decisamente superiori a quelli per l'archiviazione, economicamente ha senso eliminare i cluster quando non vengono usati. Per istruzioni sull'eliminazione di un cluster, vedere [Gestire cluster Apache Hadoop in HDInsight tramite il portale di Azure](../hdinsight-administer-use-portal-linux.md#delete-clusters).

Per iniziare a lavorare con il nuovo cluster HBase, è possibile usare le procedure disponibili in [Introduzione all'uso di Apache HBase con Apache Hadoop in HDInsight](./apache-hbase-tutorial-get-started-linux.md).

## <a name="connect-to-the-apache-hbase-cluster-using-apache-hbase-java-rpc-apis"></a>Connettersi al cluster Apache HBase tramite le API RPC Java di Apache HBase

### <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Creare una macchina virtuale IaaS (Infrastructure as a Service) nella stessa rete virtuale di Azure e nella stessa subnet. Per le istruzioni su come creare una nuova macchina virtuale IaaS, vedere [Creare una macchina virtuale che esegue Windows Server](../../virtual-machines/windows/quick-create-portal.md). Quando si usa la procedura indicata in questo documento, è necessario inserire i valori seguenti per la configurazione di rete:

* **Rete virtuale**: clustername-VNET
* **Subnet**: Subnet1

> [!IMPORTANT]  
> Sostituire `CLUSTERNAME` con il nome usato durante la creazione del cluster HDInsight nei passaggi precedenti.

Applicando questi valori, la macchina virtuale viene configurata nella stessa rete virtuale e subnet del cluster HDInsight. Ciò consente la comunicazione bidirezionale diretta. È possibile creare un cluster HDInsight con un nodo perimetrale vuoto. Il nodo perimetrale può essere usato per gestire il cluster.  Per altre informazioni, vedere [Usare nodi perimetrali vuoti in HDInsight](../hdinsight-apps-use-edge-node.md).

### <a name="obtain-fully-qualified-domain-name"></a>Ottenere il nome di dominio completo

Quando si usa un'applicazione Java per connettersi a HBase da remoto, è necessario usare il nome di dominio completo (FQDN). Per determinare quest'ultimo, è necessario ottenere il suffisso DNS specifico della connessione del cluster HBase. A questo scopo, è possibile usare uno dei metodi seguenti:

* Usare un Web browser per effettuare una chiamata [Apache Ambari](https://ambari.apache.org/):

    Passare a `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts?minimal_response=true`. Restituisce un file JSON con i suffissi DNS.

* Usare il sito Web Ambari:

    1. Passare a `https://CLUSTERNAME.azurehdinsight.net`.
    2. Selezionare **host** dal menu in alto.

* Usare Curl per effettuare chiamate REST:

    ```bash
    curl -u <username>:<password> -k https://CLUSTERNAME.azurehdinsight.net/ambari/api/v1/clusters/CLUSTERNAME.azurehdinsight.net/services/hbase/components/hbrest
    ```

Nei dati JSON (JavaScript Object Notation) restituiti, trovare la voce "host_name". Contiene il nome di dominio completo (FQDN) per i nodi nel cluster. Ad esempio:

```
"host_name" : "hn0-hbaseg.hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net"
```

La porzione del nome di dominio che inizia con il nome del cluster è il suffisso DNS. Ad esempio: `hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net`.

<!--
3.    Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix](./media/apache-hbase-provision-vnet/hdi-primary-dns-suffix.png)
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

### <a name="verify-communication-inside-virtual-network"></a>Verificare la comunicazione all'interno della rete virtuale

Per verificare che la macchina virtuale possa comunicare con il cluster HBase, usare il comando `ping headnode0.<dns suffix>` dalla macchina virtuale. Ad esempio: `ping hn0-hbaseg.hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net`.

Per usare queste informazioni in un'applicazione Java e creare un'applicazione, è possibile seguire i passaggi in [Usare Apache Maven per compilare applicazioni Java che usano Apache HBase con HDInsight (Hadoop)](./apache-hbase-build-java-maven-linux.md). Per fare in modo che l'applicazione si connetta a un server HBase remoto, modificare il file **hbase-site.xml** in questo esempio, in modo che usi il nome di dominio completo (FQDN) per Zookeeper. Ad esempio:

```xml
<property>
    <name>hbase.zookeeper.quorum</name>
    <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
</property>
```

> [!NOTE]  
> Per altre informazioni sulla risoluzione dei nomi in reti virtuali di Azure, comprese quelle relative all'uso del proprio server DNS, vedere [Risoluzione dei nomi (DNS)](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come creare un cluster Apache HBase. Per altre informazioni, vedere:

* [Introduzione a HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Usare nodi perimetrali vuoti in HDInsight](../hdinsight-apps-use-edge-node.md)
* [Configurare la replica geografica di Apache HBase in HDInsight](apache-hbase-replication.md)
* [Creare cluster Apache Hadoop in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Introduzione all'uso di Apache HBase con Apache Hadoop in HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Panoramica di Rete virtuale.](../../virtual-network/virtual-networks-overview.md)
