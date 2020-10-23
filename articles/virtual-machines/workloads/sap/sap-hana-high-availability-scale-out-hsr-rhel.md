---
title: SAP HANA la scalabilità orizzontale con HSR e pacemaker in RHEL | Microsoft Docs
description: SAP HANA la scalabilità orizzontale con HSR e pacemaker in RHEL
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
ms.date: 10/16/2020
ms.author: radeltch
ms.openlocfilehash: 520a7649942fc5186d32020853b98297ef8b34d7
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92152117"
---
# <a name="high-availability-of-sap-hana-scale-out-system-on-red-hat-enterprise-linux"></a>Disponibilità elevata del sistema di SAP HANA con scalabilità orizzontale in Red Hat Enterprise Linux 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
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
[1900823]:https://launchpad.support.sap.com/#/notes/1900823
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2593824]:https://launchpad.support.sap.com/#/notes/2593824
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


Questo articolo descrive come distribuire un sistema di SAP HANA a disponibilità elevata in una configurazione con scalabilità orizzontale con HANA System Replication (HSR) e pacemaker in Azure Red Hat Enterprise Linux macchine virtuali (VM). I file system condivisi nell'architettura presentata sono forniti da [Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-introduction.md) e sono montati su NFS.  

Nelle configurazioni di esempio, nei comandi di installazione e così via, l'istanza di HANA è **03** e l'ID di sistema Hana è **HN1**. Gli esempi sono basati su HANA 2,0 SP4 e Red Hat Enterprise Linux per SAP 7,6. 

Prima di iniziare, vedere le note e i documenti SAP seguenti:

* La nota SAP [1928533] include:  
  * Elenco delle dimensioni delle VM di Azure supportate per la distribuzione del software SAP
  * Importanti informazioni sulla capacità per le dimensioni delle VM di Azure
  * Software SAP e combinazioni di sistemi operativi e database supportati
  * La versione del kernel SAP richiesta per Windows e Linux in Microsoft Azure
