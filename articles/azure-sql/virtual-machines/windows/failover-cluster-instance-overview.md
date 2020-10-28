---
title: Istanze del cluster di failover
description: Informazioni sulle istanze del cluster di failover con SQL Server in macchine virtuali di Azure.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 6f216a7f0851661efc61a771fc35feb71e77fd1f
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792481"
---
# <a name="failover-cluster-instances-with-sql-server-on-azure-virtual-machines"></a>Istanze del cluster di failover con SQL Server in macchine virtuali di Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questo articolo presenta le differenze di funzionalità quando si lavora con le istanze del cluster di failover per SQL Server in macchine virtuali (VM) di Azure. 

## <a name="overview"></a>Panoramica

SQL Server in macchine virtuali di Azure usa funzionalità WSFC (Windows Server Failover Clustering) per fornire disponibilità elevata locale tramite ridondanza a livello di istanza del server, ovvero un'istanza del cluster di failover. Un'istanza del cluster di failover è una singola istanza di SQL Server installata in nodi di WSFC (o semplicemente nel cluster) e, possibilmente, in più subnet. Nella rete un'istanza del cluster di failover viene riconosciuta come istanza di SQL Server in esecuzione in un singolo computer. Tuttavia, l'istanza del cluster di failover fornisce failover da un nodo di WSFC a un altro se quello corrente diventa indisponibile.

Il resto dell'articolo è incentrato sulle differenze per le istanze del cluster di failover quando vengono usate con SQL Server in macchine virtuali di Azure. Per altre informazioni sulla tecnologia del clustering di failover, vedere: 

