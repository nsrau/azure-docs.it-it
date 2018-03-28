---
title: Operazioni di SAP HANA in Azure | Microsoft Docs
description: Guida operativa per i sistemi SAP HANA distribuiti in macchine virtuali di Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: juergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/13/2017
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0cb715960a516c6b2ca16376c12cb6f796e0b395
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="sap-hana-on-azure-operations-guide"></a>Guida operativa a SAP HANA in Azure
Questa guida contiene le indicazioni necessarie per gestire i sistemi SAP HANA distribuiti in macchine virtuali native di Azure. Questo documento non deve sostituire la documentazione standard di SAP, che include:

- [Guida all'amministrazione di SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [Guide all'installazione di SAP](https://service.sap.com/instguides)
- [Note di SAP](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>prerequisiti
Per usare questa guida sono necessarie conoscenze di base dei componenti di Azure seguenti:

- [Macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Rete e reti virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Archiviazione di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Per altre informazioni su SAP NetWeaver e altri componenti SAP in Azure, vedere la sezione [SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) della [documentazione di Azure](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Considerazioni di base sulla configurazione
Le sezioni seguenti offrono considerazioni di base sulla configurazione per la distribuzione di sistemi SAP HANA in macchine virtuali di Azure.

### <a name="connect-into-azure-virtual-machines"></a>Connettersi nelle macchine virtuali di Azure
Come illustrato nella [Guida alla pianificazione di macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide), sono disponibili due metodi di base per la connessione nelle macchine virtuali di Azure:

- Connessione tramite Internet ed endpoint pubblici in una macchina virtuale di collegamento o in una macchina virtuale che esegue SAP HANA.
- Connessione tramite una rete [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) o Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Per gli scenari di produzione è necessaria la connettività da sito a sito tramite VPN o ExpressRoute. Questo tipo di connessione è necessario anche per gli scenari non di produzione che si inseriscono negli scenari di produzione in cui viene usato il software SAP. La figura seguente mostra un esempio di connettività intersito:

![Connettività intersito](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Scegliere i tipi di macchine virtuali di Azure
I tipi di macchine virtuali di Azure che possono essere usati per gli scenari di produzione sono elencati nella [documentazione SAP per IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Per gli scenari non di produzione è disponibile una gamma più ampia di macchine virtuali native di Azure.

>[!NOTE]
> Per gli scenari non di produzione, usare i tipi di macchine virtuali elencati nella [nota di SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Per l'utilizzo di macchine virtuali di Azure per gli scenari di produzione, scegliere macchine virtuali con certificazione SAP HANA nell'[elenco di piattaforme IaaS certificate](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html) pubblicato da SAP.

Per distribuire le macchine virtuali in Azure, usare:

- Il portale di Azure.
- I cmdlet di Azure PowerShell.
- L'interfaccia della riga di comando di Azure.

È anche possibile distribuire una piattaforma SAP HANA installata completa nei servizi di macchine virtuali di Azure tramite la [piattaforma SAP Cloud](https://cal.sap.com/). Per una descrizione del processo di installazione, vedere [Distribuire SAP S/4HANA o BW/4HANA in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h) oppure usare lo script di automazione rilasciato [qui](https://github.com/AzureCAT-GSI/SAP-HANA-ARM).

### <a name="choose-azure-storage-type"></a>Scegliere un tipo di archiviazione di Azure
Azure offre due tipi di archiviazione adatti per le macchine virtuali di Azure che eseguono SAP HANA:

- [Archiviazione Standard di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Archiviazione Premium di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

Azure offre due metodi di distribuzione per VHD su Archiviazione Standard e Premium di Azure. Se lo scenario lo permette, sfruttare i vantaggi offerti dalle distribuzioni di [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/).

Per un elenco dei tipi di archiviazione e dei relativi contratti di servizio per operazioni di I/O al secondo e velocità effettiva di archiviazione, vedere la [documentazione di Azure per Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="configuring-the-storage-for-azure-virtual-machines"></a>Configurazione dell'archiviazione per macchine virtuali di Azure

Finora, per l'acquisto di appliance SAP HANA per implementazioni in locale, non era necessario preoccuparsi dei sottosistemi di I/O o delle relative capacità, perché il fornitore dell'appliance deve assicurarsi che siano soddisfatti i requisiti di archiviazione minima per SAP HANA. Se si configura l'infrastruttura di Azure in autonomia, è necessario conoscere alcuni di questi requisiti anche per comprendere i requisiti di configurazione consigliati nelle sezioni seguenti. Lo stesso vale per la configurazione delle macchine virtuali su cui si vuole eseguire SAP HANA. Alcune delle caratteristiche richieste derivano dalla necessità di:

- Abilitare il volume di lettura/scrittura in /hana/log per minimo 250 MB/sec con dimensioni di I/O di 1 MB
- Abilitare l'attività di lettura per minimo 400 MB/sec per /hana/data con dimensioni di I/O di 16 MB e 64 MB
- Abilitare l'attività di scrittura per minimo 250 MB/sec per /hana/data con dimensioni di I/O di 16 MB e 64 MB

Detto questo, una bassa latenza di archiviazione è fondamentale per i sistemi DBMS, anche perché questi sistemi, come SAP HANA, mantengono dati in memoria. Il percorso critico per l'archiviazione riguarda in genere le scritture nel log delle transazioni dei sistemi DBMS. Ma possono essere critiche anche operazioni come la scrittura di punti di salvataggio o il caricamento di dati in memoria dopo il ripristino da un arresto anomalo del sistema. È quindi obbligatorio usare Dischi Premium di Azure per i volumi /hana/data e /hana/log. Per ottenere la velocità effettiva minima di /hana/log e /hana/data come richiesto da SAP, è necessario configurare un sistema RAID 0 tramite MDADM o LVM su più dischi di Archiviazione Premium di Azure e usare i volumi RAID come volumi /hana/data e /hana/log. Per le dimensioni di striping di RAID 0 è raccomandato l'uso di:

- 64 KB o 128 KB per /hana/data
- 32 KB per /hana/log

> [!NOTE]
> Non è necessario configurare alcun livello di ridondanza con i volumi RAID perché l'Archiviazione Standard e Premium di Azure mantiene tre immagini di un disco rigido virtuale. L'utilizzo di un volume RAID è richiesto esclusivamente per configurare volumi in grado di offrire una velocità effettiva di I/O sufficiente.

Accumulare un certo numero di dischi rigidi virtuali di Azure in una configurazione RAID consente di ottenere valori cumulativi per le operazioni di I/O al secondo e la velocità effettiva di archiviazione. Pertanto, se si configura un sistema RAID 0 su 3 dischi di Archiviazione Premium di Azure P30, si dovrebbero ottenere livelli triplicati di operazioni di I/O al secondo e velocità effettiva di archiviazione rispetto a un singolo disco di Archiviazione Premium di Azure P30.

Non configurare la memorizzazione nella cache dell'Archiviazione Premium sui dischi usati per /hana/data e /hana/log. In tutti i dischi basati su questi volumi la memorizzazione nella cache deve essere impostata su 'Nessuna'.

Durante il ridimensionamento o quando si sceglie una macchina virtuale, tenere presente anche la velocità effettiva di I/O complessiva delle macchine virtuali. Per informazioni sulla velocità effettiva di archiviazione complessiva delle macchine virtuali, vedere l'articolo [Dimensioni delle macchine virtuali ottimizzate per la memoria](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

#### <a name="cost-conscious-azure-storage-configuration"></a>Configurazione di Archiviazione di Azure tenendo conto dei costi
La tabella seguente mostra una configurazione di tipi di macchine virtuali comunemente usati dai clienti per ospitare SAP HANA in macchine virtuali di Azure. Potrebbero essere presenti alcuni tipi di macchine virtuali che non soddisfano tutti i criteri minimi per SAP HANA, anche se offrono prestazioni adeguate per scenari non di produzione. 

> [!NOTE]
> Per gli scenari di produzione, controllare se un determinato tipo di macchina virtuale è supportato da SAP per SAP HANA nella [documentazione SAP per IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).



| SKU di VM | RAM | Max. velocità effettiva<br /> Velocità effettiva | /hana/data and /hana/log<br /> con striping con LVM o MDADM | /hana/shared | volume /root | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 128 GiB | 768 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443 GiB | 1200 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GiB | 2000 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M64s | 1000 GiB | 1000 MB/s | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


I dischi consigliati per i tipi di macchine virtuali più piccoli con 3 x P20 superano in dimensioni i volumi riguardanti le indicazioni di spazio contenute nel [white paper sull'archiviazione di SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Tuttavia, la scelta indicata nella tabella è stata effettuata per offrire una velocità effettiva del disco sufficiente per SAP HANA. Se è necessario apportare modifiche al volume /hana/backup, che è stato ridimensionato per mantenere backup che rappresentano il doppio del volume di memoria, procedere liberamente.   
Controllare se la velocità effettiva di archiviazione per i diversi volumi suggeriti potrà soddisfare i requisiti del carico di lavoro che si vuole eseguire. Se il carico di lavoro richiede volumi più elevati per /hana/data e /hana/log, è necessario aumentare il numero di dischi rigidi virtuali di Archiviazione Premium di Azure. Il ridimensionamento di un volume con più dischi rigidi virtuali di quelli elencati consentirà di aumentare le operazioni di I/O al secondo e la velocità effettiva di I/O entro i limiti del tipo di macchina virtuale di Azure. 

> [!NOTE]
> Le configurazioni sopra indicate NON traggono vantaggio dal [contratto di servizio per macchine virtuali con istanza singola di macchina virtuale di Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) perché usa in combinazione Archiviazione Premium di Azure e Archiviazione Standard di Azure. Tuttavia, la selezione è stata scelta per ottimizzare i costi.


#### <a name="azure-storage-configuration-to-benefit-for-meeting-single-vm-sla"></a>Configurazione di Archiviazione di Azure per soddisfare il contratto di servizio per una singola macchina virtuale
Per trarre vantaggio dal [contratto di servizio per macchine virtuali con istanza singola di macchina virtuale di Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/), è necessario usare esclusivamente dischi rigidi virtuali di Archiviazione Premium di Azure.

> [!NOTE]
> Per gli scenari di produzione, controllare se un determinato tipo di macchina virtuale è supportato da SAP per SAP HANA nella [documentazione SAP per IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

| SKU di VM | RAM | Max. velocità effettiva<br /> Velocità effettiva | /hana/data and /hana/log<br /> con striping con LVM o MDADM | /hana/shared | volume /root | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 128 GiB | 768 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P20 |
| E64v3 | 443 GiB | 1200 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| GS5 | 448 GiB | 2000 MB/s | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| M64s | 1000 GiB | 1000 MB/s | 2 x P30 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |


I dischi consigliati per i tipi di macchine virtuali più piccoli con 3 x P20 superano in dimensioni i volumi riguardanti le indicazioni di spazio contenute nel [white paper sull'archiviazione di SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Tuttavia, la scelta indicata nella tabella è stata effettuata per offrire una velocità effettiva del disco sufficiente per SAP HANA. Se è necessario apportare modifiche al volume /hana/backup, che è stato ridimensionato per mantenere backup che rappresentano il doppio del volume di memoria, procedere liberamente.  
Controllare se la velocità effettiva di archiviazione per i diversi volumi suggeriti potrà soddisfare i requisiti del carico di lavoro che si vuole eseguire. Se il carico di lavoro richiede volumi più elevati per /hana/data e /hana/log, è necessario aumentare il numero di dischi rigidi virtuali di Archiviazione Premium di Azure. Il ridimensionamento di un volume con più dischi rigidi virtuali di quelli elencati consentirà di aumentare le operazioni di I/O al secondo e la velocità effettiva di I/O entro i limiti del tipo di macchina virtuale di Azure. 



#### <a name="storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Soluzione di archiviazione con l'acceleratore di scrittura di Azure per macchine virtuali di Azure della serie M
L'acceleratore di scrittura di Azure è una funzionalità implementata esclusivamente per le macchine virtuali della serie M. Come indicato dal nome, lo scopo della funzionalità è migliorare la latenza di I/O delle scritture per l'Archiviazione Premium di Azure. Per SAP HANA, l'uso dell'acceleratore di scrittura è previsto solo sul volume /hana/log. Le configurazioni illustrate finora devono quindi essere modificate. La modifica principale è la suddivisione tra /hana/data e /hana/log per poter usare l'acceleratore di scrittura di Azure solo sul volume /hana/log. 

> [!IMPORTANT]
> La certificazione SAP HANA per le macchine virtuali di Azure della serie M è valida esclusivamente con l'acceleratore di scrittura di Azure per il volume /hana/log. Di conseguenza, per le distribuzioni di SAP HANA in scenari di produzione nelle macchine virtuali di Azure della serie M è prevista la configurazione con l'acceleratore di scrittura di Azure per il volume /hana/log.  

> [!NOTE]
> Per gli scenari di produzione, controllare se un determinato tipo di macchina virtuale è supportato da SAP per SAP HANA nella [documentazione SAP per IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

Le configurazioni consigliate sono simili alle seguenti:

| SKU di VM | RAM | Max. velocità effettiva<br /> Velocità effettiva | /hana/data | /hana/log | /hana/shared | volume /root | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M64s | 1000 GiB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |

Controllare se la velocità effettiva di archiviazione per i diversi volumi suggeriti potrà soddisfare i requisiti del carico di lavoro che si vuole eseguire. Se il carico di lavoro richiede volumi più elevati per /hana/data e /hana/log, è necessario aumentare il numero di dischi rigidi virtuali di Archiviazione Premium di Azure. Il ridimensionamento di un volume con più dischi rigidi virtuali di quelli elencati consentirà di aumentare le operazioni di I/O al secondo e la velocità effettiva di I/O entro i limiti del tipo di macchina virtuale di Azure.

L'acceleratore di scrittura di Azure funziona solo in combinazione con [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Pertanto, almeno i dischi di Archiviazione Premium di Azure che costituiscono il volume /hana/log devono essere distribuiti come dischi gestiti.

Esistono limiti per il numero di dischi rigidi virtuali di Archiviazione Premium di Azure per ogni macchina virtuale supportati dall'acceleratore di scrittura di Azure. I limiti correnti sono:

- 16 dischi rigidi virtuali per una macchina virtuale M128xx
- 8 dischi rigidi virtuali per una macchina virtuale M64xx

Istruzioni più dettagliate su come abilitare l'acceleratore di scrittura di Azure sono disponibili nell'articolo [Acceleratore di scrittura di Azure per distribuzioni SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/how-to-enable-write-accelerator).

La stessa documentazione include anche informazioni dettagliate e sulle restrizioni per l'acceleratore di scrittura di Azure.


### <a name="set-up-azure-virtual-networks"></a>Configurare le reti virtuali di Azure
Dopo aver stabilito la connettività da sito a sito in Azure tramite VPN o ExpressRoute, è necessario avere almeno una [rete virtuale di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) che sia connessa tramite un gateway virtuale al circuito VPN o ExpressRoute. Il gateway virtuale si trova in una subnet della rete virtuale di Azure. Per installare SAP HANA, si creano altre due subnet all'interno della rete virtuale. Una subnet ospita le macchine virtuali necessarie per eseguire le istanze di SAP HANA, mentre l'altra esegue macchine virtuali Jumpbox o di gestione per ospitare SAP HANA Studio o altro software di gestione.

Quando si installano le macchine virtuali per l'esecuzione di SAP HANA, queste macchine virtuali devono essere dotate di:

- Due schede di interfaccia di rete virtuali installate: una si connette alla subnet di gestione e l'altra viene usata per la connessione dalla rete locale o da altre reti all'istanza di SAP HANA nella macchina virtuale di Azure.
- Indirizzi IP privati statici distribuiti per entrambe le schede di interfaccia di rete virtuali.

Per una panoramica dei diversi metodi per l'assegnazione di indirizzi IP, vedere [Tipi di indirizzi IP e metodi di allocazione in Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

Per le macchine virtuali che eseguono SAP HANA, è consigliabile usare indirizzi IP statici assegnati, perché alcuni attributi di configurazione per HANA fanno riferimento a indirizzi IP.

I [gruppi di sicurezza di rete di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) vengono usati per indirizzare il traffico instradato all'istanza di SAP HANA o a Jumpbox. I gruppi di sicurezza di rete sono associati alla subnet di SAP HANA e alla subnet di gestione.

La figura seguente mostra una panoramica di uno schema di distribuzione approssimativo per SAP HANA:

![Schema di distribuzione approssimativo per SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)


Per distribuire SAP HANA in Azure senza una connessione da sito a sito, accedere all'istanza di SAP HANA tramite un indirizzo IP pubblico. L'indirizzo IP deve essere assegnato alla macchina virtuale di Azure che esegue la macchina virtuale Jumpbox. In questo scenario di base la distribuzione si basa su servizi DNS predefiniti di Azure per risolvere i nomi host. Questi servizi sono particolarmente importanti in distribuzioni più complesse in cui vengono usati indirizzi IP pubblici. Usare i gruppi di sicurezza di rete di Azure per limitare le porte aperte o gli intervalli di indirizzi IP che possono connettersi nelle subnet di Azure con asset che hanno indirizzi IP pubblici. L'immagine seguente mostra uno schema approssimativo per la distribuzione di SAP HANA senza connessione da sito a sito:
  
![Schema di distribuzione approssimativo per SAP HANA senza connessione da sito a sito](media/hana-vm-operations/hana-simple-networking2.PNG)
 


## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Operazioni per la distribuzione di SAP HANA in macchine virtuali di Azure
Le sezioni seguenti descrivono alcune delle operazioni correlate alla distribuzione di sistemi SAP HANA in macchine virtuali di Azure.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Operazioni di backup e ripristino nelle macchine virtuali di Azure
I documenti seguenti descrivono come eseguire il backup e il ripristino della distribuzione di SAP HANA:

- [Panoramica del backup di SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Backup di SAP HANA a livello di file](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Backup degli snapshot di archiviazione di SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>Avviare e riavviare le macchine virtuali che contengono SAP HANA
Una delle principali caratteristiche del cloud pubblico di Azure è che i costi vengono addebitati solo in base ai minuti di calcolo. Ad esempio, quando si arresta una macchina virtuale che esegue SAP HANA, durante l'intervallo di calcolo vengono fatturati solo i costi di archiviazione. Un'altra caratteristica si presenta quando si specificano gli indirizzi IP statici per le macchine virtuali nella distribuzione iniziale. Quando si riavvia una macchina virtuale con SAP HANA, la macchina viene riavviata con l'indirizzo IP precedente. 


### <a name="use-saprouter-for-sap-remote-support"></a>Usare SAProuter per il supporto remoto SAP
Se esiste una connessione da sito a sito tra le sedi locali e Azure e si eseguono componenti SAP, è probabile che SAProuter sia già in esecuzione. In questo caso, completare le attività seguenti per il supporto remoto:

- Gestire l'indirizzo IP privato e statico della macchina virtuale che ospita SAP HANA nella configurazione di SAProuter.
- Configurare il gruppo di sicurezza di rete della subnet che ospita la macchina virtuale HANA per consentire il traffico attraverso la porta TCP/IP 3299.

Se ci si connette ad Azure tramite Internet senza avere a disposizione un router SAP per la macchina virtuale con SAP HANA, è necessario installare il componente. Installare SAProuter in una macchina virtuale separata nella subnet di gestione. L'immagine seguente mostra uno schema approssimativo per la distribuzione di SAP HANA senza connessione da sito a sito e con SAProuter:

![Schema di distribuzione approssimativo per SAP HANA senza connessione da sito a sito e con SAProuter](media/hana-vm-operations/hana-simple-networking3.PNG)

Installare SAProuter in una macchina virtuale separata e non nella macchina virtuale Jumpbox. La macchina virtuale separata deve avere un indirizzo IP statico. Per connettere la propria istanza di SAProuter all'istanza di SAProuter ospitata da SAP, contattare SAP per chiedere un indirizzo IP. L'istanza di SAProuter ospitata da SAP è la controparte dell'istanza di SAProuter installata nella macchina virtuale. Usare l'indirizzo IP fornito da SAP per configurare l'istanza di SAProuter. Nelle impostazioni di configurazione l'unica porta necessaria è la porta TCP 3299.

Per altre informazioni su come configurare e gestire le connessioni di supporto remoto tramite SAProuter, vedere la [documentazione di SAP](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Disponibilità elevata con SAP HANA in macchine virtuali native di Azure
Se si esegue SUSE Linux 12 SP1 o versione successiva, è possibile creare un cluster Pacemaker con dispositivi STONITH. È possibile usare i dispositivi per impostare una configurazione di SAP HANA che usi la replica sincrona con la replica di sistema HANA e il failover automatico. Per altre informazioni sulla procedura di configurazione, vedere [Guida alla disponibilità elevata di SAP HANA per macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
