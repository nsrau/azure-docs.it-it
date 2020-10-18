---
title: Disponibilità elevata delle macchine virtuali di Azure per SAP NW in RHEL con Azure NetApp Files | Microsoft Docs
description: Stabilire la disponibilità elevata per SAP NW in macchine virtuali di Azure (VM) RHEL con Azure NetApp Files.
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
ms.date: 10/16/2020
ms.author: radeltch
ms.openlocfilehash: 14c5cd480f17e137965ae4bbf6700a36eb92cc3e
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2020
ms.locfileid: "92167832"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>Disponibilità elevata di macchine virtuali di Azure per SAP NetWeaver in Red Hat Enterprise Linux con Azure NetApp Files per le applicazioni SAP

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

Questo articolo descrive come distribuire le macchine virtuali, configurare le macchine virtuali, installare il framework del cluster e installare un sistema SAP NetWeaver 7.50 a disponibilità elevata mediante [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md).
Nelle configurazioni di esempio, i comandi di installazione e così via. L'istanza di ASC è il numero 00, l'istanza ERS è il numero 01, l'istanza dell'applicazione primaria (PAS) è 02 e l'istanza dell'applicazione (AAS) è 03. Viene usato l'ID del sistema SAP QAS. 

Il livello del database non è descritto in dettaglio in questo articolo.  

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
* [Pianificazione e implementazione di Macchine virtuali di Azure per SAP in Linux][planning-guide]
* [Distribuzione di Macchine virtuali di Azure per SAP in Linux][deployment-guide]
* [Distribuzione DBMS di Macchine virtuali di Azure per SAP in Linux][dbms-guide]
* [SAP NetWeaver nel cluster Pacemaker](https://access.redhat.com/articles/3150081)
* Documentazione generale di RHEL
  * [High Availability Add-On Overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index) (Panoramica dei componenti aggiuntivi a disponibilità elevata)
  * [High Availability Add-On Administration](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index) (Amministrazione dei componenti aggiuntivi a disponibilità elevata)
  * [High Availability Add-On Reference](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index) (Riferimento dei componenti aggiuntivi a disponibilità elevata)
  * [Configurazione di ASCS/ERS per SAP Netweaver con risorse autonome in RHEL 7.5](https://access.redhat.com/articles/3569681)
  * [Configurare SAP S/4HANA ASC/ERS con il server di Accodamento autonomo 2 (ENSA2) in pacemaker in RHEL ](https://access.redhat.com/articles/3974941)
* Documentazione di RHEL specifica di Azure:
  * [Support Policies for RHEL High Availability Clusters - Microsoft Azure Virtual Machines as Cluster Members](https://access.redhat.com/articles/3131341) (Criteri di supporto per cluster RHEL a disponibilità elevata - Macchine virtuali di Microsoft Azure come membri del cluster)
  * [Installing and Configuring a Red Hat Enterprise Linux 7.4 (and later) High-Availability Cluster on Microsoft Azure](https://access.redhat.com/articles/3252491) (Installazione e configurazione di un cluster Red Hat Enterprise Linux 7.4 e versioni successive a disponibilità elevata in Microsoft Azure)
* [Applicazioni NetApp SAP su Microsoft Azure con Azure NetApp Files][anf-sap-applications-azure]

## <a name="overview"></a>Panoramica

La disponibilità elevata per SAP NetWeaver Central Services richiede l'archiviazione condivisa.
A tale scopo, in Red Hat Linux finora era necessario compilare un cluster GlusterFS a disponibilità elevata separato. 

È ora possibile ottenere la disponibilità elevata di SAP NetWeaver usando l'archiviazione condivisa, distribuita in Azure NetApp Files. L'uso di Azure NetApp Files per l'archiviazione condivisa Elimina la necessità di un altro [cluster GlusterFS](./high-availability-guide-rhel-glusterfs.md). Pacemaker è ancora necessario per la disponibilità elevata di SAP Netweaver Central Services (ASCS/SCS).

![Panoramica della disponibilità elevata di SAP NetWeaver](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS e il database SAP HANA usano un nome host virtuale e indirizzi IP virtuali. Per usare un indirizzo IP virtuale in Azure, occorre il bilanciamento del carico. È consigliabile usare [Load Balancer Standard](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md). L'elenco seguente mostra la configurazione del servizio di bilanciamento del carico con IP front-end distinti per (A) SCS e ERS.

### <a name="ascs"></a>(A)SCS

* Configurazione front-end
  * Indirizzo IP 192.168.14.9
* Porta probe
  * Porta 620<strong>&lt;nr&gt;</strong>
* Regole di bilanciamento del carico
  * Se si usa Load Balancer Standard, selezionare **Porte a disponibilità elevata**
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
* Porta probe
  * Porta 621<strong>&lt;nr&gt;</strong>
* Regole di bilanciamento del carico
  * Se si usa Load Balancer Standard, selezionare **Porte a disponibilità elevata**
  * 32<strong>&lt;nr&gt;</strong> TCP
  * 33<strong>&lt;nr&gt;</strong> TCP
  * 5<strong>&lt;nr&gt;</strong>13 TCP
  * 5<strong>&lt;nr&gt;</strong>14 TCP
  * 5<strong>&lt;nr&gt;</strong>16 TCP

* Configurazione back-end
  * Connessione alle interfacce di rete primarie di tutte le macchine virtuali che devono far parte del cluster (A)SCS/ERS

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Configurazione dell'infrastruttura Azure NetApp Files 

SAP NetWeaver richiede un'archiviazione condivisa per la directory di trasporto e del profilo.  Prima di procedere con la configurazione dell'infrastruttura Azure NetApp Files, acquisire familiarità con la [documentazione di Azure NetApp Files][anf-azure-doc]. Controllare se l'area di Azure selezionata offre Azure NetApp Files. Il collegamento seguente mostra la disponibilità di Azure NetApp Files per area di Azure: [Disponibilità di Azure NetApp Files per area di Azure][anf-avail-matrix].

I file di Azure NetApp sono disponibili in diverse [aree di Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Prima di distribuire Azure NetApp Files, richiedere l'onboarding in Azure NetApp Files, seguendo le [istruzioni di registrazione per Azure NetApp Files][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Distribuire le risorse di Azure NetApp Files  

I passaggi presuppongono che sia già stata eseguita la distribuzione di [Rete virtuale di Azure](../../../virtual-network/virtual-networks-overview.md). Le risorse di Azure NetApp Files e tutte le macchine virtuali, in cui le risorse di Azure NetApp Files verranno montate, devono essere distribuite nella stessa istanza di Rete virtuale di Azure o in reti virtuali di Azure con peering.  

1. Se non è già stato fatto, richiedere l'[onboarding in Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md).  
2. Creare l'account NetApp nell'area di Azure selezionata, seguendo le [istruzioni per creare un account NetApp](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md).  
3. Configurare il pool di capacità di Azure NetApp Files, seguendo le [istruzioni su come configurare pool di capacità Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md).  
L'architettura di SAP NetWeaver presentata in questo articolo usa un singolo pool di capacità di Azure NetApp Files, SKU Premium. Lo SKU Premium di Azure NetApp Files è consigliabile per il carico di lavoro dell'applicazione SAP NetWeaver in Azure.  
4. Delegare una subnet ai file di Azure NetApp come descritto nelle [istruzioni per delegare una subnet a Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).  

5. Distribuire volumi di Azure NetApp Files, seguendo le [istruzioni per creare un volume per Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md). Distribuire i volumi nella [subnet](/rest/api/virtualnetwork/subnets) di Azure NetApp Files designata. Gli indirizzi IP dei volumi di Azure NetApp vengono assegnati automaticamente. Le risorse di Azure NetApp Files e le macchine virtuali di Azure devono trovarsi nella stessa istanza di Rete virtuale di Azure o in reti virtuali di Azure con peering. In questo esempio vengono usati due volumi Azure NetApp Files: SAP<b>QAS</b> e transSAP. I percorsi dei file montati nei punti di montaggio corrispondenti sono/usrsap<b>QAS</b>/sapmnt<b>QAS</b><b>,/usrsap QAS/usrsap QAS</b><b>sys e</b>così via.  

   1. volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/sapmnt<b>QAS</b>)
   2. volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>ASC)
   3. volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>sys)
   4. volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>ERS)
   5. volume transSAP (nfs://192.168.24.4/transSAP)
   6. volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>pas)
   7. volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>AAS)
  
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

## <a name="setting-up-ascs"></a>Configurazione di (A)SCS

In questo esempio, le risorse sono state distribuite manualmente tramite il [portale di Azure](https://portal.azure.com/#home).

### <a name="deploy-linux-manually-via-azure-portal"></a>Distribuire Linux manualmente tramite il portale di Azure

Per prima cosa è necessario creare i volumi Azure NetApp Files. Distribuire le macchine virtuali. Creare quindi un servizio di bilanciamento del carico e usare le macchine virtuali nei pool back-end.

1. Creare un bilanciamento del carico (interno, standard).  
   1. Creare gli indirizzi IP front-end
      1. Indirizzo IP 192.168.14.9 per ASC
         1. Aprire il servizio di bilanciamento del carico, selezionare Pool di indirizzi IP front-end e fare clic su Aggiungi
         1. Immettere il nome del nuovo pool di indirizzi IP front-end, ad esempio **frontend.QAS.ASCS**
         1. Impostare l'assegnazione su statico e immettere l'indirizzo IP (ad esempio **192.168.14.9**)
         1. Scegliere OK
      1. Indirizzo IP 192.168.14.10 per ASC ERS
         * Ripetere i passaggi precedenti in "a" per creare un indirizzo IP per ERS, ad esempio **192.168.14.10** e front- **end. QAS. ERS**)
   1. Creare il pool back-end
      1. Aprire il servizio di bilanciamento del carico, selezionare Pool back-end e fare clic su Aggiungi
      1. Immettere il nome del nuovo pool back-end, ad esempio **backend.QAS**
      1. Fare clic su Aggiungi una macchina virtuale.
      1. Selezionare macchina virtuale. 
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
      1. Regole di bilanciamento del carico per ASC
         1. Aprire il servizio di bilanciamento del carico, selezionare regole di bilanciamento del carico e fare clic su Aggiungi.
         1. Immettere il nome della nuova regola di bilanciamento del carico, ad esempio **lb.QAS.ASCS**
         1. Selezionare l'indirizzo IP front-end per ASCS, il pool back-end e il probe di integrità creati in precedenza, ad esempio **frontend.QAS.ASCS**, **backend.QAS** e **health.QAS.ASCS**
         1. Selezionare **Porte a disponibilità elevata**
         1. Aumentare il timeout di inattività a 30 minuti
         1. **Assicurarsi di abilitare l'indirizzo IP mobile**
         1. Fare clic su OK.
         * Ripetere i passaggi precedenti per creare regole di bilanciamento del carico per ERS, ad esempio **lb.QAS.ERS**
1. In alternativa, se lo scenario richiede Load Balancer Basic (interno), seguire questa procedura:  
   1. Creare gli indirizzi IP front-end
      1. Indirizzo IP 192.168.14.9 per ASC
         1. Aprire il servizio di bilanciamento del carico, selezionare Pool di indirizzi IP front-end e fare clic su Aggiungi
         1. Immettere il nome del nuovo pool di indirizzi IP front-end, ad esempio **frontend.QAS.ASCS**
         1. Impostare l'assegnazione su statico e immettere l'indirizzo IP (ad esempio **192.168.14.9**)
         1. Scegliere OK
      1. Indirizzo IP 192.168.14.10 per ASC ERS
         * Ripetere i passaggi precedenti in "a" per creare un indirizzo IP per ERS, ad esempio **192.168.14.10** e front- **end. QAS. ERS**)
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
         1. Aprire il servizio di bilanciamento del carico, selezionare regole di bilanciamento del carico e fare clic su Aggiungi.
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

      > [!IMPORTANT]
      > L'IP mobile non è supportato in una configurazione IP secondaria di NIC negli scenari di bilanciamento del carico. Per informazioni dettagliate, vedere limitazioni del servizio di [bilanciamento del carico di Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview#limitations). Se è necessario un indirizzo IP aggiuntivo per la macchina virtuale, distribuire una seconda scheda di interfaccia di rete.  

      > [!Note]
      > Se vengono inserite macchine virtuali senza indirizzi IP pubblici nel pool back-end di Load Balancer Standard interno ad Azure (nessun indirizzo IP pubblico), non sarà presente alcuna connettività Internet in uscita, a meno che non venga eseguita una configurazione aggiuntiva per consentire il routing a endpoint pubblici. Per informazioni dettagliate su come ottenere la connettività in uscita, vedere [Connettività degli endpoint pubblici per le macchine virtuali usando Load Balancer Standard di Azure negli scenari a disponibilità elevata SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

      > [!IMPORTANT]
      > Non abilitare i timestamp TCP nelle macchine virtuali di Azure che si trovano dietro Azure Load Balancer. Se si abilitano i timestamp TCP, i probe di integrità avranno esito negativo. Impostare il parametro **net.ipv4.tcp_timestamps** su **0**. Per informazioni dettagliate, vedere [Probe di integrità di Load Balancer](../../../load-balancer/load-balancer-custom-probe-overview.md).

## <a name="disable-id-mapping-if-using-nfsv41"></a>Disabilitare il mapping degli ID (se si usa NFSv4.1)

Le istruzioni riportate in questa sezione sono applicabili solo se si usano volumi Azure NetApp Files con il protocollo NFSv4.1. Eseguire la configurazione su tutte le macchine virtuali, in cui verranno montati volumi Azure NetApp Files NFSv4.1.  

1. Verificare l'impostazione del dominio NFS. Verificare che il dominio sia configurato come dominio di Azure NetApp Files predefinito, ad esempio **`defaultv4iddomain.com`** , e che il mapping sia impostato su **nobody** (nessuno).  

    > [!IMPORTANT]
    > Verificare di impostare il dominio NFS in `/etc/idmapd.conf` sulla macchina virtuale in modo che corrisponda alla configurazione del dominio predefinito in Azure NetApp Files: **`defaultv4iddomain.com`** . In caso di mancata corrispondenza tra la configurazione del dominio nel client NFS (ovvero la macchina virtuale) e nel server NFS, ad esempio la configurazione di Azure NetApp, le autorizzazioni per i file nei volumi Azure NetApp montati nelle VM verranno visualizzate come `nobody`.  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
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
    mount 192.168.24.5:/sap<b>QAS</b>
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   Per ulteriori informazioni su come modificare i `nfs4_disable_idmapping` parametri, vedere https://access.redhat.com/solutions/1749883 .

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

1. **[1]** Creare directory SAP nel volume Azure NetApp Files.  
   Montare temporaneamente il volume Azure NetApp Files in una delle macchine virtuali e creare le directory SAP (percorsi di file).  

    ```
     # mount temporarily the volume
     sudo mkdir -p /saptmp
     # If using NFSv3
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 192.168.24.5:/sapQAS /saptmp
     # If using NFSv4.1
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys,tcp 192.168.24.5:/sapQAS /saptmp
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

   Se si usa NFSv3:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Se si usa NFSv 4.1:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   > [!NOTE]
   > Assicurarsi di associare la versione del protocollo NFS dei volumi Azure NetApp Files, durante il montaggio dei volumi. Se i volumi Azure NetApp Files vengono creati come volumi NFSv3, usare la configurazione NFSv3 corrispondente. Se i volumi Azure NetApp Files vengono creati come volumi NFSv4.1, seguire le istruzioni per disabilitare il mapping degli ID e assicurarsi di usare la configurazione NFSv4.1 corrispondente. In questo esempio i volumi Azure NetApp Files sono stati creati come volumi NFSv3.  

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
   # If using NFSv3
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
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
   
   # If using NFSv3
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
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

   Se durante l'installazione non viene creata una sottocartella in /usr/sap/**QAS**/ERS**01**, provare a impostare il proprietario e il gruppo della cartella ERS**01** e riprovare.

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
   
   # Add the keep alive parameter, if using ENSA1
   enque/encni/set_so_keepalive = true
   ```

   Per ENSA1 e ENSA2, assicurarsi che i parametri del `keepalive` sistema operativo siano impostati come descritto nella nota SAP [1410736](https://launchpad.support.sap.com/#/notes/1410736).  

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

   Le comunicazioni tra il server applicazioni SAP NetWeaver e ASCS/SCS vengono instradate tramite un servizio di bilanciamento del carico software. Il servizio di bilanciamento del carico disconnette le connessioni inattive dopo un timeout configurabile. Per evitare questo problema, è necessario impostare un parametro nel profilo ASC/SCS di SAP NetWeaver e modificare le impostazioni di sistema di Linux. Per altre informazioni, leggere la [nota SAP 1410736][1410736].

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

   Se si usa l'architettura del server di accodamento 1 (ENSA1), definire le risorse nel modo seguente:

   ```
   sudo pcs property set maintenance-mode=true
   
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint location rsc_sap_QAS_ASCS00 rule score=2000 runs_ers_QAS eq 1
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   SAP ha introdotto il supporto per il server di accodamento 2, inclusa la replica, a partire da SAP NW 7.52. A partire dalla piattaforma ABAP 1809, il server di accodamento 2 viene installato per impostazione predefinita. Vedere la nota SAP [2630416](https://launchpad.support.sap.com/#/notes/2630416) per il supporto del server di accodamento 2.
   Se si usa l'architettura di Accodamento server 2 ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), installare Resource Agent Resource-Agents-SAP-4.1.1-12.EL7.x86_64 o versione successiva e definire le risorse come segue:

    ```
    sudo pcs property set maintenance-mode=true
    
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-QAS_ASCS then stop g-QAS_AERS symmetrical=false
   
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   Se si esegue l'aggiornamento da una versione precedente e si passa al server di Accodamento 2, vedere la nota SAP [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   > [!NOTE]
   > I timeout nella configurazione precedente sono solo esempi e possono essere adattati alla configurazione specifica di SAP. 

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

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Preparazione del server applicazioni di SAP NetWeaver

   Alcuni database richiedono che l'installazione dell'istanza di database venga eseguita in un server applicazioni. Preparare le macchine virtuali del server applicazioni da poter usare in questi casi.  

   I passaggi seguenti presuppongono che il server applicazioni venga installato in un server diverso dai server ASCS/SCS e HANA. Diversamente, alcuni dei passaggi seguenti non sono necessari, ad esempio la configurazione della risoluzione dei nomi host.  

   Gli elementi seguenti sono preceduti dall'indicazione **[A]** , applicabile a PAS e AAS, **[P]** , applicabile solo a PAS o **[S]** , applicabile solo a AAS.  

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
   Se si usa NFSv3:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Se si usa NFSv 4.1:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   Montare le nuove condivisioni

   ```
   sudo mount -a
   ```

1. **[P]** creare e montare la directory pas  
   Se si usa NFSv3:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   Se si usa NFSv 4.1:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[S]** creare e montare la directory AAS  
   Se si usa NFSv3:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   Se si usa NFSv 4.1:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
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

In questo esempio, SAP NetWeaver è installato in SAP HANA. Per questa installazione è possibile usare qualsiasi database supportato. Per altre informazioni su come installare SAP HANA in Azure, vedere [disponibilità elevata di SAP Hana in macchine virtuali di Azure in Red Hat Enterprise Linux][sap-hana-ha] . For a list of supported databases, see [SAP Note 1928533][1928533] .

1. Eseguire l'installazione dell'istanza del database SAP

   Installare l'istanza del database di SAP NetWeaver come radice usando un nome host virtuale mappato all'indirizzo IP della configurazione front-end di bilanciamento del carico per il database.

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

   Eseguire il comando seguente per elencare le voci come \<sapsid> adm

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

   L'output mostra che l'indirizzo IP della voce predefinita fa riferimento alla macchina virtuale e non all'indirizzo IP del bilanciamento del carico. Questa voce deve essere modificata in modo che faccia riferimento al nome host virtuale del bilanciamento del carico. Verificare di usare la stessa porta (**30313** nell'output precedente) e lo stesso nome del database (**QAS** nell'output precedente).

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

   Terminando il server messaggi una sola volta, questo verrà riavviato da `sapstart`. Terminandolo un numero di volte sufficiente, Pacemaker sposterà infine l'istanza ASCS in un altro nodo. Eseguire i comandi seguenti come radice per pulire lo stato della risorsa dell'istanza ASCS ed ERS dopo il test.

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

   Se si esegue il comando una sola volta, `sapstart` riavvierà il processo. Se lo si esegue un numero di volte sufficiente, `sapstart` non riavvierà il processo e la risorsa sarà in stato arrestato. Eseguire i comandi seguenti come radice per pulire lo stato della risorsa dell'istanza ERS dopo il test.

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

* [Guida al multi-SID per la disponibilità elevata per SAP NW in macchine virtuali di Azure su RHEL per applicazioni SAP](./high-availability-guide-rhel-multi-sid.md)
* [Guida alla pianificazione e all'implementazione di macchine virtuali di Azure per SAP][planning-guide]
* [Distribuzione di Macchine virtuali di Azure per SAP][deployment-guide]
* [Distribuzione DBMS di Macchine virtuali di Azure per SAP][dbms-guide]
* Per informazioni su come stabilire la disponibilità elevata e pianificare il ripristino di emergenza di SAP HANA in Azure (istanze di grandi dimensioni), vedere [disponibilità elevata e ripristino di emergenza di SAP Hana (istanze large) in Azure](hana-overview-high-availability-disaster-recovery.md).
* Per informazioni su come ottenere la disponibilità elevata e un piano di ripristino di emergenza di SAP HANA nelle macchine virtuali di Azure vedere [Disponibilità elevata di SAP HANA nelle macchine virtuali di Azure (VM)][sap-hana-ha]
