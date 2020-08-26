---
title: SAP HANA con scalabilità orizzontale con Azure NetApp Files su RHEL | Microsoft Docs
description: Guida alla disponibilità elevata per SAP NetWeaver in Red Hat Enterprise Linux con Azure NetApp Files per le applicazioni SAP
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
ms.date: 06/15/2020
ms.author: radeltch
ms.openlocfilehash: 9978137edb7874a8b93e0c9a5f1f9979ce449277
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88893171"
---
# <a name="deploy-a-sap-hana-scale-out-system-with-standby-node-on-azure-vms-by-using-azure-netapp-files-on-red-hat-enterprise-linux"></a>Distribuire un sistema di SAP HANA con scale-out con un nodo standby in macchine virtuali di Azure usando Azure NetApp Files su Red Hat Enterprise Linux 

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


Questo articolo descrive come distribuire un sistema di SAP HANA a disponibilità elevata in una configurazione con scalabilità orizzontale con standby in Azure Red Hat Enterprise Linux macchine virtuali (VM), usando [Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-introduction.md) per i volumi di archiviazione condivisa.  

Nelle configurazioni di esempio, nei comandi di installazione e così via, l'istanza di HANA è **03** e l'ID di sistema Hana è **HN1**. Gli esempi sono basati su HANA 2,0 SP4 e Red Hat Enterprise Linux per SAP 7,6. 

Prima di iniziare, vedere le note e i documenti SAP seguenti:

* [Documentazione di Azure NetApp Files][anf-azure-doc] 
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
* Documentazione generale di RHEL
  * [High Availability Add-On Overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index) (Panoramica dei componenti aggiuntivi a disponibilità elevata)
  * [High Availability Add-On Administration](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index) (Amministrazione dei componenti aggiuntivi a disponibilità elevata)
  * [High Availability Add-On Reference](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index) (Riferimento dei componenti aggiuntivi a disponibilità elevata)
  * [Guida alla rete Red Hat Enterprise Linux](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide)
