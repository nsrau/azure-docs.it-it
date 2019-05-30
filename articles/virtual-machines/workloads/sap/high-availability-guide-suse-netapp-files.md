---
title: Disponibilità elevata di Azure le macchine virtuali per SAP NetWeaver su SUSE Linux Enterprise Server con file di Azure NetApp | Microsoft Docs
description: Guida alla disponibilità elevata per SAP NetWeaver su SUSE Linux Enterprise Server con file NetApp di Azure per le applicazioni SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/30/2019
ms.author: radeltch
ms.openlocfilehash: b3b5a89b43eaa5c0851962aef414ec9c9b7440da
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357724"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-with-azure-netapp-files-for-sap-applications"></a>Disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure su SUSE Linux Enterprise Server con file NetApp di Azure per le applicazioni SAP

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

Questo articolo descrive come distribuire le macchine virtuali, configurare le macchine virtuali, installare il framework del cluster e installare un sistema SAP NetWeaver 7.50 a disponibilità elevata, usando [file di Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/).
Le configurazioni di esempio, i comandi di installazione e così via, l'istanza ASCS è numero 00, il numero di istanza ERS 01, l'istanza Primary Application (PAS) è 02 e l'istanza di applicazione (AAS) è 03. SAP System ID QAS viene usato. 

Questo articolo illustra come ottenere la disponibilità elevata per applicazioni SAP NetWeaver con Azure NetApp file. Il livello di database non viene trattato in dettaglio in questo articolo.

Leggere prima di tutto i documenti e le note SAP seguenti:

* [Documentazione di Azure i file di NetApp][anf-azure-doc] 
* Nota SAP [1928533], contenente:  
  * Elenco delle dimensioni delle VM di Azure supportate per la distribuzione di software SAP
  * Importanti informazioni sulla capacità per le dimensioni delle VM di Azure
  * Software SAP e combinazioni di sistemi operativi e database supportati
  * Versione del kernel SAP richiesta per Windows e Linux in Microsoft Azure
* La nota SAP [2015553] elenca i prerequisiti per le distribuzioni di software SAP supportate da SAP in Azure.
* La nota SAP [2205917] contiene le impostazioni consigliate del sistema operativo per SUSE Linux Enterprise Server for SAP Applications
* La nota SAP [1944799] contiene linee guida per SAP HANA per SUSE Linux Enterprise Server for SAP Applications
* La nota SAP [2178632] contiene informazioni dettagliate su tutte le metriche di monitoraggio segnalate per SAP in Azure.
* La nota SAP [2191498] contiene la versione dell'agente host SAP per Linux necessaria in Azure.
* La nota SAP [2243692] contiene informazioni sulle licenze SAP in Linux in Azure.
* La nota SAP [1984787] contiene informazioni generali su SUSE Linux Enterprise Server 12.
* La nota SAP [1999351] contiene informazioni aggiuntive sulla risoluzione dei problemi per l'estensione di monitoraggio avanzato di Azure per SAP.
* [Community WIKI SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) contiene tutte le note su SAP necessarie per Linux.
* [Pianificazione e implementazione di Macchine virtuali di Azure per SAP in Linux][planning-guide]
* [Distribuzione di Macchine virtuali di Azure per SAP in Linux][deployment-guide]
* [Distribuzione DBMS di Macchine virtuali di Azure per SAP in Linux][dbms-guide]
* [SUSE SAP HA Best Practice Guides][suse-ha-guide] (Guide relative alle procedure consigliate di SUSE SAP HA) Le guide contengono tutte le informazioni necessarie per configurare la replica di sistema Netweaver HA e SAP HANA in locale. Usare le guide come indicazioni di base generali. Forniscono informazioni molto più dettagliate.
* [Note sulla versione di SUSE High Availability Extension 12 SP3][suse-ha-12sp3-relnotes]
* [NetApp applicazioni SAP in Microsoft Azure usando i file di Azure NetApp][anf-sap-applications-azure]

## <a name="overview"></a>Panoramica

Availability(HA) elevata per i servizi centrali SAP Netweaver richiede l'archiviazione condivisa.
A tale scopo in SUSE Linux finora è stato necessario creare cluster NFS a disponibilità elevata distinto. 

A questo punto è possibile ottenere SAP Netweaver a disponibilità elevata con archiviazione condivisa, distribuita in Azure i file di NetApp. Usare file di NetApp di Azure per l'archiviazione condivisa Elimina la necessità di ulteriori [cluster NFS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs). Pacemaker è ancora necessario per la disponibilità elevata di services(ASCS/SCS) centrale di SAP Netweaver.


![Panoramica della disponibilità elevata di SAP NetWeaver](./media/high-availability-guide-suse-anf/high-availability-guide-suse-anf.PNG)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS e il database SAP HANA usano un nome host virtuale e indirizzi IP virtuali. In Azure, un [bilanciamento del carico](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) deve usare un indirizzo IP virtuale. L'elenco seguente mostra la configurazione del bilanciamento del carico di (A)SCS ed ERS.