* Nota SAP [2015553]: elenca i prerequisiti per le distribuzioni del software SAP supportate da SAP in Azure
* Nota SAP [2002167] con le impostazioni del sistema operativo consigliate per Red Hat Enterprise Linux
* La nota SAP [2009879] contiene le linee guida di SAP HANA per Red Hat Enterprise Linux
* Nota SAP [2178632]: contiene informazioni dettagliate su tutte le metriche di monitoraggio segnalate per SAP in Azure
* Nota SAP [2191498]: contiene la versione richiesta dell'agente host SAP per Linux in Azure
* Nota SAP [2243692]: contiene informazioni sulle licenze SAP in Linux in Azure
* Nota SAP [1999351]: contiene informazioni aggiuntive sulla risoluzione dei problemi per l'estensione di monitoraggio avanzato di Azure per SAP
* Nota SAP [1900823]: contiene informazioni sui requisiti di archiviazione SAP Hana
* [Wiki della community SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): contiene tutte le note SAP necessarie per Linux
* [Pianificazione e implementazione di Macchine virtuali di Azure per SAP in Linux][planning-guide]
* [Distribuzione di Macchine virtuali di Azure per SAP in Linux][deployment-guide]
* [Distribuzione DBMS di Macchine virtuali di Azure per SAP in Linux][dbms-guide]
* [Requisiti di rete SAP HANA](https://www.sap.com/documents/2016/08/1cd2c2fb-807c-0010-82c7-eda71af511fa.html)
* Documentazione generale di RHEL
  * [High Availability Add-On Overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index) (Panoramica dei componenti aggiuntivi a disponibilità elevata)
  * [High Availability Add-On Administration](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index) (Amministrazione dei componenti aggiuntivi a disponibilità elevata)
  * [High Availability Add-On Reference](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index) (Riferimento dei componenti aggiuntivi a disponibilità elevata)
  * [Guida alla rete Red Hat Enterprise Linux](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide)
  * [Ricerca per categorie configurare SAP HANA la replica di sistema Scale-Out in un cluster Pacemaker con file System HANA in condivisioni NFS](https://access.redhat.com/solutions/5423971)
* Documentazione di RHEL specifica di Azure:
  * [Install SAP HANA on Red Hat Enterprise Linux for Use in Microsoft Azure](https://access.redhat.com/public-cloud/microsoft-azure) (Installare SAP HANA su Red Hat Enterprise Linux per l'uso in Microsoft Azure)
  * [Soluzione Red Hat Enterprise Linux per SAP HANA Scale-Out e la replica di sistema](https://access.redhat.com/solutions/4386601)
* [Applicazioni NetApp SAP su Microsoft Azure con Azure NetApp Files][anf-sap-applications-azure]
* [Documentazione di Azure NetApp Files][anf-azure-doc] 


## <a name="overview"></a>Panoramica

Un metodo per ottenere la disponibilità elevata HANA per le installazioni con scalabilità orizzontale di HANA consiste nel configurare la replica di sistema HANA e proteggere la soluzione con cluster pacemaker per consentire il failover automatico. Quando si verifica un errore in un nodo attivo, il cluster esegue il failover delle risorse HANA sull'altro sito.  
La configurazione presentata mostra tre nodi HANA in ogni sito, oltre al nodo del creatore di maggioranza per prevenire lo scenario Split Brain. Le istruzioni possono essere adattate per includere più macchine virtuali come nodi del database HANA.  

La file system condivisa HANA `/hana/shared` nell'architettura presentata viene fornita da [Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-introduction.md). Viene montata tramite NFSv 4.1 in ogni nodo HANA nello stesso sito di replica di sistema HANA. I file System `/hana/data` e `/hana/log` sono file system locali e non sono condivisi tra i nodi del database Hana. SAP HANA verrà installato in modalità non condivisa. 

> [!TIP]
> Per le configurazioni di archiviazione SAP HANA consigliate, vedere [SAP Hana configurazioni di archiviazione delle macchine virtuali di Azure](./hana-vm-operations-storage.md).   

[![SAP HANA con scalabilità orizzontale con cluster HSR e pacemaker](./media/sap-hana-high-availability-rhel/sap-hana-high-availability-scale-out-hsr-rhel.png)](./media/sap-hana-high-availability-rhel/sap-hana-high-availability-scale-out-hsr-rhel-detail.png#lightbox)

Nel diagramma precedente, tre subnet sono rappresentate all'interno di una rete virtuale di Azure, seguendo le raccomandazioni della rete SAP HANA: 
* per la comunicazione client- `client` 10.23.0.0/24  
* per la comunicazione tra nodi HANA interna- `inter` 10.23.1.128/26  
* per la replica di sistema HANA- `hsr` 10.23.1.192/26  

Quando `/hana/data` e `/hana/log` vengono distribuiti nei dischi locali, non è necessario distribuire una subnet separata e schede di rete virtuali separate per la comunicazione con l'archiviazione.  

I volumi di Azure NetApp vengono distribuiti in una subnet separata, [delegata a Azure NetApp Files] ( https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet: `anf` 10.23.1.0/26.   

## <a name="set-up-the-infrastructure"></a>Configurare l'infrastruttura

Nelle istruzioni seguenti si presuppone che sia già stato creato il gruppo di risorse, la rete virtuale di Azure con tre subnet di rete di Azure: `client` `inter` e `hsr` .

### <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Distribuire macchine virtuali Linux tramite il portale di Azure
1. Distribuire le macchine virtuali di Azure.  
Per la configurazione presentata in questo documento, distribuire sette macchine virtuali: 
   - tre macchine virtuali da usare come nodi del database HANA per il sito di replica HANA 1: **Hana-S1-DB1**, **Hana-S1-DB2** e **Hana-S1-DB3**  
   - tre macchine virtuali da usare come nodi del database HANA per la replica HANA sito 2: **Hana-S2-DB1**, **Hana-S2-DB2** e **Hana-S2-DB3**  
   - una macchina virtuale di piccole dimensioni che funge da *creatore della maggioranza*: **Hana-s-mm**

   Le macchine virtuali, distribuite come nodi di SAP DB HANA, devono essere certificate da SAP per HANA come pubblicate nella [directory dell'Hardware SAP Hana](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Quando si distribuiscono i nodi del database HANA, assicurarsi che sia selezionata l'opzione [rete accelerata](../../../virtual-network/create-vm-accelerated-networking-cli.md) .  
  
   Per il nodo creatore della maggioranza, è possibile distribuire una macchina virtuale di piccole dimensioni perché questa macchina virtuale non esegue alcuna delle risorse SAP HANA. La VM di maggioranza viene usata nella configurazione del cluster per ottenere un numero dispari di nodi del cluster in uno scenario Split Brain. In questo esempio la macchina virtuale di maggioranza Maker necessita solo di un'interfaccia di rete virtuale nella `client` subnet.        

   Distribuire dischi gestiti locali per `/hana/data` e `/hana/log` . La configurazione di archiviazione minima consigliata per `/hana/data` ed `/hana/log` è descritta in [SAP Hana configurazioni di archiviazione delle macchine virtuali di Azure](./hana-vm-operations-storage.md).

   Distribuire l'interfaccia di rete primaria per ogni macchina virtuale nella `client` subnet della rete virtuale.  
   Quando la macchina virtuale viene distribuita tramite portale di Azure, viene generato automaticamente il nome dell'interfaccia di rete. In queste istruzioni per semplicità si farà riferimento alle interfacce di rete primarie generate automaticamente, che sono collegate alla `client` subnet della rete virtuale di Azure come **Hana-S1-DB1-client**, **Hana-S1-DB2-client**, **Hana-S1-DB3-client**e così via.  


   > [!IMPORTANT]
   > Verificare che il sistema operativo selezionato sia certificato per SAP per SAP HANA sui tipi di VM specifici in uso. Per un elenco dei tipi di VM SAP HANA certificati e delle versioni del sistema operativo per questi tipi, accedere al sito [SAP Hana piattaforme IaaS certificate](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) . Fare clic sui dettagli del tipo di macchina virtuale elencato per ottenere l'elenco completo delle versioni del sistema operativo supportate da SAP HANA per quel tipo.  
  

2. Creare sei interfacce di rete, una per ogni macchina virtuale del database HANA, nella `inter` subnet della rete virtuale (in questo esempio, **Hana-S1-DB1-Inter**, **Hana-S1-DB2-Inter**, **Hana-S1-DB3-Inter**, **Hana-S2-DB1-Inter**, **Hana-S2-DB2-Inter**e **Hana-S2-DB3-Inter**).  

3. Creare sei interfacce di rete, una per ogni macchina virtuale del database HANA, nella `hsr` subnet della rete virtuale (in questo esempio, **Hana-S1-DB1-HSR**, **Hana-S1-DB2-HSR**, **Hana-S1-DB3-HSR**, **Hana-S2-DB1-HSR**, **Hana-S2-DB2-HSR**e **Hana-S2-DB3-HSR**).  

4. Alleghi le interfacce di rete virtuale appena create alle macchine virtuali corrispondenti:  

    a. Passare alla macchina virtuale nella [portale di Azure](https://portal.azure.com/#home).  

    b. Nel riquadro sinistro selezionare **macchine virtuali**. Filtrare sul nome della macchina virtuale (ad esempio, **Hana-S1-DB1**), quindi selezionare la macchina virtuale.  

    c. Nel riquadro **Panoramica** selezionare **Arresta** per deallocare la macchina virtuale.  

    d. Selezionare **rete**e quindi collegare l'interfaccia di rete. Nell'elenco a discesa **Connetti interfaccia di rete** selezionare le interfacce di rete già create per le `inter` subnet e `hsr` .  
    
    e. Selezionare **Salva**. 
 
    f. Ripetere i passaggi da b a e per le macchine virtuali rimanenti (in questo esempio,  **Hana-S1-DB2**, **Hana-S1-DB3**, **Hana-S2-DB1**, **Hana-S2-DB2** e **Hana-S2-DB3**).
 
    g. Per il momento, lasciare le macchine virtuali in stato di arresto. Successivamente, verrà abilitata la [rete accelerata](../../../virtual-network/create-vm-accelerated-networking-cli.md) per tutte le interfacce di rete appena collegate.  

5. Abilitare la rete accelerata per le interfacce di rete aggiuntive per le `inter` subnet e seguendo `hsr` questa procedura:  

    a. Aprire [Azure cloud Shell](https://azure.microsoft.com/features/cloud-shell/) nel [portale di Azure](https://portal.azure.com/#home).  

    b. Eseguire i comandi seguenti per abilitare la rete accelerata per le interfacce di rete aggiuntive, che sono collegate `inter` alle `hsr` subnet e.  

    ```
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-inter --accelerated-networking true
    
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-hsr --accelerated-networking true
    ```

7. Avviare le macchine virtuali del database HANA

### <a name="deploy-azure-load-balancer"></a>Distribuisci Azure Load Balancer

1. È consigliabile usare Load Balancer standard. Per distribuire Load Balancer standard, seguire questa procedura di configurazione:
   1. Prima, creare il pool di indirizzi IP front-end:

      1. Aprire il servizio di bilanciamento del carico, selezionare **Pool di indirizzi IP front-end** e quindi **Aggiungi**.
      1. Immettere il nome del nuovo pool di indirizzi IP front-end (ad esempio, **hana-frontend**).
      1. Impostare l' **assegnazione** su **statico** e immettere l'indirizzo IP (ad esempio, **10.23.0.18**).
      1. Selezionare **OK**.
      1. Dopo aver creato il nuovo pool di indirizzi IP front-end, annotare l'indirizzo IP del pool.

   1. Successivamente, creare un pool back-end e aggiungere tutte le macchine virtuali del cluster al pool back-end:

      1. Aprire il servizio di bilanciamento del carico, selezionare **Pool back-end** e quindi **Aggiungi**.
      1. Immettere il nome del nuovo pool back-end (ad esempio, **hana-backend**).
      1. Selezionare **Aggiungi una macchina virtuale**.
      1. Selezionare **Macchina virtuale**.
      1. Selezionare le macchine virtuali del cluster SAP HANA e i relativi indirizzi IP per la `client` subnet.
      1. Selezionare **Aggiungi**.

   1. Creare quindi un probe di integrità:

      1. Aprire il servizio di bilanciamento del carico, selezionare **Probe integrità** e quindi **Aggiungi**.
      1. Immettere il nome del nuovo probe di integrità (ad esempio, **hana-hp**).
      1. Selezionare **TCP** come protocollo e la porta 625**03**. Lasciare il valore di **Intervallo** impostato su 5 e il valore di **Soglia di non integrità** impostato su 2.
      1. Selezionare **OK**.

   1. Successivamente, creare le regole del servizio di bilanciamento del carico:
   
      1. Aprire il servizio di bilanciamento del carico, selezionare **Regole di bilanciamento del carico** e quindi **Aggiungi**.
      1. Immettere il nome della nuova regola di bilanciamento del carico (ad esempio, **hana-lb**).
      1. Selezionare l'indirizzo IP front-end, il pool back-end e il probe di integrità creati in precedenza (ad esempio, **hana-frontend**, **hana-backend** e **hana-hp**).
      1. Selezionare **Porte a disponibilità elevata**.
      1. Aumentare il valore di **Timeout di inattività** a 30 minuti.
      1. Assicurarsi di selezionare **Abilita l'indirizzo IP mobile**.
      1. Selezionare **OK**.

   > [!IMPORTANT]
   > L'IP mobile non è supportato in una configurazione IP secondaria di NIC negli scenari di bilanciamento del carico. Per informazioni dettagliate, vedere limitazioni del servizio di [bilanciamento del carico di Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview#limitations). Se è necessario un indirizzo IP aggiuntivo per la macchina virtuale, distribuire una seconda scheda di interfaccia di rete.    
   
   > [!Note]
   > Se vengono inserite macchine virtuali senza indirizzi IP pubblici nel pool back-end di Load Balancer Standard interno ad Azure (nessun indirizzo IP pubblico), non sarà presente alcuna connettività Internet in uscita, a meno che non venga eseguita una configurazione aggiuntiva per consentire il routing a endpoint pubblici. Per informazioni dettagliate su come ottenere la connettività in uscita, vedere [Connettività degli endpoint pubblici per le macchine virtuali usando Load Balancer Standard di Azure negli scenari a disponibilità elevata SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md).  


   > [!IMPORTANT]
   > Non abilitare i timestamp TCP nelle macchine virtuali di Azure che si trovano dietro Azure Load Balancer. Se si abilitano i timestamp TCP, i probe di integrità avranno esito negativo. Impostare il parametro **net.ipv4.tcp_timestamps** su **0**. Per informazioni dettagliate, vedere [Probe di integrità di Load Balancer](../../../load-balancer/load-balancer-custom-probe-overview.md).
   > Vedere anche la nota SAP [2382421](https://launchpad.support.sap.com/#/notes/2382421).  

### <a name="deploy-the-azure-netapp-files-infrastructure"></a>Distribuire l'infrastruttura Azure NetApp Files 

Distribuire i volumi e per la `/hana/shared` file System. Sarà necessario un volume separato `/hana/shared` per ogni sito di replica di sistema Hana. Per altre informazioni, vedere [configurare l'infrastruttura Azure NetApp files](./sap-hana-scale-out-standby-netapp-files-rhel.md#set-up-the-azure-netapp-files-infrastructure).

In questo esempio sono stati usati i volumi Azure NetApp Files seguenti: 

* volume **HN1**-Shared-s1 (NFS://10.23.1.7/**HN1**-Shared-S1)
* volume **HN1**-Shared-s2 (NFS://10.23.1.7/**HN1**-Shared-S2)

## <a name="operating-system-configuration-and-preparation"></a>Configurazione e preparazione del sistema operativo

Le istruzioni riportate nelle sezioni successive sono precedute da una delle abbreviazioni seguenti:
* **[A]**: applicabile a tutti i nodi
* **[Ah]**: applicabile a tutti i nodi del database Hana
* **[M]**: applicabile al nodo del produttore della maggioranza
* **[AH1]**: applicabile a tutti i nodi del database Hana nel sito 1
* **[AH2]**: applicabile a tutti i nodi del database Hana nel sito 2
* **[1]**: applicabile solo al nodo 1 del database Hana, sito 1
* **[2]**: applicabile solo al nodo 1 del database Hana, sito 2


Configurare e preparare il sistema operativo seguendo questa procedura:

1. **[A]** gestire i file host nelle macchine virtuali. Includere le voci per tutte le subnet. Per questo esempio sono state aggiunte le voci seguenti `/etc/hosts` .  

    ```
     # Client subnet
     10.23.0.11 hana-s1-db1
     10.23.0.12 hana-s1-db1
     10.23.0.13 hana-s1-db2
     10.23.0.14 hana-s2-db1
     10.23.0.15 hana-s2-db2
     10.23.0.16 hana-s2-db3
     10.23.0.17 hana-s-mm
     # Internode subnet
     10.23.1.138 hana-s1-db1-inter
     10.23.1.139 hana-s1-db2-inter
     10.23.1.140 hana-s1-db3-inter
     10.23.1.141 hana-s2-db1-inter
     10.23.1.142 hana-s2-db2-inter
     10.23.1.143 hana-s2-db3-inter
     # HSR subnet
     10.23.1.202 hana-s1-db1-hsr
     10.23.1.203 hana-s1-db2-hsr
     10.23.1.204 hana-s1-db3-hsr
     10.23.1.205 hana-s2-db1-hsr
     10.23.1.206 hana-s2-db2-hsr
     10.23.1.207 hana-s2-db3-hsr
    ```


2. **[A]** installare il pacchetto client NFS.  

    ```yum install nfs-utils ```


3. **[Ah]** Red Hat per la configurazione HANA.  

    Configurare RHEL come descritto in <https://access.redhat.com/solutions/2447641> e nelle note SAP seguenti:  
   - [2292690 - SAP HANA DB: Recommended OS settings for RHEL 7](https://launchpad.support.sap.com/#/notes/2292690) (2292690 - SAP HANA DB: impostazioni del sistema operativo consigliate per RHEL 7)
   - [2777782-SAP HANA DB: impostazioni del sistema operativo consigliate per RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582-Linux: esecuzione di applicazioni SAP compilate con GCC 6. x](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824-Linux: esecuzione di applicazioni SAP compilate con GCC 7. x](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607-Linux: esecuzione di applicazioni SAP compilate con GCC 9. x](https://launchpad.support.sap.com/#/notes/2886607)

## <a name="prepare-the-file-systems"></a>Preparare il file System
### <a name="mount-the-shared-file-systems"></a>Montare i file system condivisi

In questo esempio, i file System HANA condivisi vengono distribuiti in Azure NetApp Files e montati tramite NFSv4.  

1. **[Ah]** Creare punti di montaggio per i volumi del database HANA.  

    ```
    mkdir -p /hana/shared
    ```

2. **[Ah]** Verificare l'impostazione del dominio NFS. Verificare che il dominio sia configurato come predefinito Azure NetApp Files dominio, ovvero che **`defaultv4iddomain.com`** il mapping sia impostato su **nessuno**.  
   Questo passaggio è necessario solo se si usa Azure NetAppFiles NFSv 4.1.  

    > [!IMPORTANT]
    > Verificare di impostare il dominio NFS in `/etc/idmapd.conf` sulla macchina virtuale in modo che corrisponda alla configurazione del dominio predefinito in Azure NetApp Files: **`defaultv4iddomain.com`** . In caso di mancata corrispondenza tra la configurazione del dominio nel client NFS (ovvero la macchina virtuale) e nel server NFS, ad esempio la configurazione di Azure NetApp, le autorizzazioni per i file nei volumi Azure NetApp montati nelle VM verranno visualizzate come `nobody`.  

    ```
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

3. **[Ah]** Verificare `nfs4_disable_idmapping` . Il valore deve essere impostato su **Y**. Per creare la struttura di directory in cui si trova `nfs4_disable_idmapping`, eseguire il comando mount. Non sarà possibile creare manualmente la directory in/sys/modules, perché l'accesso è riservato per il kernel/driver.  
   Questo passaggio è necessario solo se si usa Azure NetAppFiles NFSv 4.1.  

    ```
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.9.0.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

   Per ulteriori informazioni su come modificare i `nfs4_disable_idmapping` parametri, vedere https://access.redhat.com/solutions/1749883 .

4. **[AH1]** Montare i volumi di Azure NetApp Files condivisi nelle VM di database Microsoft1 HANA.  

    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.23.1.7:/HN1-shared-s1 /hana/shared
    ```

5. **[AH2]** Montare i volumi di Azure NetApp Files condivisi nelle VM di database SITE2 HANA.  

    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.23.1.7:/HN1-shared-s2 /hana/shared
    ```


10. **[Ah]** Verificare che i `/hana/shared/` file system corrispondenti siano montati in tutte le VM del database Hana con il protocollo NFS versione **NFSv4**.  

    ```
    sudo nfsstat -m
    # Verify that flag vers is set to 4.1 
    # Example from SITE 1, hana-s1-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s1
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.11,local_lock=none,addr=10.23.1.7
    # Example from SITE 2, hana-s2-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s2
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.14,local_lock=none,addr=10.23.1.7
    ```

### <a name="prepare-the-data-and-log-local-file-systems"></a>Preparare i dati e registrare i file system locali
Nella configurazione presentata, i file System `/hana/data` e `/hana/log` vengono distribuiti in un disco gestito e sono collegati localmente a ogni macchina virtuale del database Hana. È necessario eseguire i passaggi per creare i volumi di dati e di log locali in ogni macchina virtuale HANA DB. 

Configurare il layout del disco con  **Logical Volume Manager (LVM)**. Nell'esempio seguente si presuppone che a ogni macchina virtuale HANA siano collegati tre dischi dati, usati per creare due volumi.

1. **[Ah]** Elencare tutti i dischi disponibili:
    ```
    ls /dev/disk/azure/scsi1/lun*
    ```

   Output di esempio:

    ```
    /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2 
    ```

2. **[Ah]** Creare volumi fisici per tutti i dischi che si vuole usare:
    ```
    sudo pvcreate /dev/disk/azure/scsi1/lun0
    sudo pvcreate /dev/disk/azure/scsi1/lun1
    sudo pvcreate /dev/disk/azure/scsi1/lun2
    ```

3. **[Ah]** Creare un gruppo di volumi per i file di dati. Usare un gruppo di volumi per i file di log e uno per la directory condivisa di SAP HANA:
    ```
    sudo vgcreate vg_hana_data_HN1 /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
    sudo vgcreate vg_hana_log_HN1 /dev/disk/azure/scsi1/lun2
    ```

4. **[Ah]** Creare i volumi logici. 
   Quando si usa `lvcreate` senza l'opzione `-i`, viene creato un volume lineare. Si consiglia di creare un volume con striping per migliorare le prestazioni di I/O e allineare le dimensioni di striping ai valori documentati in [Configurazioni di archiviazione della macchina virtuale SAP HANA](./hana-vm-operations-storage.md). L'argomento `-i` deve essere il numero dei volumi fisici sottostanti e l'argomento `-I` è la dimensione della striscia. In questo documento vengono usati due volumi fisici per il volume di dati, quindi l'argomento dell'opzione `-i` è impostato su **2**. La dimensione di striping per il volume di dati è **256 KiB**. Un volume fisico viene usato per il volume di log, quindi non viene usata alcuna opzione `-i` o `-I` in modo esplicito per i comandi del volume di log.  

   > [!IMPORTANT]
   > Usare l' `-i` opzione e impostarla sul numero del volume fisico sottostante quando si usa più di un volume fisico per ogni volume di dati o di log. Usare l'opzione `-I` per specificare le dimensioni di striping durante la creazione di un volume con striping.  
   > Vedere [Configurazioni di archiviazione della macchina virtuale SAP HANA](./hana-vm-operations-storage.md) per le configurazioni di archiviazione consigliate, incluse le dimensioni di striping e il numero di dischi.  

    ```
    sudo lvcreate -i 2 -I 256 -l 100%FREE -n hana_data vg_hana_data_HN1
    sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_HN1
    sudo mkfs.xfs /dev/vg_hana_data_HN1/hana_data
    sudo mkfs.xfs /dev/vg_hana_log_HN1/hana_log
    ```

5. **[Ah]** Creare le directory di montaggio e copiare l'UUID di tutti i volumi logici:
    ```
    sudo mkdir -p /hana/data/HN1
    sudo mkdir -p /hana/log/HN1
    # Write down the ID of /dev/vg_hana_data_HN1/hana_data and /dev/vg_hana_log_HN1/hana_log
    sudo blkid
    ```

6. **[Ah]** Creare `fstab` voci per i volumi logici e montare:
    ```
    sudo vi /etc/fstab
    ```

   Inserire la riga seguente nel file `/etc/fstab`:

    ```
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_data_HN1-hana_data /hana/data/HN1 xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_log_HN1-hana_log /hana/log/HN1 xfs  defaults,nofail  0  2
    ```

   Montare i nuovi volumi:

    ```
    sudo mount -a
    ```

## <a name="installation"></a>Installazione  

In questo esempio per la distribuzione di SAP HANA nella configurazione con scalabilità orizzontale con HSR in macchine virtuali di Azure, è stato usato HANA 2,0 SP4.  

### <a name="prepare-for-hana-installation"></a>Preparare l'installazione di HANA

1. **[Ah]** Prima dell'installazione di HANA, impostare la password radice. È possibile disabilitare la password radice dopo che l'installazione è stata completata. Comando Execute `root` As `passwd` .  

2. **[1, 2]** modificare le autorizzazioni in `/hana/shared` 
    ```
    chmod 775 /hana/shared
    ```

3. **[1]** verificare che sia possibile accedere tramite SSH alle VM del database Hana in questo sito **Hana-S1-DB2** e **Hana-S1-DB3**, senza che venga richiesta una password.  
   In caso contrario, scambiare le chiavi SSH, come documentato in [uso dell'autenticazione basata su chiavi](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/s2-ssh-configuration-keypairs).  
    ```
    ssh root@hana-s1-db2
    ssh root@hana-s1-db3
    ```

4. **[2]** verificare che sia possibile accedere tramite SSH alle VM del database Hana in questo sito **Hana-S2-DB2** e **Hana-S2-DB3**, senza che venga richiesta una password.  
   In caso contrario, scambiare le chiavi SSH, come documentato in [uso dell'autenticazione basata su chiavi](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/s2-ssh-configuration-keypairs).  
    ```
    ssh root@hana-s2-db2
    ssh root@hana-s2-db3
    ```

5. **[Ah]** Installare pacchetti aggiuntivi, necessari per HANA 2,0 SP4. Per ulteriori informazioni, vedere la nota SAP [2593824](https://launchpad.support.sap.com/#/notes/2593824) per RHEL 7. 

    ```
    # If using RHEL 7
    yum install libgcc_s1 libstdc++6 compat-sap-c++-7 libatomic1
    # If using RHEL 8
    yum install libatomic libtool-ltdl.x86_64
    ```


6. **[A]** disabilitare temporaneamente il firewall, in modo che non interferisca con l'installazione di Hana. È possibile riabilitarla, al termine dell'installazione di HANA. 
    ```
    # Execute as root
    systemctl stop firewalld
    systemctl disable firewalld
    ```

### <a name="hana-installation-on-the-first-node-on-each-site"></a>Installazione di HANA nel primo nodo in ogni sito

1. **[1]** installare SAP Hana seguendo le istruzioni riportate nella [Guida all'installazione e all'aggiornamento di SAP Hana 2,0](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html). Nelle istruzioni seguenti viene illustrata l'installazione di SAP HANA nel primo nodo nel sito 1.   

   a. Avviare il programma **hdblcm** come `root` dalla directory del software di installazione di Hana. Usare il `internal_network` parametro e passare lo spazio degli indirizzi per la subnet, che viene usato per la comunicazione tra nodi Hana interna.  

    ```
    ./hdblcm --internal_network=10.23.1.128/26
    ```

   b. Al prompt dei comandi, immettere i valori seguenti:

     * Per **scegliere un'azione**: immettere **1** (per l'installazione)
     * Per **ulteriori componenti per l'installazione**: immettere **2, 3**
     * Per percorso di installazione: premere INVIO (il valore predefinito è/Hana/Shared)
     * Per **nome host locale**: premere INVIO per accettare il valore predefinito
     * Per **aggiungere host al sistema?**: immettere **n**
     * Per **SAP Hana ID sistema**: immettere **HN1**
     * Per **numero di istanza** [00]: immettere **03**
     * Per il **gruppo di lavoro host locale** [impostazione predefinita]: premere INVIO per accettare il valore predefinito
     * Per **Seleziona utilizzo sistema/immettere l'indice [4]**: immettere **4** (per personalizzato)
     * Per il **percorso dei volumi di dati** [/Hana/data/HN1]: premere INVIO per accettare il valore predefinito
     * Per il **percorso dei volumi di log** [/Hana/log/HN1]: premere INVIO per accettare il valore predefinito
     * Per **limitare l'allocazione di memoria massima?** [n]: immettere **n**
     * Per **nome host del certificato per l'host Hana-S1-DB1** [Hana-S1-DB1]: premere INVIO per accettare il valore predefinito
     * Per la **password dell'utente agente host SAP (sapadm)**: immettere la password
     * Per **confermare la password dell'utente agente host SAP (sapadm)**: immettere la password
     * Per la **password dell'amministratore di sistema (hn1adm)**: immettere la password
     * Per la **Home directory dell'amministratore di sistema** [/usr/SAP/HN1/Home]: premere INVIO per accettare il valore predefinito
     * Per la **Shell di accesso dell'amministratore di sistema** [/bin/sh]: premere INVIO per accettare il valore predefinito
     * Per l' **ID utente dell'amministratore di sistema** [1001]: premere INVIO per accettare il valore predefinito
     * Per **immettere l'ID del gruppo utenti (sapsys)** [79]: premere INVIO per accettare il valore predefinito
     * Per la **password utente del database di sistema (System)**: immettere la password del sistema
     * Per **Conferma password utente database di sistema (sistema)**: immettere la password del sistema
     * Per **riavviare il sistema dopo il riavvio del computer?** [n]: immettere **n** 
     * Per **continuare (y/n)**: convalidare il riepilogo e, in caso di esito positivo, immettere **y**

2. **[2]** ripetere il passaggio precedente per installare SAP Hana nel primo nodo del sito 2.   

3. **[1, 2]** verifica global.ini  

   Visualizzare global.ini e assicurarsi che sia attiva la configurazione per la comunicazione interna SAP HANA tra nodi. Verificare la sezione **comunicazione** . Deve avere lo spazio degli indirizzi per la `inter` subnet e `listeninterface` deve essere impostato su `.internal` . Verificare la sezione **internal_hostname_resolution** . Deve avere gli indirizzi IP per le macchine virtuali HANA che appartengono alla `inter` subnet.  

   ```
     sudo cat /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
     # Example from SITE1 
     [communication]
     internal_network = 10.23.1.128/26
     listeninterface = .internal
     [internal_hostname_resolution]
     10.23.1.138 = hana-s1-db1
     10.23.1.139 = hana-s1-db2
     10.23.1.140 = hana-s1-db3
   ```

4. **[1, 2]** preparare l' `global.ini` installazione in un ambiente non condiviso, come descritto nella nota SAP [2080991](https://launchpad.support.sap.com/#/notes/0002080991).  

   ```
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    [persistence]
    basepath_shared = no
   ```

4. **[1, 2]** riavviare SAP HANA per attivare le modifiche.  

   ```
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem
   ```

6. **[1, 2]** verificare che l'interfaccia client utilizzerà gli indirizzi IP dalla `client` subnet per la comunicazione.  

    ```
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "password" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result - example from SITE 2
    "hana-s2-db1","net_publicname","10.23.0.14"
   ```

   Per informazioni su come verificare la configurazione, vedere la nota SAP [2183363-configurazione di SAP Hana rete interna](https://launchpad.support.sap.com/#/notes/2183363).  

7. **[Ah]** Modificare le autorizzazioni per le directory di dati e log per evitare errori di installazione di HANA.  

   ```
    sudo chmod o+w -R /hana/data /hana/log
   ```

8. **[1]** installare i nodi Hana secondari. Le istruzioni di esempio in questo passaggio sono per il sito 1.  
   a. Avviare il programma **hdblcm** residente come `root` .    
    ```
     cd /hana/shared/HN1/hdblcm
     ./hdblcm 
    ```

   b. Al prompt dei comandi, immettere i valori seguenti:

     * Per **scegliere un'azione**: immettere **2** (per aggiungere gli host)
     * Per **immettere i nomi host delimitati da virgole da aggiungere**: Hana-S1-DB2, Hana-S1-DB3
     * Per **ulteriori componenti per l'installazione**: immettere **2, 3**
     * Per **immettere il nome utente radice [root]**: premere INVIO per accettare il valore predefinito
     * Per i **ruoli selezionati per l'host ' Hana-S1-DB2' [1]**: 1 (per il ruolo di lavoro)
     * Per **immettere il gruppo di failover host per l'host ' Hana-S1-DB2' [impostazione predefinita]**: premere INVIO per accettare il valore predefinito
     * Per **immettere il numero della partizione di archiviazione per l'host ' Hana-S1-DB2' [<<assign automatically>>]**: premere INVIO per accettare il valore predefinito
     * Per **immettere il gruppo di lavoro per l'host ' Hana-S1-DB2' [impostazione predefinita]**: premere INVIO per accettare il valore predefinito
     * Per i **ruoli selezionati per l'host ' Hana-S1-DB3' [1]**: 1 (per il ruolo di lavoro)
     * Per **immettere il gruppo di failover host per l'host ' Hana-S1-DB3' [impostazione predefinita]**: premere INVIO per accettare il valore predefinito
     * Per **immettere il numero della partizione di archiviazione per l'host ' Hana-S1-DB3' [<<assign automatically>>]**: premere INVIO per accettare il valore predefinito
     * Per **immettere il gruppo di lavoro per l'host ' Hana-S1-DB3' [impostazione predefinita]**: premere INVIO per accettare il valore predefinito
     * Per la **password dell'amministratore di sistema (hn1adm)**: immettere la password
     * Per **immettere la password dell'utente agente host SAP (sapadm)**: immettere la password
     * Per **confermare la password dell'utente agente host SAP (sapadm)**: immettere la password
     * Per **nome host del certificato per l'host Hana-S1-DB2** [Hana-S1-DB2]: premere INVIO per accettare il valore predefinito
     * Per **nome host del certificato per l'host Hana-S1-DB3** [Hana-S1-DB3]: premere INVIO per accettare il valore predefinito
     * Per **continuare (y/n)**: convalidare il riepilogo e, in caso di esito positivo, immettere **y**

9. **[2]** ripetere il passaggio precedente per installare i nodi del SAP Hana secondario nel sito 2.   

## <a name="configure-sap-hana-20-system-replication"></a>Configurare la replica di sistema di SAP HANA 2.0

1. **[1]** configurare la replica di sistema nel sito 1:

   Eseguire il backup dei database come **HN1**ADM:

    ```
    hdbsql -d SYSTEMDB -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupSYS')"
    hdbsql -d HN1 -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupHN1')"
    ```

   Copiare i file PKI di sistema nel sito secondario:

    ```
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/data/SSFS_HN1.DAT hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/data/
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/key/SSFS_HN1.KEY  hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/key/
    ```

   Creare il sito primario:

    ```
    hdbnsutil -sr_enable --name=HANA_S1
    ```

2. **[2]** configurare la replica di sistema nel sito 2:
    
   Registrare il secondo sito per avviare la replica di sistema. Eseguire il comando seguente come <hanasid\>adm:

    ```
    sapcontrol -nr 03 -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=hana-s1-db1 --remoteInstance=03 --replicationMode=sync --name=HANA_S2
    sapcontrol -nr 03 -function StartSystem
    ```

3. **[1]** Verificare lo stato della replica

   Verificare lo stato della replica e attendere che tutti i database siano sincronizzati.

    ```
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    # | Database | Host          | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary     | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
    # |          |               |       |              |           |         |           | Host          | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
    # | -------- | ------------- | ----- | ------------ | --------- | ------- | --------- | ------------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
    # | HN1      | hana-s1-db3   | 30303 | indexserver  |         5 |       1 | HANA_S1   | hana-s2-db3   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | SYSTEMDB | hana-s1-db1   | 30301 | nameserver   |         1 |       1 | HANA_S1   | hana-s2-db1   |     30301 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30307 | xsengine     |         2 |       1 | HANA_S1   | hana-s2-db1   |     30307 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30303 | indexserver  |         3 |       1 | HANA_S1   | hana-s2-db1   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db2   | 30303 | indexserver  |         4 |       1 | HANA_S1   | hana-s2-db2   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    #
    # status system replication site "2": ACTIVE
    # overall system replication status: ACTIVE
    #
    # Local System Replication State
    #   
    # mode: PRIMARY
    # site id: 1
    # site name: HANA_S1
    ```

4. **[1, 2]** modificare la configurazione di Hana in modo che la comunicazione per la replica di sistema Hana venga diretta tramite le interfacce di rete virtuale di replica di sistema Hana.   
   - Arrestare HANA in entrambi i siti
    ```
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem HDB
    ```

   - Modificare global.ini per aggiungere il mapping dell'host per la replica di sistema HANA: usare gli indirizzi IP dalla `hsr` subnet.  
    ```
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [system_replication_hostname_resolution]
    10.23.1.202 = hana-s1-db1
    10.23.1.203 = hana-s1-db2
    10.23.1.204 = hana-s1-db3
    10.23.1.205 = hana-s2-db1
    10.23.1.206 = hana-s2-db2
    10.23.1.207 = hana-s2-db3
    ```

   - Avviare HANA in entrambi i siti
   ```
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem HDB
   ```

   Per ulteriori informazioni, vedere la pagina relativa alla [risoluzione dei nomi host per la replica di sistema](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/1.0.12/en-US/c0cba1cb2ba34ec89f45b48b2157ec7b.html).  

5. **[Ah]** Abilitare di nuovo il firewall.  
   - Abilitare di nuovo il firewall
       ```
       # Execute as root
       systemctl start firewalld
       systemctl enable firewalld
       ```

   - Aprire le porte del firewall necessarie. Sarà necessario modificare le porte per il numero di istanza di HANA.  

       > [!IMPORTANT]
       > Creare regole del firewall per consentire la comunicazione tra nodi HANA e il traffico client. Le porte necessarie sono elencate in [TCP/IP Ports of All SAP Products](https://help.sap.com/viewer/ports) (Porte TCP/IP per tutti i prodotti SAP). I comandi seguenti sono solo un esempio. In questo scenario con il numero di sistema usato 03.

       ```
        # Execute as root
        sudo firewall-cmd --zone=public --add-port=30301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30301/tcp
        sudo firewall-cmd --zone=public --add-port=30303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30303/tcp
        sudo firewall-cmd --zone=public --add-port=30306/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30306/tcp
        sudo firewall-cmd --zone=public --add-port=30307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30307/tcp
        sudo firewall-cmd --zone=public --add-port=30313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30313/tcp
        sudo firewall-cmd --zone=public --add-port=30315/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30315/tcp
        sudo firewall-cmd --zone=public --add-port=30317/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30317/tcp
        sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30340/tcp
        sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30341/tcp
        sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30342/tcp
        sudo firewall-cmd --zone=public --add-port=1128/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1128/tcp
        sudo firewall-cmd --zone=public --add-port=1129/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1129/tcp
        sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40302/tcp
        sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40301/tcp
        sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40307/tcp
        sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40303/tcp
        sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40340/tcp
        sudo firewall-cmd --zone=public --add-port=50313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50313/tcp
        sudo firewall-cmd --zone=public --add-port=50314/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50314/tcp
        sudo firewall-cmd --zone=public --add-port=30310/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30310/tcp
        sudo firewall-cmd --zone=public --add-port=30302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30302/tcp
       ```

## <a name="create-a-pacemaker-cluster"></a>Creare un cluster Pacemaker

Seguire i passaggi della procedura di [configurazione di Pacemaker in Red Hat Enterprise Linux in Azure](high-availability-guide-rhel-pacemaker.md) per creare un cluster Pacemaker di base per questo server HANA.
Includere tutte le macchine virtuali, incluso il creatore della maggioranza nel cluster.  

> [!IMPORTANT]
> Non impostare `quorum expected-votes` su 2, in quanto non si tratta di un cluster a due nodi.  
> Verificare che la proprietà del cluster `concurrent-fencing`  sia abilitata, in modo che la schermatura del nodo venga deserializzata.   

## <a name="create-file-system-resources"></a>Creare risorse file system

1. **[1, 2]** arrestare SAP Hana in entrambi i siti di replica. Eseguire come <SID \> adm.  

    ```
    sapcontrol -nr 03 -function StopSystem
    ```

2. **[Ah]** Annullare `/hana/shared` il montaggio di file System, che è stato temporaneamente montato per l'installazione in tutte le VM del database Hana. È necessario arrestare tutti i processi e le sessioni, che usano il file system, prima di poterlo disinstallare. 
 
    ```
    umount /hana/shared 
    ```

3. **[1]** creare le risorse del cluster file System per `/hana/shared` in stato disabilitato. Le risorse vengono create con l'opzione `--disabled` , perché è necessario definire i vincoli location prima che i montaggi siano abilitati.  

    ```
    # /hana/shared file system for site 1
    pcs resource create fs_hana_shared_s1 --disabled ocf:heartbeat:Filesystem device=10.23.1.7:/HN1-shared-s1  directory=/hana/shared \
    fstype=nfs options='defaults,rw,hard,timeo=600,rsize=262144,wsize=262144,proto=tcp,intr,noatime,sec=sys,vers=4.1,lock,_netdev' op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 \
    op start interval=0 timeout=120 op stop interval=0 timeout=120

    # /hana/shared file system for site 2   
    pcs resource create fs_hana_shared_s2 --disabled ocf:heartbeat:Filesystem device=10.23.1.7:/HN1-shared-s1 directory=/hana/shared \
    fstype=nfs options='defaults,rw,hard,timeo=600,rsize=262144,wsize=262144,proto=tcp,intr,noatime,sec=sys,vers=4.1,lock,_netdev' op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 \
    op start interval=0 timeout=120 op stop interval=0 timeout=120

    # clone the /hana/shared file system resources for both site1 and site2
    pcs resource clone fs_hana_shared_s1 meta clone-node-max=1 interleave=true
    pcs resource clone fs_hana_shared_s2 meta clone-node-max=1 interleave=true
    ```
 
   `OCF_CHECK_LEVEL=20` l'attributo viene aggiunto all'operazione di monitoraggio, in modo che le operazioni di monitoraggio eseguano un test di lettura/scrittura sul file system. Senza questo attributo, l'operazione di monitoraggio verifica solo che il file system sia montato. Questo può costituire un problema perché, in caso di perdita della connettività, il file system può rimanere montato, sebbene sia inaccessibile.  

   `on-fail=fence` l'attributo viene aggiunto anche all'operazione di monitoraggio. Con questa opzione, se l'operazione di monitoraggio ha esito negativo in un nodo, il nodo viene immediatamente recintato. Senza questa opzione, il comportamento predefinito prevede l'arresto di tutte le risorse che dipendono dalla risorsa non riuscita, quindi il riavvio della risorsa non riuscita, quindi l'avvio di tutte le risorse che dipendono dalla risorsa che ha avuto esito negativo. Questo comportamento non solo può richiedere molto tempo quando una risorsa SAPHana dipende dalla risorsa che ha avuto esito negativo, ma può anche avere esito negativo. Non è possibile arrestare correttamente la risorsa SAPHana se la condivisione NFS che contiene i file binari HANA non è accessibile.  

4. **[1]** configurare e verificare gli attributi del nodo. A tutti i nodi SAP HANA database nel sito di replica 1 viene assegnato l'attributo `S1` e a tutti i nodi di SAP Hana database nel sito di replica 2 viene assegnato l'attributo `S2` .  

    ```
    # HANA replication site 1
    pcs node attribute hana-s1-db1 NFS_SID_SITE=S1
    pcs node attribute hana-s1-db2 NFS_SID_SITE=S1
    pcs node attribute hana-s1-db3 NFS_SID_SITE=S1
    # HANA replication site 2
    pcs node attribute hana-s2-db1 NFS_SID_SITE=S2
    pcs node attribute hana-s2-db2 NFS_SID_SITE=S2
    pcs node attribute hana-s2-db3 NFS_SID_SITE=S2
    #To verify the attribute assignment to nodes execute
    pcs node attribute
    ```

5. **[1]** configurare i vincoli, che determinano la posizione in cui verranno montati i file System NFS e abilitano le risorse file System.  
    ```
    # Configure the constraints
    pcs constraint location fs_hana_shared_s1-clone rule resource-discovery=never score=-INFINITY NFS_SID_SITE ne S1
    pcs constraint location fs_hana_shared_s2-clone rule resource-discovery=never score=-INFINITY NFS_SID_SITE ne S2
    # Enable the file system resources
    pcs resource enable fs_hana_shared_s1
    pcs resource enable fs_hana_shared_s2
    ```

   Quando si abilitano le risorse file system, il cluster monterà i `/hana/shared` file System.
 
6. **[Ah]** Verificare che i volumi e siano montati in `/hana/shared` in tutte le VM del database Hana in entrambi i siti.

    ```
    sudo nfsstat -m
    # Verify that flag vers is set to 4.1 
    # Example from SITE 1, hana-s1-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s1
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.11,local_lock=none,addr=10.23.1.7
    # Example from SITE 2, hana-s2-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s2
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.14,local_lock=none,addr=10.23.1.7
    ```

7. **[1]** configurare le risorse degli attributi. Configurare i vincoli che impostano gli attributi su `true` , se sono montati i montaggi NFS per `hana/shared` .  

    ```
    # Configure the attribure resources
    pcs resource create hana_nfs_s1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs_s1_active
    pcs resource create hana_nfs_s2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs_s2_active
    # Clone the attribure resources
    pcs resource clone hana_nfs_s1_active meta clone-node-max=1 interleave=true
    pcs resource clone hana_nfs_s2_active meta clone-node-max=1 interleave=true
    # Configure the constraints, which will set the attribute values
    pcs constraint order fs_hana_shared_s1-clone then hana_nfs_s1_active-clone
    pcs constraint order fs_hana_shared_s2-clone then hana_nfs_s2_active-clone
    ```

   > [!TIP]
   > Se la configurazione include altri file System, oltre a/ `hana/shared` , che sono montati NFS, includere `sequential=false` l'opzione, in modo che non esistano dipendenze di ordinamento tra i file System. Tutti i file system montati NFS devono iniziare, prima della risorsa attribute corrispondente, ma non devono essere avviati in alcun ordine. Per altre informazioni, vedere [ricerca per categorie configurare SAP HANA Scale-Out HSR in un cluster pacemaker quando i file System Hana sono condivisioni NFS](https://access.redhat.com/solutions/5423971).  

8. **[1]** posizionare pacemaker in modalità manutenzione per preparare la creazione delle risorse del cluster Hana.  
    ```
    pcs property set maintenance-mode=true
    ```

## <a name="create-sap-hana-cluster-resources"></a>Creare le risorse cluster SAP HANA

1. **[A]** installare l'agente risorse con scalabilità orizzontale Hana in tutti i nodi del cluster, incluso il creatore di maggioranza.    

    ```
    yum install -y resource-agents-sap-hana-scaleout 
    ```


   > [!NOTE]
   > Consultare i [criteri di supporto per i cluster RHEL a disponibilità elevata-gestione dei SAP Hana in un cluster](https://access.redhat.com/articles/3397471) per la versione minima supportata del pacchetto `resource-agents-sap-hana-scaleout` per la versione del sistema operativo.  

2. **[1, 2]** installare l'"hook di replica di sistema" Hana. L'hook deve essere installato in un nodo del database HANA in ogni sito di replica di sistema. SAP HANA dovrebbe essere ancora inattivo.        

   1. Preparare l'hook come `root` 
    ```
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR-ScaleOut/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. Modificare `global.ini`
    ```
    # add to global.ini
    [ha_dr_provider_SAPHanaSR]
    provider = SAPHanaSR
    path = /hana/shared/myHooks
    execution_order = 1
    
    [trace]
    ha_dr_saphanasr = info
    ```

3. **[Ah]** Il cluster richiede la configurazione di sudoers sul nodo del cluster per <SID \> adm. In questo esempio si otterrà creando un nuovo file. Eseguire i comandi come `root` .    
    ``` 
    cat << EOF > /etc/sudoers.d/20-saphana
    # SAPHanaSR-ScaleOut needs for srHook
     Cmnd_Alias SOK = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SOK -t crm_config -s SAPHanaSR
     Cmnd_Alias SFAIL = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SFAIL -t crm_config -s SAPHanaSR
     hn1adm ALL=(ALL) NOPASSWD: SOK, SFAIL
     EOF
    ```

4. **[1, 2]** avviare SAP Hana in entrambi i siti di replica. Eseguire come <SID \> adm.  

    ```
    sapcontrol -nr 03 -function StartSystem 
    ```

5. **[1]** verificare l'installazione dell'hook. Eseguire come <SID \> adm nel sito di replica di sistema Hana attivo.   

    ```
    cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*

     # Example entries
     # 2020-07-21 22:04:32.364379 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:46.905661 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:52.092016 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:52.782774 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:53.117492 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:06:35.599324 ha_dr_SAPHanaSR SOK
    ```

6. **[1]** creare le risorse del cluster Hana. Eseguire i comandi seguenti come `root` .    
   1. Verificare che il cluster sia già in modalità di manutenzione.  
    
   2. Successivamente, creare la risorsa della topologia HANA.  
      Se si compila il cluster RHEL **7. x** , usare i comandi seguenti:  
      ```
      pcs resource create SAPHanaTopology_HN1_HDB03 SAPHanaTopologyScaleOut \
       SID=HN1 InstanceNumber=03 \
       op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600

      pcs resource clone SAPHanaTopology_HN1_HDB03 meta clone-node-max=1 interleave=true
      ```

      Se si compila il cluster RHEL **8. x** , usare i comandi seguenti:  
      ```
      pcs resource create SAPHanaTopology_HN1_HDB03 SAPHanaTopology \
       SID=HN1 InstanceNumber=03 meta clone-node-max=1 interleave=true \
       op methods interval=0s timeout=5 \
       op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600

      pcs resource clone SAPHanaTopology_HN1_HDB03 meta clone-node-max=1 interleave=true
      ```

   3. Successivamente, creare la risorsa dell'istanza HANA.  
      > [!NOTE]
      > Questo articolo contiene riferimenti al termine *slave*, un termine che Microsoft non usa più. Quando il termine viene rimosso dal software, questo verrà rimosso da questo articolo.  
 
      Se si compila il cluster RHEL **7. x** , usare i comandi seguenti:    
      ```
      pcs resource create SAPHana_HN1_HDB03 SAPHanaController \
       SID=HN1 InstanceNumber=03 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
       op start interval=0 timeout=3600 op stop interval=0 timeout=3600 op promote interval=0 timeout=3600 \
       op monitor interval=60 role="Master" timeout=700 op monitor interval=61 role="Slave" timeout=700
     
      pcs resource master msl_SAPHana_HN1_HDB03 SAPHana_HN1_HDB03 \
       meta master-max="1" clone-node-max=1 interleave=true
      ```

      Se si compila il cluster RHEL **8. x** , usare i comandi seguenti:  
      ```
      pcs resource create SAPHana_HN1_HDB03 SAPHanaController \
       SID=HN1 InstanceNumber=03 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
       op demote interval=0s timeout=320 op methods interval=0s timeout=5 \
       op start interval=0 timeout=3600 op stop interval=0 timeout=3600 op promote interval=0 timeout=3600 \
       op monitor interval=60 role="Master" timeout=700 op monitor interval=61 role="Slave" timeout=700
     
      pcs resource promotable SAPHana_HN1_HDB03 \
       meta master-max="1" clone-node-max=1 interleave=true
      ```
      > [!IMPORTANT]
      > Si consiglia di impostare AUTOMATED_REGISTER su **No**, durante l'esecuzione di test di failover completi, per impedire che l'istanza primaria non riuscita venga registrata automaticamente come secondaria. Una volta completati i test di failover, impostare AUTOMATED_REGISTER su **Sì**, in modo che dopo che la replica del sistema di acquisizione delle acquisizioni possa essere ripresa automaticamente. 

   4. Creare un indirizzo IP virtuale e le risorse associate.  
      ```
      pcs resource create vip_HN1_03 ocf:heartbeat:IPaddr2 ip=10.23.0.18 op monitor interval="10s" timeout="20s"
      sudo pcs resource create nc_HN1_03 azure-lb port=62503
      sudo pcs resource group add g_ip_HN1_03 nc_HN1_03 vip_HN1_03
      ```

   5. Creare i vincoli del cluster  
      Se si compila il cluster RHEL **7. x** , usare i comandi seguenti:  
      ```
      #Start HANA topology, before the HANA instance
      pcs constraint order SAPHanaTopology_HN1_HDB03-clone then msl_SAPHana_HN1_HDB03

      pcs constraint colocation add g_ip_HN1_03 with master msl_SAPHana_HN1_HDB03 4000
      #HANA resources are only allowed to run on a node, if the node's NFS file systems are mounted. The constraint also avoids the majority maker node
      pcs constraint location SAPHanaTopology_HN1_HDB03-clone rule resource-discovery=never score=-INFINITY hana_nfs_s1_active ne true and hana_nfs_s2_active ne true
      ```
 
      Se si compila il cluster RHEL **8. x** , usare i comandi seguenti:  
      ```
      #Start HANA topology, before the HANA instance
      pcs constraint order SAPHanaTopology_HN1_HDB03-clone then SAPHana_HN1_HDB03-clone

      pcs constraint colocation add g_ip_HN1_03 with master SAPHana_HN1_HDB03-clone 4000
      #HANA resources are only allowed to run on a node, if the node's NFS file systems are mounted. The constraint also avoids the majority maker node
      pcs constraint location SAPHanaTopology_HN1_HDB03-clone rule resource-discovery=never score=-INFINITY hana_nfs_s1_active ne true and hana_nfs_s2_active ne true
      ```

7. **[1]** posizionare il cluster fuori dalla modalità di manutenzione. Assicurarsi che lo stato del cluster sia corretto e che tutte le risorse siano avviate.  
    ```
    sudo pcs property set maintenance-mode=false
    #If there are failed cluster resources, you may need to run the next command
    pcs resource cleanup
    ```
  
   > [!NOTE]
   > I timeout nella configurazione precedente sono solo esempi e possono essere adattati alla configurazione HANA specifica. Ad esempio, potrebbe essere necessario aumentare il timeout di avvio, se l'avvio del database di SAP HANA richiede più tempo.
  
## <a name="test-sap-hana-failover"></a>Test SAP HANA failover 

1. Prima di avviare un test, controllare il cluster e SAP HANA lo stato della replica di sistema.  

   a. Verificare che non siano presenti azioni cluster non riuscite  
     ```
     #Verify that there are no failed cluster actions
     pcs status
     # Example
     #Stack: corosync
     #Current DC: hana-s-mm (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
     #Last updated: Thu Sep 24 06:00:20 2020
     #Last change: Thu Sep 24 05:59:17 2020 by root via crm_attribute on hana-s1-db1
     #
     #7 nodes configured
     #45 resources configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Active resources:
     #
     #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
     #Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
     #    Started: [ hana--s1-db1 hana-s1-db2 hana-s1-db3 ]
     #Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
     #    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
     #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
     #Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
     #    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
     #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
     #    Masters: [ hana-s1-db1 ]
     #    Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Resource Group: g_ip_HN1_03
     #    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s1-db1
     #    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
     ```

   b. Verificare che la replica di sistema SAP HANA sia sincronizzata

      ```
      # Verify HANA HSR is in sync
      sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
      #| Database | Host        | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary     | Secondary| Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
      #|          |             |       |              |           |         |           | Host          | Port     | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
      #| -------- | ----------- | ----- | ------------ | --------- | ------- | --------- | ------------- | -------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
      #| HN1      | hana-s1-db3 | 30303 | indexserver  |         5 |       2 | HANA_S1   | hana-s2-db3 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db2 | 30303 | indexserver  |         4 |       2 | HANA_S1   | hana-s2-db2 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |  
      #| SYSTEMDB | hana-s1-db1 | 30301 | nameserver   |         1 |       2 | HANA_S1   | hana-s2-db1 |     30301  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db1 | 30307 | xsengine     |         2 |       2 | HANA_S1   | hana-s2-db1 |     30307  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db1 | 30303 | indexserver  |         3 |       2 | HANA_S1   | hana-s2-db1 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |

      #status system replication site "1": ACTIVE
      #overall system replication status: ACTIVE

      #Local System Replication State
      #~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

      #mode: PRIMARY
      #site id: 1
      #site name: HANA_S1
      ```

2. Verificare la configurazione del cluster per uno scenario di errore, quando un nodo perde l'accesso alla condivisione NFS ( `/hana/shared` ).  

   Gli agenti di risorse SAP HANA dipendono dai file binari, archiviati in `/hana/shared` per eseguire operazioni durante il failover. Il file System `/hana/shared` viene montato su NFS nella configurazione presentata. Un test che può essere eseguito consiste nel rimontare il `/hana/shared` file System come di sola *lettura*. Questo approccio verifica che venga eseguito il failover del cluster, se l'accesso a `/hana/shared` viene perso nel sito di replica del sistema attivo.  

   **Risultato previsto**: quando si esegue il rimontaggio come di sola `/hana/shared` *lettura*, l'operazione di monitoraggio che esegue l'operazione di lettura/scrittura su file System, avrà esito negativo perché non è in grado di scrivere nel file System e ATTIVERÀ il failover delle risorse Hana. Lo stesso risultato è previsto quando il nodo HANA perde l'accesso alla condivisione NFS.  
     
   È possibile controllare lo stato delle risorse del cluster eseguendo `crm_mon` o `pcs status` . Stato delle risorse prima dell'avvio del test:
      ```
      # Output of crm_mon
      #7 nodes configured
      #45 resources configured

      #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      #
      #Active resources:

      #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
      # Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
      #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
      #     Masters: [ hana-s1-db1 ]
      #     Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Resource Group: g_ip_HN1_03
      #     nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s1-db1
      #     vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
      ```

   Per simulare l'errore per `/hana/shared` in una delle macchine virtuali del sito di replica primaria, eseguire il comando seguente:
      ```
      # Execute as root 
      mount -o ro /hana/shared
      # Or if the above command returns an error
      sudo mount -o ro 10.23.1.7/HN1-shared-s1 /hana/shared
      ```
  
   La VM HANA, che ha perso l'accesso a `/hana/shared` , deve essere riavviata o arrestata, a seconda della configurazione del cluster. Viene eseguita la migrazione delle risorse cluster all'altro sito di replica di sistema HANA.  
         
   Se il cluster non è stato avviato nella macchina virtuale, è stato riavviato, avviare il cluster eseguendo: 

      ```
      # Start the cluster 
      pcs cluster start
      ```
   
   All'avvio del cluster file system `/hana/shared` verrà automaticamente montata.     
   Se si imposta AUTOMATED_REGISTER = "false", sarà necessario configurare SAP HANA la replica di sistema nel sito secondario. In questo caso, è possibile eseguire questi comandi per riconfigurare SAP HANA come secondario.   

      ```
      # Execute on the secondary 
      su - hn1adm
      # Make sure HANA is not running on the secondary site. If it is started, stop HANA
      sapcontrol -nr 03 -function StopWait 600 10
      # Register the HANA secondary site
      hdbnsutil -sr_register --name=HANA_S1 --remoteHost=hana-s2-db1 --remoteInstance=03 --replicationMode=sync
      # Switch back to root and cleanup failed resources
      pcs resource cleanup SAPHana_HN1_HDB03
      ```

   Stato delle risorse, dopo il test: 

      ```
      # Output of crm_mon
      #7 nodes configured
      #45 resources configured
    
      #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      
      #Active resources:
    
      #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
      # Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
      #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
      #     Masters: [ hana-s2-db1 ]
      #     Slaves: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db2 hana-s2-db3 ]
      # Resource Group: g_ip_HN1_03
      #     nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s2-db1
      #     vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s2-db1
      ```


Si consiglia di testare accuratamente la configurazione del cluster di SAP HANA, eseguendo anche i test, documentati in [disponibilità elevata per SAP Hana in macchine virtuali di Azure in RHEL](./sap-hana-high-availability-rhel.md#test-the-cluster-setup).


## <a name="next-steps"></a>Passaggi successivi

* [Pianificazione e implementazione di macchine virtuali di Azure per SAP][planning-guide]
* [Distribuzione di Macchine virtuali di Azure per SAP][deployment-guide]
* [Distribuzione DBMS di Macchine virtuali di Azure per SAP][dbms-guide]
* Per informazioni su come stabilire la disponibilità elevata e pianificare il ripristino di emergenza di SAP HANA nelle VM di Azure, vedere [disponibilità elevata di SAP Hana in macchine virtuali di Azure][sap-hana-ha].