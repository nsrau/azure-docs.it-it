---
title: Disponibilità elevata di macchine virtuali di Azure per SAP NW su Guida multi-SID RHEL Documenti Microsoft
description: Disponibilità elevata delle macchine virtuali di Azure per SAP NetWeaver in Red Hat Enterprise Linux
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/24/2020
ms.author: radeltch
ms.openlocfilehash: 4f1bfd58e27f0cd677980ff9351d32d91a68e3e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247436"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-red-hat-enterprise-linux-for-sap-applications-multi-sid-guide"></a>Disponibilità elevata per SAP NetWeaver nelle macchine virtuali di Azure in Red Hat Enterprise Linux per le applicazioni SAP multi-SID guida

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

In questo articolo viene descritto come distribuire più sistemi SAP NetWeaver a disponibilità elevata(ovvero multi-SID) in un cluster a due nodi in macchine virtuali di Azure con Red Hat Enterprise Linux per applicazioni SAP.  

Nelle configurazioni di esempio, i comandi di installazione e tre sistemi SAP NetWeaver 7.50 vengono distribuiti in un cluster a disponibilità elevata a due nodi. I SID dei sistemi SAP sono:
* **NW1**: numero di istanza ASCS **00** e nome host virtuale **msnw1ascs**; Numero di istanza ERS **02** e nome host virtuale **msnw1ers**.  
* **NW2**: numero di istanza ASCS **10** e nome host virtuale **msnw2ascs**; Numero di istanza ERS **12** e nome host virtuale **msnw2ers**.  
* **NW3**: Numero di istanza ASCS **20** e nome host virtuale **msnw3ascs**; Numero di istanza ERS **22** e nome host virtuale **msnw3ers**.  

L'articolo non copre il livello di database e la distribuzione delle condivisioni SAP NFS. Negli esempi di questo articolo viene usato il volume **sapMSID** dei [file NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes) di Azure per le condivisioni NFS, presupponendo che il volume sia già distribuito. Si presuppone inoltre che il volume File NetApp di Azure venga distribuito con il protocollo NFSv3 e che esistano i percorsi di file seguenti per le risorse cluster per le istanze ASCS ed ERS dei sistemi SAP NW1, NW2 e NW3:  

