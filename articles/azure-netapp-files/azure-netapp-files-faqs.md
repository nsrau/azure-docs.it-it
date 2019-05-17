---
title: Domande frequenti sui file di Azure NetApp | Microsoft Docs
description: Risposte alle domande frequenti su Azure NetApp file.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: b-juche
ms.openlocfilehash: 6f1ca3398678b59a81e5c22b51b36a1f5505d4c2
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806387"
---
# <a name="faqs-about-azure-netapp-files"></a>Domande frequenti sui file di Azure NetApp

Questo articolo risponde alle domande frequenti (FAQ) su file di Azure NetApp. 

## <a name="networking-faqs"></a>Domande frequenti su rete

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>Il percorso dati NFS enunciato in Internet?  

 No. Il percorso dati NFS non passa da Internet. File di NetApp Azure è un servizio nativo di Azure che viene distribuito nella rete virtuale di Azure (VNet) in cui il servizio è disponibile. NetApp file usa una subnet del delegato e Azure effettua il provisioning di un'interfaccia di rete direttamente sulla rete virtuale. 

Visualizzare [pianificazione di rete le linee guida per i file di Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) per informazioni dettagliate.  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>È possibile collegare una rete virtuale già creata per il servizio Azure NetApp file?

Sì, è possibile connettere reti virtuali che è stato creato per il servizio. 

Visualizzare [pianificazione di rete le linee guida per i file di Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) per informazioni dettagliate.  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>È possibile montare un volume NFS NetApp di file di Azure Usa il nome FQDN DNS?

Sì, è possibile, se si creano le voci DNS necessarie. File di NetApp Azure fornisce l'indirizzo IP del servizio per il volume con provisioning. 

> [!NOTE] 
> File di Azure NetApp possibile distribuire altri indirizzi IP per il servizio in base alle esigenze.  Le voci DNS potrebbero dover essere aggiornate periodicamente.

## <a name="security-faqs"></a>Domande frequenti sulla sicurezza

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Il traffico di rete tra VM di Azure e lo spazio di archiviazione può essere crittografato?

Il traffico dei dati (il traffico da client NFSv3 o a SMBv3 ai volumi di file di Azure NetApp) non è crittografato. Tuttavia, il traffico da una VM di Azure, in esecuzione un client SMB o NFS, NetApp in file di Azure è sicuro quanto qualsiasi altro traffico di Azure-VM-per-VM. Il traffico è locale rispetto alla rete di data center Azure. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>Lo spazio di archiviazione può essere crittografato a riposo?

Tutti i volumi di file di NetApp Azure vengono crittografati usando lo standard FIPS 140-2 standard. Tutte le chiavi vengono gestite dal servizio file di Azure NetApp. 

### <a name="how-are-encryption-keys-managed"></a>Come vengono gestite le chiavi di crittografia? 

Gestione delle chiavi per i file di Azure NetApp viene gestita dal servizio.  Attualmente, le chiavi gestite dall'utente (Bring Your proprie chiavi) non sono supportate.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>È possibile configurare le regole dei criteri di esportazione NFS per controllare l'accesso alla destinazione di montaggio del servizio file di Azure NetApp?


Sì, è possibile configurare fino a cinque regole in un singolo criterio esportazione NFS.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>File di Azure NetApp supporta gruppi di sicurezza di rete?

No, attualmente è possibile applicare gruppi di sicurezza di rete alla subnet del delegato di file di Azure NetApp o le interfacce di rete create dal servizio.

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>È possibile usare Azure IAM con file di Azure NetApp?

Sì, i file di Azure NetApp supporta funzionalità RBAC con IAM di Azure.

## <a name="performance-faqs"></a>Domande frequenti sulle prestazioni

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Cosa fare per ottimizzare o ottimizzare le prestazioni di file di Azure NetApp?

È possibile eseguire le azioni seguenti per i requisiti relativi alle prestazioni: 
- Assicurarsi che la macchina virtuale viene ridimensionata in modo appropriato.
- Abilitare la funzionalità rete accelerata per la macchina virtuale.
- Selezionare il livello di servizio desiderato e le dimensioni per il pool di capacità.
- Creare un volume con dimensioni la quota desiderata per la capacità e prestazioni.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Come è possibile convertire i livelli di velocità effettiva in base al servizio NetApp di file di Azure per IOPS?

