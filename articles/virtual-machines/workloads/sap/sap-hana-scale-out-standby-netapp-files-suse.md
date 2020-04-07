---
title: Scalabilità orizzontale di SAP HANA con standby con i file NetApp di Azure su SLES Documenti Microsoft
description: Guida alla disponibilità elevata per SAP NetWeaver in SUSE Linux Enterprise Server con file NetApp di Azure per applicazioni SAP
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
ms.date: 04/06/2020
ms.author: radeltch
ms.openlocfilehash: b584341931299e408b596bd6a66d1da4580cfffe
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754788"
---
# <a name="deploy-a-sap-hana-scale-out-system-with-standby-node-on-azure-vms-by-using-azure-netapp-files-on-suse-linux-enterprise-server"></a>Distribuire un sistema di scalabilità orizzontale SAP HANA con nodo standby nelle macchine virtuali di Azure usando i file di Azure NetApp in SUSE Linux Enterprise ServerDeploy a SAP HANA scale-out system with standby node on Azure VMs by using Azure NetApp Files on SUSE Linux Enterprise Server 

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

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


Questo articolo descrive come distribuire un sistema SAP HANA a disponibilità elevata in una configurazione con scalabilità orizzontale con standby nelle macchine virtuali di Azure usando I file di Rete di [Azure](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/) per i volumi di archiviazione condivisa.  

Nelle configurazioni di esempio, nei comandi di installazione e così via, l'istanza HANA è **03** e l'ID di sistema HANA è **HN1**. Gli esempi sono basati su HANA 2.0 SP4 e SUSE Linux Enterprise Server per SAP 12 SP4. 

Prima di iniziare, fare riferimento alle note e ai documenti SAP seguenti:

* [Documentazione sui file NetApp di AzureAzure NetApp Files documentation][anf-azure-doc] 
* SAP Note [1928533] include:  
  * Un elenco delle dimensioni delle macchine virtuali di Azure supportate per la distribuzione del software SAP
  * Importanti informazioni sulla capacità per le dimensioni delle VM di Azure
  * Software SAP e combinazioni di sistemi operativi e database supportati
  * La versione del kernel SAP richiesta per Windows e Linux in Microsoft Azure
