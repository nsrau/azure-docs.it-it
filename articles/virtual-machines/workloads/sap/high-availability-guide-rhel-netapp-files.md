---
title: Disponibilità elevata di macchine virtuali di Azure per SAP NetWeaver in Red Hat Enterprise Linux con Azure NetApp Files | Microsoft Docs
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
ms.date: 11/07/2019
ms.author: radeltch
ms.openlocfilehash: ba8dc3080f3b584ae3a60576e4cc670dc60c28a0
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151819"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>Disponibilità elevata di macchine virtuali di Azure per SAP NetWeaver in Red Hat Enterprise Linux con Azure NetApp Files per le applicazioni SAP

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

Questo articolo descrive come distribuire le macchine virtuali, configurare le macchine virtuali, installare il Framework del cluster e installare un sistema SAP NetWeaver 7,50 a disponibilità elevata usando [Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/).
Nelle configurazioni di esempio, i comandi di installazione e così via. L'istanza di ASC è il numero 00, l'istanza ERS è il numero 01, l'istanza dell'applicazione primaria (PAS) è 02 e l'istanza dell'applicazione (AAS) è 03. Viene usato l'ID del sistema SAP QAS. 

Il livello del database non è illustrato in dettaglio in questo articolo.  

Leggere prima di tutto i documenti e le note SAP seguenti:

* [Documentazione di Azure NetApp Files][anf-azure-doc] 
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
* [Pianificazione e implementazione di macchine virtuali di Azure per SAP in Linux][planning-guide]
* [Distribuzione di macchine virtuali di Azure per SAP in Linux][deployment-guide]
* [Distribuzione DBMS di macchine virtuali di Azure per SAP in Linux][dbms-guide]
* [SAP NetWeaver nel cluster Pacemaker](https://access.redhat.com/articles/3150081)
* Documentazione generale di RHEL
  * [High Availability Add-On Overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index) (Panoramica dei componenti aggiuntivi a disponibilità elevata)
  * [High Availability Add-On Administration](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index) (Amministrazione dei componenti aggiuntivi a disponibilità elevata)
  * [High Availability Add-On Reference](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index) (Riferimento dei componenti aggiuntivi a disponibilità elevata)
  * [Configurazione di ASCS/ERS per SAP Netweaver con risorse autonome in RHEL 7.5](https://access.redhat.com/articles/3569681)
  * [Configurare SAP S/4HANA ASC/ERS con il server di Accodamento autonomo 2 (ENSA2) in pacemaker in RHEL](https://access.redhat.com/articles/3974941)
* Documentazione di RHEL specifica di Azure:
  * [Support Policies for RHEL High Availability Clusters - Microsoft Azure Virtual Machines as Cluster Members](https://access.redhat.com/articles/3131341) (Criteri di supporto per cluster RHEL a disponibilità elevata - Macchine virtuali di Microsoft Azure come membri del cluster)
  * [Installing and Configuring a Red Hat Enterprise Linux 7.4 (and later) High-Availability Cluster on Microsoft Azure](https://access.redhat.com/articles/3252491) (Installazione e configurazione di un cluster Red Hat Enterprise Linux 7.4 e versioni successive a disponibilità elevata in Microsoft Azure)
* [Applicazioni SAP NetApp su Microsoft Azure con Azure NetApp Files][anf-sap-applications-azure]

## <a name="overview"></a>Overview

La disponibilità elevata per i servizi SAP NetWeaver Central richiede l'archiviazione condivisa.
A tale scopo, in Red Hat Linux finora era necessario compilare un cluster GlusterFS a disponibilità elevata separato. 

A questo punto è possibile ottenere la disponibilità elevata di SAP NetWeaver usando l'archiviazione condivisa, distribuita in Azure NetApp Files. L'uso di Azure NetApp Files per l'archiviazione condivisa Elimina la necessità di un altro [cluster GlusterFS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs). Pacemaker è ancora necessario per la disponibilità elevata di SAP NetWeaver Central Services (ASC/SCS).

![Panoramica della disponibilità elevata di SAP NetWeaver](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS e il database SAP HANA usano un nome host virtuale e indirizzi IP virtuali. Per usare un indirizzo IP virtuale in Azure, occorre il bilanciamento del carico. È consigliabile usare [Load Balancer standard](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal). L'elenco seguente mostra la configurazione del servizio di bilanciamento del carico con IP front-end distinti per (A) SCS e ERS.

> [!IMPORTANT]
> Il clustering a più SID di SAP ASC/ERS con Red Hat Linux come sistema operativo guest nelle macchine virtuali di Azure **non è supportato**. Il clustering a più SID descrive l'installazione di più istanze di SAP ASC/ERS con SID diversi in un cluster pacemaker.

### <a name="ascs"></a>(A)SCS

* Configurazione front-end
  * Indirizzo IP 192.168.14.9
* Configurazione back-end
  * Connessione alle interfacce di rete primarie di tutte le macchine virtuali che devono far parte del cluster (A)SCS/ERS
* Porta probe
  * Porta 620<strong>&lt;nr&gt;</strong>
* Regole di bilanciamento del carico
  * Se si usa Load Balancer Standard, selezionare **porte a disponibilità elevata**
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
  * Se si usa Load Balancer Standard, selezionare **porte a disponibilità elevata**
  * 32<strong>&lt;nr&gt;</strong> TCP
  * 33<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Configurazione dell'infrastruttura di Azure NetApp Files 

SAP NetWeaver richiede un'archiviazione condivisa per la directory di trasporto e del profilo.  Prima di procedere con l'installazione dell'infrastruttura dei file di Azure NetApp, acquisire familiarità con la [documentazione Azure NetApp files][anf-azure-doc]. Controllare se l'area di Azure selezionata offre Azure NetApp Files. Il collegamento seguente mostra la disponibilità di Azure NetApp Files dall'area di Azure: [Azure NetApp files disponibilità per area di Azure][anf-avail-matrix].

I file di Azure NetApp sono disponibili in diverse [aree di Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Prima di distribuire Azure NetApp Files, richiedere l'onboarding a Azure NetApp Files, seguendo le [istruzioni per la registrazione di file NetApp di Azure][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Distribuisci risorse Azure NetApp Files  

I passaggi presuppongono che sia già stata distribuita la [rete virtuale di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Le risorse Azure NetApp Files e le macchine virtuali in cui verranno montate le risorse di Azure NetApp Files devono essere distribuite nella stessa rete virtuale di Azure o in reti virtuali di Azure con peering.  

1. Se non è già stato fatto, richiedere l' [onboarding per Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register).  
2. Creare l'account NetApp nell'area di Azure selezionata seguendo le [istruzioni per la creazione dell'account NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  
3. Configurare Azure NetApp Files pool di capacità seguendo le [istruzioni su come configurare Azure NetApp files pool di capacità](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  
L'architettura di SAP NetWeaver presentata in questo articolo usa il pool di capacità singolo Azure NetApp Files, SKU Premium. È consigliabile Azure NetApp Files SKU Premium per il carico di lavoro dell'applicazione SAP NetWeaver in Azure.  
4. Delegare una subnet ai file di Azure NetApp come descritto nelle [istruzioni delegare una subnet a Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

5. Distribuire Azure NetApp Files volumi, seguendo le [istruzioni per creare un volume per Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes). Distribuire i volumi nella [subnet](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)Azure NetApp files designata. Tenere presente che le risorse Azure NetApp Files e le macchine virtuali di Azure devono trovarsi nella stessa rete virtuale di Azure o in reti virtuali di Azure con peering. In questo esempio vengono usati due volumi Azure NetApp Files: SAP<b>QAS</b> e transSAP. I percorsi dei file montati nei punti di montaggio corrispondenti sono/usrsap<b>QAS</b>/sapmnt<b>QAS</b><b>,/usrsap QAS/usrsap QAS</b><b>sys e</b>così via.  

   1. volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/sapmnt<b>QAS</b>)
   2. volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>ASC)
   3. volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>sys)
   4. volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>ERS)
   5. volume transSAP (nfs://192.168.24.4/transSAP)
   6. volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>pas)
   7. volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>AAS)
  
In questo esempio è stato usato Azure NetApp Files per tutti i file System di SAP NetWeaver per dimostrare come è possibile usare Azure NetApp Files. I file System SAP che non devono essere montati tramite NFS possono anche essere distribuiti come [archiviazione su disco di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) . In questo esempio <b>a-e</b> deve essere in Azure NetApp files e <b>f-g</b> (ovvero,/usr/SAP/<b>QAS</b>/d<b>02</b>,/usr/SAP/<b>QAS</b>/d<b>03</b>) può essere distribuito come archiviazione su disco di Azure. 

### <a name="important-considerations"></a>Considerazioni importanti

Quando si prende in considerazione Azure NetApp Files per SAP NetWeaver sull'architettura a disponibilità elevata SUSE, tenere presente le considerazioni seguenti:

- Il pool di capacità minimo è 4 TiB. È possibile aumentare le dimensioni del pool di capacità con incrementi di 1 TiB.
- Il volume minimo è 100 GiB
- Azure NetApp Files e tutte le macchine virtuali, in cui verranno montati i volumi Azure NetApp Files, devono trovarsi nella stessa rete virtuale di Azure o in [reti virtuali con peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) nella stessa area. Azure NetApp Files l'accesso al peering VNET nella stessa area è ora supportato. L'accesso a NetApp di Azure sul peering globale non è ancora supportato.
- La rete virtuale selezionata deve avere una subnet, delegata a Azure NetApp Files.
- Azure NetApp Files offre [criteri di esportazione](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): è possibile controllare i client consentiti, il tipo di accesso (lettura & scrittura, sola lettura e così via). 
- Azure NetApp Files funzionalità non è ancora in grado di riconoscere la zona. Attualmente Azure NetApp Files funzionalità non viene distribuita in tutte le zone di disponibilità in un'area di Azure. Tenere presente le implicazioni potenziali di latenza in alcune aree di Azure. 

## <a name="setting-up-ascs"></a>Configurazione di (A)SCS

In questo esempio, le risorse sono state distribuite manualmente tramite il [portale di Azure](https://portal.azure.com/#home).

### <a name="deploy-linux-manually-via-azure-portal"></a>Distribuire Linux manualmente tramite il portale di Azure

Per prima cosa è necessario creare i volumi Azure NetApp Files. Distribuire le macchine virtuali. Successivamente, creare un servizio di bilanciamento del carico e usare le macchine virtuali nei pool back-end.

1. Creare un servizio di bilanciamento del carico (interno, standard):  
   1. Creare gli indirizzi IP front-end
      1. Indirizzo IP 192.168.14.9 per ASC
         1. Aprire il servizio di bilanciamento del carico, selezionare Pool di indirizzi IP front-end e fare clic su Aggiungi
         1. Immettere il nome del nuovo pool di indirizzi IP front-end, ad esempio front- **end. QAS. ASC**)
         1. Impostare l'assegnazione su statico e immettere l'indirizzo IP (ad esempio **192.168.14.9**)
         1. Fare clic su OK.
      1. Indirizzo IP 192.168.14.10 per ASC ERS
         * Ripetere i passaggi precedenti in "a" per creare un indirizzo IP per ERS, ad esempio **192.168.14.10** e front- **end. QAS. ERS**)
   1. Creare i pool back-end
      1. Creare un pool back-end per ASCS
         1. Aprire il servizio di bilanciamento del carico, selezionare Pool back-end e fare clic su Aggiungi
         1. Immettere il nome del nuovo pool back-end, ad esempio **back-end. QAS**)
         1. Fare clic su Aggiungi una macchina virtuale.
         1. Selezionare macchina virtuale. 
         1. Selezionare le macchine virtuali del cluster (A) SCS e i relativi indirizzi IP.
         1. Fare clic su Aggiungi.
   1. Creare i probe di integrità
      1. Porta 620**00** per ASCS
         1. Aprire il servizio di bilanciamento del carico, selezionare Probe integrità e fare clic su Aggiungi
         1. Immettere il nome del nuovo probe di integrità, ad esempio **integrità. QAS. ASC**)
         1. Selezionare TCP come protocollo, la porta 620**00**, mantenere 5 per Intervallo e impostare Soglia di non integrità su 2
         1. Fare clic su OK.
      1. Porta 621**01** per ASC ERS
            * Ripetere i passaggi precedenti in "c" per creare un probe di integrità per ERS, ad esempio 621**01** e **Health. QAS. ERS**)
   1. Regole di bilanciamento del carico
      1. Regole di bilanciamento del carico per ASC
         1. Aprire il servizio di bilanciamento del carico, selezionare regole di bilanciamento del carico e fare clic su Aggiungi.
         1. Immettere il nome della nuova regola di bilanciamento del carico, ad esempio **lb. QAS. ASC**)
         1. Selezionare l'indirizzo IP front-end per ASC, il pool back-end e il probe di integrità creati in precedenza, ad esempio front- **end. QAS. ASC**, **back-end. QAS** e **Health. QAS. ASC**)
         1. Selezionare le **porte a disponibilità elevata**
         1. Aumentare il timeout di inattività a 30 minuti
         1. **Assicurarsi di abilitare l'indirizzo IP mobile**
         1. Fare clic su OK.
         * Ripetere i passaggi precedenti per creare regole di bilanciamento del carico per ERS, ad esempio **lb. QAS. ERS**)
