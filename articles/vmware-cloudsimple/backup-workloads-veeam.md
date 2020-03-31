---
title: Azure VMware Solution by CloudSimple - Back up workload virtual machines on Private Cloud using Veeam
description: Descrive come eseguire il backup delle macchine virtuali in esecuzione in un cloud privato cloud basato su Azure usando Veeam B&R 9.5
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3262841efb9109b1de24fe501ea0a7bea0dd612d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025130"
---
# <a name="back-up-workload-vms-on-cloudsimple-private-cloud-using-veeam-br"></a>Eseguire il backup delle macchine virtuali del carico di lavoro nel cloudSimple Private Cloud usando Veeam B&R

Questa guida descrive come eseguire il backup delle macchine virtuali in esecuzione in un cloud privato CloudSimple basato su Azure usando Veeam B&R 9.5.This guide describes how you can back up your virtual machines that are running in an Azure-based CloudSimple Private Cloud by using Veeam B&R 9.5.

## <a name="about-the-veeam-back-up-and-recovery-solution"></a>Informazioni sulla soluzione di backup e ripristino di Veeam

La soluzione Veeam include i componenti seguenti.

**Backup Server**

Il server di backup è un server Windows (VM) che funge da centro di controllo per Veeam ed esegue queste funzioni: 

* Coordina le attività di backup, replica, verifica del ripristino e ripristino
* Controlla la programmazione dei processi e l'allocazione delle risorse
* Consente di configurare e gestire i componenti dell'infrastruttura di backup e di specificare le impostazioni globali per l'infrastruttura di backup

**Server proxy**

I server proxy vengono installati tra il server di backup e altri componenti dell'infrastruttura di backup. Gestiscono le seguenti funzioni:

* Recupero dei dati della macchina virtuale dall'archiviazione di produzioneRetrieval of VM data from the production storage
* Compressione
* Deduplicazione
* Crittografia
* Trasmissione dei dati al repository di backup

**Archivio di backup**

Il repository di backup è il percorso di archiviazione in cui Veeam mantiene i file di backup, le copie delle macchine virtuali e i metadati per le macchine virtuali replicate.  Il repository può essere un server Windows o Linux con dischi locali (o NFS/SMB montati) o un'appliance di deduplicazione dell'archiviazione hardware.

### <a name="veeam-deployment-scenarios"></a>Scenari di distribuzione di VeeamVeeam deployment scenarios
È possibile sfruttare Azure per fornire un repository di backup e una destinazione di archiviazione per il backup e l'archiviazione a lungo termine. Tutto il traffico di rete di backup tra le macchine virtuali nel cloud privato e il repository di backup in Azure si sposta su un collegamento a larghezza di banda elevata e bassa latenza. Il traffico di replica tra aree passa attraverso la rete backplane interna di Azure, riducendo così i costi della larghezza di banda per gli utenti.

**Distribuzione di base**

Per gli ambienti con meno di 30 TB di cui eseguire il backup, CloudSimple consiglia la configurazione seguente:

* Server di backup Veeam e server proxy installati nella stessa macchina virtuale nel cloud privato.
* Un repository di backup primario basato su Linux in Azure configurato come destinazione per i processi di backup.
* `azcopy`usato per copiare i dati dal repository di backup primario a un contenitore BLOB di Azure che viene replicato in un'altra area.

![Scenari di distribuzione di base](media/veeam-basicdeployment.png)

**Distribuzione avanzata**

Per gli ambienti con più di 30 TB di cui eseguire il backup, CloudSimple consiglia la configurazione seguente:

* Un server proxy per nodo nel cluster vSAN, come consigliato da Veeam.
* Repository di backup primario basato su Windows nel cloud privato per memorizzare nella cache cinque giorni di dati per ripristini veloci.
* Repository di backup Linux in Azure come destinazione per i processi di copia di backup per la conservazione a più prolungata. Questo repository deve essere configurato come repository di backup con scalabilità orizzontale.
* `azcopy`usato per copiare i dati dal repository di backup primario a un contenitore BLOB di Azure che viene replicato in un'altra area.

![Scenari di distribuzione di base](media/veeam-advanceddeployment.png)