* NOTa SAP [2015553:]elenca i prerequisiti per le distribuzioni di software SAP supportati da SAP in Azure
* Nota SAP [2205917:]contiene le impostazioni del sistema operativo consigliate per SUSE Linux Enterprise Server per applicazioni SAP
* Nota SAP [1944799]: contiene linee guida SAP per SUSE Linux Enterprise Server for SAP Applications
* NOTa SAP [2178632]: contiene informazioni dettagliate su tutte le metriche di monitoraggio segnalate per SAP in Azure
* Nota SAP [2191498:]contiene la versione dell'agente host SAP necessaria per Linux in Azure
* Nota SAP [2243692:]contiene informazioni sulle licenze SAP su Linux in Azure
* Nota SAP [1984787]: contiene informazioni generali su SUSE Linux Enterprise Server 12
* Nota SAP [1999351]: contiene informazioni aggiuntive sulla risoluzione dei problemi per l'estensione di monitoraggio avanzato di Azure per SAP
* Nota SAP [1900823:]contiene informazioni sui requisiti di archiviazione SAP HANA
* [SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Contiene tutte le note SAP necessarie per Linux
* [Pianificazione e implementazione di Macchine virtuali di Azure per SAP in Linux][planning-guide]
* [Distribuzione di Macchine virtuali di Azure per SAP in Linux][deployment-guide]
* [Distribuzione DBMS di Macchine virtuali di Azure per SAP in Linux][dbms-guide]
* [SUSE SAP HA Best Practice Guides][suse-ha-guide]: Contiene tutte le informazioni necessarie per configurare NetWeaver High Availability e SAP HANA System Replication in locale (da utilizzare come linea di base generale; forniscono informazioni molto più dettagliate)
* [Note sulla versione di SUSE High Availability Extension 12 SP3][suse-ha-12sp3-relnotes]
* [Applicazioni SAP NetApp in Microsoft Azure con i file NetApp di Azure][anf-sap-applications-azure]


## <a name="overview"></a>Panoramica

Un metodo per ottenere la disponibilità elevata HANA consiste nel configurare il failover automatico dell'host. Per configurare il failover automatico dell'host, aggiungere una o più macchine virtuali al sistema HANA e configurarle come nodi standby. Quando si verifica un errore nel nodo attivo, un nodo standby subentra automaticamente. Nella configurazione presentata con le macchine virtuali di Azure è possibile eseguire il failover automatico usando [NFS nei file NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)di Azure.  

> [!NOTE]
> Il nodo standby deve accedere a tutti i volumi del database. I volumi HANA devono essere montati come volumi NFSv4. Il meccanismo di blocco basato sul lease di file `I/O` migliorato nel protocollo NFSv4 viene utilizzato per la scherma. 

> [!IMPORTANT]
> Per compilare la configurazione supportata, è necessario distribuire i volumi di log e dati HANA come volumi NFSv4.1 e montarli utilizzando il protocollo NFSv4.1. La configurazione di failover automatico dell'host HANA con nodo standby non è supportata con NFSv3.

![Panoramica della disponibilità elevata di SAP NetWeaver](./media/high-availability-guide-suse-anf/sap-hana-scale-out-standby-netapp-files-suse.png)

Nel diagramma precedente, che segue le raccomandazioni di rete SAP HANA, tre subnet sono rappresentate all'interno di una rete virtuale di Azure:In the preceding diagram, which follows SAP HANA network recommendations, three subnets are represented within one Azure virtual network: 
* Per la comunicazione con il cliente
* Per la comunicazione con il sistema di storage
* Per la comunicazione interna tra nodi HANA

I volumi NetApp di Azure si trovano in una subnet separata, [delegata a File NetApp di Azure](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

Per questa configurazione di esempio, le subnet sono:For this example configuration, the subnets are:  

  - `client`10.23.0.0/24  
  - `storage`10.23.2.0/24  
  - `hana`10.23.3.0/24  
  - `anf`10.23.1.0/26  

## <a name="set-up-the-azure-netapp-files-infrastructure"></a>Configurare l'infrastruttura dei file NetApp di AzureSet up the Azure NetApp Files infrastructure 

Prima di procedere con l'installazione dell'infrastruttura dei file NetApp di Azure, acquisire familiarità con la documentazione relativa ai [file NetApp di Azure.][anf-azure-doc] 

File NetApp di Azure è disponibile in diverse aree di [Azure.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp) Verificare se l'area di Azure selezionata offre i file NetApp di Azure.Check to see whether your selected Azure region offers Azure NetApp Files.  

Per informazioni sulla disponibilità dei file NetApp di Azure in base all'area di Azure, vedere Disponibilità dei file NetApp di Azure per area di Azure.For information about the availability of Azure NetApp Files by Azure region, see [Azure NetApp Files Availability by Azure Region.][anf-avail-matrix]  

Prima di distribuire File NetApp di Azure, richiedere l'onboarding ai file NetApp di Azure accedendo alle istruzioni per la registrazione dei [file NetApp di Azure.][anf-register] 

### <a name="deploy-azure-netapp-files-resources"></a>Distribuire le risorse dei file NetApp di AzureDeploy Azure NetApp Files resources  

Le istruzioni seguenti presuppongono che la [rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)di Azure sia già stata distribuita. Le risorse e le macchine virtuali dei file NetApp di Azure, in cui verranno montate le risorse File NetApp di Azure, devono essere distribuite nella stessa rete virtuale di Azure o nelle reti virtuali di Azure con peered.  

1. Se le risorse non sono già state distribuite, richiedere [l'onboarding afile NetApp di Azure](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register).  

2. Creare un account NetApp nell'area di Azure selezionata seguendo le istruzioni in [Creare un account NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  

3. Configurare un pool di capacità dei file NetApp di Azure seguendo le istruzioni in Configurare un pool di capacità dei file netapp di Azure.Set up an Azure NetApp Files capacity pool by following the instructions in [Set up an Azure NetApp Files capacity pool](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  

   L'architettura HANA presentata in questo articolo usa un singolo pool di capacità dei file NetApp di Azure a livello di *servizio Ultra.The* HANA architecture presented in this article uses a single Azure NetApp Files capacity pool at the Ultra Service level. Per i carichi di lavoro HANA in Azure, è consigliabile usare un livello di [servizio](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)Azure NetApp Files *Ultra* o *Premium.*  

4. Delegare una subnet a File NetApp di Azure, come descritto nelle istruzioni in [Delegare una subnet afile NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)di Azure .  

5. Distribuire i volumi dei file NetApp di Azure seguendo le istruzioni in [Creare un volume NFS per i file NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)di Azure.  

   Durante la distribuzione dei volumi, assicurarsi di selezionare la versione **NFSv4.1.** Attualmente, l'accesso a NFSv4.1 richiede l'inserimento nella whitelist aggiuntivo. Distribuire i volumi nella [subnet](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)dei file netApp di Azure designata. 
   
   Tenere presente che le risorse File NetApp di Azure e le macchine virtuali di Azure devono trovarsi nella stessa rete virtuale di Azure o in reti virtuali di Azure con peered. Ad esempio, **HN1**-data-mnt00001, **HN1**-log-mnt00001 e così via, sono i nomi dei volumi e nfs://10.23.1.5/**HN1**-data-mnt00001, nfs://10.23.1.4/**HN1**-log-mnt00001 e così via, sono i percorsi dei file di Azure NetApp.  

   * volume **HN1**-data-mnt00001 (nfs://10.23.1.5/**HN1**-data-mnt00001)
   * volume **HN1**-data-mnt00002 (nfs://10.23.1.6/**HN1**-data-mnt00002)
   * volume **HN1**-log-mnt00001 (nfs://10.23.1.4/**HN1**-log-mnt00001)
   * volume **HN1**-log-mnt00002 (nfs://10.23.1.6/**HN1**-log-mnt00002)
   * volume **HN1**-condiviso (nfs://10.23.1.4/**HN1**-condiviso)
   
   In questo esempio è stato usato un volume separato di file NetApp di Azure per ogni volume di dati e log HANA. Per una configurazione più ottimizzata in termini di costi su sistemi più piccoli o non produttivi, è possibile posizionare tutti i supporti dei dati e tutti i log montati su un singolo volume.  

### <a name="important-considerations"></a>Considerazioni importanti

Durante la creazione dei file NetApp di Azure per SAP NetWeaver nell'architettura SUSE High Availability, tenere presenti le considerazioni importanti seguenti:

- Il pool di capacità minima è 4 tebibyte (TiB).  
- La dimensione minima del volume è 100 gibibyte (GiB).
- I file NetApp di Azure e tutte le macchine virtuali in cui verranno montati i volumi di File NetApp di Azure devono trovarsi nella stessa rete virtuale di Azure o in [reti virtuali con peerè](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) nella stessa area.  
- La rete virtuale selezionata deve avere una subnet delegata a File NetApp di Azure.The selected virtual network must have a subnet that's delegated to Azure NetApp Files.
- La velocità effettiva di un volume File NetApp di Azure è una funzione della quota di volume e del livello di servizio, come documentato nel [livello di servizio per i file NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)di Azure. Quando si ridimensionano i volumi NETApp di HANA Azure, assicurarsi che la velocità effettiva risultante soddisfi i requisiti di sistema HANA.  
- Con i criteri di [esportazione](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)File di Azure NetApp è possibile controllare i client consentiti, il tipo di accesso (lettura/scrittura, sola lettura e così via). 
- La funzionalità File NetApp di Azure non è ancora in grado di riconoscere le aree. Attualmente, la funzionalità non è distribuita in tutte le zone di disponibilità in un'area di Azure.Currently, the feature isn't deployed in all availability zones in an Azure region. Tenere presente le potenziali implicazioni di latenza in alcune aree di Azure.Be aware of the potential latency implications in some Azure regions.  
-  

> [!IMPORTANT]
> Per i carichi di lavoro SAP HANA, una bassa latenza è fondamentale. Collaborare con il rappresentante Microsoft per assicurarsi che le macchine virtuali e i volumi di File NetApp di Azure vengano distribuiti in prossimità.  

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Ridimensionamento per il database HANA nei file NetApp di AzureSizing for HANA database on Azure NetApp Files

La velocità effettiva di un volume File NetApp di Azure è una funzione delle dimensioni del volume e del livello di servizio, come documentato nel livello di servizio [per i file NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)di Azure. 

Quando si progetta l'infrastruttura per SAP in Azure, tenere presente alcuni requisiti minimi di archiviazione da parte di SAP, che si traducono in caratteristiche di velocità effettiva minima:As you design the infrastructure for SAP in Azure, be aware of some minimum storage requirements by SAP, which translate into minimum throughput characteristics:

- Abilitare la lettura/scrittura su /hana/log di 250 megabyte al secondo (MB/s) con dimensioni di I/O di 1 MB.  
- Abilitare l'attività di lettura di almeno 400 MB/s per /hana/dati per dimensioni I/O da 16 MB e 64 MB.  
- Abilitare l'attività di scrittura di almeno 250 MB/s per /hana/dati con dimensioni I/O da 16 MB e 64 MB. 

I limiti di velocità effettiva dei file netApp di Azure per 1 TiB della quota del volume sono:The [Azure NetApp Files throughput limits](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) per 1 TiB of volume quota are:
- Livello di archiviazione Premium - 64 MiB/s  
- Livello di archiviazione ultra - 128 MiB/s  

Per soddisfare i requisiti minimi di velocità effettiva SAP per i dati e il log e le linee guida per /hana/shared, le dimensioni consigliate sono:

| Volume | Dimensione di<br>Livello Archiviazione Premium | Dimensione di<br>Livello di archiviazione ultra | Protocollo NFS supportato |
| --- | --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v4.1 (in questo v) |
| /hana/data | 6.3 TiB (in inglese) | 3.2 TiB | v4.1 (in questo v) |
| /hana/shared | Numero massimo (512 GB, 1xRAM) per 4 nodi di lavoroMax (512 GB, 1xRAM) per 4 worker nodes | Numero massimo (512 GB, 1xRAM) per 4 nodi di lavoroMax (512 GB, 1xRAM) per 4 worker nodes | v3 o v4.1 |

La configurazione SAP HANA per il layout presentato in questo articolo, usando il livello di archiviazione Ultra di File di Azure di Azure, sarebbe:The SAP HANA configuration for the layout that's presented in this article, using Azure NetApp Files Ultra Storage tier, would be:

| Volume | Dimensione di<br>Livello di archiviazione ultra | Protocollo NFS supportato |
| --- | --- | --- |
| /hana/log/mnt00001 | 2 TiB | v4.1 (in questo v) |
| /hana/log/mnt00002 | 2 TiB | v4.1 (in questo v) |
| /hana/dati/mnt00001 | 3.2 TiB | v4.1 (in questo v) |
| /hana/dati/mnt00002 | 3.2 TiB | v4.1 (in questo v) |
| /hana/shared | 2 TiB | v3 o v4.1 |

> [!NOTE]
> I consigli di dimensionamento dei file di Azure NetApp indicati di seguito sono destinati a soddisfare i requisiti minimi consigliati da SAP per i provider di infrastruttura. Nelle distribuzioni reali dei clienti e negli scenari di carico di lavoro, queste dimensioni potrebbero non essere sufficienti. Usare questi consigli come punto di partenza e adattarsi in base ai requisiti del carico di lavoro specifico.  

> [!TIP]
> È possibile ridimensionare i volumi dei file NetApp di Azure in modo dinamico, senza dover *smontare* i volumi, arrestare le macchine virtuali o arrestare SAP HANA.You can resize Azure NetApp Files volumes dynamically, without having to smount the volumes, stop the virtual machines, or stop SAP HANA. Questo approccio consente la flessibilità per soddisfare le richieste di velocità effettiva previste e impreviste dell'applicazione.

## <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Distribuire macchine virtuali Linux tramite il portale di AzureDeploy Linux virtual machines via the Azure portal

Innanzitutto è necessario creare i volumi dei file NetApp di Azure.First you need to create the Azure NetApp Files volumes. Eseguire quindi le operazioni seguenti:
1. Creare le subnet della rete virtuale di Azure nella rete virtuale di Azure.Create the [Azure virtual network subnets](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) in your [Azure virtual network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). 
1. Distribuire le macchine virtuali. 
1. Creare le interfacce di rete aggiuntive e collegare le interfacce di rete alle macchine virtuali corrispondenti.  

   Ogni macchina virtuale dispone di tre interfacce di rete,`client`che `storage` `hana`corrispondono alle tre subnet di rete virtuale di Azure ( , e ). 

   Per altre informazioni, vedere [Creare una macchina virtuale Linux in Azure con più schede di interfaccia](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics)di rete.  

> [!IMPORTANT]
> Per i carichi di lavoro SAP HANA, una bassa latenza è fondamentale. Per ottenere una bassa latenza, collaborare con il rappresentante Microsoft per assicurarsi che le macchine virtuali e i volumi di File NetApp di Azure siano distribuiti in prossimità. Quando si esegue [l'onboarding del nuovo sistema SAP HANA](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) che usa i file NetApp di SAP HANA Azure, inviare le informazioni necessarie. 
 
Le istruzioni successive presuppongono che siano già stati creati il gruppo di risorse, `client` `storage` la `hana`rete virtuale di Azure e le tre subnet della rete virtuale di Azure: , e . Quando si distribuiscono le macchine virtuali, selezionare la subnet client, in modo che l'interfaccia di rete client sia l'interfaccia primaria nelle macchine virtuali. Sarà inoltre necessario configurare una route esplicita alla subnet delegata File NetApp di Azure tramite il gateway della subnet di archiviazione. 

> [!IMPORTANT]
> Assicurarsi che il sistema operativo selezionato sia certificato SAP per SAP HANA nei tipi di macchine virtuali specifici in uso. Per un elenco dei tipi di VM certificati SAP HANA e delle versioni del sistema operativo per questi tipi, visitare il sito delle [piattaforme IaaS certificate SAP HANA.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Fare clic sui dettagli del tipo di macchina virtuale elencato per ottenere l'elenco completo delle versioni del sistema operativo supportate da SAP HANA per quel tipo.  

1. Creare un set di disponibilità per SAP HANA. Assicurarsi di impostare il dominio di aggiornamento massimo.  

2. Creare tre macchine virtuali (**hanadb1**, **hanadb2**, **hanadb3**) attenendosi alla seguente procedura:  

   a. Usare un'immagine SLES4SAP nella raccolta di Azure supportata per SAP HANA.Use a SLES4SAP image in the Azure gallery that's supported for SAP HANA. In questo esempio è stata utilizzata un'immagine SLES4SAP 12 SP4.  

   b. Selezionare il set di disponibilità creato in precedenza per SAP HANA.  

   c. Selezionare la subnet della rete virtuale di Azure client. Selezionare [Rete accelerata](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).  

   Quando si distribuiscono le macchine virtuali, il nome dell'interfaccia di rete viene generato automaticamente. In queste istruzioni per semplicità si farà riferimento alle interfacce di rete generate automaticamente, che sono collegate alla subnet di rete virtuale di Azure client, come **hanadb1-client**, **hanadb2-client**e **hanadb3-client**. 

3. Creare tre interfacce di rete, una `storage` per ogni macchina virtuale, per la subnet di rete virtuale (in questo esempio, **hanadb1-storage**, **hanadb2-storage**e **hanadb3-storage**).  

4. Creare tre interfacce di rete, una `hana` per ogni macchina virtuale, per la subnet della rete virtuale (in questo **esempio, hanadb1-hana**, **hanadb2-hana**e **hanadb3-hana**).  

5. Collegare le interfacce di rete virtuale appena create alle macchine virtuali corrispondenti eseguendo la procedura seguente:Attach the newly created virtual network interfaces to the corresponding virtual machines by doing the following steps:  

    a. Passare alla macchina virtuale nel portale di [Azure](https://portal.azure.com/#home).  

    b. Nel riquadro sinistro selezionare **Macchine virtuali**. Filtrare in base al nome della macchina virtuale, ad esempio **hanadb1**, e quindi selezionare la macchina virtuale.  

    c. Nel riquadro **Panoramica** selezionare **Interrompi** per deallocare la macchina virtuale.  

    d. Selezionare **Rete**, quindi collegare l'interfaccia di rete. Nell'elenco a discesa **Connetti interfaccia** di rete selezionare `storage` le `hana` interfacce di rete già create per le subnet e .  
    
    e. Selezionare **Salva**. 
 
    f. Ripetere i passaggi da b a e per le altre macchine virtuali (nell'esempio **hanadb2** e **hanadb3**).
 
    g. Lasciare le macchine virtuali in stato di arresto per il momento. Successivamente, abiliteremo la [rete accelerata](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) per tutte le interfacce di rete appena collegate.  

6. Abilitare la rete accelerata per `storage` le `hana` interfacce di rete aggiuntive per le subnet e eseguendo la procedura seguente:  

    a. Aprire [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) nel portale di [Azure.](https://portal.azure.com/#home)  

    b. Eseguire i comandi seguenti per abilitare la rete accelerata per `storage` le `hana` interfacce di rete aggiuntive, che sono collegate alle subnet e .  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-storage</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true

    </code></pre>

7. Avviare le macchine virtuali eseguendo la procedura seguente:Start the virtual machines by doing the following steps:  

    a. Nel riquadro sinistro selezionare **Macchine virtuali**. Filtrare in base al nome della macchina virtuale, ad esempio **hanadb1**, quindi selezionarlo.  

    b. Nel riquadro **Panoramica** selezionare **Avvia**.  

## <a name="operating-system-configuration-and-preparation"></a>Configurazione e preparazione del sistema operativo

Le istruzioni nelle sezioni successive sono precedute da una delle seguenti operazioni:
* **[A]**: Applicabile a tutti i nodi
* **[1]**: Applicabile solo al nodo 1
* **[2]**: Applicabile solo al nodo 2
* **[3]**: Applicabile solo al nodo 3

Configurare e preparare il sistema operativo eseguendo la procedura seguente:

1. **[A]** Gestire i file host nelle macchine virtuali. Includere le voci per tutte le subnet. Per questo esempio sono `/etc/hosts` state aggiunte le voci seguenti.  

    <pre><code>
    # Storage
    10.23.2.4   hanadb1-storage
    10.23.2.5   hanadb2-storage
    10.23.2.6   hanadb3-storage
    # Client
    10.23.0.5   hanadb1
    10.23.0.6   hanadb2
    10.23.0.7   hanadb3
    # Hana
    10.23.3.4   hanadb1-hana
    10.23.3.5   hanadb2-hana
    10.23.3.6   hanadb3-hana
    </code></pre>

2. **[A]** Modificare le impostazioni di configurazione DHCP e cloud per l'interfaccia di rete per l'archiviazione per evitare modifiche indesiderate al nome host.  

    Le istruzioni seguenti presuppongono `eth1`che l'interfaccia di rete di archiviazione sia . 

    <pre><code>
    vi /etc/sysconfig/network/dhcp
    # Change the following DHCP setting to "no"
    DHCLIENT_SET_HOSTNAME="no"
    vi /etc/sysconfig/network/ifcfg-<b>eth1</b>
    # Edit ifcfg-eth1 
    #Change CLOUD_NETCONFIG_MANAGE='yes' to "no"
    CLOUD_NETCONFIG_MANAGE='no'
    </code></pre>

2. **[A]** Aggiungere una route di rete, in modo che la comunicazione con i file NetApp di Azure passi tramite l'interfaccia di rete di archiviazione.  

    Le istruzioni seguenti presuppongono `eth1`che l'interfaccia di rete di archiviazione sia .  

    <pre><code>
    vi /etc/sysconfig/network/ifroute-<b>eth1</b>
    # Add the following routes 
    # RouterIPforStorageNetwork - - -
    # ANFNetwork/cidr RouterIPforStorageNetwork - -
    <b>10.23.2.1</b> - - -
    <b>10.23.1.0/26</b> <b>10.23.2.1</b> - -
    </code></pre>

    Riavviare la macchina virtuale per attivare le modifiche.  

3. **[A]** Preparare il sistema operativo per l'esecuzione di SAP HANA su sistemi NetApp con NFS, come descritto in [Applicazioni SAP NetApp in Microsoft Azure con file di Azure NetApp][anf-sap-applications-azure]. Creare il file di configurazione */etc/sysctl.d/netapp-hana.conf* per le impostazioni di configurazione di NetApp.  

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

4. **[A]** Crea il file di configurazione */etc/sysctl.d/ms-az.conf* con le impostazioni di configurazione di Microsoft per Azure.  

    <pre><code>
    vi /etc/sysctl.d/ms-az.conf
    # Add the following entries in the configuration file
    ipv6.conf.all.disable_ipv6 = 1
    net.ipv4.tcp_max_syn_backlog = 16348
    net.ipv4.ip_local_port_range = 40000 65300
    net.ipv4.conf.all.rp_filter = 0
    sunrpc.tcp_slot_table_entries = 128
    vm.swappiness=10
    </code></pre>

4. **[A]** Regolare le impostazioni sunrpc, come consigliato nelle [applicazioni SAP NetApp in Microsoft Azure usando i file NetApp][anf-sap-applications-azure]di Azure.  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

## <a name="mount-the-azure-netapp-files-volumes"></a>Montare i volumi dei file NetApp di AzureMount the Azure NetApp Files volumes

1. **[A]** Creare punti di montaggio per i volumi del database HANA.  

    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** Creare directory specifiche del nodo per /usr/sap in **HN1**-shared.  

    <pre><code>
    # Create a temporary directory to mount <b>HN1</b>-shared
    mkdir /mnt/tmp
    mount <b>10.23.1.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** Verificare l'impostazione del dominio NFS. Assicurarsi che il dominio sia configurato come dominio predefinito dei **`defaultv4iddomain.com`** file NetApp di Azure, ad esempio e che il mapping sia impostato su **nessuno.**  

    > [!IMPORTANT]
    > Assicurarsi di impostare il `/etc/idmapd.conf` dominio NFS nella macchina virtuale in modo **`defaultv4iddomain.com`** che corrisponda alla configurazione di dominio predefinita nei file NetApp di Azure: . Se esiste una mancata corrispondenza tra la configurazione del dominio nel client NFS (ovvero la macchina virtuale) e il server NFS, ovvero la configurazione di Azure NetApp, `nobody`le autorizzazioni per i file nei volumi NetApp di Azure montati nelle macchine virtuali verranno visualizzate come .  

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

4. **[A]** `nfs4_disable_idmapping`Verificare . Deve essere impostato su **Y**. Per creare la `nfs4_disable_idmapping` struttura di directory in cui si trova, eseguire il comando mount. Non sarà possibile creare manualmente la directory in /sys/modules, perché l'accesso è riservato per il kernel /drivers.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.23.1.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

5. **[A]** Creare manualmente il gruppo SAP HANA e l'utente. Gli ID per il gruppo sapsys e l'utente **hn1**adm devono essere impostati sugli stessi ID, forniti durante l'onboarding. (In questo esempio, gli ID sono impostati su **1001**.) Se gli ID non sono impostati correttamente, non sarà possibile accedere ai volumi. Gli ID per il gruppo sapsys e gli account utente **hn1**adm e sapadm devono essere gli stessi in tutte le macchine virtuali.  

    <pre><code>
    # Create user group 
    sudo groupadd -g 1001 sapsys
    # Create  users 
    sudo useradd <b>hn1</b>adm -u 1001 -g 1001 -d /usr/sap/<b>HN1</b>/home -c "SAP HANA Database System" -s /bin/sh
    sudo useradd sapadm -u 1002 -g 1001 -d /home/sapadm -c "SAP Local Administrator" -s /bin/sh
    # Set the password  for both user ids
    sudo passwd hn1adm
    sudo passwd sapadm
    </code></pre>

6. **[A]** Montare i volumi condivisi di File NetApp di Azure.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.5:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **[1] Montare** i volumi specifici del nodo su **hanadb1**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** Montare i volumi specifici del nodo su **hanadb2**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** Montare i volumi specifici del nodo su **hanadb3**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** Verificare che tutti i volumi HANA siano montati con la versione del protocollo **NFS NFSv4**.  

    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.23.1.5:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.5
    /hana/log/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/data/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/log/<b>HN1</b>/mnt00001 from 10.23.1.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /usr/sap/<b>HN1</b> from 10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /hana/shared from 10.23.1.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    </code></pre>

## <a name="installation"></a>Installazione  

In this example for deploying SAP HANA in scale-out configuration with standby node with Azure, we've used HANA 2.0 SP4.  

### <a name="prepare-for-hana-installation"></a>Preparazione per l'installazione HANA

1. **[A]** Prima dell'installazione di HANA, impostare la password root. È possibile disattivare la password root al termine dell'installazione. Esegui `root` come `passwd`comando .  

2. **[1]** Verificare che sia possibile accedere tramite SSH ad **hanadb2** e **hanadb3**, senza che venga richiesta una password.  

    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** Installare pacchetti aggiuntivi, necessari per HANA 2.0 SP4. Per ulteriori informazioni, vedere Nota SAP [2593824](https://launchpad.support.sap.com/#/notes/2593824). 

    <pre><code>
    sudo zypper install libgcc_s1 libstdc++6 libatomic1 
    </code></pre>

4. **[2], [3]** Modificare la proprietà `data` di `log` SAP HANA e directory in **hn1**adm.   

    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

### <a name="hana-installation"></a>Installazione HANA

1. **[1]** Installare SAP HANA seguendo le istruzioni nella [guida all'installazione e all'aggiornamento di SAP HANA 2.0](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html). In questo esempio viene installata la scalabilità orizzontale SAP HANA con master, un worker e un nodo di standby.  

   a. Avviare il programma **hdblcm** dalla directory del software di installazione HANA. Utilizzare `internal_network` il parametro e passare lo spazio di indirizzi per la subnet, utilizzato per la comunicazione interna tra nodi HANA.  

    <pre><code>
    ./hdblcm --internal_network=10.23.3.0/24
    </code></pre>

   b. Al prompt, immettere i seguenti valori:

     * Per **Scegliere un'azione**: immettere **1** (per l'installazione)
     * Per **Componenti aggiuntivi per l'installazione**: immettere **2, 3**
     * Per il percorso di installazione: premere Invio (il valore predefinito è /hana/shared)
     * Per **Nome host locale**: premere Invio per accettare
     * In **Aggiungere host al sistema?**: immettere **y**
     * Per **i nomi host separati da virgole da aggiungere**: immettere **hanadb2, hanadb3**
     * Per **Nome utente radice** [root]: premere Invio per accettare
     * Per **Root User Password**: inserire la password dell'utente root
     * Per i ruoli per l'host hanadb2: immettere **1** (per il worker)
     * Per Il gruppo di **failover host** per l'host hanadb2 [predefinito]: premere Invio per accettare
     * Per **Numero di partizione di** archiviazione <assign automatically> per l'host hanadb2 [<>]: premere INVIO per accettare
     * Per **Gruppo di lavoro** per host hanadb2 [predefinito]: premere INVIO per accettare il valore predefinito
     * Per **Selezionare i ruoli** per l'host hanadb3: immettere **2** (per lo standby)
     * Per Il gruppo di **failover host** per l'host hanadb3 [predefinito]: premere Invio per accettare
     * Per **Gruppo di lavoro** per host hanadb3 [predefinito]: premere INVIO per accettare il valore predefinito
     * Per **SAP HANA System ID**: immettere **HN1**
     * Per **Numero istanza** [00]: immettere **03**
     * Per **Gruppo di lavoro host locale** [predefinito]: premere INVIO per accettare
     * Per **Selezionare l'utilizzo del sistema / Immettere l'indice [4]**: immettere **4** (per l'utilizzo personalizzato)
     * Per **La posizione dei volumi di dati** [/hana/data/HN1]: premere Invio per accettare
     * Per **Percorso dei volumi di registro** [/hana/log/HN1]: premere Invio per accettare
     * Per **limitare l'allocazione di memoria massima?** [n]: immettere **n**
     * Per **Nome host certificato per host hanadb1** [hanadb1]: premere INVIO per accettare
     * Per **Nome host certificato per host hanadb2** [hanadb2]: premere INVIO per accettare
     * Per **Nome host certificato per host hanadb3** [hanadb3]: premere INVIO per accettare
     * Per **l'amministratore di sistema (hn1adm) Password**: immettere la password
     * Per **System Database User (system) Password**: inserire la password del sistema
     * Per **Conferma password utente (sistema)** del database di sistema : immettere la password del sistema
     * Per riavviare il sistema dopo il **riavvio del computer?** [n]: immettere **n** 
     * Per **Vuoi continuare (y/n)**: convalidare il riepilogo e se tutto sembra buono, immettere **y**


2. **[1]** Verificare global.ini  

   Visualizzare global.ini e assicurarsi che la configurazione per la comunicazione tra nodi SAP HANA interna sia presente. Verificare la sezione **di comunicazione.** Deve avere lo spazio `hana` degli indirizzi `listeninterface` per la `.internal`subnet e deve essere impostato su . Verificare la sezione **internal_hostname_resolution.** Deve avere gli indirizzi IP per le macchine `hana` virtuali HANA che appartengono alla subnet.  

   <pre><code>
    sudo cat /usr/sap/<b>HN1</b>/SYS/global/hdb/custom/config/global.ini
    # Example 
    #global.ini last modified 2019-09-10 00:12:45.192808 by hdbnameserve
    [communication]
    internal_network = <b>10.23.3/24</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.23.3.4</b> = <b>hanadb1</b>
    <b>10.23.3.5</b> = <b>hanadb2</b>
    <b>10.23.3.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** Aggiungere il mapping dell'host per garantire che gli indirizzi IP del client vengano utilizzati per la comunicazione client. Aggiungere `public_host_resolution`la sezione e aggiungere gli indirizzi IP corrispondenti dalla subnet client.  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.23.0.5</b>
    map_<b>hanadb2</b> = <b>10.23.0.6</b>
    map_<b>hanadb3</b> = <b>10.23.0.7</b>
   </code></pre>

4. **[1]** Riavviare SAP HANA per attivare le modifiche.  

   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** Verificare che l'interfaccia client utilizzi `client` gli indirizzi IP della subnet per la comunicazione.  

   <pre><code>
    sudo -u hn1adm /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.23.0.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.23.0.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.23.0.5</b>"
   </code></pre>

   Per informazioni su come verificare la configurazione, vedere NOTa SAP [2183363 - Configurazione della rete interna SAP HANA](https://launchpad.support.sap.com/#/notes/2183363).  

6. Per ottimizzare SAP HANA per l'archiviazione dei file NetApp di Azure sottostante, impostare i parametri SAP HANA seguenti:To optimize SAP HANA for the underlying Azure NetApp Files storage, set the following SAP HANA parameters:

   - `max_parallel_io_requests`**128 Del 12**
   - `async_read_submit`**su**
   - `async_write_submit_active`**su**
   - `async_write_submit_blocks`**tutti**

   Per altre informazioni, vedere [Applicazioni SAP NetApp in Microsoft Azure con l'uso][anf-sap-applications-azure]dei file NetApp di Azure.For more information, see NetApp SAP Applications on Microsoft Azure using Azure NetApp Files . 

   A partire dai sistemi SAP HANA 2.0, è possibile impostare i parametri in `global.ini`. Per ulteriori informazioni, vedere Nota SAP [1999930](https://launchpad.support.sap.com/#/notes/1999930).  
   
   Per le versioni dei sistemi SAP HANA 1.0 SPS12 e precedenti, questi parametri possono essere impostati durante l'installazione, come descritto nella nota SAP [2267798](https://launchpad.support.sap.com/#/notes/2267798).  

7. Lo spazio di archiviazione usato dai file NetApp di Azure ha un limite di dimensione del file di 16 terabyte (TB). SAP HANA non è implicitamente a conoscenza del limite di archiviazione e non crea automaticamente un nuovo file di dati quando viene raggiunto il limite di dimensione del file di 16 TB. Man mano che SAP HANA tenta di aumentare l'entità del file oltre i 16 TB, tale tentativo genererà errori e, infine, un arresto anomalo del server di indicizzazione. 

   > [!IMPORTANT]
   > Per impedire a SAP HANA di tentare di aumentare i file di dati `global.ini`oltre il limite di [16 TB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-resource-limits) del sottosistema di archiviazione, impostare i parametri seguenti in .  
   > - datavolume_striping : true
   > - datavolume_striping_size_gb 15000 Per ulteriori informazioni, vedere nota SAP [2400005](https://launchpad.support.sap.com/#/notes/2400005).
   > Tenere presente SAP Note [2631285](https://launchpad.support.sap.com/#/notes/2631285). 

## <a name="test-sap-hana-failover"></a>Testare il failover SAP HANA 

1. Simulare un arresto anomalo del nodo in un nodo di lavoro SAP HANA. Eseguire le operazioni seguenti: 

   a. Prima di simulare l'arresto anomalo del nodo, eseguire i comandi seguenti come **hn1**adm per acquisire lo stato dell'ambiente:  

   <pre><code>
    # Check the landscape status
    python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
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

   b. Per simulare un arresto anomalo del nodo, eseguire il comando seguente come radice nel nodo di lavoro, ovvero hanadb2 in questo caso:To simulate a node crash, run the following command as root on the worker node, which is **hanadb2** in this case:  
   
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   c. Monitorare il sistema per il completamento del failover. Al termine del failover, acquisire lo stato, che dovrebbe essere simile al seguente:When the failover has been completed, capture the status, which should look like the following:  

    <pre><code>
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    # Check the landscape status
    /usr/sap/HN1/HDB03/exe/python_support> python landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | no     | info   |          |        |         2 |         0 | default  | default  | master 2   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb3 | yes    | info   |          |        |         0 |         2 | default  | default  | master 3   | slave      | standby     | slave       | standby | worker  | default | default |
   </code></pre>

   > [!IMPORTANT]
   > Quando un nodo si verifica il panico del `kernel.panic` kernel, evitare ritardi con il failover SAP HANA impostando su 20 secondi in *tutte le* macchine virtuali HANA. La configurazione viene `/etc/sysctl`eseguita in . Riavviare le macchine virtuali per attivare la modifica. Se questa modifica non viene eseguita, il failover può richiedere 10 o più minuti quando un nodo si verifica il panico del kernel.  

2. Uccidere il server dei nomi eseguendo le operazioni seguenti:

   a. Prima del test, controllare lo stato dell'ambiente eseguendo i seguenti comandi come **hn1**adm:  

   <pre><code>
    #Landscape status 
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
   </code></pre>

   b. Eseguire i seguenti comandi come **adm hn1**sul nodo master attivo, che in questo caso è **hanadb1:**  

    <pre><code>
        hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
    </code></pre>
    
    Il nodo standby **hanadb3** assumerà il controllo del nodo master. Ecco lo stato della risorsa dopo il completamento del test di failover:Here is the resource state after the failover test is completed:  

    <pre><code>
        # Check the instance status
        sapcontrol -nr 03 -function GetSystemInstanceList
        GetSystemInstanceList
        OK
        hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
        hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
        hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
        hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
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

   c. Riavviare l'istanza HANA in **hanadb1,** ovvero nella stessa macchina virtuale in cui è stato ucciso il server dei nomi. Il nodo **hanadb1** si unirà nuovamente all'ambiente e manterrà il proprio ruolo di standby.  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Dopo l'avvio di SAP HANA in **hanadb1**, si prevede il seguente stato:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03 -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   d. Ancora una volta, uccidere il server dei nomi sul nodo master attualmente attivo, ovvero sul nodo **hanadb3**.  
   
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   Il nodo **hanadb1** riprenderà il ruolo di nodo master. Al termine del test di failover, lo stato sarà simile al seguente:After the failover test has been completed, the status will look like this:

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
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

   e. Avviare SAP HANA in **hanadb3**, che sarà pronto per essere utilizzato come nodo di standby.  

   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Dopo l'avvio di SAP HANA su **hanadb3**, lo stato sarà simile al seguente:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
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

* [Pianificazione e implementazione di Macchine virtuali di Azure per SAP][planning-guide]
* [Distribuzione di macchine virtuali di Azure per SAP][deployment-guide]
* [Distribuzione DBMS di Macchine virtuali di Azure per SAP][dbms-guide]
* Per informazioni su come stabilire la disponibilità elevata e pianificare il ripristino di emergenza di SAP HANA nelle macchine virtuali di Azure, vedere Disponibilità elevata di SAP HANA in Macchine virtuali di Azure.To learn how to establish high availability and plan for disaster recovery of SAP HANA on Azure VMs, see [High Availability of SAP HANA on Azure Virtual Machines (VMs)][sap-hana-ha].
