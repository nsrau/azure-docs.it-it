---
title: Disponibilità elevata di Azure le macchine virtuali per SAP NetWeaver su Red Hat Enterprise Linux con file di Azure NetApp | Microsoft Docs
description: Disponibilità elevata delle macchine virtuali di Azure per SAP NetWeaver in Red Hat Enterprise Linux
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/14/2019
ms.author: radeltch
ms.openlocfilehash: 8679cfe54c8fb2c88b312f67ea9b2d7115cc479e
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503578"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>Disponibilità elevata di Azure le macchine virtuali per SAP NetWeaver su Red Hat Enterprise Linux con file NetApp di Azure per le applicazioni SAP

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2009879]: https://launchpad.support.sap.com/#/notes/2009879
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

Questo articolo descrive come distribuire le macchine virtuali, configurare le macchine virtuali, installare il framework del cluster e installare un sistema SAP NetWeaver 7.50 a disponibilità elevata, usando [file di Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/).
Nelle configurazioni di esempio, nei comandi di installazione e così via Istanza ASCS è numero 00, l'istanza di ERS è numero 01, istanza Primary Application (PAS) è 02 e l'istanza dell'applicazione (AAS) è 03. SAP System ID QAS viene usato. 

Il livello di database non viene trattato in dettaglio in questo articolo.  

Leggere prima di tutto i documenti e le note SAP seguenti:

* [Documentazione di Azure i file di NetApp][anf-azure-doc] 
* Nota SAP [1928533], contenente:
  * Elenco delle dimensioni delle VM di Azure supportate per la distribuzione di software SAP
  * Importanti informazioni sulla capacità per le dimensioni delle VM di Azure
  * Software SAP e combinazioni di sistemi operativi e database supportati
  * Versione del kernel SAP richiesta per Windows e Linux in Microsoft Azure

