---
title: Domande frequenti sui file NetApp di Azure Documenti Microsoft
description: Risposte alle domande frequenti sui file NetApp di Azure.Answers frequently asked questions about Azure NetApp Files.
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
ms.date: 04/03/2020
ms.author: b-juche
ms.openlocfilehash: aebc669a90511e48ddd2a7876553948c04b97710
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667820"
---
# <a name="faqs-about-azure-netapp-files"></a>Domande frequenti sui file NetApp di AzureFAQQs About Azure NetApp Files

Questo articolo risponde alle domande frequenti sui file NetApp di Azure.This article answers frequently asked questions (FAQs) about Azure NetApp Files. 

## <a name="networking-faqs"></a>Domande frequenti sulla rete

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>Il percorso dati NFS passa su Internet?  

No. Il percorso dati NFS non passa su Internet. File NetApp di Azure è un servizio nativo di Azure distribuito nella rete virtuale di Azure in cui il servizio è disponibile. File NetApp di Azure usa una subnet delegata ed esegue il provisioning di un'interfaccia di rete direttamente nella rete virtuale. 

Per informazioni dettagliate, vedere Linee guida per la pianificazione della rete di file netApp di [Azure.See Guidelines for Azure NetApp Files network planning](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) for details.  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>È possibile connettere una rete virtuale già creata al servizio File NetApp di Azure?

Sì, è possibile connettere le reti virtuali create al servizio. 

Per informazioni dettagliate, vedere Linee guida per la pianificazione della rete di file netApp di [Azure.See Guidelines for Azure NetApp Files network planning](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) for details.  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>È possibile montare un volume NFS di file NetApp di Azure usando il nome FQDN DNS?

Sì, è possibile, se si creano le voci DNS necessarie. File NetApp di Azure fornisce l'IP del servizio per il volume di cui è stato eseguito il provisioning. 

> [!NOTE] 
> I file NetApp di Azure possono distribuire indirizzi IP aggiuntivi per il servizio in base alle esigenze.  Potrebbe essere necessario aggiornare periodicamente le voci DNS.

## <a name="security-faqs"></a>Domande frequenti sulla sicurezza

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>È possibile crittografare il traffico di rete tra la macchina virtuale di Azure e l'archiviazione?

Il traffico dati (il traffico dal client NFSv3, NFSv4.1 o SMBv3 ai volumi dei file NetApp di Azure) non è crittografato. Tuttavia, il traffico da una macchina virtuale di Azure (che esegue un client NFS o SMB) ai file NetApp di Azure è sicuro come qualsiasi altro traffico da Azure-VM a VM.However, the traffic from an Azure VM (running an NFS or SMB client) to Azure NetApp Files is secure as any other Azure-VM-to-VM traffic. Questo traffico è locale per la rete di data center di Azure.This traffic is local to the Azure data-center network. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>L'archiviazione può essere crittografata a riposo?

Tutti i volumi di file NetApp di Azure vengono crittografati utilizzando lo standard FIPS 140-2. Tutte le chiavi sono gestite dal servizio File di Rete di Azure.All keys are managed by the Azure NetApp Files service. 

### <a name="how-are-encryption-keys-managed"></a>Come vengono gestite le chiavi di crittografia? 

La gestione delle chiavi per i file NetApp di Azure viene gestita dal servizio. Viene generata una chiave di crittografia dei dati XTS-AES-256 univoca per ogni volume. Una gerarchia di chiavi di crittografia viene utilizzata per crittografare e proteggere tutte le chiavi del volume. Queste chiavi di crittografia non vengono mai visualizzate o segnalate in un formato non crittografato. Le chiavi di crittografia vengono eliminate immediatamente quando viene eliminato un volume.

Il supporto per le chiavi gestite dall'utente (Bring Your Own Keys) tramite Azure dedicato HSM è disponibile in modo controllato nelle aree Est,Stati Uniti Occidentali2 e Stati Uniti centro-meridionali.  È possibile richiedere **anffeedback@microsoft.com**l'accesso all'indirizzo . Man mano che la capacità è disponibile, le richieste verranno approvate.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>È possibile configurare le regole dei criteri di esportazione NFS per controllare l'accesso alla destinazione di montaggio del servizio File netApp di Azure?


Sì, è possibile configurare fino a cinque regole in un singolo criterio di esportazione NFS.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>I file NetApp di Azure supportano i gruppi di sicurezza di rete?

