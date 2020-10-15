---
title: Scenari supportati per SAP HANA in Azure (istanze large) | Microsoft Docs
description: Scenari supportati e relativi dettagli di architettura per SAP HANA in Azure (istanze Large)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/26/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b4946524768d0cff483feb4045a2cc5fba169a7a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86507948"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>Scenari supportati nelle istanze Large di HANA
Questo articolo descrive gli scenari supportati e i dettagli dell'architettura per le istanze large di HANA (HLI).

>[!NOTE]
>Se lo scenario richiesto non è indicato in questo articolo, contattare il team di gestione dei servizi Microsoft per valutare i requisiti.
Prima di configurare l'unità HLI, convalidare la progettazione con SAP o con il partner di implementazione del servizio.

## <a name="terms-and-definitions"></a>Termini e definizioni
Verranno ora illustrati i termini e le definizioni usati in questo articolo:

- **SID**: identificatore di sistema per il sistema Hana
- **HLI**: istanze large di Hana
- **Ripristino di**emergenza
- **Ripristino**di emergenza normale: configurazione del sistema con una risorsa dedicata solo a scopo di ripristino di emergenza
- **Il ripristino di emergenza multifunzione**: sistema del sito di ripristino di emergenza configurato per l'uso di un ambiente non di produzione insieme a un'istanza di produzione configurata per un evento di ripristino di emergenza 
- **SID singolo**: sistema con un'istanza installata
- **Multi-SID**: sistema con più istanze configurate; detto anche ambiente MCOS
- **HSR**: replica di sistema SAP Hana

## <a name="overview"></a>Panoramica
Le istanze large di HANA supportano un'ampia gamma di architetture che consentono di soddisfare i requisiti aziendali. Le sezioni seguenti illustrano gli scenari di architettura e i relativi dettagli di configurazione. 

Il progetto di architettura derivata è esclusivamente dal punto di vista dell'infrastruttura ed è necessario consultare SAP o i partner di implementazione per la distribuzione di HANA. Se gli scenari non sono elencati in questo articolo, contattare il team di account Microsoft per esaminare l'architettura e derivare una soluzione.

> [!NOTE]
> Queste architetture sono completamente conformi alla progettazione TDI (Tailored Data Integration) e supportate da SAP.

Questo articolo descrive i dettagli dei due componenti in ogni architettura supportata:

- Ethernet
- Archiviazione

### <a name="ethernet"></a>Ethernet

Ogni server di cui è stato effettuato il provisioning è preconfigurato con set di interfacce Ethernet. Le interfacce Ethernet configurate in ogni unità HLI sono classificate in quattro tipi:

- **R**: utilizzato per o per accesso client.
- **B**: usato per la comunicazione da nodo a nodo. Questa interfaccia è configurata in tutti i server (indipendentemente dalla topologia richiesta) ma utilizzata solo per scenari con scalabilità orizzontale.
- **C**: usato per la connettività da nodo a archiviazione.
- **D**: usato per la connessione del dispositivo da nodo a iSCSI per l'installazione di STONITH. Questa interfaccia viene configurata solo quando viene richiesta l'installazione di HSR.  

| Interfaccia logica NIC | Tipo di SKU | Nome con sistema operativo SUSE | Nome con sistema operativo RHEL | Caso d'uso|
| --- | --- | --- | --- | --- |
| Una | TIPO I | eth0.tenant | eno1.tenant | Da client a HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Da nodo a nodo|
| C | TIPO I | eth1.tenant | eno2.tenant | Da nodo a archiviazione |
| D | TIPO I | eth4.tenant | eno4.tenant | STONITH |
| Una | TIPO II | vlan\<tenantNo> | team0.tenant | Da client a HLI |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Da nodo a nodo|
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Da nodo a archiviazione |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | STONITH |

È possibile scegliere l'interfaccia in base alla topologia configurata nell'unità HLI. Ad esempio, l'interfaccia "B" è configurata per la comunicazione da nodo a nodo, che risulta utile quando si dispone di una topologia con scalabilità orizzontale configurata. Questa interfaccia non viene usata per le configurazioni a nodo singolo, con scalabilità verticale. Per ulteriori informazioni sull'utilizzo dell'interfaccia, vedere gli scenari richiesti (più avanti in questo articolo). 

Se necessario, è possibile definire schede NIC aggiuntive. Tuttavia, *non è possibile* modificare le configurazioni delle schede di rete esistenti.

>[!NOTE]
>Potrebbero essere presenti interfacce aggiuntive che sono interfacce fisiche o di collegamento. È consigliabile considerare solo le interfacce citate in precedenza per il caso d'uso. Tutti gli altri possono essere ignorati.

La distribuzione per le unità con due indirizzi IP assegnati dovrebbe avere un aspetto simile al seguente:

- Ethernet "A" deve avere un indirizzo IP assegnato all'interno dell'intervallo di indirizzi del pool IP del server inviato a Microsoft. Questo indirizzo IP deve essere mantenuto nella directory *hosts* del sistema operativo.

- Per Ethernet "C" deve essere assegnato un indirizzo IP usato per la comunicazione con NFS. Questo indirizzo non *deve essere* mantenuto nella directory *etc/hosts* per consentire il traffico da istanza a istanza all'interno del tenant.

Per la replica di sistema HANA o la distribuzione con scalabilità orizzontale HANA, una configurazione di Blade con due indirizzi IP assegnati non è appropriata. Se si dispone solo di due indirizzi IP assegnati e si vuole distribuire tale configurazione, contattare SAP HANA in gestione dei servizi di Azure. Possono assegnare un terzo indirizzo IP in una terza VLAN. Per le unità di istanze large di HANA con tre indirizzi IP assegnati su tre porte NIC, si applicano le regole di utilizzo seguenti:

- Ethernet "A" deve avere un indirizzo IP assegnato al di fuori dell'intervallo di indirizzi del pool IP del server inviato a Microsoft. Questo indirizzo IP non deve essere mantenuto nella directory *etc/hosts* del sistema operativo.

- Ethernet "B" deve essere gestito esclusivamente nella directory *etc/hosts* per la comunicazione tra le diverse istanze. Questi sono gli indirizzi IP da mantenere nelle configurazioni HANA con scalabilità orizzontale come indirizzi IP usati da HANA per la configurazione tra i nodi.

