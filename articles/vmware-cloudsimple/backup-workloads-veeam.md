---
title: 'Soluzione VMware di Azure di CloudSimple: eseguire il backup di macchine virtuali del carico di lavoro nel cloud privato con Veeam'
description: Viene descritto come eseguire il backup delle macchine virtuali in esecuzione in un cloud privato CloudSimple basato su Azure usando Veeam B & R 9,5
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3414cc54e5023bdeebb2d5536c1408f981e68f19
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73891390"
---
# <a name="back-up-workload-vms-on-cloudsimple-private-cloud-using-veeam-br"></a>Eseguire il backup di macchine virtuali del carico di lavoro nel cloud privato CloudSimple usando Veeam B & R

Questa guida descrive come è possibile eseguire il backup delle macchine virtuali in esecuzione in un cloud privato CloudSimple basato su Azure usando Veeam B & R 9,5.

## <a name="about-the-veeam-back-up-and-recovery-solution"></a>Informazioni sulla soluzione di backup e ripristino di Veeam

La soluzione Veeam include i componenti seguenti.

**Server di backup**

Il server di backup è un server Windows (VM) che funge da centro di controllo per Veeam ed esegue queste funzioni: 

* Coordina il backup, la replica, la verifica del ripristino e le attività di ripristino
* Controlla la pianificazione di processi e l'allocazione delle risorse
* Consente di configurare e gestire i componenti dell'infrastruttura di backup e di specificare le impostazioni globali per l'infrastruttura di backup

**Server proxy**

I server proxy vengono installati tra il server di backup e altri componenti dell'infrastruttura di backup. Gestiscono le funzioni seguenti:

* Recupero dei dati della macchina virtuale dalla risorsa di archiviazione di produzione
* Compressione
* Deduplicazione
* Crittografia
* Trasmissione di dati al repository di backup

**Archivio di backup**

Il repository di backup è il percorso di archiviazione in cui Veeam mantiene i file di backup, le copie della macchina virtuale e i metadati per le VM replicate.  Il repository può essere un server Windows o Linux con dischi locali (o NFS/SMB montati) o un'appliance di deduplicazione dell'hardware di archiviazione.

### <a name="veeam-deployment-scenarios"></a>Scenari di distribuzione Veeam
È possibile sfruttare Azure per fornire un repository di backup e una destinazione di archiviazione per il backup e l'archiviazione a lungo termine. Tutto il traffico di rete di backup tra le macchine virtuali nel cloud privato e il repository di backup in Azure si sposta su un collegamento a larghezza di banda elevata e a bassa latenza. Il traffico di replica tra le aree passa attraverso la rete interna del backplane di Azure, che riduce i costi di larghezza di banda per gli utenti.

**Distribuzione di base**

Per gli ambienti con meno di 30 TB per il backup, CloudSimple consiglia la configurazione seguente:

* Server di backup Veeam e server proxy installati nella stessa macchina virtuale nel cloud privato.
* Un repository di backup primario basato su Linux in Azure configurato come destinazione per i processi di backup.
* `azcopy` usato per copiare i dati dal repository di backup primario a un contenitore BLOB di Azure che viene replicato in un'altra area.

![Scenari di distribuzione di base](media/veeam-basicdeployment.png)

**Distribuzione avanzata**

Per gli ambienti con oltre 30 TB per il backup, CloudSimple consiglia la configurazione seguente:

* Un server proxy per nodo nel cluster rete VSAN, come consigliato da Veeam.
* Archivio di backup primario basato su Windows nel cloud privato per memorizzare nella cache cinque giorni di dati per ripristini rapidi.
* Archivio di backup Linux in Azure come destinazione per i processi di copia di backup per un periodo di conservazione più lungo. Questo repository deve essere configurato come archivio di backup con scalabilità orizzontale.
* `azcopy` usato per copiare i dati dal repository di backup primario a un contenitore BLOB di Azure che viene replicato in un'altra area.

![Scenari di distribuzione di base](media/veeam-advanceddeployment.png)