No, attualmente non è possibile applicare i gruppi di sicurezza di rete alla subnet delegata dei file di Azure NetApp o alle interfacce di rete create dal servizio.

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>È possibile usare Azure IAM con i file NetApp di Azure?

Sì, File NetApp di Azure supporta le funzionalità RBAC con Azure IAM.Yes, Azure NetApp Files supports RBAC features with Azure IAM.

## <a name="performance-faqs"></a>Domande frequenti sulle prestazioni

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Cosa devo fare per ottimizzare o ottimizzare le prestazioni dei file NetApp di Azure?

È possibile eseguire le azioni seguenti in base ai requisiti di prestazioni:You can take the following actions per the performance requirements: 
- Assicurarsi che la macchina virtuale sia dimensionata in modo appropriato.
- Abilitare la rete accelerata per la macchina virtuale.
- Selezionare il livello di servizio e le dimensioni desiderati per il pool di capacità.
- Creare un volume con la dimensione della quota desiderata per la capacità e le prestazioni.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Come si convertono i livelli di servizio basati sulla velocità effettiva dei file NetApp di Azure in operazioni di I/O al secondo?

È possibile convertire MB/s in operazioni di I/O al secondo utilizzando la formula seguente:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Come si modifica il livello di servizio di un volume?

La modifica del livello di servizio di un volume non è attualmente supportata.

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Come è possibile monitorare le prestazioni dei file NetApp di Azure?

File NetApp di Azure offre metriche sulle prestazioni del volume. È anche possibile usare Monitoraggio di Azure per monitorare le metriche di utilizzo per I file NetApp di Azure.You can also use Azure Monitor for monitoring usage metrics for Azure NetApp Files.  Vedere Metriche per i file NetApp di Azure per l'elenco delle metriche delle prestazioni per i file NetApp di Azure.See Metrics for [Azure NetApp Files](azure-netapp-files-metrics.md) for the list of performance metrics for Azure NetApp Files.

## <a name="nfs-faqs"></a>Domande frequenti su NFS

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Si vuole che un volume venga montato automaticamente quando una macchina virtuale di Azure viene avviata o riavviata.  Come si configura l'host per i volumi NFS persistenti?

Affinché un volume NFS venga montato automaticamente all'avvio o al riavvio della macchina virtuale, aggiungere una voce al `/etc/fstab` file nell'host. 

Per informazioni dettagliate, vedere [Montare o smontare un volume per macchine virtuali Windows o Linux.See Mount](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) or unmount a volume for Windows or Linux virtual machines for details.  

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Perché il comando DF sul client NFS non mostra la dimensione del volume di cui è stato eseguito il provisioning?

La dimensione del volume riportata in DF è la dimensione massima che il volume dei file di Rete di Azure può raggiungere. La dimensione del volume File NetApp di Azure in DF non riflette la quota o le dimensioni del volume.  È possibile ottenere la dimensione o la quota del volume dei file netApp di Azure tramite il portale di Azure o l'API.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Quale versione di NFS supporta i file NetApp di Azure?

Azure NetApp Files supports NFSv3 and NFSv4.1. È possibile [creare un volume](azure-netapp-files-create-volumes.md) utilizzando una versione NFS. 

### <a name="how-do-i-enable-root-squashing"></a>Come si abilita lo schiacciamento delle radici?

La squashing radice non è attualmente supportata.

## <a name="smb-faqs"></a>Domande frequenti su SMB

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>È necessaria una connessione Active Directory per l'accesso SMB? 

Sì, è necessario creare una connessione Active Directory prima di distribuire un volume SMB. I controller di dominio specificati devono essere accessibili dalla subnet delegata dei file di Azure NetApp per una connessione corretta.  Per informazioni [dettagliate, vedere Creare un volume SMB.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb) 

### <a name="how-many-active-directory-connections-are-supported"></a>Quante connessioni Active Directory sono supportate?

File NetApp di Azure non supporta più connessioni di Active Directory (AD) in una singola *area,* anche se le connessioni di Active Directory si trovano in account NetApp diversi. Tuttavia, è possibile avere più connessioni AD in una singola *sottoscrizione*, purché le connessioni di Active Directory si trovino in aree diverse. Se sono necessarie più connessioni AD in una singola area, è possibile usare sottoscrizioni separate a tale scopo. 

Viene configurata una connessione AD per ogni account NetApp; la connessione AD è visibile solo tramite l'account NetApp in cui viene creata.

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>I file netApp di Azure supportano Azure Active Directory? 

