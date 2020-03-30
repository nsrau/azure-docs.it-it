---
title: Scalabilità orizzontale di SAP HANA con standby con i file NetApp di Azure su RHEL Documenti Microsoft
description: Guida alla disponibilità elevata per SAP NetWeaver su Red Hat Enterprise Linux con file NetApp di Azure per applicazioni SAP
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
ms.date: 01/10/2020
ms.author: radeltch
ms.openlocfilehash: ace30b044682cb4e62a0f81da90f21a77be1edd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372975"
---
# <a name="deploy-a-sap-hana-scale-out-system-with-standby-node-on-azure-vms-by-using-azure-netapp-files-on-red-hat-enterprise-linux"></a>Distribuire un sistema di scalabilità orizzontale SAP HANA con nodo standby nelle macchine virtuali di Azure usando i file di Azure NetApp in Red Hat Enterprise LinuxDeploy a SAP HANA scale-out system with standby node on Azure VMs by using Azure NetApp Files on Red Hat Enterprise Linux 

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
[2593824]:https://launchpad.support.sap.com/#/notes/2455582
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


Questo articolo descrive come distribuire un sistema SAP HANA a disponibilità elevata in una configurazione con scalabilità orizzontale con standby in macchine virtuali (VM) Linux di Azure Red Hat Enterprise usando [I file](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/) di Rete di Azure per i volumi di archiviazione condivisi.  

Nelle configurazioni di esempio, nei comandi di installazione e così via, l'istanza HANA è **03** e l'ID di sistema HANA è **HN1**. Gli esempi sono basati su HANA 2.0 SP4 e Red Hat Enterprise Linux per SAP 7.6. 

Prima di iniziare, fare riferimento alle note e ai documenti SAP seguenti:

* [Documentazione sui file NetApp di AzureAzure NetApp Files documentation][anf-azure-doc] 
* SAP Note [1928533] include:  
  * Un elenco delle dimensioni delle macchine virtuali di Azure supportate per la distribuzione del software SAP
  * Importanti informazioni sulla capacità per le dimensioni delle VM di Azure
  * Software SAP e combinazioni di sistemi operativi e database supportati
  * La versione del kernel SAP richiesta per Windows e Linux in Microsoft Azure
