---
title: Procedure consigliate per la configurazione del cluster
description: Informazioni sulle configurazioni del cluster supportate quando si configura la disponibilità elevata e il ripristino di emergenza (HADR) per SQL Server in macchine virtuali di Azure, ad esempio i quorum supportati o le opzioni di routing della connessione.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: d20ac5964ef70618d4d7dc2d4a7fe7d7d01284ce
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85965647"
---
# <a name="cluster-configuration-best-practices-sql-server-on-azure-vms"></a>Procedure consigliate per la configurazione del cluster (SQL Server nelle VM di Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Un cluster viene usato per la disponibilità elevata e il ripristino di emergenza (HADR) con SQL Server in macchine virtuali (VM) di Azure. 

Questo articolo fornisce le procedure consigliate per la configurazione del cluster per le [istanze del cluster di failover (failover)](failover-cluster-instance-overview.md) e i [gruppi di disponibilità](availability-group-overview.md) quando vengono usate con SQL Server nelle VM di Azure. 


## <a name="networking"></a>Rete

Usare una singola scheda di interfaccia di rete per server (nodo cluster) e una singola subnet. La rete di Azure dispone di ridondanza fisica, che rende le schede di rete e le subnet aggiuntive non necessarie in un cluster guest di macchine virtuali di Azure. Il report di convalida del cluster informa che i nodi sono raggiungibili solo in una singola rete. È possibile ignorare questo avviso nei cluster di failover guest della macchina virtuale di Azure.

## <a name="quorum"></a>Quorum

Sebbene un cluster a due nodi funzioni senza una [risorsa quorum](/windows-server/storage/storage-spaces/understand-quorum), i clienti devono usare esclusivamente una risorsa quorum per ottenere supporto di produzione. La convalida del cluster non passerà alcun cluster senza una risorsa quorum. 

Tecnicamente, un cluster a tre nodi può sopravvivere a una perdita a nodo singolo (fino a due nodi) senza una risorsa quorum. Tuttavia, quando il cluster si trova in due nodi, si rischia di eseguire: 

- **Partizione nello spazio** (Split Brain): i nodi del cluster vengono separati in rete a causa del problema di server, nic o switch. 
- **Partizione nel tempo** (amnesia): un nodo viene aggiunto o riunito al cluster e tenta di richiedere la proprietà del gruppo di cluster o di un ruolo del cluster in modo non appropriato. 

La risorsa quorum protegge il cluster in base a uno di questi problemi. 

Per configurare la risorsa quorum con SQL Server nelle macchine virtuali di Azure, è possibile usare i tipi di controllo seguenti: 


