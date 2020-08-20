---
title: Disponibilità elevata delle macchine virtuali di Azure per SAP NW su SLES con Azure NetApp Files | Microsoft Docs
description: Guida alla disponibilità elevata per SAP NetWeaver su SUSE Linux Enterprise Server con Azure NetApp Files per applicazioni SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/04/2020
ms.author: radeltch
ms.openlocfilehash: dd245ddfa7f6be90e37bacc0797a3af4adc8bb96
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88650435"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-with-azure-netapp-files-for-sap-applications"></a>Disponibilità elevata per SAP NetWeaver su macchine virtuali di Azure su SUSE Linux Enterprise Server con Azure NetApp Files per applicazioni SAP

[dbms-guide]:dbms_guide_general.md
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

Questo articolo descrive come distribuire le macchine virtuali, configurare le macchine virtuali, installare il framework del cluster e installare un sistema SAP NetWeaver 7.50 a disponibilità elevata mediante [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md).
Nelle configurazioni di esempio, come i comandi di installazione e così via, l'istanza di ASC è il numero 00, il numero di istanza ERS è 01, l'istanza dell'applicazione primaria (PAS) è 02 e l'istanza dell'applicazione (AAS) è 03. Viene usato l'ID del sistema SAP QAS. 

Questo articolo illustra come ottenere la disponibilità elevata per l'applicazione SAP NetWeaver con Azure NetApp Files. Il livello del database non è descritto in dettaglio in questo articolo.

Leggere prima di tutto i documenti e le note SAP seguenti:

* [Documentazione di Azure NetApp Files][anf-azure-doc] 
* Nota SAP [1928533][1928533], contenente:  
  * Elenco delle dimensioni delle VM di Azure supportate per la distribuzione di software SAP
  * Importanti informazioni sulla capacità per le dimensioni delle VM di Azure
  * Software SAP e combinazioni di sistemi operativi e database supportati
  * Versione del kernel SAP richiesta per Windows e Linux in Microsoft Azure
* La nota SAP [2015553][2015553] elenca i prerequisiti per le distribuzioni di software SAP supportate da SAP in Azure.
* La nota SAP [2205917][2205917] contiene le impostazioni consigliate del sistema operativo per SUSE Linux Enterprise Server for SAP Applications
* La nota SAP [1944799][1944799] contiene linee guida per SAP HANA per SUSE Linux Enterprise Server for SAP Applications
* La nota SAP [2178632][2178632] contiene informazioni dettagliate su tutte le metriche di monitoraggio segnalate per SAP in Azure.
* La nota SAP [2191498][2191498] contiene la versione dell'agente host SAP per Linux necessaria in Azure.
* La nota SAP [2243692][2243692] contiene informazioni sulle licenze SAP in Linux in Azure.
* La nota SAP [1984787][1984787] contiene informazioni generali su SUSE Linux Enterprise Server 12.
* La nota SAP [1999351][1999351] contiene informazioni aggiuntive sulla risoluzione dei problemi per l'estensione di monitoraggio avanzato di Azure per SAP.
* Community WIKI SAP https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) contiene tutte le note su SAP necessarie per Linux.
* [Pianificazione e implementazione di Macchine virtuali di Azure per SAP in Linux][planning-guide]
* [Distribuzione di Macchine virtuali di Azure per SAP in Linux][deployment-guide]
* [Distribuzione DBMS di Macchine virtuali di Azure per SAP in Linux][dbms-guide]
* [SUSE SAP HA Best Practice Guides][suse-ha-guide] (Guide relative alle procedure consigliate di SUSE SAP HA) Le guide contengono tutte le informazioni necessarie per configurare la replica di sistema Netweaver HA e SAP HANA in locale. Usare le guide come indicazioni di base generali. Forniscono informazioni molto più dettagliate.
* [Note sulla versione di SUSE High Availability Extension 12 SP3][suse-ha-12sp3-relnotes]
* [Applicazioni NetApp SAP su Microsoft Azure con Azure NetApp Files][anf-sap-applications-azure]

## <a name="overview"></a>Panoramica

La disponibilità elevata per SAP NetWeaver Central Services richiede l'archiviazione condivisa.
Per ottenere questo risultato in SUSE Linux finora era necessario compilare un cluster NFS a disponibilità elevata separato. 

È ora possibile ottenere la disponibilità elevata di SAP NetWeaver usando l'archiviazione condivisa, distribuita in Azure NetApp Files. L'uso di Azure NetApp Files per l'archiviazione condivisa elimina la necessità di un [cluster NFS](./high-availability-guide-suse-nfs.md) aggiuntivo. Pacemaker è ancora necessario per la disponibilità elevata di SAP Netweaver Central Services (ASCS/SCS).