Sono supportati sia Servizi di [dominio Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory-domain-services/overview) che Servizi di dominio Active [Directory.](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) È possibile usare i controller di dominio di Active Directory esistenti con File di Azure NetApp.You can use existing Active Directory domain controllers with Azure NetApp Files. I controller di dominio possono risiedere in Azure come macchine virtuali o in locale tramite ExpressRoute o VPN S2S.Domain controllers can reside in Azure as virtual machines, or on premises via ExpressRoute or S2S VPN. File NetApp di Azure non supporta l'aggiunta ad Active Directory per [Azure Active Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/) in questo momento.

Se si usano file NetApp di Azure con Servizi di `OU=AADDC Computers` dominio Azure Active Directory, il percorso dell'unità organizzativa si verifica quando si configura Active Directory per l'account NetApp.

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>Quali versioni di Windows Server Active Directory sono supportate?

File NetApp di Azure supporta le versioni di Windows Server 2008r2SP1-2019 di Servizi di dominio Active Directory.

### <a name="why-does-the-available-space-on-my-smb-client-not-show-the-provisioned-size"></a>Perché lo spazio disponibile sul client SMB non mostra le dimensioni di cui è stato eseguito il provisioning?

La dimensione del volume segnalata dal client SMB è la dimensione massima che il volume file Di Azure NetApp può raggiungere. La dimensione del volume File NetApp di Azure come illustrato nel client SMB non riflette la quota o le dimensioni del volume. È possibile ottenere la dimensione o la quota del volume dei file netApp di Azure tramite il portale di Azure o l'API.

<!--
### Does Azure NetApp Files support Kerberos encryption?

Yes, by default, Azure NetApp Files supports both AES-128 and AES-256 encryption for traffic between the service and the targeted Active Directory domain controllers. See [Create an SMB volume for Azure NetApp Files](azure-netapp-files-create-volumes-smb.md) for requirements. 
-->

<!--
### Does Azure NetApp Files support LDAP signing? 