* volume sapMSID (nfs://10.42.0.4/sapmnt<b>NW1</b>)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>ascs)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>sys)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>ers)
* volume sapMSID (nfs://10.42.0.4/sapmnt<b>NW2</b>)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>ascs)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>sys)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>ers)
* volume sapMSID (nfs://10.42.0.4/sapmnt<b>NW3</b>)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>ascs)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>sys)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>ers)

Prima di iniziare, fare riferimento alle note e ai documenti SAP seguenti:

* Nota SAP [1928533], contenente:
  * Elenco delle dimensioni delle VM di Azure supportate per la distribuzione di software SAP
  * Importanti informazioni sulla capacità per le dimensioni delle VM di Azure
  * Software SAP e combinazioni di sistemi operativi e database supportati
  * Versione del kernel SAP richiesta per Windows e Linux in Microsoft Azure
* [Documentazione sui file NetApp di AzureAzure NetApp Files documentation][anf-azure-doc]
* La nota SAP [2015553] elenca i prerequisiti per le distribuzioni di software SAP supportate da SAP in Azure.
* La nota SAP [2002167] indica le impostazioni del sistema operativo consigliate per Red Hat Enterprise Linux
* La nota SAP [2009879] contiene le linee guida di SAP HANA per Red Hat Enterprise Linux
* La nota SAP [2178632] contiene informazioni dettagliate su tutte le metriche di monitoraggio segnalate per SAP in Azure.
* La nota SAP [2191498] contiene la versione dell'agente host SAP per Linux necessaria in Azure.
* La nota SAP [2243692] contiene informazioni sulle licenze SAP in Linux in Azure.
* La nota SAP [1999351] contiene informazioni aggiuntive sulla risoluzione dei problemi per l'estensione di monitoraggio avanzato di Azure per SAP.
* [Community WIKI SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) contiene tutte le note su SAP necessarie per Linux.
* [Pianificazione e implementazione di Macchine virtuali di Azure per SAP in Linux][planning-guide]
* [Distribuzione di Macchine virtuali di Azure per SAP in Linux][deployment-guide]
* [Distribuzione DBMS di Macchine virtuali di Azure per SAP in Linux][dbms-guide]
* [SAP NetWeaver nel cluster Pacemaker](https://access.redhat.com/articles/3150081)
* Documentazione generale di RHEL
  * [High Availability Add-On Overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index) (Panoramica dei componenti aggiuntivi a disponibilità elevata)
  * [High Availability Add-On Administration](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index) (Amministrazione dei componenti aggiuntivi a disponibilità elevata)
  * [High Availability Add-On Reference](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index) (Riferimento dei componenti aggiuntivi a disponibilità elevata)
  * [Configurazione di ASCS/ERS per SAP Netweaver con risorse autonome in RHEL 7.5](https://access.redhat.com/articles/3569681)
  * [Configurare SAP S/4HANA ASCS/ERS con Il server Enqueue 2 (ENSA2) autonomo in Pacemaker su RHEL](https://access.redhat.com/articles/3974941)
* Documentazione RHEL specifica di Azure:Azure-specific RHEL documentation:
  * [Support Policies for RHEL High Availability Clusters - Microsoft Azure Virtual Machines as Cluster Members](https://access.redhat.com/articles/3131341) (Criteri di supporto per cluster RHEL a disponibilità elevata - Macchine virtuali di Microsoft Azure come membri del cluster)
  * [Installing and Configuring a Red Hat Enterprise Linux 7.4 (and later) High-Availability Cluster on Microsoft Azure](https://access.redhat.com/articles/3252491) (Installazione e configurazione di un cluster Red Hat Enterprise Linux 7.4 e versioni successive a disponibilità elevata in Microsoft Azure)
* [Applicazioni SAP NetApp in Microsoft Azure con i file NetApp di Azure][anf-sap-applications-azure]

## <a name="overview"></a>Panoramica

Le macchine virtuali, che fanno parte del cluster, devono essere dimensionate per poter eseguire tutte le risorse, nel caso in cui si verifichi il failover. Ogni SID SAP può eseguire il failover indipendentemente l'uno dall'altro nel cluster a disponibilità elevata multi-SID.  

Per ottenere un'elevata disponibilità, SAP NetWeaver richiede condivisioni a disponibilità elevata. In questa documentazione vengono presentati gli esempi con le condivisioni SAP distribuite nei [volumi NFS dei file NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)di Azure. È anche possibile ospitare le condivisioni nel [cluster GlusterFS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)a disponibilità elevata, che può essere utilizzato da più sistemi SAP.  

![Panoramica della disponibilità elevata di SAP NetWeaver](./media/high-availability-guide-rhel/ha-rhel-multi-sid.png)

> [!IMPORTANT]
> Il supporto per il clustering multi-SID di SAP ASCS/ERS con Red Hat Linux come sistema operativo guest nelle macchine virtuali di Azure è limitato a **cinque** SID SAP nello stesso cluster. Ogni nuovo SID aumenta la complessità. Non è **supportata**una combinazione di Server di replica raccolta SAP 1 e Server di replica accodamento 2 nello stesso cluster. Il clustering multi-SID descrive l'installazione di più istanze SAP ASCS/ERS con SID diversi in un cluster Pacemaker. Attualmente il clustering multi-SID è supportato solo per ASCS/ERS.  

> [!TIP]
> Il clustering multi-SID di SAP ASCS/ERS è una soluzione con maggiore complessità. È più complesso da implementare. Si tratta anche di maggiore sforzo amministrativo, durante l'esecuzione di attività di manutenzione (come l'applicazione di patch del sistema operativo). Prima di iniziare l'implementazione effettiva, pianificare attentamente la distribuzione e tutti i componenti coinvolti come macchine virtuali, supporti NFS, VIP, configurazioni di bilanciamento del carico e così via.  

SAP NetWeaver ASCS, SAP NetWeaver SCS e SAP NetWeaver ERS utilizzano indirizzi IP virtuali e di nome host virtuali. Per usare un indirizzo IP virtuale in Azure, occorre il bilanciamento del carico. È consigliabile usare il servizio di [bilanciamento del carico Standard.](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal)  

L'elenco seguente mostra la configurazione del servizio di bilanciamento del carico (A)SCS ed ERS per questo esempio di cluster multi-SID con tre sistemi SAP. Sono necessari IP front-end separati, probe di integrità e regole di bilanciamento del carico per ogni istanza ASCS ed ERS per ogni SID. Assegnare tutte le macchine virtuali che fanno parte del cluster ASCS/ASCS a un pool back-end di un singolo ILB.  

### <a name="ascs"></a>(A)SCS

* Configurazione front-end
  * Indirizzo IP per NW1: 10.3.1.50
  * Indirizzo IP per NW2: 10.3.1.52
  * Indirizzo IP per NW3: 10.3.1.54

* Porte sonda
  * Porta 620<strong>&lt;&gt;nr</strong>, quindi per le porte sonde NW1, NW2 e NW3 620**00**, 620**10** e 620**20**
* Regole di bilanciamento del carico: crearne una per ogni istanza, ovvero NW1/ASCS, NW2/ASCS e NW3/ASCS.
  * Se si utilizza Load Balancer Standard, selezionare **Porte HA**
  * Se si usa Load Balancer di base, creare regole di bilanciamento del carico per le porte seguentiIf using Basic Load Balancer, create Load balancing rules for the following ports
    * 32<strong>&lt;nr&gt; </strong> TCP
    * 36<strong>&lt;nr&gt; </strong> TCP
    * 39<strong>&lt;nr&gt; </strong> TCP
    * 81<strong>&lt;nr&gt; </strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;nr&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* Configurazione front-end
  * Indirizzo IP per NW1 10.3.1.51
  * Indirizzo IP per NW2 10.3.1.53
  * Indirizzo IP per NW3 10.3.1.55

* Porta probe
  * Porta 621<strong>&lt;&gt;nr</strong>, quindi per le porte probe NW1, NW2 e N3 621**02**, 621**12** e 621**22**
* Regole di bilanciamento del carico: crearne una per ogni istanza, ovvero NW1/ERS, NW2/ERS e NW3/ERS.
  * Se si utilizza Load Balancer Standard, selezionare **Porte HA**
  * Se si usa Load Balancer di base, creare regole di bilanciamento del carico per le porte seguentiIf using Basic Load Balancer, create Load balancing rules for the following ports
    * 32<strong>&lt;nr&gt; </strong> TCP
    * 33<strong>&lt;nr&gt; </strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;nr&gt;</strong>16 TCP

* Configurazione back-end
  * Connessione alle interfacce di rete primarie di tutte le macchine virtuali che devono far parte del cluster (A)SCS/ERS

> [!Note]
> Quando le macchine virtuali senza indirizzi IP pubblici vengono inserite nel pool back-end del servizio di bilanciamento del carico standard di Azure interno (nessun indirizzo IP pubblico), non sarà disponibile alcuna connettività Internet in uscita, a meno che non venga eseguita una configurazione aggiuntiva per consentire il routing agli endpoint pubblici. Per informazioni dettagliate su come ottenere la connettività in uscita, vedere Connettività degli endpoint pubblici per macchine virtuali che [usano Azure Standard Load Balancer in scenari di disponibilità elevata SAP.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)  

> [!IMPORTANT]
> Non abilitare timestamp TCP nelle macchine virtuali di Azure posizionati dietro Azure Load Balancer.Do not enable TCP timestamps on Azure VMs placed behind Azure Load Balancer. L'abilitazione dei timestamp TCP causerà l'esito negativo dei probe di integrità. Impostare il parametro **net.ipv4.tcp_timestamps su** **0**. Per informazioni dettagliate, vedere [Sondati di integrità di Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

## <a name="sap-shares"></a>Azioni SAP

SAP NetWeaver richiede l'archiviazione condivisa per il trasporto, la directory del profilo e così via. Per il sistema SAP a disponibilità elevata, è importante disporre di condivisioni a disponibilità elevata. È necessario decidere l'architettura per le condivisioni SAP. Un'opzione consiste nel distribuire le condivisioni nei [volumi NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)file NetApp di Azure.  Con i file NetApp di Azure, si otterrà la disponibilità elevata incorporata per le condivisioni NFS SAP.

Un'altra opzione consiste nel creare [GlusterFS nelle macchine virtuali di Azure in Red Hat Enterprise Linux per SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs), che può essere condiviso tra più sistemi SAP. 

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>Distribuire il primo sistema SAP nel clusterDeploy the first SAP system in the cluster

Dopo aver deciso l'architettura per le condivisioni SAP, distribuire il primo sistema SAP nel cluster seguendo la documentazione corrispondente.

* Se si usano i volumi NFS dei file di Rete di Azure, seguire la disponibilità elevata delle macchine virtuali di Azure per SAP NetWeaver in Red Hat Enterprise Linux con i file NetApp di Azure per le applicazioni SAPIf using Azure NetApp Files NFS volumes, follow [Azure VMs high availability for SAP NetWeaver on Red Hat Enterprise Linux with Azure NetApp Files for SAP applications](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)  
* Se si usa il cluster GlusterFS, seguire [GlusterFS in macchine virtuali di Azure in Red Hat Enterprise Linux per SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs).  

I documenti sopra elencati guideranno l'utente attraverso i passaggi per preparare l'infrastruttura necessaria, creare il cluster, preparare il sistema operativo per l'esecuzione dell'applicazione SAP.  

> [!TIP]
> Verificare sempre la funzionalità di failover del cluster, dopo la distribuzione del primo sistema, prima di aggiungere i SID SAP aggiuntivi al cluster. In questo modo si saprà che la funzionalità del cluster funziona, prima di aggiungere la complessità di sistemi SAP aggiuntivi al cluster.   

## <a name="deploy-additional-sap-systems-in-the-cluster"></a>Distribuire sistemi SAP aggiuntivi nel clusterDeploy additional SAP systems in the cluster

In questo esempio si presuppone che il sistema **NW1** sia già stato distribuito nel cluster. Verrà illustrato come distribuire nei sistemi SAP del cluster **NW2** e **NW3**. 

Gli elementi seguenti sono preceduti dall'indicazione **[A]** - applicabile a tutti i nodi, **[1]** - applicabile solo al nodo 1 o **[2]** - applicabile solo al nodo 2.

### <a name="prerequisites"></a>Prerequisiti 

> [!IMPORTANT]
> Prima di seguire le istruzioni per distribuire sistemi SAP aggiuntivi nel cluster, seguire le istruzioni per distribuire il primo sistema SAP nel cluster, poiché sono necessari solo durante la prima distribuzione del sistema.  

In questa documentazione si presuppone che:This documentation assumes that:
* Il cluster Pacemaker è già configurato e in esecuzione.  
* Almeno un sistema SAP (istanza ASCS / ERS) è già distribuito ed è in esecuzione nel cluster.  
* La funzionalità di failover del cluster è stata testata.  
* Vengono distribuite le condivisioni NFS per tutti i sistemi SAP.  

### <a name="prepare-for-sap-netweaver-installation"></a>Preparazione per l'installazione di SAP NetWeaver

1. Aggiungere la configurazione per il sistema appena distribuito, ovvero **NW2**, **NW3**, al servizio di bilanciamento del carico di Azure esistente, seguendo le istruzioni [Distribuire manualmente Azure Load Balancer tramite](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#deploy-linux-manually-via-azure-portal)il portale di Azure. Regolare gli indirizzi IP, le porte del probe di integrità, le regole di bilanciamento del carico per la configurazione.  

2. **[A]** Risoluzione dei nomi di installazione per i sistemi SAP aggiuntivi. È possibile utilizzare il `/etc/hosts` server DNS o modificarlo su tutti i nodi. In questo esempio viene `/etc/hosts` illustrato come utilizzare il file.  Adattare gli indirizzi IP e i nomi host all'ambiente. 

    ```
    sudo vi /etc/hosts
    # IP address of the load balancer frontend configuration for NW2 ASCS
    10.3.1.52 msnw2ascs
    # IP address of the load balancer frontend configuration for NW3 ASCS
    10.3.1.54 msnw3ascs
    # IP address of the load balancer frontend configuration for NW2 ERS
    10.3.1.53 msnw2ers
    # IP address of the load balancer frontend configuration for NW3 ERS
    10.3.1.55 msnw3ers
   ```

3. **[A]** Creare le directory condivise per i sistemi SAP **NW2** e **NW3** aggiuntivi che si stanno distribuendo nel cluster. 

    ```
    sudo mkdir -p /sapmnt/NW2
    sudo mkdir -p /usr/sap/NW2/SYS
    sudo mkdir -p /usr/sap/NW2/ASCS10
    sudo mkdir -p /usr/sap/NW2/ERS12
    sudo mkdir -p /sapmnt/NW3
    sudo mkdir -p /usr/sap/NW3/SYS
    sudo mkdir -p /usr/sap/NW3/ASCS20
    sudo mkdir -p /usr/sap/NW3/ERS22

    
    sudo chattr +i /sapmnt/NW2
    sudo chattr +i /usr/sap/NW2/SYS
    sudo chattr +i /usr/sap/NW2/ASCS10
    sudo chattr +i /usr/sap/NW2/ERS12
    sudo chattr +i /sapmnt/NW3
    sudo chattr +i /usr/sap/NW3/SYS
    sudo chattr +i /usr/sap/NW3/ASCS20
    sudo chattr +i /usr/sap/NW3/ERS22
   ```

4. **[A]** Aggiungere le voci di montaggio per i file system /sapmnt/SID e /usr/sap/SID/SYS per i sistemi SAP aggiuntivi che si stanno distribuendo nel cluster. In questo esempio **NW2** e **NW3**.  

   Aggiornare `/etc/fstab` il file con i file system per i sistemi SAP aggiuntivi che si stanno distribuendo nel cluster.  

   * Se si usano i file di Azure NetApp, seguire le istruzioni [qui](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#prepare-for-sap-netweaver-installation)  
   * Se si utilizza il cluster GlusterFS, seguire le istruzioni [riportate di seguito](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel#prepare-for-sap-netweaver-installation)  

### <a name="install-ascs--ers"></a>Installare ASCS / ERS

1. Creare le risorse del cluster IP virtuale e probe di integrità per le istanze ASCS dei sistemi SAP aggiuntivi che si stanno distribuendo nel cluster. L'esempio illustrato di seguito è per **NW2** e **NW3** ASCS, usando NFS in volumi di file NetApp di Azure con protocollo NFSv3.  

    ```
    sudo pcs resource create fs_NW2_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ascs' \
    directory='/usr/sap/NW2/ASCS10' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_ASCS

    sudo pcs resource create vip_NW2_ASCS IPaddr2 \
    ip=10.3.1.52 cidr_netmask=24 \
     --group g-NW2_ASCS
    
    sudo pcs resource create nc_NW2_ASCS azure-lb port=62010 \
     --group g-NW2_ASCS

    sudo pcs resource create fs_NW3_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ascs' \
    directory='/usr/sap/NW3/ASCS20' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-NW3_ASCS

    sudo pcs resource create vip_NW3_ASCS IPaddr2 \
    ip=10.3.1.54 cidr_netmask=24 \
    --group g-NW3_ASCS

    sudo pcs resource create nc_NW3_ASCS azure-lb port=62020 \
    --group g-NW3_ASCS
    ```

   Assicurarsi che lo stato del cluster sia ok e che tutte le risorse siano state avviate. Non è importante il nodo su cui sono in esecuzione le risorse.  

2. **[1]** Installare SAP NetWeaver ASCS  

   Installare SAP NetWeaver ASCS come radice, utilizzando un nome host virtuale che esegue il mapping all'indirizzo IP della configurazione frontend del servizio di bilanciamento del carico per ASCS. Ad esempio, per il sistema **NW2,** il nome host virtuale è <b>msnw2ascs</b>, <b>10.3.1.52</b> e il numero di istanza utilizzato per il probe del servizio di bilanciamento del carico, ad esempio <b>10</b>. Per il sistema **NW3**, il nome host virtuale è <b>msnw3ascs</b>, <b>10.3.1.54</b> e il numero di istanza utilizzato per il probe del servizio di bilanciamento del carico, ad esempio <b>20</b>. Annotare il nodo del cluster in cui è stato installato ASCS per ogni SID SAP.  

   È possibile usare il parametro sapinst SAPINST_REMOTE_ACCESS_USER per consentire a un utente non ROOT di connettersi a sapinst. È possibile utilizzare i SAPINST_USE_HOSTNAME per installare SAP, usando il nome host virtuale.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   Se l'installazione non riesce a creare una sottocartella in /usr/sap/**SID**/ASCS**Instance**, provare a impostare il proprietario su **sid**adm e group su sapsys dell'istanza ASCS, quindi riprovare.**Instance#**

3. **[1]** Creare un IP virtuale e risorse cluster di sonda di integrità per l'istanza ERS del sistema SAP aggiuntivo che si sta distribuendo nel cluster. L'esempio illustrato di seguito è per **NW2** e **NW3** ERS, usando NFS in volumi di file NetApp di Azure con protocollo NFSv3.  

    ```
    sudo pcs resource create fs_NW2_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ers' \
    directory='/usr/sap/NW2/ERS12' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_AERS

    sudo pcs resource create vip_NW2_AERS IPaddr2 \
    ip=10.3.1.53 cidr_netmask=24 \
     --group g-NW2_AERS

    sudo pcs resource create nc_NW2_AERS azure-lb port=62112 \
     --group g-NW2_AERS

    sudo pcs resource create fs_NW3_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ers' \
    directory='/usr/sap/NW3/ERS22' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW3_AERS

    sudo pcs resource create vip_NW3_AERS IPaddr2 \
    ip=10.3.1.55 cidr_netmask=24 \
     --group g-NW3_AERS

    sudo pcs resource create nc_NW3_AERS azure-lb port=62122 \
     --group g-NW3_AERS
   ```

   Assicurarsi che lo stato del cluster sia ok e che tutte le risorse siano state avviate.  

   Assicurarsi quindi che le risorse del gruppo ERS appena creato siano in esecuzione sul nodo del cluster, di fronte al nodo del cluster in cui è stata installata l'istanza ASCS per lo stesso sistema SAP.  Ad esempio, se NW2 ASCS è stato installato in `rhelmsscl1`, assicurarsi che il gruppo NW2 ERS sia in esecuzione su `rhelmsscl2`.  È possibile eseguire la migrazione `rhelmsscl2` del gruppo NW2 ERS a eseguendo il comando seguente per una delle risorse cluster del gruppo: 

    ```
      pcs resource move fs_NW2_AERS rhelmsscl2
    ```

4. **[2]** Installare SAP NetWeaver ERS

   Installare SAP NetWeaver ERS come root nell'altro nodo, utilizzando un nome host virtuale che esegue il mapping all'indirizzo IP della configurazione frontend del servizio di bilanciamento del carico per ERS. Ad esempio, per il sistema **NW2**, il nome host virtuale sarà <b>msnw2ers</b>, <b>10.3.1.53</b> e il numero di istanza utilizzato per il probe del servizio di bilanciamento del carico, ad esempio <b>12</b>. Per il sistema **NW3**, il nome host virtuale <b>msnw3ers</b>, <b>10.3.1.55</b> e il numero di istanza utilizzato per il probe del servizio di bilanciamento del carico, ad esempio <b>22</b>. 

   È possibile usare il parametro sapinst SAPINST_REMOTE_ACCESS_USER per consentire a un utente non ROOT di connettersi a sapinst. È possibile utilizzare i SAPINST_USE_HOSTNAME per installare SAP, usando il nome host virtuale.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > Usare SWPM SP 20 PL 05 o versione successiva. Le versioni precedenti non impostano correttamente le autorizzazioni e l'installazione avrà esito negativo.

   Se l'installazione non riesce a creare una sottocartella in /usr/sap/**NW2**/ERS**Instance,** provare a impostare il proprietario su **sid**adm e il gruppo su sapsys della cartella**ERS Instance** e riprovare.

   Se era necessario eseguire la migrazione del gruppo ERS del sistema SAP appena distribuito in un altro nodo del cluster, non dimenticare di rimuovere il vincolo di posizione per il gruppo ERS. È possibile rimuovere il vincolo eseguendo il comando seguente (l'esempio è fornito per i sistemi SAP **NW2** e **NW3**). Assicurarsi di rimuovere i vincoli temporanei per la stessa risorsa utilizzata nel comando per spostare il gruppo di cluster ERS.

    ```
      pcs resource clear fs_NW2_AERS
      pcs resource clear fs_NW3_AERS
    ```

5. **[1]** Adattare i profili di istanza ASCS/SCS ed ERS per i nuovi sistemi SAP installati. L'esempio riportato di seguito è per NW2. È necessario adattare i profili ASCS/SCS ed ERS per tutte le istanze SAP aggiunte al cluster.  
 
   * Profilo ASCS/SCS

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
      # Change the restart command to a start command
      #Restart_Program_01 = local $(_EN) pf=$(_PF)
      Start_Program_01 = local $(_EN) pf=$(_PF)

      # Add the keep alive parameter
      enque/encni/set_so_keepalive = true
      ```

   * Profilo ERS

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ERS12_msnw2ers
   
      # Change the restart command to a start command
      #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
      Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
      # remove Autostart from ERS profile
      # Autostart = 1
      ```

6. **[A]** Aggiornare il file /usr/sap/sapservices

   Per impedire l'avvio delle istanze da parte dello script di avvio `/usr/sap/sapservices` sapinit, tutte le istanze gestite da Pacemaker devono essere impostate come commento dal file.  L'esempio riportato di seguito è per i sistemi SAP **NW2** e **NW3**.  

   ```
    # On the node where ASCS was installed, comment out the line for the ASCS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ASCS10/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ASCS10/exe/sapstartsrv pf=/usr/sap/NW2/SYS/profile/NW2_ASCS10_msnw2ascs -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ASCS20/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ASCS20/exe/sapstartsrv pf=/usr/sap/NW3/SYS/profile/NW3_ASCS20_msnw3ascs -D -u nw3adm

    # On the node where ERS was installed, comment out the line for the ERS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ERS12/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ERS12/exe/sapstartsrv pf=/usr/sap/NW2/ERS12/profile/NW2_ERS12_msnw2ers -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ERS22/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ERS22/exe/sapstartsrv pf=/usr/sap/NW3/ERS22/profile/NW3_ERS22_msnw3ers -D -u nw3adm
   ```

7. **[1]** Creare le risorse cluster SAP per il sistema SAP appena installato.  

   Se si utilizza l'architettura enSA1 (enSA1) del server di accodamento 1, definire le risorse per i sistemi SAP **NW2** e **NW3** come segue:

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint location rsc_sap_NW2_ASCS10 rule score=2000 runs_ers_NW2 eq 1
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint location rsc_sap_NW3_ASCS20 rule score=2000 runs_ers_NW3 eq 1
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   SAP ha introdotto il supporto per il server di accodamento 2, inclusa la replica, a partire da SAP NW 7.52. A partire dalla piattaforma ABAP 1809, il server di accodamento 2 viene installato per impostazione predefinita. Vedere la nota SAP [2630416](https://launchpad.support.sap.com/#/notes/2630416) per il supporto del server 2 di accodamento.
   Se si utilizza l'architettura del server di accodamento 2 ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), definire le risorse per i sistemi SAP **NW2** e **NW3** come segue:

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW2_ASCS then stop g-NW2_AERS symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW3_ASCS then stop g-NW3_AERS symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   Se si esegue l'aggiornamento da una versione precedente e si passa al server di accodamento 2, vedere la nota SAP [2641019.](https://launchpad.support.sap.com/#/notes/2641019) 

   > [!NOTE]
   > I timeout nella configurazione precedente sono solo esempi e potrebbe essere necessario adattarli alla configurazione SAP specifica. 

   Assicurarsi che lo stato del cluster sia corretto e che tutte le risorse siano avviate. Non è importante il nodo su cui sono in esecuzione le risorse.
   L'esempio seguente mostra lo stato delle risorse del cluster, dopo che i sistemi SAP **NW2** e **NW3** sono stati aggiunti al cluster. 

    ```
     sudo pcs status

    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    ```

8. **[A]** Aggiungere regole firewall per ASCS ed ERS su entrambi i nodi.  Nell'esempio riportato di seguito vengono illustrate le regole del firewall per entrambi i sistemi SAP **NW2** e **NW3**.  

   ```
    # NW2 - ASCS
    sudo firewall-cmd --zone=public --add-port=62010/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62010/tcp
    sudo firewall-cmd --zone=public --add-port=3210/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3210/tcp
    sudo firewall-cmd --zone=public --add-port=3610/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3610/tcp
    sudo firewall-cmd --zone=public --add-port=3910/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3910/tcp
    sudo firewall-cmd --zone=public --add-port=8110/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8110/tcp
    sudo firewall-cmd --zone=public --add-port=51013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51013/tcp
    sudo firewall-cmd --zone=public --add-port=51014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51014/tcp
    sudo firewall-cmd --zone=public --add-port=51016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51016/tcp
    # NW2 - ERS
    sudo firewall-cmd --zone=public --add-port=62112/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62112/tcp
    sudo firewall-cmd --zone=public --add-port=3312/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3312/tcp
    sudo firewall-cmd --zone=public --add-port=51213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51213/tcp
    sudo firewall-cmd --zone=public --add-port=51214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51214/tcp
    sudo firewall-cmd --zone=public --add-port=51216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51216/tcp
    # NW3 - ASCS
    sudo firewall-cmd --zone=public --add-port=62020/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62020/tcp
    sudo firewall-cmd --zone=public --add-port=3220/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3220/tcp
    sudo firewall-cmd --zone=public --add-port=3620/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3620/tcp
    sudo firewall-cmd --zone=public --add-port=3920/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3920/tcp
    sudo firewall-cmd --zone=public --add-port=8120/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8120/tcp
    sudo firewall-cmd --zone=public --add-port=52013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52013/tcp
    sudo firewall-cmd --zone=public --add-port=52014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52014/tcp
    sudo firewall-cmd --zone=public --add-port=52016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52016/tcp
    # NW3 - ERS
    sudo firewall-cmd --zone=public --add-port=62122/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62122/tcp
    sudo firewall-cmd --zone=public --add-port=3322/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3322/tcp
    sudo firewall-cmd --zone=public --add-port=52213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52213/tcp
    sudo firewall-cmd --zone=public --add-port=52214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52214/tcp
    sudo firewall-cmd --zone=public --add-port=52216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52216/tcp
   ```

### <a name="proceed-with-the-sap-installation"></a>Procedere con l'installazione SAP 

Completare l'installazione SAP:

* [Preparazione dei server applicazioni SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [Installazione di un'istanza DBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#install-database)
* [Installazione di un server applicazioni SAP primario](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#sap-netweaver-application-server-installation)
* Installazione di una o più istanze aggiuntive dell'applicazione SAPInstalling one or more additional SAP application instances

## <a name="test-the-multi-sid-cluster-setup"></a>Testare la configurazione del cluster multi-SID

I test seguenti sono un sottoinsieme dei test case nelle guide alle procedure consigliate di Red Hat. Sono inclusi per la vostra convenienza. Per l'elenco completo dei test del cluster, fare riferimento alla documentazione seguente:For the full list of cluster tests, reference the following documentation:

* Se si usano i volumi NFS dei file di Rete di Azure, seguire la disponibilità elevata delle macchine virtuali di Azure per SAP NetWeaver in RHEL con i file NetApp di Azure per le applicazioni SAPIf using Azure NetApp Files NFS volumes, follow [Azure VMs high availability for SAP NetWeaver on RHEL with Azure NetApp Files for SAP applications](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)
* Se si `GlusterFS`usa l'utilizzo di disponibilità elevata , seguire la disponibilità elevata delle macchine virtuali di [Azure per SAP NetWeaver in RHEL per le applicazioni SAP.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)  

Leggere sempre le guide alle procedure consigliate di Red Hat ed eseguire tutti i test aggiuntivi che potrebbero essere stati aggiunti.  
I test presentati si trovano in un cluster multi-SID a due nodi con tre sistemi SAP installati.  

1. Eseguire manualmente la migrazione dell'istanza ASCS. L'esempio mostra la migrazione dell'istanza ASCS per il sistema SAP NW3.

   Stato delle risorse prima dell'avvio del test:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   Eseguire i comandi seguenti come root per eseguire la migrazione dell'istanza ASCS NW3.

   ```
    pcs resource move rsc_sap_NW3_ASCS200
    # Clear temporary migration constraints
    pcs resource clear rsc_sap_NW3_ASCS20

    # Remove failed actions for the ERS that occurred as part of the migration
    pcs resource cleanup rsc_sap_NW3_ERS22
   ```

   Stato delle risorse dopo il test:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

1. Simulare l'arresto anomalo del nodo

   Stato delle risorse prima dell'avvio del test:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

   Eseguire il comando seguente come root in un nodo, in cui è in esecuzione almeno un'istanza ASCS. In questo esempio è stato `rhelmsscl1`eseguito il comando su , in cui sono in esecuzione le istanze ASCS per NW1, NW2 e NW3.  

   ```
   echo c > /proc/sysrq-trigger
   ```

   Lo stato dopo il test e dopo il nodo che è stato arrestato in modo anomalo è stato riavviato, dovrebbe essere simile al seguente.

   ```
    Full list of resources:

    rsc_st_azure    (stonith:fence_azure_arm):      Started rhelmsscl2
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   Se sono presenti messaggi per le risorse non riuscite, pulire lo stato delle risorse non riuscite. Ad esempio:

   ```
   pcs resource cleanup rsc_sap_NW1_ERS02
   ```

## <a name="next-steps"></a>Passaggi successivi

* [Pianificazione e implementazione di Macchine virtuali di Azure per SAP][planning-guide]
* [Distribuzione di macchine virtuali di Azure per SAP][deployment-guide]
* [Distribuzione DBMS di Macchine virtuali di Azure per SAP][dbms-guide]
* Per informazioni su come ottenere la disponibilità elevata e un piano di ripristino di emergenza di SAP HANA nelle macchine virtuali di Azure, vedere [Disponibilità elevata di SAP HANA nelle macchine virtuali di Azure (VM)][sap-hana-ha].