![Panoramica della disponibilità elevata di SAP NetWeaver](./media/high-availability-guide-suse-anf/high-availability-guide-suse-anf.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS e il database SAP HANA usano un nome host virtuale e indirizzi IP virtuali. In Azure è necessario un [servizio di bilanciamento del carico](../../../load-balancer/load-balancer-overview.md) per usare un indirizzo IP virtuale. È consigliabile usare [Load Balancer Standard](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md). L'elenco seguente mostra la configurazione del bilanciamento del carico di (A)SCS ed ERS.

### <a name="ascs"></a>(A)SCS

* Configurazione front-end
  * Indirizzo IP 10.1.1.20
* Porta probe
  * Porta 620<strong>&lt;nr&gt;</strong>
* Regole di bilanciamento del carico
  * Se si usa Load Balancer Standard, selezionare **Porte a disponibilità elevata**
  * Se si usa Load Balancer Basic, creare regole di bilanciamento del carico per le porte seguenti
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
* Porta probe
  * Porta 621<strong>&lt;nr&gt;</strong>
* Regole di bilanciamento del carico
  * Se si usa Load Balancer Standard, selezionare **Porte a disponibilità elevata**
  * Se si usa Load Balancer Basic, creare regole di bilanciamento del carico per le porte seguenti
    * 32<strong>&lt;nr&gt;</strong> TCP
    * 33<strong>&lt;nr&gt;</strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;nr&gt;</strong>16 TCP

* Configurazione back-end
  * Connessione alle interfacce di rete primarie di tutte le macchine virtuali che devono far parte del cluster (A)SCS/ERS


## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Configurazione dell'infrastruttura Azure NetApp Files 

SAP NetWeaver richiede un'archiviazione condivisa per la directory di trasporto e del profilo.  Prima di procedere con la configurazione dell'infrastruttura Azure NetApp Files, acquisire familiarità con la [documentazione di Azure NetApp Files][anf-azure-doc]. Controllare se l'area di Azure selezionata offre Azure NetApp Files. Il collegamento seguente mostra la disponibilità di Azure NetApp Files per area di Azure: [Disponibilità di Azure NetApp Files per area di Azure][anf-avail-matrix].

Azure NetApp Files è disponibile in numerose [aree di Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Prima di distribuire Azure NetApp Files, richiedere l'onboarding in Azure NetApp Files, seguendo le [istruzioni di registrazione per Azure NetApp Files][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Distribuire le risorse di Azure NetApp Files  

I passaggi presuppongono che sia già stata eseguita la distribuzione di [Rete virtuale di Azure](../../../virtual-network/virtual-networks-overview.md). Le risorse di Azure NetApp Files e tutte le macchine virtuali, in cui le risorse di Azure NetApp Files verranno montate, devono essere distribuite nella stessa istanza di Rete virtuale di Azure o in reti virtuali di Azure con peering.  

1. Se non è già stato fatto, richiedere l'[onboarding in Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md).  

2. Creare l'account NetApp nell'area di Azure selezionata, seguendo le [istruzioni per creare un account NetApp](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md).  
3. Configurare il pool di capacità di Azure NetApp Files, seguendo le [istruzioni su come configurare pool di capacità Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md).  
L'architettura di SAP NetWeaver presentata in questo articolo usa un singolo pool di capacità di Azure NetApp Files, SKU Premium. Lo SKU Premium di Azure NetApp Files è consigliabile per il carico di lavoro dell'applicazione SAP NetWeaver in Azure.  

4. Delegare una subnet ai file di Azure NetApp come descritto nelle [istruzioni per delegare una subnet a Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).  

5. Distribuire volumi di Azure NetApp Files, seguendo le [istruzioni per creare un volume per Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md). Distribuire i volumi nella [subnet](/rest/api/virtualnetwork/subnets) di Azure NetApp Files designata. Gli indirizzi IP dei volumi di Azure NetApp vengono assegnati automaticamente. Le risorse di Azure NetApp Files e le macchine virtuali di Azure devono trovarsi nella stessa istanza di Rete virtuale di Azure o in reti virtuali di Azure con peering. In questo esempio vengono usati due volumi Azure NetApp Files: sap<b>QAS</b> e trans. I percorsi file montati nei punti di montaggio corrispondenti sono /usrsap<b>qas</b>/sapmnt<b>QAS</b>, /usrsap<b>qas</b>/usrsap<b>QAS</b>sys e così via.  

   1. volume sap<b>QAS</b> (nfs://10.1.0.4/usrsap<b>qas</b>/sapmnt<b>QAS</b>)
   2. volume sap<b>QAS</b> (nfs://10.1.0.4/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs)
   3. volume sap<b>QAS</b> (nfs://10.1.0.4/usrsap<b>qas</b>/usrsap<b>QAS</b>sys)
   4. volume sap<b>QAS</b> (nfs://10.1.0.4/usrsap<b>qas</b>/usrsap<b>QAS</b>ers)
   5. volume trans (nfs://10.1.0.4/trans)
   6. volume sap<b>QAS</b> (nfs://10.1.0.4/usrsap<b>qas</b>/usrsap<b>QAS</b>pas)
   7. volume sap<b>QAS</b> (nfs://10.1.0.4/usrsap<b>qas</b>/usrsap<b>QAS</b>aas)

   
In questo esempio è stato usato Azure NetApp Files per tutti i file system di SAP NetWeaver per dimostrare come è possibile usare Azure NetApp Files. I file system SAP che non devono essere montati tramite NFS possono anche essere distribuiti come [archiviazione su disco di Azure](../../disks-types.md#premium-ssd). In questo esempio <b>a-e</b> deve trovarsi in Azure NetApp Files e <b>f-g</b> (ovvero /usr/sap/<b>QAS</b>/D<b>02</b>, /usr/sap/<b>QAS</b>/D<b>03</b>) può essere distribuito come archiviazione su disco di Azure. 

### <a name="important-considerations"></a>Considerazioni importanti

Quando si prende in considerazione Azure NetApp Files per SAP Netweaver sull'architettura a disponibilità elevata SUSE, tenere presenti le considerazioni importanti seguenti:

- La capacità minima del pool è di 4 TiB. È possibile aumentare le dimensioni del pool di capacità con incrementi di 1 TiB.
- Il volume minimo è di 100 GiB.
- Azure NetApp Files e tutte le macchine virtuali in cui verranno montati i volumi Azure NetApp Files devono trovarsi nella stessa istanza di Rete virtuale di Azure o in [reti virtuali con peering](../../../virtual-network/virtual-network-peering-overview.md) nella stessa area. L'accesso ad Azure NetApp Files mediante peering VNET nella stessa area è ora supportato. L'accesso a NetApp di Azure mediante peering globale non è ancora supportato.
- La rete virtuale selezionata deve avere una subnet delegata ad Azure NetApp Files.
- Azure NetApp Files offre [criteri di esportazione](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md): è possibile controllare i client consentiti, il tipo di accesso, ad esempio lettura e scrittura, sola lettura e così via. 
- La funzionalità Azure NetApp Files non è ancora in grado di riconoscere la zona. Attualmente la funzionalità Azure NetApp Files non viene distribuita in tutte le zone di disponibilità in un'area di Azure. Tenere presente le implicazioni di latenza potenziali in alcune aree di Azure. 
- I volumi Azure NetApp Files possono essere distribuiti come volumi NFSv3 o NFSv4.1. Entrambi i protocolli sono supportati per il livello applicazione SAP (server di applicazione SAP, ASCS/ERS). 

## <a name="deploy-linux-vms-manually-via-azure-portal"></a>Distribuire macchine virtuali Linux manualmente tramite il portale di Azure

Per prima cosa è necessario creare i volumi Azure NetApp Files. Distribuire le macchine virtuali. Successivamente, creare un servizio di bilanciamento del carico e usare le macchine virtuali nei pool back-end.

1. Creare un gruppo di risorse
1. Creare una rete virtuale
1. Creare un set di disponibilità per ASCS.  
   Impostare il numero massimo di domini di aggiornamento
1. Creare la macchina virtuale 1  
   Usare almeno SLES4SAP 12 SP3, in questo esempio viene usata l'immagine SLES4SAP 12 SP3  
   Selezionare il set di disponibilità creato in precedenza per ASCS  
1. Creare la macchina virtuale 2  
   Usare almeno SLES4SAP 12 SP3, in questo esempio viene usata l'immagine SLES4SAP 12 SP3  
   Selezionare il set di disponibilità creato in precedenza per ASCS  
1. Creare un set di disponibilità per le istanze dell'applicazione SAP (PAS, AAS)    
   Impostare il numero massimo di domini di aggiornamento
1. Creare la macchina virtuale 3  
   Usare almeno SLES4SAP 12 SP3, in questo esempio viene usata l'immagine SLES4SAP 12 SP3  
   Selezionare il set di disponibilità creato in precedenza per PAS/AAS   
1. Creare la macchina virtuale 4  
   Usare almeno SLES4SAP 12 SP3, in questo esempio viene usata l'immagine SLES4SAP 12 SP3  
   Selezionare il set di disponibilità creato in precedenza per PAS/AAS  

## <a name="disable-id-mapping-if-using-nfsv41"></a>Disabilitare il mapping degli ID (se si usa NFSv4.1)

Le istruzioni riportate in questa sezione sono applicabili solo se si usano volumi Azure NetApp Files con il protocollo NFSv4.1. Eseguire la configurazione su tutte le macchine virtuali, in cui verranno montati volumi Azure NetApp Files NFSv4.1.  

1. Verificare l'impostazione del dominio NFS. Verificare che il dominio sia configurato come dominio di Azure NetApp Files predefinito, ad esempio **`defaultv4iddomain.com`** , e che il mapping sia impostato su **nobody** (nessuno).  

    > [!IMPORTANT]
    > Verificare di impostare il dominio NFS in `/etc/idmapd.conf` sulla macchina virtuale in modo che corrisponda alla configurazione del dominio predefinito in Azure NetApp Files: **`defaultv4iddomain.com`** . In caso di mancata corrispondenza tra la configurazione del dominio nel client NFS (ovvero la macchina virtuale) e nel server NFS, ad esempio la configurazione di Azure NetApp, le autorizzazioni per i file nei volumi Azure NetApp montati nelle VM verranno visualizzate come `nobody`.  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Verbosity = 0
    Pipefs-Directory = /var/lib/nfs/rpc_pipefs
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** Verificare `nfs4_disable_idmapping`. Il valore deve essere impostato su **Y**. Per creare la struttura di directory in cui si trova `nfs4_disable_idmapping`, eseguire il comando mount. Non sarà possibile creare manualmente la directory in/sys/modules, perché l'accesso è riservato per il kernel/driver.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.1.0.4:/sapmnt/<b>qas</b> /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>


## <a name="setting-up-ascs"></a>Configurazione di (A)SCS

In questo esempio le risorse sono state distribuite manualmente tramite il [portale di Azure](https://portal.azure.com/#home).

### <a name="deploy-azure-load-balancer-manually-via-azure-portal"></a>Distribuire Azure Load Balancer manualmente tramite il portale di Azure

Per prima cosa è necessario creare i volumi Azure NetApp Files. Distribuire le macchine virtuali. Creare quindi un servizio di bilanciamento del carico e usare le macchine virtuali nei pool back-end.

1. Creare un bilanciamento del carico (interno, standard).  
   1. Creare gli indirizzi IP front-end
      1. Indirizzo IP 10.1.1.20 per ASCS
         1. Aprire il servizio di bilanciamento del carico, selezionare Pool di indirizzi IP front-end e fare clic su Aggiungi
         1. Immettere il nome del nuovo pool di indirizzi IP front-end, ad esempio **frontend.QAS.ASCS**
         1. Impostare Assegnazione su Statico e immettere l'indirizzo IP, ad esempio **10.1.1.20**
         1. Fare clic su OK.
      1. Indirizzo IP 10.1.1.21 per ASCS ERS
         * Ripetere i passaggi precedenti in "a" per creare un indirizzo IP per ERS, ad esempio **10.1.1.21** e **frontend.QAS.ERS**
   1. Creare il pool back-end
      1. Aprire il servizio di bilanciamento del carico, selezionare Pool back-end e fare clic su Aggiungi
      1. Immettere il nome del nuovo pool back-end, ad esempio **backend.QAS**
      1. Fare clic su Aggiungi una macchina virtuale.
      1. Selezionare Macchina virtuale.
      1. Selezionare le macchine virtuali del cluster (A)SCS e il rispettivo indirizzo IP.
      1. Fare clic su Aggiungi.
   1. Creare i probe di integrità
      1. Porta 620**00** per ASCS
         1. Aprire il servizio di bilanciamento del carico, selezionare Probe integrità e fare clic su Aggiungi
         1. Immettere il nome del nuovo probe di integrità, ad esempio **health.QAS.ASCS**
         1. Selezionare TCP come protocollo, la porta 620**00**, mantenere 5 per Intervallo e impostare Soglia di non integrità su 2
         1. Fare clic su OK.
      1. Porta 621**01** per ASCS ERS
            * Ripetere i passaggi precedenti in "c" per creare un probe di integrità per ERS, ad esempio 621**01** e **health.QAS.ERS**
   1. Regole di bilanciamento del carico
      1. Creare un pool back-end per ASCS
         1. Aprire il servizio di bilanciamento del carico, selezionare Regole di bilanciamento del carico e fare clic su Aggiungi
         1. Immettere il nome della nuova regola di bilanciamento del carico, ad esempio **lb.QAS.ASCS**
         1. Selezionare l'indirizzo IP front-end per ASCS, il pool back-end e il probe di integrità creati in precedenza, ad esempio **frontend.QAS.ASCS**, **backend.QAS** e **health.QAS.ASCS**
         1. Selezionare **Porte a disponibilità elevata**
         1. Aumentare il timeout di inattività a 30 minuti
         1. **Assicurarsi di abilitare l'indirizzo IP mobile**
         1. Fare clic su OK.
         * Ripetere i passaggi precedenti per creare regole di bilanciamento del carico per ERS, ad esempio **lb.QAS.ERS**
1. In alternativa, se lo scenario richiede Load Balancer Basic (interno), seguire questa procedura:  
   1. Creare gli indirizzi IP front-end
      1. Indirizzo IP 10.1.1.20 per ASCS
         1. Aprire il servizio di bilanciamento del carico, selezionare Pool di indirizzi IP front-end e fare clic su Aggiungi
         1. Immettere il nome del nuovo pool di indirizzi IP front-end, ad esempio **frontend.QAS.ASCS**
         1. Impostare Assegnazione su Statico e immettere l'indirizzo IP, ad esempio **10.1.1.20**
         1. Fare clic su OK.
      1. Indirizzo IP 10.1.1.21 per ASCS ERS
         * Ripetere i passaggi precedenti in "a" per creare un indirizzo IP per ERS, ad esempio **10.1.1.21** e **frontend.QAS.ERS**
   1. Creare il pool back-end
      1. Aprire il servizio di bilanciamento del carico, selezionare Pool back-end e fare clic su Aggiungi
      1. Immettere il nome del nuovo pool back-end, ad esempio **backend.QAS**
      1. Fare clic su Aggiungi una macchina virtuale.
      1. Selezionare il set di disponibilità creato in precedenza per ASCS 
      1. Selezionare le macchine virtuali del cluster (A)SCS
      1. Fare clic su OK.
   1. Creare i probe di integrità
      1. Porta 620**00** per ASCS
         1. Aprire il servizio di bilanciamento del carico, selezionare Probe integrità e fare clic su Aggiungi
         1. Immettere il nome del nuovo probe di integrità, ad esempio **health.QAS.ASCS**
         1. Selezionare TCP come protocollo, la porta 620**00**, mantenere 5 per Intervallo e impostare Soglia di non integrità su 2
         1. Fare clic su OK.
      1. Porta 621**01** per ASCS ERS
            * Ripetere i passaggi precedenti in "c" per creare un probe di integrità per ERS, ad esempio 621**01** e **health.QAS.ERS**
   1. Regole di bilanciamento del carico
      1. TCP 32**00** per ASCS
         1. Aprire il servizio di bilanciamento del carico, selezionare Regole di bilanciamento del carico e fare clic su Aggiungi
         1. Immettere il nome della nuova regola di bilanciamento del carico, ad esempio **lb.QAS.ASCS.3200**
         1. Selezionare l'indirizzo IP front-end per ASCS, il pool back-end e il probe di integrità creati in precedenza, ad esempio **frontend.QAS.ASCS**
         1. Mantenere il protocollo **TCP**, immettere la porta **3200**
         1. Aumentare il timeout di inattività a 30 minuti
         1. **Assicurarsi di abilitare l'indirizzo IP mobile**
         1. Fare clic su OK.
      1. Porte aggiuntive per ASCS
         * Ripetere i passaggi precedenti per le porte 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 e TCP per ASCS
      1. Porte aggiuntive per ASCS ERS
         * Ripetere i passaggi precedenti per le porte 32**01**, 33**01**, 5**01**13, 5**01**14, 5**01**16 e TCP per ASCS ERS

      > [!Note]
      > Se vengono inserite macchine virtuali senza indirizzi IP pubblici nel pool back-end di Load Balancer Standard interno ad Azure (nessun indirizzo IP pubblico), non sarà presente alcuna connettività Internet in uscita, a meno che non venga eseguita una configurazione aggiuntiva per consentire il routing a endpoint pubblici. Per informazioni dettagliate su come ottenere la connettività in uscita, vedere [Connettività degli endpoint pubblici per le macchine virtuali usando Load Balancer Standard di Azure negli scenari a disponibilità elevata SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

      > [!IMPORTANT]
      > Non abilitare i timestamp TCP nelle macchine virtuali di Azure che si trovano dietro Azure Load Balancer. Se si abilitano i timestamp TCP, i probe di integrità avranno esito negativo. Impostare il parametro **net.ipv4.tcp_timestamps** su **0**. Per informazioni dettagliate, vedere [Probe di integrità di Load Balancer](../../../load-balancer/load-balancer-custom-probe-overview.md).

### <a name="create-pacemaker-cluster"></a>Creare un cluster Pacemaker

Seguire i passaggi descritti in [Configurazione di Pacemaker su SUSE Linux Enterprise Server in Azure](high-availability-guide-suse-pacemaker.md) per creare un cluster Pacemaker di base per questo server (A)SCS.

### <a name="installation"></a>Installazione

Gli elementi seguenti sono preceduti dall'indicazione **[A]** - applicabile a tutti i nodi, **[1]** - applicabile solo al nodo 1 o **[2]** - applicabile solo al nodo 2.

1. **[A]**  Installare il connettore SUSE

   <pre><code>sudo zypper install sap-suse-cluster-connector
   </code></pre>

   > [!NOTE]
   > Il problema noto relativo all'uso di un trattino nei nomi host è stato risolto con la versione **3.1.1** del pacchetto **sap-suse-cluster-connector**. Verificare di usare almeno la versione 3.1.1 del pacchetto sap-suse-cluster-connector, se si usano nodi del cluster con un trattino nel nome host. In caso contrario, il cluster non funzionerà. 

   Verificare di aver installato la nuova versione del connettore del cluster SAP SUSE. La versione precedente era denominata sap_suse_cluster_connector mentre la nuova è denominata **sap-suse-cluster-connector**.

   <pre><code>sudo zypper info sap-suse-cluster-connector
   
    # Information for package sap-suse-cluster-connector:
    # ---------------------------------------------------
    # Repository     : SLE-12-SP3-SAP-Updates
    # Name           : sap-suse-cluster-connector
    # Version        : 3.1.0-8.1
    # Arch           : noarch
    # Vendor         : SUSE LLC &lt;https://www.suse.com/&gt;
    # Support Level  : Level 3
    # Installed Size : 45.6 KiB
    # Installed      : Yes
    # Status         : up-to-date
    # Source package : sap-suse-cluster-connector-3.1.0-8.1.src
    # Summary        : SUSE High Availability Setup for SAP Products
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

4. **[1]** Creare directory SAP nel volume Azure NetApp Files.  
   Montare temporaneamente il volume Azure NetApp Files in una delle macchine virtuali e creare le directory SAP (percorsi di file).  

   ```
    # mount temporarily the volume
    sudo mkdir -p /saptmp
    # If using NFSv3
    sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 10.1.0.4:/sapQAS /saptmp
    # If using NFSv4.1
    sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys,tcp 10.1.0.4:/sapQAS /saptmp
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

2. **[A]** Configurare `autofs`

   <pre><code>
   sudo vi /etc/auto.master
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Se si usa NFSv3, creare un file con:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind 10.1.0.4:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=3,nobind 10.1.0.4:/trans
   /usr/sap/<b>QAS</b>/SYS -nfsvers=3,nobind 10.1.0.4:/usrsap<b>qas</b>/usrsap<b>QAS</b>sys
   </code></pre>
   
   Se si usa NFSv4.1, creare un file con:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=4.1,nobind,sec=sys 10.1.0.4:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=4.1,nobind,sec=sys 10.1.0.4:/trans
   /usr/sap/<b>QAS</b>/SYS -nfsvers=4.1,nobind,sec=sys 10.1.0.4:/usrsap<b>qas</b>/usrsap<b>QAS</b>sys
   </code></pre>
   
   > [!NOTE]
   > Assicurarsi di associare la versione del protocollo NFS dei volumi Azure NetApp Files, durante il montaggio dei volumi. Se i volumi Azure NetApp Files vengono creati come volumi NFSv3, usare la configurazione NFSv3 corrispondente. Se i volumi Azure NetApp Files vengono creati come volumi NFSv4.1, seguire le istruzioni per disabilitare il mapping degli ID e assicurarsi di usare la configurazione NFSv4.1 corrispondente. In questo esempio i volumi Azure NetApp Files sono stati creati come volumi NFSv3.  
   
   Riavviare `autofs` per montare le nuove condivisioni
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

   > [!IMPORTANT]
   > Sono state rilevate situazioni di test recenti, in cui netcat smette di rispondere alle richieste dovute al backlog e alla limitazione della gestione di una sola connessione. La risorsa netcat smette di essere in ascolto delle richieste del servizio di bilanciamento del carico di Azure e l'IP mobile diventa non disponibile.  
   > Per i cluster Pacemaker esistenti, in passato era consigliabile sostituire netcat con socat. Attualmente è consigliabile usare l'agente di risorse azure-lb, che fa parte degli agenti di risorse del pacchetto, con i requisiti di versione del pacchetto seguenti:
   > - Per SLES 12 SP4/SP5 la versione deve essere almeno resource-agents-4.3.018.a7fb5035-3.30.1.  
   > - Per SLES 15/15 SP1 la versione deve essere almeno resource-agents-4.3.0184.6ee15eb2-4.13.1.  
   >
   > Si noti che la modifica richiederà un breve tempo di inattività.  
   > Per i cluster Pacemaker esistenti, se la configurazione è stata già modificata per usare socat, come descritto in [Azure Load-Balancer Detection Hardening](https://www.suse.com/support/kb/doc/?id=7024128) (Protezione avanzata dei rilevamenti del servizio di bilanciamento del carico di Azure), non è necessario passare immediatamente all'agente delle risorse azure-lb.

   <pre><code>sudo crm node standby <b>anftstsapcl2</b>
   # If using NFSv3
   sudo crm configure primitive fs_<b>QAS</b>_ASCS Filesystem device='<b>10.1.0.4</b>/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs' directory='/usr/sap/<b>QAS</b>/ASCS<b>00</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   # If using NFSv4.1
   sudo crm configure primitive fs_<b>QAS</b>_ASCS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs' directory='/usr/sap/<b>QAS</b>/ASCS<b>00</b>' fstype='nfs' options='sec=sys,vers=4.1' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ASCS IPaddr2 \
     params ip=<b>10.1.1.20</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ASCS azure-lb port=620<b>00</b>
   
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
   #     nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      <b>Started anftstsapcl1</b>
   #     vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   </code></pre>
  
2. **[1]** Installare SAP NetWeaver ASCS  

   Installare SAP NetWeaver ASCS come radice nel primo nodo usando un nome host virtuale mappato all'indirizzo IP della configurazione front-end di bilanciamento del carico per l'istanza ASCS, ad esempio <b>anftstsapvh</b>, <b>10.1.1.20</b> e il numero di istanza usato per il probe del bilanciamento del carico, ad esempio <b>00</b>.

   È possibile usare il parametro sapinst SAPINST_REMOTE_ACCESS_USER per consentire a un utente non ROOT di connettersi a sapinst. È possibile usare il parametro SAPINST_USE_HOSTNAME per installare SAP, usando il nome host virtuale.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   Se durante l'installazione non viene creata una sottocartella in /usr/sap/**QAS**/ASCS**00**, provare a impostare il proprietario e il gruppo della cartella ASCS**00** e riprovare. 

   <pre><code>
   chown <b>qas</b>adm /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   </code></pre>

3. **[1]** Creare una risorsa IP virtuale e un probe di integrità per l'istanza di ERS

   <pre><code>
   sudo crm node online <b>anftstsapcl2</b>
   sudo crm node standby <b>anftstsapcl1</b>
   # If using NFSv3
   sudo crm configure primitive fs_<b>QAS</b>_ERS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>ers' directory='/usr/sap/<b>QAS</b>/ERS<b>01</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   # If using NFSv4.1
   sudo crm configure primitive fs_<b>QAS</b>_ERS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>ers' directory='/usr/sap/<b>QAS</b>/ERS<b>01</b>' fstype='nfs' options='sec=sys,vers=4.1'\
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ERS IPaddr2 \
     params ip=<b>10.1.1.21</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ERS azure-lb port=621<b>01</b>
   
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
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:azure-lb):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS  (ocf::heartbeat:IPaddr2):     <b>Started anftstsapcl2</b>
   </code></pre>

4. **[2]** Installare SAP NetWeaver ERS

   Installare SAP NetWeaver ERS come radice nel secondo nodo usando un nome host virtuale mappato all'indirizzo IP della configurazione front-end di bilanciamento del carico per ERS, ad esempio <b>anftstsapers</b>, <b>10.1.1.21</b> e il numero di istanza usato per il probe del bilanciamento del carico, ad esempio <b>01</b>.

   È possibile usare il parametro sapinst SAPINST_REMOTE_ACCESS_USER per consentire a un utente non ROOT di connettersi a sapinst. È possibile usare il parametro SAPINST_USE_HOSTNAME per installare SAP, usando il nome host virtuale.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   > [!NOTE]
   > Usare SWPM SP 20 PL 05 o versione successiva. Le versioni precedenti non impostano correttamente le autorizzazioni e l'installazione avrà esito negativo.

   Se durante l'installazione non viene creata una sottocartella in /usr/sap/**QAS**/ERS**01**, provare a impostare il proprietario e il gruppo della cartella ERS**01** e riprovare.

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
   
   # Add the keep alive parameter, if using ENSA1
   enque/encni/set_so_keepalive = true
   </code></pre>

   Per ENSA1 e ENSA2, assicurarsi che i parametri del `keepalive` sistema operativo siano impostati come descritto nella nota SAP [1410736](https://launchpad.support.sap.com/#/notes/1410736).  

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

   <pre><code>
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

7. **[A]** Configurare gli utenti SAP dopo l'installazione

   <pre><code>
   # Add sidadm to the haclient group
   sudo usermod -aG haclient <b>qas</b>adm
   </code></pre>

8. **[1]** Aggiungere i servizi SAP ASCS e ERS al file `sapservice`

   Aggiungere la voce del servizio ASCS al secondo nodo e copiare la voce del servizio ERS nel primo nodo.

   <pre><code>
   cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>anftstsapcl2</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>anftstsapcl2</b> "cat /usr/sap/sapservices" | grep ERS<b>01</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

9. **[1]** Creare le risorse del cluster SAP

Se si usa l'architettura del server di accodamento 1 (ENSA1), definire le risorse nel modo seguente:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ERS<b>01</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ERS<b>01</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
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

   SAP ha introdotto il supporto per il server di accodamento 2, inclusa la replica, a partire da SAP NW 7.52. A partire dalla piattaforma ABAP 1809, il server di accodamento 2 viene installato per impostazione predefinita. Vedere la nota SAP [2630416](https://launchpad.support.sap.com/#/notes/2630416) per il supporto del server di accodamento 2.
Se si usa l'architettura del server di accodamento 2 ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), definire le risorse nel modo seguente:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ERS<b>01</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ERS<b>01</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>" AUTOMATIC_RECOVER=false IS_ERS=true
   
   sudo crm configure modgroup g-<b>QAS</b>_ASCS add rsc_sap_<b>QAS</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>QAS</b>_ERS add rsc_sap_<b>QAS</b>_ERS<b>01</b>
   
   sudo crm configure colocation col_sap_<b>QAS</b>_no_both -5000: g-<b>QAS</b>_ERS g-<b>QAS</b>_ASCS
   sudo crm configure order ord_sap_<b>QAS</b>_first_start_ascs Optional: rsc_sap_<b>QAS</b>_ASCS<b>00</b>:start rsc_sap_<b>QAS</b>_ERS<b>01</b>:stop symmetrical=false
   
   sudo crm node online <b>anftstsapcl1</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   Se si esegue l'aggiornamento da una versione precedente e si passa al server di accodamento 2, vedere la nota SAP [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   Assicurarsi che lo stato del cluster sia corretto e che tutte le risorse siano avviate. Non è importante il nodo su cui sono in esecuzione le risorse.

   <pre><code>sudo crm_mon -r
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      <b>Started anftstsapcl1</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl1</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:azure-lb):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl2</b>
   </code></pre>

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Preparazione del server applicazioni di SAP NetWeaver 

Alcuni database richiedono che l'installazione dell'istanza di database venga eseguita in un server applicazioni. Preparare le macchine virtuali del server applicazioni da poter usare in questi casi.

I passaggi seguenti presuppongono che il server applicazioni venga installato in un server diverso dai server ASCS/SCS e HANA. Diversamente, alcuni dei passaggi seguenti non sono necessari, ad esempio la configurazione della risoluzione dei nomi host.

Gli elementi seguenti sono preceduti dall'indicazione **[A]** , applicabile a PAS e AAS, **[P]** , applicabile solo a PAS o **[S]** , applicabile solo a AAS.


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

1. **[A]** Creare la directory sapmnt

   <pre><code>
   sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. **[P]** Creare la directory PAS

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>02</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>02</b>
   </code></pre>

1. **[S]** Creare la directory AAS

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>03</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>03</b>
   </code></pre>

1. **[P]** Configurare `autofs` su PAS

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Se si usa NFSv3, creare un nuovo file con:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind <b>10.1.0.4</b>:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=3,nobind <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>02</b> -nfsvers=3,nobind <b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>pas
   </code></pre>

   Se si usa NFSv4.1, creare un nuovo file con:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>02</b> -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>pas
   </code></pre>

   Riavviare `autofs` per montare le nuove condivisioni

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[P]** Configurare `autofs` su AAS

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Se si usa NFSv3, creare un nuovo file con:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind <b>10.1.0.4</b>:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=3,nobind <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>03</b> -nfsvers=3,nobind <b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>aas
   </code></pre>

   Se si usa NFSv4.1, creare un nuovo file con:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>03</b> -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>aas
   </code></pre>

   Riavviare `autofs` per montare le nuove condivisioni

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

   Installare l'istanza del database di SAP NetWeaver come radice usando un nome host virtuale mappato all'indirizzo IP della configurazione front-end di bilanciamento del carico per il database.

   È possibile usare il parametro sapinst SAPINST_REMOTE_ACCESS_USER per consentire a un utente non ROOT di connettersi a sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Installazione del server applicazioni di SAP NetWeaver

Per installare il server applicazioni SAP, seguire questi passaggi.

1. **[A]** Per preparare il server applicazioni. Seguire la procedura descritta nel capitolo [Preparazione del server applicazioni di SAP NetWeaver](high-availability-guide-suse-netapp-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) sopra riportato.

2. **[A]** Installare un server applicazioni di SAP NetWeaver. Installare un server applicazioni di SAP NetWeaver primario o aggiuntivo.

   È possibile usare il parametro sapinst SAPINST_REMOTE_ACCESS_USER per consentire a un utente non ROOT di connettersi a sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

3. **[A]** Aggiornare l'archivio sicuro di SAP HANA

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

   L'output mostra che l'indirizzo IP della voce predefinita fa riferimento alla macchina virtuale e non all'indirizzo IP del bilanciamento del carico. Questa voce deve essere modificata in modo che faccia riferimento al nome host virtuale del bilanciamento del carico. Verificare di usare la stessa porta (**30313** nell'output precedente) e lo stesso nome del database (**QAS** nell'output precedente).

   <pre><code>
   su - <b>qas</b>adm
   hdbuserstore SET DEFAULT <b>qasdb:30313@QAS</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>Testare la configurazione del cluster

I test seguenti sono una copia dei test case contenuti nelle [guide alle procedure consigliate di SUSE][suse-ha-guide]. Vengono copiati per comodità. Leggere sempre anche le guide alle procedure consigliate ed eseguire tutti i test aggiuntivi che possono essere stati aggiunti.

1. Test HAGetFailoverConfig, HACheckConfig e HACheckFailoverConfig

   Eseguire questi comandi come \<sapsid>adm nel nodo di cui è in esecuzione l'istanza di ASCS. Se i comandi hanno esito negativo con ERRORE: memoria insufficiente, la causa potrebbe essere la presenza di trattini nel nome host. Si tratta di un problema noto, che verrà risolto da SUSE nel pacchetto sap-suse-cluster-connector.

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
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rscsap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
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
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

3. Testare HAFailoverToNode

   Stato delle risorse prima dell'avvio del test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   Eseguire questi comandi come \<sapsid>adm per la migrazione dell'istanza ASCS.

   <pre><code>
   anftstsapcl1:qasadm 53> sapcontrol -nr 00 -host anftstsapvh -user <b>qas</b>adm &lt;password&gt; -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   # Remove migration constraints
   anftstsapcl1:~ # crm resource clear rsc_sap_QAS_ASCS00
   #INFO: Removed migration constraints for rsc_sap_QAS_ASCS00
   </code></pre>

   Stato delle risorse dopo il test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

4. Simulare l'arresto anomalo del nodo 

   Stato delle risorse prima dell'avvio del test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
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
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
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
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

5. Testare il riavvio manuale dell'istanza ASCS

   Stato delle risorse prima dell'avvio del test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Creare un blocco di accodamento, ad esempio modificando un utente nella transazione su01. Eseguire questi comandi come <sapsid\>adm nel nodo di cui è in esecuzione l'istanza di ASCS. I comandi interromperanno l'istanza ASCS per poi riavviarla. Se si usa l'architettura del server di accodamento 1, in questo test è prevista la perdita del blocco di accodamento. Se si usa l'architettura del server di accodamento 2, l'accodamento verrà mantenuto. 

   <pre><code>anftstsapcl2:qasadm 51> sapcontrol -nr 00 -function StopWait 600 2
   </code></pre>

   L'istanza ASCS dovrebbe essere ora disabilitata in Pacemaker

   <pre><code>  rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Stopped (disabled)
   </code></pre>

   Avviare l'istanza ASCS nuovamente nello stesso nodo.

   <pre><code>anftstsapcl2:qasadm 52> sapcontrol -nr 00 -function StartWait 600 2
   </code></pre>

   Il blocco di accodamento della transazione su01 andrà perso se si usa l'architettura della replica del server di accodamento 1 e il back-end verrà reimpostato. Stato delle risorse dopo il test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

6. Terminare il processo server messaggi

   Stato delle risorse prima dell'avvio del test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Eseguire i comandi seguenti come radice per identificare il processo del server messaggi e terminarlo.

   <pre><code>anftstsapcl2:~ # pgrep ms.sapQAS | xargs kill -9
   </code></pre>

   Terminando il server messaggi una sola volta, questo verrà riavviato da `sapstart`. Terminandolo un numero di volte sufficiente, Pacemaker sposterà infine l'istanza ASCS in un altro nodo. Eseguire i comandi seguenti come radice per pulire lo stato della risorsa dell'istanza ASCS ed ERS dopo il test.

   <pre><code>
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Stato delle risorse dopo il test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

7. Terminare il processo server di accodamento

   Stato delle risorse prima dell'avvio del test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl2
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
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

8. Terminare il processo del server di replica dell'accodamento

   Stato delle risorse prima dell'avvio del test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Eseguire i comandi seguenti come radice nel nodo in cui è in esecuzione l'istanza ERS per terminare il processo del server di replica dell'accodamento.

   <pre><code>anftstsapcl1:~ # pgrep er.sapQAS | xargs kill -9
   </code></pre>

   Se si esegue il comando una sola volta, `sapstart` riavvierà il processo. Se lo si esegue un numero di volte sufficiente, `sapstart` non riavvierà il processo e la risorsa sarà in stato arrestato. Eseguire i comandi seguenti come radice per pulire lo stato della risorsa dell'istanza ERS dopo il test.

   <pre><code>anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Stato delle risorse dopo il test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

9. Terminare il processo di accodamento sapstartsrv

   Stato delle risorse prima dell'avvio del test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
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
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

## <a name="next-steps"></a>Passaggi successivi

* [HA for SAP NW on Azure VMs on SLES for SAP applications multi-SID guide](./high-availability-guide-suse-multi-sid.md) (Guida a più SID per la disponibilità elevata per SAP NW in macchine virtuali di Azure su SLES per applicazioni SAP)
* [Guida alla pianificazione e all'implementazione di macchine virtuali di Azure per SAP][planning-guide]
* [Distribuzione di Macchine virtuali di Azure per SAP][deployment-guide]
* [Distribuzione DBMS di Macchine virtuali di Azure per SAP][dbms-guide]
* Per informazioni su come ottenere la disponibilità elevata e un piano di ripristino di emergenza di SAP HANA nelle macchine virtuali di Azure vedere [Disponibilità elevata di SAP HANA nelle macchine virtuali di Azure (VM)][sap-hana-ha]