||[Disco di controllo](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |[Cloud di controllo](/windows-server/failover-clustering/deploy-cloud-witness)  |[Condivisione file di controllo](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |
|---------|---------|---------|---------|
|**Sistema operativo supportato**| Tutti |Windows Server 2016+| Windows Server 2012 +|
|**Versione SQL Server supportata**|SQL Server 2019|SQL Server 2016 +|SQL Server 2016 +|



### <a name="disk-witness"></a>Disco di controllo

Un disco di controllo è un disco di dimensioni ridotte del cluster nel gruppo di archiviazione disponibile nel cluster. Questo disco è a disponibilità elevata ed è in grado di eseguire il failover tra i nodi. Contiene una copia del database del cluster, con dimensioni predefinite generalmente inferiori a 1 GB. 

Configurare un disco condiviso di Azure come disco di controllo. 

Per iniziare, vedere [configurare un disco](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)di controllo.


**Sistema operativo supportato**: tutti    
**Versione SQL supportata**: SQL Server 2019   


### <a name="cloud-witness"></a>Cloud di controllo

Un server di controllo del mirroring è un tipo di quorum di controllo del cluster di failover che usa Microsoft Azure per fornire un voto sul quorum del cluster. Le dimensioni predefinite sono pari a circa 1 MB e contengono solo il timestamp. Un server di controllo del cloud è ideale per le distribuzioni in più siti, più zone e più aree.

Per iniziare, vedere [configurare un cloud](/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp)di controllo.


**Sistema operativo supportato**: Windows Server 2016 e versioni successive   
**Versione SQL supportata**: SQL Server 2016 e versioni successive     


### <a name="file-share-witness"></a>Condivisione file di controllo

Una condivisione file di controllo è una condivisione file SMB che in genere è configurata in un file server che esegue Windows Server. Mantiene le informazioni di clustering in un file witness. log, ma non archivia una copia del database del cluster. In Azure è possibile configurare una [condivisione file di Azure](../../../storage/files/storage-how-to-create-file-share.md) da usare come condivisione file di controllo oppure è possibile usare una condivisione file in una macchina virtuale separata.

Se si intende usare un'altra condivisione file di Azure, è possibile montarla con lo stesso processo usato per [montare la condivisione file Premium](failover-cluster-instance-premium-file-share-manually-configure.md#mount-premium-file-share). 

Per iniziare, vedere [configurare una condivisione file](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)di controllo.


**Sistema operativo supportato**: Windows Server 2012 e versioni successive   
**Versione SQL supportata**: SQL Server 2016 e versioni successive   


## <a name="connectivity"></a>Connettività

In un ambiente di rete locale tradizionale, un'istanza del cluster di failover di SQL Server sembra essere una singola istanza di SQL Server in esecuzione in un singolo computer. Poiché viene eseguito il failover dell'istanza del cluster di failover dal nodo al nodo, il nome della rete virtuale (VNN) per l'istanza fornisce un punto di connessione unificato e consente alle applicazioni di connettersi all'istanza di SQL Server senza sapere quale nodo è attualmente attivo. Quando si verifica un failover, il nome della rete virtuale viene registrato nel nuovo nodo attivo dopo l'avvio. Questo processo è trasparente per il client o l'applicazione che si connette a SQL Server e questo riduce al minimo i tempi di inattività del client o dell'applicazione durante un errore. 

Usare un VNN con Azure Load Balancer o un nome di rete distribuito (DNN) per instradare il traffico verso VNN dell'istanza del cluster di failover con SQL Server in macchine virtuali di Azure. La funzionalità DNN è attualmente disponibile solo per SQL Server 2019 CU2 e versioni successive in una macchina virtuale Windows Server 2016 (o versioni successive). 

Nella tabella seguente viene confrontata la supportabilità della connessione HADR: 

| |**VNN (Virtual Network Name)**  |**DNN (Distributed Network Name)**  |
|---------|---------|---------|
|**Versione minima del sistema operativo**| Windows Server 2012 | Windows Server 2016|
|**Versione minima di SQL Server** |SQL Server 2012 |SQL Server 2019 CU2|
|**Soluzione HADR supportata** | Istanza del cluster di failover <br/> gruppo di disponibilità | Istanza del cluster di failover|


### <a name="virtual-network-name-vnn"></a>VNN (Virtual Network Name)

Poiché il punto di accesso IP virtuale funziona in modo diverso in Azure, è necessario configurare [Azure Load Balancer](../../../load-balancer/index.yml) per instradare il traffico all'indirizzo IP dei nodi dell'istanza del cluster di failover. In macchine virtuali di Azure, un servizio di bilanciamento del carico include l'indirizzo IP per il VNN su cui si basano le risorse del SQL Server cluster. Il servizio di bilanciamento del carico distribuisce i flussi in ingresso che arrivano al front-end e quindi instrada il traffico alle istanze definite dal pool back-end. Il flusso del traffico viene configurato usando le regole di bilanciamento del carico e i probe di integrità. Con SQL Server FCI, le istanze del pool back-end sono le macchine virtuali di Azure in esecuzione SQL Server. 

Si verifica un lieve ritardo di failover quando si usa il servizio di bilanciamento del carico, perché il probe di integrità esegue controlli attivi ogni 10 secondi per impostazione predefinita. 

Per iniziare, informazioni su come [configurare Azure Load Balancer per un'istanza FCI](hadr-vnn-azure-load-balancer-configure.md). 

**Sistema operativo supportato**: Windows Server 2012 e versioni successive   
**Versione SQL supportata**: SQL Server 2012 e versioni successive   
**Soluzione HADR supportata**: istanza del cluster di failover e gruppo di disponibilità 


### <a name="distributed-network-name-dnn"></a>DNN (Distributed Network Name)

Il nome di rete distribuita è una nuova funzionalità di Azure per SQL Server 2019 CU2. DNN fornisce un modo alternativo per SQL Server client di connettersi all'istanza del cluster di failover di SQL Server senza usare un servizio di bilanciamento del carico. 

Quando viene creata una risorsa DNN, il cluster associa il nome DNS agli indirizzi IP di tutti i nodi del cluster. Il client SQL tenterà di connettersi a ogni indirizzo IP in questo elenco per individuare il nodo in cui è attualmente in esecuzione l'istanza del cluster di failover. È possibile accelerare questo processo specificando `MultiSubnetFailover=True` nella stringa di connessione. Questa impostazione indica al provider di provare tutti gli indirizzi IP in parallelo, in modo che il client possa connettersi immediatamente all'istanza FCI. 

Quando possibile, è consigliabile usare un nome di rete distribuita su un servizio di bilanciamento del carico perché: 
- La soluzione end-to-end è più affidabile perché non è più necessario gestire la risorsa di bilanciamento del carico. 
- L'eliminazione dei probe del servizio di bilanciamento del carico riduce al minimo la durata del failover. 
- Il DNN semplifica il provisioning e la gestione dell'istanza del cluster di failover con SQL Server nelle VM di Azure. 

La maggior parte delle funzionalità SQL Server funziona in modo trasparente con FCI. In questi casi, è possibile sostituire semplicemente il nome DNS VNN esistente con il nome DNS DNN o impostare il valore DNN con il nome DNS VNN esistente. Tuttavia, alcuni componenti lato server richiedono un alias di rete che esegue il mapping del nome VNN al nome DNN. Casi specifici possono richiedere l'uso esplicito del nome DNS DNN, ad esempio quando si definiscono determinati URL in una configurazione lato server. 

Per iniziare, informazioni su come [configurare una risorsa DNN per un'istanza FCI](hadr-distributed-network-name-dnn-configure.md). 

**Sistema operativo supportato**: Windows Server 2016 e versioni successive   
**Versione SQL supportata**: SQL Server 2019 e versioni successive   
**Soluzione HADR supportata**: solo istanza del cluster di failover


## <a name="limitations"></a>Limitazioni

Quando si lavora con la FCI o i gruppi di disponibilità e SQL Server in macchine virtuali di Azure, tenere presenti le limitazioni seguenti. 

### <a name="msdtc"></a>MSDTC 
Macchine virtuali di Azure supporta Microsoft Distributed Transaction Coordinator (MSDTC) in Windows Server 2019 con archiviazione in volumi condivisi cluster (CSV) e [Load Balancer standard di Azure](../../../load-balancer/load-balancer-standard-overview.md).

In macchine virtuali di Azure, MSDTC non è supportato per Windows Server 2016 o versioni precedenti perché:

- La risorsa MSDTC in cluster non può essere configurata per usare la risorsa di archiviazione condivisa. In Windows Server 2016, se si crea una risorsa MSDTC, non verrà visualizzata alcuna risorsa di archiviazione condivisa disponibile per l'uso, anche se lo spazio di archiviazione è disponibile. Questo problema è stato risolto per Windows Server 2019.
- Il servizio di bilanciamento del carico di base non gestisce le porte RPC.


## <a name="next-steps"></a>Passaggi successivi

Dopo aver determinato le procedure consigliate appropriate per la soluzione, iniziare preparando la [macchina virtuale SQL Server per l'istanza](failover-cluster-instance-prepare-vm.md)del cluster di failover. È anche possibile creare il gruppo di disponibilità usando l' [interfaccia](availability-group-az-cli-configure.md)della riga di comando di Azure o i [modelli di avvio rapido di Azure](availability-group-quickstart-template-configure.md). 

