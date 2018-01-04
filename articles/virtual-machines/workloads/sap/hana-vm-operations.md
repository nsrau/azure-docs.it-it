---
title: Operazioni di SAP HANA in Azure | Documenti Microsoft
description: Guida per i sistemi SAP HANA che vengono distribuite macchine virtuali di Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: 
author: juergent
manager: patfilot
editor: 
tags: azure-resource-manager
keywords: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/17/2017
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1d6991d40b9bb8543898bbbdc9d7c905dfe11536
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2018
---
# <a name="sap-hana-on-azure-operations-guide"></a>Guida operativa a SAP HANA in Azure
Questo documento fornisce istruzioni per SAP HANA sistemi operativi che vengono distribuiti in Azure native macchine virtuali (VM). Questo documento non è destinato a sostituire la documentazione SAP standard, che include il contenuto seguente:

- [Guida all'amministrazione di SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [Guide all'installazione di SAP](https://service.sap.com/instguides)
- [Note su SAP](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>Prerequisiti
Per usare questa Guida, è necessario conoscenza di base dei componenti di Azure seguenti:

- [Macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Rete di Azure e le reti virtuali](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Archiviazione di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Per ulteriori informazioni su SAP NetWeaver e altri componenti SAP in Azure, vedere il [SAP in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) sezione la [documentazione di Azure](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Considerazioni di base sulla configurazione
Nelle sezioni seguenti vengono descritte considerazioni sulla configurazione di base per la distribuzione di sistemi SAP HANA in macchine virtuali di Azure.

### <a name="connect-into-azure-virtual-machines"></a>Collegare macchine virtuali di Azure
Come descritto nel [macchine virtuali di Azure Guida alla pianificazione](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide), sono disponibili due metodi di base per la connessione in macchine virtuali di Azure:

- Connettersi tramite internet e gli endpoint pubblici in una macchina virtuale passare o nella macchina virtuale che esegue SAP HANA.
- Connettersi tramite un [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) o Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Connettività da sito a sito tramite VPN o ExpressRoute è necessaria per scenari di produzione. Questo tipo di connessione è necessario anche per gli scenari non di produzione che feed in scenari di produzione in cui viene usato il software SAP. L'immagine seguente mostra un esempio di connettività cross-site:

![Connettività intersito](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Scegliere i tipi di macchine Virtuali di Azure
I tipi di macchina virtuale di Azure che possono essere utilizzati per gli scenari di produzione sono elencati nel [documentazione SAP per IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Per gli scenari non di produzione, è disponibile un'ampia gamma di tipi nativi di macchina virtuale di Azure.

>[!NOTE]
>Per gli scenari non di produzione, utilizzare i tipi VM elencati nel [nota SAP &#1928533;](https://launchpad.support.sap.com/#/notes/1928533).

Distribuire le macchine virtuali in Azure utilizzando:

- Il portale di Azure.
- Cmdlet di Azure PowerShell.
- L'interfaccia CLI di Azure.

È anche possibile distribuire una piattaforma completa di SAP HANA installata in servizi della macchina virtuale di Azure tramite il [piattaforma Cloud SAP](https://cal.sap.com/). Viene descritto il processo di installazione [BW/4HANA in Azure o distribuire SAP S/4HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="choose-azure-storage-type"></a>Scegliere il tipo di archiviazione di Azure
Azure offre due tipi di archiviazione che sono adatti per le macchine virtuali di Azure che eseguono SAP HANA:

- [Archiviazione Standard di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Archiviazione Premium di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

Azure offre due metodi di distribuzione per VHD su Archiviazione Standard e Premium di Azure. Consente lo scenario complessivo, sfruttare i vantaggi di [disco gestito di Azure](https://azure.microsoft.com/services/managed-disks/) le distribuzioni.

Per un elenco di tipi di archiviazione e i relativi contratti di servizio, esaminare il [documentazione di Azure per i dischi gestiti](https://azure.microsoft.com/pricing/details/managed-disks/).

I dischi Premium di Azure è consigliato per i volumi /hana/data e /hana/log. È possibile compilare un RAID LVM su più dischi di archiviazione Premium e utilizzare i volumi RAID come /hana/data e /hana/log.

Nella tabella seguente viene illustrata una configurazione di tipi di macchine Virtuali che utilizzano in genere i clienti a host SAP HANA in macchine virtuali di Azure:

| SKU di VM | RAM | Max. / O DI MACCHINA VIRTUALE<br /> Velocità effettiva | /hana/data and /hana/log<br /> con striping con LVM o MDADM | /hana/shared | volume /root | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| E16v3 | 128 GiB | 384 MB | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256 giB | 768 MB | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443 giB | 1200 GB | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 giB | 2000 GB | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M64s | 1000 giB | 1000 GB | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1750 giB | 1000 GB | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | GiB 2000 | 2000 GB |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3800 giB | 2000 GB | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


> [!NOTE]
> I dischi consigliati per i tipi di macchina virtuale più piccola con dimensione x P20 3 volumi riguardanti le indicazioni di spazio in base al [white paper di archiviazione di SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). Tuttavia, la scelta come visualizzato nella tabella è stata effettuata per fornire una velocità effettiva del disco sufficienti per SAP HANA. Se è necessario minore velocità effettiva dei / o, è possibile modificare la scelta di dischi di archiviazione Premium per /hana/data e /hana/log. Stesso vale per il ridimensionamento del volume /hana/backup, che è stato ridimensionato per mantenere i backup che rappresentano due volte il volume di memoria. Se è necessario meno spazio, è possibile modificare. Tenere inoltre la velocità effettiva dei / o VM globale presente durante il ridimensionamento o decidere per una macchina virtuale. Complessivo della velocità effettiva di macchina virtuale è documentata nell'articolo [dimensioni delle macchine virtuali di ottimizzazione per la memoria](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)  

> [!NOTE]
> Se si desidera trarre vantaggio da [macchina virtuale di Azure singolo contratto di servizio VM](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) è necessario modificare tutti i dischi rigidi virtuali che sono elencati come archiviazione Standard (Sxx) in archiviazione Premium (Pxx). 


### <a name="set-up-azure-virtual-networks"></a>Impostare le reti virtuali di Azure
Quando si dispone di connettività site-to-site in Azure tramite VPN o ExpressRoute, è necessario avere almeno un [rete virtuale di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) collegato tramite un Gateway virtuale al circuito ExpressRoute o VPN. Il Gateway virtuale si trova in una subnet nella rete virtuale di Azure. Per installare SAP HANA, si crea due altre subnet all'interno della rete virtuale. Una subnet ospita le macchine virtuali per eseguire le istanze di SAP HANA. Le altre subnet esegue Jumpbox o macchine virtuali di gestione per ospitare SAP HANA Studio o altri software di gestione.

Quando si installano le macchine virtuali per l'esecuzione di SAP HANA, le macchine virtuali devono:

- Due schede di rete virtuale installati: una scheda di rete per la connessione alla subnet di gestione e una scheda di rete per la connessione dalla rete locale o altre reti, l'istanza di SAP HANA nella macchina virtuale di Azure.
- Privato indirizzi IP statici che vengono distribuiti per entrambe le schede di rete virtuale.

Per una panoramica dei diversi metodi per l'assegnazione di indirizzi IP, vedere [l'indirizzo IP di tipi e i metodi di allocazione in Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

[Azure rete sicurezza gruppi](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) vengono utilizzati per indirizzare il traffico indirizzato per l'istanza di SAP HANA o il Jumpbox. Il NSGs vengono associate a subnet SAP HANA e la subnet di gestione.

La figura seguente mostra una panoramica di uno schema di distribuzione approssimativa per SAP HANA:

![Schema di distribuzione approssimativo per SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)


Per distribuire SAP HANA in Azure senza una connessione site-to-site, accedere all'istanza SAP HANA, anche se un indirizzo IP pubblico. L'indirizzo IP deve essere assegnato alla macchina virtuale di Azure che esegue la VM Jumpbox. In questo scenario di base, la distribuzione si basa su Azure servizi DNS predefiniti per risolvere i nomi host. In una distribuzione più complessa in cui vengono utilizzati indirizzi IP pubblici, servizi di Azure per DNS predefiniti sono particolarmente importanti. Utilizzare Azure NSGs per limitare le porte aperte e intervalli di indirizzi IP che è possano collegare le subnet di Azure con le risorse che dispongono degli indirizzi IP pubblici. Di seguito viene illustrato uno schema approssimativo per la distribuzione di SAP HANA senza una connessione site-to-site:
  
![Schema di distribuzione approssimativa per SAP HANA senza una connessione site-to-site](media/hana-vm-operations/hana-simple-networking2.PNG)
 


## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Operazioni per la distribuzione di SAP HANA in macchine virtuali di Azure
Nelle sezioni seguenti vengono descritte alcune delle operazioni correlate alla distribuzione di sistemi SAP HANA in macchine virtuali di Azure.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Eseguire il backup e ripristinare le operazioni nelle macchine virtuali di Azure
I documenti seguenti descrivono come eseguire il backup e ripristinare la distribuzione di SAP HANA:

- [Panoramica del backup di SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Backup di file a livello di SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Benchmark di SAP HANA archiviazione dello snapshot](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>Avviare e riavviare le macchine virtuali che contengono SAP HANA
Una funzionalità principali di cloud pubblico di Azure è che vengono addebitati solo i minuti di elaborazione. Ad esempio, quando si arresta una macchina virtuale che esegue SAP HANA, sta fatturato solo per i costi di archiviazione durante tale periodo. Quando si specificano gli indirizzi IP statici per le macchine virtuali nella distribuzione iniziale, è disponibile un'altra funzionalità. Quando si riavvia una macchina virtuale con SAP HANA, viene riavviata la macchina virtuale con indirizzo IP precedente. 


### <a name="use-saprouter-for-sap-remote-support"></a>Utilizzare SAProuter per il supporto remoto SAP
Se si dispone di una connessione da sito a sito tra i percorsi locali e Azure, e si eseguono i componenti SAP, quindi SAProuter probabilmente già in esecuzione. In questo caso, completare i seguenti elementi per supporto remoto:

- Gestire l'indirizzo IP statico e privato della macchina virtuale che ospita SAP HANA nella configurazione SAProuter.
- Configurare il gruppo della subnet che ospita la VM HANA per consentire il traffico attraverso la porta TCP/IP 3299.

Se ci si connette a Azure tramite internet e non si dispone di un router SAP per la macchina virtuale con SAP HANA, è necessario installare il componente. Installare SAProuter in una macchina virtuale separata della subnet di gestione. Di seguito viene illustrato uno schema approssimativo per la distribuzione di SAP HANA senza una connessione site-to-site e con SAProuter:

![Sgrossare dello schema di distribuzione per SAP HANA senza una connessione site-to-site e SAProuter](media/hana-vm-operations/hana-simple-networking3.PNG)

Assicurarsi di installare SAProuter in una macchina virtuale separata e non in VM Jumpbox. La macchina virtuale separata deve avere un indirizzo IP statico. Per connettere il SAProuter SAProuter ospitato da SAP, contattare SAP per un indirizzo IP. (SAProuter ospitato da SAP è l'equivalente dell'istanza SAProuter installato nella macchina virtuale). Usare l'indirizzo IP da SAP per configurare l'istanza SAProuter. Nelle impostazioni di configurazione, la porta solo necessaria è la porta TCP 3299.

Per ulteriori informazioni su come impostare e gestire le connessioni di assistenza remota tramite SAProuter, vedere il [documentazione SAP](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Disponibilità elevata con SAP HANA in macchine virtuali di Azure native
Se si esegue SUSE Linux 12 SP1 o versione successiva, è possibile stabilire un cluster Pacemaker con dispositivi STONITH. È possibile utilizzare i dispositivi per impostare una configurazione di SAP HANA che utilizza la replica sincrona con replica DFS HANA e il failover automatico. Per ulteriori informazioni sulla procedura di installazione, vedere [la disponibilità elevata di SAP HANA su macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability).