> [!IMPORTANT]
> Clustering di multi-SID di SAP ASCS/ERS con SUSE Linux come sistema operativo guest nelle macchine virtuali di Azure viene **non è supportato**. Multi-SID di clustering vengono descritti l'installazione di più istanze di SAP ASCS/ERS con SID diverso in un cluster Pacemaker


### <a name="ascs"></a>(A)SCS

* Configurazione front-end
  * Indirizzo IP 10.1.1.20
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
  * Indirizzo IP 10.1.1.21
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

5. Distribuire i volumi di file di Azure NetApp, seguendo la [istruzioni per creare un volume per i file di Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes). Distribuire i volumi nei file di NetApp Azure designata [subnet](https://docs.microsoft.com/rest/api/virtualnetwork/subnets). Tenere presente che le risorse di Azure i file di NetApp e macchine virtuali di Azure devono trovarsi nella stessa rete virtuale di Azure o in reti virtuali di Azure con peering. Ad esempio sapmnt<b>QAS</b>, usrsap<b>QAS</b>e così via sono i nomi dei volumi e sapmnt<b>qas</b>, usrsap<b>qas</b>e così via sono il filepaths per Azure Volumi di file NetApp.  

   1. volume sapmnt<b>QAS</b> (nfs://10.1.0.4/sapmnt<b>qas</b>)
   2. volume usrsap<b>QAS</b> (nfs://10.1.0.4/usrsap<b>qas</b>)
   3. volume usrsap<b>QAS</b>sys (nfs://10.1.0.5/usrsap<b>qas</b>sys)
   4. volume usrsap<b>QAS</b>ers (nfs://10.1.0.4/usrsap<b>qas</b>ers)
   5. volume trans (nfs://10.1.0.4/trans)
   6. volume usrsap<b>QAS</b>pas (nfs://10.1.0.5/usrsap<b>qas</b>agli indirizzi PA)
   7. volume usrsap<b>QAS</b>aas (nfs://10.1.0.4/usrsap<b>qas</b>aas)
   
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

## <a name="deploy-linux-vms-manually-via-azure-portal"></a>Distribuire macchine virtuali Linux manualmente tramite il portale di Azure

Prima di tutto è necessario creare i volumi di file di Azure NetApp. Distribuire le macchine virtuali. Successivamente, creare un servizio di bilanciamento del carico e usare le macchine virtuali nei pool back-end.

1. Creare un gruppo di risorse
1. Creare una rete virtuale
1. Creare un Set di disponibilità per ASCS  
   Impostare il numero massimo di domini di aggiornamento
1. Creare la macchina virtuale 1  
   Usare almeno SLES4SAP 12 SP3, in questo esempio viene usata l'immagine SLES4SAP 12 SP3  
   Seleziona Set di disponibilità creato in precedenza per ASCS  
1. Creare la macchina virtuale 2  
   Usare almeno SLES4SAP 12 SP3, in questo esempio viene usata l'immagine SLES4SAP 12 SP3  
   Seleziona Set di disponibilità creato in precedenza per ASCS  
1. Creare un Set di disponibilità per le istanze dell'applicazione SAP (PA, AAS)    
   Impostare il numero massimo di domini di aggiornamento
1. Creare la macchina virtuale 3  
   Usare almeno SLES4SAP 12 SP3, in questo esempio viene usata l'immagine SLES4SAP 12 SP3  
   Seleziona Set di disponibilità creato in precedenza per PAS/AAS   
1. Creare la macchina virtuale 4  
   Usare almeno SLES4SAP 12 SP3, in questo esempio viene usata l'immagine SLES4SAP 12 SP3  
   Seleziona Set di disponibilità creato in precedenza per PAS/AAS  

## <a name="setting-up-ascs"></a>Configurazione di (A)SCS

In questo esempio, le risorse sono state distribuite manualmente tramite il [portale di Azure](https://portal.azure.com/#home) .

### <a name="deploy-azure-load-balancer-manually-via-azure-portal"></a>Distribuire manualmente il servizio di bilanciamento del carico di Azure tramite il portale di Azure

Prima di tutto è necessario creare i volumi di file di Azure NetApp. Distribuire le macchine virtuali. Successivamente, creare un servizio di bilanciamento del carico e usare le macchine virtuali nei pool back-end.

1. Creare un servizio di bilanciamento del carico (interno)  
   1. Creare gli indirizzi IP front-end
      1. Indirizzo IP 10.1.1.20 per ASCS
         1. Aprire il servizio di bilanciamento del carico, selezionare Pool di indirizzi IP front-end e fare clic su Aggiungi
         1. Immettere il nome del nuovo pool di IP front-end (ad esempio **front-end. QAS. ASCS**)
         1. Impostare assegnazione su statico e immettere l'indirizzo IP (ad esempio **10.1.1.20**)
         1. Fare clic su OK.
      1. Indirizzo IP 10.1.1.21 per ASCS ERS
         * Ripetere i passaggi precedenti in "a" per creare un indirizzo IP per ERS (ad esempio **10.1.1.21** e **front-end. QAS. ERS**)
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
         * Ripetere i passaggi descritti in precedenza in "d" per le porte 33**01**, 5**01**13, 5**01**14, 5**01**16 e TCP per ASCS ERS

> [!IMPORTANT]
> Non abilitare TCP timestamp sulle macchine virtuali di Azure posizionato dietro bilanciamento del carico di Azure. Abilitazione di TCP timestamp causerà i probe di integrità errore. Impostare il parametro **net.ipv4.tcp_timestamps** al **0**. Per informazioni dettagliate, vedere [probe di integrità di Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Creare un cluster Pacemaker

Seguire i passaggi descritti in [Configurazione di Pacemaker su SUSE Linux Enterprise Server in Azure](high-availability-guide-suse-pacemaker.md) per creare un cluster Pacemaker di base per questo server (A)SCS.

### <a name="installation"></a>Installazione

Gli elementi seguenti sono preceduti dall'indicazione **[A]** - applicabile a tutti i nodi, **[1]** - applicabile solo al nodo 1 o **[2]** - applicabile solo al nodo 2.

1. **[A]**  Installare il connettore SUSE

   <pre><code>sudo zypper install sap-suse-cluster-connector
   </code></pre>

   > [!NOTE]
   > Non usare trattini nei nomi host dei nodi del cluster in uso. In caso contrario, il cluster non funzionerà. Si tratta di una limitazione nota e SUSE sta lavorando a una correzione. La correzione verrà rilasciata come patch del pacchetto sap-suse-cloud-connector.

   Verificare di aver installato la nuova versione del connettore del cluster SAP SUSE. La versione precedente era denominata sap_suse_cluster_connector mentre la nuova è denominata **sap-suse-cluster-connector**.

   <pre><code>sudo zypper info sap-suse-cluster-connector
   
      Information for package sap-suse-cluster-connector:
   ---------------------------------------------------
   Repository     : SLE-12-SP3-SAP-Updates
   Name           : sap-suse-cluster-connector
   Version        : 3.1.0-8.1
   Arch           : noarch
   Vendor         : SUSE LLC &lt;https://www.suse.com/&gt;
   Support Level  : Level 3
   Installed Size : 45.6 KiB
   Installed      : Yes
   Status         : up-to-date
   Source package : sap-suse-cluster-connector-3.1.0-8.1.src
   Summary        : SUSE High Availability Setup for SAP Products
   </code></pre>

2. **[A]**  Aggiornare gli agenti delle risorse SAP  
   
   È necessaria una patch per il pacchetto degli agenti delle risorse per usare la nuova configurazione descritta in questo articolo. È possibile verificare se la patch è già installata con il comando seguente

   <pre><code>sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   L'output dovrebbe essere simile al seguente

   <pre><code>&lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   Se il comando grep non trova il parametro IS_ERS, è necessario installare la patch indicata nella [pagina di download di SUSE](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents)

   <pre><code># example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

3. **[A]** Configurare la risoluzione dei nomi host

   È possibile usare un server DNS o modificare /etc/hosts in tutti i nodi. Questo esempio mostra come usare il file /etc/hosts.
   Sostituire l'indirizzo IP e il nome host nei comandi seguenti

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Inserire le righe seguenti in /etc/hosts. Modificare l'indirizzo IP e il nome host in base all'ambiente   

   <pre><code>
   # IP address of cluster node 1
   <b>10.1.1.18    anftstsapcl1</b>
   # IP address of cluster node 2
   <b>10.1.1.6     anftstsapcl2</b>
   # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
   <b>10.1.1.20    anftstsapvh</b>
   # IP address of the load balancer frontend configuration for SAP Netweaver ERS
   <b>10.1.1.21    anftstsapers</b>
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>Prepararsi per l'installazione di SAP NetWeaver

1. **[A]** Creare le directory condivise

   <pre><code>sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>QAS</b>/SYS
   sudo mkdir -p /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>QAS</b>/ERS<b>01</b>
   
   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>QAS</b>/SYS
   sudo chattr +i /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>

2. **[A]** Configurare autofs

   <pre><code>
   sudo vi /etc/auto.master
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Creare un file con

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync 10.1.0.4:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync 10.1.0.4:/trans
   /usr/sap/<b>QAS</b>/SYS -nfsvers=3,nobind,sync 10.1.0.5:/usrsap<b>qas</b>sys
   </code></pre>
   
   > [!NOTE]
   > File di NetApp Azure supporta attualmente solo NFSv3. Non omettere le nfsvers = opzione 3.
   
   Riavviare `autofs` montare le nuove condivisioni
    <pre><code>
      sudo systemctl enable autofs
      sudo service autofs restart
     </code></pre>

3. **[A]**  Configurare il file SWAP

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Riavviare l'agente per attivare la modifica

   <pre><code>sudo service waagent restart
   </code></pre>


### <a name="installing-sap-netweaver-ascsers"></a>Installazione di SAP NetWeaver ASCS/ERS

1. **[1]**  Creare una risorsa IP virtuale e un probe di integrità per l'istanza di ASCS

   <pre><code>sudo crm node standby <b>anftstsapcl2</b>
   
   sudo crm configure primitive fs_<b>QAS</b>_ASCS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>' directory='/usr/sap/<b>QAS</b>/ASCS<b>00</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ASCS IPaddr2 \
     params ip=<b>10.1.1.20</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ASCS anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 620<b>00</b>" \
     op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>QAS</b>_ASCS fs_<b>QAS</b>_ASCS nc_<b>QAS</b>_ASCS vip_<b>QAS</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   Assicurarsi che lo stato del cluster sia corretto e che tutte le risorse siano avviate. Non è importante il nodo su cui sono in esecuzione le risorse.

   <pre><code>sudo crm_mon -r
   
   # Node anftstsapcl2: standby
   # <b>Online: [ anftstsapcl1 ]</b>
   # 
   # Full list of resources:
   #
   # Resource Group: g-QAS_ASCS
   #     fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #     nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl1</b>
   #     vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   #     rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl1</b>
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   </code></pre>
  
2. **[1]** Installare SAP NetWeaver ASCS  

   Installare SAP NetWeaver ASCS come root nel primo nodo usando un nome host virtuale mappato all'indirizzo IP della configurazione front-end di bilanciamento del carico per l'istanza ASCS, ad esempio <b>anftstsapvh</b>, <b>10.1.1.20</b> e il numero di istanza usato per il probe del bilanciamento del carico, ad esempio <b>00</b>.

   È possibile usare il parametro sapinst SAPINST_REMOTE_ACCESS_USER per consentire a un utente non ROOT di connettersi a sapinst. È possibile usare parametri SAPINST_USE_HOSTNAME a installare SAP, usando nome host virtuale.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   Se l'installazione non riesce a creare una sottocartella in /usr/SAP/**QAS**/ASCS**00**, provare a impostare il proprietario e il gruppo di ASCS**00** cartella e riprovare. 

   <pre><code>
   chown <b>qas</b>adm /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   </code></pre>

3. **[1]** Creare una risorsa IP virtuale e un probe di integrità per l'istanza di ERS

   <pre><code>
   sudo crm node online <b>anftstsapcl2</b>
   sudo crm node standby <b>anftstsapcl1</b>
   
   sudo crm configure primitive fs_<b>QAS</b>_ERS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>ers' directory='/usr/sap/<b>QAS</b>/ERS<b>01</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ERS IPaddr2 \
     params ip=<b>10.1.1.21</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ERS anything \
    params binfile="/usr/bin/nc" cmdline_options="-l -k 621<b>01</b>" \
    op monitor timeout=20s interval=10 depth=0
   
   # WARNING: Resources nc_QAS_ASCS,nc_QAS_ERS violate uniqueness for parameter "binfile": "/usr/bin/nc"
   # Do you still want to commit (y/n)? y
   
   sudo crm configure group g-<b>QAS</b>_ERS fs_<b>QAS</b>_ERS nc_<b>QAS</b>_ERS vip_<b>QAS</b>_ERS
   </code></pre>

   Assicurarsi che lo stato del cluster sia corretto e che tutte le risorse siano avviate. Non è importante il nodo su cui sono in esecuzione le risorse.

   <pre><code>sudo crm_mon -r
   
   # Node <b>anftstsapcl1: standby</b>
   # <b>Online: [ anftstsapcl2 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS  (ocf::heartbeat:IPaddr2):     <b>Started anftstsapcl2</b>
   </code></pre>

4. **[2]** Installare SAP NetWeaver ERS

   Installare SAP NetWeaver ERS come root nel secondo nodo usando un nome host virtuale mappato all'indirizzo IP della configurazione front-end di bilanciamento del carico per ERS, ad esempio <b>anftstsapers</b>, <b>10.1.1.21</b> e il numero di istanza usato per il probe del bilanciamento del carico, ad esempio <b>01</b>.

   È possibile usare il parametro sapinst SAPINST_REMOTE_ACCESS_USER per consentire a un utente non ROOT di connettersi a sapinst. È possibile usare parametri SAPINST_USE_HOSTNAME a installare SAP, usando nome host virtuale.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   > [!NOTE]
   > Usare SWPM SP 20 PL 05 o versione successiva. Le versioni precedenti non impostano correttamente le autorizzazioni e l'installazione avrà esito negativo.

   Se l'installazione non riesce a creare una sottocartella in /usr/SAP/**QAS**/ERS**01**, provare a impostare il proprietario e il gruppo di ERS**01** cartella e riprovare.

   <pre><code>
   chown qasadm /usr/sap/<b>QAS</b>/ERS<b>01</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>


5. **[1]**  Adattare i profili di istanza ASCS/SCS e ERS
 
   * Profilo ASCS/SCS

   <pre><code>
   sudo vi /sapmnt/<b>QAS</b>/profile/<b>QAS</b>_<b>ASCS00</b>_<b>anftstsapvh</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * Profilo ERS

   <pre><code>
   sudo vi /sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>

6. **[A]**  Configurare keep-alive

   Le comunicazioni tra il server applicazioni SAP NetWeaver e ASCS/SCS vengono instradate tramite un servizio di bilanciamento del carico software. Il servizio di bilanciamento del carico disconnette le connessioni inattive dopo un timeout configurabile. Per evitare questo comportamento, è necessario impostare un parametro nel profilo ASCS/SCS di SAP NetWeaver e modificare le impostazioni di sistema di Linux. Per altre informazioni, leggere la [nota SAP 1410736][1410736].

   Il parametro del profilo ASCS/SCS enque/encni/set_so_keepalive è già stato aggiunto nell'ultimo passaggio.

   <pre><code>
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

7. **[A]** Configurare gli utenti SAP dopo l'installazione

   <pre><code>
   # Add sidadm to the haclient group
   sudo usermod -aG haclient <b>qas</b>adm
   </code></pre>

8. **[1]**  Aggiungere i servizi SAP ASCS e ERS al file sapservice

   Aggiungere la voce del servizio ASCS al secondo nodo e copiare la voce del servizio ERS nel primo nodo.

   <pre><code>
   cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>anftstsapcl2</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>anftstsapcl2</b> "cat /usr/sap/sapservices" | grep ERS<b>01</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

9. **[1]** Creare le risorse del cluster SAP

Se si usa l'architettura del server 1 enqueue (ENSA1), definire le risorse come segue:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ERS<b>01</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ERS<b>01</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000
   
   sudo crm configure modgroup g-<b>QAS</b>_ASCS add rsc_sap_<b>QAS</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>QAS</b>_ERS add rsc_sap_<b>QAS</b>_ERS<b>01</b>
   
   sudo crm configure colocation col_sap_<b>QAS</b>_no_both -5000: g-<b>QAS</b>_ERS g-<b>QAS</b>_ASCS
   sudo crm configure location loc_sap_<b>QAS</b>_failover_to_ers rsc_sap_<b>QAS</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>QAS</b> eq 1
   sudo crm configure order ord_sap_<b>QAS</b>_first_start_ascs Optional: rsc_sap_<b>QAS</b>_ASCS<b>00</b>:start rsc_sap_<b>QAS</b>_ERS<b>01</b>:stop symmetrical=false
   
   sudo crm node online <b>anftstsapcl1</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   SAP introdotto il supporto per il server di Accodamento 2, che include replica, a partire da SAP NW 7.52. A partire da ABAP piattaforma 1809, 2 server di accodamento è installato per impostazione predefinita. Vedere SAP nota [2630416](https://launchpad.support.sap.com/#/notes/2630416) per il supporto di server 2 enqueue.
Se si usa l'architettura del server 2 enqueue ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), definire le risorse come segue:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ERS<b>01</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ERS<b>01</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>" AUTOMATIC_RECOVER=false IS_ERS=true
   
   sudo crm configure modgroup g-<b>QAS</b>_ASCS add rsc_sap_<b>QAS</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>QAS</b>_ERS add rsc_sap_<b>QAS</b>_ERS<b>01</b>
   
   sudo crm configure colocation col_sap_<b>QAS</b>_no_both -5000: g-<b>QAS</b>_ERS g-<b>QAS</b>_ASCS
   sudo crm configure order ord_sap_<b>QAS</b>_first_start_ascs Optional: rsc_sap_<b>QAS</b>_ASCS<b>00</b>:start rsc_sap_<b>QAS</b>_ERS<b>01</b>:stop symmetrical=false
   
   sudo crm node online <b>anftstsapcl1</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   Se si esegue l'aggiornamento da una versione precedente e passare a server di Accodamento 2, vedere SAP nota [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   Assicurarsi che lo stato del cluster sia corretto e che tutte le risorse siano avviate. Non è importante il nodo su cui sono in esecuzione le risorse.

   <pre><code>sudo crm_mon -r
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl1</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl1</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl2</b>
   </code></pre>

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Preparazione del server applicazioni di SAP NetWeaver 

Alcuni database richiedono che l'installazione dell'istanza di database venga eseguita in un server applicazioni. Preparare le macchine virtuali del server applicazioni da poter usare in questi casi.

I passaggi seguenti presuppongono che il server applicazioni venga installato in un server diverso dai server ASCS/SCS e HANA. Diversamente, alcuni dei passaggi seguenti non sono necessari, ad esempio la configurazione della risoluzione dei nomi host.

Gli elementi seguenti sono preceduti **[A]** - applicabile sia agli indirizzi PA, AAS **[P]** - applicabile solo agli indirizzi PA oppure **[S]** - applicabile solo al AAS.


1. **[A]**  Configurare il sistema operativo

   Ridurre le dimensioni della cache dirty. Per altre informazioni, vedere [Prestazioni di scrittura ridotte sui server SLES 11 12 con RAM di grandi dimensioni](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>
   sudo vi /etc/sysctl.conf
   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]** Configurare la risoluzione dei nomi host

   È possibile usare un server DNS o modificare /etc/hosts in tutti i nodi. Questo esempio mostra come usare il file /etc/hosts.
   Sostituire l'indirizzo IP e il nome host nei comandi seguenti

   ```bash
   sudo vi /etc/hosts
   ```

   Inserire le righe seguenti in /etc/hosts. Modificare l'indirizzo IP e il nome host in base all'ambiente

   <pre><code>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.1.1.20 anftstsapvh</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.1.1.21 anftstsapers</b>
   # IP address of all application servers
   <b>10.1.1.15 anftstsapa01</b>
   <b>10.1.1.16 anftstsapa02</b>
   </code></pre>

1. **[A]**  Creare la directory sapmnt

   <pre><code>
   sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. **[P]**  Creare la directory PAS

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>02</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>02</b>
   </code></pre>

1. **[S]**  Creare la directory AAS

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>03</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>03</b>
   </code></pre>

1. **[P]**  Configurare autofs su PAS

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Creare un nuovo file con

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>02</b> -nfsvers=3,nobind,sync <b>10.1.0.5</b>:/ursap<b>qas</b>pas
   </code></pre>

   Riavviare `autofs` montare le nuove condivisioni

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[P]**  Configurare autofs su AAS

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Creare un nuovo file con

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>03</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/usrsap<b>qas</b>aas
   </code></pre>

   Riavviare `autofs` montare le nuove condivisioni

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[A]**  Configurare il file SWAP

   <pre><code>
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Riavviare l'agente per attivare la modifica

   <pre><code>sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Installare il database

In questo esempio, SAP NetWeaver è installato in SAP HANA. Per questa installazione è possibile usare qualsiasi database supportato. Per altre informazioni su come installare SAP HANA in Azure, vedere [Disponibilità elevata di SAP HANA in macchine virtuali di Azure (VM)][sap-hana-ha]. Per un elenco dei database supportati, vedere la [nota SAP 1928533][1928533].

* Eseguire l'installazione dell'istanza del database SAP

   Installare l'istanza del database SAP NetWeaver come root usando un nome host virtuale mappato all'indirizzo IP della configurazione front-end di bilanciamento del carico per il database.

   È possibile usare il parametro sapinst SAPINST_REMOTE_ACCESS_USER per consentire a un utente non ROOT di connettersi a sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Installazione del server applicazioni di SAP NetWeaver

Per installare il server applicazioni SAP, seguire questi passaggi.

1. **[A]**  Prepara il server applicazioni seguire i passaggi nel capitolo [preparazione del server applicazioni SAP NetWeaver](high-availability-guide-suse-netapp-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) precedenti per preparare il server applicazioni.

2. **[A]**  Server applicazioni di installare SAP NetWeaver installare un server applicazioni SAP NetWeaver primario o aggiuntivo.

   È possibile usare il parametro sapinst SAPINST_REMOTE_ACCESS_USER per consentire a un utente non ROOT di connettersi a sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

3. **[A]**  Archivio sicuro update SAP HANA

   Aggiornare l'archivio sicuro di SAP HANA in modo che punti al nome virtuale della configurazione della replica di sistema di SAP HANA.

   Eseguire il comando seguente per ottenere un elenco delle voci
   <pre><code>
   hdbuserstore List
   </code></pre>

   Viene visualizzato un elenco di tutte le voci, che avrà un aspetto simile al seguente
   <pre><code>
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.1.1.5:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>QAS</b>
   </code></pre>

   L'output mostra che l'indirizzo IP della voce predefinita fa riferimento alla macchina virtuale e non all'indirizzo IP del bilanciamento del carico. Questa voce deve essere modificata in modo che faccia riferimento al nome host virtuale del bilanciamento del carico. Assicurarsi di usare la stessa porta (**30313** nell'output precedente) e il nome del database (**QAS** nell'output precedente).

   <pre><code>
   su - <b>qas</b>adm
   hdbuserstore SET DEFAULT <b>qasdb:30313@QAS</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>Testare la configurazione del cluster

I test seguenti sono una copia di test case nel [best guide pratiche di SUSE][suse-ha-guide]. Vengono copiati per comodità. Leggere sempre anche le guide alle procedure consigliate ed eseguire tutti i test aggiuntivi che possono essere stati aggiunti.

1. Test HAGetFailoverConfig HACheckConfig e HACheckFailoverConfig

   Eseguire i comandi seguenti come \<sapsid>adm nel nodo di cui è in esecuzione l'istanza di ASCS. Se i comandi hanno esito negativo con ERRORE: memoria insufficiente, la causa potrebbe essere la presenza di trattini nel nome host. Si tratta di un problema noto, che verrà risolto da SUSE nel pacchetto sap-suse-cluster-connector.

   <pre><code>
   anftstsapcl1:qasadm 52> sapcontrol -nr 00 -function HAGetFailoverConfig
   07.03.2019 20:08:59
   HAGetFailoverConfig
   OK
   HAActive: TRUE
   HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP3
   HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP3 (sap_suse_cluster_connector 3.1.0)
   HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   HAActiveNode: anftstsapcl1
   HANodes: anftstsapcl1, anftstsapcl2

   anftstsapcl1:qasadm 54> sapcontrol -nr 00 -function HACheckConfig
   07.03.2019 23:28:29
   HACheckConfig
   OK
   state, category, description, comment
   SUCCESS, SAP CONFIGURATION, Redundant ABAP instance configuration, 2 ABAP instances detected
   SUCCESS, SAP CONFIGURATION, Redundant Java instance configuration, 0 Java instances detected
   SUCCESS, SAP CONFIGURATION, Enqueue separation, All Enqueue server separated from application server
   SUCCESS, SAP CONFIGURATION, MessageServer separation, All MessageServer separated from application server
   SUCCESS, SAP CONFIGURATION, ABAP instances on multiple hosts, ABAP instances on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP SPOOL service configuration, 2 ABAP instances with SPOOL service detected
   SUCCESS, SAP STATE, Redundant ABAP SPOOL service state, 2 ABAP instances with active SPOOL service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP SPOOL service on multiple hosts, ABAP instances with active ABAP SPOOL service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP BATCH service configuration, 2 ABAP instances with BATCH service detected
   SUCCESS, SAP STATE, Redundant ABAP BATCH service state, 2 ABAP instances with active BATCH service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP BATCH service on multiple hosts, ABAP instances with active ABAP BATCH service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP DIALOG service configuration, 2 ABAP instances with DIALOG service detected
   SUCCESS, SAP STATE, Redundant ABAP DIALOG service state, 2 ABAP instances with active DIALOG service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP DIALOG service on multiple hosts, ABAP instances with active ABAP DIALOG service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP UPDATE service configuration, 2 ABAP instances with UPDATE service detected
   SUCCESS, SAP STATE, Redundant ABAP UPDATE service state, 2 ABAP instances with active UPDATE service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP UPDATE service on multiple hosts, ABAP instances with active ABAP UPDATE service on multiple hosts detected
   SUCCESS, SAP STATE, SCS instance running, SCS instance status ok
   SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version (anftstsapvh_QAS_00), SAPInstance includes is-ers patch
   SUCCESS, SAP CONFIGURATION, Enqueue replication (anftstsapvh_QAS_00), Enqueue replication enabled
   SUCCESS, SAP STATE, Enqueue replication state (anftstsapvh_QAS_00), Enqueue replication active
   
   anftstsapcl1:qasadm 55> sapcontrol -nr 00 -function HACheckFailoverConfig
   07.03.2019 23:30:48
   HACheckFailoverConfig
   OK
   state, category, description, comment
   SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   </code></pre>

2. Eseguire la migrazione manuale dell'istanza ASCS

   Stato delle risorse prima dell'avvio del test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rscsap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Starting anftstsapcl1
   </code></pre>

   Eseguire i comandi seguenti come radice per la migrazione dell'istanza ASCS.

   <pre><code>
   anftstsapcl1:~ # crm resource migrate rsc_sap_QAS_ASCS00 force
   INFO: Move constraint created for rsc_sap_QAS_ASCS00
   
   anftstsapcl1:~ # crm resource unmigrate rsc_sap_QAS_ASCS00
   INFO: Removed migration constraints for rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Stato delle risorse dopo il test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

3. Testare HAFailoverToNode

   Stato delle risorse prima dell'avvio del test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   Eseguire i comandi seguenti come \<sapsid>adm per la migrazione dell'istanza ASCS.

   <pre><code>
   anftstsapcl1:qasadm 53> sapcontrol -nr 00 -host anftstsapvh -user <b>qas</b>adm &lt;password&gt; -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Stato delle risorse dopo il test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

4. Simulare l'arresto anomalo del nodo 

   Stato delle risorse prima dell'avvio del test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Eseguire i comandi seguenti come radice nel nodo di cui è in esecuzione l'istanza di ASCS

   <pre><code>anftstsapcl2:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Se si usa SBD, Pacemaker non dovrebbe essere avviato automaticamente nel nodo terminato. Lo stato dopo il riavvio del nodo dovrebbe essere simile al seguente.

   <pre><code>Online:
   Online: [ anftstsapcl1 ]
   OFFLINE: [ anftstsapcl2 ]

   Full list of resources:

    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1

   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=166, status=complete, exitreason='',
    last-rc-change='Fri Mar  8 18:26:10 2019', queued=0ms, exec=0ms
   </code></pre>

   Usare i comandi seguenti per avviare Pacemaker nel nodo terminato, pulire i messaggi SBD, quindi pulire la risorsa per la quale si è verificato un errore.

   <pre><code>
   # run as root
   # list the SBD device(s)
   anftstsapcl2:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405b730e31e7d5a4516a2a697dcf;/dev/disk/by-id/scsi-36001405f69d7ed91ef54461a442c676e;/dev/disk/by-id/scsi-360014058e5f335f2567488882f3a2c3a"

   anftstsapcl2:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e11 -d /dev/disk/by-id/scsi-36001405f69d7ed91ef54461a442c676e -d /dev/disk/by-id/scsi-360014058e5f335f2567488882f3a2c3a message anftstsapcl2 clear

   anftstsapcl2:~ # systemctl start pacemaker
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Stato delle risorse dopo il test:

   <pre><code>
   Full list of resources:
   
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

5. Testare il riavvio manuale dell'istanza ASCS

   Stato delle risorse prima dell'avvio del test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Creare un blocco di accodamento, ad esempio modificando un utente nella transazione su01. Eseguire i comandi seguenti come < sapsid\>adm nel nodo in cui è in esecuzione l'istanza di ASCS. I comandi interromperanno l'istanza ASCS per poi riavviarla. Se si usa l'architettura del server 1 enqueue, il blocco di accodamento è previsto per questo test non venga mantenuta. Se si usa l'architettura di Accodamento server 2, l'accodamento verrà conservata. 

   <pre><code>anftstsapcl2:qasadm 51> sapcontrol -nr 00 -function StopWait 600 2
   </code></pre>

   L'istanza ASCS dovrebbe essere ora disabilitata in Pacemaker

   <pre><code>  rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Stopped (disabled)
   </code></pre>

   Avviare l'istanza ASCS nuovamente nello stesso nodo.

   <pre><code>anftstsapcl2:qasadm 52> sapcontrol -nr 00 -function StartWait 600 2
   </code></pre>

   Il blocco di Accodamento di transazione su01 andrà perso, se si usa l'architettura di replica 1 server di accodamento e il back-end deve essere stato reimpostato. Stato delle risorse dopo il test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

6. Terminare il processo server messaggi

   Stato delle risorse prima dell'avvio del test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Eseguire i comandi seguenti come radice per identificare il processo del server messaggi e terminarlo.

   <pre><code>anftstsapcl2:~ # pgrep ms.sapQAS | xargs kill -9
   </code></pre>

   Terminando il server messaggi una sola volta, questo verrà riavviato da sapstart. Terminandolo un numero di volte sufficiente, Pacemaker sposterà infine l'istanza ASCS in un altro nodo. Eseguire i comandi seguenti come radice per pulire lo stato della risorsa dell'istanza ASCS ed ERS dopo il test.

   <pre><code>
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Stato delle risorse dopo il test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

7. Terminare il processo server di accodamento

   Stato delle risorse prima dell'avvio del test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   Eseguire i comandi seguenti come radice nel nodo di cui è in esecuzione l'istanza di ASCS per terminare il server di accodamento.

   <pre><code>anftstsapcl1:~ # pgrep en.sapQAS | xargs kill -9
   </code></pre>

   L'istanza ASCS deve eseguire immediatamente il failover in un altro nodo. Anche l'istanza ERS deve eseguire il failover dopo l'avvio dell'istanza ASCS. Eseguire i comandi seguenti come radice per pulire lo stato della risorsa dell'istanza ASCS ed ERS dopo il test.

   <pre><code>
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Stato delle risorse dopo il test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

8. Terminare il processo del server di replica dell'accodamento

   Stato delle risorse prima dell'avvio del test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Eseguire i comandi seguenti come radice nel nodo in cui è in esecuzione l'istanza ERS per terminare il processo del server di replica dell'accodamento.

   <pre><code>anftstsapcl1:~ # pgrep er.sapQAS | xargs kill -9
   </code></pre>

   Se si esegue solo il comando una volta, `sapstart` riavvierà il processo. Se viene eseguito abbastanza spesso `sapstart` non verrà riavviato il processo e la risorsa sarà nello stato arrestato. Eseguire i comandi seguenti come radice per pulire lo stato della risorsa dell'istanza ERS dopo il test.

   <pre><code>anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Stato delle risorse dopo il test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

9. Terminare il processo di accodamento sapstartsrv

   Stato delle risorse prima dell'avvio del test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Eseguire i comandi seguenti come radice nel nodo di cui è in esecuzione ASCS.

   <pre><code>
   anftstsapcl2:~ # pgrep -fl ASCS00.*sapstartsrv
   #67625 sapstartsrv
   
   anftstsapcl2:~ # kill -9 67625
   </code></pre>

   Il processo sapstartsrv deve sempre essere riavviato per l'agente delle risorse Pacemaker. Stato delle risorse dopo il test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

## <a name="next-steps"></a>Passaggi successivi

* [Pianificazione e implementazione di Macchine virtuali di Azure per SAP][planning-guide]
* [Distribuzione di Macchine virtuali di Azure per SAP][deployment-guide]
* [Distribuzione DBMS di Macchine virtuali di Azure per SAP][dbms-guide]
* Per informazioni su come implementare la disponibilità elevata e pianificare il ripristino di emergenza di SAP 
* HANA in Azure (istanze large), vedere [SAP HANA (istanze large) ad alto disponibilità e ripristino di emergenza in Azure](hana-overview-high-availability-disaster-recovery.md).
* Per informazioni su come ottenere la disponibilità elevata e un piano di ripristino di emergenza di SAP HANA nelle macchine virtuali di Azure, vedere [Disponibilità elevata di SAP HANA nelle macchine virtuali di Azure (VM)][sap-hana-ha].
