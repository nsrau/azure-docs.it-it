---
title: SAP HANA la scalabilità orizzontale con il nodo standby in macchine virtuali di Azure con Azure NetApp Files in SUSE Linux Enterprise Server | Microsoft Docs
description: Guida alla disponibilità elevata per SAP NetWeaver in SUSE Linux Enterprise Server con Azure NetApp Files per le applicazioni SAP
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
ms.date: 04/30/2019
ms.author: radeltch
ms.openlocfilehash: 76369c1a4beb792de03cf0ccae5c86825812f103
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934169"
---
# <a name="sap-hana-scale-out-with-standby-node-on-azure-vms-with-azure-netapp-files-on-suse-linux-enterprise-server"></a>SAP HANA la scalabilità orizzontale con il nodo standby in macchine virtuali di Azure con Azure NetApp Files in SUSE Linux Enterprise Server 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]: https://launchpad.support.sap.com/#/notes/2205917
[1944799]: https://launchpad.support.sap.com/#/notes/1944799
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]: https://launchpad.support.sap.com/#/notes/1900823

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


Questo articolo descrive come distribuire un sistema HANA a disponibilità elevata nella configurazione con scalabilità orizzontale con standby in macchine virtuali di Azure con [Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/) per i volumi di archiviazione condivisa.  
Nelle configurazioni di esempio, i comandi di installazione e così via, l'istanza di HANA è **03** e l'ID di sistema Hana è **HN1**. Gli esempi sono basati su HANA 2,0 SP4 e SUSE Linux Enterprise Server per SAP 12 SP4. 

Leggere prima di tutto i documenti e le note SAP seguenti:

* [Documentazione di Azure NetApp Files][anf-azure-doc] 
* Nota SAP [1928533], contenente:  
  * Elenco delle dimensioni delle VM di Azure supportate per la distribuzione di software SAP
  * Importanti informazioni sulla capacità per le dimensioni delle VM di Azure
  * Software SAP e combinazioni di sistemi operativi e database supportati
  * Versione del kernel SAP richiesta per Windows e Linux in Microsoft Azure