Nella figura precedente si noti che il proxy di backup è una macchina virtuale con accesso a caldo per i dischi delle VM del carico di lavoro nell'archivio dati rete VSAN. Veeam usa la modalità di trasporto del proxy di backup dell'appliance virtuale per rete VSAN.

## <a name="requirements-for-veeam-solution-on-cloudsimple"></a>Requisiti per la soluzione Veeam in CloudSimple

Per la soluzione Veeam è necessario eseguire le operazioni seguenti:

* Fornisci le tue licenze Veeam.
* Distribuire e gestire Veeam per eseguire il backup dei carichi di lavoro in esecuzione nel cloud privato CloudSimple.

Questa soluzione offre il controllo completo dello strumento Veeam Backup e offre la possibilità di usare l'interfaccia Veeam nativa o il plug-in Veeam vCenter per gestire i processi di backup delle macchine virtuali.

Se si è un utente Veeam esistente, è possibile ignorare la sezione relativa ai componenti della soluzione Veeam e proseguire direttamente con gli [scenari di distribuzione di Veeam](#veeam-deployment-scenarios).

## <a name="install-and-configure-veeam-backups-in-your-cloudsimple-private-cloud"></a>Installare e configurare i backup Veeam nel cloud privato CloudSimple

Le sezioni seguenti descrivono come installare e configurare una soluzione di backup Veeam per il cloud privato CloudSimple.

Il processo di distribuzione è costituito dai passaggi seguenti:

1. [interfaccia utente di vCenter: configurare i servizi di infrastruttura nel cloud privato](#vcenter-ui-set-up-infrastructure-services-in-your-private-cloud)
2. [Portale di CloudSimple: configurare la rete cloud privata per Veeam](#cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam)
3. [Portale di CloudSimple: escalation dei privilegi](#cloudsimple-private-cloud-escalate-privileges-for-cloudowner)
4. [Portale di Azure: connettere la rete virtuale al cloud privato](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
5. [Portale di Azure: creare un repository di backup in Azure](#azure-portal-connect-your-virtual-network-to-the-private-cloud)
6. [Portale di Azure: configurare l'archivio BLOB di Azure per la conservazione dei dati a lungo termine](#configure-azure-blob-storage-for-long-term-data-retention)
7. [interfaccia utente di vCenter del cloud privato: installare Veeam B & R](#vcenter-console-of-private-cloud-install-veeam-br)
8. [Console Veeam: configurare il backup Veeam & software di ripristino](#veeam-console-install-veeam-backup-and-recovery-software)
9. [Portale di CloudSimple: configurare i privilegi di accesso Veeam e decalation](#cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges)

### <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare la distribuzione di Veeam sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure di proprietà dell'utente
* Un gruppo di risorse di Azure creato in precedenza
* Una rete virtuale di Azure nella sottoscrizione
* Un account di archiviazione di Azure
* Un [cloud privato](create-private-cloud.md) creato tramite il portale CloudSimple.  

Durante la fase di implementazione sono necessari gli elementi seguenti:

* Modelli VMware per Windows per installare Veeam (ad esempio Windows Server 2012 R2-immagine bit 64)
* Una VLAN disponibile identificata per la rete di backup
* CIDR della subnet da assegnare alla rete di backup
* Media installabile Veeam 9,5 U3 (ISO) caricato nell'archivio dati rete VSAN del cloud privato

### <a name="vcenter-ui-set-up-infrastructure-services-in-your-private-cloud"></a>interfaccia utente di vCenter: configurare i servizi di infrastruttura nel cloud privato

Configurare i servizi di infrastruttura nel cloud privato per semplificare la gestione dei carichi di lavoro e degli strumenti.

* È possibile aggiungere un provider di identità esterno come descritto in [configurare le origini di identità vCenter da usare Active Directory](set-vcenter-identity.md) se si verifica una delle condizioni seguenti:

  * Si vuole identificare gli utenti dal Active Directory locale (AD) nel cloud privato.
  * Si vuole configurare un annuncio nel cloud privato per tutti gli utenti.
  * Si desidera utilizzare Azure AD.
* Per fornire servizi di ricerca di indirizzi IP, gestione indirizzi IP e risoluzione dei nomi per i carichi di lavoro nel cloud privato, configurare un server DHCP e DNS come descritto in [configurare le applicazioni e i carichi di lavoro DNS e DHCP nel cloud privato CloudSimple](dns-dhcp-setup.md).

### <a name="cloudsimple-private-cloud-set-up-private-cloud-networking-for-veeam"></a>Cloud privato CloudSimple: configurare la rete cloud privata per Veeam

Accedere al portale di CloudSimple per configurare la rete cloud privata per la soluzione Veeam.

Creare una VLAN per la rete di backup e assegnarle un CIDR della subnet. Per istruzioni, vedere [creare e gestire VLAN/subnet](create-vlan-subnet.md).

Creare regole del firewall tra la subnet di gestione e la rete di backup per consentire il traffico di rete sulle porte usate da Veeam. Vedere l'argomento Veeam [porte usate](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95). Per istruzioni sulla creazione di regole del firewall, vedere [configurare le regole e le tabelle del firewall](firewall.md).

Nella tabella seguente viene fornito un elenco di porte.

| Icona | Descrizione | Icona | Descrizione |
| ------------ | ------------- | ------------ | ------------- |
| Server di backup  | vCenter  | HTTPS/TCP  | 443 |
| Server di backup <br> *Obbligatorio per la distribuzione di Veeam Backup & componenti di replica* | Proxy di backup  | TCP/UDP  | 135, da 137 a 139 e 445 |
    | Server di backup   | DNS  | UDP  | 53  | 
    | Server di backup   | Server di notifica di aggiornamento Veeam  | TCP  | 80  | 
    | Server di backup   | Server di aggiornamento licenze Veeam  | TCP  | 443  | 
    | Proxy di backup   | vCenter |   |   | 
    | Proxy di backup  | Repository di backup Linux   | TCP  | 22  | 
    | Proxy di backup  | Archivio di backup di Windows  | TCP  | 49152-65535   | 
    | Archivio di backup  | Proxy di backup  | TCP  | 2500-5000  | 
    | Repository di backup di origine<br> *Usato per i processi di copia di backup*  | Repository di backup di destinazione  | TCP  | 2500-5000  | 

Creare regole del firewall tra la subnet del carico di lavoro e la rete di backup, come descritto in [configurare le regole e le tabelle del firewall](firewall.md).  Per il backup e il ripristino in grado di riconoscere le applicazioni, è necessario aprire [porte aggiuntive](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95) sulle VM del carico di lavoro che ospitano applicazioni specifiche.

Per impostazione predefinita, CloudSimple fornisce un collegamento 1 Gbps ExpressRoute. Per le dimensioni di ambiente più grandi, potrebbe essere necessario un collegamento con una maggiore larghezza di banda. Per ulteriori informazioni sui collegamenti con larghezza di banda più elevata, contattare il supporto di Azure.

Per continuare l'installazione, sono necessari la chiave di autorizzazione e l'URI del circuito peer e l'accesso alla sottoscrizione di Azure.  Queste informazioni sono disponibili nella pagina connessione della rete virtuale nel portale di CloudSimple. Per istruzioni, vedere [ottenere informazioni sul peering per la rete virtuale di Azure per la connessione CloudSimple](virtual-network-connection.md). Se si verificano problemi durante il recupero delle informazioni, [contattare il supporto tecnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="cloudsimple-private-cloud-escalate-privileges-for-cloudowner"></a>Cloud privato CloudSimple: escalation dei privilegi per cloudowner

L'utente ' cloudowner ' predefinito non dispone di privilegi sufficienti nel cloud privato vCenter per installare VEEAM, quindi è necessario eseguire l'escalation dei privilegi vCenter dell'utente. Per ulteriori informazioni, vedere [privilegi di escalation](escalate-private-cloud-privileges.md).

### <a name="azure-portal-connect-your-virtual-network-to-the-private-cloud"></a>Portale di Azure: connettere la rete virtuale al cloud privato

Connettere la rete virtuale al cloud privato seguendo le istruzioni riportate in [connessione alla rete virtuale di Azure con ExpressRoute](azure-expressroute-connection.md).

### <a name="azure-portal-create-a-backup-repository-vm"></a>Portale di Azure: creare una VM del repository di backup

1. Creare una VM D2 v3 standard con (2 vCPU e 8 GB di memoria).
2. Selezionare l'immagine basata su CentOS 7,4.
3. Configurare un gruppo di sicurezza di rete (NSG) per la macchina virtuale. Verificare che la macchina virtuale non disponga di un indirizzo IP pubblico e che non sia raggiungibile dalla rete Internet pubblica.
4. Creare un account utente basato su nome utente e password per la nuova macchina virtuale. Per istruzioni, vedere [creare una macchina virtuale Linux nel portale di Azure](../virtual-machines/linux/quick-create-portal.md).
5. Creare da 512 GiB standard HDD e collegarlo alla VM del repository.  Per istruzioni, vedere [come aggiungere un disco dati gestito a una macchina virtuale Windows nel portale di Azure](../virtual-machines/windows/attach-managed-disk-portal.md).
6. [Creare un volume XFS sul disco gestito](https://www.digitalocean.com/docs/volumes/how-to/). Accedere alla macchina virtuale usando le credenziali indicate in precedenza. Eseguire lo script seguente per creare un volume logico, aggiungervi il disco, creare una [partizione](https://www.digitalocean.com/docs/volumes/how-to/partition/) del file system XFS e [montare](https://www.digitalocean.com/docs/volumes/how-to/mount/) la partizione nel percorso/BACKUP1.

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

7. Esporre/BACKUP1 come punto di montaggio NFS al server di backup Veeam in esecuzione nel cloud privato. Per istruzioni, vedere l'articolo su Digital Ocean [come configurare un montaggio NFS su CentOS 6](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-centos-6). Usare questo nome di condivisione NFS quando si configura il repository di backup nel server di backup Veeam.

8. Configurare le regole di filtro in NSG per la macchina virtuale del repository di backup per consentire in modo esplicito tutto il traffico di rete da e verso la macchina virtuale.

> [!NOTE]
> Veeam Backup & replica usa il protocollo SSH per comunicare con i repository di backup di Linux e richiede l'utilità SCP nei repository Linux. Verificare che il daemon SSH sia configurato correttamente e che SCP sia disponibile nell'host Linux.

### <a name="configure-azure-blob-storage-for-long-term-data-retention"></a>Configurare l'archiviazione BLOB di Azure per la conservazione dei dati a lungo termine

1. Creare un account di archiviazione per utilizzo generico (GPv2) di tipo standard e un contenitore BLOB come descritto nel Introduzione video Microsoft [con archiviazione di Azure](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage).
2. Creare un contenitore di archiviazione di Azure, come descritto in creare un riferimento al [contenitore](https://docs.microsoft.com/rest/api/storageservices/create-container) .
2. Scaricare l'utilità da riga di comando `azcopy` per Linux da Microsoft. È possibile usare i comandi seguenti nella shell bash in CentOS 7,5.

    ```
    wget -O azcopy.tar.gz https://aka.ms/downloadazcopylinux64
    tar -xf azcopy.tar.gz
    sudo ./install.sh
    sudo yum -y install libunwind.x86_64
    sudo yum -y install icu
    ```

3. Usare il comando `azcopy` per copiare i file di backup da e verso il contenitore BLOB.  Per i comandi dettagliati, vedere [trasferire dati con AzCopy in Linux](../storage/common/storage-use-azcopy-linux.md) .

### <a name="vcenter-console-of-private-cloud-install-veeam-br"></a>Console vCenter del cloud privato: installare Veeam B & R

Accedere a vCenter dal cloud privato per creare un account del servizio Veeam, installare Veeam B & R 9,5 e configurare Veeam con l'account del servizio.

1. Creare un nuovo ruolo denominato "Veeam Backup Role" e assegnargli le autorizzazioni necessarie, come consigliato da Veeam. Per informazioni dettagliate, vedere l'argomento Veeam [autorizzazioni necessarie](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95).
2. Creare un nuovo gruppo "gruppo di utenti Veeam" in vCenter e assegnargli il "ruolo di backup Veeam".
3. Creare un nuovo utente "account del servizio Veeam" e aggiungerlo al "gruppo di utenti Veeam".

    ![Creazione di un account del servizio Veeam](media/veeam-vcenter01.png)

4. Creare un gruppo di porte distribuite in vCenter usando la VLAN della rete di backup. Per informazioni dettagliate, vedere il video VMware [creazione di un gruppo di porte distribuite nel client Web vSphere](https://www.youtube.com/watch?v=wpCd5ZbPOpA).
5. Creare le macchine virtuali per i server di backup e proxy Veeam in vCenter in base ai [requisiti di sistema Veeam](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95). È possibile utilizzare Windows 2012 R2 o Linux. Per altre informazioni, vedere [requisiti per l'uso di repository di backup Linux](https://www.veeam.com/kb2216).
6. Montare il file ISO Veeam installabile come dispositivo CDROM nella macchina virtuale del server di backup Veeam.
7. Utilizzando una sessione RDP per il computer Windows 2012 R2 (la destinazione per l'installazione di Veeam), [installare Veeam B & R 9.5 U3](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95) in una VM Windows 2012 R2.
8. Trovare l'indirizzo IP interno della VM Veeam Backup Server e configurare l'indirizzo IP in modo che sia statico nel server DHCP. I passaggi esatti necessari per eseguire questa operazione dipendono dal server DHCP. Ad esempio, l'articolo NETGATE <a href="https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html" target="_blank">mapping DHCP statici</a> spiega come configurare un server DHCP usando un router pfSense.

### <a name="veeam-console-install-veeam-backup-and-recovery-software"></a>Console Veeam: installare il software di backup e ripristino Veeam

Utilizzando la console Veeam, configurare il software di backup e ripristino Veeam. Per informazioni dettagliate, vedere [Veeam Backup & Replication V9-Installation and Deployment](https://www.youtube.com/watch?v=b4BqC_WXARk).

1. Aggiungere VMware vSphere come ambiente server gestito. Quando richiesto, specificare le credenziali dell'account del servizio Veeam creato all'inizio della [console vCenter del cloud privato: installare Veeam B & R](#vcenter-console-of-private-cloud-install-veeam-br).

    * Usare le impostazioni predefinite per il controllo di carico e le impostazioni avanzate predefinite.
    * Impostare il percorso del server di montaggio come server di backup.
    * Modificare il percorso di backup della configurazione per il server Veeam nel repository remoto.

2. Aggiungere il server Linux in Azure come repository di backup.

    * Usare le impostazioni predefinite per il controllo di carico e per le impostazioni avanzate. 
    * Impostare il percorso del server di montaggio come server di backup.
    * Modificare il percorso di backup della configurazione per il server Veeam nel repository remoto.

3. Abilitare la crittografia del backup della configurazione usando **le impostazioni di backup della configurazione Home >** .

4. Aggiungere una macchina virtuale Windows Server come server proxy per l'ambiente VMware. Utilizzando "regole traffico" per un proxy, crittografare i dati di backup in rete.

5. Configurare i processi di backup.
    * Per configurare i processi di backup, seguire le istruzioni riportate in [creazione di un processo di backup](https://www.youtube.com/watch?v=YHxcUFEss4M).
    * Abilitare la crittografia dei file di backup in **Impostazioni avanzate > archiviazione**.

6. Configurare i processi di copia di backup.

    * Per configurare i processi di copia di backup, seguire le istruzioni riportate nel video [creazione di un processo di copia di backup](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s).
    * Abilitare la crittografia dei file di backup in **Impostazioni avanzate > archiviazione**.

### <a name="cloudsimple-portal-set-up-veeam-access-and-de-escalate-privileges"></a>Portale di CloudSimple: configurare i privilegi di accesso Veeam e decalation
Creare un indirizzo IP pubblico per il server di backup e ripristino Veeam. Per istruzioni, vedere [allocare indirizzi IP pubblici](public-ips.md).

Creare una regola del firewall usando per consentire al server di backup Veeam di creare una connessione in uscita al sito web Veeam per scaricare gli aggiornamenti/patch sulla porta TCP 80. Per istruzioni, vedere [configurare le regole e le tabelle del firewall](firewall.md).

Per annullare l'escalation dei privilegi, vedere [decrescenza dei privilegi](escalate-private-cloud-privileges.md#de-escalate-privileges).

## <a name="references"></a>Riferimenti

### <a name="cloudsimple-references"></a>Riferimenti a CloudSimple

* [Creare un cloud privato](create-private-cloud.md)
* [Creare e gestire VLAN/subnet](create-vlan-subnet.md)
* [Origini di identità vCenter](set-vcenter-identity.md)
* [Configurazione di DNS e DHCP del carico di lavoro](dns-dhcp-setup.md)
* [Escalation dei privilegi](escalate-privileges.md)
* [Configurare le regole e le tabelle del firewall](firewall.md)
* [Autorizzazioni per cloud privato](learn-private-cloud-permissions.md)
* [Allocare indirizzi IP pubblici](public-ips.md)

### <a name="veeam-references"></a>Riferimenti a Veeam

* [Porte usate](https://helpcenter.veeam.com/docs/backup/vsphere/used_ports.html?ver=95)
* [Autorizzazioni necessarie](https://helpcenter.veeam.com/docs/backup/vsphere/required_permissions.html?ver=95)
* [Requisiti di sistema](https://helpcenter.veeam.com/docs/backup/vsphere/system_requirements.html?ver=95)
* [Installazione di Veeam Backup & replica](https://helpcenter.veeam.com/docs/backup/vsphere/install_vbr.html?ver=95)
* [Moduli e autorizzazioni richiesti per il supporto del repository e del repository di più sistemi operativi per Linux](https://www.veeam.com/kb2216)
* [Veeam Backup & Replication V9-installazione e distribuzione-video](https://www.youtube.com/watch?v=b4BqC_WXARk)
* [Veeam V9 creazione di un processo di backup-video](https://www.youtube.com/watch?v=YHxcUFEss4M)
* [Veeam V9 creazione di un processo di copia di backup-video](https://www.youtube.com/watch?v=LvEHV0_WDWI&t=2s)

### <a name="azure-references"></a>Riferimenti di Azure

* [Configurare un gateway di rete virtuale per ExpressRoute usando il portale di Azure](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
* [Connettere una VNet a una sottoscrizione circuit-different](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription)
* [Creare una macchina virtuale Linux nel portale di Azure](../virtual-machines/linux/quick-create-portal.md)
* [Come aggiungere un disco dati gestito a una macchina virtuale Windows nel portale di Azure](../virtual-machines/windows/attach-managed-disk-portal.md)
* [Introduzione con archiviazione di Azure-video](https://azure.microsoft.com/resources/videos/get-started-with-azure-storage)
* [Crea contenitore](https://docs.microsoft.com/rest/api/storageservices/create-container)
* [Trasferire dati con AzCopy in Linux](../storage/common/storage-use-azcopy-linux.md)

### <a name="vmware-references"></a>Riferimenti VMware

* [Creazione di un gruppo di porte distribuite in vSphere Web client-video](https://www.youtube.com/watch?v=wpCd5ZbPOpA)

### <a name="other-references"></a>Altri riferimenti

* [Creare un volume XFS nel disco gestito-RedHat](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/ch-xfs)
* [Come configurare un montaggio NFS su CentOS 7-HowToForge](https://www.howtoforge.com/nfs-server-and-client-on-centos-7)
* [Configurazione del server DHCP-NETGATE](https://www.netgate.com/docs/pfsense/dhcp/dhcp-server.html)
