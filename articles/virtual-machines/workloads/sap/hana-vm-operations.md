---
title: Operazioni di SAP HANA in Azure | Microsoft Docs
description: Operazioni di SAP HANA in macchine virtuali native di Azure
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
ms.openlocfilehash: 0328bdc40429e1e82a76f290f5bde39089db0a9d
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/29/2017
---
# <a name="sap-hana-on-azure-operations-guide"></a>Guida operativa a SAP HANA in Azure
Questa guida contiene le indicazioni per eseguire sistemi SAP HANA distribuiti in macchine virtuali di Azure. Questo documento non deve sostituire la documentazione standard di SAP. Le guide e le note SAP sono disponibili agli indirizzi seguenti:

- [SAP Administration Guide](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/330e5550b09d4f0f8b6cceb14a64cd22.html) (Guida all'amministrazione di SAP)
- [SAP Installation Guides](https://service.sap.com/instguides) (Guide all'installazione di SAP)
- [SAP Notes](https://sservice.sap.com/notes) (Note SAP)

Come prerequisito, è necessaria una conoscenza di base dei diversi componenti di Azure seguenti:

- [Macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Rete e reti virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Archiviazione di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

La documentazione aggiuntiva su SAP NetWeaver e altri componenti SAP in Azure è disponibile nella sezione [SAP in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) della [documentazione di Azure](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Considerazioni di base sulla configurazione
### <a name="connecting-into-azure"></a>Connessione in Azure
Come descritto in [Guida alla pianificazione e all'implementazione di macchine virtuali di Azure per SAP NetWeaver][planning-guide], esistono due metodi di base per connettersi in Macchine virtuali di Azure. 

- Connessione tramite Internet ed endpoint pubblici in una macchina virtuale jumpbox o esecuzione di SAP HANA sulla macchina virtuale
- Connessione tramite una [rete VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) o Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/)

Per gli scenari di produzione o per gli scenari con ambienti non di produzione integrati in scenari di produzione insieme a software SAP, è necessaria connettività da sito a sito tramite una rete VPN o ExpressRoute, come mostrato in questa immagine:

![Connettività intersito](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choice-of-azure-vm-types"></a>Scelta dei tipi di macchina virtuale di Azure
I tipi di macchina virtuale di Azure che è possibile usare per scenari di produzione possono essere esaminati [qui](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Per gli scenari non di produzione, è possibile scegliere tra una gamma più ampia di macchine virtuali native di Azure. Tuttavia, è consigliabile limitarsi ai tipi di macchina virtuale indicati nella [nota SAP n. 1928533](https://launchpad.support.sap.com/#/notes/1928533). La distribuzione di queste macchine virtuali in Azure può essere eseguita tramite:

- Portale di Azure
- Cmdlet di Azure PowerShell
- Interfaccia della riga di comando di Azure

È anche possibile distribuire una piattaforma SAP HANA installata completa nei servizi di macchine virtuali di Azure tramite la [piattaforma SAP Cloud](https://cal.sap.com/), come descritto [qui](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="choice-of-azure-storage"></a>Scelta dell'archiviazione di Azure
Azure offre due tipi principali di archiviazione adatti per le macchine virtuali di Azure che eseguono SAP HANA

- [Archiviazione Standard di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Archiviazione Premium di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

Azure offre due metodi di distribuzione per VHD su Archiviazione Standard e Premium di Azure. Se lo scenario complessivo lo permette, è consigliabile sfruttare distribuzioni di [Dischi gestiti di Azure](https://azure.microsoft.com/services/managed-disks/).

Per informazioni precise sui tipi di archiviazione e sui contratti di servizio per ogni tipo di archiviazione, vedere [questa documentazione](https://azure.microsoft.com/pricing/details/managed-disks/)

È consigliabile usare Dischi Premium di Azure per volumi /hana/data e /hana/log. Sono supportati la compilazione di un volume LVM RAID in più dischi di Archiviazione Premium e l'uso di questi volumi RAID come volumi /hana/data e /hana/log.

Una possibile configurazione per diversi tipi di macchina virtuale comuni usati fino a ora dai clienti per l'hosting di SAP HANA su macchine virtuali di Azure può includere gli elementi seguenti:

| SKU di VM | RAM | /hana/data and /hana/log<br /> con striping con LVM o MDADM | /hana/shared | volume /root | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | -- |
| E16v3 | 128 GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S10 |
| E32v3 | 256 GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443 GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M64s | 1 TB | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1,7 TB | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2 TB | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128ms | 3,8 TB | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 5 x S30 |


### <a name="azure-networking"></a>Rete di Azure
Presupponendo connettività da sito a sito tramite VPN o ExpressRoute in Azure, sarà necessaria almeno una [rete virtuale di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) connessa tramite un gateway virtuale alla VPN o al circuito ExpressRoute. Il gateway virtuale si trova in una subnet nella rete virtuale di Azure. Per installare HANA, è necessario creare altre due subnet nella rete virtuale. Una subnet ospiterà le macchine virtuali che eseguono le istanze di SAP HANA, l'altra subnet eseguirà le eventuali macchine virtuali jumpbox o di gestione che possono ospitare SAP HANA Studio o altro software di gestione.
Quando si installano le macchine virtuali che devono eseguire HANA, queste devono avere:

- Due schede di interfaccia di rete virtuali installate, di cui una si connette alla subnet di gestione e l'altra viene usata per la connessione dall'istanza locale o da altre reti all'istanza di SAP HANA nella macchina virtuale di Azure.
- Indirizzi IP privati statici distribuiti per entrambe le schede di interfaccia di rete virtuali

Una panoramica delle diverse possibilità di assegnazione degli indirizzi IP è disponibile [qui](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

Il routing del traffico direttamente all'istanza di SAP HANA o al jumpbox viene gestito da [gruppi di sicurezza di rete di Azure](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg) associati alla subnet HANA e alla subnet di gestione.

Nel complesso, lo schema di distribuzione approssimativo sarà simile al seguente:

![Schema di distribuzione approssimativo per SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)


Se è stato distribuito SAP HANA in Azure senza connettività da sito a sito (VPN o ExpressRoute in Azure), l'accesso all'istanza di SAP HANA viene eseguito tramite un indirizzo IP pubblico assegnato alla macchina virtuale di Azure che esegue la macchina virtuale jumpbox. Nel caso più semplice, è possibile usare anche i servizi DNS integrati di Azure per la risoluzione dei nomi host. In particolare quando si usano indirizzi IP pubblici, è consigliabile usare gruppi di sicurezza di rete di Azure per limitare le porte aperte o gli intervalli di indirizzi IP consentiti per la connessione nelle subnet di Azure che eseguono asset con indirizzi IP pubblici. Lo schema di una distribuzione di questo tipo può essere simile al seguente:
  
![Schema di distribuzione approssimativo per SAP HANA senza connessione da sito a sito](media/hana-vm-operations/hana-simple-networking2.PNG)
 


## <a name="operations"></a>Operazioni
### <a name="backup-and-restore-operations-on-azure-vms"></a>Operazioni di backup e ripristino nelle macchine virtuali di Azure
Le opzioni disponibili per il backup e il ripristino di SAP HANA sono descritte in questi documenti:

- [Panoramica del backup di SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Backup a livello di file di SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Backup degli snapshot di archiviazione di SAP HANA](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)



### <a name="start-and-restart-of-vms-containing-sap-hana"></a>Avviare e riavviare le macchine virtuali che contengono SAP HANA
Uno dei punti di forza del cloud pubblico di Azure è il fatto che vengono addebitati solo i minuti di calcolo effettivamente spesi. Questo significa che se si arresta una macchina virtuale che esegue SAP HANA, durante questo intervallo di tempo verranno fatturati solo i costi per l'archiviazione. Quando si avvia di nuovo la macchina virtuale con SAP HANA, la macchina virtuale viene avviata di nuovo con gli stessi indirizzi IP (se la distribuzione è stata eseguita con indirizzi IP statici). 


### <a name="saprouter-enabling-sap-remote-support"></a>SAPRouter per abilitare il supporto remoto SAP
Se è presente una connessione da sito a sito tra le posizioni locali e Azure e si eseguono già componenti SAP, è molto probabile che SAProuter sia già in esecuzione. In questo caso, non è necessario eseguire altre operazioni con le istanze di SAP HANA distribuite in Azure, ad eccezione del mantenimento degli indirizzi IP privato e statico della macchina virtuale che ospita HANA nella configurazione di SAPRouter e l'adattamento dell'NSG della subnet che ospita la macchina virtuale HANA (traffico attraverso la porta TCP/IP 3299 consentito).

Se si distribuisce SAP HANA e ci si connette ad Azure tramite Internet e SAPRouter non è installato nella rete virtuale che esegue una macchina virtuale con SAP HANA, è consigliabile installare SAPRouter in una macchina virtuale separata nella subnet di gestione, come mostrato qui:


![Schema di distribuzione approssimativo per SAP HANA senza connessione da sito a sito e SAPRouter](media/hana-vm-operations/hana-simple-networking3.PNG)

È consigliabile installare SAPRouter in una macchina virtuale separata e non nella macchina virtuale jumpbox. La macchina virtuale separata deve avere un indirizzo IP statico. Per connettere SAPRouter all'istanza di SAPRouter ospitata da SAP (controparte dell'istanza di SAPRouter installata nella macchina virtuale), è necessario contattare SAP per ottenere un indirizzo IP, necessario per configurare l'istanza di SAPRouter. L'unica porta necessaria è la porta TCP 3299.
Per altre informazioni su come configurare e gestire connessioni di supporto remoto tramite SAPRouter, vedere questa [risorsa SAP](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Disponibilità elevata con SAP HANA in macchine virtuali native di Azure
Tramite l'esecuzione di SUSE Linux 12 SP1 e versioni più recenti, è possibile definire un cluster Pacemaker con dispositivi STONITH per impostare una configurazione SAP HANA che usi la replica sincrona con la replica di sistema e il failover automatico HANA. La procedura di configurazione viene descritta nell'articolo [Disponibilità elevata di SAP HANA in Macchine virtuali di Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-high-availability).

 