* Documentazione di RHEL specifica di Azure:
  * [Install SAP HANA on Red Hat Enterprise Linux for Use in Microsoft Azure](https://access.redhat.com/public-cloud/microsoft-azure) (Installare SAP HANA su Red Hat Enterprise Linux per l'uso in Microsoft Azure)
* [Applicazioni NetApp SAP su Microsoft Azure con Azure NetApp Files][anf-sap-applications-azure]


## <a name="overview"></a>Panoramica

Un metodo per ottenere la disponibilità elevata di HANA consiste nel configurare il failover automatico dell'host. Per configurare il failover automatico dell'host, aggiungere una o più macchine virtuali al sistema HANA e configurarle come nodi standby. Quando si verifica un errore nel nodo attivo, il nodo standby assume automaticamente il valore. Nella configurazione presentata con macchine virtuali di Azure si ottiene il failover automatico usando [NFS in Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-introduction.md).  

> [!NOTE]
> Il nodo standby deve accedere a tutti i volumi del database. I volumi HANA devono essere montati come volumi NFSv4. Il meccanismo di blocco basato sul lease di file migliorato nel protocollo NFSv4 viene usato per la `I/O` schermatura. 

> [!IMPORTANT]
> Per compilare la configurazione supportata, è necessario distribuire i volumi di dati e di log HANA come NFSv 4.1 volumi e montarli usando il protocollo NFSv 4.1. La configurazione del failover automatico dell'host HANA con il nodo standby non è supportata con NFSv3.

![Panoramica della disponibilità elevata di SAP NetWeaver](./media/sap-hana-high-availability-rhel/sap-hana-scale-out-standby-netapp-files-rhel.png)

Nel diagramma precedente, che segue SAP HANA consigli sulla rete, tre subnet sono rappresentate all'interno di una rete virtuale di Azure: 
* Per la comunicazione client
* Per la comunicazione con il sistema di archiviazione
* Per la comunicazione interna HANA tra nodi

I volumi di Azure NetApp si trovano in una subnet separata, [delegata a Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).  

Per questa configurazione di esempio, le subnet sono:  

  - `client` 10.9.1.0/26  
  - `storage` 10.9.3.0/26  
  - `hana` 10.9.2.0/26  
  - `anf` 10.9.0.0/26 (subnet delegata a Azure NetApp Files)

## <a name="set-up-the-azure-netapp-files-infrastructure"></a>Configurare l'infrastruttura di Azure NetApp Files 

Prima di procedere con l'installazione di Azure NetApp Files infrastruttura, acquisire familiarità con la [documentazione di Azure NetApp files][anf-azure-doc]. 

Azure NetApp Files è disponibile in diverse [aree di Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Verificare se l'area di Azure selezionata offre Azure NetApp Files.  

Per informazioni sulla disponibilità di Azure NetApp Files dall'area di Azure, vedere [Azure NetApp files disponibilità per area di Azure][anf-avail-matrix].  

Prima di distribuire Azure NetApp Files, richiedere l'onboarding per Azure NetApp Files eseguendo [la registrazione per istruzioni Azure NetApp files][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Distribuire le risorse di Azure NetApp Files  

Le istruzioni seguenti presuppongono che la [rete virtuale di Azure](../../../virtual-network/virtual-networks-overview.md)sia già stata distribuita. Le risorse Azure NetApp Files e le macchine virtuali in cui verranno montate le risorse di Azure NetApp Files devono essere distribuite nella stessa rete virtuale di Azure o in reti virtuali di Azure con peering.  

1. Se le risorse non sono già state distribuite, richiedere l' [onboarding per Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-register.md).  

2. Per creare un account NetApp nell'area di Azure selezionata, seguire le istruzioni riportate in [creare un account NetApp](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md).  

3. Configurare un pool di capacità Azure NetApp Files seguendo le istruzioni riportate in [configurare un pool di capacità Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md).  

   L'architettura HANA presentata in questo articolo usa un singolo pool di capacità Azure NetApp Files al livello di *servizio ultra* . Per i carichi di lavoro HANA in Azure, è consigliabile usare un [livello di servizio](../../../azure-netapp-files/azure-netapp-files-service-levels.md)Azure NetApp files *ultra* o *Premium* .  

4. Delegare una subnet a Azure NetApp Files, come descritto nelle istruzioni in [delegare una subnet a Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).  

5. Distribuire Azure NetApp Files volumi seguendo le istruzioni riportate in [creare un volume NFS per Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).  

   Quando si distribuiscono i volumi, assicurarsi di selezionare la versione **NFSv 4.1** . Distribuire i volumi nella [subnet](/rest/api/virtualnetwork/subnets) di Azure NetApp Files designata. Gli indirizzi IP dei volumi di Azure NetApp vengono assegnati automaticamente. 
   
   Tenere presente che le risorse Azure NetApp Files e le macchine virtuali di Azure devono trovarsi nella stessa rete virtuale di Azure o in reti virtuali di Azure con peering. Ad esempio, **HN1**-data-Mnt00001, **HN1**-log-mnt00001 e così via, sono i nomi di volume e NFS://10.9.0.4/**HN1**-data-mnt00001, NFS://10.9.0.4/**HN1**-log-mnt00001 e così via, sono i percorsi dei file per i volumi Azure NetApp files.  

   * volume **HN1**-data-mnt00001 (NFS://10.9.0.4/**HN1**-data-mnt00001)
   * volume **HN1**-data-mnt00002 (NFS://10.9.0.4/**HN1**-data-mnt00002)
   * volume **HN1**-log-mnt00001 (NFS://10.9.0.4/**HN1**-log-mnt00001)
   * volume **HN1**-log-mnt00002 (NFS://10.9.0.4/**HN1**-log-mnt00002)
   * volume **HN1**-shared (NFS://10.9.0.4/**HN1**-Shared)
   
   In questo esempio è stato usato un volume di Azure NetApp Files separato per ogni volume di log e di dati HANA. Per una configurazione più conveniente in sistemi più piccoli o non produttivi, è possibile collocare tutti i montaggi dei dati in un singolo volume e tutti i log vengono montati in un singolo volume diverso.  

### <a name="important-considerations"></a>Considerazioni importanti

Quando si crea la Azure NetApp Files per SAP HANA la scalabilità orizzontale con lo scenario stand by nodes, tenere presente le seguenti considerazioni importanti:

- Il pool di capacità minimo è 4 TB (TiB).  
- La dimensione minima del volume è 100 gibibyte (GiB).
- Azure NetApp Files e tutte le macchine virtuali in cui verranno montati i volumi di Azure NetApp Files devono trovarsi nella stessa rete virtuale di Azure o in [reti virtuali con peering](../../../virtual-network/virtual-network-peering-overview.md) nella stessa area.  
- La rete virtuale selezionata deve avere una subnet delegata a Azure NetApp Files.
- La velocità effettiva di un volume Azure NetApp Files è una funzione della quota del volume e del livello di servizio, come documentato nel [livello di servizio per Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). Quando si dimensionano i volumi di HANA Azure NetApp, assicurarsi che la velocità effettiva risultante soddisfi i requisiti di sistema HANA.  
- Con i [criteri di esportazione](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)Azure NetApp files, è possibile controllare i client consentiti, il tipo di accesso (lettura/scrittura, sola lettura e così via). 
- La funzionalità Azure NetApp Files non è ancora in grado di riconoscere la zona. Attualmente, la funzionalità non viene distribuita in tutte le zone di disponibilità in un'area di Azure. Tenere presente le implicazioni di latenza potenziali in alcune aree di Azure.  

> [!IMPORTANT]
> Per carichi di lavoro SAP HANA, è fondamentale una bassa latenza. Collaborare con il rappresentante Microsoft per assicurarsi che le macchine virtuali e i volumi Azure NetApp Files vengano distribuiti in prossimità.  

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Dimensionamento per il database HANA in Azure NetApp Files

La velocità effettiva di un volume Azure NetApp Files è una funzione delle dimensioni del volume e del livello di servizio, come documentato nel [livello di servizio per Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). 

Quando si progetta l'infrastruttura per SAP in Azure, tenere presenti alcuni requisiti minimi di archiviazione da parte di SAP, che si traduce in caratteristiche di velocità effettiva minime:

- Lettura/scrittura su/Hana/log di 250 megabyte al secondo (MB/s) con dimensioni di I/O di 1 MB.  
- Attività di lettura di almeno 400 MB/s per/Hana/data per le dimensioni di I/O di 16 MB e 64 MB.  
- Attività di scrittura di almeno 250 MB/s per/Hana/data con dimensioni di I/O di 16 MB e 64 MB. 

I [limiti di velocità effettiva di Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md) per 1 TiB della quota del volume sono:
- Livello di archiviazione Premium-64 MiB/s  
- Livello di archiviazione Ultra-128 MiB/s  

Per soddisfare i requisiti di velocità effettiva minima SAP per dati e log e per le linee guida per/Hana/Shared, le dimensioni consigliate sono:

| Volume | Dimensioni di<br>Livello Archiviazione Premium | Dimensioni di<br>Livello Archiviazione Ultra | Protocollo NFS supportato |
| --- | --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v4.1 |
| /hana/data | 6,3 TiB | 3,2 TiB | v4.1 |
| /hana/shared | 1xRAM per 4 nodi del ruolo di lavoro | 1xRAM per 4 nodi del ruolo di lavoro | v3 o v4.1 |

La configurazione SAP HANA per il layout presentato in questo articolo, usando Azure NetApp Files livello di archiviazione Ultra, sarà:

| Volume | Dimensioni di<br>Livello Archiviazione Ultra | Protocollo NFS supportato |
| --- | --- | --- |
| /hana/log/mnt00001 | 2 TiB | v4.1 |
| /hana/log/mnt00002 | 2 TiB | v4.1 |
| /hana/data/mnt00001 | 3,2 TiB | v4.1 |
| /hana/data/mnt00002 | 3,2 TiB | v4.1 |
| /hana/shared | 2 TiB | v3 o v4.1 |

> [!NOTE]
> Le indicazioni relative al dimensionamento Azure NetApp Files indicate di seguito sono destinate a soddisfare i requisiti minimi consigliati da SAP per i provider di infrastruttura. Nelle distribuzioni reali dei clienti e negli scenari di carico di lavoro, queste dimensioni potrebbero non essere sufficienti. Considerare quindi queste indicazioni come punto di inizio e adattarle in base ai requisiti del carico di lavoro specifico.  

> [!TIP]
> È possibile ridimensionare Azure NetApp Files volumi in modo dinamico, senza dover *smontare* i volumi, arrestare le macchine virtuali o arrestare SAP Hana. Questo approccio offre la flessibilità necessaria per soddisfare le esigenze di velocità effettiva prevista e non prevista dell'applicazione.

## <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Distribuire macchine virtuali Linux tramite il portale di Azure

Per prima cosa è necessario creare i volumi Azure NetApp Files. Eseguire quindi i passaggi seguenti:
1. Creare le [subnet della rete virtuale di Azure](../../../virtual-network/virtual-network-manage-subnet.md) nella [rete virtuale di Azure](../../../virtual-network/virtual-networks-overview.md). 
1. Distribuire le macchine virtuali. 
1. Creare le interfacce di rete aggiuntive e alleghi le interfacce di rete alle VM corrispondenti.  

   Ogni macchina virtuale dispone di tre interfacce di rete, che corrispondono alle tre subnet della rete virtuale di Azure ( `client` `storage` e `hana` ). 

   Per altre informazioni, vedere [creare una macchina virtuale Linux in Azure con più schede di interfaccia di rete](../../linux/multiple-nics.md).  

> [!IMPORTANT]
> Per carichi di lavoro SAP HANA, è fondamentale una bassa latenza. Per ottenere una bassa latenza, collaborare con il rappresentante Microsoft per assicurarsi che le macchine virtuali e i volumi Azure NetApp Files vengano distribuiti in prossimità. Quando si sta caricando un [nuovo SAP Hana sistema](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) che usa SAP Hana Azure NetApp files, inviare le informazioni necessarie. 
 
Nelle istruzioni successive si presuppone che siano già stati creati il gruppo di risorse, la rete virtuale di Azure e le tre subnet della rete virtuale di Azure: `client` , `storage` e `hana` . Quando si distribuiscono le macchine virtuali, selezionare la subnet client, in modo che l'interfaccia di rete del client sia l'interfaccia principale nelle VM. Sarà anche necessario configurare una route esplicita per la Azure NetApp Files subnet delegata tramite il gateway della subnet di archiviazione. 

> [!IMPORTANT]
> Verificare che il sistema operativo selezionato sia certificato per SAP per SAP HANA sui tipi di VM specifici in uso. Per un elenco dei tipi di VM SAP HANA certificati e delle versioni del sistema operativo per questi tipi, accedere al sito [SAP Hana piattaforme IaaS certificate](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) . Fare clic sui dettagli del tipo di macchina virtuale elencato per ottenere l'elenco completo delle versioni del sistema operativo supportate da SAP HANA per quel tipo.  

1. Creare un set di disponibilità per SAP HANA. Assicurarsi di impostare il dominio di aggiornamento massimo.  

2. Creare tre macchine virtuali (**hanadb1**, **hanadb2**, **hanadb3**) seguendo questa procedura:  

   a. Usare un'immagine Red Hat Enterprise Linux nella raccolta di Azure supportata per SAP HANA. In questo esempio è stata usata un'immagine RHEL-SAP-HA 7,6.  

   b. Selezionare il set di disponibilità creato in precedenza per SAP HANA.  

   c. Selezionare la subnet della rete virtuale di Azure client. Selezionare [rete accelerata](../../../virtual-network/create-vm-accelerated-networking-cli.md).  

   Quando si distribuiscono le macchine virtuali, il nome dell'interfaccia di rete viene generato automaticamente. In queste istruzioni per semplicità si farà riferimento alle interfacce di rete generate automaticamente, che sono collegate alla subnet della rete virtuale di Azure client come **hanadb1-client**, **hanadb2-client**e **hanadb3-client**. 

3. Creare tre interfacce di rete, una per ogni macchina virtuale, per la `storage` subnet della rete virtuale (in questo esempio, **hanadb1-storage**, **hanadb2-storage**e **hanadb3-storage**).  

4. Creare tre interfacce di rete, una per ogni macchina virtuale, per la `hana`  subnet della rete virtuale (in questo esempio, **hanadb1-Hana**, **hanadb2-Hana**e **hanadb3-Hana**).  

5. Alleghi le interfacce di rete virtuale appena create alle macchine virtuali corrispondenti attenendosi alla procedura seguente:  

    a. Passare alla macchina virtuale nella [portale di Azure](https://portal.azure.com/#home).  

    b. Nel riquadro sinistro selezionare **macchine virtuali**. Filtrare sul nome della macchina virtuale (ad esempio, **hanadb1**), quindi selezionare la macchina virtuale.  

    c. Nel riquadro **Panoramica** selezionare **Arresta** per deallocare la macchina virtuale.  

    d. Selezionare **rete**e quindi collegare l'interfaccia di rete. Nell'elenco a discesa **Connetti interfaccia di rete** selezionare le interfacce di rete già create per le `storage` subnet e `hana` .  
    
    e. Seleziona **Salva**. 
 
    f. Ripetere i passaggi da b a e per le macchine virtuali rimanenti (in questo esempio,  **hanadb2** e **hanadb3**).
 
    g. Per il momento, lasciare le macchine virtuali in stato di arresto. Successivamente, verrà abilitata la [rete accelerata](../../../virtual-network/create-vm-accelerated-networking-cli.md) per tutte le interfacce di rete appena collegate.  

6. Abilitare la rete accelerata per le interfacce di rete aggiuntive per le `storage` subnet e seguendo `hana` questa procedura:  

    a. Aprire [Azure cloud Shell](https://azure.microsoft.com/features/cloud-shell/) nel [portale di Azure](https://portal.azure.com/#home).  

    b. Eseguire i comandi seguenti per abilitare la rete accelerata per le interfacce di rete aggiuntive, che sono collegate `storage` alle `hana` subnet e.  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-storage</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true

    </code></pre>

7. Avviare le macchine virtuali seguendo questa procedura:  

    a. Nel riquadro sinistro selezionare **macchine virtuali**. Filtrare sul nome della macchina virtuale (ad esempio, **hanadb1**) e quindi selezionarlo.  

    b. Nel riquadro **Panoramica** selezionare **Avvia**.  

## <a name="operating-system-configuration-and-preparation"></a>Configurazione e preparazione del sistema operativo

Le istruzioni riportate nelle sezioni successive sono precedute da uno dei seguenti elementi:
* **[A]**: applicabile a tutti i nodi
* **[1]**: applicabile solo al nodo 1
* **[2]**: applicabile solo al nodo 2
* **[3]**: applicabile solo al nodo 3

Configurare e preparare il sistema operativo seguendo questa procedura:

1. **[A]** gestire i file host nelle macchine virtuali. Includere le voci per tutte le subnet. Per questo esempio sono state aggiunte le voci seguenti `/etc/hosts` .  

    <pre><code>
    # Storage
    10.9.3.4   hanadb1-storage
    10.9.3.5   hanadb2-storage
    10.9.3.6   hanadb3-storage
    # Client
    10.9.1.5   hanadb1
    10.9.1.6   hanadb2
    10.9.1.7   hanadb3
    # Hana
    10.9.2.4   hanadb1-hana
    10.9.2.5   hanadb2-hana
    10.9.2.6   hanadb3-hana
    </code></pre>

3. **[A]** aggiungere una route di rete, in modo che la comunicazione con il Azure NetApp files passa attraverso l'interfaccia di rete di archiviazione.  

   In questo esempio verrà usato `Networkmanager` per configurare la route di rete aggiuntiva. Nelle istruzioni seguenti si presuppone che l'interfaccia di rete di archiviazione sia `eth1` .  
   Per prima cosa, determinare il nome della connessione per il dispositivo `eth1` . In questo esempio il nome della connessione per il dispositivo `eth1` è `Wired connection 1` .  

    <pre><code>
    # Execute as root
    nmcli connection
    # Result
    #NAME                UUID                                  TYPE      DEVICE
    #System eth0         5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03  ethernet  eth0
    #Wired connection 1  4b0789d1-6146-32eb-83a1-94d61f8d60a7  ethernet  eth1
    </code></pre>

   Configurare quindi route aggiuntive per la rete delegata Azure NetApp Files tramite `eth1` .  

    <pre><code>
    # Add the following route 
    # ANFDelegatedSubnet/cidr via StorageSubnetGW dev StorageNetworkInterfaceDevice
    nmcli connection modify <b>"Wired connection 1"</b> +ipv4.routes <b>"10.9.0.0/26 10.9.3.1"</b>
    </code></pre>

    Riavviare la macchina virtuale per attivare le modifiche.  

3. **[A]** installare il pacchetto client NFS.  

    <pre><code>
    yum install nfs-utils
    </code></pre>

3. **[A]** preparare il sistema operativo per l'esecuzione di SAP Hana in Azure NetApp con NFS, come descritto in [applicazioni SAP NetApp su Microsoft Azure con Azure NetApp files][anf-sap-applications-azure]. Creare il file di configurazione */etc/sysctl.d/NetApp-Hana.conf* per le impostazioni di configurazione NetApp.  

    <pre><code>
    vi /etc/sysctl.d/netapp-hana.conf
    # Add the following entries in the configuration file
    net.core.rmem_max = 16777216
    net.core.wmem_max = 16777216
    net.core.rmem_default = 16777216
    net.core.wmem_default = 16777216
    net.core.optmem_max = 16777216
    net.ipv4.tcp_rmem = 65536 16777216 16777216
    net.ipv4.tcp_wmem = 65536 16777216 16777216
    net.core.netdev_max_backlog = 300000 
    net.ipv4.tcp_slow_start_after_idle=0 
    net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **[A] creare il** file di configurazione */etc/sysctl.d/MS-AZ.conf* con impostazioni di ottimizzazione aggiuntive.  

    <pre><code>
    vi /etc/sysctl.d/ms-az.conf
    # Add the following entries in the configuration file
    net.ipv6.conf.all.disable_ipv6 = 1
    net.ipv4.tcp_max_syn_backlog = 16348
    net.ipv4.ip_local_port_range = 40000 65300
    net.ipv4.conf.all.rp_filter = 0
    sunrpc.tcp_slot_table_entries = 128
    vm.swappiness=10
    </code></pre>

5. **[A]** modificare le impostazioni di sunrpc, come consigliato nelle [applicazioni SAP NetApp su Microsoft Azure usando Azure NetApp files][anf-sap-applications-azure].  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

6. **[A]** Red Hat per la configurazione Hana.

    Configurare RHEL come descritto nella nota SAP [2292690], [2455582], [2593824] e <https://access.redhat.com/solutions/2447641> .

    > [!NOTE]
    > Se si installa HANA 2,0 SP04, sarà necessario installare il pacchetto `compat-sap-c++-7` come descritto nella nota SAP [2593824], prima di poter installare SAP Hana. 

## <a name="mount-the-azure-netapp-files-volumes"></a>Montare i volumi Azure NetApp Files

1. **[A]** creare punti di montaggio per i volumi del database Hana.  

    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** creare directory specifiche del nodo per/usr/SAP in **HN1**-Shared.  

    <pre><code>
    # Create a temporary directory to mount <b>HN1</b>-shared
    mkdir /mnt/tmp
    # if using NFSv3 for this volume, mount with the following command
    mount <b>10.9.0.4</b>:/<b>HN1</b>-shared /mnt/tmp
    # if using NFSv4.1 for this volume, mount with the following command
    mount -t nfs -o sec=sys,vers=4.1 <b>10.9.0.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** verificare l'impostazione del dominio NFS. Verificare che il dominio sia configurato come dominio di Azure NetApp Files predefinito, ad esempio **`defaultv4iddomain.com`** , e che il mapping sia impostato su **nobody** (nessuno).  

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
    mount 10.9.0.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   Per ulteriori informazioni su come modificare i `nfs4_disable_idmapping` parametri, vedere https://access.redhat.com/solutions/1749883 .

6. **[A]** montare i volumi di Azure NetApp files condivisi.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **[1]** montare i volumi specifici del nodo in **hanadb1**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** montare i volumi specifici del nodo in **hanadb2**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** montare i volumi specifici del nodo in **hanadb3**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** verificare che tutti i volumi Hana siano montati con il protocollo NFS versione **NFSv4**.  

    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.9.0.4:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/log/<b>HN1</b>/mnt00002 from 10.9.0.4:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/data/<b>HN1</b>/mnt00002 from 10.9.0.4:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/log/<b>HN1</b>/mnt00001 from 10.9.0.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /usr/sap/<b>HN1</b> from 10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/shared from 10.9.0.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    </code></pre>

## <a name="installation"></a>Installazione  

In questo esempio per la distribuzione di SAP HANA nella configurazione con scalabilità orizzontale con il nodo standby con Azure, è stato usato HANA 2,0 SP4.  

### <a name="prepare-for-hana-installation"></a>Preparare l'installazione di HANA

1. **[A]** prima dell'installazione di Hana, impostare la password radice. È possibile disabilitare la password radice dopo che l'installazione è stata completata. Comando Execute `root` As `passwd` .  

2. **[1]** verificare che sia possibile accedere tramite SSH a **hanadb2** e **hanadb3**senza che venga richiesta una password.  

    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** installare pacchetti aggiuntivi, necessari per Hana 2,0 SP4. Per altre informazioni, vedere la nota SAP [2593824](https://launchpad.support.sap.com/#/notes/2593824). 

    <pre><code>
    yum install libgcc_s1 libstdc++6 compat-sap-c++-7 libatomic1 
    </code></pre>

4. **[2], [3]** Modificare la proprietà di SAP HANA `data` e `log` directory in **HN1**ADM.   

    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

5. **[A]** disabilitare temporaneamente il firewall, in modo che non interferisca con l'installazione di Hana. È possibile riabilitarla, al termine dell'installazione di HANA. 
   <pre><code>
    # Execute as root
    systemctl stop firewalld
    systemctl disable firewalld
   </code></pre>

### <a name="hana-installation"></a>Installazione di HANA

1. **[1]** installare SAP Hana seguendo le istruzioni riportate nella [Guida all'installazione e all'aggiornamento di SAP Hana 2,0](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html). In questo esempio viene installato SAP HANA la scalabilità orizzontale con un nodo master, un ruolo di lavoro e un nodo standby.  

   a. Avviare il programma **hdblcm** dalla directory del software di installazione di Hana. Usare il `internal_network` parametro e passare lo spazio degli indirizzi per la subnet, che viene usato per la comunicazione tra nodi Hana interna.  

    <pre><code>
    ./hdblcm --internal_network=10.9.2.0/26
    </code></pre>

   b. Al prompt dei comandi, immettere i valori seguenti:

     * Per **scegliere un'azione**: immettere **1** (per l'installazione)
     * Per **ulteriori componenti per l'installazione**: immettere **2, 3**
     * Per percorso di installazione: premere INVIO (il valore predefinito è/Hana/Shared)
     * Per **nome host locale**: premere INVIO per accettare il valore predefinito
     * In **aggiungere gli host al sistema?**: immettere **y**
     * Per **i nomi host delimitati da virgole da aggiungere**: immettere **hanadb2, hanadb3**
     * Per **nome utente radice** [root]: premere INVIO per accettare il valore predefinito
     * Per i ruoli per l'host hanadb2: immettere **1**  (per il ruolo di lavoro)
     * Per **gruppo di failover host** per host hanadb2 [impostazione predefinita]: premere INVIO per accettare il valore predefinito
     * Per **numero di partizione di archiviazione** per host hanadb2 [<<assign automatically>>]: premere INVIO per accettare il valore predefinito
     * Per **gruppo di lavoro** per host hanadb2 [impostazione predefinita]: premere INVIO per accettare il valore predefinito
     * Per i **ruoli selezionati** per l'host hanadb3: immettere **2** (per standby)
     * Per **gruppo di failover host** per host hanadb3 [impostazione predefinita]: premere INVIO per accettare il valore predefinito
     * Per **gruppo di lavoro** per host hanadb3 [impostazione predefinita]: premere INVIO per accettare il valore predefinito
     * Per **SAP Hana ID sistema**: immettere **HN1**
     * Per **numero di istanza** [00]: immettere **03**
     * Per il **gruppo di lavoro host locale** [impostazione predefinita]: premere INVIO per accettare il valore predefinito
     * Per **Seleziona utilizzo sistema/immettere l'indice [4]**: immettere **4** (per personalizzato)
     * Per il **percorso dei volumi di dati** [/Hana/data/HN1]: premere INVIO per accettare il valore predefinito
     * Per il **percorso dei volumi di log** [/Hana/log/HN1]: premere INVIO per accettare il valore predefinito
     * Per **limitare l'allocazione di memoria massima?** [n]: immettere **n**
     * Per **nome host certificato per host hanadb1** [hanadb1]: premere INVIO per accettare il valore predefinito
     * Per **nome host certificato per host hanadb2** [hanadb2]: premere INVIO per accettare il valore predefinito
     * Per **nome host certificato per host hanadb3** [hanadb3]: premere INVIO per accettare il valore predefinito
     * Per la **password dell'amministratore di sistema (hn1adm)**: immettere la password
     * Per la **password utente del database di sistema (System)**: immettere la password del sistema
     * Per **Conferma password utente database di sistema (sistema)**: immettere la password del sistema
     * Per **riavviare il sistema dopo il riavvio del computer?** [n]: immettere **n** 
     * Per **continuare (y/n)**: convalidare il riepilogo e, in caso di esito positivo, immettere **y**


2. **[1]** verificare global.ini  

   Visualizzare global.ini e assicurarsi che sia attiva la configurazione per la comunicazione interna SAP HANA tra nodi. Verificare la sezione **comunicazione** . Deve avere lo spazio degli indirizzi per la `hana` subnet e `listeninterface` deve essere impostato su `.internal` . Verificare la sezione **internal_hostname_resolution** . Deve avere gli indirizzi IP per le macchine virtuali HANA che appartengono alla `hana` subnet.  

   <pre><code>
    sudo cat /usr/sap/<b>HN1</b>/SYS/global/hdb/custom/config/global.ini
    # Example 
    #global.ini last modified 2019-09-10 00:12:45.192808 by hdbnameserve
    [communication]
    internal_network = <b>10.9.2.0/26</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.9.2.4</b> = <b>hanadb1</b>
    <b>10.9.2.5</b> = <b>hanadb2</b>
    <b>10.9.2.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** aggiungere il mapping dell'host per assicurarsi che gli indirizzi IP del client siano utilizzati per la comunicazione client. Aggiungere `public_host_resolution` la sezione e aggiungere gli indirizzi IP corrispondenti dalla subnet del client.  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.9.1.5</b>
    map_<b>hanadb2</b> = <b>10.9.1.6</b>
    map_<b>hanadb3</b> = <b>10.9.1.7</b>
   </code></pre>

4. **[1]** riavviare SAP HANA per attivare le modifiche.  

   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** verificare che l'interfaccia client utilizzerà gli indirizzi IP dalla `client` subnet per la comunicazione.  

   <pre><code>
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.9.1.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.9.1.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.9.1.5</b>"
   </code></pre>

   Per informazioni su come verificare la configurazione, vedere la nota SAP [2183363-configurazione di SAP Hana rete interna](https://launchpad.support.sap.com/#/notes/2183363).  

5. **[A]** abilitare di nuovo il firewall.  
   - Arrestare HANA
       <pre><code>
        sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
       </code></pre>
   - Abilitare di nuovo il firewall
       <pre><code>
        # Execute as root
        systemctl start firewalld
        systemctl enable firewalld
       </code></pre>

   - Aprire le porte del firewall necessarie

       > [!IMPORTANT]
       > Creare regole del firewall per consentire la comunicazione tra nodi HANA e il traffico client. Le porte necessarie sono elencate in [TCP/IP Ports of All SAP Products](https://help.sap.com/viewer/ports) (Porte TCP/IP per tutti i prodotti SAP). I comandi seguenti sono solo un esempio. In questo scenario con il numero di sistema usato 03.

       <pre><code>
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
       </code></pre>

   - Avvia HANA
       <pre><code>
        sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
       </code></pre>

6. Per ottimizzare SAP HANA per l'archiviazione Azure NetApp Files sottostante, impostare i parametri di SAP HANA seguenti:

   - `max_parallel_io_requests`**128**
   - `async_read_submit`**il**
   - `async_write_submit_active`**il**
   - `async_write_submit_blocks`**tutto**

   Per altre informazioni, vedere [applicazioni SAP NetApp su Microsoft Azure con Azure NetApp files][anf-sap-applications-azure]. 

   A partire da SAP HANA sistemi 2,0, è possibile impostare i parametri in `global.ini` . Per altre informazioni, vedere la nota SAP [1999930](https://launchpad.support.sap.com/#/notes/1999930).  
   
   Per SAP HANA versioni dei sistemi 1,0 SPS12 e versioni precedenti, questi parametri possono essere impostati durante l'installazione, come descritto nella nota SAP [2267798](https://launchpad.support.sap.com/#/notes/2267798).  

7. Lo spazio di archiviazione usato da Azure NetApp Files ha un limite di dimensioni del file di 16 terabyte (TB). SAP HANA non è in grado di riconoscere in modo implicito il limite di archiviazione e non crea automaticamente un nuovo file di dati quando viene raggiunto il limite delle dimensioni del file pari a 16 TB. Poiché SAP HANA tenta di espandere il file oltre i 16 TB, il tentativo provocherà errori e, infine, in un arresto anomalo del server di indice. 

   > [!IMPORTANT]
   > Per evitare che SAP HANA tenti di espandere i file di dati oltre il [limite di 16 TB](../../../azure-netapp-files/azure-netapp-files-resource-limits.md) del sottosistema di archiviazione, impostare i parametri seguenti in `global.ini` .  
   > - datavolume_striping = true
   > - datavolume_striping_size_gb = 15000 per ulteriori informazioni, vedere la nota SAP [2400005](https://launchpad.support.sap.com/#/notes/2400005).
   > Tenere presente la nota SAP [2631285](https://launchpad.support.sap.com/#/notes/2631285). 

## <a name="test-sap-hana-failover"></a>Test SAP HANA failover 

1. Simulare un arresto anomalo di un nodo in un nodo del ruolo di lavoro SAP HANA. Eseguire le operazioni seguenti: 

   a. Prima di simulare l'arresto anomalo del nodo, eseguire i comandi seguenti come **HN1**ADM per acquisire lo stato dell'ambiente:  

   <pre><code>
    # Check the landscape status
    python /usr/sap/HN1/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |

    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
   </code></pre>

   b. Per simulare un arresto anomalo del nodo, eseguire il comando seguente come radice nel nodo del ruolo di lavoro, che in questo caso è **hanadb2** :  
   
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   c. Monitorare il sistema per il completamento del failover. Al termine del failover, acquisire lo stato, che dovrebbe essere simile al seguente:  

    <pre><code>
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | no     | info   |          |        |         2 |         0 | default  | default  | master 2   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb3 | yes    | info   |          |        |         0 |         2 | default  | default  | master 3   | slave      | standby     | slave       | standby | worker  | default | default |
   </code></pre>

   > [!IMPORTANT]
   > Quando un nodo sperimenta il kernel panic, evitare ritardi con SAP HANA failover impostando `kernel.panic` su 20 secondi in *tutte le* macchine virtuali Hana. La configurazione viene eseguita in `/etc/sysctl` . Riavviare le macchine virtuali per attivare la modifica. Se questa modifica non viene eseguita, il failover può richiedere 10 o più minuti quando si verifica il panico del kernel.  

2. Per terminare il server dei nomi, eseguire le operazioni seguenti:

   a. Prima del test, controllare lo stato dell'ambiente eseguendo i comandi seguenti come **HN1**ADM:  

   <pre><code>
    #Landscape status 
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
   </code></pre>

   b. Eseguire i comandi seguenti come **HN1**ADM sul nodo master attivo, che è **hanadb1** in questo caso:  

    <pre><code>
        hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
    </code></pre>
    
    Il **hanadb3** del nodo di standby assumerà il nome di nodo master. Di seguito è riportato lo stato della risorsa dopo il completamento del test di failover:  

    <pre><code>
     # Check the instance status
     sapcontrol -nr 03  -function GetSystemInstanceList
     GetSystemInstanceList
     OK
     hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
     hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
     hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
     hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
     # Check the landscape status
     python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
     | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
     |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
     |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
     | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
     | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
     | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
     | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
    </code></pre>

   c. Riavviare l'istanza di HANA in **hanadb1** , ovvero nella stessa macchina virtuale in cui è stato terminato il server dei nomi. Il nodo **hanadb1** si riunirà all'ambiente e manterrà il relativo ruolo standby.  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Dopo che SAP HANA è stato avviato in **hanadb1**, prevedere lo stato seguente:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   d. Anche in questo caso, terminare il nome del server sul nodo master attualmente attivo, ovvero sul nodo **hanadb3**.  
   
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   Il nodo **hanadb1** riprenderà il ruolo del nodo master. Al termine del test di failover, lo stato avrà un aspetto simile al seguente:

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

   e. Avviare SAP HANA in **hanadb3**, che sarà pronto a fungere da nodo standby.  

   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Dopo che SAP HANA è stato avviato in **hanadb3**, lo stato avrà un aspetto simile al seguente:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

## <a name="next-steps"></a>Passaggi successivi

* [Pianificazione e implementazione di macchine virtuali di Azure per SAP][planning-guide]
* [Distribuzione di Macchine virtuali di Azure per SAP][deployment-guide]
* [Distribuzione DBMS di Macchine virtuali di Azure per SAP][dbms-guide]
* Per informazioni su come stabilire la disponibilità elevata e pianificare il ripristino di emergenza di SAP HANA nelle VM di Azure, vedere [disponibilità elevata di SAP Hana in macchine virtuali di Azure][sap-hana-ha].