* NOTa SAP [2015553:]elenca i prerequisiti per le distribuzioni di software SAP supportati da SAP in Azure
* SAP Nota [2002167] ha consigliato le impostazioni del sistema operativo per Red Hat Enterprise Linux
* La nota SAP [2009879] contiene le linee guida di SAP HANA per Red Hat Enterprise Linux
* NOTa SAP [2178632]: contiene informazioni dettagliate su tutte le metriche di monitoraggio segnalate per SAP in Azure
* Nota SAP [2191498:]contiene la versione dell'agente host SAP necessaria per Linux in Azure
* Nota SAP [2243692:]contiene informazioni sulle licenze SAP su Linux in Azure
* Nota SAP [1999351]: contiene informazioni aggiuntive sulla risoluzione dei problemi per l'estensione di monitoraggio avanzato di Azure per SAP
* Nota SAP [1900823:]contiene informazioni sui requisiti di archiviazione SAP HANA
* [SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Contiene tutte le note SAP necessarie per Linux
* [Pianificazione e implementazione di Macchine virtuali di Azure per SAP in Linux][planning-guide]
* [Distribuzione di Macchine virtuali di Azure per SAP in Linux][deployment-guide]
* [Distribuzione DBMS di Macchine virtuali di Azure per SAP in Linux][dbms-guide]
* Documentazione generale di RHEL
  * [High Availability Add-On Overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index) (Panoramica dei componenti aggiuntivi a disponibilità elevata)
  * [High Availability Add-On Administration](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index) (Amministrazione dei componenti aggiuntivi a disponibilità elevata)
  * [High Availability Add-On Reference](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index) (Riferimento dei componenti aggiuntivi a disponibilità elevata)
  * [Guida alla rete Linux di Red Hat Enterprise](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide)
* Documentazione RHEL specifica di Azure:Azure-specific RHEL documentation:
  * [Install SAP HANA on Red Hat Enterprise Linux for Use in Microsoft Azure](https://access.redhat.com/solutions/3193782) (Installare SAP HANA su Red Hat Enterprise Linux per l'uso in Microsoft Azure)
* [Applicazioni SAP NetApp in Microsoft Azure con i file NetApp di Azure][anf-sap-applications-azure]


## <a name="overview"></a>Panoramica

Un metodo per ottenere la disponibilità elevata HANA consiste nel configurare il failover automatico dell'host. Per configurare il failover automatico dell'host, aggiungere una o più macchine virtuali al sistema HANA e configurarle come nodi standby. Quando si verifica un errore nel nodo attivo, un nodo standby subentra automaticamente. Nella configurazione presentata con le macchine virtuali di Azure è possibile eseguire il failover automatico usando [NFS nei file NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)di Azure.  

> [!NOTE]
> Il nodo standby deve accedere a tutti i volumi del database. I volumi HANA devono essere montati come volumi NFSv4. Il meccanismo di blocco basato sul lease di file `I/O` migliorato nel protocollo NFSv4 viene utilizzato per la scherma. 

> [!IMPORTANT]
> Per compilare la configurazione supportata, è necessario distribuire i volumi di log e dati HANA come volumi NFSv4.1 e montarli utilizzando il protocollo NFSv4.1. La configurazione di failover automatico dell'host HANA con nodo standby non è supportata con NFSv3.

![Panoramica della disponibilità elevata di SAP NetWeaver](./media/sap-hana-high-availability-rhel/sap-hana-scale-out-standby-netapp-files-rhel.png)

Nel diagramma precedente, che segue le raccomandazioni di rete SAP HANA, tre subnet sono rappresentate all'interno di una rete virtuale di Azure:In the preceding diagram, which follows SAP HANA network recommendations, three subnets are represented within one Azure virtual network: 
* Per la comunicazione con il cliente
* Per la comunicazione con il sistema di storage
* Per la comunicazione interna tra nodi HANA

I volumi NetApp di Azure si trovano in una subnet separata, [delegata a File NetApp di Azure](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

Per questa configurazione di esempio, le subnet sono:For this example configuration, the subnets are:  

  - `client`10.9.1.0/26  
  - `storage`10.9.3.0/26  
  - `hana`10.9.2.0/26  
  - `anf`10.9.0.0/26 (subnet delegata ai file NetApp di Azure)

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

   Durante la distribuzione dei volumi, assicurarsi di selezionare la versione **NFSv4.1.** Distribuire i volumi nella [subnet](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)dei file netApp di Azure designata. 
   
   Tenere presente che le risorse File NetApp di Azure e le macchine virtuali di Azure devono trovarsi nella stessa rete virtuale di Azure o in reti virtuali di Azure con peered. Ad esempio, **HN1**-data-mnt00001, **HN1**-log-mnt00001 e così via, sono i nomi dei volumi e nfs://10.9.0.4/**HN1**-data-mnt00001, nfs://10.9.0.4/**HN1**-log-mnt00001 e così via, sono i percorsi dei file di Azure NetApp.  

   * volume **HN1**-data-mnt00001 (nfs://10.9.0.4/**HN1**-data-mnt00001)
   * volume **HN1**-data-mnt00002 (nfs://10.9.0.4/**HN1**-data-mnt00002)
   * volume **HN1**-log-mnt00001 (nfs://10.9.0.4/**HN1**-log-mnt00001)
   * volume **HN1**-log-mnt00002 (nfs://10.9.0.4/**HN1**-log-mnt00002)
   * volume **HN1**-condiviso (nfs://10.9.0.4/**HN1**-condiviso)
   
   In questo esempio è stato usato un volume separato di file NetApp di Azure per ogni volume di dati e log HANA. Per una configurazione più ottimizzata in termini di costi su sistemi più piccoli o non produttivi, è possibile posizionare tutti i supporti di dati in un singolo volume e tutti i log vengono montati su un singolo volume diverso.  

### <a name="important-considerations"></a>Considerazioni importanti

Quando si crea i file di Azure NetApp per SAP HANA scale-out con lo scenario di nodi stand by, tenere presenti le considerazioni importanti seguenti:As you're creating your Azure NetApp Files for SAP HANA scale-out with stand by nodes scenario, be aware of the following important considerations:

- Il pool di capacità minima è 4 tebibyte (TiB).  
- La dimensione minima del volume è 100 gibibyte (GiB).
- I file NetApp di Azure e tutte le macchine virtuali in cui verranno montati i volumi di File NetApp di Azure devono trovarsi nella stessa rete virtuale di Azure o in [reti virtuali con peerè](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) nella stessa area.  
- La rete virtuale selezionata deve avere una subnet delegata a File NetApp di Azure.The selected virtual network must have a subnet that's delegated to Azure NetApp Files.
- La velocità effettiva di un volume File NetApp di Azure è una funzione della quota di volume e del livello di servizio, come documentato nel [livello di servizio per i file NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)di Azure. Quando si ridimensionano i volumi NETApp di HANA Azure, assicurarsi che la velocità effettiva risultante soddisfi i requisiti di sistema HANA.  
- Con i criteri di [esportazione](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)File di Azure NetApp è possibile controllare i client consentiti, il tipo di accesso (lettura/scrittura, sola lettura e così via). 
- La funzionalità File NetApp di Azure non è ancora in grado di riconoscere le aree. Attualmente, la funzionalità non è distribuita in tutte le zone di disponibilità in un'area di Azure.Currently, the feature isn't deployed in all availability zones in an Azure region. Tenere presente le potenziali implicazioni di latenza in alcune aree di Azure.Be aware of the potential latency implications in some Azure regions.  

> [!IMPORTANT]
> Per i carichi di lavoro SAP HANA, una bassa latenza è fondamentale. Collaborare con il rappresentante Microsoft per assicurarsi che le macchine virtuali e i volumi di File NetApp di Azure vengano distribuiti in prossimità.  

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Ridimensionamento per il database HANA nei file NetApp di AzureSizing for HANA database on Azure NetApp Files

La velocità effettiva di un volume File NetApp di Azure è una funzione delle dimensioni del volume e del livello di servizio, come documentato nel livello di servizio [per i file NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)di Azure. 

Quando si progetta l'infrastruttura per SAP in Azure, tenere presente alcuni requisiti minimi di archiviazione da parte di SAP, che si traducono in caratteristiche di velocità effettiva minima:As you design the infrastructure for SAP in Azure, be aware of some minimum storage requirements by SAP, which translate into minimum throughput characteristics:

- Lettura/scrittura su /hana/registro di 250 megabyte al secondo (MB/s) con dimensioni di I/O di 1 MB.  
- Attività di lettura di almeno 400 MB/s per /hana/dati per dimensioni I/O da 16 MB e 64 MB.  
- Attività di scrittura di almeno 250 MB/s per /hana/dati con dimensioni I/O da 16 MB e 64 MB. 

I limiti di velocità effettiva dei file netApp di Azure per 1 TiB della quota del volume sono:The [Azure NetApp Files throughput limits](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) per 1 TiB of volume quota are:
- Livello di archiviazione Premium - 64 MiB/s  
- Livello di archiviazione ultra - 128 MiB/s  

Per soddisfare i requisiti minimi di velocità effettiva SAP per i dati e il log e le linee guida per /hana/shared, le dimensioni consigliate sono:

| Volume | Dimensione di<br>Livello Archiviazione Premium | Dimensione di<br>Livello di archiviazione ultra | Protocollo NFS supportato |
| --- | --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v4.1 (in questo v) |
| /hana/data | 6.3 TiB (in inglese) | 3.2 TiB | v4.1 (in questo v) |
| /hana/shared | 1xRAM per 4 nodi di lavoro | 1xRAM per 4 nodi di lavoro | v3 o v4.1 |

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

   a. Usare un'immagine Linux di Red Hat Enterprise nella raccolta di Azure supportata per SAP HANA. In questo esempio è stata usata un'immagine RHEL-SAP-HA 7.6.  

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

3. **[A]** Aggiungere una route di rete, in modo che la comunicazione con i file NetApp di Azure passi tramite l'interfaccia di rete di archiviazione.  

   In questo esempio `Networkmanager` verrà utilizzato per configurare la route di rete aggiuntiva. Le istruzioni seguenti presuppongono `eth1`che l'interfaccia di rete di archiviazione sia .  
   Determinare innanzitutto il nome `eth1`della connessione per il dispositivo . In questo esempio il `eth1` nome `Wired connection 1`della connessione per device è .  

    <pre><code>
    # Execute as root
    nmcli connection
    # Result
    #NAME                UUID                                  TYPE      DEVICE
    #System eth0         5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03  ethernet  eth0
    #Wired connection 1  4b0789d1-6146-32eb-83a1-94d61f8d60a7  ethernet  eth1
    </code></pre>

   Configurare quindi una route aggiuntiva alla `eth1`rete delegata File NetApp di Azure tramite .  

    <pre><code>
    # Add the following route 
    # ANFDelegatedSubnet/cidr via StorageSubnetGW dev StorageNetworkInterfaceDevice
    nmcli connection modify <b>"Wired connection 1"</b> +ipv4.routes <b>"10.9.0.0/26 10.9.3.1"</b>
    </code></pre>

    Riavviare la macchina virtuale per attivare le modifiche.  

3. **[A]** Installare il pacchetto client NFS.  

    <pre><code>
    yum install nfs-utils
    </code></pre>

3. **[A]** Preparare il sistema operativo per l'esecuzione di SAP HANA su Azure NetApp con NFS, come descritto in [SAP HANA su NetApp AFF Systems with NFS configuration guide](https://www.netapp.com/us/media/tr-4435.pdf). Creare il file di configurazione */etc/sysctl.d/netapp-hana.conf* per le impostazioni di configurazione di NetApp.  

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
    net.core.netdev_max_backlog = 300000 net.ipv4.tcp_slow_start_after_idle=0 net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **[A]** Crea il file di configurazione */etc/sysctl.d/ms-az.conf* con impostazioni di ottimizzazione aggiuntive.  

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

5. **[A]** Regolare le impostazioni sunrpc, come consigliato in [SAP HANA su NetApp AFF Systems con guida](https://www.netapp.com/us/media/tr-4435.pdf)alla configurazione NFS .  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

6. **[A]** Red Hat per la configurazione HANA.

    Configurare RHEL come descritto in NOTE SAP [2292690], [2455582], [2593824] e <https://access.redhat.com/solutions/2447641>.

    > [!NOTE]
    > Se si installa HANA 2.0 SP04, `compat-sap-c++-7` sarà necessario installare il pacchetto come descritto nella nota [2593824]di SAP, prima di poter installare SAP HANA. 

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
    mount <b>10.9.0.4</b>:/<b>HN1</b>-shared /mnt/tmp
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
    mount 10.9.0.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   Per ulteriori informazioni su `nfs4_disable_idmapping` come https://access.redhat.com/solutions/1749883modificare il parametro, vedere .

6. **[A]** Montare i volumi condivisi di File NetApp di Azure.  

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

7. **[1] Montare** i volumi specifici del nodo su **hanadb1**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** Montare i volumi specifici del nodo su **hanadb2**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** Montare i volumi specifici del nodo su **hanadb3**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** Verificare che tutti i volumi HANA siano montati con la versione del protocollo **NFS NFSv4**.  

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
    yum install libgcc_s1 libstdc++6 compat-sap-c++-7 libatomic1 
    </code></pre>

4. **[2], [3]** Modificare la proprietà `data` di `log` SAP HANA e directory in **hn1**adm.   

    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

5. **[A]** Disattivare temporaneamente il firewall, in modo che non interferisca con l'installazione di HANA. È possibile riattivarlo, al termine dell'installazione di HANA. 
   <pre><code>
    # Execute as root
    systemctl stop firewalld
    systemctl disable firewalld
   </code></pre>

### <a name="hana-installation"></a>Installazione HANA

1. **[1]** Installare SAP HANA seguendo le istruzioni nella [guida all'installazione e all'aggiornamento di SAP HANA 2.0](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html). In questo esempio viene installata la scalabilità orizzontale SAP HANA con master, un worker e un nodo di standby.  

   a. Avviare il programma **hdblcm** dalla directory del software di installazione HANA. Utilizzare `internal_network` il parametro e passare lo spazio di indirizzi per la subnet, utilizzato per la comunicazione interna tra nodi HANA.  

    <pre><code>
    ./hdblcm --internal_network=10.9.2.0/26
    </code></pre>

   b. Al prompt, immettere i seguenti valori:

     * Per **Scegliere un'azione**: immettere **1** (per l'installazione)
     * Per **Componenti aggiuntivi per l'installazione**: immettere **2, 3**
     * Per il percorso di installazione: premere Invio (il valore predefinito è /hana/shared)
     * Per **Nome host locale**: premere Invio per accettare
     * In **Aggiungere host al sistema?**: immettere **y**
     * Per **i nomi host separati da virgole da aggiungere**: immettere **hanadb2, hanadb3**
     * Per **Nome utente radice** [root]: premere Invio per accettare
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
    internal_network = <b>10.9.2.0/26</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.9.2.4</b> = <b>hanadb1</b>
    <b>10.9.2.5</b> = <b>hanadb2</b>
    <b>10.9.2.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** Aggiungere il mapping dell'host per garantire che gli indirizzi IP del client vengano utilizzati per la comunicazione client. Aggiungere `public_host_resolution`la sezione e aggiungere gli indirizzi IP corrispondenti dalla subnet client.  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.9.1.5</b>
    map_<b>hanadb2</b> = <b>10.9.1.6</b>
    map_<b>hanadb3</b> = <b>10.9.1.7</b>
   </code></pre>

4. **[1]** Riavviare SAP HANA per attivare le modifiche.  

   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** Verificare che l'interfaccia client utilizzi `client` gli indirizzi IP della subnet per la comunicazione.  

   <pre><code>
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.9.1.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.9.1.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.9.1.5</b>"
   </code></pre>

   Per informazioni su come verificare la configurazione, vedere NOTa SAP [2183363 - Configurazione della rete interna SAP HANA](https://launchpad.support.sap.com/#/notes/2183363).  

5. **[A]** Riattivare il firewall.  
   - Arrestare HANA
       <pre><code>
        sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
       </code></pre>
   - Riattivare il firewall
       <pre><code>
        # Execute as root
        systemctl start firewalld
        systemctl enable firewalld
       </code></pre>

   - Aprire le porte del firewall necessarie

       > [!IMPORTANT]
       > Creare regole del firewall per consentire la comunicazione tra nodi HANA e il traffico client. Le porte necessarie sono elencate in [TCP/IP Ports of All SAP Products](https://help.sap.com/viewer/ports) (Porte TCP/IP per tutti i prodotti SAP). I comandi seguenti sono solo un esempio. In questo scenario con il numero di sistema utilizzato 03.

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

   - Avviare HANA
       <pre><code>
        sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
       </code></pre>

6. Per ottimizzare SAP HANA per l'archiviazione dei file NetApp di Azure sottostante, impostare i parametri SAP HANA seguenti:To optimize SAP HANA for the underlying Azure NetApp Files storage, set the following SAP HANA parameters:

   - `max_parallel_io_requests`**128 Del 12**
   - `async_read_submit` **on**
   - `async_write_submit_active` **on**
   - `async_write_submit_blocks`**tutti**

   Per ulteriori informazioni, vedere [SAP HANA su NetApp AFF Systems with NFS Configuration Guide](https://www.netapp.com/us/media/tr-4435.pdf). 

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
   > Quando un nodo si verifica il panico del `kernel.panic` kernel, evitare ritardi con il failover SAP HANA impostando su 20 secondi in *tutte le* macchine virtuali HANA. La configurazione viene `/etc/sysctl`eseguita in . Riavviare le macchine virtuali per attivare la modifica. Se questa modifica non viene eseguita, il failover può richiedere 10 o più minuti quando un nodo si verifica il panico del kernel.  

2. Uccidere il server dei nomi eseguendo le operazioni seguenti:

   a. Prima del test, controllare lo stato dell'ambiente eseguendo i seguenti comandi come **hn1**adm:  

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

   b. Eseguire i seguenti comandi come **adm hn1**sul nodo master attivo, che in questo caso è **hanadb1:**  

    <pre><code>
        hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
    </code></pre>
    
    Il nodo standby **hanadb3** assumerà il controllo del nodo master. Ecco lo stato della risorsa dopo il completamento del test di failover:Here is the resource state after the failover test is completed:  

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

   c. Riavviare l'istanza HANA in **hanadb1,** ovvero nella stessa macchina virtuale in cui è stato ucciso il server dei nomi. Il nodo **hanadb1** si unirà nuovamente all'ambiente e manterrà il proprio ruolo di standby.  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Dopo l'avvio di SAP HANA in **hanadb1**, si prevede il seguente stato:  

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

   d. Ancora una volta, uccidere il server dei nomi sul nodo master attualmente attivo, ovvero sul nodo **hanadb3**.  
   
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   Il nodo **hanadb1** riprenderà il ruolo di nodo master. Al termine del test di failover, lo stato sarà simile al seguente:After the failover test has been completed, the status will look like this:

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

* [Pianificazione e implementazione di Macchine virtuali di Azure per SAP][planning-guide]
* [Distribuzione di macchine virtuali di Azure per SAP][deployment-guide]
* [Distribuzione DBMS di Macchine virtuali di Azure per SAP][dbms-guide]
* Per informazioni su come stabilire la disponibilità elevata e pianificare il ripristino di emergenza di SAP HANA in Azure (istanze di grandi dimensioni), vedere Disponibilità elevata SAP HANA (istanze di grandi dimensioni) e ripristino di emergenza in Azure.To learn how to establish high availability and plan for disaster recovery of SAP HANA on Azure (large instances), see [SAP HANA (large instances) high availability and disaster recovery on Azure.](hana-overview-high-availability-disaster-recovery.md)
* Per informazioni su come stabilire la disponibilità elevata e pianificare il ripristino di emergenza di SAP HANA nelle macchine virtuali di Azure, vedere Disponibilità elevata di SAP HANA in Macchine virtuali di Azure.To learn how to establish high availability and plan for disaster recovery of SAP HANA on Azure VMs, see [High Availability of SAP HANA on Azure Virtual Machines (VMs)][sap-hana-ha].