Nella figura precedente, si noti che il proxy di backup è una macchina virtuale con accesso Hot Add ai dischi VM del carico di lavoro nell'archivio dati vSAN. Veeam utilizza la modalità di trasporto proxy di backup dell'appliance virtuale per vSAN.

## <a name="requirements-for-veeam-solution-on-cloudsimple"></a>Requisiti per la soluzione Veeam su CloudSimple

La soluzione Veeam richiede di effettuare le seguenti operazioni:

* Fornisci le tue licenze Veeam.
* Distribuire e gestire Veeam per eseguire il backup dei carichi di lavoro in esecuzione nel cloud privato CloudSimple.

Questa soluzione offre il controllo completo sullo strumento di backup Veeam e offre la possibilità di utilizzare l'interfaccia Veeam nativa o il plug-in Veeam vCenter per gestire i processi di backup della macchina virtuale.

Se si è un utente Veeam esistente, è possibile ignorare la sezione relativa ai componenti della soluzione Veeam e passare direttamente a [Veeam Deployment Scenarios](#veeam-deployment-scenarios).

## <a name="install-and-configure-veeam-backups-in-your-cloudsimple-private-cloud"></a>Installare e configurare i backup di Veeam nel cloudSimple Private Cloud

Nelle sezioni seguenti viene descritto come installare e configurare una soluzione di backup Veeam per il cloud privato CloudSimple.The following sections describe how to install and configure a Veeam backup solution for your CloudSimple Private Cloud.

Il processo di distribuzione è costituito dai seguenti passaggi:

1. [Interfaccia utente di vCenter: configurare i servizi di infrastruttura nel cloud privato](#vcenter-ui-set-up-infrastructure-services-in-your-private-cloud)
2. [Portale CloudSimple: configurare la rete Cloud privata per Veeam](#cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam)
3. [Portale CloudSimple: Escalate Privileges](#cloudsimple-private-cloud-escalate-privileges-for-cloudowner)
4. [Portale di Azure: Connettere la rete virtuale al cloud privatoAzure portal: Connect your virtual network to the Private Cloud](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
5. [Azure portal: Create a backup repository in Azure](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
6. [Portale di Azure: configurare l'archiviazione BLOB di Azure per la conservazione dei dati a lungo termineAzure portal: Configure Azure blob storage for long term data retention](#configure-azure-blob-storage-for-long-term-data-retention)
7. [Interfaccia utente vCenter di Private Cloud: Installare Veeam B&R](#vcenter-console-of-private-cloud-install-veeam-br)
8. [Veeam Console: Configurare il software Veeam Backup & Recovery](#veeam-console-install-veeam-backup-and-recovery-software)
9. [Portale CloudSimple: configurare l'accesso Veeam e desalire l'escalation](#cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges)

### <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare la distribuzione di Veeam, è necessario quanto segue:

* Una sottoscrizione di Azure di proprietà dell'utente
* Un gruppo di risorse di Azure creato in precedenaA pre-created Azure resource group
* Una rete virtuale di Azure nella sottoscrizioneAn Azure virtual network in your subscription
* Un account di archiviazione di Azure
* Un [cloud privato](create-private-cloud.md) creato utilizzando il portale CloudSimple.  

Durante la fase di implementazione sono necessari i seguenti elementi:

* Modelli VMware per Windows per installare Veeam (ad esempio Windows Server 2012 R2 - immagine a 64 bit)
* Una VLAN disponibile identificata per la rete di backup
* CIDR della subnet da assegnare alla rete di backup
* Veeam 9.5 u3 supporti installabili (ISO) caricati nell'archivio dati vSAN del Cloud Privato

### <a name="vcenter-ui-set-up-infrastructure-services-in-your-private-cloud"></a>Interfaccia utente di vCenter: configurare i servizi di infrastruttura nel cloud privato

Configura i servizi di infrastruttura nel cloud privato per semplificare la gestione dei carichi di lavoro e degli strumenti.

* È possibile aggiungere un provider di identità esterno come descritto in Configurare le origini di [identità del vCenter per l'utilizzo](set-vcenter-identity.md) di Active Directory se si verifica una delle condizioni seguenti:

  * Si desidera identificare gli utenti da Active Directory (AD) locale nel cloud privato.
  * Si desidera configurare un Active Directory nel cloud privato per tutti gli utenti.
  * Si vuole usare Azure AD.
* Per fornire servizi di ricerca degli indirizzi IP, gestione degli indirizzi IP e risoluzione dei nomi per i carichi di lavoro nel cloud privato, configurare un server DHCP e DNS come descritto in [Configurare applicazioni e carichi di lavoro DNS e DHCP nel cloud privato CloudSimple](dns-dhcp-setup.md).

### <a name="cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam"></a>CloudSimple Private Cloud: Configurare la rete cloud privata per Veeam

Accedi al portale CloudSimple per configurare la rete Cloud privata per la soluzione Veeam.

Creare una VLAN per la rete di backup e assegnarle un CIDR di subnet. Per istruzioni, vedere [Creare e gestire VLAN/Subnets](create-vlan-subnet.md).

Creare regole firewall tra la subnet di gestione e la rete di backup per consentire il traffico di rete sulle porte utilizzate da Veeam. Vedere l'argomento Veeam [Porte utilizzate](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95). Per istruzioni sulla creazione di regole firewall, consultate [Configurare tabelle e regole del firewall.](firewall.md)

Nella tabella seguente viene fornito un elenco di porte.

| Icona | Descrizione | Icona | Descrizione |
| ------------ | ------------- | ------------ | ------------- |
| Backup Server  | vCenter  | HTTPS / TCP  | 443 |
| Backup Server <br> *Necessario per la distribuzione dei componenti di Veeam Backup & Replica* | Backup Proxy  | TCP/UDP  | 135, 137 a 139 e 445 |
    | Backup Server   | DNS  | UDP  | 53  | 
    | Backup Server   | Server di notifica aggiornamento Veeam  | TCP  | 80  | 
    | Backup Server   | Server di aggiornamento licenze Veeam  | TCP  | 443  | 
    | Backup Proxy   | vCenter |   |   | 
    | Backup Proxy  | Archivio di backup Linux   | TCP  | 22  | 
    | Backup Proxy  | Archivio di Windows Backup  | TCP  | 49152 - 65535   | 
    | Archivio di backup  | Backup Proxy  | TCP  | 2500 -5000  | 
    | Archivio di backup di origine<br> *Utilizzato per i processi di copia di backup*  | Archivio di backup di destinazione  | TCP  | 2500 - 5000  | 

Creare regole del firewall tra la subnet del carico di lavoro e la rete di backup come descritto in [Configurare](firewall.md)le tabelle e le regole del firewall .  Per il backup e il ripristino in grado di riconoscere le applicazioni, è necessario aprire [porte aggiuntive](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95) nelle macchine virtuali del carico di lavoro che ospitano applicazioni specifiche.

Per impostazione predefinita, CloudSimple fornisce un collegamento ExpressRoute di 1 Gbps. Per ambienti di dimensioni maggiori, potrebbe essere necessario un collegamento di larghezza di banda superiore. Contattare il supporto di Azure per altre informazioni sui collegamenti con larghezza di banda maggiore.

To continue the setup, you need the authorization key and peer circuit URI and access to your Azure Subscription.  Queste informazioni sono disponibili nella pagina Connessione di rete virtuale nel portale CloudSimple.This information is available on the Virtual Network Connection page in the CloudSimple portal. Per istruzioni, vedere Ottenere informazioni sul peering per la [rete virtuale di Azure alla connessione CloudSimple](virtual-network-connection.md). In caso di problemi nell'ottenere le informazioni, [contattare](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)il supporto tecnico .

### <a name="cloudsimple-private-cloud-escalate-privileges-for-cloudowner"></a>CloudSimple Private Cloud: escalation dei privilegi per il cloudowner

L'utente predefinito 'cloudowner' non dispone di privilegi sufficienti nel vCenter di Private Cloud per installare VEEAM, pertanto i privilegi di vCenter dell'utente devono essere riassegnati. Per ulteriori informazioni, consultate [Riassegnare i privilegi.](escalate-private-cloud-privileges.md)

### <a name="azure-portal-connect-your-virtual-network-to-the-private-cloud"></a>Portale di Azure: Connettere la rete virtuale al cloud privatoAzure portal: Connect your virtual network to the Private Cloud

Connettere la rete virtuale al cloud privato seguendo le istruzioni in Connessione di rete virtuale di [Azure tramite ExpressRoute](azure-expressroute-connection.md).

### <a name="azure-portal-create-a-backup-repository-vm"></a>Portale di Azure: creare una macchina virtuale del repository di backupAzure portal: Create a backup repository VM

1. Creare una macchina virtuale D2 v3 standard con (2 vCPU e 8 GB di memoria).
2. Selezionare l'immagine basata su CentOS 7.4.
3. Configurare un gruppo di sicurezza di rete per la macchina virtuale. Verificare che la macchina virtuale non disponga di un indirizzo IP pubblico e non sia raggiungibile da Internet pubblica.
4. Creare un account utente basato su nome utente e password per la nuova macchina virtuale. Per istruzioni, vedere Creare una macchina virtuale Linux nel portale di Azure.For instructions, see Create a [Linux virtual machine in the Azure portal.](../virtual-machines/linux/quick-create-portal.md)
5. Creare un HDD standard 1x512 GiB e collegarlo alla macchina virtuale del repository.  Per istruzioni, vedere Come collegare un disco dati gestito a una macchina virtuale Windows nel portale di Azure.For instructions, see [How to attach a managed data disk to a Windows VM in the Azure portal](../virtual-machines/windows/attach-managed-disk-portal.md).
6. [Creare un volume XFS sul disco gestito.](https://www.digitalocean.com/docs/volumes/how-to/) Accedere alla macchina virtuale utilizzando le credenziali menzionate in precedenza. Eseguire lo script seguente per creare un volume logico, aggiungervi il disco, creare una [partizione](https://www.digitalocean.com/docs/volumes/how-to/partition/) del file system XFS e [montare](https://www.digitalocean.com/docs/volumes/how-to/mount/) la partizione nel percorso /backup1.

    Script di esempio:

    ```
    sudo pvcreate /dev/sdc
    sudo vgcreate backup1 /dev/sdc
    sudo lvcreate -n backup1 -l 100%FREE backup1
    sudo mkdir -p /backup1
    sudo chown veeamadmin /backup1
    sudo chmod 775 /backup1
    sudo mkfs.xfs -d su=64k -d sw=1 -f /dev/mapper/backup1-backup1
    sudo mount -t xfs /dev/mapper/backup1-backup1 /backup1
    ```

7. Esporre /backup1 come punto di montaggio NFS al server di backup Veeam in esecuzione nel cloud privato. Per istruzioni, vedere l'articolo Digital Ocean [How to Set Up an NFS Mount on CentOS 6](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-centos-6). Utilizzare questo nome di condivisione NFS quando si configura il repository di backup nel server di backup Veeam.

8. Configurare le regole di filtro nel gruppo di sicurezza di rete per la macchina virtuale del repository di backup per consentire in modo esplicito tutto il traffico di rete da e verso la macchina virtuale.

> [!NOTE]
> Veeam Backup & Replica utilizza il protocollo SSH per comunicare con i repository di backup Linux e richiede l'utilità SCP nei repository Linux. Verificare che il daemon SSH sia configurato correttamente e che SCP sia disponibile nell'host Linux.

### <a name="configure-azure-blob-storage-for-long-term-data-retention"></a>Configurare l'archiviazione BLOB di Azure per la conservazione dei dati a lungo termineConfigure Azure blob storage for long term data retention

1. Creare un account di archiviazione generico (GPv2) di tipo standard e un contenitore BLOB come descritto nel video Microsoft Introduzione ad Archiviazione di Azure.Create a general purpose storage account (GPv2) of standard type and a blob container as described in the Microsoft video [Getting Started with Azure Storage](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage).
2. Creare un contenitore di archiviazione di Azure, come descritto in [Riferimento di Creazione contenitore.](https://docs.microsoft.com/rest/api/storageservices/create-container)
2. Scaricare `azcopy` l'utilità della riga di comando per Linux da Microsoft.Download the command line utility for Linux from Microsoft. È possibile utilizzare i seguenti comandi nella shell bash in CentOS 7.5.

    ```
    wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinux64
    tar -xf azcopy.tar.gz
    sudo ./install.sh
    sudo yum -y install libunwind.x86_64
    sudo yum -y install icu
    ```

3. Usare `azcopy` il comando per copiare i file di backup da e verso il contenitore BLOB.  Vedere [Trasferire dati con AzCopy su Linux](../storage/common/storage-use-azcopy-linux.md) per i comandi dettagliati.

### <a name="vcenter-console-of-private-cloud-install-veeam-br"></a>console vCenter di Private Cloud: Installare Veeam B&R

Accedi a vCenter dal tuo cloud privato per creare un account del servizio Veeam, installare Veeam B&R 9.5 e configurare Veeam utilizzando l'account del servizio.

1. Creare un nuovo ruolo denominato "Veeam Backup Role" e assegnargli le autorizzazioni necessarie come consigliato da Veeam. Per informazioni dettagliate, vedere l'argomento Veeam [Autorizzazioni necessarie](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95).
2. Creare un nuovo gruppo 'Veeam User Group' in vCenter e assegnargli il "Veeam Backup Role".
3. Creare un nuovo utente 'Veeam Service Account' e aggiungerlo al 'Veeam User Group'.

    ![Creazione di un account del servizio VeeamCreating a Veeam service account](media/veeam-vcenter01.png)

4. Creare un gruppo di porte distribuite in vCenter utilizzando la Rete VLAN della rete di backup. Per informazioni dettagliate, vedere il video di VMware Creazione di un gruppo di [porte distribuite nel client Web vSphere](https://www.youtube.com/watch?v=wpCd5ZbPOpA).
5. Creare le macchine virtuali per i server di backup e proxy Veeam in vCenter in base ai requisiti di sistema di [Veeam](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95). È possibile utilizzare Windows 2012 R2 o Linux. Per ulteriori informazioni, vedere [Requisiti per l'utilizzo](https://www.veeam.com/kb2216)dei repository di backup Linux.
6. Montare l'ISO Veeam installabile come dispositivo CDROM nella macchina virtuale del server di backup Veeam.
7. Utilizzando una sessione RDP per il computer Windows 2012 R2 (la destinazione per l'installazione di Veeam), [installare Veeam B&R 9.5u3](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95) in una macchina virtuale Windows 2012 R2.
8. Individuare l'indirizzo IP interno della macchina virtuale del server di backup Veeam e configurare l'indirizzo IP in modo che sia statico nel server DHCP. I passaggi esatti necessari per eseguire questa operazione dipendono dal server DHCP. Ad esempio, l'articolo di Netgate <a href="https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html" target="_blank">mapping DHCP statici</a> spiega come configurare un server DHCP utilizzando un router pfSense.

### <a name="veeam-console-install-veeam-backup-and-recovery-software"></a>Console Veeam: installare il software di backup e ripristino Veeam

Utilizzando la console Veeam, configurare il software di backup e ripristino Veeam. Per informazioni [dettagliate, vedere Veeam Backup & Replication v9 - Installazione e distribuzione](https://www.youtube.com/watch?v=b4BqC_WXARk).

1. Aggiungere VMware vSphere come ambiente server gestito. Quando richiesto, fornire le credenziali dell'account del servizio Veeam creato all'inizio di [vCenter Console of Private Cloud: Install Veeam B&R](#vcenter-console-of-private-cloud-install-veeam-br).

    * Utilizzare le impostazioni predefinite per il controllo del carico e le impostazioni avanzate predefinite.
    * Impostare il percorso del server di montaggio come server di backup.
    * Modificare il percorso di backup della configurazione per il server Veeam nel repository remoto.

2. Aggiungere il server Linux in Azure come repository di backup.

    * Utilizzare le impostazioni predefinite per il controllo del carico e per le impostazioni avanzate. 
    * Impostare il percorso del server di montaggio come server di backup.
    * Modificare il percorso di backup della configurazione per il server Veeam nel repository remoto.

3. Abilitare la crittografia del backup della configurazione utilizzando **Home> Impostazioni di backup della configurazione**.

4. Aggiungere una macchina virtuale del server Windows come server proxy per l'ambiente VMware.Add a Windows server VM as a proxy server for VMware environment. Utilizzando 'Regole di traffico' per un proxy, crittografare i dati di backup in rete.

5. Configurare i processi di backup.
    * Per configurare i processi di backup, seguire le istruzioni in [Creazione di un processo](https://www.youtube.com/watch?v=YHxcUFEss4M)di backup .
    * Abilitare la crittografia dei file di backup in **Impostazioni avanzate > archiviazione**.

6. Configurare i processi di copia di backup.

    * Per configurare i processi di copia di backup, seguire le istruzioni nel video [Creazione di un](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)processo di copia di backup .
    * Abilitare la crittografia dei file di backup in **Impostazioni avanzate > archiviazione**.

### <a name="cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges"></a>Portale CloudSimple: configurare l'accesso Veeam e desalire l'escalation
Creare un indirizzo IP pubblico per il server di backup e ripristino Veeam. Per istruzioni, consultate [Allocare indirizzi IP pubblici.](public-ips.md)

Creare una regola del firewall utilizzando per consentire al server di backup Veeam di creare una connessione in uscita al sito Web Veeam per il download di aggiornamenti/patch sulla porta TCP 80. Per istruzioni, consultate [Configurare tabelle e regole del firewall.](firewall.md)

Per decennare i privilegi, vedere [De-escalation dei privilegi](escalate-private-cloud-privileges.md#de-escalate-privileges).

## <a name="references"></a>Riferimenti

### <a name="cloudsimple-references"></a>Riferimenti CloudSimple

* [Creare un cloud privato](create-private-cloud.md)
* [Creare e gestire VLAN/subnet](create-vlan-subnet.md)
* [Origini identità di vCenter](set-vcenter-identity.md)
* [Configurazione DNS e DHCP del carico di lavoro](dns-dhcp-setup.md)
* [Riassegnare i privilegi](escalate-privileges.md)
* [Configurare tabelle e regole del firewall](firewall.md)
* [Autorizzazioni per il cloud privato](learn-private-cloud-permissions.md)
* [Allocare indirizzi IP pubblici](public-ips.md)

### <a name="veeam-references"></a>Riferimenti Veeam

* [Porte utilizzate](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)
* [Autorizzazioni necessarie](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95)
* [Requisiti di sistema](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95)
* [Installazione della replica & di backup di Veeam](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95)
* [Moduli e autorizzazioni necessari per il supporto di Multi-OS FLR e Repository per Linux](https://www.veeam.com/kb2216)
* [Veeam Backup & Replica v9 - Installazione e distribuzione - Video](https://www.youtube.com/watch?v=b4BqC_WXARk)
* [Veeam v9 Creazione di un processo di backup - Video](https://www.youtube.com/watch?v=YHxcUFEss4M)
* [Veeam v9 Creazione di un processo di copia di backup - Video](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)

### <a name="azure-references"></a>Riferimenti di AzureAzure references

* [Configurare un gateway di rete virtuale per ExpressRoute usando il portale di Azure](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
* [Connettere una rete virtuale a un circuito: diversa sottoscrizione](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)
* [Creare una macchina virtuale Linux nel portale di Azure](../virtual-machines/linux/quick-create-portal.md)
* [Collegare un disco dati gestito a una macchina virtuale Windows nel portale di Azure](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Introduzione ad Archiviazione di Azure - Video](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage)
* [Create Container](https://docs.microsoft.com/rest/api/storageservices/create-container)
* [Trasferire dati con AzCopy in Linux](../storage/common/storage-use-azcopy-linux.md)

### <a name="vmware-references"></a>Riferimenti di VMware

* [Creazione di un gruppo di porte distribuite nel client Web vSphere - Video](https://www.youtube.com/watch?v=wpCd5ZbPOpA)

### <a name="other-references"></a>Altri riferimenti

* [Creare un volume XFS sul disco gestito - RedHatCreate an XFS volume on the managed disk - RedHat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/ch-xfs)
* [Come impostare un monte NFS su CentOS 7 - HowToForge](https://www.howtoforge.com/nfs-server-and-client-on-centos-7)
* [Configurazione del server DHCP - Netgate](https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html)