1. In alternativa, se lo scenario richiede il servizio di bilanciamento del carico di base (interno), seguire questa procedura:  
   1. Creare gli indirizzi IP front-end
      1. Indirizzo IP 192.168.14.9 per ASC
         1. Aprire il servizio di bilanciamento del carico, selezionare Pool di indirizzi IP front-end e fare clic su Aggiungi
         1. Immettere il nome del nuovo pool di indirizzi IP front-end, ad esempio front- **end. QAS. ASC**)
         1. Impostare l'assegnazione su statico e immettere l'indirizzo IP (ad esempio **192.168.14.9**)
         1. Fare clic su OK.
      1. Indirizzo IP 192.168.14.10 per ASC ERS
         * Ripetere i passaggi precedenti in "a" per creare un indirizzo IP per ERS, ad esempio **192.168.14.10** e front- **end. QAS. ERS**)
   1. Creare i pool back-end
      1. Creare un pool back-end per ASCS
         1. Aprire il servizio di bilanciamento del carico, selezionare Pool back-end e fare clic su Aggiungi
         1. Immettere il nome del nuovo pool back-end, ad esempio **back-end. QAS**)
         1. Fare clic su Aggiungi una macchina virtuale.
         1. Selezionare il set di disponibilità creato in precedenza per ASC 
         1. Selezionare le macchine virtuali del cluster (A)SCS
         1. Fare clic su OK.
   1. Creare i probe di integrità
      1. Porta 620**00** per ASCS
         1. Aprire il servizio di bilanciamento del carico, selezionare Probe integrità e fare clic su Aggiungi
         1. Immettere il nome del nuovo probe di integrità, ad esempio **integrità. QAS. ASC**)
         1. Selezionare TCP come protocollo, la porta 620**00**, mantenere 5 per Intervallo e impostare Soglia di non integrità su 2
         1. Fare clic su OK.
      1. Porta 621**01** per ASC ERS
            * Ripetere i passaggi precedenti in "c" per creare un probe di integrità per ERS, ad esempio 621**01** e **Health. QAS. ERS**)
   1. Regole di bilanciamento del carico
      1. TCP 32**00** per ASCS
         1. Aprire il servizio di bilanciamento del carico, selezionare regole di bilanciamento del carico e fare clic su Aggiungi.
         1. Immettere il nome della nuova regola di bilanciamento del carico, ad esempio **lb. QAS. ASC. 3200**)
         1. Selezionare l'indirizzo IP front-end per ASC, il pool back-end e il probe di integrità creati in precedenza, ad esempio front- **end. QAS. ASC**)
         1. Mantenere il protocollo **TCP**, immettere la porta **3200**
         1. Aumentare il timeout di inattività a 30 minuti
         1. **Assicurarsi di abilitare l'indirizzo IP mobile**
         1. Fare clic su OK.
      1. Porte aggiuntive per ASCS
         * Ripetere i passaggi precedenti in "d" per le porte**36 00**,**39 00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 e TCP per ASC
      1. Porte aggiuntive per ASCS ERS
         * Ripetere i passaggi precedenti in "d" per le porte 32**01**, 33**01**, 5**01**13, 5**01**14, 5**01**16 e TCP per ASC ERS