* La nota SAP [2015553] elenca i prerequisiti per le distribuzioni di software SAP supportate da SAP in Azure.
* La nota SAP [2002167] indica le impostazioni del sistema operativo consigliate per Red Hat Enterprise Linux
* La nota SAP [2009879] contiene le linee guida di SAP HANA per Red Hat Enterprise Linux
* La nota SAP [2178632] contiene informazioni dettagliate su tutte le metriche di monitoraggio segnalate per SAP in Azure.
* La nota SAP [2191498] contiene la versione dell'agente host SAP per Linux necessaria in Azure.
* La nota SAP [2243692] contiene informazioni sulle licenze SAP in Linux in Azure.
* La nota SAP [1999351] contiene informazioni aggiuntive sulla risoluzione dei problemi per l'estensione di monitoraggio avanzato di Azure per SAP.
* [Community WIKI SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) contiene tutte le note su SAP necessarie per Linux.
* [Pianificazione di macchine virtuali di Azure e implementazione per SAP in Linux][planning-guide]
* [Distribuzione di macchine virtuali di Azure per SAP in Linux][deployment-guide]
* [Distribuzione DBMS di macchine virtuali di Azure per SAP in Linux][dbms-guide]
* [SAP NetWeaver nel cluster Pacemaker](https://access.redhat.com/articles/3150081)
* Documentazione generale di RHEL
  * [High Availability Add-On Overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index) (Panoramica dei componenti aggiuntivi a disponibilità elevata)
  * [High Availability Add-On Administration](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index) (Amministrazione dei componenti aggiuntivi a disponibilità elevata)
  * [High Availability Add-On Reference](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index) (Riferimento dei componenti aggiuntivi a disponibilità elevata)
  * [Configurazione di ASCS/ERS per SAP Netweaver con risorse autonome in RHEL 7.5](https://access.redhat.com/articles/3569681)
  * [Configurare S/4 Hana SAP ASCS/ERS con il Server di Accodamento autonomo 2 (ENSA2) in Pacemaker in RHEL ](https://access.redhat.com/articles/3974941)
* Documentazione di RHEL specifica per Azure:
  * [Support Policies for RHEL High Availability Clusters - Microsoft Azure Virtual Machines as Cluster Members](https://access.redhat.com/articles/3131341) (Criteri di supporto per cluster RHEL a disponibilità elevata - Macchine virtuali di Microsoft Azure come membri del cluster)
  * [Installing and Configuring a Red Hat Enterprise Linux 7.4 (and later) High-Availability Cluster on Microsoft Azure](https://access.redhat.com/articles/3252491) (Installazione e configurazione di un cluster Red Hat Enterprise Linux 7.4 e versioni successive a disponibilità elevata in Microsoft Azure)
* [NetApp applicazioni SAP in Microsoft Azure usando i file di Azure NetApp][anf-sap-applications-azure]

## <a name="overview"></a>Panoramica

Availability(HA) elevata per i servizi centrali SAP Netweaver richiede l'archiviazione condivisa.
A tale scopo in Red Hat Linux finora è stato necessario creare cluster di GlusterFS separato a disponibilità elevata. 

A questo punto è possibile ottenere SAP Netweaver a disponibilità elevata con archiviazione condivisa, distribuita in Azure i file di NetApp. Usare file di NetApp di Azure per l'archiviazione condivisa Elimina la necessità di ulteriori [GlusterFS cluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs). Pacemaker è ancora necessario per la disponibilità elevata di services(ASCS/SCS) centrale di SAP Netweaver.

![Panoramica della disponibilità elevata di SAP NetWeaver](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS e il database SAP HANA usano un nome host virtuale e indirizzi IP virtuali. Per usare un indirizzo IP virtuale in Azure, occorre il bilanciamento del carico. Nell'elenco seguente mostra la configurazione del bilanciamento del carico con gli indirizzi IP di front separato per (A) SCS ed ERS.

> [!IMPORTANT]
> Clustering di multi-SID di SAP ASCS/ERS con Red Hat Linux come sistema operativo guest nelle macchine virtuali di Azure viene **non è supportato**. Multi-SID di clustering vengono descritti l'installazione di più istanze di SAP ASCS/ERS con SID diverso in un cluster Pacemaker.

### <a name="ascs"></a>(A)SCS

* Configurazione front-end
  * Indirizzo IP 192.168.14.9
* Configurazione back-end
  * Connessione alle interfacce di rete primarie di tutte le macchine virtuali che devono far parte del cluster (A)SCS/ERS
* Porta probe
  * Porta 620<strong>&lt;nr&gt;</strong>
* Regole di bilanciamento del carico
  * 32<strong>&lt;nr&gt;</strong> TCP
  * 36<strong>&lt;nr&gt;</strong> TCP
  * 39<strong>&lt;nr&gt;</strong> TCP
  * 81<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* Configurazione front-end
  * Indirizzo IP 192.168.14.10
* Configurazione back-end
  * Connessione alle interfacce di rete primarie di tutte le macchine virtuali che devono far parte del cluster (A)SCS/ERS
* Porta probe
  * Porta 621<strong>&lt;nr&gt;</strong>
* Regole di bilanciamento del carico
  * 32<strong>&lt;nr&gt;</strong> TCP
  * 33<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Configurazione dell'infrastruttura di file di Azure NetApp 

SAP NetWeaver richiede un'archiviazione condivisa per la directory di trasporto e del profilo.  Prima di procedere con il programma di installazione per l'infrastruttura di file di Azure NetApp, acquisire familiarità con le [documentazione di Azure i file di NetApp][anf-azure-doc]. Controllare se l'area di Azure selezionata offre file NetApp di Azure. Il collegamento seguente mostra la disponibilità NetApp di file di Azure dall'area di Azure: [Disponibilità di file NetApp Azure dall'area di Azure][anf-avail-matrix].

È disponibile in diversi file di Azure NetApp [aree di Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Prima di distribuire i file di Azure NetApp, richiesta di onboarding NetApp in file di Azure, seguire le [registrarsi per le istruzioni di file di Azure NetApp][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Distribuire le risorse di Azure i file di NetApp  

I passaggi presuppongono che hai già distribuito [rete virtuale di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Le risorse di Azure i file di NetApp e le macchine virtuali, in cui verranno montate le risorse di Azure NetApp file devono essere distribuite nella stessa rete virtuale di Azure o in reti virtuali di Azure con peering.  

1. Se non è stato fatto già, chiedere [onboarding NetApp in file di Azure](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register).  
2. Creare l'account di NetApp nell'area di Azure selezionato, seguendo la [istruzioni per creare NetApp Account](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  
3. Configurare pool di capacità di file di Azure NetApp, seguendo la [istruzioni su come impostare il pool di capacità di file di Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  
L'architettura di SAP Netweaver presentata in questo articolo Usa singolo pool di capacità di file di Azure NetApp, SKU Premium. È consigliabile SKU Premium di Azure NetApp file per carico di lavoro dell'applicazione SAP Netweaver in Azure.  
4. Delegare una subnet per i file di Azure NetApp, come descritto nel [istruzioni delegare una subnet per i file di Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

5. Distribuire i volumi di file di Azure NetApp, seguendo la [istruzioni per creare un volume per i file di Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes). Distribuire i volumi nei file di NetApp Azure designata [subnet](https://docs.microsoft.com/rest/api/virtualnetwork/subnets). Tenere presente che le risorse di Azure i file di NetApp e macchine virtuali di Azure devono trovarsi nella stessa rete virtuale di Azure o in reti virtuali di Azure con peering. In questo esempio viene usato due volumi di file di Azure NetApp: sap<b>QAS</b> e transSAP. I percorsi dei file che sono montati nei punti di montaggio corrispondenti vengono /usrsap<b>qas</b>/sapmnt<b>QAS</b>, /usrsap<b>qas</b>/usrsap<b>QAS</b>sys, and so on.  

   1. sap volume<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/sapmnt<b>QAS</b>)
   2. sap volume<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs)
   3. sap volume<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>sys)
   4. sap volume<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ers)
   5. volume transSAP (nfs://192.168.24.4/transSAP)
   6. sap volume<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>agli indirizzi PA)
   7. sap volume<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>aas)
  
In questo esempio viene usato il file di NetApp Azure per tutti i sistemi di file di SAP Netweaver per illustrare come è possibile usare file di Azure NetApp. I sistemi di file SAP che non devono essere montati tramite NFS possono anche essere distribuiti come [archiviazione di Azure disk](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) . In questo esempio <b>a-e</b> deve essere nel file di Azure NetApp e <b>f-g</b> (vale a dire /usr/SAP/<b>QAS</b>/D<b>02</b>, /usr/SAP/<b>QAS </b>/D<b>03</b>) può essere distribuito come risorsa di archiviazione dischi di Azure. 

### <a name="important-considerations"></a>Considerazioni importanti

Quando si esaminano file NetApp di Azure per SAP Netweaver su architettura a disponibilità elevata di SUSE, tenere presente le considerazioni importanti seguenti:

- Il pool di capacità minima è 4 TiB. Le dimensioni del pool di capacità devono essere in multipli di 4 TiB.
- Il volume minimo è 100 GiB
- File di Azure NetApp e tutte le macchine virtuali, in cui verranno montati volumi di file di Azure NetApp, devono essere nella stessa rete virtuale di Azure o in [eseguire il peering reti virtuali](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) nella stessa area. È ora supportata l'accesso di Azure i file di NetApp su VNET peering nella stessa area. Accesso NetApp Azure tramite il peering globale non è ancora supportata.
- Rete virtuale selezionata deve avere una subnet, delegata NetApp in file di Azure.
- File di NetApp Azure attualmente supporta solo NFSv3 
- File di Azure NetApp offre [esportare criteri](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): è possibile controllare i client consentiti, il tipo di accesso (lettura e scrittura, sola lettura e così via.). 
- La funzionalità Azure file NetApp fuso non è ancora. Funzione Azure NetApp file non è attualmente distribuita in tutte le zone di disponibilità in un'area di Azure. Tenere presenti le potenziali implicazioni per la latenza in alcune aree di Azure. 

## <a name="setting-up-ascs"></a>Configurazione di (A)SCS

In questo esempio, le risorse sono state distribuite manualmente tramite il [portale di Azure](https://portal.azure.com/#home).

### <a name="deploy-linux-manually-via-azure-portal"></a>Distribuire Linux manualmente tramite il portale di Azure

Prima di tutto è necessario creare i volumi di file di Azure NetApp. Distribuire le macchine virtuali. Successivamente, creare un servizio di bilanciamento del carico e usare le macchine virtuali nei pool back-end.

1. Creare un servizio di bilanciamento del carico (interno)  
   1. Creare gli indirizzi IP front-end
      1. Indirizzo IP 192.168.14.9 per ASCS
         1. Aprire il servizio di bilanciamento del carico, selezionare Pool di indirizzi IP front-end e fare clic su Aggiungi
         1. Immettere il nome del nuovo pool di IP front-end (ad esempio **front-end. QAS. ASCS**)
         1. Impostare assegnazione su statico e immettere l'indirizzo IP (ad esempio **192.168.14.9**)
         1. Fare clic su OK.
      1. Indirizzo IP 192.168.14.10 per ASCS ERS
         * Ripetere i passaggi precedenti in "a" per creare un indirizzo IP per ERS (ad esempio **192.168.14.10** e **front-end. QAS. ERS**)
   1. Creare i pool back-end
      1. Creare un pool back-end per ASCS
         1. Aprire il servizio di bilanciamento del carico, selezionare Pool back-end e fare clic su Aggiungi
         1. Immettere il nome del nuovo pool di back-end (ad esempio **back-end. QAS**)
         1. Fare clic su Aggiungi una macchina virtuale.
         1. Selezionare il Set di disponibilità creato in precedenza per ASCS 
         1. Selezionare le macchine virtuali del cluster (A)SCS
         1. Fare clic su OK.
   1. Creare i probe di integrità
      1. Porta 620**00** per ASCS
         1. Aprire il servizio di bilanciamento del carico, selezionare Probe integrità e fare clic su Aggiungi
         1. Immettere il nome del nuovo probe di integrità (ad esempio **integrità. QAS. ASCS**)
         1. Selezionare TCP come protocollo, la porta 620**00**, mantenere 5 per Intervallo e impostare Soglia di non integrità su 2
         1. Fare clic su OK.
      1. Porta 621**01** per ASCS ERS
            * Ripetere i passaggi precedenti in "c" per creare un probe di integrità per ERS (ad esempio 621**01** e **integrità. QAS. ERS**)
   1. Regole di bilanciamento del carico
      1. TCP 32**00** per ASCS
         1. Aprire il servizio di bilanciamento del carico, selezionare le regole di bilanciamento del carico e fare clic su Aggiungi
         1. Immettere il nome della nuova regola di bilanciamento carico (ad esempio **bilanciamento del carico. QAS. ASCS.3200**)
         1. Selezionare l'indirizzo IP front-end per ASCS, il pool back-end e probe di integrità creati in precedenza (ad esempio **front-end. QAS. ASCS**)
         1. Mantenere il protocollo **TCP**, immettere la porta **3200**
         1. Aumentare il timeout di inattività a 30 minuti
         1. **Assicurarsi di abilitare l'indirizzo IP mobile**
         1. Fare clic su OK.
      1. Porte aggiuntive per ASCS
         * Ripetere i passaggi descritti in precedenza in "d" per le porte 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 e TCP per ASCS
      1. Porte aggiuntive per ASCS ERS
         * Ripetere i passaggi descritti in precedenza in "d" per le porte 32**01**, 33**01**, 5**01**13, 5**01**14, 5**01**16 e TCP per ASCS ERS


> [!IMPORTANT]
> Non abilitare TCP timestamp sulle macchine virtuali di Azure posizionato dietro bilanciamento del carico di Azure. Abilitazione di TCP timestamp causerà i probe di integrità errore. Impostare il parametro **net.ipv4.tcp_timestamps** al **0**. Per informazioni dettagliate, vedere [probe di integrità di Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Creare un cluster Pacemaker

Seguire i passaggi della procedura di [configurazione di Pacemaker in Red Hat Enterprise Linux in Azure](high-availability-guide-rhel-pacemaker.md) per creare un cluster Pacemaker di base per questo server (A)SCS.

### <a name="prepare-for-sap-netweaver-installation"></a>Prepararsi per l'installazione di SAP NetWeaver

Gli elementi seguenti sono preceduti dall'indicazione **[A]** - applicabile a tutti i nodi, **[1]** - applicabile solo al nodo 1 o **[2]** - applicabile solo al nodo 2.

1. **[A]** Configurare la risoluzione dei nomi host

   È possibile usare un server DNS o modificare /etc/hosts in tutti i nodi. Questo esempio mostra come usare il file /etc/hosts.
   Sostituire l'indirizzo IP e il nome host nei comandi seguenti

    ```
    sudo vi /etc/hosts
    ```

   Inserire le righe seguenti in /etc/hosts. Modificare l'indirizzo IP e il nome host in base all'ambiente

    ```
    # IP address of cluster node 1
    192.168.14.5    anftstsapcl1
    # IP address of cluster node 2
    192.168.14.6     anftstsapcl2
    # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
    192.168.14.9    anftstsapvh
    # IP address of the load balancer frontend configuration for SAP Netweaver ERS
    192.168.14.10    anftstsapers
    ```

1. **[1]**  Directory creare SAP nel volume di file di Azure NetApp.  
   Montare il volume di file di Azure NetApp temporaneamente su una delle macchine virtuali e creare le directory SAP (percorsi di file).  

    ```
     #mount temporarily the volume
     sudo mkdir -p /saptmp
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 192.168.24.5:/sapQAS /saptmp
     # create the SAP directories
     sudo cd /saptmp
     sudo mkdir -p sapmntQAS
     sudo mkdir -p usrsapQASascs
     sudo mkdir -p usrsapQASers
     sudo mkdir -p usrsapQASsys
     sudo mkdir -p usrsapQASpas
     sudo mkdir -p usrsapQASaas
     # unmount the volume and delete the temporary directory
     sudo cd ..
     sudo umount /saptmp
     sudo rmdir /saptmp

1. **[A]** Create the shared directories

   ```
   sudo mkdir -p /sapmnt/QAS sudo mkdir -p /usr/sap/trans sudo mkdir -p /usr/sap/QAS/SYS sudo mkdir -p /usr/sap/QAS/ASCS00 sudo mkdir -p /usr/sap/QAS/ERS01
   
   Sudo chattr + ho/sapmnt/QAS sudo chattr + si /usr/sap/trans sudo chattr + ho /usr/sap/QAS/SYS sudo chattr + si /usr/sap/QAS/ASCS00 sudo chattr + ho /usr/sap/QAS/ERS01
   ```

1. **[A]** Install NFS client and other requirements

   ```
   sudo yum -y install nfs-utils resource-agents resource-agents-sap
   ```

1. **[A]** Check version of resource-agents-sap

   Make sure that the version of the installed resource-agents-sap package is at least 3.9.5-124.el7
   ```
   Sudo yum risorsa info-agenti-sap
   
   # <a name="loaded-plugins-langpacks-product-id-search-disabled-repos"></a>Caricare i plug-in: langpacks, id prodotto, ricerca-disabled-repository
   # <a name="repodata-is-over-2-weeks-old-install-yum-cron-or-run-yum-makecache-fast"></a>Repodata è superiore a 2 settimane. Installare yum cron? Oppure eseguire: makecache yum veloce
   # <a name="installed-packages"></a>Pacchetti installati
   # <a name="name---------resource-agents-sap"></a>Nome: risorse-agenti-sap
   # <a name="arch---------x8664"></a>Arch        : x86_64
   # <a name="version------395"></a>Versione: 3.9.5
   # <a name="release------124el7"></a>Versione: 124.el7
   # <a name="size---------100-k"></a>Dimensioni: 100 k
   # <a name="repo---------installed"></a>Repository: installata
   # <a name="from-repo----rhel-sap-for-rhel-7-server-rpms"></a>Dal repository: rhel-sap-for-rhel-7-server-rpms
   # <a name="summary------sap-cluster-resource-agents-and-connector-script"></a>Riepilogo: Gli agenti delle risorse del cluster SAP e uno script di connettore
   # <a name="url----------httpsgithubcomclusterlabsresource-agents"></a>URL         : https://github.com/ClusterLabs/resource-agents
   # <a name="license------gplv2"></a>Licenza: GPLv2+
   # <a name="description--the-sap-resource-agents-and-connector-script-interface-with"></a>Descrizione: Il agenti delle risorse SAP e l'interfaccia di script di connettore con
   #          <a name="-pacemaker-to-allow-sap-instances-to-be-managed-in-a-cluster"></a>: Per consentire alle istanze SAP a essere gestiti in un cluster pacemaker
   #          <a name="-environment"></a>: ambiente.
   ```


1. **[A]** Add mount entries

   ```
   sudo vi /etc/fstab
   
   # <a name="add-the-following-lines-to-fstab-save-and-exit"></a>Aggiungere le righe seguenti a fstab, salvare e chiudere
    192.168.24.5:/sapQAS/sapmntQAS/sapmnt/QAS nfs rw, disco rigido, rsize BlockSize=65536, wsize BlockSize=65536, Vers=2.1 = 3 192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw, disco rigido, rsize BlockSize=65536, wsize BlockSize=65536, Vers=2.1 = 3 192.168.24.4:/transSAP /usr/sap/trans nfs rw, disco rigido, rsize. = wsize 65536, BlockSize=65536, Vers=2.1 = 3
   ```

   Mount the new shares

   ```
   sudo mount -a  
   ```

1. **[A]** Configure SWAP file

   ```
   sudo vi /etc/waagent.conf
   
   # <a name="set-the-property-resourcediskenableswap-to-y"></a>Impostare la proprietà ResourceDisk.EnableSwap su y
   # <a name="create-and-use-swapfile-on-resource-disk"></a>Creare e usare file di scambio sul disco risorse.
   ResourceDisk.EnableSwap=y
   
   # <a name="set-the-size-of-the-swap-file-with-property-resourcediskswapsizemb"></a>Impostare le dimensioni del file di scambio con proprietà ResourceDisk.SwapSizeMB
   # <a name="the-free-space-of-resource-disk-varies-by-virtual-machine-size-make-sure-that-you-do-not-set-a-value-that-is-too-big-you-can-check-the-swap-space-with-command-swapon"></a>Lo spazio disponibile del disco delle risorse varia a seconda delle dimensioni di macchina virtuale. Assicurarsi che non si imposta un valore che è troppo grande. È possibile controllare lo spazio di SWAPPING con comando swapon
   # <a name="size-of-the-swapfile"></a>Dimensioni del file di scambio.
   ResourceDisk.SwapSizeMB=2000
   ```

   Restart the Agent to activate the change

   ```
   riavviare il waagent servizio Sudo
   ```

1. **[A]** RHEL configuration

   Configure RHEL as described in SAP Note [2002167]

### Installing SAP NetWeaver ASCS/ERS

1. **[1]** Create a virtual IP resource and health-probe for the ASCS instance

   ```
   Sudo PC nodo in standby anftstsapcl2
   
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     Directory = fstype '/ usr/sap/QAS/ASCS00' = 'nfs' \
     -g-QAS_ASCS di gruppo
   
   la risorsa PC Sudo creare vip_QAS_ASCS IPaddr2 \
     IP = 192.168.14.9 cidr_netmask = 24 \
     -g-QAS_ASCS di gruppo
   
   Sudo PC risorse creare nc_QAS_ASCS porta di azure-lb = 62000 \
     -g-QAS_ASCS di gruppo
   ```

   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

   ```
   stato di PC Sudo
   
   # <a name="node-anftstsapcl2-standby"></a>Nodo anftstsapcl2: standby
   # <a name="online--anftstsapcl1-"></a>Online: [anftstsapcl1]
   #
   # <a name="full-list-of-resources"></a>Elenco completo delle risorse:
   #
   # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl1"></a>rsc_st_azure    (stonith:fence_azure_arm):      Anftstsapcl1 avviata
   #  <a name="resource-group-g-qasascs"></a>Gruppo di risorse: g-QAS_ASCS
   #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl1"></a>fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Anftstsapcl1 avviata
   #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl1"></a>nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Anftstsapcl1 avviata
   #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl1"></a>vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Anftstsapcl1 avviata
   ```

1. **[1]** Install SAP NetWeaver ASCS  

   Install SAP NetWeaver ASCS as root on the first node using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the ASCS, for example <b>anftstsapvh</b>, <b>192.168.14.9</b> and the instance number that you used for the probe of the load balancer, for example <b>00</b>.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   # <a name="allow-access-to-swpm-this-rule-is-not-permanent-if-you-reboot-the-machine-you-have-to-run-the-command-again"></a>Consentire l'accesso a SWPM. Questa regola non è permanente. Se si riavvia il computer, è necessario eseguire nuovamente il comando.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   If the installation fails to create a subfolder in /usr/sap/**QAS**/ASCS**00**, try setting the owner and group of the ASCS**00** folder and retry.

   ```
   sudo chown qasadm /usr/sap/QAS/ASCS00 sudo chgrp sapsys /usr/sap/QAS/ASCS00
   ```

1. **[1]** Create a virtual IP resource and health-probe for the ERS instance

   ```
   Sudo PC nodo anftstsapcl2 unstandby sudo PC nodo in standby anftstsapcl1
   
   la risorsa PC Sudo creare fs_QAS_AERS device='192.168.24.5:/sapQAS/usrsapQASers Filesystem' \
     Directory = fstype '/ usr/sap/QAS/ERS01' = 'nfs' \
    --group g-QAS_AERS

   la risorsa PC Sudo creare vip_QAS_AERS IPaddr2 \
     IP = 192.168.14.10 cidr_netmask = 24 \
    --group g-QAS_AERS
   
   sudo pcs resource create nc_QAS_AERS azure-lb port=62101 \
    --group g-QAS_AERS
   ```
 
   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

   ```
   stato di PC Sudo
   
   # <a name="node-anftstsapcl1-standby"></a>Nodo anftstsapcl1: standby
   # <a name="online--anftstsapcl2-"></a>Online: [anftstsapcl2]
   #
   # <a name="full-list-of-resources"></a>Elenco completo delle risorse:
   #
   # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl2"></a>rsc_st_azure    (stonith:fence_azure_arm):      Anftstsapcl2 avviata
   #  <a name="resource-group-g-qasascs"></a>Gruppo di risorse: g-QAS_ASCS
   #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Anftstsapcl2 avviata
   #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Avviato anftstsapcl2 <
   #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Anftstsapcl2 avviata
   #  <a name="resource-group-g-qasaers"></a>Gruppo di risorse: g-QAS_AERS
   #      <a name="fsqasaers--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Anftstsapcl2 avviata
   #      <a name="ncqasaers--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Anftstsapcl2 avviata
   #      <a name="vipqasaers-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Anftstsapcl2 avviata
   ```

1. **[2]** Install SAP NetWeaver ERS  

   Install SAP NetWeaver ERS as root on the second node using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the ERS, for example <b>anftstsapers</b>, <b>192.168.14.10</b> and the instance number that you used for the probe of the load balancer, for example <b>01</b>.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   # <a name="allow-access-to-swpm-this-rule-is-not-permanent-if-you-reboot-the-machine-you-have-to-run-the-command-again"></a>Consentire l'accesso a SWPM. Questa regola non è permanente. Se si riavvia il computer, è necessario eseguire nuovamente il comando.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   If the installation fails to create a subfolder in /usr/sap/**QAS**/ERS**01**, try setting the owner and group of the ERS**01** folder and retry.

   ```
   sudo chown qaadm /usr/sap/QAS/ERS01 sudo chgrp sapsys /usr/sap/QAS/ERS01
   ```

1. **[1]** Adapt the ASCS/SCS and ERS instance profiles

   * ASCS/SCS profile

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh
   
   # <a name="change-the-restart-command-to-a-start-command"></a>Modificare il comando di riavvio in un comando di avvio
   #<a name="restartprogram01--local-en-pfpf"></a>Restart_Program_01 = pf=$(_PF) $(_EN) locale
   Start_Program_01 = pf=$(_PF) $(_EN) locale
   
   # <a name="add-the-keep-alive-parameter"></a>Aggiungere il parametro di keep-alive
   enque/encni/set_so_keepalive = true
   ```

   * ERS profile

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ERS01_anftstsapers
   
   # <a name="change-the-restart-command-to-a-start-command"></a>Modificare il comando di riavvio in un comando di avvio
   #<a name="restartprogram00--local-er-pfpfl-nrscsid"></a>Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # <a name="remove-autostart-from-ers-profile"></a>rimuovere l'avvio automatico dal profilo ERS
   # <a name="autostart--1"></a>Avvio automatico = 1
   ```


1. **[A]** Configure Keep Alive

   The communication between the SAP NetWeaver application server and the ASCS/SCS is routed through a software load balancer. The load balancer disconnects inactive connections after a configurable timeout. To prevent this, you need to set a parameter in the SAP NetWeaver ASCS/SCS profile and change the Linux system settings. Read [SAP Note 1410736][1410736] for more information.

   The ASCS/SCS profile parameter enque/encni/set_so_keepalive was already added in the last step.

   ```
   # <a name="change-the-linux-system-configuration"></a>Modificare la configurazione di sistema di Linux
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   ```

1. **[A]** Update the /usr/sap/sapservices file

   To prevent the start of the instances by the sapinit startup script, all instances managed by Pacemaker must be commented out from /usr/sap/sapservices file. Do not comment out the SAP HANA instance if it will be used with HANA SR.

   ```
   sudo vi /usr/sap/sapservices
   
   # <a name="on-the-node-where-you-installed-the-ascs-comment-out-the-following-line"></a>Nel nodo in cui è installata l'istanza ASCS, impostare come commento la riga seguente
   # <a name="ldlibrarypathusrsapqasascs00exeldlibrarypath-export-ldlibrarypath-usrsapqasascs00exesapstartsrv-pfusrsapqassysprofileqasascs00anftstsapvh--d--u-qasadm"></a>LD_LIBRARY_PATH=/usr/sap/QAS/ASCS00/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ASCS00/exe/sapstartsrv pf=/usr/sap/QAS/SYS/profile/QAS_ASCS00_anftstsapvh -D -u qasadm
   
   # <a name="on-the-node-where-you-installed-the-ers-comment-out-the-following-line"></a>Nel nodo in cui è installato ERS, impostare come commento la riga seguente
   # <a name="ldlibrarypathusrsapqasers01exeldlibrarypath-export-ldlibrarypath-usrsapqasers01exesapstartsrv-pfusrsapqasers01profileqasers01anftstsapers--d--u-qasadm"></a>LD_LIBRARY_PATH = / usr/sap/QAS/ERS01/file exe: $LD_LIBRARY_PATH; esportazione LD_LIBRARY_PATH; /usr/SAP/QAS/ERS01/exe/sapstartsrv pf = / usr/sap/QAS/ERS01/profilo/QAS_ERS01_anftstsapers - D -u qasadm
   ```

1. **[1]** Create the SAP cluster resources

   If using enqueue server 1 architecture (ENSA1), define the resources as follows:

   ```
   proprietà PC Sudo impostata la modalità di manutenzione = true
   
    la risorsa PC Sudo creare rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER = false \
    risorse di Meta-adesione = 5000 migrazione-soglia ad=1 \
    -g-QAS_ASCS di gruppo
   
    la risorsa PC Sudo creare rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER = false IS_ERS = true \
    --group g-QAS_AERS
      
    con più sedi vincolo PC Sudo aggiungere g-QAS_AERS con punteggio regola g-QAS_ASCS-5000 sudo PC vincolo percorso rsc_sap_QAS_ASCS00 = 2000 runs_ers_QAS eq 1 sudo PC ordine g-QAS_ASCS quindi QAS_AERS g tipo di vincolo facoltativo simmetrico = = false
    
    la proprietà Sudo PC nodo anftstsapcl1 unstandby sudo PC impostata in modalità di manutenzione = false
    ```

   SAP introduced support for enqueue server 2, including replication, as of SAP NW 7.52. Starting with ABAP Platform 1809, enqueue server 2 is installed by default. See SAP note [2630416](https://launchpad.support.sap.com/#/notes/2630416) for enqueue server 2 support.
   If using enqueue server 2 architecture ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), install resource agent resource-agents-sap-4.1.1-12.el7.x86_64 or newer and define the resources as follows:

    ```
    proprietà PC Sudo impostata la modalità di manutenzione = true
    
    la risorsa PC Sudo creare rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER = false \
    risorse di Meta-adesione = 5000 \
    -g-QAS_ASCS di gruppo
   
    la risorsa PC Sudo creare rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER = false IS_ERS = true \
    --group g-QAS_AERS
      
    con più sedi vincolo PC Sudo aggiungere g-QAS_AERS con g-QAS_ASCS-5000 sudo PC ordine g-QAS_ASCS quindi QAS_AERS g tipo di vincolo facoltativo simmetrico = = false
   
    la proprietà Sudo PC nodo anftstsapcl1 unstandby sudo PC impostata in modalità di manutenzione = false
    ```

   If you are upgrading from an older version and switching to enqueue server 2, see SAP note [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

    ```
    stato di PC Sudo
    
    # <a name="online--anftstsapcl1-anftstsapcl2-"></a>Online: [anftstsapcl1 anftstsapcl2]
    #
    # <a name="full-list-of-resources"></a>Elenco completo delle risorse:
    #
    # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl2"></a>rsc_st_azure    (stonith:fence_azure_arm):      Anftstsapcl2 avviata
    #  <a name="resource-group-g-qasascs"></a>Gruppo di risorse: g-QAS_ASCS
    #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Anftstsapcl2 avviata
    #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Anftstsapcl2 avviata
    #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Anftstsapcl2 avviata
    #      <a name="rscsapqasascs00-ocfheartbeatsapinstance---started-anftstsapcl2"></a>rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Anftstsapcl2 avviata
    #  <a name="resource-group-g-qasaers"></a>Gruppo di risorse: g-QAS_AERS
    #      <a name="fsqasaers--------ocfheartbeatfilesystem----started-anftstsapcl1"></a>fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Anftstsapcl1 avviata
    #      <a name="ncqasaers--------ocfheartbeatazure-lb------started-anftstsapcl1"></a>nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Anftstsapcl1 avviata
    #      <a name="vipqasaers-------ocfheartbeatipaddr2-------started-anftstsapcl1"></a>vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Anftstsapcl1 avviata
    #      <a name="rscsapqasers01--ocfheartbeatsapinstance---started-anftstsapcl1"></a>rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Anftstsapcl1 avviata
   ```

1. **[A]** Add firewall rules for ASCS and ERS on both nodes
   Add the firewall rules for ASCS and ERS on both nodes.
   ```
   # <a name="probe-port-of-ascs"></a>Porta probe di ASCS
   firewall Sudo-cmd - zona pubblica - aggiungere-port = = 62000/tcp-- firewall permanente sudo-cmd - zona = pubblica - aggiungere-port = firewall/tcp 62000 sudo-cmd - zona = pubblica - aggiungere-port = 3200/tcp-- firewall permanente sudo-cmd - zona = pubblica - aggiungere-port = sudo 3200/tcp firewall-cmd - zona pubblica - aggiungere-port = = 3600/tcp-- firewall permanente sudo-cmd - zona = pubblica - aggiungere-port = firewall 3600/tcp sudo-cmd - zona = pubblica - aggiungere-port = 3900/tcp-- firewall permanente sudo-cmd - zona = pubblica - aggiungere-port = sudo 3900/tcp firewall-cmd - zona pubblica - aggiungere-port = = tcp 8100, firewall permanente sudo-cmd - zona = pubblica - aggiungere-port = firewall/tcp 8100 sudo-cmd - zona = pubblica - aggiungere-port = 50013/tcp-- firewall permanente sudo-cmd - zona = pubblica - aggiungere-port = sudo 50013/tcp firewall-cmd - zona = pubblica - aggiungere-port = 50014/tcp-- firewall permanente sudo-cmd - zona pubblica - aggiungere-port = = firewall 50014/tcp sudo-cmd - zona = pubblica - aggiungere-port = 50016/tcp-- firewall permanente sudo-cmd - zona = pubblica - aggiungere-port = 50016/tcp
   # <a name="probe-port-of-ers"></a>Porta probe del ERS
   firewall Sudo-cmd - zona = pubblica - aggiungere-port = 62101/tcp-- firewall permanente sudo-cmd - zona pubblica - aggiungere-port = = firewall 62101/tcp sudo-cmd - zona = pubblica - aggiungere-port = 3301/tcp-- firewall permanente sudo-cmd - zona = pubblica - aggiungere-port = sudo 3301/tcp firewall-cmd - zona = pubblica - aggiungere-port = 50113/tcp-- firewall permanente sudo-cmd - zona pubblica - aggiungere-port = = firewall 50113/tcp sudo-cmd - zona = pubblica - aggiungere-port = 50114/tcp-- firewall permanente sudo-cmd - zona = pubblica - aggiungere-port = sudo 50114/tcp firewall-cmd - zona pubblica - aggiungere-port = = 50116/tcp-- firewall permanente sudo-cmd - zona pubblica - aggiungere-port = = 50116/tcp
   ```

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver application server preparation

   Some databases require that the database instance installation is executed on an application server. Prepare the application server virtual machines to be able to use them in these cases.  

   The steps bellow assume that you install the application server on a server different from the ASCS/SCS and HANA servers. Otherwise some of the steps below (like configuring host name resolution) are not needed.  

   The following items are prefixed with either **[A]** - applicable to both PAS and AAS, **[P]** - only applicable to PAS or **[S]** - only applicable to AAS.  

1. **[A]** Setup host name resolution
   You can either use a DNS server or modify the /etc/hosts on all nodes. This example shows how to use the /etc/hosts file.
   Replace the IP address and the hostname in the following commands:  

   ```
   sudo vi /etc/hosts
   ```

   Insert the following lines to /etc/hosts. Change the IP address and hostname to match your environment.

   ```
   # <a name="ip-address-of-the-load-balancer-frontend-configuration-for-sap-netweaver-ascs"></a>Indirizzo IP della configurazione front-end di bilanciamento del carico per SAP NetWeaver ASCS
   192.168.14.9 anftstsapvh
   # <a name="ip-address-of-the-load-balancer-frontend-configuration-for-sap-netweaver-ascs-ers"></a>Indirizzo IP della configurazione front-end di bilanciamento del carico per SAP NetWeaver ASCS ERS
   192.168.14.10 anftstsapers 192.168.14.7 anftstsapa01 192.168.14.8 anftstsapa02
   ```

1. **[A]** Create the sapmnt directory
   Create the sapmnt directory.
   ```
   Sudo mkdir -p/sapmnt/QAS sudo mkdir -p /usr/sap/trans

   Sudo chattr + ho/sapmnt/QAS sudo chattr + ho /usr/sap/trans
   ```

1. **[A]** Install NFS client and other requirements  

   ```
   sudo yum -y install nfs-utils uuidd
   ```

1. **[A]** Add mount entries  

   ```
   sudo vi /etc/fstab
   
   # <a name="add-the-following-lines-to-fstab-save-and-exit"></a>Aggiungere le righe seguenti a fstab, salvare e chiudere
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3 192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Mount the new shares

   ```
   sudo mount -a
   ```

1. **[P]** Create and mount the PAS directory  

   ```
   sudo mkdir -p /usr/sap/QAS/D02 sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # <a name="add-the-following-line-to-fstab"></a>Aggiungere la riga seguente a fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # <a name="mount"></a>Eseguire il montaggio
   sudo mount -a
   ```

1. **[S]** Create and mount the AAS directory  

   ```
   sudo mkdir -p /usr/sap/QAS/D03 sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # <a name="add-the-following-line-to-fstab"></a>Aggiungere la riga seguente a fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # <a name="mount"></a>Eseguire il montaggio
   sudo mount -a
   ```


1. **[A]** Configure SWAP file
 
   ```
   sudo vi /etc/waagent.conf
   
   # <a name="set-the-property-resourcediskenableswap-to-y"></a>Impostare la proprietà ResourceDisk.EnableSwap su y
   # <a name="create-and-use-swapfile-on-resource-disk"></a>Creare e usare file di scambio sul disco risorse.
   ResourceDisk.EnableSwap=y
   
   # <a name="set-the-size-of-the-swap-file-with-property-resourcediskswapsizemb"></a>Impostare le dimensioni del file di scambio con proprietà ResourceDisk.SwapSizeMB
   # <a name="the-free-space-of-resource-disk-varies-by-virtual-machine-size-make-sure-that-you-do-not-set-a-value-that-is-too-big-you-can-check-the-swap-space-with-command-swapon"></a>Lo spazio disponibile del disco delle risorse varia a seconda delle dimensioni di macchina virtuale. Assicurarsi che non si imposta un valore che è troppo grande. È possibile controllare lo spazio di SWAPPING con comando swapon
   # <a name="size-of-the-swapfile"></a>Dimensioni del file di scambio.
   ResourceDisk.SwapSizeMB=2000
   ```

   Restart the Agent to activate the change

   ```
   riavviare il waagent servizio Sudo
   ```

## Install database

In this example, SAP NetWeaver is installed on SAP HANA. You can use every supported database for this installation. For more information on how to install SAP HANA in Azure, see [High availability of SAP HANA on Azure VMs on Red Hat Enterprise Linux][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533].

1. Run the SAP database instance installation

   Install the SAP NetWeaver database instance as root using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the database.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## SAP NetWeaver application server installation

Follow these steps to install an SAP application server.

1. Prepare application server

   Follow the steps in the chapter [SAP NetWeaver application server preparation](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) above to prepare the application server.

1. Install SAP NetWeaver application server

   Install a primary or additional SAP NetWeaver applications server.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

1. Update SAP HANA secure store

   Update the SAP HANA secure store to point to the virtual name of the SAP HANA System Replication setup.

   Run the following command to list the entries as \<sapsid>adm

   ```
   Elenco hdbuserstore
   ```

   This should list all entries and should look similar to
   ```
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   ENV PREDEFINITI PRINCIPALI: 192.168.14.4:30313   USER: SAPABAP1   DATABASE: QAS
   ```

   The output shows that the IP address of the default entry is pointing to the virtual machine and not to the load balancer's IP address. This entry needs to be changed to point to the virtual hostname of the load balancer. Make sure to use the same port (**30313** in the output above) and database name (**QAS** in the output above)!

   ```
   unità di streaming - hdbuserstore qasadm SET DEFAULT qasdb:30313@QAS SAPABAP1 <password of ABAP schema>
   ```

## Test the cluster setup

1. Manually migrate the ASCS instance

   Resource state before starting the test:

   ```
    rsc_st_azure    (stonith:fence_azure_arm):      Avviare il gruppo di risorse anftstsapcl1: g-QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Avviare il gruppo di risorse anftstsapcl1: g-QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Anftstsapcl2 avviata
   ```

   Run the following commands as root to migrate the ASCS instance.

   ```
   [root@anftstsapcl1 ~] rsc_sap_QAS_ASCS00 lo spostamento di risorse di PC &
   
   [root@anftstsapcl1 ~] risorsa PC & cancellare rsc_sap_QAS_ASCS00
   
   # <a name="remove-failed-actions-for-the-ers-that-occurred-as-part-of-the-migration"></a>Rimuovere le azioni non riuscite per ERS che si sono verificate durante la migrazione
   [root@anftstsapcl1 ~] & PC risorse pulizia rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Avviare il gruppo di risorse anftstsapcl1: g-QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Avviare il gruppo di risorse anftstsapcl2: g-QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Anftstsapcl1 avviata
   ```

1. Simulate node crash

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Avviare il gruppo di risorse anftstsapcl1: g-QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Avviare il gruppo di risorse anftstsapcl2: g-QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Anftstsapcl1 avviata
   ```

   Run the following command as root on the node where the ASCS instance is running

   ```
   [root@anftstsapcl2 ~] echo & b > /proc/sysrq-trigger
   ```

   The status after the node is started again should look like this.

   ```
   Online: [anftstsapcl1 anftstsapcl2]
   
   Elenco completo delle risorse:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Avviare il gruppo di risorse anftstsapcl1: g-QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Avviare il gruppo di risorse anftstsapcl1: g-QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Anftstsapcl2 avviata
   
   Azioni non riuscite:
   * rsc_sap_QAS_ERS01_monitor_11000 su anftstsapcl1 'non è in esecuzione' (7): chiamare = 45, stato = completo, exitreason = ',
   ```

   Use the following command to clean the failed resources.

   ```
   [root@anftstsapcl1 ~] & PC risorse pulizia rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Avviare il gruppo di risorse anftstsapcl1: g-QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Avviare il gruppo di risorse anftstsapcl1: g-QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Anftstsapcl2 avviata
   ```

1. Kill message server process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Avviare il gruppo di risorse anftstsapcl1: g-QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Avviare il gruppo di risorse anftstsapcl1: g-QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Anftstsapcl2 avviata
   ```
   
   Run the following commands as root to identify the process of the message server and kill it.

   ```
   [root@anftstsapcl1 ~] & pgrep ms.sapQAS | xargs kill -9
   ```

   If you only kill the message server once, it will be restarted by `sapstart`. If you kill it often enough, Pacemaker will eventually move the ASCS instance to the other node. Run the following commands as root to clean up the resource state of the ASCS and ERS instance after the test.

   ```
   [root@anftstsapcl1 ~] & PC risorse pulizia rsc_sap_QAS_ASCS00 [root@anftstsapcl1 ~] & PC risorse pulizia rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Avviare il gruppo di risorse anftstsapcl1: g-QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Avviare il gruppo di risorse anftstsapcl2: g-QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Anftstsapcl1 avviata
   ```

1. Kill enqueue server process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Avviare il gruppo di risorse anftstsapcl1: g-QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Avviare il gruppo di risorse anftstsapcl2: g-QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Anftstsapcl1 avviata
   ```

   Run the following commands as root on the node where the ASCS instance is running to kill the enqueue server.

   ```
   [root@anftstsapcl2 ~] & pgrep en.sapQAS | xargs kill -9
   ```

   The ASCS instance should immediately fail over to the other node. The ERS instance should also fail over after the ASCS instance is started. Run the following commands as root to clean up the resource state of the ASCS and ERS instance after the test.

   ```
   [root@anftstsapcl2 ~] & PC risorse pulizia rsc_sap_QAS_ASCS00 [root@anftstsapcl2 ~] & PC risorse pulizia rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Avviare il gruppo di risorse anftstsapcl1: g-QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Avviare il gruppo di risorse anftstsapcl1: g-QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Anftstsapcl2 avviata
   ```

1. Kill enqueue replication server process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Avviare il gruppo di risorse anftstsapcl1: g-QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Avviare il gruppo di risorse anftstsapcl1: g-QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Anftstsapcl2 avviata
   ```

   Run the following command as root on the node where the ERS instance is running to kill the enqueue replication server process.

   ```
   [root@anftstsapcl2 ~] & pgrep er.sapQAS | xargs kill -9
   ```

   If you only run the command once, `sapstart` will restart the process. If you run it often enough, `sapstart` will not restart the process and the resource will be in a stopped state. Run the following commands as root to clean up the resource state of the ERS instance after the test.

   ```
   [root@anftstsapcl2 ~] & PC risorse pulizia rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Avviare il gruppo di risorse anftstsapcl1: g-QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Avviare il gruppo di risorse anftstsapcl1: g-QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Anftstsapcl2 avviata
   ```

1. Kill enqueue sapstartsrv process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Avviare il gruppo di risorse anftstsapcl1: g-QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Avviare il gruppo di risorse anftstsapcl1: g-QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Anftstsapcl2 avviata
   ```

   Run the following commands as root on the node where the ASCS is running.

   ```
   [root@anftstsapcl1 ~] & pgrep -fl ASCS00.*sapstartsrv
   # <a name="59545-sapstartsrv"></a>sapstartsrv 59545
   
   [root@anftstsapcl1 ~] kill & 59545-9
   ```

   The sapstartsrv process should always be restarted by the Pacemaker resource agent as part of the monitoring. Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Avviare il gruppo di risorse anftstsapcl1: g-QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):    Started anftstsapcl1      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Avviare il gruppo di risorse anftstsapcl1: g-QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Anftstsapcl2 avviata
   ```

## Next steps

* [Azure Virtual Machines planning and implementation for SAP][planning-guide]
* [Azure Virtual Machines deployment for SAP][deployment-guide]
* [Azure Virtual Machines DBMS deployment for SAP][dbms-guide]
* To learn how to establish high availability and plan for disaster recovery of SAP HANA on Azure (large instances), see [SAP HANA (large instances) high availability and disaster recovery on Azure](hana-overview-high-availability-disaster-recovery.md).
* To learn how to establish high availability and plan for disaster recovery of SAP HANA on Azure VMs, see [High Availability of SAP HANA on Azure Virtual Machines (VMs)][sap-hana-ha]
