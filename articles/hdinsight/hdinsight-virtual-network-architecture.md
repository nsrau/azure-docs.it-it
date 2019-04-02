---
title: Architettura di rete virtuale di Azure HDInsight
description: Scopri le risorse disponibili quando si crea un cluster HDInsight in una rete virtuale di Azure.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: hrasheed
ms.openlocfilehash: 6d92273298c0448d7377acab6f3b8ea1cc1ed908
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762923"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Architettura di rete virtuale di Azure HDInsight

Questo articolo illustra le risorse che sono presenti quando si distribuisce un cluster HDInsight in una rete virtuale di Azure personalizzata. Queste informazioni consentono di connettersi alle risorse locali nel cluster HDInsight in Azure. Per altre informazioni sulle reti virtuali di Azure, vedere [che cos'è rete virtuale di Azure?](../virtual-network/virtual-networks-overview.md).

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Tipi di risorsa nel cluster HDInsight di Azure

I cluster HDInsight di Azure hanno tipi diversi di macchine virtuali o i nodi. Ogni tipo di nodo svolge un ruolo nell'operazione del sistema. La tabella seguente riepiloga questi tipi di nodo e i relativi ruoli del cluster.

| Type | DESCRIZIONE |
| --- | --- |
| Nodo head |  Per tutti i tipi di cluster, ad eccezione di Apache Storm, i nodi head ospitano i processi che gestiscono l'esecuzione dell'applicazione distribuita. Il nodo head è anche il nodo che è possibile usare SSH per connettersi ed eseguire applicazioni che vengono quindi coordinate per l'esecuzione tra le risorse del cluster. Il numero di nodi head è fissato a due per tutti i tipi di cluster. |
| Nodo zooKeeper | Zookeeper coordina le attività tra i nodi che eseguono l'elaborazione dei dati. Inoltre non elezione del nodo head e tiene traccia del nodo head nel quale è in esecuzione uno specifico servizio master. Il numero di nodi ZooKeeper è fissato a due. |
| Nodo del ruolo di lavoro | Rappresenta i nodi che supportano la funzionalità di elaborazione dei dati. Nodi di lavoro possono essere aggiunti o rimossi dal cluster scalabilità capacità di elaborazione e la gestione dei costi. |
| Nodo perimetrale R Server | Il nodo perimetrale R Server rappresenta il nodo è possibile usare SSH per connettersi ed eseguire applicazioni che vengono quindi coordinate per l'esecuzione tra le risorse del cluster. Un nodo perimetrale non partecipa all'analisi dei dati all'interno del cluster. Questo nodo ospita anche Rstudio con R Server, consentendo di eseguire l'applicazione di R usando un browser. |
| Nodo area | Per il tipo di cluster HBase, il nodo di area (detto anche un nodo di dati) viene eseguito il Server di area. Server di area servono e gestire una parte dei dati gestiti da HBase. I nodi di area possono essere aggiunto o rimosso dal cluster scalabilità capacità di elaborazione e la gestione dei costi.|
| Nodo nimbus | Per il tipo di cluster Storm, il nodo Nimbus offre funzionalità simili al nodo Head. Il nodo Nimbus Assegna attività agli altri nodi nel cluster tramite Zookeeper, che coordina l'esecuzione di topologie Storm. |
| Nodi Supervisor | Per il tipo di cluster Storm, il nodo Supervisore esegue le istruzioni fornite dal nodo Nimbus per eseguire l'elaborazione desiderato. |

## <a name="basic-virtual-network-resources"></a>Risorse di rete virtuale di base

Il diagramma seguente illustra il posizionamento dei nodi di HDInsight e le risorse di rete in Azure.

![Diagramma di HDInsight entità create nella rete virtuale personalizzata Azure](./media/hdinsight-virtual-network-architecture/vnet-diagram.png)

Le risorse predefinite presenti quando HDInsight viene distribuita in una rete virtuale di Azure includono i tipi di nodo cluster indicati nella tabella precedente, nonché i dispositivi di rete che supportano la comunicazione tra la rete virtuale e all'esterno delle reti.

La tabella seguente riepiloga i nodi del cluster di nove che vengono creati quando HDInsight viene distribuita in una rete virtuale di Azure personalizzata.

| Tipo di risorsa | Numero presente | Dettagli |
| --- | --- | --- |
|Nodo head | two |    |
|Nodo Zookeeper | three | |
|Nodo del ruolo di lavoro | two | Questo numero può variare in base alla configurazione del cluster e la scalabilità. Per Apache Kafka, è necessario un minimo di tre nodi di lavoro.  |
|Nodo del gateway | two | I nodi del gateway sono macchine virtuali di Azure che vengono create in Azure, ma non sono visibili nella sottoscrizione. Contattare il supporto tecnico se è necessario riavviare i nodi. |

Le seguenti risorse di rete presenti vengono automaticamente create all'interno della rete virtuale usata con HDInsight:

| Risorse di rete | Numero presente | Dettagli |
| --- | --- | --- |
|Bilanciamento del carico | three | |
|Interfacce di rete | nove | Questo valore si basa su un cluster normale, in cui ogni nodo ha la propria interfaccia di rete. Le nove interfacce sono per i due nodi head, tre nodi zookeeper, due nodi di lavoro e due nodi del gateway indicati nella tabella precedente. |
|Indirizzi IP pubblici | two |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>Endpoint per la connessione a HDInsight

Si può accedere al cluster HDInsight in tre modi:

- Un endpoint HTTPS all'esterno della rete virtuale a `CLUSTERNAME.azurehdinsight.net`.
- Un endpoint SSH per connettersi direttamente al nodo head in `CLUSTERNAME-ssh.azurehdinsight.net`.
- Un endpoint HTTPS nella rete virtuale `CLUSTERNAME-int.azurehdinsight.net`. Si noti che la "-int" in questo URL. Questo endpoint verrà risolta in un indirizzo IP privato della rete virtuale e non è accessibile dalla rete internet pubblica.

Questi tre endpoint vengono assegnati un bilanciamento del carico.

Gli indirizzi IP pubblici vengono forniti anche per i due endpoint che consentono la connessione dall'esterno della rete virtuale.

1. Un indirizzo IP pubblico viene assegnato al bilanciamento del carico per il nome di dominio completo (FQDN) da utilizzare quando ci si connette al cluster da internet `CLUSTERNAME.azurehdinsight.net`.
1. Il secondo indirizzo IP pubblico viene usato per il nome di dominio unico SSH `CLUSTERNAME-ssh.azurehdinsight.net`.

## <a name="next-steps"></a>Passaggi successivi

* [Proteggere il traffico in ingresso ai cluster HDInsight in una rete virtuale con endpoint privato](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