> [!Note]
> Quando le macchine virtuali senza indirizzi IP pubblici vengono inserite nel pool back-end del servizio di bilanciamento del carico di Azure standard (nessun indirizzo IP pubblico), non vi sarà connettività Internet in uscita, a meno che non venga eseguita una configurazione aggiuntiva per consentire il routing a endpoint pubblici. Per informazioni dettagliate su come ottenere la connettività in uscita, vedere [connettività degli endpoint pubblici per le macchine virtuali con Azure Load Balancer standard negli scenari di disponibilità elevata di SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections).  

> [!IMPORTANT]
> Non abilitare i timestamp TCP nelle macchine virtuali di Azure che si trovano dietro Azure Load Balancer. Se si abilitano i timestamp TCP, i probe di integrità avranno esito negativo. Impostare il parametro **net. IPv4. tcp_timestamps** su **0**. Per informazioni dettagliate, vedere [Load Balancer Probe di integrità](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

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

1. **[1]** creare directory SAP nel volume Azure NetApp files.  
   Montare temporaneamente il volume Azure NetApp Files in una delle macchine virtuali e creare le directory SAP (percorsi di file).  

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
    ``` 

1. **[A]** Creare le directory condivise

   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/QAS/SYS
   sudo mkdir -p /usr/sap/QAS/ASCS00
   sudo mkdir -p /usr/sap/QAS/ERS01
   
   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/QAS/SYS
   sudo chattr +i /usr/sap/QAS/ASCS00
   sudo chattr +i /usr/sap/QAS/ERS01
   ```

1. **[A] installare il** client NFS e altri requisiti

   ```
   sudo yum -y install nfs-utils resource-agents resource-agents-sap
   ```

1. **[A]** Controllare la versione di resource-agents-sap

   Assicurarsi che la versione del pacchetto resource-agents-sap installato sia almeno 3.9.5-124.el7
   ```
   sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : 3.9.5
   # Release     : 124.el7
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   ```


1. **[A]** Aggiungere le voci di montaggio

   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   > [!NOTE]
   > Assicurarsi di associare la versione del protocollo NFS dei volumi di Azure NetApp Files, durante il montaggio dei volumi. In questo esempio i volumi di Azure NetApp Files sono stati creati come volumi NFSv3.  

   Montare le nuove condivisioni

   ```
   sudo mount -a  
   ```

1. **[A]**  Configurare il file SWAP

   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   Riavviare l'agente per attivare la modifica

   ```
   sudo service waagent restart
   ```

1. **[A]** Configurazione di RHEL

   Configurare RHEL come descritto nella nota SAP [2002167]

### <a name="installing-sap-netweaver-ascsers"></a>Installazione di SAP NetWeaver ASCS/ERS

1. **[1]**  Creare una risorsa IP virtuale e un probe di integrità per l'istanza di ASCS

   ```
   sudo pcs node standby anftstsapcl2
   
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' \
     --group g-QAS_ASCS
   
   sudo pcs resource create vip_QAS_ASCS IPaddr2 \
     ip=192.168.14.9 cidr_netmask=24 \
     --group g-QAS_ASCS
   
   sudo pcs resource create nc_QAS_ASCS azure-lb port=62000 \
     --group g-QAS_ASCS
   ```

   Assicurarsi che lo stato del cluster sia corretto e che tutte le risorse siano avviate. Non è importante il nodo su cui sono in esecuzione le risorse.

   ```
   sudo pcs status
   
   # Node anftstsapcl2: standby
   # Online: [ anftstsapcl1 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
   ```

1. **[1]** Installare SAP NetWeaver ASCS  

   Installare SAP NetWeaver ASC come root nel primo nodo usando un nome host virtuale mappato all'indirizzo IP della configurazione front-end del servizio di bilanciamento del carico per ASC, ad esempio <b>anftstsapvh</b>, <b>192.168.14.9</b> e il numero di istanza usato per il probe del servizio di bilanciamento del carico, ad esempio <b>00</b>.

   È possibile usare il parametro sapinst SAPINST_REMOTE_ACCESS_USER per consentire a un utente non ROOT di connettersi a sapinst.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Se l'installazione non riesce a creare una sottocartella in/usr/SAP/**QAS**/ASCS**00**, provare a impostare il proprietario e il gruppo della cartella ASC**00** e riprovare.

   ```
   sudo chown qasadm /usr/sap/QAS/ASCS00
   sudo chgrp sapsys /usr/sap/QAS/ASCS00
   ```

1. **[1]** Creare una risorsa IP virtuale e un probe di integrità per l'istanza di ERS

   ```
   sudo pcs node unstandby anftstsapcl2
   sudo pcs node standby anftstsapcl1
   
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' \
    --group g-QAS_AERS

   sudo pcs resource create vip_QAS_AERS IPaddr2 \
     ip=192.168.14.10 cidr_netmask=24 \
    --group g-QAS_AERS
   
   sudo pcs resource create nc_QAS_AERS azure-lb port=62101 \
    --group g-QAS_AERS
   ```
 
   Assicurarsi che lo stato del cluster sia corretto e che tutte le risorse siano avviate. Non è importante il nodo su cui sono in esecuzione le risorse.

   ```
   sudo pcs status
   
   # Node anftstsapcl1: standby
   # Online: [ anftstsapcl2 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2<
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   #  Resource Group: g-QAS_AERS
   #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
   #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   ```

1. **[2]** Installare SAP NetWeaver ERS  

   Installare SAP NetWeaver ERS come root nel secondo nodo usando un nome host virtuale mappato all'indirizzo IP della configurazione front-end del servizio di bilanciamento del carico per ERS, ad esempio <b>anftstsapers</b>, <b>192.168.14.10</b> e il numero di istanza usato per il probe del servizio di bilanciamento del carico, ad esempio <b>01</b>.

   È possibile usare il parametro sapinst SAPINST_REMOTE_ACCESS_USER per consentire a un utente non ROOT di connettersi a sapinst.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Se l'installazione non riesce a creare una sottocartella in/usr/SAP/**QAS**/ERS**01**, provare a impostare il proprietario e il gruppo della cartella ERS**01** e riprovare.

   ```
   sudo chown qaadm /usr/sap/QAS/ERS01
   sudo chgrp sapsys /usr/sap/QAS/ERS01
   ```

1. **[1]**  Adattare i profili di istanza ASCS/SCS e ERS

   * Profilo ASCS/SCS

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   ```

   * Profilo ERS

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ERS01_anftstsapers
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```


1. **[A]**  Configurare keep-alive

   Le comunicazioni tra il server applicazioni SAP NetWeaver e ASCS/SCS vengono instradate tramite un servizio di bilanciamento del carico software. Il servizio di bilanciamento del carico disconnette le connessioni inattive dopo un timeout configurabile. Per evitare questo problema, è necessario impostare un parametro nel profilo ASC/SCS di SAP NetWeaver e modificare le impostazioni di sistema di Linux. Per ulteriori informazioni, leggere la [Nota SAP 1410736][1410736] .

   Il parametro del profilo ASCS/SCS enque/encni/set_so_keepalive è già stato aggiunto nell'ultimo passaggio.

   ```
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   ```

1. **[A]** Aggiornare il file /usr/sap/sapservices

   Per impedire l'avvio delle istanze per lo script di avvio sapinit, tutte le istanze gestite da Pacemaker devono essere impostate come commento nel file /usr/sap/sapservices. Non impostare come commento l'istanza di SAP HANA se verrà usata con HANA SR.

   ```
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ASCS00/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ASCS00/exe/sapstartsrv pf=/usr/sap/QAS/SYS/profile/QAS_ASCS00_anftstsapvh -D -u qasadm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ERS01/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ERS01/exe/sapstartsrv pf=/usr/sap/QAS/ERS01/profile/QAS_ERS01_anftstsapers -D -u qasadm
   ```

1. **[1]** Creare le risorse del cluster SAP

   Se si usa l'architettura di Accodamento server 1 (ENSA1), definire le risorse nel modo seguente:

   ```
   sudo pcs property set maintenance-mode=true
   
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint location rsc_sap_QAS_ASCS00 rule score=2000 runs_ers_QAS eq 1
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   SAP ha introdotto il supporto per l'accodamento del server 2, inclusa la replica, a partire da SAP NW 7,52. A partire dalla piattaforma ABAP 1809, il server di Accodamento 2 viene installato per impostazione predefinita. Vedere la nota SAP [2630416](https://launchpad.support.sap.com/#/notes/2630416) per il supporto del server di Accodamento 2.
   Se si usa l'architettura di Accodamento server 2 ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), installare l'agente risorse Resource-Agents-SAP-4.1.1 -12. el7. x86_64 o versione successiva e definire le risorse come segue:

    ```
    sudo pcs property set maintenance-mode=true
    
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
   
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   Se si esegue l'aggiornamento da una versione precedente e si passa al server di Accodamento 2, vedere la nota SAP [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   Assicurarsi che lo stato del cluster sia corretto e che tutte le risorse siano avviate. Non è importante il nodo su cui sono in esecuzione le risorse.

    ```
    sudo pcs status
    
    # Online: [ anftstsapcl1 anftstsapcl2 ]
    #
    # Full list of resources:
    #
    # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
    #  Resource Group: g-QAS_ASCS
    #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
    #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
    #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
    #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    #  Resource Group: g-QAS_AERS
    #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
    #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
    #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
    #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. **[A]** aggiungere regole del firewall per ASC e ERS in entrambi i nodi aggiungere le regole del firewall per ASC e ERS in entrambi i nodi.
   ```
   # Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=62000/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62000/tcp
   sudo firewall-cmd --zone=public --add-port=3200/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3200/tcp
   sudo firewall-cmd --zone=public --add-port=3600/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3600/tcp
   sudo firewall-cmd --zone=public --add-port=3900/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3900/tcp
   sudo firewall-cmd --zone=public --add-port=8100/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=8100/tcp
   sudo firewall-cmd --zone=public --add-port=50013/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50013/tcp
   sudo firewall-cmd --zone=public --add-port=50014/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50014/tcp
   sudo firewall-cmd --zone=public --add-port=50016/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50016/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=62101/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62101/tcp
   sudo firewall-cmd --zone=public --add-port=3301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3301/tcp
   sudo firewall-cmd --zone=public --add-port=50113/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50113/tcp
   sudo firewall-cmd --zone=public --add-port=50114/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50114/tcp
   sudo firewall-cmd --zone=public --add-port=50116/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50116/tcp
   ```

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Preparazione del server applicazioni di SAP NetWeaver

   Alcuni database richiedono che l'installazione dell'istanza di database venga eseguita in un server applicazioni. Preparare le macchine virtuali del server applicazioni da poter usare in questi casi.  

   I passaggi seguenti presuppongono che il server applicazioni venga installato in un server diverso dai server ASCS/SCS e HANA. Diversamente, alcuni dei passaggi seguenti non sono necessari, ad esempio la configurazione della risoluzione dei nomi host.  

   Gli elementi seguenti sono preceduti da **[a]** -applicabile sia a pas sia a AAS, **[P]** -applicabile solo a pas o **[S]** -applicabile solo a AAS.  

1. **[A]** configurare la risoluzione dei nomi host è possibile usare un server DNS o modificare i/o/o in tutti i nodi. Questo esempio mostra come usare il file /etc/hosts.
   Sostituire l'indirizzo IP e il nome host nei comandi seguenti:  

   ```
   sudo vi /etc/hosts
   ```

   Inserire le righe seguenti in /etc/hosts. Modificare l'indirizzo IP e il nome host in modo che corrispondano all'ambiente.

   ```
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   192.168.14.9 anftstsapvh
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   192.168.14.10 anftstsapers
   192.168.14.7 anftstsapa01
   192.168.14.8 anftstsapa02
   ```

1. **[A]** creare la directory sapmnt creare la directory sapmnt.
   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   ```

1. **[A] installare il** client NFS e altri requisiti  

   ```
   sudo yum -y install nfs-utils uuidd
   ```

1. **[A]** Aggiungere le voci di montaggio  

   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Montare le nuove condivisioni

   ```
   sudo mount -a
   ```

1. **[P]** creare e montare la directory pas  

   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

1. **[S]** creare e montare la directory AAS  

   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```


1. **[A]**  Configurare il file SWAP
 
   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   Riavviare l'agente per attivare la modifica

   ```
   sudo service waagent restart
   ```

## <a name="install-database"></a>Installare il database

In questo esempio, SAP NetWeaver è installato in SAP HANA. Per questa installazione è possibile usare qualsiasi database supportato. Per altre informazioni su come installare SAP HANA in Azure, vedere [disponibilità elevata di SAP Hana in macchine virtuali di Azure in Red Hat Enterprise Linux][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533].

1. Eseguire l'installazione dell'istanza del database SAP

   Installare l'istanza del database di SAP NetWeaver come root usando un nome host virtuale mappato all'indirizzo IP della configurazione front-end del servizio di bilanciamento del carico per il database.

   È possibile usare il parametro sapinst SAPINST_REMOTE_ACCESS_USER per consentire a un utente non ROOT di connettersi a sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## <a name="sap-netweaver-application-server-installation"></a>Installazione del server applicazioni di SAP NetWeaver

Per installare il server applicazioni SAP, seguire questi passaggi.

1. Preparare il server applicazioni

   Per preparare il server applicazioni, seguire la procedura descritta nel capitolo [Preparazione del server applicazioni di SAP NetWeaver](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) sopra riportato.

1. Installare il server applicazioni di SAP NetWeaver

   Installare un server applicazioni di SAP NetWeaver primario o aggiuntivo.

   È possibile usare il parametro sapinst SAPINST_REMOTE_ACCESS_USER per consentire a un utente non ROOT di connettersi a sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

1. Aggiornare l'archivio sicuro di SAP HANA

   Aggiornare l'archivio sicuro di SAP HANA in modo che punti al nome virtuale della configurazione della replica di sistema di SAP HANA.

   Eseguire il comando seguente per visualizzare le voci come \< sapsid>adm

   ```
   hdbuserstore List
   ```

   Viene visualizzato un elenco di tutte le voci, che avrà un aspetto simile al seguente
   ```
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 192.168.14.4:30313
     USER: SAPABAP1
     DATABASE: QAS
   ```

   L'output mostra che l'indirizzo IP della voce predefinita fa riferimento alla macchina virtuale e non all'indirizzo IP del bilanciamento del carico. Questa voce deve essere modificata in modo che faccia riferimento al nome host virtuale del bilanciamento del carico. Assicurarsi di usare la stessa porta (**30313** nell'output precedente) e il nome del database (**QAS** nell'output precedente).

   ```
   su - qasadm
   hdbuserstore SET DEFAULT qasdb:30313@QAS SAPABAP1 <password of ABAP schema>
   ```

## <a name="test-the-cluster-setup"></a>Testare la configurazione del cluster

1. Eseguire la migrazione manuale dell'istanza ASCS

   Stato delle risorse prima dell'avvio del test:

   ```
    rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Eseguire i comandi seguenti come radice per la migrazione dell'istanza ASCS.

   ```
   [root@anftstsapcl1 ~]# pcs resource move rsc_sap_QAS_ASCS00
   
   [root@anftstsapcl1 ~]# pcs resource clear rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Stato delle risorse dopo il test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Simulare l'arresto anomalo del nodo

   Stato delle risorse prima dell'avvio del test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   Eseguire i comandi seguenti come radice nel nodo di cui è in esecuzione l'istanza di ASCS

   ```
   [root@anftstsapcl2 ~]# echo b > /proc/sysrq-trigger
   ```

   Lo stato dopo il riavvio del nodo dovrebbe essere simile al seguente.

   ```
   Online: [ anftstsapcl1 anftstsapcl2 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   
   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=45, status=complete, exitreason='',
   ```

   Eseguire il comando seguente per pulire le risorse con errori.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Stato delle risorse dopo il test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Terminare il processo server messaggi

   Stato delle risorse prima dell'avvio del test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```
   
   Eseguire i comandi seguenti come radice per identificare il processo del server messaggi e terminarlo.

   ```
   [root@anftstsapcl1 ~]# pgrep ms.sapQAS | xargs kill -9
   ```

   Se si uccide il server di messaggi una sola volta, questo verrà riavviato da `sapstart`. Terminandolo un numero di volte sufficiente, Pacemaker sposterà infine l'istanza ASCS in un altro nodo. Eseguire i comandi seguenti come radice per pulire lo stato della risorsa dell'istanza ASCS ed ERS dopo il test.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Stato delle risorse dopo il test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Terminare il processo server di accodamento

   Stato delle risorse prima dell'avvio del test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   Eseguire i comandi seguenti come radice nel nodo di cui è in esecuzione l'istanza di ASCS per terminare il server di accodamento.

   ```
   [root@anftstsapcl2 ~]# pgrep en.sapQAS | xargs kill -9
   ```

   L'istanza ASCS deve eseguire immediatamente il failover in un altro nodo. Anche l'istanza ERS deve eseguire il failover dopo l'avvio dell'istanza ASCS. Eseguire i comandi seguenti come radice per pulire lo stato della risorsa dell'istanza ASCS ed ERS dopo il test.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Stato delle risorse dopo il test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Terminare il processo del server di replica dell'accodamento

   Stato delle risorse prima dell'avvio del test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Eseguire i comandi seguenti come radice nel nodo in cui è in esecuzione l'istanza ERS per terminare il processo del server di replica dell'accodamento.

   ```
   [root@anftstsapcl2 ~]# pgrep er.sapQAS | xargs kill -9
   ```

   Se il comando viene eseguito solo una volta, `sapstart` riavvierà il processo. Se viene eseguito abbastanza spesso, `sapstart` non riavvierà il processo e lo stato della risorsa sarà interrotto. Eseguire i comandi seguenti come radice per pulire lo stato della risorsa dell'istanza ERS dopo il test.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Stato delle risorse dopo il test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Terminare il processo di accodamento sapstartsrv

   Stato delle risorse prima dell'avvio del test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Eseguire i comandi seguenti come radice nel nodo di cui è in esecuzione ASCS.

   ```
   [root@anftstsapcl1 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@anftstsapcl1 ~]# kill -9 59545
   ```

   Il processo sapstartsrv deve sempre essere riavviato dall'agente delle risorse Pacemaker come parte del monitoraggio. Stato delle risorse dopo il test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

## <a name="next-steps"></a>Passaggi successivi

* [Pianificazione e implementazione di macchine virtuali di Azure per SAP][planning-guide]
* [Distribuzione di macchine virtuali di Azure per SAP][deployment-guide]
* [Distribuzione DBMS di macchine virtuali di Azure per SAP][dbms-guide]
* Per informazioni su come stabilire la disponibilità elevata e un piano di ripristino di emergenza di SAP HANA in Azure (istanze di grandi dimensioni), vedere [Disponibilità elevata e ripristino di emergenza di SAP HANA (istanze di grandi dimensioni) in Azure](hana-overview-high-availability-disaster-recovery.md).
* Per informazioni su come stabilire la disponibilità elevata e pianificare il ripristino di emergenza di SAP HANA nelle VM di Azure, vedere [disponibilità elevata di SAP Hana in macchine virtuali di Azure (VM)][sap-hana-ha]
