---
title: 'Technical guidance: ripristino dall&quot;ambiente locale ad Azure | Microsoft Docs'
description: Articolo sulla progettazione di sistemi di ripristino dall&quot;infrastruttura locale ad Azure
services: 
documentationcenter: na
author: adamglick
manager: saladki
editor: 
ms.assetid: 114c29c9-453f-4a54-8d0f-d77c5ad47d60
ms.service: resiliency
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: aglick
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 2890efbfec90ef0e8e205a801c6e9cf6166985f0


---
# <a name="azure-resiliency-technical-guidance-recovery-from-on-premises-to-azure"></a>Materiale sussidiario sulla resilienza di Azure: ripristino dall'ambiente locale ad Azure
Azure offre una serie completa di servizi per abilitare l'estensione di un data center locale ad Azure per scopi di disponibilità elevata e ripristino di emergenza:

* **Rete**: con la rete privata virtuale, la rete locale viene estesa in modo sicuro al cloud.
* **Calcolo**: i clienti che usano Hyper-V in locale possono trasferire le macchine virtuali esistenti in Azure.
* **Archiviazione**: StorSimple estende il file system ad Archiviazione di Azure. Il servizio Backup di Azure consente il backup dei file e dei database SQL in Archiviazione di Azure.
* **Replica di database**: con i gruppi di disponibilità SQL Server 2014 o versioni successive è possibile implementare disponibilità elevata e ripristino di emergenza per i dati locali.

## <a name="networking"></a>Rete
È possibile usare la rete virtuale di Azure per creare una sezione logicamente isolata in Azure e connetterla in modo sicuro a un data center locale o a un singolo computer client usando una connessione IPsec. Con la rete virtuale è possibile sfruttare l'infrastruttura scalabile su richiesta in Azure offrendo connettività ai dati e alle applicazioni locali, inclusi i sistemi in esecuzione in Windows Server, mainframe e UNIX. Per altre informazioni vedere la [documentazione sulla rete di Azure](../virtual-network/virtual-networks-overview.md) .

