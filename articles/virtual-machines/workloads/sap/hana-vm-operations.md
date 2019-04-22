---
title: Configurazioni e operazioni dell'infrastruttura SAP HANA in Azure | Microsoft Docs
description: Guida operativa per i sistemi SAP HANA distribuiti in macchine virtuali di Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: acccc553c5b63b2acd0f9793b0397b25145449dd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59699331"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>Configurazioni e operazioni dell'infrastruttura SAP HANA in Azure
Questo articolo fornisce indicazioni su come configurare l'infrastruttura di Azure e gestire i sistemi SAP HANA distribuiti in Azure native macchine virtuali (VM). Questo articolo include anche informazioni sulla configurazione di SAP HANA scalabilità orizzontale per lo SKU di VM M128s. Questo articolo non è destinato a sostituire la documentazione SAP standard, che include il contenuto seguente:

- [Guida all'amministrazione di SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [Guide all'installazione di SAP](https://service.sap.com/instguides)
- [Note su SAP](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>Prerequisiti
Per usare questa guida sono necessarie conoscenze di base dei componenti di Azure seguenti:

- [Macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Rete e reti virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Archiviazione di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Per altre informazioni su SAP NetWeaver e altri componenti SAP in Azure, vedere la sezione [SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) della [documentazione di Azure](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Considerazioni di base sulla configurazione
Le sezioni seguenti offrono considerazioni di base sulla configurazione per la distribuzione di sistemi SAP HANA in macchine virtuali di Azure.

### <a name="connect-to-azure-virtual-machines"></a>Connettersi a macchine virtuali di Azure
Come documentato nel [macchine virtuali di Azure Guida alla pianificazione](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide), due metodi di base vengono utilizzati per connettersi alle macchine virtuali di Azure:

- Connettersi tramite internet ed endpoint pubblici in una VM JumpBox o nella macchina virtuale che esegue SAP HANA.
- Connessione tramite una rete [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) o Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Per gli scenari di produzione è necessaria la connettività da sito a sito tramite VPN o ExpressRoute. Questo tipo di connessione è necessario anche per gli scenari non di produzione che si inseriscono negli scenari di produzione in cui viene usato il software SAP. La figura seguente mostra un esempio di connettività intersito:

![Connettività intersito](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Scegliere i tipi di macchine virtuali di Azure
I tipi di macchine Virtuali di Azure da usare per gli scenari di produzione sono elencati nel [documentazione SAP per IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Per gli scenari non di produzione, è disponibile un'ampia gamma di tipi di VM di Azure native.

>[!NOTE]
> Per gli scenari non di produzione, usare i tipi di VM presenti nel [nota SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Per l'uso di macchine virtuali di Azure per scenari di produzione, controllare per le macchine virtuali in pubblicato da SAP certificate per SAP HANA [elenco di piattaforme IaaS Certificate](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

Per distribuire le macchine virtuali in Azure, usare:

- Il portale di Azure.
- I cmdlet di Azure PowerShell.
- L'interfaccia della riga di comando di Azure.

È anche possibile distribuire una piattaforma SAP HANA installata completa nei servizi macchine Virtuali di Azure tramite il [piattaforma SAP cloud](https://cal.sap.com/). Per informazioni sul processo di installazione, vedere [distribuire SAP S/4 Hana o BW/4 Hana in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h). Per informazioni su automazione rilasciato, vedere [questo articolo su GitHub](https://github.com/AzureCAT-GSI/SAP-HANA-ARM).

### <a name="choose-an-azure-storage-type"></a>Scegliere un tipo di archiviazione di Azure
Azure offre due tipi di archiviazione adatti per le VM di Azure che eseguono SAP HANA:  

- Unità disco rigido standard (HDD)
- Unità SSD Premium (SSD)

Per altre informazioni su questi tipi di disco, vedere [selezionare un tipo di disco](../../windows/disks-types.md).

Azure offre due metodi di distribuzione per i dischi rigidi virtuali in archiviazione standard di Azure e archiviazione premium. Se lo scenario lo permette, sfruttare i vantaggi offerti dalle distribuzioni di [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/).

Per un elenco di tipi di archiviazione e velocità effettiva IOPS e di archiviazione relativi contratti di servizio, vedere [documentazione di Azure per i dischi gestiti](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="configure-the-storage-for-azure-virtual-machines"></a>Configurare l'archiviazione per macchine virtuali di Azure

Probabile che mai /STATISTICS sui sottosistemi i/o e le relative funzionalità perché il fornitore dell'appliance assicurati che sono stati soddisfatti i requisiti di archiviazione minima per SAP HANA. Quando si compila manualmente l'infrastruttura di Azure, è necessario essere a conoscenza di alcuni di questi requisiti. È inoltre necessario comprendere i requisiti di configurazione suggeriti nelle sezioni seguenti. Per i casi in cui si configura le macchine virtuali si vuole che SAP HANA per l'esecuzione, potrebbe essere necessario:

- Abilitare il volume di lettura/scrittura in /hana/log per minimo 250 MB/sec per le dimensioni dei / o di 1 MB.
- Abilitare le attività di lettura di almeno 400 MB/sec per /hana/data per le dimensioni dei / o di 16 MB e 64 MB.
- Abilitare l'attività di scrittura di almeno 250 MB/sec per /hana/data con dimensioni dei / o 16 MB e 64 MB.

Archiviazione a bassa latenza è fondamentale per i sistemi DBMS, anche se DBMS, come SAP HANA, mantiene i dati in memoria. Il percorso critico per l'archiviazione riguarda in genere le scritture nel log delle transazioni dei sistemi DBMS. Tuttavia operazioni quali la scrittura di punti di salvataggio o caricamento dati in memoria dopo il ripristino di arresto anomalo del sistema può anche essere fondamentale. 

Uso di dischi premium di Azure per i volumi /hana/data e /hana/log è obbligatorio. Per ottenere la velocità effettiva minima di /hana/log e /hana/data come richiesto da SAP, compilare un sistema RAID 0 tramite mdadm o un Manager di volumi logici (LVM) su più dischi di archiviazione premium di Azure. Usare anche i volumi RAID come /hana/data e /hana/log volumi. È consigliabile usare le dimensioni di striping riportate di seguito per il sistema RAID 0:

- 64 KB o 128 KB per hana/data
- 32 KB per hana/log

> [!NOTE]
> Non è necessario configurare alcun livello di ridondanza con i volumi RAID poiché archiviazione standard e premium di Azure mantiene tre immagini di un disco rigido virtuale. L'uso di un volume RAID è esclusivamente per configurare i volumi che offrono una velocità effettiva i/o sufficiente.

Accumulare un numero di dischi rigidi virtuali di Azure di sotto di un sistema RAID è cumulabile da un lato di velocità effettiva IOPS e di archiviazione. Se si configura un sistema RAID 0 su 3 dischi di archiviazione premium di Azure x P30, offre tre volte il numero di IOPS e tre volte la velocità effettiva di archiviazione di un disco P30 di archiviazione premium di Azure singolo.

I suggerimenti di memorizzazione nella cache seguenti si presuppongono le caratteristiche dei / o per SAP HANA:

- C'è hanno pressoché alcun carico di lavoro di lettura per i file di dati HANA. Le eccezioni sono di grandi dimensioni i/o dopo il riavvio dell'istanza di HANA o quando i dati vengono caricati in HANA. Un altro caso di I/O in lettura di dimensioni maggiori sui file di dati è rappresentato dai backup di database HANA. Di conseguenza, la cache di lettura non ha senso perché, nella maggior parte dei casi, tutti i volumi di file di dati devono essere letto completamente.
- La scrittura sui file di dati si verifica in burst in base ai punti di salvataggio HANA e al ripristino da arresto anomalo del sistema HANA. La scrittura di punti di salvataggio è asincrona e si rivela tutt'altro alcuna transazione utente. La scrittura di dati durante il ripristino da arresto anomalo del sistema è un'operazione critica a livello di prestazioni per ottenere che il sistema risponda di nuovo rapidamente. Ripristino di arresto anomalo del sistema deve essere situazioni piuttosto eccezionali.
- Esistono pochissime operazioni di lettura dai file di rollforward HANA. Le eccezioni sono i/o elevato quando si esegue backup del log delle transazioni, del recupero di arresto anomalo del sistema o la fase di riavvio di un'istanza di HANA.  
- Il carico principale in base al file di log di rollforward di SAP HANA è di scrittura. A seconda della natura del carico di lavoro, è possibile avere i/o con dimensioni di 4 KB o, in altri casi, i/o le dimensioni pari o superiore a 1 MB. La latenza di scrittura rispetto al log di rollforward SAP HANA è critica dal punto di vista delle prestazioni.
- Tutte le operazioni di scrittura devono essere resi persistenti su disco in modo affidabile.

In seguito a questi modelli dei / o osservati da SAP HANA, impostare la memorizzazione nella cache per i diversi volumi che usano archiviazione premium di Azure per:

- **/hana/data**: Nessuna memorizzazione nella cache.
- **/hana/log**: Nessuna memorizzazione nella cache, con un'eccezione per le macchine virtuali serie M (vedere più avanti in questo articolo).
- **/hana/shared**: Leggere la memorizzazione nella cache.


Inoltre, tenere presente la velocità effettiva dei / o VM generale quando si ridimensiona o si decide in una macchina virtuale. Complessivamente la velocità effettiva di archiviazione della macchina virtuale è documentata in [dimensioni delle macchine virtuali ottimizzate per la memoria](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

#### <a name="linux-io-scheduler-mode"></a>Modalità di utilità di pianificazione dei / o Linux
Linux offre varie modalità di pianificazione I/O diverse. Un comune da fornitori di Linux e SAP si consiglia di impostare la modalità di utilità di pianificazione i/o per i volumi di disco dal **cfq** modalità per il **noop** modalità. Per altre informazioni, vedere [SAP Note #1984798](https://launchpad.support.sap.com/#/notes/1984787). 


#### <a name="storage-solution-with-write-accelerator-for-azure-m-series-virtual-machines"></a>Soluzione di archiviazione con acceleratore di scrittura per Azure le macchine virtuali serie M
 Scrivere che acceleratore è una funzionalità di Azure che viene implementata per le macchine virtuali serie M di Azure in modo esclusivo. Lo scopo della funzionalità è migliorare la latenza dei / o delle scritture per archiviazione premium di Azure. Per SAP HANA, l'uso dell'acceleratore di scrittura è previsto solo sul volume /hana/log. Per questo motivo, è necessario modificare le configurazioni illustrate finora. La modifica principale è la suddivisione tra /hana/data e /hana/log per poter usare l'acceleratore di scrittura sul volume /hana/log solo. 

> [!IMPORTANT]
> Certificazione SAP HANA per Azure è esclusivo per acceleratore di scrittura per il volume hana/log delle macchine virtuali serie M. Di conseguenza, le distribuzioni di scenario di produzione SAP HANA in Azure serie M di macchine virtuali dovrebbero essere configurato con acceleratore di scrittura per la/hana/log volume.

> [!NOTE]
> Per gli scenari di produzione, controllare se un determinato tipo di macchina virtuale è supportato da SAP per SAP HANA nella [documentazione SAP per IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

La tabella seguente illustra le configurazioni consigliate.

| SKU di VM | RAM | / O massimo VM<br /> throughput | /hana/data | /hana/log | /hana/shared | volume /root | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/sec | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/sec | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1.000 MB/sec | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1.000 giB | 1.000 MB/sec | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1.750 giB | 1.000 MB/sec | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2.000 giB | 2.000 MB/sec |3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3.800 giB | 2.000 MB/sec | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |

Controllare se la velocità effettiva di archiviazione per i diversi volumi suggeriti soddisfa il carico di lavoro che si desidera eseguire. Se il carico di lavoro richiede volumi più elevati per /hana/data e /hana/log, aumentare il numero di archiviazione premium di Azure i dischi rigidi virtuali. Ridimensionamento di un volume con più dischi rigidi virtuali rispetto a elencati aumenta il numero di IOPS e velocità effettiva dei / o entro i limiti del tipo di macchina virtuale di Azure.

L'acceleratore di scrittura funziona solo in combinazione con [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Almeno la i dischi di archiviazione premium di Azure che costituiscono il volume /hana/log devono essere distribuiti come dischi gestiti.

Sono previsti limiti per l'archiviazione premium di Azure i dischi rigidi virtuali per ogni macchina virtuale che può supportare l'acceleratore di scrittura. I limiti correnti sono:

- 16 dischi rigidi virtuali per un M128xx della macchina virtuale.
- 8 dischi rigidi virtuali per M64xx una macchina virtuale.
- 4 file VHD per un M32xx della macchina virtuale.

Per altre informazioni su come abilitare l'acceleratore di scrittura, vedere [acceleratore di scrittura](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Per altre informazioni su e restrizioni per l'acceleratore di scrittura, vedere la documentazione stessa.


#### <a name="cost-conscious-azure-storage-configuration"></a>Configurazione di archiviazione di Azure attento ai costi
La tabella seguente mostra una configurazione di tipi di macchine virtuali comunemente usati dai clienti per ospitare SAP HANA in macchine virtuali di Azure. Potrebbero essere presenti alcuni tipi di VM che non soddisfano tutti i criteri minimi per SAP HANA. È anche possibile che alcuni tipi di VM che non sono ufficialmente supportati con SAP HANA da SAP. Finora, tali macchine virtuali hanno eseguito correttamente per gli scenari non di produzione. 

> [!NOTE]
> Per gli scenari di produzione, controllare se un determinato tipo di macchina virtuale è supportato da SAP per SAP HANA nella [documentazione SAP per IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).


| SKU di VM | RAM | / O massimo VM<br /> throughput | /hana/data and /hana/log<br /> con striping con LVM o mdadm | /hana/shared | volume /root | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/sec | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E16v3 | 128 GiB | 384 MB/sec | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256 GiB | 768 MB/sec | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 432 GiB | 1,200 MB/sec | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GiB | 2.000 MB/sec | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M32ts | 192 GiB | 500 MB/sec | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M32ls | 256 GiB | 500 MB/sec | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M64ls | 512 GiB | 1.000 MB/sec | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 |1 x S30 |
| M64s | 1.000 giB | 1.000 MB/sec | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1.750 giB | 1.000 MB/sec | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2.000 giB | 2.000 MB/sec |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3.800 giB | 2.000 MB/sec | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


I dischi sono consigliati per i tipi di macchine Virtuali più piccoli con 3 x P20 P20 i volumi per quanto riguarda le indicazioni di spazio che sono espressi nel [white paper su archiviazione di SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). La scelta visualizzata nella tabella è stata effettuata per fornire velocità effettiva del disco sufficienti per SAP HANA. Se è necessario modificare il **backup dihana/** volume, che è stato ridimensionato per mantenere backup che rappresentano il doppio del volume di memoria, è possibile apportare modifiche. 

Controllare se la velocità effettiva di archiviazione per i diversi volumi suggeriti soddisfa il carico di lavoro che si desidera eseguire. Se il carico di lavoro richiede volumi più elevati per /hana/data e /hana/log, aumentare il numero di archiviazione premium di Azure i dischi rigidi virtuali. Ridimensionamento di un volume con più dischi rigidi virtuali rispetto a elencati aumenta il numero di IOPS e velocità effettiva dei / o entro i limiti del tipo di macchina virtuale di Azure. 

> [!NOTE]
> Le configurazioni precedenti non beneficiano [macchina virtuale di Azure l'accesso single VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) perché usa una combinazione di archiviazione premium di Azure e archiviazione standard di Azure. La selezione è stata scelta per ottimizzare i costi. Scegliere l'archiviazione premium per tutti i dischi nella tabella in cui sono elencati come risorsa di archiviazione Azure standard (Sxx) per rendere conformi con il contratto di servizio della macchina virtuale singola Azure la configurazione della macchina virtuale.


> [!NOTE]
> Le raccomandazioni per la configurazione del disco di destinazione i requisiti minimi che SAP fornisce il provider di infrastruttura. Negli scenari di carico di lavoro e le distribuzioni ai clienti effettivi, queste indicazioni non offrono funzionalità sufficienti in alcune situazioni. Ad esempio, un cliente necessario un ricaricamento dei dati più veloce dopo un riavvio HANA, o le configurazioni di backup richiesto maggiore larghezza di banda per lo spazio di archiviazione. Gli altri casi comprendono /hana/log, in cui 5.000 IOPS non erano sufficienti per il carico di lavoro specifico. Usare queste indicazioni come punto di partenza e adattarli in base ai requisiti del carico di lavoro.
>  

### <a name="set-up-azure-virtual-networks"></a>Configurare le reti virtuali di Azure
Quando si dispone di connettività site-to-site in Azure tramite VPN o Azure ExpressRoute, è necessario disporre almeno una rete virtuale di Azure che è connessa tramite un gateway virtuale al circuito ExpressRoute o VPN. Nelle distribuzioni semplici, il gateway virtuale può essere distribuito in una subnet di rete virtuale di Azure che ospita le istanze di SAP HANA troppo. 

Per installare SAP HANA, creare altre due subnet nella rete virtuale di Azure. Una subnet ospita le macchine virtuali necessarie per eseguire le istanze di SAP HANA, L'altra subnet viene eseguito JumpBox o macchine virtuali di gestione per ospitare SAP HANA Studio, altri software di gestione o l'applicazione in uso.

> [!IMPORTANT]
> La configurazione [Appliance virtuali di rete di Azure](https://azure.microsoft.com/solutions/network-appliances/) nel percorso di comunicazione tra l'applicazione SAP e il livello DBMS di un SAP NetWeaver-, Hybris- o sistema basati su S/4HANA SAP non è supportata. Questa restrizione è per motivi di prestazioni e funzionalità. Il percorso di comunicazione tra il livello applicazione SAP e il livello DBMS deve essere uno diretto. Non include la restrizione [gruppo di sicurezza dell'applicazione (ASG) e sicurezza di rete del gruppo di regole (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) se tali regole ASG e sicurezza di rete consentano a un percorso di comunicazione diretta. 
>
> Altri scenari in cui non sono supportati Appliance virtuali di rete sono: 
>
> - Percorsi di comunicazione tra macchine virtuali di Azure che rappresentano Linux Pacemaker nodi e cluster dispositivi SBD come descritto in [disponibilità elevata per SAP NetWeaver in macchine virtuali di Azure su SUSE Linux Enterprise Server for SAP Applications](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse).
> - Percorsi di comunicazione tra macchine virtuali di Azure e Windows Server Scale-Out File Server impostato come descritto in [clustering di un'istanza di SAP ASCS/SCS in un cluster di failover Windows tramite una condivisione file in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). 
>
> Appliance virtuali di rete nei percorsi di comunicazione possono facilmente raddoppiare la latenza di rete tra i partner di comunicazione due. È anche possibile limitare la velocità effettiva in percorsi critici tra il livello applicazione SAP e il livello DBMS. In alcuni scenari di clienti, Appliance virtuali di rete possono provocare cluster Linux Pacemaker esito negativo. Questi sono casi in cui comunicano le comunicazioni tra i nodi del cluster Pacemaker di Linux per il dispositivo SBD attraverso un'appliance virtuale di rete.  
> 

> [!IMPORTANT]
> Un altro di progettazione del *non* supportata è la separazione del livello applicazione SAP e il livello DBMS in diverse reti virtuali di Azure che non sono [eseguire il peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) tra loro. È consigliabile separare il livello applicazione SAP e il livello DBMS usando subnet all'interno di una rete virtuale di Azure anziché tramite diverse reti virtuali di Azure. 
>
>Se si decide di non segue il suggerimento e invece separare i due livelli in diverse reti virtuali, le due reti virtuali devono trovarsi [peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). 
>
> Tenere presente che traffico di rete tra due [peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) reti virtuali di Azure è soggetta a costi di trasferimento. Volume di dati molto grandi che è costituito da diversi terabyte verrà scambiati tra il livello applicazione SAP e il livello DBMS. Se il livello applicazione SAP e il livello DBMS sono separati tra due con peering reti virtuali di Azure, è possibile accumulare sostanziale dei costi. 

Quando si installano le macchine virtuali per l'esecuzione di SAP HANA, queste macchine virtuali devono essere dotate di:

- Due NIC virtuale installata. Si connette una scheda di rete alla subnet di gestione. Un'altra interfaccia di rete connette dalla rete locale o ad altre reti all'istanza di SAP HANA nella VM di Azure.
- Indirizzi IP privati statici distribuiti per entrambe le schede di interfaccia di rete virtuali.

> [!NOTE]
> Assegnazione di indirizzi IP statici tramite Azure si intende assegnare gli utenti a singole interfacce di rete virtuale. Non assegnare indirizzi IP statici all'interno del sistema operativo guest a un'interfaccia di rete virtuale. Alcuni servizi di Azure come Backup di Azure si basano sul fatto che al minimo la scheda virtuale primaria è impostata su DHCP e non per gli indirizzi IP statici. Per altre informazioni, vedere [backup di macchine virtuali di Azure di risolvere i problemi](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Per assegnare più indirizzi IP statici a una macchina virtuale, assegnare più schede di rete virtuale a una macchina virtuale.
>
>

Per le distribuzioni sono permanenti, creare un'architettura di rete del Data Center virtuale in Azure. Questa architettura si consiglia la separazione tra il gateway di rete virtuale di Azure che si connette a un'istanza locale in una rete virtuale di Azure separata. Questa rete virtuale separata ospita tutto il traffico che lascia l'azienda in locale o a internet. Usando questo approccio, è possibile distribuire software per controllare e registrare il traffico che entra in Data Center virtuale in Azure in questa rete virtuale hub separato. In questo modo, è necessario una rete virtuale che ospita tutte le configurazioni software e che si riferisce al traffico in ingresso e in uscita per la distribuzione di Azure.


Per altre informazioni sull'approccio di Data Center virtuale e sulla progettazione di rete virtuale di Azure correlati, vedere: 

- [Azure Data Center virtuale: Una prospettiva di rete](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter).
- [Data Center virtuale di Azure e il piano di controllo di enterprise](https://docs.microsoft.com/azure/architecture/vdc/).


>[!NOTE]
>Il traffico scambiato tra una rete virtuale hub e una rete virtuale spoke usando [peering di rete virtuale di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) sono soggette a ulteriori [costi](https://azure.microsoft.com/pricing/details/virtual-network/). Basato su questi costi, si potrebbe essere necessario a compromessi tra l'esecuzione di una struttura di rete hub-spoke strict e in esecuzione più [gateway Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) connettersi spoke per ignorare il peering di rete virtuale. 
>
> I gateway ExpressRoute di Azure introducono ulteriori [costi](https://azure.microsoft.com/pricing/details/vpn-gateway/) troppo. Potrebbero verificarsi anche costi aggiuntivi per il software di terze parti che consente di accedere, controllare e monitorare il traffico di rete. Prendere in considerazione i costi per lo scambio di dati tramite peering rispetto a quelli creati da altri gateway ExpressRoute e licenze software di rete virtuale. È possibile decidere su micro-segmentazione entro una rete virtuale con subnet come unità di isolamento anziché le reti virtuali.


Per una panoramica dei diversi metodi che consentono di assegnare indirizzi IP, vedere [metodi di allocazione in Azure e i tipi di indirizzi IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

Per le macchine virtuali che eseguono SAP HANA, funzionamento con gli indirizzi IP statici assegnati. Il motivo è che alcuni attributi di configurazione per HANA fanno riferimento a indirizzi IP.

[Gli Nsg Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) vengono usati per indirizzare il traffico viene instradato all'istanza di SAP HANA o JumpBox. il Nsg e infine [gruppi di sicurezza delle applicazioni](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups) sono associati alla subnet di SAP HANA e alla subnet di gestione.

L'immagine seguente mostra una panoramica di uno schema di distribuzione approssimativo per SAP HANA che segue un'architettura di rete virtuale hub e spoke:

![Schema di distribuzione approssimativo per SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)

Per distribuire SAP HANA in Azure senza una connessione site-to-site, dello scudo di protezione dell'istanza di SAP HANA dalla rete internet pubblica e lo nasconde dietro un proxy di inoltro. In questo scenario di base, la distribuzione si basa sui servizi incorporati di Azure DNS per risolvere i nomi host. Questi servizi sono particolarmente importanti in distribuzioni più complesse in cui vengono usati indirizzi IP pubblici. Usare gli Nsg di Azure e [Appliance virtuali di rete di Azure](https://azure.microsoft.com/solutions/network-appliances/) per monitorare il routing da internet, l'architettura di rete virtuale di Azure in Azure. 

L'immagine seguente mostra uno schema approssimativo per informazioni su come distribuire SAP HANA senza connessione site-to-site in un'architettura di rete virtuale hub e spoke:
  
![Schema di distribuzione approssimativo per SAP HANA senza connessione da sito a sito](media/hana-vm-operations/hana-simple-networking2.PNG)
 

Per un'altra descrizione di come usare Appliance virtuali di rete di Azure per controllare e monitorare l'accesso da internet senza l'architettura di rete virtuale hub e spoke, vedere [distribuire Appliance virtuali di rete a disponibilità elevata](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).


## <a name="configure-azure-infrastructure-for-sap-hana-scale-out"></a>Configurare l'infrastruttura di Azure per aumentare le istanze SAP HANA
Microsoft dispone di uno SKU di VM serie M che è certificato per una configurazione di tipo scale-out di SAP HANA. Il tipo di VM M128s è certificato per un aumento del numero massimo di 16 nodi. Per le modifiche nelle certificazioni di tipo scale-out di SAP HANA in macchine virtuali di Azure, vedere la [elenco di piattaforme IaaS Certificate](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

Il numero minimo di versioni del sistema operativo usato per distribuire le configurazioni di scalabilità orizzontale in macchine virtuali di Azure è:

- SUSE Linux 12 SP3.
- Red Hat Linux 7.4.

Per la certificazione di scalabilità orizzontale di 16 nodi:

- Un nodo è il nodo master.
- Un massimo di 15 nodi sono nodi di lavoro.

>[!NOTE]
>Nelle distribuzioni di tipo scale-out macchina virtuale di Azure, non è possibile usare un nodo di standby.
>

Esistono due motivi per cui non è possibile configurare un nodo di standby:

- A questo punto, Azure non ha servizi NFS nativi. Di conseguenza, le condivisioni NFS devono essere configurate con l'aiuto di funzionalità di terze parti.
- Nessuna delle configurazioni NFS di terze parti può soddisfare i criteri di latenza di archiviazione per SAP HANA con le soluzioni distribuite in Azure.

Di conseguenza, i volumi /hana/data e /hana/log non possono essere condivisi. Non condividere questi volumi dei nodi del singoli impedisce l'uso di un nodo di standby di SAP HANA in una configurazione di scalabilità orizzontale.

L'immagine seguente mostra la struttura di base per un singolo nodo in una configurazione di scalabilità orizzontale:

![Nozioni di base sullo scale-out di un singolo nodo](media/hana-vm-operations/scale-out-basics.PNG)

La configurazione di base di un nodo di macchina virtuale per lo scale-out di SAP HANA è simile alla seguente:

- Per /hana/shared, creare un cluster NFS a disponibilità elevata basato su SUSE Linux 12 SP3. Il cluster ospita le condivisioni NFS /hana/shared della configurazione di scalabilità orizzontale e SAP NetWeaver o BW/4 Hana Central Services. Per informazioni su come creare una configurazione di questo tipo, vedere [disponibilità elevata per NFS in macchine virtuali di Azure su SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
- Tutti gli altri volumi di dischi non vengono condivise tra i vari nodi e non sono basate su NFS. Configurazioni di installazione e i passaggi per le installazioni di HANA di tipo scale-out con non condivisa /hana/data e /hana/log vengono forniti più avanti in questo articolo.

>[!NOTE]
>Il cluster NFS a disponibilità elevata visualizzato finora nelle immagini è supportato solo con SUSE Linux. Una soluzione NFS a disponibilità elevata, basata su Red Hat verrà suggerita in un secondo momento.

I volumi per i nodi di ridimensionamento è identico a quello di scalabilità verticale, ad eccezione di /hana/shared. Nella tabella seguente illustra i tipi e le dimensioni consigliate per lo SKU di VM M128s.

| SKU di VM | RAM | / O massimo VM<br /> throughput | /hana/data | /hana/log | volume /root | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- |
| M128s | 2.000 giB | 2.000 MB/s |3 x P30 | 2 x P20 | 1 x P6 | 1 x P6 | 2 x P40 |


Controllare se la velocità effettiva di archiviazione per i diversi volumi suggeriti soddisfa il carico di lavoro che si desidera eseguire. Se il carico di lavoro richiede volumi più elevati per /hana/data e /hana/log, aumentare il numero di archiviazione premium di Azure i dischi rigidi virtuali. Ridimensionamento di un volume con più dischi rigidi virtuali rispetto a elencati aumenta il numero di IOPS e velocità effettiva dei / o entro i limiti del tipo di macchina virtuale di Azure. Si applicano anche acceleratore di scrittura nei dischi che formano il volume /hana/log.
 

Per una formula che definisce la dimensione del volume hana/condiviso per la scalabilità orizzontale come le dimensioni della memoria di un singolo nodo di lavoro per ogni quattro nodi di lavoro, vedere [requisiti di archiviazione di SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

Supponendo che si esegue SAP HANA scalabilità orizzontale Certificate M128s macchina virtuale di Azure con circa 2 TB di memoria, le indicazioni SAP vengano riepilogate come segue:

- Per un nodo master e un massimo di quattro nodi di lavoro, le dimensioni del volume /hana/shared sono 2 TB.
- Per un nodo master e otto e cinque nodi di lavoro, le dimensioni del volume /hana/shared sono 4 TB.
- Per un nodo master e nodi di lavoro da 9 a 12, le dimensioni del volume /hana/shared sono 6 TB.
- Per un nodo master e 12 a 15 nodi di lavoro, le dimensioni del volume /hana/shared sono 8 TB.

Le altre importanti progettazione che viene visualizzato nell'immagine della configurazione del nodo singolo per una macchina virtuale SAP HANA di tipo scale-out è la rete virtuale con la configurazione della subnet. SAP consiglia una separazione del traffico destinati al client e dell'applicazione dalle comunicazioni tra i nodi HANA. 

Per raggiungere questo obiettivo, collegare due NIC virtuali diversi per la macchina virtuale, come illustrato nelle figure. Entrambe le schede di rete virtuale sono in subnet diverse e presentano due diversi indirizzi IP. È quindi possibile controllare il flusso del traffico con le regole di routing mediante Nsg o route definite dall'utente.

In particolare in Azure, non esistono alcun mezzo e metodi per applicare la qualità del servizio e le quote nelle specifiche interfacce di rete virtuale. Di conseguenza, la separazione della comunicazione tra nodi e destinati al client e dell'applicazione non si apre qualsiasi possibilità di definire le priorità di un flusso di traffico rispetto a un altro. Al contrario, la separazione rimane una misura di sicurezza per le comunicazioni tra nodi delle configurazioni di tipo scale-out di schermatura.  

>[!IMPORTANT]
>SAP consiglia di separare il traffico di rete per l'applicazione client e lato tra nodi il traffico e come descritto in questo articolo. È consigliabile inserire un'architettura posto come illustrato nell'immagine precedente.
>

L'immagine seguente mostra l'architettura di rete minime richieste dal punto di vista di rete:

![Nozioni di base sullo scale-out di un singolo nodo](media/hana-vm-operations/scale-out-networking-overview.PNG)

I limiti supportati finora sono 15 nodi di lavoro oltre a un nodo master.

L'immagine seguente mostra l'architettura di archiviazione da un punto di vista dell'archiviazione:


![Nozioni di base sullo scale-out di un singolo nodo](media/hana-vm-operations/scale-out-storage-overview.PNG)

Il volume /Hana/Shared. si trova sulla configurazione di condivisione NFS a disponibilità elevata. Tutte le altre unità vengono montate in locale per le singole macchine virtuali. 

### <a name="highly-available-nfs-share"></a>Condivisione NFS a disponibilità elevata
Finora, il cluster NFS a disponibilità elevata sta lavorando solo con SUSE Linux. Per informazioni sull'installazione, vedere [disponibilità elevata per NFS in macchine virtuali di Azure su SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs). Se si non condivide il cluster NFS con tutte le altre configurazioni HANA esterno della rete virtuale di Azure che esegue istanze di SAP HANA, è necessario installarlo nella stessa rete virtuale. Installarlo nella propria subnet e assicurarsi che non tutto il traffico non autorizzato possa accedere alla subnet. Al contrario, limitare il traffico a tale subnet per gli indirizzi IP della VM che eseguono il traffico al volume /hana/shared.

Le raccomandazioni relative all'interfaccia di rete virtuale di una VM di tipo scale-out HANA che instrada il traffico /hana/shared, sono:

- Poiché il traffico verso /hana/shared è moderato, distribuirla tramite l'interfaccia di rete virtuale che viene assegnato alla rete di client nella configurazione minima.
- Infine, per il traffico per hana/condiviso, distribuire una terza subnet nella rete virtuale in cui si distribuisce la configurazione di tipo scale-out di SAP HANA. Assegnare una scheda di rete virtuale terzo ospitata in tale subnet. Utilizzare la terza scheda di rete virtuale e indirizzo IP associato per il traffico nella condivisione NFS. È quindi possibile applicare regole di routing e accesso separate.

>[!IMPORTANT]
>Il traffico di rete tra le macchine virtuali con SAP HANA in una modalità di scalabilità orizzontale distribuita e NFS a disponibilità elevata in nessuna circostanza può essere instradato attraverso un [appliance virtuale di rete](https://azure.microsoft.com/solutions/network-appliances/) o simili dispositivi di rete. Azure Nsg sono tali dispositivi. Controllare le regole di routing per assicurarsi che siano deviate Appliance virtuali di rete o simili dispositivi di rete quando accedono a NFS a disponibilità elevata condividono dalle macchine virtuali che eseguono SAP HANA.
> 

Se si vuole condividere il cluster NFS a disponibilità elevata tra le configurazioni di SAP HANA, spostare tutte le configurazioni di HANA nella stessa rete virtuale. 
 

### <a name="install-an-sap-hana-scale-out-in-azure"></a>Installare un scale-out di SAP HANA in Azure
Per installare una configurazione SAP di scalabilità orizzontale, seguire questa procedura generale:

- Distribuire nuovi o adattare nuova infrastruttura di rete virtuale di Azure.
- Distribuire le nuove macchine virtuali tramite volumi di archiviazione premium gestiti da Azure.
- Distribuire un nuovo o adattare un cluster esistente di NFS a disponibilità elevata.
- Adattare il routing di rete per assicurarsi che, ad esempio, la comunicazione tra nodi tra le macchine virtuali non viene instradata attraverso un [appliance virtuale di rete](https://azure.microsoft.com/solutions/network-appliances/). Lo stesso vale per il traffico tra le macchine virtuali e del cluster NFS a disponibilità elevata.
- Installare il nodo master SAP HANA.
- Adattare i parametri di configurazione del nodo master SAP HANA.
- Continuare l'installazione dei nodi di lavoro SAP HANA.

#### <a name="install-sap-hana-in-a-scale-out-configuration"></a>Installare SAP HANA in una configurazione di scalabilità orizzontale
Viene distribuita l'infrastruttura di macchine Virtuali di Azure e le altre operazioni preliminari sono terminate. A questo punto, per installare le configurazioni di tipo scale-out di SAP HANA, seguire questa procedura:

- Installare il nodo master SAP HANA in base alla documentazione di SAP.
- *Dopo l'installazione, modificare il file Global e aggiungere il parametro ' basepath_shared = no' per il Global*. Questo parametro consente di SAP HANA per l'esecuzione di scalabilità orizzontale senza volumi /hana/log e /hana/data condiviso tra i nodi. Per altre informazioni, vedere [SAP Note #2080991](https://launchpad.support.sap.com/#/notes/2080991).
- Dopo aver modificato il parametro Global, riavviare l'istanza di SAP HANA.
- Aggiungere altri nodi di lavoro. Per altre informazioni, vedere [Guida all'amministrazione di SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html). Specificare la rete interna per la comunicazione tra i nodi di SAP HANA durante l'installazione o in un secondo momento usando, ad esempio, di hdblcm locale. Per altre informazioni, vedere [SAP Note #2183363](https://launchpad.support.sap.com/#/notes/2183363). 

Dopo questa routine di installazione, la configurazione di scalabilità orizzontale che è stato installato Usa dischi non condivisi per eseguire /hana/data e /hana/log. Il volume hana/condiviso viene posizionato su disponibilità elevata condivisione NFS.


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>SAP HANA 2.0 suddivisione in livelli dinamica per macchine virtuali di Azure

Oltre le certificazioni SAP HANA in macchine virtuali serie M di Azure, SAP HANA 2.0 suddivisione in livelli dinamico (DT 2.0) è supportato anche in Microsoft Azure. Per collegamenti alla documentazione di suddivisione in livelli dinamica di SAP HANA, vedere la sezione "Collega a DT documentazione 2.0" più avanti in questo articolo. Non esiste alcuna differenza nell'installazione del prodotto o operativo, ad esempio, tramite il pannello di controllo di SAP HANA all'interno di una macchina virtuale di Azure, ma alcuni elementi importanti sono obbligatori per il supporto ufficiale su Azure. Punti chiave seguenti sono descritti nelle sezioni seguenti. 

SAP HANA DT 2.0 non è supportato da SAP BW o S4HANA. I principali casi d'uso sono subito le applicazioni native di HANA.


### <a name="overview"></a>Panoramica

La figura seguente offre una panoramica del supporto DT 2.0 in Microsoft Azure. Seguire questi requisiti obbligatori per assicurare la conformità con la certificazione ufficiale:

- DT 2.0 deve essere installato in una VM di Azure dedicata. Potrebbe non essere eseguita nella stessa VM in cui SAP HANA viene eseguito.
- SAP HANA e le macchine virtuali 2.0 DT devono essere distribuite all'interno della stessa rete virtuale di Azure.
- Le macchine virtuali di 2.0 di DT e SAP HANA deve essere distribuite con rete accelerata di Azure abilitata.
- Il tipo di archiviazione per le macchine virtuali 2.0 DT deve essere archiviazione premium di Azure.
- Più dischi di Azure devono essere collegati alla macchina virtuale 2.0 DT.
- Creazione di un software RAID o volume con striping tramite LVM o mdadm, è necessario con lo striping tra i dischi di Azure.

Le sezioni seguenti forniscono una spiegazione più approfondita.

![Panoramica dell'architettura di SAP HANA 2.0 DT](media/hana-vm-operations/hana-dt-20.PNG)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>VM di Azure dedicata per SAP HANA DT 2.0

Nell'ambiente IaaS di Azure, DT 2.0 è supportato solo in una macchina virtuale dedicata. DT 2.0 non è consentita l'esecuzione nella stessa VM di Azure in cui è in esecuzione l'istanza di HANA. Inizialmente, due tipi di macchina virtuale sono utilizzabile per l'esecuzione di SAP HANA 2.0 DT:

- M64-32ms 
- E32sv3 

Per descrizioni dei tipi VM, vedere [dimensioni delle macchine virtuali ottimizzate per la memoria](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

Dato l'idea alla base di DT 2.0, che riguarda l'offload dei dati meno attivi per ridurre i costi, è opportuno usare dimensioni di macchina virtuale corrispondente. Non sono presenti regole strict per le combinazioni possibili. Tutto dipende dal carico di lavoro del cliente specifico.

La tabella seguente illustra le configurazioni consigliate.

| Tipo di VM SAP HANA | Tipo di VM DT 2.0 |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms | E32sv3 |
| M64s | E32sv3 |


Tutte le combinazioni di macchine virtuali della serie M certificate per SAP HANA con DT 2.0 le macchine virtuali supportate, ad esempio M64 32ms ed E32sv3, sono possibili.


### <a name="azure-networking-and-sap-hana-dt-20"></a>Rete di Azure e SAP HANA DT 2.0

Installazione DT 2.0 in una macchina virtuale dedicata richiede velocità effettiva di rete tra la macchina virtuale 2.0 DT e macchina virtuale di SAP HANA con un minimo di 10 GB. Di conseguenza, è necessario posizionare tutte le macchine virtuali all'interno della stessa rete virtuale di Azure e abilitare la rete accelerata di Azure.

Per altre informazioni sulle rete accelerata di Azure, vedere [creare una macchina virtuale Linux con rete accelerata](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).

### <a name="vm-storage-for-sap-hana-dt-20"></a>Archiviazione VM per SAP HANA 2.0 DT

In base alle linee guida consigliate DT 2.0, la velocità effettiva IO disco minima è 50 MB/sec per ogni core fisico. Osservando la specifica per i due tipi di macchine Virtuali di Azure, sono supportati per DT 2.0, il limite di velocità effettiva dei / o per la macchina virtuale di dimensioni massime del disco è:

- **E32sv3**:   768 MB/sec, rimosso dalla cache, ovvero un rapporto di 48 MB/sec per ogni core fisico
- **M64-32ms**:  1.000 MB/sec, rimosso dalla cache, ovvero un rapporto di 62,5 MB/sec per ogni core fisico

È necessario collegare più dischi di Azure per la macchina virtuale 2.0 DT e creazione di un RAID software tramite lo striping a livello di sistema operativo per raggiungere il limite massimo di velocità effettiva del disco per ogni macchina virtuale. Un singolo disco di Azure non può fornire la velocità effettiva per raggiungere il limite massimo di macchine Virtuali. Archiviazione premium di Azure è obbligatorio eseguire DT 2.0. 

- Per altre informazioni sui tipi di dischi di Azure disponibili, vedere [selezionare un tipo di disco per le macchine virtuali Windows di Azure IaaS](../../windows/disks-types.md).
- Per altre informazioni su come creare un RAID software tramite mdadm, vedere [configurare un RAID software in Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid).
- Per altre informazioni su come configurare LVM per creare un volume con striping per la massima velocità effettiva, vedere [configurare LVM in una VM Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm).

A seconda dei requisiti di dimensioni, sono disponibili diverse opzioni per raggiungere la velocità effettiva massima di una macchina virtuale. Di seguito sono elencate le possibili configurazioni dei dischi volume di dati per ogni tipo di VM DT 2.0 per raggiungere il limite massimo di velocità effettiva della VM. La macchina virtuale E32sv3 viene considerata un livello di base per i carichi di lavoro più piccoli. Se non è sufficientemente veloce, potrebbe essere necessario ridimensionare la macchina virtuale a M64 32ms.

Perché la VM M64 32ms ha una grande quantità di memoria, il carico dei / o potrà non raggiungere il limite, specialmente per carichi di lavoro a elevato utilizzo di lettura. Numero di dischi inferiore nel set di striping può essere sufficienti a seconda del carico di lavoro specifici del cliente. Per essere al sicuro, le seguenti configurazioni del disco sono state scelte per garantire la massima velocità effettiva.


| SKU di VM | Configurazione disco 1 | Configurazione disco 2 | Configurazione disco 3 | Configurazione disco 4 | Configurazione disco 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50 -> 16 TB | 4 x P40 -> 8 TB | 5 x P30 -> 5 TB | 7 x P20 -> 3,5 TB | 8 x P15 -> 2 TB | 
| E32sv3 | 3 x P50 -> 12 TB | 3 x P40 -> 6 TB | 4 x P30 -> 4 TB | 5 x P20 -> 2,5 TB | 6 x P15 -> 1,5 TB | 


In particolare se il carico di lavoro a elevato utilizzo di lettura, attivando l'impostazione di "sola lettura" di cache host di Azure, come consigliato per i volumi di dati del software del database potrà migliorare le prestazioni dei / o. Il log delle transazioni, la cache del disco host di Azure deve essere "none". 

Il punto di partenza consigliato per le dimensioni del volume di log è un'euristica del 15% delle dimensioni dei dati. Per creare il volume di log, usare i tipi di dischi di Azure diversa a seconda dei requisiti di costi e la velocità effettiva. Per il volume di log, è necessaria ad alta velocità effettiva dei / o. 

Se si usa la macchina virtuale digitare M64 32ms, è consigliabile abilitare [acceleratore di scrittura](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator). Acceleratore di scrittura è una funzionalità di Azure che fornisce la latenza di scrittura su disco ottimali per il log delle transazioni. È disponibile solo per la serie M. Esistono alcuni aspetti da considerare, ad esempio il numero massimo di dischi per ogni tipo di macchina virtuale. Per ulteriori informazioni sull'acceleratore di scrittura, vedere [acceleratore di scrittura abilitare](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).


Nella tabella seguente mostra alcuni esempi che consentono di ridimensionare il volume di log.

| tipo dimensioni e il disco del volume dati | configurazione 1 tipo di volume di log e disco | configurazione di tipo per il volume e disco 2 di log |
| --- | --- | --- |
| 4 x P50 -> 16 TB | 5 x P20 -> 2,5 TB | 3 x P30 -> 3 TB |
| 6 x P15 -> 1,5 TB | 4 x P6 -> 256 GB | 1 x P15 -> 256 GB |


Analogamente a scale-out di SAP HANA, la directory /hana/shared deve essere condivisa tra la macchina virtuale di SAP HANA e la macchina virtuale 2.0 DT. È consigliabile usare la stessa architettura di SAP HANA scalabilità orizzontale usando macchine virtuali dedicate, che fungono da un server NFS a disponibilità elevata. Per garantire un volume di backup condiviso, utilizzare la stessa progettazione. Ma spetta all'utente può decidere se è necessaria la disponibilità elevata o se è sufficiente usare una macchina virtuale dedicata con capacità di archiviazione sufficiente per agire come un server di backup.



### <a name="links-to-dt-20-documentation"></a>Collegamenti alla documentazione di DT 2.0 

- [SAP HANA dinamica suddivisione in livelli guida all'installazione e aggiornamento](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [Risorse ed esercitazioni di suddivisione in livelli dinamiche di SAP HANA](https://help.sap.com/viewer/fb9c3779f9d1412b8de6dd0788fa167b/2.0.03/en-US)
- [SAP HANA dinamica suddivisione in livelli nel modello di prova](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
- [SAP HANA 2.0 SPS 02 dynamic tiering enhancements](https://blogs.sap.com/2017/07/31/sap-hana-2.0-sps-02-dynamic-tiering-enhancements/) (Miglioramenti di SAP HANA 2.0 SPS 02 Dynamic Tiering)




## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Operazioni per la distribuzione di SAP HANA in macchine virtuali di Azure
Le sezioni seguenti descrivono alcune delle operazioni correlate alla distribuzione di sistemi SAP HANA in macchine virtuali di Azure.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Operazioni di backup e ripristino nelle macchine virtuali di Azure
I documenti seguenti descrivono come eseguire il backup e il ripristino della distribuzione di SAP HANA:

- [Panoramica del backup di SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Backup di SAP HANA a livello di file](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Backup degli snapshot di archiviazione di SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>Avviare e riavviare le macchine virtuali che contengono SAP HANA
Una delle principali caratteristiche del cloud pubblico di Azure è che i costi vengono addebitati solo in base ai minuti di calcolo. Ad esempio, quando si arresta una macchina virtuale che esegue SAP HANA, vengono addebitati solo i costi di archiviazione durante tale periodo. Un'altra caratteristica si presenta quando si specificano gli indirizzi IP statici per le macchine virtuali nella distribuzione iniziale. Quando si riavvia una macchina virtuale con SAP HANA, la macchina viene riavviata con l'indirizzo IP precedente. 


### <a name="use-saprouter-for-sap-remote-support"></a>Usare SAProuter per il supporto remoto SAP
Se si dispone di una connessione site-to-site tra le sedi locali e Azure e si eseguono componenti SAP, si sta eseguendo probabilmente già SAProuter. In questo caso, seguire questi passaggi per il supporto remoto:

- Gestire l'indirizzo IP privato e statico della macchina virtuale che ospita SAP HANA nella configurazione di SAProuter.
- Configurare il gruppo di sicurezza di rete della subnet che ospita la macchina virtuale HANA per consentire il traffico attraverso la porta TCP/IP 3299.

Se connette ad Azure tramite internet e non si dispone di un router SAP per la macchina virtuale con SAP HANA, installare il componente. Installare SAProuter in una macchina virtuale separata nella subnet di gestione. 

L'immagine seguente mostra uno schema approssimativo per la distribuzione di SAP HANA senza connessione da sito a sito e con SAProuter:

![Schema di distribuzione approssimativo per SAP HANA senza connessione da sito a sito e con SAProuter](media/hana-vm-operations/hana-simple-networking3.PNG)

Assicurarsi di installare SAProuter in una macchina virtuale separata e non nella VM JumpBox. La macchina virtuale separata deve avere un indirizzo IP statico. Per connettere il SAProuter all'istanza di SAProuter ospitata da SAP, contattare SAP per un indirizzo IP. Istanza di SAProuter ospitata da SAP è la controparte dell'istanza di SAProuter installata nella macchina virtuale. Usare l'indirizzo IP fornito da SAP per configurare l'istanza di SAProuter. Nelle impostazioni di configurazione l'unica porta necessaria è la porta TCP 3299.

Per altre informazioni su come configurare e gestire le connessioni di supporto remoto tramite SAProuter, vedere [documentazione di SAP](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Disponibilità elevata con SAP HANA in macchine virtuali native di Azure
Se si esegue SUSE Linux Enterprise Server for SAP Applications 12 SP1 o versione successiva, è possibile creare un cluster Pacemaker con dispositivi STONITH. Usare i dispositivi per impostare una configurazione di SAP HANA che usi la replica sincrona con la replica di sistema HANA e failover automatico. Per altre informazioni sulla procedura di configurazione, vedere [Guida alla disponibilità elevata di SAP HANA per macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
