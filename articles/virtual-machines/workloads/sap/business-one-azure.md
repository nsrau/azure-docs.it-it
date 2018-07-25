---
title: SAP Business One nelle macchine virtuali di Azure | Microsoft Docs
description: SAP Business One in Azure
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
ms.date: 07/15/2018
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0960f569f2a582d9712473081f66205272cfe31a
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2018
ms.locfileid: "39116961"
---
# <a name="sap-business-one-on-azure-virtual-machines"></a>SAP Business One nelle macchine virtuali di Azure
Questo documento contiene indicazioni per la distribuzione di SAP Business One nelle macchine virtuali di Azure, ma non sostituisce la documentazione relativa all'installazione di Business One per SAP. Presenta le linee guida di base per la pianificazione e la distribuzione per consentire l'esecuzione di applicazioni Business One nell'infrastruttura di Azure.

Business One supporta due diversi database:
- SQL Server: vedere [Nota SAP n. 928839 - Release Planning for Microsoft SQL Server](https://launchpad.support.sap.com/#/notes/928839) (Pianificazione del rilascio per Microsoft SQL Server)
- SAP HANA: per l'esatta matrice di supporto di SAP Business One per SAP HANA, vedere [SAP Product Availability Matrix](https://support.sap.com/pam) (Matrice di disponibilità dei prodotti SAP)

Per SQL Server sono applicabili le considerazioni di base relative alla distribuzione riportate nell'articolo [Distribuzione DBMS in macchine virtuali di Azure per SAP NetWeaver](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/dbms-guide). Per SAP HANA le considerazioni sono riportate in questo documento.

## <a name="prerequisites"></a>Prerequisiti
Per usare questa guida sono necessarie conoscenze di base dei componenti di Azure seguenti:

- [Macchine virtuali di Azure in Windows](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- [Macchine virtuali di Azure in Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Rete di Azure e gestione di reti virtuali con PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-virtual-network)
- [Rete di Azure e gestione di reti virtuali con l'interfaccia della riga di comando](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Gestire i dischi di Azure con l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Anche se si è interessati solo a Business One, il documento [Guida alla pianificazione e all'implementazione di macchine virtuali di Azure per SAP NetWeaver](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/planning-guide) può essere una buona fonte di informazioni.

Si presuppone che la distribuzione di SAP Business One venga eseguita da un utente che abbia familiarità con:

- L'installazione di SAP HANA in una determinata infrastruttura, ad esempio una macchina virtuale
- L'installazione dell'applicazione SAP Business One in un'infrastruttura come le macchine virtuali di Azure
- Il funzionamento di SAP Business One e del sistema DBMS selezionato
- La distribuzione dell'infrastruttura in Azure

Tutte queste aree non sono trattate in questo documento.

Oltre alla documentazione di Azure, è necessario tenere presenti le note principali di SAP, che fanno riferimento a Business One o che sono rilasciate da SAP come note centrali per Business One:

- [528296 - General Overview Note for SAP Business One Releases and Related Products](https://launchpad.support.sap.com/#/notes/528296) (Nota generale per le versioni di SAP Business One e i prodotti correlati)
- [2216195 - Release Updates Note for SAP Business One 9.2, version for SAP HANA](https://launchpad.support.sap.com/#/notes/2216195) (Nota sugli aggiornamenti della versione per SAP Business One 9.2, versione per SAP HANA)
- [2483583 - Central Note for SAP Business One 9.3](https://launchpad.support.sap.com/#/notes/2483583) (Nota centrale per SAP Business One 9.3)
- [2483615 - Release Updates Note for SAP Business One 9.3](https://launchpad.support.sap.com/#/notes/2483615) (Nota sugli aggiornamenti della versione per SAP Business One 9.3)
- [2483595 - Collective Note for SAP Business One 9.3 General Issues](https://launchpad.support.sap.com/#/notes/2483595) (Nota collettiva sui problemi generali relativi a SAP Business One 9.3)
- [2027458 - Collective Consulting Note for SAP HANA-Related Topics of SAP Business One, version for SAP HANA](https://launchpad.support.sap.com/#/notes/2027458) (Nota collettiva sugli argomenti correlati a SAP HANA di SAP Business One, versione per SAP HANA)


## <a name="business-one-architecture"></a>Architettura di Business One
Business One è un'applicazione a due livelli:

- Un livello client con un fat client
- Un livello database contenente lo schema del database per un tenant

Una panoramica più dettagliata sui componenti in esecuzione nella parte client e su quelli in esecuzione nella parte server è riportata in [SAP Business One Administrator's Guide](https://help.sap.com/http.svc/rc/879bd9289df34a47af838e67d74ea302/9.3/en-US/AdministratorGuide_SQL.pdf) (Manuale dell'amministratore di SAP Business One). 

Poiché è presente un'interazione critica a latenza elevata tra il livello client e il livello DBMS, quando si esegue la distribuzione in Azure, entrambi i livelli devono trovarsi in Azure. Normalmente, gli utenti stabiliscono una connessione Desktop remoto a una o più macchine virtuali con un servizio Desktop remoto per i componenti client di Business One.

### <a name="sizing-vms-for-sap-business-one"></a>Definizione della dimensione delle macchine virtuali per SAP Business One

Per definire la dimensione di una o più macchine virtuali client, i requisiti relativi alle risorse indicati da SAP sono riportati in [SAP Business One Hardware Requirements Guide](https://help.sap.com/http.svc/rc/011000358700000244612011e/9.3/en-US/B1_Hardware_Requirements_Guide.pdf) (Guida ai requisiti hardware di SAP Business One). Per Azure, esaminare e calcolare con attenzione i requisiti indicati nel capitolo 2.4 del documento.

Come macchine virtuali di Azure per l'hosting dei componenti client Business One e dell'host DBMS, sono consentite solo le macchine virtuali supportate da SAP NetWeaver. Per l'elenco delle macchine virtuali di Azure supportate da SAP NetWeaver, leggere la [nota SAP n. 1928533](https://launchpad.support.sap.com/#/notes/1928533).

Per l'esecuzione di SAP HANA come back-end DBMS per Business One, sono supportate solo le macchine virtuali elencate per Business on HANA nell'[elenco delle piattaforme IaaS certificate per HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure%23SAP%20Business%20One). I componenti client di Business One non sono soggetti a questa restrizione più forte applicata a SAP HANA come sistema DBMS.

### <a name="operating-system-releases-to-use-for-sap-business-one"></a>Versioni del sistema operativo da usare per SAP Business One

In teoria è sempre preferibile usare le versioni di sistema operativo più recenti. In particolare, nello spazio Linux, è stata introdotta una nuova funzionalità di Azure con diverse versioni secondarie più recenti di Suse e Red Hat. Sul lato Windows, è vivamente consigliato l'uso di Windows Server 2016.


## <a name="deploying-infrastructure-in-azure-for-sap-business-one"></a>Distribuzione dell'infrastruttura in Azure per SAP Business One
Nelle sezioni che seguono sono illustrati i principali componenti dell'infrastruttura per la distribuzione di SAP.

### <a name="azure-network-infrastructure"></a>Infrastruttura di rete di Azure
L'infrastruttura di rete da distribuire in Azure varia a seconda che si distribuisca in modo autonomo un singolo sistema Business One o che si esegua l'hosting di decine di sistemi Business One per i clienti. La progettazione può anche variare leggermente a seconda della modalità di connessione ad Azure. Tra le diverse possibilità è inclusa una progettazione con connettività VPN ad Azure ed estensione di Active Directory ad Azure tramite [VPN](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-plan-design) o [ExpressRoute](https://docs.microsoft.com/en-us/azure/expressroute/expressroute-introduction).

![Configurazione di rete semplificata con Business One](./media/business-one-azure/simple-network-with-VPN.PNG)

Questa configurazione semplificata presenta diverse istanze di sicurezza che consentono di controllare e limitare il routing. L'istanza iniziale è costituita da: 

- Router o firewall sul lato locale del cliente.
- L'istanza successiva consiste nel [gruppo di sicurezza di rete di Azure](https://docs.microsoft.com/en-us/azure/virtual-network/security-overview) che è possibile usare per introdurre le regole di routing e sicurezza per la rete virtuale di Azure in cui viene eseguita la configurazione di SAP Business One.
- Per evitare che gli utenti del client Business One possano vedere anche il server Business One che esegue il database, è necessario separare la macchina virtuale che ospita il client Business One e il server Business One in due subnet diverse all'interno della rete virtuale.
- Per limitare l'accesso al server Business One, si usa il gruppo di sicurezza di rete di Azure assegnato alle due diverse subnet.

Una versione più sofisticata di una configurazione di rete di Azure si basa sulle [procedure consigliate per un'architettura hub-spoke in Azure](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). Nel modello di architettura hub-spoke la prima configurazione semplificata assumerebbe l'aspetto seguente:


![Configurazione hub-spoke con Business One](./media/business-one-azure/hub-spoke-network-with-VPN.PNG)

Per i casi in cui gli utenti si connettono ad Azure attraverso Internet senza connettività privata, la progettazione della rete in Azure deve essere allineata ai principi documentati nell'architettura di riferimento di Azure per una [rete perimetrale tra Azure e Internet](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/dmz/secure-vnet-dmz).

### <a name="business-one-database-server"></a>Server di database Business One
Per il tipo di database sono disponibili SQL Server e SAP HANA. Indipendentemente dal sistema DBMS, è consigliabile leggere il documento [Considerazioni sulla distribuzione DBMS di macchine virtuali di Azure per un carico di lavoro SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) per informazioni generali sulle distribuzioni DBMS nelle macchine virtuali di Azure e gli argomenti correlati relativi alla rete e all'archiviazione.

Anche se è già stata sottolineata l'importanza di consultare documenti specifici e generici relativi ai database, è consigliabile acquisire familiarità con i concetti riportati nei documenti seguenti:

- [Gestire la disponibilità delle macchine virtuali Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) e [Gestire la disponibilità delle macchine virtuali Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)
- [Contratto di servizio per Macchine virtuali](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)

Questi documenti consentono di prendere decisioni consapevoli nella scelta dei tipi di archiviazione e della configurazione con disponibilità elevata.

In linea di principio è opportuno:

- Preferire [Archiviazione Premium di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage) ad [Archiviazione Standard di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- Preferire Azure Managed Disks ai dischi non gestiti
- Assicurarsi che per i dischi sia configurata una velocità effettiva sufficiente in termini di I/O e operazioni di I/O al secondo
- Combinare i volumi /hana/data e /hana/log per avere una configurazione di archiviazione economicamente conveniente


#### <a name="sql-server-as-dbms"></a>SQL Server come DBMS
Per distribuire SQL Server come DBMS per Business One, consultare il documento [Distribuzione DBMS per SQL Server di Macchine virtuali di Azure per SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver). 

La tabella seguente presenta le dimensioni approssimative del lato DBMS per SQL Server:

| Numero di utenti | vCPU | Memoria | Tipi di macchine virtuali di esempio |
| --- | --- | --- | --- |
| Fino a 20 | 4 | 16 GB | D4s_v3, E4s_v3 |
| Fino a 40 | 8 | 32 GB | D8s_v3, E8s_v3 |
| Fino a 80 | 16 | 64 GB | D16s_v3, E16s_v3 |
| Fino a 150 | 32 | 128 GB | D32s_v3, E32s_v3 |

I valori indicati nella tabella dovrebbero dare un'idea delle dimensioni iniziali. Se è necessario un numero maggiore o minore di risorse, Azure consente di adattare la configurazione con facilità. Per cambiare il tipo di macchina virtuale è sufficiente riavviare la macchina.


#### <a name="sap-hana-as-dbms"></a>SAP HANA come DBMS
Se si usa SAP HANA come DBMS, seguire le considerazioni riportate nel documento [Guida operativa a SAP HANA in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).

Per le configurazioni a disponibilità elevata e di ripristino di emergenza relative a SAP HANA come database per Business One in Azure, è consigliabile leggere [Disponibilità elevata di SAP HANA per macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview) e gli argomenti correlati indicati in questo documento.

Per le strategie di backup e ripristino di SAP HANA, è consigliabile leggere [Guida del backup di SAP HANA in macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide) e gli argomenti correlati indicati in questo documento.

 
### <a name="business-one-client-server"></a>Client server Business One
Per questi componenti, le considerazioni relative all'archiviazione non sono di primaria importanza. Tuttavia, per avere una piattaforma affidabile, è opportuno usare Archiviazione Premium di Azure per questa macchina virtuale, anche per il disco rigido virtuale di base. Per definire la dimensione della macchina virtuale, seguire le informazioni riportate in [SAP Business One Hardware Requirements Guide](https://help.sap.com/http.svc/rc/011000358700000244612011e/9.3/en-US/B1_Hardware_Requirements_Guide.pdf) (Guida ai requisiti hardware di SAP Business One). Per Azure, esaminare e calcolare con attenzione i requisiti indicati nel capitolo 2.4 del documento. Quando si calcolano i requisiti, per trovare la macchina virtuale ideale è necessario confrontarli con i dati riportati nei documenti seguenti:

- [Dimensioni per le macchine virtuali Windows in Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes)
- [Nota SAP n. 1928533](https://launchpad.support.sap.com/#/notes/1928533)

Confrontare i requisiti di CPU e memoria rispetto ai dati riportati nella documentazione Microsoft. Inoltre, quando si scelgono le macchine virtuali, tenere presente la velocità effettiva della rete.