## <a name="compute"></a>Calcolo
Se si usa Hyper-V in locale è possibile trasferire le macchine virtuali esistenti in Azure e provider di servizi che eseguono Windows Server 2012 o versioni successive, senza apportare modifiche alle macchine virtuali o convertirne i formati. Per altre informazioni vedere [Informazioni sui dischi e sui dischi rigidi virtuali per le macchine virtuali di Azure](../virtual-machines/virtual-machines-linux-about-disks-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="azure-site-recovery"></a>Azure Site Recovery
Se è necessario il ripristino di emergenza distribuito come servizio (DRaaS), Azure offre [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/). Azure Site Recovery offre protezione completa per VMware, Hyper-V e server fisici. Con Azure Site Recovery è possibile usare un altro server locale o Azure come sito di ripristino. Per altre informazioni su Azure Site Recovery, vedere la [documentazione di Azure Site Recovery](https://azure.microsoft.com/documentation/services/site-recovery/).

## <a name="storage"></a>Archiviazione
Sono disponibili diverse opzioni per l'uso di Azure come sito di backup per i dati locali.

### <a name="storsimple"></a>StorSimple
StorSimple integra in modo sicuro e trasparente l'archiviazione cloud per applicazioni locali. Offre una singola appliance che mette a disposizione archiviazione locale e cloud a livelli e a prestazioni elevate, archiviazione live, protezione dei dati basata su cloud e ripristino di emergenza. Per altre informazioni vedere la [pagina del prodotto StorSimple](https://azure.microsoft.com/services/storsimple/).

### <a name="azure-backup"></a>Backup di Azure
Backup di Azure consente il backup cloud con i consueti strumenti in Windows Server 2012 o versioni successive, Windows Server 2012 Essentials o versioni successive e System Center 2012 Data Protection Manager o versioni successive. Questi strumenti offrono un flusso di lavoro per la gestione del backup indipendente dalla posizione di archiviazione dei backup, che si tratti di un disco locale o di Archiviazione di Azure. Dopo aver eseguito il backup dei dati nel cloud, gli utenti autorizzati possono facilmente recuperare i backup in qualsiasi server.

Con i backup incrementali, solo le modifiche ai file vengono trasferite nel cloud. È così possibile usare lo spazio di archiviazione in modo efficiente, ridurre il consumo della larghezza di banda e offrire un ripristino temporizzato di più versioni dei dati. È anche possibile usare altre funzionalità, ad esempio i criteri di conservazione dei dati, la compressione dei dati e la limitazione del trasferimento dati. L'uso di Azure come posizione di backup offre l'ovvio vantaggio di avere automaticamente i backup all'esterno. Si elimina così la necessità di proteggere i supporti di backup locali.

Per altre informazioni, vedere [Informazioni su Backup di Azure](../backup/backup-introduction-to-azure-backup.md) e [Configurare il backup di Azure per i dati DPM](https://technet.microsoft.com/library/jj728752.aspx).

## <a name="database"></a>Database
È possibile disporre di una soluzione di ripristino di emergenza per i database di SQL Server in un ambiente IT ibrido usando i gruppi di disponibilità AlwaysOn, il mirroring del database, il log shipping e funzionalità di backup e ripristino con l'archiviazione BLOB di Azure. Tutte queste soluzioni usano SQL Server in esecuzione in macchine virtuali di Azure.

È possibile usare Gruppi di disponibilità AlwaysOn in un ambiente IT ibrido in cui le repliche di database sono presenti sia in locale, sia nel cloud. Vedere il diagramma seguente.

![Gruppi di disponibilità AlwaysOn SQL Server in un'architettura di cloud ibrido](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-3.png)

Il mirroring del database può anche estendersi su server locali e nel cloud in una configurazione basata su certificati. Il diagramma seguente illustra questo concetto.

![Mirroring del database SQL Server in un'architettura di cloud ibrido](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-4.png)

È possibile usare il log shipping per sincronizzare un database locale con un database di SQL Server in una macchina virtuale di Azure.

![Log shipping SQL Server in un'architettura di cloud ibrido](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-5.png)

È infine possibile eseguire il backup di un database locale direttamente nell'archivio BLOB di Azure.

![Backup di SQL Server in un archivio BLOB di Azure in un'architettura di cloud ibrido](./media/resiliency-technical-guidance-recovery-on-premises-azure/SQL_Server_Disaster_Recovery-6.png)

Per altre informazioni, vedere [Disponibilità elevata e ripristino di emergenza per SQL Server nelle macchine virtuali di Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e [Backup e ripristino per SQL Server nelle macchine virtuali di Azure](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="checklists-for-on-premises-recovery-in-microsoft-azure"></a>Elenchi di controllo per il ripristino locale in Microsoft Azure
### <a name="networking"></a>Rete
1. Vedere la sezione Rete di questo documento.
2. Usare la rete virtuale per connettere in modo sicuro l'ambiente locale al cloud.

### <a name="compute"></a>Calcolo
1. Vedere la sezione Calcolo di questo documento.
2. Rilocare le macchine virtuali tra Hyper-V e Azure.

### <a name="storage"></a>Archiviazione
1. Vedere la sezione Archiviazione di questo documento.
2. Sfruttare i servizi StorSimple per l'uso dell'archiviazione cloud.
3. Usare i servizi di Backup di Azure.

### <a name="database"></a>Database
1. Vedere la sezione Database di questo documento.
2. Prendere in considerazione l'uso di SQL Server nelle macchine virtuali di Azure come backup.
3. Impostare Gruppi di disponibilità AlwaysOn.
4. Configurare il mirroring del database basato su certificati.
5. Usare il log shipping.
6. Eseguire il backup del database locale nell'archivio BLOB di Azure.

## <a name="next-steps"></a>Passaggi successivi
Questo articolo fa parte della serie [Materiale sussidiario sulla resilienza di Azure](resiliency-technical-guidance.md). Il prossimo articolo di questa serie riguarda il [ripristino dal danneggiamento o dall'eliminazione accidentale dei dati](resiliency-technical-guidance-recovery-data-corruption.md).




<!--HONumber=Jan17_HO1-->