* La nota SAP [2015553] elenca i prerequisiti per le distribuzioni di software SAP supportate da SAP in Azure.
* La nota SAP [2205917] contiene le impostazioni consigliate del sistema operativo per SUSE Linux Enterprise Server for SAP Applications
* La nota SAP [1944799] contiene linee guida sap per SUSE Linux Enterprise Server per le applicazioni SAP
* La nota SAP [2178632] contiene informazioni dettagliate su tutte le metriche di monitoraggio segnalate per SAP in Azure.
* La nota SAP [2191498] contiene la versione dell'agente host SAP per Linux necessaria in Azure.
* La nota SAP [2243692] contiene informazioni sulle licenze SAP in Linux in Azure.
* La nota SAP [1984787] contiene informazioni generali su SUSE Linux Enterprise Server 12.
* La nota SAP [1999351] contiene informazioni aggiuntive sulla risoluzione dei problemi per l'estensione di monitoraggio avanzato di Azure per SAP.
* La nota SAP [1900823] contiene informazioni sui requisiti di archiviazione SAP Hana
* [Community WIKI SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) contiene tutte le note su SAP necessarie per Linux.
* [Pianificazione e implementazione di macchine virtuali di Azure per SAP in Linux][planning-guide]
* [Distribuzione di macchine virtuali di Azure per SAP in Linux][deployment-guide]
* [Distribuzione DBMS di macchine virtuali di Azure per SAP in Linux][dbms-guide]
* [Guide alle procedure consigliate di SUSE SAP][suse-ha-guide] Le guide contengono tutte le informazioni necessarie per configurare NetWeaver a disponibilità elevata e SAP HANA replica di sistema locale. Usare le guide come indicazioni di base generali. Forniscono informazioni molto più dettagliate.
* [Note sulla versione di SUSE High Availability Extension 12 SP3][suse-ha-12sp3-relnotes]
* [Applicazioni SAP NetApp su Microsoft Azure con Azure NetApp Files][anf-sap-applications-azure]
* [SAP Hana nei sistemi NetApp con NFS](https://www.netapp.com/us/media/tr-4435.pdf). La guida alla configurazione contiene informazioni su come configurare SAP HANA, usando NFS fornito da Azure NetApp Files.


## <a name="overview"></a>Panoramica

Uno dei metodi per ottenere la disponibilità elevata di HANA è il failover automatico dell'host. Per configurare il failover automatico dell'host, una o più macchine virtuali vengono aggiunte al sistema HANA e configurate come nodi standby. Quando si verifica un errore nel nodo attivo, il nodo standby assume automaticamente il valore. Nella configurazione presentata con macchine virtuali di Azure, eseguita da [NFS in Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/).  

Il nodo standby deve avere accesso a tutti i volumi del database. I volumi HANA devono essere montati come volumi NFSv4. Il meccanismo di blocco basato sul lease di file migliorato nel protocollo NFSv4 viene usato per `I/O` la schermatura. 

> [!IMPORTANT]
> È obbligatorio distribuire i volumi di log e di dati HANA come volumi NFSv 4.1 e montarli, usando il protocollo NFSv 4.1, per avere la configurazione supportata. La configurazione del failover automatico dell'host HANA con il nodo standby non è supportata con NFSv3.

![Panoramica della disponibilità elevata di SAP NetWeaver](./media/high-availability-guide-suse-anf/sap-hana-scale-out-standby-netapp-files-suse.png)

In seguito ai consigli sulla rete di SAP HANA, sono state create tre subnet all'interno di una rete virtuale di Azure: per la comunicazione con il sistema di archiviazione, per la comunicazione tra nodi HANA interna e per la comunicazione client. I volumi di Azure NetApp si trovano in una subnet separata, [delegata a Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

Per questa configurazione di esempio, le subnet sono:  

  - `storage` 10.23.2.0/24  
  - `hana` 10.23.3.0/24  
  - `client` 10.23.0.0/24  
  - `anf` 10.23.1.0/26  

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Configurazione dell'infrastruttura di Azure NetApp Files 

Prima di procedere con l'installazione dell'infrastruttura dei file di Azure NetApp, acquisire familiarità con la [documentazione Azure NetApp files][anf-azure-doc]. I file di Azure NetApp sono disponibili in diverse [aree di Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Controllare se l'area di Azure selezionata offre Azure NetApp Files.  

Il collegamento seguente mostra la disponibilità di Azure NetApp Files dall'area di Azure: [Azure NetApp files disponibilità per area di Azure][anf-avail-matrix].  
Prima di distribuire Azure NetApp Files, richiedere l'onboarding a Azure NetApp Files, seguendo le [istruzioni per la registrazione di file NetApp di Azure][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Distribuisci risorse Azure NetApp Files  

I passaggi seguenti presuppongono che la [rete virtuale di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)sia già stata distribuita. Le risorse Azure NetApp Files e le macchine virtuali in cui verranno montate le risorse di Azure NetApp Files devono essere distribuite nella stessa rete virtuale di Azure o in reti virtuali di Azure con peering.  

1. Se non è già stato fatto, richiedere l' [onboarding per Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register).  

2. Creare l'account NetApp nell'area di Azure selezionata seguendo le [istruzioni per la creazione dell'account NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  

3. Configurare Azure NetApp Files pool di capacità seguendo le [istruzioni su come configurare Azure NetApp files pool di capacità](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  
L'architettura HANA illustrata in questo articolo usa il pool di capacità singolo Azure NetApp Files e il livello di servizio ultra. È consigliabile Azure NetApp Files [livello di servizio](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) Ultra o Premium per carichi di lavoro Hana in Azure.  

4. Delegare una subnet ai file di Azure NetApp come descritto nelle [istruzioni delegare una subnet a Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

5. Distribuire Azure NetApp Files volumi, seguendo le [istruzioni per creare un volume per Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes).  Assicurarsi di selezionare **NFSv 4.1** versione, durante la distribuzione dei volumi. Attualmente l'accesso a NFSv 4.1 richiede un elenco elementi consentiti aggiuntivo. Distribuire i volumi nella [subnet](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)Azure NetApp files designata. Tenere presente che le risorse Azure NetApp Files e le macchine virtuali di Azure devono trovarsi nella stessa rete virtuale di Azure o in reti virtuali di Azure con peering. Ad esempio, <b>HN1</b>-data-Mnt00001, <b>HN1</b>-log-mnt00001 e così via sono i nomi di volume<b>e NFS://10.23.1.5/HN1-data</b>-mnt00001, NFS://10.23.1.4/<b>HN1</b>-log-mnt00001 e così via sono i percorsi dei file per i volumi Azure NetApp files.  

   1. volume <b>HN1</b>-data-mnt00001 (NFS://10.23.1.5/<b>HN1</b>-data-mnt00001)
   2. volume <b>HN1</b>-data-mnt00002 (NFS://10.23.1.6/<b>HN1</b>-data-mnt00002)
   3. volume <b>HN1</b>-log-mnt00001 (NFS://10.23.1.4/<b>HN1</b>-log-mnt00001)
   4. volume <b>HN1</b>-log-mnt00002 (NFS://10.23.1.6/<b>HN1</b>-log-mnt00002)
   5. volume <b>HN1</b>-shared (NFS://10.23.1.4/<b>HN1</b>-Shared)
   
   In questo esempio sono stati usati Azure NetApp Files distinti per ogni volume di log e di dati HANA. Per una configurazione più conveniente in sistemi più piccoli o non produttivi, è possibile posizionare tutti i montaggi dei dati e tutti i log vengono montati in un singolo volume.  

### <a name="important-considerations"></a>Considerazioni importanti

Quando si prende in considerazione Azure NetApp Files per SAP NetWeaver sull'architettura a disponibilità elevata SUSE, tenere presente le considerazioni seguenti:

- Il pool di capacità minimo è 4 TiB.  
- La dimensione minima del volume è 100 GiB
- Azure NetApp Files e tutte le macchine virtuali, in cui verranno montati i volumi Azure NetApp Files, devono trovarsi nella stessa rete virtuale di Azure o in [reti virtuali con peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) nella stessa area.  
- La rete virtuale selezionata deve avere una subnet, delegata a Azure NetApp Files.
- La velocità effettiva di un volume di Azure NetApp è una funzione della quota del volume e del livello di servizio, come documentato nel [livello di servizio per Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). Quando si ridimensionano i volumi di Azure NetApp di HANA, verificare che la velocità effettiva risultante soddisfi i requisiti di sistema HANA.  
- Azure NetApp Files offre [criteri di esportazione](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): è possibile controllare i client consentiti, il tipo di accesso (lettura & scrittura, sola lettura e così via). 
- Azure NetApp Files funzionalità non è ancora in grado di riconoscere la zona. Attualmente Azure NetApp Files funzionalità non viene distribuita in tutte le zone di disponibilità in un'area di Azure. Tenere presente le implicazioni potenziali di latenza in alcune aree di Azure.  
- È importante che le macchine virtuali vengano distribuite in prossimità dell'archiviazione di Azure NetApp per una bassa latenza. Per SAP HANA carichi di lavoro a bassa latenza è fondamentale. Collaborare con il rappresentante Microsoft per assicurarsi che le macchine virtuali e i volumi Azure NetApp Files vengano distribuiti in prossimità.  
- L'ID utente per <b>SID</b>ADM e l'ID gruppo per `sapsys` nelle macchine virtuali devono corrispondere alla configurazione in Azure NetApp files. 

> [!IMPORTANT]
> Per SAP HANA carichi di lavoro a bassa latenza è fondamentale. Collaborare con il rappresentante Microsoft per assicurarsi che le macchine virtuali e i volumi Azure NetApp Files vengano distribuiti in prossimità.  

> [!IMPORTANT]
> Se è presente una mancata corrispondenza tra l'ID utente per <b>SID</b>ADM e l'ID gruppo per `sapsys` tra la macchina virtuale e la configurazione di Azure NetApp, le autorizzazioni per i file nei volumi di Azure NetApp, montate in macchine virtuali, verranno visualizzate come `nobody`. Assicurarsi di specificare l'ID utente corretto per <b>SID</b>ADM e l'ID del gruppo per `sapsys`, durante [l'onboarding di un nuovo sistema](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) da Azure NetApp files.

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Dimensionamento per il database HANA in Azure NetApp Files

La velocità effettiva di un volume di Azure NetApp è una funzione delle dimensioni del volume e del livello di servizio, come documentato nel [livello di servizio per Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). 

Quando si progetta l'infrastruttura per SAP in Azure, è necessario conoscere alcuni requisiti minimi di archiviazione di SAP, che vengono convertiti in caratteristiche di velocità effettiva minime:

- Abilita lettura/scrittura su/Hana/log di 250 MB/sec con dimensioni di I/O di 1 MB  
- Abilitare l'attività di lettura di almeno 400 MB/sec per/Hana/data per le dimensioni di I/O di 16 MB e 64 MB  
- Abilitare l'attività di scrittura di almeno 250 MB/sec per/Hana/data con dimensioni di I/O di 16 MB e 64 MB  

I [limiti di velocità effettiva Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) per 1 TIB della quota del volume sono:
- Livello di archiviazione Premium-64 MiB/s  
- Livello di archiviazione Ultra-128 MiB/s  

Per soddisfare i requisiti di velocità effettiva minima SAP per dati e log e in base alle linee guida per `/hana/shared`, le dimensioni consigliate sono le seguenti:

| Volume | Dimensioni<br /> Livello di archiviazione Premium | Dimensioni<br /> Livello di archiviazione Ultra | Protocollo NFS supportato |
| --- | --- | --- |
| /Hana/log | 4 TiB | 2 TiB | v 4.1 |
| /hana/data | 6,3 TiB | 3,2 TiB | v 4.1 |
| /hana/shared | Massimo (512 GB, 1xRAM) per 4 nodi di lavoro | Massimo (512 GB, 1xRAM) per 4 nodi di lavoro | V3 o v 4.1 |

La configurazione SAP HANA per il layout presentato in questo articolo, usando Azure NetApp Files livello di archiviazione Ultra sarà simile a quanto segue:

| Volume | Dimensioni<br /> Livello di archiviazione Ultra | Protocollo NFS supportato |
| --- | --- |
| /hana/log/mnt00001 | 2 TiB | v 4.1 |
| /hana/log/mnt00002 | 2 TiB | v 4.1 |
| /hana/data/mnt00001 | 3,2 TiB | v 4.1 |
| /hana/data/mnt00002 | 3,2 TiB | v 4.1 |
| /hana/shared | 2 TiB | V3 o v 4.1 |

> [!NOTE]
> Le indicazioni relative al ridimensionamento del Azure NetApp Files indicate di seguito sono destinate a soddisfare i requisiti minimi che SAP esprime per i provider di infrastruttura. Nelle distribuzioni reali dei clienti e negli scenari di carico di lavoro, questo potrebbe non essere sufficiente. Usare queste indicazioni come punto di partenza e adattarle, in base ai requisiti del carico di lavoro specifico.  

> [!TIP]
> È possibile ridimensionare Azure NetApp Files volumi in modo dinamico, senza dover `unmount` i volumi, arrestare le macchine virtuali o arrestare SAP HANA. Questo consente la flessibilità di soddisfare le esigenze di velocità effettiva previste e non previste per le applicazioni.

## <a name="deploy-linux-virtual-machines-via-azure-portal"></a>Distribuire macchine virtuali Linux tramite portale di Azure

Per prima cosa è necessario creare i volumi Azure NetApp Files. Creare le [subnet della rete virtuale di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) nella [rete virtuale di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Distribuire le macchine virtuali. Creare le interfacce di rete aggiuntive e alleghi le interfacce di rete alle VM corrispondenti. Ogni macchina virtuale dispone di tre interfacce di rete, corrispondenti alle tre subnet della rete virtuale di Azure (`storage`, `hana` e `client`).  Vedere [come creare una macchina virtuale Linux in Azure con più schede di interfaccia di rete](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics).  

> [!IMPORTANT]
> Per SAP HANA carichi di lavoro a bassa latenza è fondamentale. Collaborare con il rappresentante Microsoft per assicurarsi che le macchine virtuali e i volumi di Azure NetApp Files vengano distribuiti in prossimità per ottenere una bassa latenza. Inviare le informazioni necessarie quando si esegue l' [onboarding di un nuovo SAP Hana sistema](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u), che usa SAP Hana Azure NetApp files.  
> 
I passaggi successivi presuppongono che sia già stato creato il gruppo di risorse, la rete virtuale di Azure e le tre subnet della rete virtuale di Azure: `storage`, `hana` e `client`.  Quando si distribuiscono le macchine virtuali, selezionare la subnet di archiviazione in modo che l'interfaccia di rete di archiviazione sia l'interfaccia principale nelle macchine virtuali. Se ciò non è possibile, è necessario configurare una route esplicita alla subnet delegata di Azure NetApp tramite il gateway della subnet di archiviazione. 

> [!IMPORTANT]
> Assicurarsi che il sistema operativo selezionato sia dotato di certificazione SAP per SAP HANA sugli specifici tipi di macchina virtuale in uso. Per un elenco dei tipi di macchina virtuale certificati per SAP HANA e delle versioni di sistema operativo correlate, vedere l'elenco delle [piattaforme IaaS certificate per SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Fare clic su ogni voce presente nell'elenco dei tipi di macchina virtuale per ottenere l'elenco completo delle versioni di sistema operativo supportate da SAP HANA per lo specifico tipo di macchina virtuale.  

1. Creare un set di disponibilità per SAP HANA. Assicurarsi di impostare il dominio di aggiornamento massimo.  

2. Creazione di tre macchine virtuali (**hanadb1**, **hanadb2**, **hanadb3**)  
   - Usare un'immagine di SLES4SAP nella raccolta di Azure supportata per SAP HANA. In questo esempio è stata usata l'immagine SLES4SAP 12 SP4.  
   - Selezionare il set di disponibilità creato in precedenza per SAP HANA.  
   - Selezionare la subnet della rete virtuale di Azure di archiviazione. Selezionare [rete accelerata](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).  
Quando si distribuiscono le macchine virtuali, il nome dell'interfaccia di rete viene generato automaticamente. Si fa riferimento alle interfacce di rete collegate alla subnet della rete virtuale di Azure di archiviazione come **hanadb1-storage**, **hanadb2-storage**e **hanadb3-storage**. 
3. Creare tre interfacce di rete, una per ogni macchina virtuale, per la `hana` subnet della rete virtuale. In questo esempio: **hanadb1-Hana**, **hanadb2-Hana**e **hanadb3-Hana**.  
4. Creare tre interfacce di rete, una per ogni macchina virtuale, per la subnet della rete virtuale **client** . In questo esempio: **hanadb1-client**, **hanadb2-client**e **hanadb3-client**.  
5. Alleghi le interfacce di rete virtuale appena create alle macchine virtuali corrispondenti  

    1. Passare alla macchina virtuale in [portale di Azure](https://portal.azure.com/#home).  
    2. Selezionare macchine virtuali nel riquadro di spostamento a sinistra. Filtrare in merito al nome della macchina virtuale, ad esempio **hanadb1**. Fare clic sulla macchina virtuale.  
    3. Come si è in panoramica, selezionare Interrompi per deallocare la macchina virtuale.  
    4. Selezionare rete, collega interfaccia di rete. Selezionare dall'elenco a discesa in "Connetti interfaccia di rete" le interfacce di rete già create per le subnet **`hana`** e **client** .  Salvare. 
    5. Ripetere l'per le macchine virtuali rimanenti. In questo esempio, **hanadb2** e **hanadb3**.
    6. Per il momento, lasciare le macchine virtuali in stato di arresto. Successivamente, verrà abilitata la [rete accelerata](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) per tutte le interfacce di rete appena collegate.  

6. Abilitare la rete accelerata per le interfacce di rete aggiuntive per le subnet **`hana`** e **`client`** .  

    1. Aprire [cloud Shell](https://azure.microsoft.com/features/cloud-shell/) in [portale di Azure](https://portal.azure.com/#home)  
    2. Eseguire i comandi seguenti per abilitare la rete accelerata per le interfacce di rete aggiuntive, associate a **`hana`** e **`client`** subnet.  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-client</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-client</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-client</b> --accelerated-networking true
    </code></pre>
7. Avviare le macchine virtuali  

    1. Selezionare macchine virtuali nel riquadro di spostamento a sinistra. Filtrare in merito al nome della macchina virtuale, ad esempio **hanadb1**. Fare clic sulla macchina virtuale.  
    2. Come si è in panoramica, selezionare Avvia.  

## <a name="operating-system-configuration-and-preparation"></a>Configurazione e preparazione del sistema operativo

Gli elementi seguenti sono preceduti da **[a]** -applicabile a tutti i nodi, **[1]** -applicabile solo al nodo 1, **[2]** -applicabile solo al nodo 2 o **[3]** -applicabile solo al nodo 3.

1. **[A]** gestire i file host nelle macchine virtuali. Includere le voci per tutte le subnet. Per questo esempio sono state aggiunte le voci seguenti ai `/etc/hosts`.  
    <pre><code>
    # Storage
    10.23.2.4   hanadb1
    10.23.2.5   hanadb2
    10.23.2.6   hanadb3
    # Client
    10.23.0.5   hanadb1-client
    10.23.0.6   hanadb2-client
    10.23.0.7   hanadb3-client
    # Hana
    10.23.3.4   hanadb1-hana
    10.23.3.5   hanadb2-hana
    10.23.3.6   hanadb3-hana
    </code></pre>

2. **[A]** modificare le impostazioni di configurazione del cloud e DHCP per evitare modifiche al nome host non intenzionali.  
    <pre><code>
    vi /etc/sysconfig/network/dhcp
    #Change the following DHCP setting to "no"
    DHCLIENT_SET_HOSTNAME="no"
    vi /etc/sysconfig/network/ifcfg-eth0
    # Edit ifcfg-eth0 
    #Change CLOUD_NETCONFIG_MANAGE='yes' to "no"
    CLOUD_NETCONFIG_MANAGE='no'
    </code></pre>

3. **[A]** preparare il sistema operativo per l'esecuzione di SAP Hana nei sistemi NetApp con NFS, come descritto in [SAP Hana in NetApp sistemi aff con NFS Configuration Guide](https://www.netapp.com/us/media/tr-4435.pdf). Creare il file di configurazione per le impostazioni di configurazione di NetApp: `/etc/sysctl.d/netapp-hana.conf`.  

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

4. **[A] creare un** file di configurazione con Microsoft per le impostazioni di configurazione di Azure: `/etc/sysctl.d/ms-az.conf`.  

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

4. **[A]** modificare le impostazioni di sunrpc come consigliato in [SAP Hana nella Guida alla configurazione di sistemi aff NetApp con NFS](https://www.netapp.com/us/media/tr-4435.pdf).  
    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

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

2. **[1]** creare directory specifiche del nodo per `/usr/sap` in **HN1**-Shared.  

    <pre><code>
    # Create a temporary directory to mount  <b>HN1</b>-shared
    mkdir /mnt/tmp
    mount <b>10.23.1.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** verificare l'impostazione del dominio NFS. Verificare che il dominio sia configurato come **`localdomain`** e che il mapping sia impostato su **nessuno**.  
    <pre><code>
    sudo cat  /etc/idmapd.conf
    # Example
    [General]
    Verbosity = 0
    Pipefs-Directory = /var/lib/nfs/rpc_pipefs
    Domain = <b>localdomain</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A] disabilitare il** mapping dell'ID NFSv4. Eseguire il comando di montaggio per creare la struttura di directory in cui si trova `nfs4_disable_idmapping`.  Non sarà possibile creare manualmente la directory in/sys/Modules, poiché l'accesso è riservato per il kernel o i driver.  

    <pre><code>
    
    mkdir /mnt/tmp
    mount 10.23.1.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    # Disable NFSv4 idmapping. 
    echo "N" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    </code></pre>

5. **[A]** creare manualmente il gruppo SAP HANA e l'utente. Gli ID per il gruppo sapsys e l'utente **HN1**ADM devono essere impostati sugli stessi ID, forniti durante l'onboarding. Questo esempio gli ID sono impostati su **1001**. In caso contrario, non sarà possibile accedere ai volumi.  Gli ID per il gruppo sapsys e gli account utente **HN1**ADM e sapadm devono essere identici in tutte le macchine virtuali.  

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

6. **[A]** montare i volumi di Azure NetApp files condivisi.  

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

7. **[1]** montare i volumi specifici del nodo in **hanadb1**.  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** montare i volumi specifici del nodo in **hanadb2**.  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** montare i volumi specifici del nodo in **hanadb3**.  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** verificare che tutti i volumi Hana siano montati con il protocollo NFS versione **NFSv4**.  
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

In questo esempio per la distribuzione di SAP HANA nella configurazione con scalabilità orizzontale con il nodo standby con Azure, è stato usato HANA 2,0 SP4.  

### <a name="prepare-for-hana-installation"></a>Preparare l'installazione di HANA

1. **[A]** impostare la password radice, prima dell'installazione di Hana (è possibile disabilitare la password radice dopo che l'installazione è stata completata). Esegui come `root` `passwd` di comando.  

2. **[1]** verificare che sia possibile connettersi da SSH a **hanadb2** e **hanadb3**senza che venga richiesta la password.  
    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** installare pacchetti aggiuntivi, necessari per Hana 2,0 SP4. Per informazioni dettagliate, vedere la nota SAP [2593824](https://launchpad.support.sap.com/#/notes/2593824) . 
    <pre><code>
    sudo zypper install libgcc_s1 libstdc++6 libatomic1 
    </code></pre>

4. **[2, 3]** Modificare la proprietà di SAP HANA `data` e `log` directory in **HN1**ADM.   
    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

### <a name="hana-installation"></a>Installazione di HANA

1. **[1]** installare SAP Hana seguendo le istruzioni riportate nella [Guida all'installazione e all'aggiornamento di SAP Hana 2,0](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html). In questo esempio viene installato SAP HANA la scalabilità orizzontale con un nodo master, un ruolo di lavoro e un nodo standby.  
   Avviare il programma **hdblcm** dalla directory del software di installazione di Hana. Usare il parametro `internal_network` e passare lo spazio degli indirizzi per la subnet, usato per la comunicazione tra nodi HANA interna.  

    <pre><code>./hdblcm --internal_network=10.23.3.0/24
    </code></pre>

   Immettere i valori seguenti al prompt.

     * Scegliere un'azione: immettere **1** (per l'installazione)
     * Selezionare i componenti aggiuntivi per l'installazione: immettere **2, 3**
     * Immettere il percorso di installazione: premere INVIO (il valore predefinito è/Hana/Shared)
     * Immettere il nome host locale: premere INVIO per accettare il valore predefinito
     * Si desidera aggiungere host al sistema? Immettere **y**
     * Immettere i nomi host separati da coma per aggiungere: **hanadb2, hanadb3**
     * Immettere il nome utente radice [root]: premere INVIO per accettare il valore predefinito
     * Immettere la password dell'utente root: immettere la password della radice
     * Selezionare i ruoli per l'host hanadb2: immettere **1** (per il ruolo di lavoro)
     * Immettere il gruppo di failover host per l'host hanadb2 [impostazione predefinita]: premere INVIO per accettare il valore predefinito
     * Immettere il numero della partizione di archiviazione per l'host hanadb2 [<<assign automatically>>]: premere INVIO per accettare il valore predefinito
     * Immettere il gruppo di lavoro per l'host hanadb2 [impostazione predefinita]: premere INVIO per accettare il valore predefinito
     * Selezionare i ruoli per l'host hanadb3: immettere **2** (per standby)
     * Immettere il gruppo di failover host per l'host hanadb3 [impostazione predefinita]: premere INVIO per accettare il valore predefinito
     * Immettere il gruppo di lavoro per l'host hanadb3 [impostazione predefinita]: premere INVIO per accettare il valore predefinito
     * Immettere SAP HANA ID sistema: immettere **HN1**
     * Immettere il numero di istanza [00]: immettere **03**
     * Immettere il gruppo di lavoro host locale [impostazione predefinita]: premere INVIO per accettare il valore predefinito
     * Seleziona utilizzo sistema/immettere l'indice [4]: immettere **4** (per personalizzato)
     * Immettere il percorso dei volumi di dati [/hana/data/HN1]: premere INVIO per accettare il valore predefinito
     * Immettere il percorso dei volumi di log [/hana/log/HN1]: premere INVIO per accettare il valore predefinito
     * Limitare l'allocazione massima della memoria? [n]: immettere **n**
     * Immettere il nome host del certificato per l'host hanadb1 [hanadb1]: premere INVIO per accettare il valore predefinito
     * Immettere il nome host del certificato per l'host hanadb2 [hanadb2]: premere INVIO per accettare il valore predefinito
     * Immettere il nome host del certificato per l'host hanadb3 [hanadb3]: premere INVIO per accettare il valore predefinito
     * Immettere la password dell'amministratore di sistema (hn1adm): immettere la password
     * Immettere la password utente del database di sistema (SYSTEM): immettere la password del sistema
     * Conferma password utente database di sistema (sistema): immettere la password del sistema
     * Riavviare il sistema dopo il riavvio della macchina? [n]: immettere **n** 
     * Continuare (y/n): convalidare il riepilogo e se tutti gli elementi sembrano validi immettere **y**


2. **[1]** verificare Global. ini  

   Visualizzare Global. ini e verificare che sia attiva la configurazione per la comunicazione interna SAP HANA tra i nodi. Verificare la sezione **comunicazione**. Deve avere lo spazio degli indirizzi per **`hana`** subnet e `listeninterface` deve essere impostato su `.internal`. Verificare la sezione **internal_hostname_resolution**. Deve avere gli indirizzi IP per le macchine virtuali HANA che appartengono alla subnet **`hana`** .  

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

3. **[1]** aggiungere il mapping dell'host per assicurarsi che gli indirizzi IP del client siano utilizzati per la comunicazione client. Aggiungere la sezione `public_host_resolution` e aggiungere gli indirizzi IP corrispondenti dalla subnet del client.  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.23.0.5</b>
    map_<b>hanadb2</b> = <b>10.23.0.6</b>
    map_<b>hanadb3</b> = <b>10.23.0.7</b>
   </code></pre>

4. **[1]** riavviare SAP HANA per attivare le modifiche.  
   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** verificare che l'interfaccia client utilizzerà gli indirizzi IP della subnet **client** per la comunicazione.  
   <pre><code>
    sudo -u hn1adm /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.23.0.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.23.0.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.23.0.5</b>"
   </code></pre>

   Per informazioni dettagliate su come verificare la configurazione, vedere la nota SAP [2183363-configurazione di SAP Hana rete interna](https://launchpad.support.sap.com/#/notes/2183363) .  

6. Per ottimizzare SAP HANA per l'archiviazione dei file di Azure NetApp sottostante, impostare i parametri di SAP HANA seguenti:

   - `max_parallel_io_requests` **128**
   - `async_read_submit`
   - `async_write_submit_active`
   - `async_write_submit_blocks` **tutto**

   Per informazioni dettagliate, vedere la [Guida alla configurazione di SAP Hana su sistemi aff di NetApp con NFS](https://www.netapp.com/us/media/tr-4435.pdf). 

   A partire da SAP HANA sistemi 2,0 è possibile impostare i parametri in `global.ini`. Vedere la nota SAP [1999930](https://launchpad.support.sap.com/#/notes/1999930).  
   Per SAP HANA sistemi 1,0, versioni fino a SPS12, questi parametri possono essere impostati durante l'installazione, come descritto nella nota SAP [2267798](https://launchpad.support.sap.com/#/notes/2267798).  

7. Lo spazio di archiviazione usato da Azure NetApp Files ha un limite di dimensioni del file o 16 TB. SAP HANA non è in grado di riconoscere in modo implicito il limite di archiviazione e non creerà automaticamente un nuovo file di dati, quando viene raggiunto il limite delle dimensioni del file pari a 16 TB. Poiché SAP HANA tenta di espandere il file oltre i 16 TB, si verificheranno errori e infine si verificherà un arresto anomalo del server. 

   > [!IMPORTANT]
   > Per evitare che SAP HANA tenti di espandere i file di dati oltre il [limite di 16TB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-resource-limits) del Sybsystem di archiviazione, impostare i parametri seguenti in `global.ini`.  
   > -  datavolume_striping = true
   > - datavolume_striping_size_gb = 15000 per informazioni dettagliate, vedere la nota SAP [2400005](https://launchpad.support.sap.com/#/notes/2400005).
   > Prestare attenzione alla nota SAP [2631285](https://launchpad.support.sap.com/#/notes/2631285). 

## <a name="test-sap-hana-failover"></a>Test SAP HANA failover 

1. Simulare un arresto anomalo del nodo in un nodo del ruolo di lavoro SAP HANA  

   Eseguire i comandi seguenti come **HN1**ADM per acquisire lo stato dell'ambiente, prima di simulare l'arresto anomalo del nodo.  

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

   Eseguire il comando seguente come radice nel nodo del ruolo di lavoro, che in questo caso è **hanadb2** per simulare un arresto anomalo del nodo.  
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   Monitorare il sistema per il completamento del failover. Al termine dell'acquisizione dello stato, il failover dovrebbe avere un aspetto analogo a quello visualizzato di seguito.  
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
   > Per evitare ritardi con il failover di SAP HANA, quando un nodo sperimenta il kernel panic, impostare `kernel.panic` su 20 secondi in **tutte le** macchine virtuali Hana. La configurazione viene eseguita in `/etc/sysctl`. Riavviare le macchine virtuali per attivare la modifica. Il failover, quando un nodo si verifica quando il kernel panic può richiedere 10 o più minuti, se questa modifica non viene eseguita.  

2. Terminare il server dei nomi  
   Eseguire i comandi seguenti come **HN1**ADM per verificare lo stato dell'ambiente, prima del test:  

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

   Eseguire i comandi seguenti come **HN1**ADM sul nodo master attivo, che in questo caso è **hanadb1**.  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   Il **hanadb3** del nodo di standby assumerà il nome di nodo master. Stato della risorsa dopo il completamento del test di failover:  

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

   Avviare di nuovo l'istanza di HANA in **hanadb1**, ovvero nella stessa macchina virtuale in cui è stato terminato il server dei nomi. Il nodo **hanadb1** si riunirà all'ambiente e manterrà il relativo ruolo standby.  
   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Si prevede lo stato seguente dopo che SAP HANA è stato avviato in **hanadb1**:  
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

   A questo punto, terminare di nuovo il server dei nomi nel nodo master attualmente attivo, ovvero in hanadb3.  
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   Il nodo **hanadb1** riprenderà il ruolo del nodo master. Stato, dopo il completamento del test di failover sarà simile al seguente:
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

   Avviare SAP HANA in **hanadb3** -sarà pronto a fungere da nodo standby.  
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Stato dopo l'avvio di SAP HANA in **hanadb3**.  
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

* [Pianificazione e implementazione di macchine virtuali di Azure per SAP][planning-guide]
* [Distribuzione di macchine virtuali di Azure per SAP][deployment-guide]
* [Distribuzione DBMS di macchine virtuali di Azure per SAP][dbms-guide]
* Per informazioni su come stabilire la disponibilità elevata e pianificare il ripristino di emergenza di SAP 
* HANA in Azure (istanze di grandi dimensioni), vedere [disponibilità elevata e ripristino di emergenza di SAP Hana (istanze large) in Azure](hana-overview-high-availability-disaster-recovery.md).
* Per informazioni su come stabilire la disponibilità elevata e pianificare il ripristino di emergenza di SAP HANA nelle VM di Azure, vedere [disponibilità elevata di SAP Hana in macchine virtuali di Azure (VM)][sap-hana-ha]