È possibile convertire MB/s per IOPS utilizzando la formula seguente:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Come si modifica il livello di servizio di un volume?

Modifica del livello di servizio di un volume non è attualmente supportata.

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Come monitorare le prestazioni di file di Azure NetApp?

File di NetApp Azure fornisce le metriche delle prestazioni di volume. È anche possibile usare monitoraggio di Azure per monitorare le metriche di utilizzo per i file di Azure NetApp.  Visualizzare [metriche per file di Azure NetApp](azure-netapp-files-metrics.md) per un elenco delle metriche delle prestazioni per file di Azure NetApp.

## <a name="nfs-faqs"></a>Domande frequenti su NFS

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Si desidera disporre di un volume montato automaticamente quando una macchina virtuale di Azure viene avviata o riavviata.  Come configurare l'host per i volumi NFS permanenti?

Per un volume NFS il montaggio automatico all'avvio della macchina virtuale o un riavvio, aggiungere una voce per il `/etc/fstab` file nell'host. 

Ad esempio: `$ANFIP:/$FILEPATH      /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

- $ANFIP  
    L'indirizzo IP del volume del file di Azure NetApp trovato nel pannello delle proprietà del volume
- $FILEPATH  
    Il percorso di esportazione del volume del file di Azure NetApp
- $MOUNTPOINT  
    La directory creata nell'host Linux usato per montare l'esportazione NFS

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Perché il comando DF sul client per NFS non mostra le dimensioni del volume con provisioning?

Le dimensioni del volume ha segnalata in DF sono la dimensione massima che possibile raggiungere il volume di file di Azure NetApp. Le dimensioni del volume del file di Azure NetApp nel comando DF non riflette la quota o dimensione del volume.  È possibile ottenere le dimensioni del volume di file di Azure NetApp o quota tramite il portale di Azure o l'API.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Quale versione NFS supporta file di Azure NetApp?

File di NetApp Azure supporta attualmente NFSv3.

### <a name="how-do-i-enable-root-squashing"></a>Come si abilita la compressione di radice?

Compressione di radice non è attualmente supportata.

## <a name="smb-faqs"></a>Domande frequenti su SMB

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>File di Azure NetApp supporta Azure Active Directory?

No, attualmente non è supportata.  File di Azure NetApp supporta Active Directory Domain Services (portare i propri AD), che può utilizzare esistenti i controller di dominio Active Directory con file di Azure NetApp. I controller di dominio possono risiedere in Azure come macchine virtuali o in locale tramite ExpressRoute.

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>È una connessione di Active Directory necessaria per l'accesso SMB? 

Sì, è necessario creare una connessione di Active Directory prima di distribuire un volume di SMB. I controller di dominio specificati devono essere accessibili dalla subnet del delegato NetApp di file di Azure per una connessione ha esito positivo.  Visualizzare [creare un volume SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes#create-an-smb-volume) per informazioni dettagliate. 

### <a name="how-many-active-directory-connections-are-supported"></a>Il numero di connessioni Active Directory è supportato?

File di NetApp Azure supporta attualmente una connessione di Active Directory per ogni sottoscrizione. Inoltre, la connessione di Active Directory è specifica di un singolo account NetApp; non è condiviso tra gli account. 

### <a name="what-versions-of-windows-ad-are-supported"></a>Quali versioni di Windows Active Directory sono supportati?

File di NetApp Azure supporta la versione di servizi di dominio Active Directory di Windows Server 2016 2008r2SP1.

## <a name="capacity-management-faqs"></a>Domande frequenti sulla gestione della capacità

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Come effettuare il monitoraggio dell'utilizzo per il pool di capacità e volume NetApp di file di Azure? 

File di Azure NetApp fornisce metriche di utilizzo capacità pool e il volume. È anche possibile usare monitoraggio di Azure per monitorare l'utilizzo per i file di Azure NetApp. Visualizzare [metriche per file di Azure NetApp](azure-netapp-files-metrics.md) per informazioni dettagliate. 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>È possibile gestire i file di NetApp Azure tramite Azure Storage Explorer?

 No. File di NetApp Azure non è supportato da Azure Storage Explorer.

## <a name="data-migration-and-protection-faqs"></a>Domande frequenti sulla migrazione e la protezione dei dati

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Come eseguire la migrazione dei dati NetApp in file di Azure?
File di NetApp Azure offre i volumi SMB e NFS.  È possibile usare qualsiasi strumento di copia basata su file per la migrazione dei dati al servizio. 

NetApp offre una soluzione basata su SaaS [sincronizzazione Cloud NetApp](https://cloud.netapp.com/cloud-sync-service).  La soluzione consente di replicare NFS o condivide i dati SMB per le esportazioni NFS i file di Azure NetApp o SMB. 

È anche possibile usare un'ampia gamma di strumenti gratuiti per copiare i dati. Per NFS, è possibile usare gli strumenti di carichi di lavoro, ad esempio [rsync](https://rsync.samba.org/examples.html) per copiare e sincronizzare i dati di origine in un volume di file di Azure NetApp. Per SMB, è possibile usare i carichi di lavoro [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) nello stesso modo.  Questi strumenti possono anche replicare le autorizzazioni di file o cartella. 

I requisiti per la migrazione da dati in locale, NetApp in file di Azure sono come segue: 

- Verificare che il file di NetApp Azure è disponibile nell'area di Azure di destinazione.
- Convalidare la connettività di rete tra l'origine e indirizzo IP di volume di destinazione i file di Azure NetApp. Trasferimento dei dati tra locale e il servizio Azure NetApp file è supportato tramite ExpressRoute.
- Creare il volume di file di NetApp Azure di destinazione.
- Trasferire i dati di origine al volume di destinazione usando lo strumento di copia di file preferito.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Come si crea una copia di un volume di file NetApp Azure in un'altra area di Azure?
    
File di NetApp Azure offre i volumi SMB e NFS.  Qualsiasi strumento di copia basata su file può essere usato per replicare dati tra aree di Azure. 

NetApp offre una soluzione SaaS basato [sincronizzazione Cloud NetApp](https://cloud.netapp.com/cloud-sync-service).  La soluzione consente di replicare NFS o condivide i dati SMB per le esportazioni NFS i file di Azure NetApp o SMB. 

È anche possibile usare un'ampia gamma di strumenti gratuiti per copiare i dati. Per NFS, è possibile usare gli strumenti di carichi di lavoro, ad esempio [rsync](https://rsync.samba.org/examples.html) per copiare e sincronizzare i dati di origine in un volume di file di Azure NetApp. Per SMB, è possibile usare i carichi di lavoro [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) nello stesso modo.  Questi strumenti possono anche replicare le autorizzazioni di file o cartella. 

Come indicato di seguito sono riportati i requisiti per la replica di un volume di file NetApp Azure in un'altra area di Azure: 
- Verificare che il file di NetApp Azure è disponibile nell'area di Azure di destinazione.
- Convalidare la connettività di rete tra reti virtuali in ogni area. Attualmente, il peering globale tra le reti virtuali non è supportato.  È possibile stabilire la connettività tra reti virtuali tramite una connessione VPN S2S o il collegamento a un circuito ExpressRoute. 
- Creare il volume di file di NetApp Azure di destinazione.
- Trasferire i dati di origine al volume di destinazione usando lo strumento di copia di file preferito.

### <a name="is-migration-with-azure-data-box-supported"></a>È la migrazione con Azure Data Box è supportato?

 No. Azure Data Box non supporta attualmente i file di Azure NetApp. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>È la migrazione con il servizio importazione/esportazione di Azure è supportato?

 No. Il servizio importazione/esportazione di Azure non supporta attualmente i file di Azure NetApp.

## <a name="next-steps"></a>Passaggi successivi  

- [Domande frequenti su ExpressRoute di Microsoft Azure](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [Domande frequenti sulla rete virtuale Microsoft Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [Come creare una richiesta di supporto in Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)
- [Azure Data Box](https://docs.microsoft.com/azure/databox-family/)