- [Tecnologie cluster di Windows](/windows-server/failover-clustering/failover-clustering-overview)
- [Istanze del cluster di failover di SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

## <a name="quorum"></a>Quorum

Le istanze del cluster di failover con SQL Server in macchine virtuali di Azure supportano l'uso di un disco di controllo, un cloud di controllo o una condivisione file di controllo per il quorum del cluster.

Per altre informazioni, vedere [Procedure consigliate per il quorum con machine virtuali di SQL Server in Azure](hadr-cluster-best-practices.md#quorum). 


## <a name="storage"></a>Archiviazione

Nei tradizionali ambienti cluster locali, un cluster di failover Windows usa una rete di archiviazione SAN accessibile a entrambi i nodi come risorsa di archiviazione condivisa. I file di SQL Server sono ospitati nella risorsa di archiviazione condivisa e sono accessibili a un solo nodo attivo alla volta. 

SQL Server in macchine virtuali di Azure offre varie opzioni come soluzione di archiviazione condivisa per una distribuzione di istanze del cluster di failover di SQL Server: 

||[Dischi condivisi di Azure](../../../virtual-machines/windows/disks-shared.md)|[Condivisioni file Premium](../../../storage/files/storage-how-to-create-premium-fileshare.md) |[Spazi di archiviazione diretta (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)|
|---------|---------|---------|---------|
|**Versione minima del sistema operativo**| Tutti |Windows Server 2012|Windows Server 2016|
|**Versione minima di SQL Server**|Tutti|SQL Server 2012|SQL Server 2016|
|**Disponibilità di VM supportate** |Set di disponibilità con gruppi di posizionamento di prossimità |Set di disponibilità e zone di disponibilità|Set di disponibilità |
|**Supporto di FileStream**|Sì|No|Sì |
|**Cache di BLOB di Azure**|No|No|Sì|

Il resto di questa sezione illustra i vantaggi e le limitazioni di ogni opzione di archiviazione disponibile per SQL Server in macchine virtuali di Azure. 

### <a name="azure-shared-disks"></a>Dischi condivisi di Azure

I [dischi condivisi di Azure](../../../virtual-machines/windows/disks-shared.md) sono una funzionalità di [Azure Managed Disks](../../../virtual-machines/managed-disks-overview.md). WSFC (Windows Server Failover Clustering) supporta l'uso di dischi condivisi di Azure con un'istanza del cluster di failover. 

**Sistemi operativi supportati** : Tutti   
**Versione di SQL supportata** : Tutti     

**Vantaggi:** 
- Utili per applicazioni di cui eseguire la migrazione ad Azure mantenendone inalterata l'architettura di disponibilità elevata e ripristino di emergenza. 
- Consentono di eseguire la migrazione di applicazioni in cluster ad Azure così come sono, grazie al supporto della funzionalità SCSI PR (SCSI Persistent Reservations). 
- Supportano l'archiviazione condivisa su unità SSD Premium di Azure dischi Ultra di Azure.
- Consentono di usare un singolo disco condiviso o più dischi in striping per creare un pool di archiviazione condiviso. 
- Supportano FileStream.


**Limitazioni** : 
- Le macchine virtuali devono essere inserite nello stesso set di disponibilità e nello stesso gruppo di posizionamento di prossimità.
- Le zone di disponibilità non sono supportate.
- La memorizzazione nella cache di dischi SSD Premium non è supportata.
 
Per iniziare, vedere [Istanza del cluster di failover di SQL Server con dischi condivisi di Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md). 

### <a name="storage-spaces-direct"></a>Spazi di archiviazione diretta

[Spazi di archiviazione diretta](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) è una funzionalità di Windows Server supportata con il clustering di failover in macchine virtuali di Azure. Fornisce una rete di archiviazione SAN virtuale basata su software.

**Sistemi operativi supportati** : Windows Server 2016 e versioni successive   
**Versione di SQL supportata** : SQL Server 2016 e versioni successive   


**Vantaggi:** 
- La larghezza di banda sufficiente assicura una soluzione di archiviazione condivisa stabile e a levate prestazioni. 
- Supporta la cache di BLOB di Azure, per cui le operazioni di lettura possono essere eseguite in locale dalla cache. Gli aggiornamenti vengono replicati contemporaneamente in entrambi i nodi. 
- Supporta FileStream. 

**Limitazioni** :
- Disponibile solo per Windows Server 2016 e versioni successive. 
- Le zone di disponibilità non sono supportate.
- Richiede la stessa capacità per i dischi collegati a entrambe le macchine virtuali. 
- Per ottenere prestazioni elevate è necessaria un'ampia larghezza di banda a causa della replica continua dei dischi. 
- Richiede VM di dimensioni maggiori e un doppio pagamento per l'archiviazione, perché le risorse di archiviazione sono collegate a ogni VM. 

Per iniziare, vedere [Istanza del cluster di failover di SQL Server con Spazi di archiviazione diretta](failover-cluster-instance-storage-spaces-direct-manually-configure.md). 

### <a name="premium-file-share"></a>Condivisione file Premium

Le [condivisioni file Premium](../../../storage/files/storage-how-to-create-premium-fileshare.md) sono una funzionalità di [File di Azure](../../../storage/files/index.yml). Sono supportate da SSD e offrono una latenza costantemente bassa. Sono pienamente supportate per l'uso con istanze del cluster di failover per SQL Server 2012 o versione successiva in Windows Server 2012 o versione successiva. Le condivisioni file Premium offrono una maggiore flessibilità, perché è possibile ridimensionarle senza tempi di inattività.

**Sistemi operativi supportati** : Windows Server 2012 e versioni successive   
**Versione di SQL supportata** : SQL Server 2012 e versioni successive   

**Vantaggi:** 
- Un'unica soluzione di archiviazione condivisa per le macchine virtuali distribuite in più zone di disponibilità. 
- File system completamente gestito con latenze a singola cifra e prestazioni di I/O con possibilità di burst. 

**Limitazioni** :
- Disponibili solo per Windows Server 2012 e versioni successive. 
- FileStream non è supportato. 


Per iniziare, vedere [Istanza del cluster di failover di SQL Server con condivisione file Premium](failover-cluster-instance-premium-file-share-manually-configure.md). 

### <a name="partner"></a>Partner

Sono disponibili soluzioni di clustering dei partner con archiviazione supportata. 

**Sistemi operativi supportati** : Tutti   
**Versione di SQL supportata** : Tutti   

Un unico esempio usa SIOS DataKeeper come risorsa di archiviazione. Per altre informazioni, vedere il post di blog su [clustering di failover e SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).

### <a name="iscsi-and-expressroute"></a>iSCSI e ExpressRoute

È anche possibile esporre una risorsa di archiviazione a blocchi condivisa con destinazione iSCSI tramite Azure ExpressRoute. 

**Sistemi operativi supportati** : Tutti   
**Versione di SQL supportata** : Tutti   

Ad esempio, NetApp Private Storage (NPS) espone una destinazione iSCSI tramite ExpressRoute con Equinix a macchine virtuali di Azure.

Per le soluzioni di replica dei dati e archiviazione condivisa dei partner Microsoft, contattare il fornitore in caso di problemi di accesso ai dati durante il failover.

## <a name="connectivity"></a>Connettività

Le istanze del cluster di failover con SQL Server in macchine virtuali di Azure usano [DNN (Distributed Network Name)](failover-cluster-instance-distributed-network-name-dnn-configure.md) o [VNN (Virtual Network Name) con Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) per instradare il traffico all'istanza di SQL Server, indipendentemente dal nodo attualmente proprietario delle risorse del cluster. Quando si usano determinate funzionalità e DNN con un'istanza del cluster di failover di SQL Server è necessario fare altre considerazioni. Per altre informazioni, vedere [Interoperabilità di DNN con l'istanza del cluster di failover di SQL Server](failover-cluster-instance-dnn-interoperability.md). 

Per altre informazioni sulle opzioni di connettività del cluster, vedere [Instradare connessioni a disponibilità elevata e ripristino di emergenza a SQL Server in macchine virtuali di Azure](hadr-cluster-best-practices.md#connectivity). 

## <a name="limitations"></a>Limitazioni

Per le istanze del cluster di failover con SQL Server in macchine virtuali di Azure, considerare le limitazioni seguenti. 

### <a name="lightweight-resource-provider"></a>Provider di risorse leggero   
Al momento le istanze del cluster di failover di SQL Server in macchine virtuali di Azure sono supportate solo con la [modalità di gestione leggera](sql-vm-resource-provider-register.md#management-modes) dell'[estensione SQL Server IaaS Agent](sql-server-iaas-agent-extension-automate-management.md). Per passare dalla modalità di estensione completa a quella leggera, eliminare la risorsa **Macchina virtuale SQL** per le VM corrispondenti e quindi registrarle con il provider di risorse macchine virtuali di SQL in modalità leggera. Per eliminare la risorsa **Macchina virtuale SQL** tramite il portale di Azure, deselezionare la casella di controllo accanto alla macchina virtuale corretta. 

L'estensione completa supporta funzionalità quali backup automatizzato, applicazione di patch e gestione avanzata del portale. Queste funzionalità non verranno eseguite correttamente per le VM di SQL Server dopo la reinstallazione dell'agente in modalità di gestione leggera.

### <a name="msdtc"></a>MSDTC 

Le macchine virtuali di Azure supportano Microsoft Distributed Transaction Coordinator (MSDTC) in Windows Server 2019, con archiviazione in volumi condivisi del cluster e [Azure Load Balancer Standard](../../../load-balancer/load-balancer-overview.md), oppure nelle VM di SQL Server che usano dischi condivisi di Azure. 

Nelle macchine virtuali di Azure, MSDTC non è supportato in Windows Server 2016 o versione precedente con volumi condivisi del cluster per i motivi seguenti:

- La risorsa MSDTC in cluster non può essere configurata per usare la risorsa di archiviazione condivisa. In Windows Server 2016, se si crea una risorsa MSDTC, non verrà visualizzata alcuna risorsa di archiviazione condivisa disponibile per l'uso, anche se lo spazio di archiviazione è disponibile. Questo problema è stato risolto per Windows Server 2019.
- Il servizio di bilanciamento del carico di base non gestisce le porte RPC.


## <a name="next-steps"></a>Passaggi successivi

Esaminare le [procedure consigliate per le configurazioni del cluster](hadr-cluster-best-practices.md), quindi è possibile [preparare la VM di SQL Server per l'istanza del cluster di failover](failover-cluster-instance-prepare-vm.md). 

Per altre informazioni, vedere: 

- [Tecnologie cluster di Windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [Istanze del cluster di failover di SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)