Yes, Azure NetApp Files supports LDAP signing by default. This functionality enables secure LDAP lookups between the Azure NetApp Files service and the user-specified [Active Directory Domain Services domain controllers](https://docs.microsoft.com/windows/win32/ad/active-directory-domain-services). For more information, see [ADV190023 | Microsoft Guidance for Enabling LDAP Channel Binding and LDAP Signing](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).
--> 

## <a name="capacity-management-faqs"></a>Domande frequenti sulla gestione della capacità

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Come è possibile monitorare l'utilizzo per il pool di capacità e il volume dei file NetApp di Azure? 

File NetApp di Azure offre metriche di utilizzo del volume e del pool di capacità. È anche possibile usare Monitoraggio di Azure per monitorare l'utilizzo dei file NetApp di Azure.You can also use Azure Monitor to monitor usage for Azure NetApp Files. Per informazioni [dettagliate,](azure-netapp-files-metrics.md) vedere Metriche per i file NetApp di Azure.See Metrics for Azure NetApp Files for details. 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>È possibile gestire i file NetApp di Azure tramite Azure Storage Explorer?

No. Azure NetApp Files is not supported by Azure Storage Explorer.

### <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>Come è possibile determinare se una directory si sta avvicinando alla dimensione limite?

È possibile `stat` utilizzare il comando da un client per verificare se una directory si avvicina al limite di dimensione massima (320 MB).

Per una directory di 320 MB, il numero di blocchi è 655360, con ogni dimensione del blocco di 512 byte.  (Ovvero, 320x1024x1024/512.)  

Esempi:

    [makam@cycrh6rtp07 ~]$ stat bin
    File: 'bin'
    Size: 4096            Blocks: 8          IO Block: 65536  directory

    [makam@cycrh6rtp07 ~]$ stat tmp
    File: 'tmp'
    Size: 12288           Blocks: 24         IO Block: 65536  directory
 
    [makam@cycrh6rtp07 ~]$ stat tmp1
    File: 'tmp1'
    Size: 4096            Blocks: 8          IO Block: 65536  directory

## <a name="data-migration-and-protection-faqs"></a>Domande frequenti sulla migrazione e la protezione dei dati

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Come si esegue la migrazione dei dati in File NetApp di Azure?
File NetApp di Azure offre volumi NFS e SMB.  È possibile utilizzare qualsiasi strumento di copia basato su file per eseguire la migrazione dei dati nel servizio. 

NetApp offre una soluzione basata su SaaS, [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service).  La soluzione consente di replicare i dati NFS o SMB nelle esportazioni NFS o condivisioni SMB di File di Rete di Azure.NetApp Files. 

È inoltre possibile utilizzare una vasta gamma di strumenti gratuiti per copiare i dati. Per NFS, è possibile usare strumenti di carico di lavoro come rsync per copiare e sincronizzare i dati di origine in un volume file Di Azure NetApp.For NFS, you can use workloads tools such as [rsync](https://rsync.samba.org/examples.html) to copy and synchronize source data into an Azure NetApp Files volume. Per SMB, è possibile utilizzare i carichi di lavoro [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) nello stesso modo.  Questi strumenti possono anche replicare le autorizzazioni di file o cartelle. 

I requisiti per la migrazione dei dati da file NetApp locali ad Azure sono i seguenti:The requirements for data migration from on ons to Azure NetApp Files are as follows: 

- Verificare che i file NetApp di Azure siano disponibili nell'area di Azure di destinazione.
- Convalidare la connettività di rete tra l'indirizzo IP del volume di destinazione di File di rete di Azure e l'indirizzo IP del volume di destinazione. Il trasferimento dei dati tra l'ambiente locale e il servizio File NetApp di Azure è supportato tramite ExpressRoute.Data transfer between ons ons and the Azure NetApp Files service is supported over ExpressRoute.
- Creare il volume file NetApp di Azure di destinazione.
- Trasferire i dati di origine nel volume di destinazione utilizzando lo strumento di copia dei file preferito.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Come si crea una copia di un volume File NetApp di Azure in un'altra area di Azure?
    
File NetApp di Azure offre volumi NFS e SMB.  Qualsiasi strumento di copia basata su file può essere usato per replicare i dati tra le aree di Azure.Any file based-copy tool can be used to replicate data between Azure regions. 

NetApp offre una soluzione basata su SaaS, [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service).  La soluzione consente di replicare i dati NFS o SMB nelle esportazioni NFS o condivisioni SMB di File di Rete di Azure.NetApp Files. 

È inoltre possibile utilizzare una vasta gamma di strumenti gratuiti per copiare i dati. Per NFS, è possibile usare strumenti di carico di lavoro come rsync per copiare e sincronizzare i dati di origine in un volume file Di Azure NetApp.For NFS, you can use workloads tools such as [rsync](https://rsync.samba.org/examples.html) to copy and synchronize source data into an Azure NetApp Files volume. Per SMB, è possibile utilizzare i carichi di lavoro [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) nello stesso modo.  Questi strumenti possono anche replicare le autorizzazioni di file o cartelle. 

I requisiti per la replica di un volume di file NetApp di Azure in un'altra area di Azure sono i seguenti:The requirements for replicating an Azure NetApp Files volume to another Azure region are as follows: 
- Verificare che i file NetApp di Azure siano disponibili nell'area di Azure di destinazione.
- Convalidare la connettività di rete tra reti virtuali in ogni area. Attualmente, il peering globale tra reti virtuali non è supportato.  È possibile stabilire la connettività tra reti virtuali tramite il collegamento con un circuito ExpressRoute o usando una connessione VPN S2S.You can establish connectivity between VNets by linking with an ExpressRoute circuit or using a S2S VPN connection. 
- Creare il volume file NetApp di Azure di destinazione.
- Trasferire i dati di origine nel volume di destinazione utilizzando lo strumento di copia dei file preferito.

### <a name="is-migration-with-azure-data-box-supported"></a>La migrazione con Azure Data Box è supportata?

No. Azure Data Box attualmente non supporta i file NetApp di Azure.Azure Data Box does not support Azure NetApp Files currently. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>La migrazione con il servizio Importazione/Esportazione di Azure è supportata?

No. Il servizio Importazione/Esportazione di Azure attualmente non supporta i file NetApp di Azure.Azure Import/Export service does not support Azure NetApp Files currently.

## <a name="next-steps"></a>Passaggi successivi  

- [Domande frequenti su Microsoft Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [Microsoft Azure Virtual Network FAQ](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [Come creare una richiesta di supporto di AzureHow to create an Azure support request](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)
- [Azure Data Box](https://docs.microsoft.com/azure/databox-family/)
- [Domande frequenti sulle prestazioni SMB per i file NetApp di Azure](azure-netapp-files-smb-performance.md)