- Ethernet "C" deve avere un indirizzo IP assegnato usato per la comunicazione con l'archiviazione NFS. Questo tipo di indirizzo non deve essere mantenuto nella directory *etc/hosts* .

- Ethernet "D" deve essere usato esclusivamente per accedere ai dispositivi STONITH per pacemaker. Questa interfaccia è necessaria quando si configura la replica di sistema HANA e si vuole ottenere il failover automatico del sistema operativo usando un dispositivo basato su SBD.


### <a name="storage"></a>Archiviazione
L'archiviazione è preconfigurata in base alla topologia richiesta. Le dimensioni dei volumi e i punti di montaggio variano a seconda del numero di server, del numero di SKU e della topologia configurata. Per ulteriori informazioni, vedere gli scenari necessari, più avanti in questo articolo. Se è necessaria una maggiore quantità di spazio di archiviazione, è possibile acquistarla con incrementi di 1 TB.

>[!NOTE]
>Il punto di montaggio/usr/SAP/ \<SID> è un collegamento simbolico al punto di montaggio di/Hana/Shared.


## <a name="supported-scenarios"></a>Scenari supportati

I diagrammi dell'architettura nelle sezioni successive utilizzano le seguenti notazioni:

[![Tabella dei diagrammi di architettura](media/hana-supported-scenario/Legends.png)](media/hana-supported-scenario/Legends.png#lightbox)

Di seguito sono riportati gli scenari supportati:

* Nodo singolo con un SID
* Nodo singolo MCOS
* Singolo nodo con ripristino di emergenza (normale)
* Singolo nodo con ripristino di emergenza (Multipurpose)
* HSR con STONITH
* HSR con ripristino di emergenza (normale/multiuso) 
* Failover automatico dell'host (1+1) 
* Scale-out con standby
* Scale-out senza standby
* Scale-out con ripristino di emergenza

## <a name="single-node-with-one-sid"></a>Nodo singolo con un SID

Questa topologia supporta un nodo in una configurazione con scalabilità verticale con un solo SID.

### <a name="architecture-diagram"></a>Diagramma dell'architettura  

![Nodo singolo con un SID](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Ethernet
Sono preconfigurate le interfacce di rete seguenti:

| Interfaccia logica NIC | Tipo di SKU | Nome con sistema operativo SUSE | Nome con sistema operativo RHEL | Caso d'uso|
| --- | --- | --- | --- | --- |
| Una | TIPO I | eth0.tenant | eno1.tenant | Da client a HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurato ma non in uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Da nodo a archiviazione |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurato ma non in uso |
| Una | TIPO II | vlan\<tenantNo> | team0.tenant | Da client a HLI |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Configurato ma non in uso |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Da nodo a archiviazione |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurato ma non in uso |

### <a name="storage"></a>Archiviazione
I punti di montaggio seguenti sono preconfigurati:

| Mount point (Punto di montaggio) | Caso d'uso | 
| --- | --- |
|/hana/shared/SID | Installazione di HANA | 
|/hana/data/SID/mnt00001 | Installazione di file di dati | 
|/hana/log/SID/mnt00001 | Installazione dei file di log | 
|/hana/logbackups/SID | Log di rollforward |

### <a name="key-considerations"></a>Considerazioni essenziali
- /usr/SAP/SID è un collegamento simbolico a /hana/shared/SID.

## <a name="single-node-mcos"></a>Nodo singolo MCOS

Questa topologia supporta un nodo in una configurazione con scalabilità verticale con più SID.

### <a name="architecture-diagram"></a>Diagramma dell'architettura  

![Nodo singolo MCOS](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Ethernet
Sono preconfigurate le interfacce di rete seguenti:

| Interfaccia logica NIC | Tipo di SKU | Nome con sistema operativo SUSE | Nome con sistema operativo RHEL | Caso d'uso|
| --- | --- | --- | --- | --- |
| Una | TIPO I | eth0.tenant | eno1.tenant | Da client a HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurato ma non in uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Da nodo a archiviazione |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurato ma non in uso |
| Una | TIPO II | vlan\<tenantNo> | team0.tenant | Da client a HLI |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Configurato ma non in uso |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Da nodo a archiviazione |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurato ma non in uso |

### <a name="storage"></a>Archiviazione
I punti di montaggio seguenti sono preconfigurati:

| Mount point (Punto di montaggio) | Caso d'uso | 
| --- | --- |
|/hana/shared/SID1 | Installazione di HANA per SID1 | 
|/hana/data/SID1/mnt00001 | Installazione di file di dati per SID1 | 
|/hana/log/SID1/mnt00001 | Installazione dei file di log per SID1 | 
|/hana/logbackups/SID1 | Log di rollforward per SID1 |
|/hana/shared/SID2 | Installazione di HANA per SID2 | 
|/hana/data/SID2/mnt00001 | Installazione di file di dati per SID2 | 
|/hana/log/SID2/mnt00001 | Installazione dei file di log per SID2 | 
|/hana/logbackups/SID2 | Log di rollforward per SID2 |

### <a name="key-considerations"></a>Considerazioni essenziali
- /usr/SAP/SID è un collegamento simbolico a /hana/shared/SID.
- La distribuzione delle dimensioni del volume è basata sulle dimensioni del database in memoria. Per informazioni sulle dimensioni del database in memoria supportate in un ambiente a più SID, vedere [panoramica e architettura](./hana-overview-architecture.md).

## <a name="single-node-with-dr-using-storage-replication"></a>Singolo nodo con ripristino di emergenza che usa la replica di archiviazione
 
Questa topologia supporta un nodo in una configurazione con scalabilità verticale con uno o più SID, con replica basata sull'archiviazione nel sito di ripristino di emergenza per un SID primario. Nel diagramma solo un sistema a SID singolo viene illustrato nel sito primario, ma sono supportati anche i sistemi MCOS.

### <a name="architecture-diagram"></a>Diagramma dell'architettura  

![Singolo nodo con ripristino di emergenza che usa la replica di archiviazione](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Ethernet
Sono preconfigurate le interfacce di rete seguenti:

| Interfaccia logica NIC | Tipo di SKU | Nome con sistema operativo SUSE | Nome con sistema operativo RHEL | Caso d'uso|
| --- | --- | --- | --- | --- |
| Una | TIPO I | eth0.tenant | eno1.tenant | Da client a HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurato ma non in uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Da nodo a archiviazione |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurato ma non in uso |
| Una | TIPO II | vlan\<tenantNo> | team0.tenant | Da client a HLI |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Configurato ma non in uso |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Da nodo a archiviazione |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurato ma non in uso |

### <a name="storage"></a>Archiviazione
I punti di montaggio seguenti sono preconfigurati:

| Mount point (Punto di montaggio) | Caso d'uso | 
| --- | --- |
|/hana/shared/SID | Installazione di HANA per SID | 
|/hana/data/SID/mnt00001 | Installazione di file di dati per SID | 
|/hana/log/SID/mnt00001 | Installazione dei file di log per SID | 
|/hana/logbackups/SID | Log di rollforward per SID |


### <a name="key-considerations"></a>Considerazioni essenziali
- /usr/SAP/SID è un collegamento simbolico a /hana/shared/SID.
- Per MCOS: la distribuzione delle dimensioni del volume è basata sulle dimensioni del database in memoria. Per informazioni sulle dimensioni del database in memoria supportate in un ambiente a più SID, vedere [panoramica e architettura](./hana-overview-architecture.md).
- Nel sito di ripristino di emergenza: i volumi e i punti di montaggio sono configurati (contrassegnati come "necessari per l'installazione di HANA") per l'installazione dell'istanza di HANA di produzione nell'unità di ripristino di emergenza HLI. 
- Nel sito di ripristino di emergenza: i dati, i backup del log e i volumi condivisi (contrassegnati come "replica di archiviazione") vengono replicati tramite snapshot dal sito di produzione. Questi volumi vengono montati solo durante il failover. Per altre informazioni, vedere [procedura di failover](./hana-overview-high-availability-disaster-recovery.md)per il ripristino di emergenza.
- Il volume di avvio per la *classe di tipo i di SKU* viene replicato nel nodo di ripristino di emergenza.


## <a name="single-node-with-dr-multipurpose-using-storage-replication"></a>Singolo nodo con ripristino di emergenza (Multipurpose) con replica di archiviazione
 
Questa topologia supporta un nodo in una configurazione con scalabilità verticale con uno o più SID, con replica basata sull'archiviazione nel sito di ripristino di emergenza per un SID primario. Nel diagramma solo un sistema a SID singolo viene illustrato nel sito primario, ma sono supportati anche i sistemi a più SID (MCOS). Nel sito di ripristino di emergenza, l'unità HLI viene usata per l'istanza di QA mentre le operazioni di produzione sono in esecuzione dal sito primario. Durante il failover di ripristino di emergenza (o test di failover), l'istanza di QA nel sito di ripristino di emergenza viene arrestata.

### <a name="architecture-diagram"></a>Diagramma dell'architettura  

![Singolo nodo con ripristino di emergenza (Multipurpose) con replica di archiviazione](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Ethernet
Sono preconfigurate le interfacce di rete seguenti:

| Interfaccia logica NIC | Tipo di SKU | Nome con sistema operativo SUSE | Nome con sistema operativo RHEL | Caso d'uso|
| --- | --- | --- | --- | --- |
| Una | TIPO I | eth0.tenant | eno1.tenant | Da client a HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurato ma non in uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Da nodo a archiviazione |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurato ma non in uso |
| Una | TIPO II | vlan\<tenantNo> | team0.tenant | Da client a HLI |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Configurato ma non in uso |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Da nodo a archiviazione |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurato ma non in uso |

### <a name="storage"></a>Archiviazione
I punti di montaggio seguenti sono preconfigurati:

| Mount point (Punto di montaggio) | Caso d'uso | 
| --- | --- |
|**Nel sito primario**|
|/hana/shared/SID | Installazione di HANA per SID di produzione | 
|/hana/data/SID/mnt00001 | Installazione di file di dati per SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di log per il SID di produzione | 
|/hana/logbackups/SID | Log di rollforward per SID di produzione |
|**Nel sito di ripristino di emergenza**|
|/hana/shared/SID | Installazione di HANA per SID di produzione | 
|/hana/data/SID/mnt00001 | Installazione di file di dati per SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di log per il SID di produzione | 
|/hana/shared/QA-SID | Installazione di HANA per il SID di QA | 
|/hana/data/QA-SID/mnt00001 | Installazione dei file di dati per il SID QA | 
|/hana/log/QA-SID/mnt00001 | Installazione dei file di log per il SID di QA |
|/hana/logbackups/QA-SID | Log di rollforward per SID QA |

### <a name="key-considerations"></a>Considerazioni essenziali
- /usr/SAP/SID è un collegamento simbolico a /hana/shared/SID.
- Per MCOS: la distribuzione delle dimensioni del volume è basata sulle dimensioni del database in memoria. Per informazioni sulle dimensioni del database in memoria supportate in un ambiente a più SID, vedere [panoramica e architettura](./hana-overview-architecture.md).
- Nel sito di ripristino di emergenza: i volumi e i punti di montaggio sono configurati (contrassegnati come "necessari per l'installazione di HANA") per l'installazione dell'istanza di HANA di produzione nell'unità di ripristino di emergenza HLI. 
- Nel sito di ripristino di emergenza: i dati, i backup del log e i volumi condivisi (contrassegnati come "replica di archiviazione") vengono replicati tramite snapshot dal sito di produzione. Questi volumi vengono montati solo durante il failover. Per altre informazioni, vedere [procedura di failover](./hana-overview-high-availability-disaster-recovery.md)per il ripristino di emergenza. 
- Nel sito di ripristino di emergenza: i dati, i backup del log, i log e i volumi condivisi per QA (contrassegnati come "installazione dell'istanza di QA") sono configurati per l'installazione dell'istanza di QA.
- Il volume di avvio per la *classe di tipo i di SKU* viene replicato nel nodo di ripristino di emergenza.

## <a name="hsr-with-stonith-for-high-availability"></a>HSR con STONITH per la disponibilità elevata
 
Questa topologia supporta due nodi per la configurazione della replica di sistema HANA. Questa configurazione è supportata solo per le singole istanze di HANA in un nodo. Questo significa che gli scenari MCOS *non* sono supportati.

> [!NOTE]
> A partire dal 2019 dicembre, questa architettura è supportata solo per il sistema operativo SUSE.


### <a name="architecture-diagram"></a>Diagramma dell'architettura  

![HSR con STONITH per la disponibilità elevata](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Ethernet
Sono preconfigurate le interfacce di rete seguenti:

| Interfaccia logica NIC | Tipo di SKU | Nome con sistema operativo SUSE | Nome con sistema operativo RHEL | Caso d'uso|
| --- | --- | --- | --- | --- |
| Una | TIPO I | eth0.tenant | eno1.tenant | Da client a HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurato ma non in uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Da nodo a archiviazione |
| D | TIPO I | eth4.tenant | eno4.tenant | Usato per STONITH |
| Una | TIPO II | vlan\<tenantNo> | team0.tenant | Da client a HLI |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Configurato ma non in uso |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Da nodo a archiviazione |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Usato per STONITH |

### <a name="storage"></a>Archiviazione
I punti di montaggio seguenti sono preconfigurati:

| Mount point (Punto di montaggio) | Caso d'uso | 
| --- | --- |
|**Nel nodo primario**|
|/hana/shared/SID | Installazione di HANA per SID di produzione | 
|/hana/data/SID/mnt00001 | Installazione di file di dati per SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di log per il SID di produzione | 
|/hana/logbackups/SID | Log di rollforward per SID di produzione |
|**Nel nodo secondario**|
|/hana/shared/SID | Installazione di HANA per il SID secondario | 
|/hana/data/SID/mnt00001 | Installazione di file di dati per il SID secondario | 
|/hana/log/SID/mnt00001 | Installazione dei file di log per il SID secondario | 
|/hana/logbackups/SID | Log di rollforward per SID secondario |

### <a name="key-considerations"></a>Considerazioni essenziali
- /usr/SAP/SID è un collegamento simbolico a /hana/shared/SID.
- Per MCOS: la distribuzione delle dimensioni del volume è basata sulle dimensioni del database in memoria. Per informazioni sulle dimensioni del database in memoria supportate in un ambiente a più SID, vedere [panoramica e architettura](./hana-overview-architecture.md).
- STONITH: per l'installazione STONITH è configurato un SBD. Tuttavia, l'utilizzo di STONITH è facoltativo.


## <a name="high-availability-with-hsr-and-dr-with-storage-replication"></a>Disponibilità elevata con HSR e ripristino di emergenza con replica di archiviazione
 
Questa topologia supporta due nodi per la configurazione della replica di sistema HANA. Sono supportati sia il DRs normale che quello multifunzione. Queste configurazioni sono supportate solo per le singole istanze di HANA in un nodo. Questo significa che gli scenari MCOS *non* sono supportati con queste configurazioni.

Nel diagramma, uno scenario a più finalità viene illustrato nel sito di ripristino di emergenza, in cui l'unità HLI viene usata per l'istanza di QA mentre le operazioni di produzione sono in esecuzione dal sito primario. Durante il failover di ripristino di emergenza (o test di failover), l'istanza di QA nel sito di ripristino di emergenza viene arrestata. 

### <a name="architecture-diagram"></a>Diagramma dell'architettura  

![Disponibilità elevata con HSR e ripristino di emergenza con replica di archiviazione](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Ethernet
Sono preconfigurate le interfacce di rete seguenti:

| Interfaccia logica NIC | Tipo di SKU | Nome con sistema operativo SUSE | Nome con sistema operativo RHEL | Caso d'uso|
| --- | --- | --- | --- | --- |
| Una | TIPO I | eth0.tenant | eno1.tenant | Da client a HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurato ma non in uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Da nodo a archiviazione |
| D | TIPO I | eth4.tenant | eno4.tenant | Usato per STONITH |
| Una | TIPO II | vlan\<tenantNo> | team0.tenant | Da client a HLI |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Configurato ma non in uso |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Da nodo a archiviazione |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Usato per STONITH |

### <a name="storage"></a>Archiviazione
I punti di montaggio seguenti sono preconfigurati:

| Mount point (Punto di montaggio) | Caso d'uso | 
| --- | --- |
|**Nel nodo primario del sito primario**|
|/hana/shared/SID | Installazione di HANA per SID di produzione | 
|/hana/data/SID/mnt00001 | Installazione di file di dati per SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di log per il SID di produzione | 
|/hana/logbackups/SID | Log di rollforward per SID di produzione |
|**Nel nodo secondario del sito primario**|
|/hana/shared/SID | Installazione di HANA per il SID secondario | 
|/hana/data/SID/mnt00001 | Installazione di file di dati per il SID secondario | 
|/hana/log/SID/mnt00001 | Installazione dei file di log per il SID secondario | 
|/hana/logbackups/SID | Log di rollforward per SID secondario |
|**Nel sito di ripristino di emergenza**|
|/hana/shared/SID | Installazione di HANA per SID di produzione | 
|/hana/data/SID/mnt00001 | Installazione di file di dati per SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di log per il SID di produzione | 
|/hana/shared/QA-SID | Installazione di HANA per il SID di QA | 
|/hana/data/QA-SID/mnt00001 | Installazione dei file di dati per il SID QA | 
|/hana/log/QA-SID/mnt00001 | Installazione dei file di log per il SID di QA |
|/hana/logbackups/QA-SID | Log di rollforward per SID QA |

### <a name="key-considerations"></a>Considerazioni essenziali
- /usr/SAP/SID è un collegamento simbolico a /hana/shared/SID.
- Per MCOS: la distribuzione delle dimensioni del volume è basata sulle dimensioni del database in memoria. Per informazioni sulle dimensioni del database in memoria supportate in un ambiente a più SID, vedere [panoramica e architettura](./hana-overview-architecture.md).
- STONITH: per l'installazione STONITH è configurato un SBD. Tuttavia, l'utilizzo di STONITH è facoltativo.
- Nel sito di ripristino di emergenza: *sono necessari due set di volumi di archiviazione* per la replica del nodo primario e secondario.
- Nel sito di ripristino di emergenza: i volumi e i punti di montaggio sono configurati (contrassegnati come "necessari per l'installazione di HANA") per l'installazione dell'istanza di HANA di produzione nell'unità di ripristino di emergenza HLI. 
- Nel sito di ripristino di emergenza: i dati, i backup del log e i volumi condivisi (contrassegnati come "replica di archiviazione") vengono replicati tramite snapshot dal sito di produzione. Questi volumi vengono montati solo durante il failover. Per altre informazioni, vedere [procedura di failover](./hana-overview-high-availability-disaster-recovery.md)per il ripristino di emergenza. 
- Nel sito di ripristino di emergenza: i dati, i backup del log, i log e i volumi condivisi per QA (contrassegnati come "installazione dell'istanza di QA") sono configurati per l'installazione dell'istanza di QA.
- Il volume di avvio per la *classe di tipo i di SKU* viene replicato nel nodo di ripristino di emergenza.


## <a name="host-auto-failover-11"></a>Failover automatico dell'host (1+1)
 
Questa topologia supporta due nodi in una configurazione di failover automatico dell'host. È presente un nodo con un ruolo di lavoro/master e un altro come standby. *SAP supporta questo scenario solo per S/4 HANA.* Per ulteriori informazioni, vedere la [Nota OSS 2408419-SAP S/4HANA-supporto](https://launchpad.support.sap.com/#/notes/2408419)per più nodi.



### <a name="architecture-diagram"></a>Diagramma dell'architettura  

![Failover automatico dell'host (1+1)](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Ethernet
Sono preconfigurate le interfacce di rete seguenti:

| Interfaccia logica NIC | Tipo di SKU | Nome con sistema operativo SUSE | Nome con sistema operativo RHEL | Caso d'uso|
| --- | --- | --- | --- | --- |
| Una | TIPO I | eth0.tenant | eno1.tenant | Da client a HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Comunicazione da nodo a nodo |
| C | TIPO I | eth1.tenant | eno2.tenant | Da nodo a archiviazione |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurato ma non in uso |
| Una | TIPO II | vlan\<tenantNo> | team0.tenant | Da client a HLI |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Comunicazione da nodo a nodo |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Da nodo a archiviazione |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurato ma non in uso |

### <a name="storage"></a>Archiviazione
I punti di montaggio seguenti sono preconfigurati:

| Mount point (Punto di montaggio) | Caso d'uso | 
| --- | --- |
|**Nei nodi master e standby**|
|/hana/shared | Installazione di HANA per SID di produzione | 
|/hana/data/SID/mnt00001 | Installazione di file di dati per SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di log per il SID di produzione | 
|/hana/logbackups/SID | Log di rollforward per SID di produzione |



### <a name="key-considerations"></a>Considerazioni essenziali
- /usr/SAP/SID è un collegamento simbolico a /hana/shared/SID.
- In standby: i volumi e i punti di montaggio sono configurati (contrassegnati come "necessari per l'installazione di HANA") per l'installazione dell'istanza HANA nell'unità standby.
 

## <a name="scale-out-with-standby"></a>Scale-out con standby
 
Questa topologia supporta più nodi in una configurazione scale-out. È presente un nodo con un ruolo master, uno o più nodi con un ruolo di lavoro e uno o più nodi come standby. Tuttavia, può essere presente un solo nodo master in un singolo momento.


### <a name="architecture-diagram"></a>Diagramma dell'architettura  

![Scale-out con standby](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Ethernet
Sono preconfigurate le interfacce di rete seguenti:

| Interfaccia logica NIC | Tipo di SKU | Nome con sistema operativo SUSE | Nome con sistema operativo RHEL | Caso d'uso|
| --- | --- | --- | --- | --- |
| Una | TIPO I | eth0.tenant | eno1.tenant | Da client a HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Comunicazione da nodo a nodo |
| C | TIPO I | eth1.tenant | eno2.tenant | Da nodo a archiviazione |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurato ma non in uso |
| Una | TIPO II | vlan\<tenantNo> | team0.tenant | Da client a HLI |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Comunicazione da nodo a nodo |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Da nodo a archiviazione |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurato ma non in uso |

### <a name="storage"></a>Archiviazione
I punti di montaggio seguenti sono preconfigurati:

| Mount point (Punto di montaggio) | Caso d'uso | 
| --- | --- |
|**Nei nodi master, di lavoro e standby**|
|/hana/shared | Installazione di HANA per SID di produzione | 
|/hana/data/SID/mnt00001 | Installazione di file di dati per SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di log per il SID di produzione | 
|/hana/logbackups/SID | Log di rollforward per SID di produzione |


## <a name="scale-out-without-standby"></a>Scale-out senza standby
 
Questa topologia supporta più nodi in una configurazione scale-out. È presente un nodo con un ruolo master e uno o più nodi con un ruolo di lavoro. Tuttavia, può essere presente un solo nodo master in un singolo momento.


### <a name="architecture-diagram"></a>Diagramma dell'architettura  

![Scale-out senza standby](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Ethernet
Sono preconfigurate le interfacce di rete seguenti:

| Interfaccia logica NIC | Tipo di SKU | Nome con sistema operativo SUSE | Nome con sistema operativo RHEL | Caso d'uso|
| --- | --- | --- | --- | --- |
| Una | TIPO I | eth0.tenant | eno1.tenant | Da client a HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Comunicazione da nodo a nodo |
| C | TIPO I | eth1.tenant | eno2.tenant | Da nodo a archiviazione |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurato ma non in uso |
| Una | TIPO II | vlan\<tenantNo> | team0.tenant | Da client a HLI |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Comunicazione da nodo a nodo |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Da nodo a archiviazione |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurato ma non in uso |

### <a name="storage"></a>Archiviazione
I punti di montaggio seguenti sono preconfigurati:

| Mount point (Punto di montaggio) | Caso d'uso | 
| --- | --- |
|**Nei nodi master e di lavoro**|
|/hana/shared | Installazione di HANA per SID di produzione | 
|/hana/data/SID/mnt00001 | Installazione di file di dati per SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di log per il SID di produzione | 
|/hana/logbackups/SID | Log di rollforward per SID di produzione |


### <a name="key-considerations"></a>Considerazioni essenziali
- /usr/SAP/SID è un collegamento simbolico a /hana/shared/SID.

## <a name="scale-out-with-dr-using-storage-replication"></a>Scalabilità orizzontale con ripristino di emergenza tramite la replica di archiviazione
 
Questa topologia supporta più nodi in una configurazione scale-out con ripristino di emergenza. Sono supportati sia il DRs normale che quello multifunzione. Nel diagramma è illustrato solo il ripristino di emergenza normale. Questa topologia può essere richiesta con o senza il nodo standby.


### <a name="architecture-diagram"></a>Diagramma dell'architettura  

![Scalabilità orizzontale con ripristino di emergenza tramite la replica di archiviazione](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Ethernet
Sono preconfigurate le interfacce di rete seguenti:

| Interfaccia logica NIC | Tipo di SKU | Nome con sistema operativo SUSE | Nome con sistema operativo RHEL | Caso d'uso|
| --- | --- | --- | --- | --- |
| Una | TIPO I | eth0.tenant | eno1.tenant | Da client a HLI |
| B | TIPO I | eth2.tenant | eno3.tenant | Comunicazione da nodo a nodo |
| C | TIPO I | eth1.tenant | eno2.tenant | Da nodo a archiviazione |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurato ma non in uso |
| Una | TIPO II | vlan\<tenantNo> | team0.tenant | Da client a HLI |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Comunicazione da nodo a nodo |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Da nodo a archiviazione |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurato ma non in uso |

### <a name="storage"></a>Archiviazione
I punti di montaggio seguenti sono preconfigurati:

| Mount point (Punto di montaggio) | Caso d'uso | 
| --- | --- |
|**Nel nodo primario**|
|/hana/shared | Installazione di HANA per SID di produzione | 
|/hana/data/SID/mnt00001 | Installazione di file di dati per SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di log per il SID di produzione | 
|/hana/logbackups/SID | Log di rollforward per SID di produzione |
|**Nel nodo ripristino di emergenza**|
|/hana/shared | Installazione di HANA per SID di produzione | 
|/hana/data/SID/mnt00001 | Installazione di file di dati per SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di log per il SID di produzione | 


### <a name="key-considerations"></a>Considerazioni essenziali
- /usr/SAP/SID è un collegamento simbolico a /hana/shared/SID.
-  Nel sito di ripristino di emergenza: i volumi e i punti di montaggio sono configurati (contrassegnati come "necessari per l'installazione di HANA") per l'installazione dell'istanza di HANA di produzione nell'unità di ripristino di emergenza HLI. 
- Nel sito di ripristino di emergenza: i dati, i backup del log e i volumi condivisi (contrassegnati come "replica di archiviazione") vengono replicati tramite snapshot dal sito di produzione. Questi volumi vengono montati solo durante il failover. Per altre informazioni, vedere [procedura di failover](./hana-overview-high-availability-disaster-recovery.md)per il ripristino di emergenza. 
- Il volume di avvio per la *classe di tipo i di SKU* viene replicato nel nodo di ripristino di emergenza.


## <a name="single-node-with-dr-using-hsr"></a>Singolo nodo con ripristino di emergenza con HSR
 
Questa topologia supporta un nodo in una configurazione con scalabilità verticale con un SID, con la replica di sistema HANA nel sito di ripristino di emergenza per un SID primario. Nel diagramma solo un sistema a SID singolo viene illustrato nel sito primario, ma sono supportati anche i sistemi a più SID (MCOS).

### <a name="architecture-diagram"></a>Diagramma dell'architettura  

![Singolo nodo con ripristino di emergenza con HSR](media/hana-supported-scenario/single-node-hsr-dr-111.png)

### <a name="ethernet"></a>Ethernet
Sono preconfigurate le interfacce di rete seguenti:

| Interfaccia logica NIC | Tipo di SKU | Nome con sistema operativo SUSE | Nome con sistema operativo RHEL | Caso d'uso|
| --- | --- | --- | --- | --- |
| Una | TIPO I | eth0.tenant | eno1.tenant | Da client a HLI/HSR |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurato ma non in uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Da nodo a archiviazione |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurato ma non in uso |
| Una | TIPO II | vlan\<tenantNo> | team0.tenant | Da client a HLI/HSR |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Configurato ma non in uso |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Da nodo a archiviazione |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurato ma non in uso |

### <a name="storage"></a>Archiviazione
I punti di montaggio seguenti sono preconfigurati in entrambe le unità HLI (primario e ripristino di emergenza):

| Mount point (Punto di montaggio) | Caso d'uso | 
| --- | --- |
|/hana/shared/SID | Installazione di HANA per SID | 
|/hana/data/SID/mnt00001 | Installazione di file di dati per SID | 
|/hana/log/SID/mnt00001 | Installazione dei file di log per SID | 
|/hana/logbackups/SID | Log di rollforward per SID |


### <a name="key-considerations"></a>Considerazioni essenziali
- /usr/SAP/SID è un collegamento simbolico a /hana/shared/SID.
- Per MCOS: la distribuzione delle dimensioni del volume è basata sulle dimensioni del database in memoria. Per informazioni sulle dimensioni del database in memoria supportate in un ambiente a più SID, vedere [panoramica e architettura](./hana-overview-architecture.md).
- Il nodo primario viene sincronizzato con il nodo ripristino di emergenza tramite la replica di sistema HANA. 
- [Copertura globale](../../../expressroute/expressroute-global-reach.md) viene usato per collegare insieme i circuiti ExpressRoute per creare una rete privata tra le reti regionali.



## <a name="single-node-hsr-to-dr-cost-optimized"></a>HSR a nodo singolo al ripristino di emergenza (costo ottimizzato) 
 
 Questa topologia supporta un nodo in una configurazione con scalabilità verticale con un SID, con la replica di sistema HANA nel sito di ripristino di emergenza per un SID primario. Nel diagramma solo un sistema a SID singolo viene illustrato nel sito primario, ma sono supportati anche i sistemi a più SID (MCOS). Nel sito di ripristino di emergenza viene usata un'unità HLI per l'istanza di QA mentre le operazioni di produzione sono in esecuzione dal sito primario. Durante il failover di ripristino di emergenza (o test di failover), l'istanza di QA nel sito di ripristino di emergenza viene arrestata.

### <a name="architecture-diagram"></a>Diagramma dell'architettura  

![HSR a nodo singolo al ripristino di emergenza (costo ottimizzato)](media/hana-supported-scenario/single-node-hsr-dr-cost-optimized-121.png)

### <a name="ethernet"></a>Ethernet
Sono preconfigurate le interfacce di rete seguenti:

| Interfaccia logica NIC | Tipo di SKU | Nome con sistema operativo SUSE | Nome con sistema operativo RHEL | Caso d'uso|
| --- | --- | --- | --- | --- |
| Una | TIPO I | eth0.tenant | eno1.tenant | Da client a HLI/HSR |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurato ma non in uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Da nodo a archiviazione |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurato ma non in uso |
| Una | TIPO II | vlan\<tenantNo> | team0.tenant | Da client a HLI/HSR |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Configurato ma non in uso |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Da nodo a archiviazione |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurato ma non in uso |

### <a name="storage"></a>Archiviazione
I punti di montaggio seguenti sono preconfigurati:

| Mount point (Punto di montaggio) | Caso d'uso | 
| --- | --- |
|**Nel sito primario**|
|/hana/shared/SID | Installazione di HANA per SID di produzione | 
|/hana/data/SID/mnt00001 | Installazione di file di dati per SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di log per il SID di produzione | 
|/hana/logbackups/SID | Log di rollforward per SID di produzione |
|**Nel sito di ripristino di emergenza**|
|/hana/shared/SID | Installazione di HANA per SID di produzione | 
|/hana/data/SID/mnt00001 | Installazione di file di dati per SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di log per il SID di produzione | 
|/hana/logbackups/SID | Log di rollforward per SID di produzione |
|/hana/shared/QA-SID | Installazione di HANA per il SID di QA | 
|/hana/data/QA-SID/mnt00001 | Installazione dei file di dati per il SID QA | 
|/hana/log/QA-SID/mnt00001 | Installazione dei file di log per il SID di QA |
|/hana/logbackups/QA-SID | Log di rollforward per SID QA |

### <a name="key-considerations"></a>Considerazioni essenziali
- /usr/SAP/SID è un collegamento simbolico a /hana/shared/SID.
- Per MCOS: la distribuzione delle dimensioni del volume è basata sulle dimensioni del database in memoria. Per informazioni sulle dimensioni del database in memoria supportate in un ambiente a più SID, vedere [panoramica e architettura](./hana-overview-architecture.md).
- Nel sito di ripristino di emergenza: i volumi e i punti di montaggio sono configurati (contrassegnati come "istanza di PROD nel sito di ripristino di emergenza") per l'installazione dell'istanza di HANA di produzione nell'unità di ripristino di emergenza 
- Nel sito di ripristino di emergenza: i dati, i backup del log, i log e i volumi condivisi per QA (contrassegnati come "installazione dell'istanza di QA") sono configurati per l'installazione dell'istanza di QA.
- Il nodo primario viene sincronizzato con il nodo ripristino di emergenza tramite la replica di sistema HANA. 
- [Copertura globale](../../../expressroute/expressroute-global-reach.md) viene usato per collegare insieme i circuiti ExpressRoute per creare una rete privata tra le reti regionali.

## <a name="high-availability-and-disaster-recovery-with-hsr"></a>Disponibilità elevata e ripristino di emergenza con HSR 
 
 Questa topologia supporta due nodi per la configurazione della replica di sistema HANA per la disponibilità elevata delle aree locali. Per il ripristino di emergenza, il terzo nodo nell'area di ripristino di emergenza viene sincronizzato con il sito primario utilizzando HSR (modalità asincrona). 

### <a name="architecture-diagram"></a>Diagramma dell'architettura  

![Disponibilità elevata e ripristino di emergenza con HSR](media/hana-supported-scenario/hana-system-replication-dr-131.png)

### <a name="ethernet"></a>Ethernet
Sono preconfigurate le interfacce di rete seguenti:

| Interfaccia logica NIC | Tipo di SKU | Nome con sistema operativo SUSE | Nome con sistema operativo RHEL | Caso d'uso|
| --- | --- | --- | --- | --- |
| Una | TIPO I | eth0.tenant | eno1.tenant | Da client a HLI/HSR |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurato ma non in uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Da nodo a archiviazione |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurato ma non in uso |
| Una | TIPO II | vlan\<tenantNo> | team0.tenant | Da client a HLI/HSR |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Configurato ma non in uso |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Da nodo a archiviazione |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurato ma non in uso |

### <a name="storage"></a>Archiviazione
I punti di montaggio seguenti sono preconfigurati:

| Mount point (Punto di montaggio) | Caso d'uso | 
| --- | --- |
|**Nel sito primario**|
|/hana/shared/SID | Installazione di HANA per SID di produzione | 
|/hana/data/SID/mnt00001 | Installazione di file di dati per SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di log per il SID di produzione | 
|/hana/logbackups/SID | Log di rollforward per SID di produzione |
|**Nel sito di ripristino di emergenza**|
|/hana/shared/SID | Installazione di HANA per SID di produzione | 
|/hana/data/SID/mnt00001 | Installazione di file di dati per SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di log per il SID di produzione | 
|/hana/logbackups/SID | Log di rollforward per SID di produzione |


### <a name="key-considerations"></a>Considerazioni essenziali
- /usr/SAP/SID è un collegamento simbolico a /hana/shared/SID.
- Nel sito di ripristino di emergenza: i volumi e i punti di montaggio sono configurati (contrassegnati come "istanza di ripristino di emergenza") per l'installazione dell'istanza di HANA di produzione nell'unità di ripristino di emergenza HLI. 
- Il nodo del sito primario viene sincronizzato con il nodo ripristino di emergenza tramite la replica di sistema HANA. 
- [Copertura globale](../../../expressroute/expressroute-global-reach.md) viene usato per collegare insieme i circuiti ExpressRoute per creare una rete privata tra le reti regionali.

## <a name="high-availability-and-disaster-recovery-with-hsr-cost-optimized"></a>Disponibilità elevata e ripristino di emergenza con HSR (costo ottimizzato)
 
 Questa topologia supporta due nodi per la configurazione della replica di sistema HANA per la disponibilità elevata delle aree locali. Per il ripristino di emergenza, il terzo nodo nell'area di ripristino di emergenza viene sincronizzato con il sito primario usando HSR (modalità asincrona), mentre un'altra istanza (ad esempio, QA) è già in esecuzione nel nodo di ripristino di emergenza. 

### <a name="architecture-diagram"></a>Diagramma dell'architettura  

![Disponibilità elevata e ripristino di emergenza con HSR (costo ottimizzato)](media/hana-supported-scenario/hana-system-replication-dr-cost-optimized-141.png)

### <a name="ethernet"></a>Ethernet
Sono preconfigurate le interfacce di rete seguenti:

| Interfaccia logica NIC | Tipo di SKU | Nome con sistema operativo SUSE | Nome con sistema operativo RHEL | Caso d'uso|
| --- | --- | --- | --- | --- |
| Una | TIPO I | eth0.tenant | eno1.tenant | Da client a HLI/HSR |
| B | TIPO I | eth2.tenant | eno3.tenant | Configurato ma non in uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Da nodo a archiviazione |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurato ma non in uso |
| Una | TIPO II | vlan\<tenantNo> | team0.tenant | Da client a HLI/HSR |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Configurato ma non in uso |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Da nodo a archiviazione |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurato ma non in uso |

### <a name="storage"></a>Archiviazione
I punti di montaggio seguenti sono preconfigurati:

| Mount point (Punto di montaggio) | Caso d'uso | 
| --- | --- |
|**Nel sito primario**|
|/hana/shared/SID | Installazione di HANA per SID di produzione | 
|/hana/data/SID/mnt00001 | Installazione di file di dati per SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di log per il SID di produzione | 
|/hana/logbackups/SID | Log di rollforward per SID di produzione |
|**Nel sito di ripristino di emergenza**|
|/hana/shared/SID | Installazione di HANA per SID di produzione | 
|/hana/data/SID/mnt00001 | Installazione di file di dati per SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di log per il SID di produzione | 
|/hana/logbackups/SID | Log di rollforward per SID di produzione |
|/hana/shared/QA-SID | Installazione di HANA per il SID di QA | 
|/hana/data/QA-SID/mnt00001 | Installazione dei file di dati per il SID QA | 
|/hana/log/QA-SID/mnt00001 | Installazione dei file di log per il SID di QA |
|/hana/logbackups/QA-SID | Log di rollforward per SID QA |

### <a name="key-considerations"></a>Considerazioni essenziali
- /usr/SAP/SID è un collegamento simbolico a /hana/shared/SID.
- Nel sito di ripristino di emergenza: i volumi e i punti di montaggio sono configurati (contrassegnati come "istanza di ripristino di emergenza") per l'installazione dell'istanza di HANA di produzione nell'unità di ripristino di emergenza HLI. 
- Nel sito di ripristino di emergenza: i dati, i backup del log, i log e i volumi condivisi per QA (contrassegnati come "installazione dell'istanza di QA") sono configurati per l'installazione dell'istanza di QA.
- Il nodo del sito primario viene sincronizzato con il nodo ripristino di emergenza tramite la replica di sistema HANA. 
- [Copertura globale](../../../expressroute/expressroute-global-reach.md) viene usato per collegare insieme i circuiti ExpressRoute per creare una rete privata tra le reti regionali.

## <a name="scale-out-with-dr-using-hsr"></a>Scalabilità orizzontale con il ripristino di emergenza con HSR
 
Questa topologia supporta più nodi in una configurazione scale-out con ripristino di emergenza. Questa topologia può essere richiesta con o senza il nodo standby. Il nodo del sito primario viene sincronizzato con il nodo del sito di ripristino di emergenza tramite la replica di sistema HANA (modalità asincrona).


### <a name="architecture-diagram"></a>Diagramma dell'architettura  

[![Scalabilità orizzontale con il ripristino di emergenza con HSR](media/hana-supported-scenario/scale-out-dr-hsr-151.png)](media/hana-supported-scenario/scale-out-dr-hsr-151.png#lightbox)


### <a name="ethernet"></a>Ethernet
Sono preconfigurate le interfacce di rete seguenti:

| Interfaccia logica NIC | Tipo di SKU | Nome con sistema operativo SUSE | Nome con sistema operativo RHEL | Caso d'uso|
| --- | --- | --- | --- | --- |
| Una | TIPO I | eth0.tenant | eno1.tenant | Da client a HLI/HSR |
| B | TIPO I | eth2.tenant | eno3.tenant | Comunicazione da nodo a nodo |
| C | TIPO I | eth1.tenant | eno2.tenant | Da nodo a archiviazione |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurato ma non in uso |
| Una | TIPO II | vlan\<tenantNo> | team0.tenant | Da client a HLI/HSR |
| B | TIPO II | vlan\<tenantNo+2> | team0.tenant+2 | Comunicazione da nodo a nodo |
| C | TIPO II | vlan\<tenantNo+1> | team0.tenant+1 | Da nodo a archiviazione |
| D | TIPO II | vlan\<tenantNo+3> | team0.tenant+3 | Configurato ma non in uso |

### <a name="storage"></a>Archiviazione
I punti di montaggio seguenti sono preconfigurati:

| Mount point (Punto di montaggio) | Caso d'uso | 
| --- | --- |
|**Nel nodo primario**|
|/hana/shared | Installazione di HANA per SID di produzione | 
|/hana/data/SID/mnt00001 | Installazione di file di dati per SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di log per il SID di produzione | 
|/hana/logbackups/SID | Log di rollforward per SID di produzione |
|**Nel nodo ripristino di emergenza**|
|/hana/shared | Installazione di HANA per SID di produzione | 
|/hana/data/SID/mnt00001 | Installazione di file di dati per SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di log per il SID di produzione | 
|/hana/logbackups/SID | Log di rollforward per SID di produzione |


### <a name="key-considerations"></a>Considerazioni essenziali
- /usr/SAP/SID è un collegamento simbolico a /hana/shared/SID.
- Nel sito di ripristino di emergenza: i volumi e i punti di montaggio sono configurati per l'installazione dell'istanza di HANA di produzione nell'unità di ripristino di emergenza HLI. 
- Il nodo del sito primario viene sincronizzato con il nodo ripristino di emergenza tramite la replica di sistema HANA. 
- [Copertura globale](../../../expressroute/expressroute-global-reach.md) viene usato per collegare insieme i circuiti ExpressRoute per creare una rete privata tra le reti regionali.


## <a name="next-steps"></a>Passaggi successivi
- [Infrastruttura e connettività per le](./hana-overview-infrastructure-connectivity.md) istanze large di Hana
- [Disponibilità elevata e ripristino di emergenza](./hana-overview-high-availability-disaster-recovery.md) per istanze large di Hana
