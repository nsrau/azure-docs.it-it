---
title: Scenari supportati per SAP HANA in Azure (istanze di grandi dimensioni) Documenti Microsoft
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
ms.openlocfilehash: 019f462d4264d19bcc4806d91223029a95f9d819
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617172"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>Scenari supportati nelle istanze Large di HANA
In questo articolo vengono descritti gli scenari supportati e i dettagli dell'architettura per le istanze di grandi dimensioni HANA (HLI).

>[!NOTE]
>Se lo scenario richiesto non è menzionato in questo articolo, contattare il team di gestione dei servizi Microsoft per valutare i requisiti.
Prima di configurare l'unità HLI, convalidare la progettazione con SAP o il partner di implementazione del servizio.

## <a name="terms-and-definitions"></a>Termini e definizioni
Cerchiamo di capire i termini e le definizioni che vengono utilizzati in questo articolo:

- **SID**: Un identificatore di sistema per il sistema HANA
- **HLI**: Istanze Hana grandi
- **DR**: Ripristino di emergenza
- **Ripristino di emergenza normale**: Una configurazione di sistema con una risorsa dedicata solo a scopi di ripristino di emergenza
- **ripristino di emergenza multiuso**: un sistema del sito di ripristino di emergenza configurato per l'utilizzo di un ambiente non di produzione insieme a un'istanza di produzione configurata per un evento di ripristino di emergenza 
- **Single-SID**: Un sistema con un'istanza installata
- **Multi-SID**: Un sistema con più istanze configurate; chiamato anche ambiente MCOS
- **HSR**: Replica del sistema SAP HANA

## <a name="overview"></a>Panoramica
HANA Large Instances supporta un'ampia gamma di architetture che consentono di soddisfare i requisiti aziendali. Nelle sezioni seguenti vengono illustrati gli scenari architetturali e i relativi dettagli di configurazione. 

La progettazione dell'architettura derivata è puramente dal punto di vista dell'infrastruttura ed è necessario consultare SAP o i partner di implementazione per la distribuzione HANA. Se gli scenari non sono elencati in questo articolo, contattare il team di account Microsoft per esaminare l'architettura e derivare una soluzione per l'utente.

> [!NOTE]
> Queste architetture sono completamente conformi alla progettazione TDI (Tailored Data Integration) e supportate da SAP.

In questo articolo vengono descritti i dettagli dei due componenti in ogni architettura supportata:

- Ethernet
- Archiviazione

### <a name="ethernet"></a>Ethernet

Ogni server di cui è stato eseguito il provisioning è preconfigurato con set di interfacce Ethernet. Le interfacce Ethernet configurate su ciascuna unità HLI sono suddivise in quattro tipi:

- **R**: Utilizzato per o per accesso client.
- **B**: Utilizzato per la comunicazione da nodo a nodo. Questa interfaccia viene configurata in tutti i server (indipendentemente dalla topologia richiesta) ma viene utilizzata solo per gli scenari di scalabilità orizzontale.
- **C:** Utilizzato per la connettività da nodo a archivio.
- **D:** Utilizzato per la connessione da nodo a dispositivo iSCSI per la configurazione STONITH. Questa interfaccia viene configurata solo quando viene richiesta un'installazione HSR.  

| Interfaccia logica NIC | Tipo di SKU | Nome con sistema operativo SUSE | Nome con sistema operativo RHEL | Caso d'uso|
| --- | --- | --- | --- | --- |
| Una  | TIPO I | eth0.tenant | eno1.tenant | Da client a HLI |
| b | TIPO I | eth2.tenant | eno3.tenant | Da nodo a nodo|
| C | TIPO I | eth1.tenant | eno2.tenant | Da nodo a archiviazioneNode-to-storage |
| D | TIPO I | eth4.tenant | eno4.tenant | STONITH |
| Una  | TIPO II | vlan\<tenantNo> | team0.tenant | Da client a HLI |
| b | TIPO II | vlan\<tenantNo-2> | team0.tenant+2 | Da nodo a nodo|
| C | TIPO II | vlan\<tenantNo1> | team0.tenant+1 | Da nodo a archiviazioneNode-to-storage |
| D | TIPO II | > vlan\<di tenantNo | team0.tenant+3 | STONITH |

Scegliere l'interfaccia in base alla topologia configurata sull'unità HLI. Ad esempio, l'interfaccia "B" è impostata per la comunicazione da nodo a nodo, che è utile quando è configurata una topologia con scalabilità orizzontale. Questa interfaccia non viene utilizzata per le configurazioni a nodo singolo e scalabilità verticale. Per altre informazioni sull'utilizzo dell'interfaccia, esaminare gli scenari necessari (più avanti in questo articolo). 

Se necessario, è possibile definire schede NIC aggiuntive da soli. Tuttavia, le configurazioni delle schede di interfaccia di rete esistenti *non possono* essere modificate.

>[!NOTE]
>È possibile trovare interfacce aggiuntive che sono interfacce fisiche o incollaggio. È necessario considerare solo le interfacce menzionate in precedenza per il caso d'uso. Tutti gli altri possono essere ignorati.

La distribuzione per le unità con due indirizzi IP assegnati dovrebbe essere simile alla seguente:The distribution for units with two assigned IP addresses should look like:

- Ethernet "A" deve avere un indirizzo IP assegnato all'interno dell'intervallo di indirizzi del pool IP del server inviato a Microsoft. Questo indirizzo IP deve essere mantenuto nella directory */etc/hosts* del sistema operativo.

- Ethernet "C" deve avere un indirizzo IP assegnato che viene utilizzato per la comunicazione con NFS. Non *è* necessario mantenere questo indirizzo nella directory *etc/hosts* per consentire il traffico da istanza a istanza all'interno del tenant.

Per la replica di sistema HANA o la distribuzione con scalabilità orizzontale HANA, una configurazione blade con due indirizzi IP assegnati non è adatta. Se sono stati assegnati solo due indirizzi IP e si vuole distribuire una configurazione di questo tipo, contattare SAP HANA in Gestione servizi di Azure.If you have only two assigned IP addresses and you want to deploy such a configuration, contact SAP HANA on Azure Service Management. Possono assegnarti un terzo indirizzo IP in una terza VLAN. Per le unità HANA Large Instances con tre indirizzi IP assegnati su tre porte NIC, si applicano le regole di utilizzo seguenti:

- Ethernet "A" deve avere un indirizzo IP assegnato non rientra nell'intervallo di indirizzi del pool IP del server inviato a Microsoft. Questo indirizzo IP non deve essere mantenuto nella directory *etc/hosts* del sistema operativo.

- Ethernet "B" deve essere gestito esclusivamente nella directory *etc/hosts* per la comunicazione tra le varie istanze. Questi sono gli indirizzi IP da gestire nelle configurazioni HANA con scalabilità orizzontale come gli indirizzi IP utilizzati da HANA per la configurazione tra nodi.

- Ethernet "C" deve avere un indirizzo IP assegnato che viene utilizzato per la comunicazione con l'archiviazione NFS. Questo tipo di indirizzo non deve essere mantenuto nella directory *etc/hosts.*

- Ethernet "D" deve essere utilizzato esclusivamente per l'accesso ai dispositivi STONITH per Pacemaker. Questa interfaccia è necessaria quando si configura la replica di sistema HANA e si desidera ottenere il failover automatico del sistema operativo utilizzando un dispositivo basato su SBD.


### <a name="storage"></a>Archiviazione
L'archiviazione è preconfigurata in base alla topologia richiesta. Le dimensioni del volume e i punti di montaggio variano a seconda del numero di server, del numero di SKU e della topologia configurata. Per altre informazioni, esaminare gli scenari necessari (più avanti in questo articolo). Se è necessario più spazio di archiviazione, è possibile acquistarlo con incrementi di 1 TB.

>[!NOTE]
>Il punto di montaggio\</usr/sap/ SID> è un collegamento simbolico al punto di montaggio /hana/shared.


## <a name="supported-scenarios"></a>Scenari supportati

I diagrammi dell'architettura nelle sezioni successive utilizzano le notazioni seguenti:The architecture diagrams in the next sections use the following notations:

[![Tabella dei diagrammi dell'architettura](media/hana-supported-scenario/Legends.png)](media/hana-supported-scenario/Legends.png#lightbox)

Di seguito sono riportati gli scenari supportati:Here are the supported scenarios:

* Nodo singolo con un SID
* Nodo singolo MCOS
* Nodo singolo con DR (normale)
* Singolo nodo con DR (multiuso)
* HSR con STONITH
* HSR con DR (normale/multiuso) 
* Failover automatico dell'host (1+1) 
* Scale-out con standby
* Scale-out senza standby
* Scale-out con ripristino di emergenza

## <a name="single-node-with-one-sid"></a>Nodo singolo con un SID

Questa topologia supporta un nodo in una configurazione con scalabilità verticale con un SID.

### <a name="architecture-diagram"></a>Diagramma dell'architettura  

![Nodo singolo con un SID](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Ethernet
Sono preconfigurate le interfacce di rete seguenti:

| Interfaccia logica NIC | Tipo di SKU | Nome con sistema operativo SUSE | Nome con sistema operativo RHEL | Caso d'uso|
| --- | --- | --- | --- | --- |
| Una  | TIPO I | eth0.tenant | eno1.tenant | Da client a HLI |
| b | TIPO I | eth2.tenant | eno3.tenant | Configurato ma non in uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Da nodo a archiviazioneNode-to-storage |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurato ma non in uso |
| Una  | TIPO II | vlan\<tenantNo> | team0.tenant | Da client a HLI |
| b | TIPO II | vlan\<tenantNo-2> | team0.tenant+2 | Configurato ma non in uso |
| C | TIPO II | vlan\<tenantNo1> | team0.tenant+1 | Da nodo a archiviazioneNode-to-storage |
| D | TIPO II | > vlan\<di tenantNo | team0.tenant+3 | Configurato ma non in uso |

### <a name="storage"></a>Archiviazione
I seguenti punti di montaggio sono preconfigurati:

| Mount point (Punto di montaggio) | Caso d'uso | 
| --- | --- |
|/hana/shared/SID | Installazione HANA | 
|/hana/data/SID/mnt00001 | Installazione dei file di dati | 
|/hana/log/SID/mnt00001 | Installazione dei file di registro | 
|/hana/logbackups/SID | Log di rollforward |

### <a name="key-considerations"></a>Considerazioni sulle chiavi
- /usr/SAP/SID è un collegamento simbolico a /hana/shared/SID.

## <a name="single-node-mcos"></a>Nodo singolo MCOS

Questa topologia supporta un nodo in una configurazione con scalabilità verticale con più SID.

### <a name="architecture-diagram"></a>Diagramma dell'architettura  

![Nodo singolo MCOS](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Ethernet
Sono preconfigurate le interfacce di rete seguenti:

| Interfaccia logica NIC | Tipo di SKU | Nome con sistema operativo SUSE | Nome con sistema operativo RHEL | Caso d'uso|
| --- | --- | --- | --- | --- |
| Una  | TIPO I | eth0.tenant | eno1.tenant | Da client a HLI |
| b | TIPO I | eth2.tenant | eno3.tenant | Configurato ma non in uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Da nodo a archiviazioneNode-to-storage |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurato ma non in uso |
| Una  | TIPO II | vlan\<tenantNo> | team0.tenant | Da client a HLI |
| b | TIPO II | vlan\<tenantNo-2> | team0.tenant+2 | Configurato ma non in uso |
| C | TIPO II | vlan\<tenantNo1> | team0.tenant+1 | Da nodo a archiviazioneNode-to-storage |
| D | TIPO II | > vlan\<di tenantNo | team0.tenant+3 | Configurato ma non in uso |

### <a name="storage"></a>Archiviazione
I seguenti punti di montaggio sono preconfigurati:

| Mount point (Punto di montaggio) | Caso d'uso | 
| --- | --- |
|/hana/shared/SID1 | Installazione HANA per SID1 | 
|/hana/data/SID1/mnt00001 | Installazione dei file di dati per SID1 | 
|/hana/log/SID1/mnt00001 | Installazione dei file di registro per SID1 | 
|/hana/logbackups/SID1 | Log di rollforward per SID1 |
|/hana/shared/SID2 | Installazione HANA per SID2 | 
|/hana/data/SID2/mnt00001 | Installazione dei file di dati per SID2 | 
|/hana/log/SID2/mnt00001 | Installazione dei file di registro per SID2 | 
|/hana/logbackups/SID2 | Log di rollforward per SID2 |

### <a name="key-considerations"></a>Considerazioni sulle chiavi
- /usr/SAP/SID è un collegamento simbolico a /hana/shared/SID.
- La distribuzione delle dimensioni del volume si basa sulle dimensioni del database in memoria. Per informazioni sulle dimensioni dei database in memoria in un ambiente multiSID, vedere [Panoramica e architettura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).

## <a name="single-node-with-dr-using-storage-replication"></a>Singolo nodo con ripristino di emergenza tramite la replica di archiviazioneSingle node with DR using storage replication
 
Questa topologia supporta un nodo in una configurazione con scalabilità verticale con uno o più SID, con replica basata sull'archiviazione nel sito di ripristino di emergenza per un SID primario. Nel diagramma, solo un sistema a SID singolo è rappresentato nel sito primario, ma sono supportati anche i sistemi MCOS.

### <a name="architecture-diagram"></a>Diagramma dell'architettura  

![Singolo nodo con ripristino di emergenza tramite la replica di archiviazioneSingle node with DR using storage replication](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Ethernet
Sono preconfigurate le interfacce di rete seguenti:

| Interfaccia logica NIC | Tipo di SKU | Nome con sistema operativo SUSE | Nome con sistema operativo RHEL | Caso d'uso|
| --- | --- | --- | --- | --- |
| Una  | TIPO I | eth0.tenant | eno1.tenant | Da client a HLI |
| b | TIPO I | eth2.tenant | eno3.tenant | Configurato ma non in uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Da nodo a archiviazioneNode-to-storage |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurato ma non in uso |
| Una  | TIPO II | vlan\<tenantNo> | team0.tenant | Da client a HLI |
| b | TIPO II | vlan\<tenantNo-2> | team0.tenant+2 | Configurato ma non in uso |
| C | TIPO II | vlan\<tenantNo1> | team0.tenant+1 | Da nodo a archiviazioneNode-to-storage |
| D | TIPO II | > vlan\<di tenantNo | team0.tenant+3 | Configurato ma non in uso |

### <a name="storage"></a>Archiviazione
I seguenti punti di montaggio sono preconfigurati:

| Mount point (Punto di montaggio) | Caso d'uso | 
| --- | --- |
|/hana/shared/SID | Installazione HANA per SID | 
|/hana/data/SID/mnt00001 | Installazione dei file di dati per SID | 
|/hana/log/SID/mnt00001 | Installazione dei file di registro per il SID | 
|/hana/logbackups/SID | Log di rollforward per SID |


### <a name="key-considerations"></a>Considerazioni sulle chiavi
- /usr/SAP/SID è un collegamento simbolico a /hana/shared/SID.
- Per MCOS: la distribuzione delle dimensioni del volume si basa sulle dimensioni del database in memoria. Per informazioni sulle dimensioni dei database in memoria in un ambiente multiSID, vedere [Panoramica e architettura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).
- Nel sito di ripristino di emergenza: i volumi e i punti di montaggio sono configurati (contrassegnati come "Obbligatorio per l'installazione HANA") per l'installazione dell'istanza HANA di produzione nell'unità HLI di ripristino di emergenza. 
- Nel sito di ripristino di emergenza: i dati, i backup del log e i volumi condivisi (contrassegnati come "Replica archiviazione") vengono replicati tramite snapshot dal sito di produzione. Questi volumi vengono montati solo durante il failover. Per ulteriori informazioni, vedere Procedura di failover del ripristino di [emergenza](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery).
- Il volume di avvio per la *classe SKU di tipo I* viene replicato nel nodo di ripristino di emergenza.


## <a name="single-node-with-dr-multipurpose-using-storage-replication"></a>Singolo nodo con ripristino di emergenza (multiuso) tramite la replica di archiviazioneSingle node with DR (multipurpose) using storage replication
 
Questa topologia supporta un nodo in una configurazione con scalabilità verticale con uno o più SID, con replica basata sull'archiviazione nel sito di ripristino di emergenza per un SID primario. Nel diagramma, solo un sistema a SID singolo è rappresentato nel sito primario, ma sono supportati anche i sistemi multi-SID (MCOS). Nel sito di ripristino di emergenza, l'unità HLI viene utilizzata per l'istanza di controllo qualità durante l'esecuzione delle operazioni di produzione dal sito primario. Durante il failover di ripristino di emergenza (o test di failover), l'istanza di Controllo di accesso generale nel sito di ripristino di emergenza viene eliminata.

### <a name="architecture-diagram"></a>Diagramma dell'architettura  

![Singolo nodo con ripristino di emergenza (multiuso) tramite la replica di archiviazioneSingle node with DR (multipurpose) using storage replication](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Ethernet
Sono preconfigurate le interfacce di rete seguenti:

| Interfaccia logica NIC | Tipo di SKU | Nome con sistema operativo SUSE | Nome con sistema operativo RHEL | Caso d'uso|
| --- | --- | --- | --- | --- |
| Una  | TIPO I | eth0.tenant | eno1.tenant | Da client a HLI |
| b | TIPO I | eth2.tenant | eno3.tenant | Configurato ma non in uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Da nodo a archiviazioneNode-to-storage |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurato ma non in uso |
| Una  | TIPO II | vlan\<tenantNo> | team0.tenant | Da client a HLI |
| b | TIPO II | vlan\<tenantNo-2> | team0.tenant+2 | Configurato ma non in uso |
| C | TIPO II | vlan\<tenantNo1> | team0.tenant+1 | Da nodo a archiviazioneNode-to-storage |
| D | TIPO II | > vlan\<di tenantNo | team0.tenant+3 | Configurato ma non in uso |

### <a name="storage"></a>Archiviazione
I seguenti punti di montaggio sono preconfigurati:

| Mount point (Punto di montaggio) | Caso d'uso | 
| --- | --- |
|**Nel sito primario**|
|/hana/shared/SID | Installazione HANA per siD di produzione | 
|/hana/data/SID/mnt00001 | Installazione dei file di dati per il SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di registro per il SID di produzione | 
|/hana/logbackups/SID | Log di rollforward per SID di produzione |
|**Nel sito di ripristino di emergenza**|
|/hana/shared/SID | Installazione HANA per siD di produzione | 
|/hana/data/SID/mnt00001 | Installazione dei file di dati per il SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di registro per il SID di produzione | 
|/hana/shared/QA-SID | Installazione HANA per Il SiD QA | 
|/hana/data/QA-SID/mnt00001 | Installazione dei file di dati per il SID QA | 
|/hana/log/QA-SID/mnt00001 | Installazione dei file di registro per il SID QA |
|/hana/logbackups/QA-SID | Log di rollforward per SID QA |

### <a name="key-considerations"></a>Considerazioni sulle chiavi
- /usr/SAP/SID è un collegamento simbolico a /hana/shared/SID.
- Per MCOS: la distribuzione delle dimensioni del volume si basa sulle dimensioni del database in memoria. Per informazioni sulle dimensioni dei database in memoria in un ambiente multiSID, vedere [Panoramica e architettura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).
- Nel sito di ripristino di emergenza: i volumi e i punti di montaggio sono configurati (contrassegnati come "Obbligatorio per l'installazione HANA") per l'installazione dell'istanza HANA di produzione nell'unità HLI di ripristino di emergenza. 
- Nel sito di ripristino di emergenza: i dati, i backup del log e i volumi condivisi (contrassegnati come "Replica archiviazione") vengono replicati tramite snapshot dal sito di produzione. Questi volumi vengono montati solo durante il failover. Per ulteriori informazioni, vedere Procedura di failover del ripristino di [emergenza](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery). 
- Nel sito di ripristino di emergenza: i dati, i backup del log, il log e i volumi condivisi per il controllo qualità (contrassegnati come "installazione dell'istanza QA") sono configurati per l'installazione dell'istanza QA.
- Il volume di avvio per la *classe SKU di tipo I* viene replicato nel nodo di ripristino di emergenza.

## <a name="hsr-with-stonith-for-high-availability"></a>HSR con STONITH per un'elevata disponibilità
 
Questa topologia supporta due nodi per la configurazione di replica di sistema HANA. Questa configurazione è supportata solo per singole istanze HANA in un nodo. Ciò significa che gli scenari MCOS *non* sono supportati.

> [!NOTE]
> A partire da dicembre 2019, questa architettura è supportata solo per il sistema operativo SUSE.


### <a name="architecture-diagram"></a>Diagramma dell'architettura  

![HSR con STONITH per un'elevata disponibilità](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Ethernet
Sono preconfigurate le interfacce di rete seguenti:

| Interfaccia logica NIC | Tipo di SKU | Nome con sistema operativo SUSE | Nome con sistema operativo RHEL | Caso d'uso|
| --- | --- | --- | --- | --- |
| Una  | TIPO I | eth0.tenant | eno1.tenant | Da client a HLI |
| b | TIPO I | eth2.tenant | eno3.tenant | Configurato ma non in uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Da nodo a archiviazioneNode-to-storage |
| D | TIPO I | eth4.tenant | eno4.tenant | Usato per STONITH |
| Una  | TIPO II | vlan\<tenantNo> | team0.tenant | Da client a HLI |
| b | TIPO II | vlan\<tenantNo-2> | team0.tenant+2 | Configurato ma non in uso |
| C | TIPO II | vlan\<tenantNo1> | team0.tenant+1 | Da nodo a archiviazioneNode-to-storage |
| D | TIPO II | > vlan\<di tenantNo | team0.tenant+3 | Usato per STONITH |

### <a name="storage"></a>Archiviazione
I seguenti punti di montaggio sono preconfigurati:

| Mount point (Punto di montaggio) | Caso d'uso | 
| --- | --- |
|**Nel nodo primario**|
|/hana/shared/SID | Installazione HANA per siD di produzione | 
|/hana/data/SID/mnt00001 | Installazione dei file di dati per il SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di registro per il SID di produzione | 
|/hana/logbackups/SID | Log di rollforward per SID di produzione |
|**Nel nodo secondario**|
|/hana/shared/SID | Installazione HANA per SID secondario | 
|/hana/data/SID/mnt00001 | Installazione dei file di dati per il SID secondario | 
|/hana/log/SID/mnt00001 | Installazione dei file di registro per il SID secondario | 
|/hana/logbackups/SID | Log di rollforward per SID secondario |

### <a name="key-considerations"></a>Considerazioni sulle chiavi
- /usr/SAP/SID è un collegamento simbolico a /hana/shared/SID.
- Per MCOS: la distribuzione delle dimensioni del volume si basa sulle dimensioni del database in memoria. Per informazioni sulle dimensioni dei database in memoria in un ambiente multiSID, vedere [Panoramica e architettura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).
- STONITH: per l'installazione STONITH è configurato un SBD. Tuttavia, l'uso di STONITH è facoltativo.


## <a name="high-availability-with-hsr-and-dr-with-storage-replication"></a>Disponibilità elevata con HSR e ripristino di emergenza con replica di archiviazioneHigh availability with HSR and DR with storage replication
 
Questa topologia supporta due nodi per la configurazione di replica di sistema HANA. Sono supportati sia i DR normali che i RE multiuso. Queste configurazioni sono supportate solo per singole istanze HANA in un nodo. Ciò significa che gli scenari MCOS *non* sono supportati con queste configurazioni.

Nel diagramma, uno scenario multiuso viene illustrato nel sito di ripristino di emergenza, in cui l'unità HLI viene utilizzata per l'istanza di controllo qualità durante l'esecuzione delle operazioni di produzione dal sito primario. Durante il failover di ripristino di emergenza (o test di failover), l'istanza di Controllo di accesso generale nel sito di ripristino di emergenza viene eliminata. 

### <a name="architecture-diagram"></a>Diagramma dell'architettura  

![Disponibilità elevata con HSR e ripristino di emergenza con replica di archiviazioneHigh availability with HSR and DR with storage replication](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Ethernet
Sono preconfigurate le interfacce di rete seguenti:

| Interfaccia logica NIC | Tipo di SKU | Nome con sistema operativo SUSE | Nome con sistema operativo RHEL | Caso d'uso|
| --- | --- | --- | --- | --- |
| Una  | TIPO I | eth0.tenant | eno1.tenant | Da client a HLI |
| b | TIPO I | eth2.tenant | eno3.tenant | Configurato ma non in uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Da nodo a archiviazioneNode-to-storage |
| D | TIPO I | eth4.tenant | eno4.tenant | Usato per STONITH |
| Una  | TIPO II | vlan\<tenantNo> | team0.tenant | Da client a HLI |
| b | TIPO II | vlan\<tenantNo-2> | team0.tenant+2 | Configurato ma non in uso |
| C | TIPO II | vlan\<tenantNo1> | team0.tenant+1 | Da nodo a archiviazioneNode-to-storage |
| D | TIPO II | > vlan\<di tenantNo | team0.tenant+3 | Usato per STONITH |

### <a name="storage"></a>Archiviazione
I seguenti punti di montaggio sono preconfigurati:

| Mount point (Punto di montaggio) | Caso d'uso | 
| --- | --- |
|**Nel nodo primario del sito primario**|
|/hana/shared/SID | Installazione HANA per siD di produzione | 
|/hana/data/SID/mnt00001 | Installazione dei file di dati per il SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di registro per il SID di produzione | 
|/hana/logbackups/SID | Log di rollforward per SID di produzione |
|**Nel nodo secondario del sito primario**|
|/hana/shared/SID | Installazione HANA per SID secondario | 
|/hana/data/SID/mnt00001 | Installazione dei file di dati per il SID secondario | 
|/hana/log/SID/mnt00001 | Installazione dei file di registro per il SID secondario | 
|/hana/logbackups/SID | Log di rollforward per SID secondario |
|**Nel sito di ripristino di emergenza**|
|/hana/shared/SID | Installazione HANA per siD di produzione | 
|/hana/data/SID/mnt00001 | Installazione dei file di dati per il SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di registro per il SID di produzione | 
|/hana/shared/QA-SID | Installazione HANA per Il SiD QA | 
|/hana/data/QA-SID/mnt00001 | Installazione dei file di dati per il SID QA | 
|/hana/log/QA-SID/mnt00001 | Installazione dei file di registro per il SID QA |
|/hana/logbackups/QA-SID | Log di rollforward per SID QA |

### <a name="key-considerations"></a>Considerazioni sulle chiavi
- /usr/SAP/SID è un collegamento simbolico a /hana/shared/SID.
- Per MCOS: la distribuzione delle dimensioni del volume si basa sulle dimensioni del database in memoria. Per informazioni sulle dimensioni dei database in memoria in un ambiente multiSID, vedere [Panoramica e architettura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).
- STONITH: per l'installazione STONITH è configurato un SBD. Tuttavia, l'uso di STONITH è facoltativo.
- Nel sito di ripristino di emergenza: per la replica del nodo primario e secondario *sono necessari due set di volumi di archiviazione.*
- Nel sito di ripristino di emergenza: i volumi e i punti di montaggio sono configurati (contrassegnati come "Obbligatorio per l'installazione HANA") per l'installazione dell'istanza HANA di produzione nell'unità HLI di ripristino di emergenza. 
- Nel sito di ripristino di emergenza: i dati, i backup del log e i volumi condivisi (contrassegnati come "Replica archiviazione") vengono replicati tramite snapshot dal sito di produzione. Questi volumi vengono montati solo durante il failover. Per ulteriori informazioni, vedere Procedura di failover del ripristino di [emergenza](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery). 
- Nel sito di ripristino di emergenza: i dati, i backup del log, il log e i volumi condivisi per il controllo qualità (contrassegnati come "installazione dell'istanza QA") sono configurati per l'installazione dell'istanza QA.
- Il volume di avvio per la *classe SKU di tipo I* viene replicato nel nodo di ripristino di emergenza.


## <a name="host-auto-failover-11"></a>Failover automatico dell'host (1+1)
 
Questa topologia supporta due nodi in una configurazione di failover automatico dell'host. Esiste un nodo con un ruolo master/lavoratore e un altro come standby. *SAP supporta questo scenario solo per S/4 HANA.* Per ulteriori informazioni, vedere [OSS nota 2408419 - SAP S/4HANA - Supporto multi-nodo](https://launchpad.support.sap.com/#/notes/2408419).



### <a name="architecture-diagram"></a>Diagramma dell'architettura  

![Failover automatico dell'host (1+1)](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Ethernet
Sono preconfigurate le interfacce di rete seguenti:

| Interfaccia logica NIC | Tipo di SKU | Nome con sistema operativo SUSE | Nome con sistema operativo RHEL | Caso d'uso|
| --- | --- | --- | --- | --- |
| Una  | TIPO I | eth0.tenant | eno1.tenant | Da client a HLI |
| b | TIPO I | eth2.tenant | eno3.tenant | Comunicazione da nodo a nodo |
| C | TIPO I | eth1.tenant | eno2.tenant | Da nodo a archiviazioneNode-to-storage |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurato ma non in uso |
| Una  | TIPO II | vlan\<tenantNo> | team0.tenant | Da client a HLI |
| b | TIPO II | vlan\<tenantNo-2> | team0.tenant+2 | Comunicazione da nodo a nodo |
| C | TIPO II | vlan\<tenantNo1> | team0.tenant+1 | Da nodo a archiviazioneNode-to-storage |
| D | TIPO II | > vlan\<di tenantNo | team0.tenant+3 | Configurato ma non in uso |

### <a name="storage"></a>Archiviazione
I seguenti punti di montaggio sono preconfigurati:

| Mount point (Punto di montaggio) | Caso d'uso | 
| --- | --- |
|**Nei nodi master e standby**|
|/hana/shared | Installazione HANA per siD di produzione | 
|/hana/data/SID/mnt00001 | Installazione dei file di dati per il SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di registro per il SID di produzione | 
|/hana/logbackups/SID | Log di rollforward per SID di produzione |



### <a name="key-considerations"></a>Considerazioni sulle chiavi
- /usr/SAP/SID è un collegamento simbolico a /hana/shared/SID.
- In standby: i volumi e i punti di montaggio sono configurati (contrassegnati come "Obbligatorio per l'installazione HANA") per l'installazione dell'istanza HANA nell'unità di standby.
 

## <a name="scale-out-with-standby"></a>Scale-out con standby
 
Questa topologia supporta più nodi in una configurazione scale-out. È presente un nodo con un ruolo master, uno o più nodi con un ruolo di lavoro e uno o più nodi come standby. Tuttavia, può essere presente un solo nodo master in un singolo punto nel tempo.


### <a name="architecture-diagram"></a>Diagramma dell'architettura  

![Scale-out con standby](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Ethernet
Sono preconfigurate le interfacce di rete seguenti:

| Interfaccia logica NIC | Tipo di SKU | Nome con sistema operativo SUSE | Nome con sistema operativo RHEL | Caso d'uso|
| --- | --- | --- | --- | --- |
| Una  | TIPO I | eth0.tenant | eno1.tenant | Da client a HLI |
| b | TIPO I | eth2.tenant | eno3.tenant | Comunicazione da nodo a nodo |
| C | TIPO I | eth1.tenant | eno2.tenant | Da nodo a archiviazioneNode-to-storage |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurato ma non in uso |
| Una  | TIPO II | vlan\<tenantNo> | team0.tenant | Da client a HLI |
| b | TIPO II | vlan\<tenantNo-2> | team0.tenant+2 | Comunicazione da nodo a nodo |
| C | TIPO II | vlan\<tenantNo1> | team0.tenant+1 | Da nodo a archiviazioneNode-to-storage |
| D | TIPO II | > vlan\<di tenantNo | team0.tenant+3 | Configurato ma non in uso |

### <a name="storage"></a>Archiviazione
I seguenti punti di montaggio sono preconfigurati:

| Mount point (Punto di montaggio) | Caso d'uso | 
| --- | --- |
|**Nei nodi master, di lavoro e standby**|
|/hana/shared | Installazione HANA per siD di produzione | 
|/hana/data/SID/mnt00001 | Installazione dei file di dati per il SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di registro per il SID di produzione | 
|/hana/logbackups/SID | Log di rollforward per SID di produzione |


## <a name="scale-out-without-standby"></a>Scale-out senza standby
 
Questa topologia supporta più nodi in una configurazione scale-out. È presente un nodo con un ruolo master e uno o più nodi con un ruolo di lavoro. Tuttavia, può essere presente un solo nodo master in un singolo punto nel tempo.


### <a name="architecture-diagram"></a>Diagramma dell'architettura  

![Scale-out senza standby](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Ethernet
Sono preconfigurate le interfacce di rete seguenti:

| Interfaccia logica NIC | Tipo di SKU | Nome con sistema operativo SUSE | Nome con sistema operativo RHEL | Caso d'uso|
| --- | --- | --- | --- | --- |
| Una  | TIPO I | eth0.tenant | eno1.tenant | Da client a HLI |
| b | TIPO I | eth2.tenant | eno3.tenant | Comunicazione da nodo a nodo |
| C | TIPO I | eth1.tenant | eno2.tenant | Da nodo a archiviazioneNode-to-storage |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurato ma non in uso |
| Una  | TIPO II | vlan\<tenantNo> | team0.tenant | Da client a HLI |
| b | TIPO II | vlan\<tenantNo-2> | team0.tenant+2 | Comunicazione da nodo a nodo |
| C | TIPO II | vlan\<tenantNo1> | team0.tenant+1 | Da nodo a archiviazioneNode-to-storage |
| D | TIPO II | > vlan\<di tenantNo | team0.tenant+3 | Configurato ma non in uso |

### <a name="storage"></a>Archiviazione
I seguenti punti di montaggio sono preconfigurati:

| Mount point (Punto di montaggio) | Caso d'uso | 
| --- | --- |
|**Nei nodi master e di lavoro**|
|/hana/shared | Installazione HANA per siD di produzione | 
|/hana/data/SID/mnt00001 | Installazione dei file di dati per il SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di registro per il SID di produzione | 
|/hana/logbackups/SID | Log di rollforward per SID di produzione |


### <a name="key-considerations"></a>Considerazioni sulle chiavi
- /usr/SAP/SID è un collegamento simbolico a /hana/shared/SID.

## <a name="scale-out-with-dr-using-storage-replication"></a>Scalabilità orizzontale con ripristino di emergenza tramite la replica di archiviazioneScale-out with DR using storage replication
 
Questa topologia supporta più nodi in una configurazione scale-out con ripristino di emergenza. Sono supportati sia i DR normali che i RE multiuso. Nel diagramma è illustrato solo il ripristino di emergenza normale. Questa topologia può essere richiesta con o senza il nodo standby.


### <a name="architecture-diagram"></a>Diagramma dell'architettura  

![Scalabilità orizzontale con ripristino di emergenza tramite la replica di archiviazioneScale-out with DR using storage replication](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Ethernet
Sono preconfigurate le interfacce di rete seguenti:

| Interfaccia logica NIC | Tipo di SKU | Nome con sistema operativo SUSE | Nome con sistema operativo RHEL | Caso d'uso|
| --- | --- | --- | --- | --- |
| Una  | TIPO I | eth0.tenant | eno1.tenant | Da client a HLI |
| b | TIPO I | eth2.tenant | eno3.tenant | Comunicazione da nodo a nodo |
| C | TIPO I | eth1.tenant | eno2.tenant | Da nodo a archiviazioneNode-to-storage |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurato ma non in uso |
| Una  | TIPO II | vlan\<tenantNo> | team0.tenant | Da client a HLI |
| b | TIPO II | vlan\<tenantNo-2> | team0.tenant+2 | Comunicazione da nodo a nodo |
| C | TIPO II | vlan\<tenantNo1> | team0.tenant+1 | Da nodo a archiviazioneNode-to-storage |
| D | TIPO II | > vlan\<di tenantNo | team0.tenant+3 | Configurato ma non in uso |

### <a name="storage"></a>Archiviazione
I seguenti punti di montaggio sono preconfigurati:

| Mount point (Punto di montaggio) | Caso d'uso | 
| --- | --- |
|**Nel nodo primario**|
|/hana/shared | Installazione HANA per siD di produzione | 
|/hana/data/SID/mnt00001 | Installazione dei file di dati per il SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di registro per il SID di produzione | 
|/hana/logbackups/SID | Log di rollforward per SID di produzione |
|**Nel nodo ripristino di emergenza**|
|/hana/shared | Installazione HANA per siD di produzione | 
|/hana/data/SID/mnt00001 | Installazione dei file di dati per il SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di registro per il SID di produzione | 


### <a name="key-considerations"></a>Considerazioni sulle chiavi
- /usr/SAP/SID è un collegamento simbolico a /hana/shared/SID.
-  Nel sito di ripristino di emergenza: i volumi e i punti di montaggio sono configurati (contrassegnati come "Obbligatorio per l'installazione HANA") per l'installazione dell'istanza HANA di produzione nell'unità HLI di ripristino di emergenza. 
- Nel sito di ripristino di emergenza: i dati, i backup del log e i volumi condivisi (contrassegnati come "Replica archiviazione") vengono replicati tramite snapshot dal sito di produzione. Questi volumi vengono montati solo durante il failover. Per ulteriori informazioni, vedere Procedura di failover del ripristino di [emergenza](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery). 
- Il volume di avvio per la *classe SKU di tipo I* viene replicato nel nodo di ripristino di emergenza.


## <a name="single-node-with-dr-using-hsr"></a>Singolo nodo con DR con HSR
 
Questa topologia supporta un nodo in una configurazione con scalabilità verticale con un SID, con replica di sistema HANA al sito di ripristino di emergenza per un SID primario. Nel diagramma, solo un sistema a SID singolo è rappresentato nel sito primario, ma sono supportati anche i sistemi multi-SID (MCOS).

### <a name="architecture-diagram"></a>Diagramma dell'architettura  

![Singolo nodo con DR con HSR](media/hana-supported-scenario/single-node-hsr-dr-111.png)

### <a name="ethernet"></a>Ethernet
Sono preconfigurate le interfacce di rete seguenti:

| Interfaccia logica NIC | Tipo di SKU | Nome con sistema operativo SUSE | Nome con sistema operativo RHEL | Caso d'uso|
| --- | --- | --- | --- | --- |
| Una  | TIPO I | eth0.tenant | eno1.tenant | Da client a HLI/HSR |
| b | TIPO I | eth2.tenant | eno3.tenant | Configurato ma non in uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Da nodo a archiviazioneNode-to-storage |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurato ma non in uso |
| Una  | TIPO II | vlan\<tenantNo> | team0.tenant | Da client a HLI/HSR |
| b | TIPO II | vlan\<tenantNo-2> | team0.tenant+2 | Configurato ma non in uso |
| C | TIPO II | vlan\<tenantNo1> | team0.tenant+1 | Da nodo a archiviazioneNode-to-storage |
| D | TIPO II | > vlan\<di tenantNo | team0.tenant+3 | Configurato ma non in uso |

### <a name="storage"></a>Archiviazione
I seguenti punti di montaggio sono preconfigurati su entrambe le unità HLI (Primarie e DR):

| Mount point (Punto di montaggio) | Caso d'uso | 
| --- | --- |
|/hana/shared/SID | Installazione HANA per SID | 
|/hana/data/SID/mnt00001 | Installazione dei file di dati per SID | 
|/hana/log/SID/mnt00001 | Installazione dei file di registro per il SID | 
|/hana/logbackups/SID | Log di rollforward per SID |


### <a name="key-considerations"></a>Considerazioni sulle chiavi
- /usr/SAP/SID è un collegamento simbolico a /hana/shared/SID.
- Per MCOS: la distribuzione delle dimensioni del volume si basa sulle dimensioni del database in memoria. Per informazioni sulle dimensioni dei database in memoria in un ambiente multiSID, vedere [Panoramica e architettura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).
- Il nodo primario viene sincronizzato con il nodo di ripristino di emergenza utilizzando la replica di sistema HANA. 
- [La copertura globale](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) viene usata per collegare i circuiti ExpressRoute per creare una rete privata tra le reti regionali.



## <a name="single-node-hsr-to-dr-cost-optimized"></a>Singolo nodo da HSR a DR (costo ottimizzato) 
 
 Questa topologia supporta un nodo in una configurazione con scalabilità verticale con un SID, con replica di sistema HANA al sito di ripristino di emergenza per un SID primario. Nel diagramma, solo un sistema a SID singolo è rappresentato nel sito primario, ma sono supportati anche i sistemi multi-SID (MCOS). Nel sito di ripristino di emergenza, un'unità HLI viene utilizzata per l'istanza di controllo qualità durante l'esecuzione delle operazioni di produzione dal sito primario. Durante il failover di ripristino di emergenza (o test di failover), l'istanza di Controllo di accesso generale nel sito di ripristino di emergenza viene eliminata.

### <a name="architecture-diagram"></a>Diagramma dell'architettura  

![Singolo nodo da HSR a DR (costo ottimizzato)](media/hana-supported-scenario/single-node-hsr-dr-cost-optimized-121.png)

### <a name="ethernet"></a>Ethernet
Sono preconfigurate le interfacce di rete seguenti:

| Interfaccia logica NIC | Tipo di SKU | Nome con sistema operativo SUSE | Nome con sistema operativo RHEL | Caso d'uso|
| --- | --- | --- | --- | --- |
| Una  | TIPO I | eth0.tenant | eno1.tenant | Da client a HLI/HSR |
| b | TIPO I | eth2.tenant | eno3.tenant | Configurato ma non in uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Da nodo a archiviazioneNode-to-storage |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurato ma non in uso |
| Una  | TIPO II | vlan\<tenantNo> | team0.tenant | Da client a HLI/HSR |
| b | TIPO II | vlan\<tenantNo-2> | team0.tenant+2 | Configurato ma non in uso |
| C | TIPO II | vlan\<tenantNo1> | team0.tenant+1 | Da nodo a archiviazioneNode-to-storage |
| D | TIPO II | > vlan\<di tenantNo | team0.tenant+3 | Configurato ma non in uso |

### <a name="storage"></a>Archiviazione
I seguenti punti di montaggio sono preconfigurati:

| Mount point (Punto di montaggio) | Caso d'uso | 
| --- | --- |
|**Nel sito primario**|
|/hana/shared/SID | Installazione HANA per siD di produzione | 
|/hana/data/SID/mnt00001 | Installazione dei file di dati per il SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di registro per il SID di produzione | 
|/hana/logbackups/SID | Log di rollforward per SID di produzione |
|**Nel sito di ripristino di emergenza**|
|/hana/shared/SID | Installazione HANA per siD di produzione | 
|/hana/data/SID/mnt00001 | Installazione dei file di dati per il SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di registro per il SID di produzione | 
|/hana/logbackups/SID | Log di rollforward per SID di produzione |
|/hana/shared/QA-SID | Installazione HANA per Il SiD QA | 
|/hana/data/QA-SID/mnt00001 | Installazione dei file di dati per il SID QA | 
|/hana/log/QA-SID/mnt00001 | Installazione dei file di registro per il SID QA |
|/hana/logbackups/QA-SID | Log di rollforward per SID QA |

### <a name="key-considerations"></a>Considerazioni sulle chiavi
- /usr/SAP/SID è un collegamento simbolico a /hana/shared/SID.
- Per MCOS: la distribuzione delle dimensioni del volume si basa sulle dimensioni del database in memoria. Per informazioni sulle dimensioni dei database in memoria in un ambiente multiSID, vedere [Panoramica e architettura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).
- Nel sito di ripristino di emergenza: i volumi e i punti di montaggio sono configurati (contrassegnati come "Istanza PROD nel sito di ripristino di emergenza") per l'installazione dell'istanza HANA di produzione nell'unità RdI di ripristino di emergenza. 
- Nel sito di ripristino di emergenza: i dati, i backup del log, il log e i volumi condivisi per il controllo qualità (contrassegnati come "installazione dell'istanza QA") sono configurati per l'installazione dell'istanza QA.
- Il nodo primario viene sincronizzato con il nodo di ripristino di emergenza utilizzando la replica di sistema HANA. 
- [La copertura globale](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) viene usata per collegare i circuiti ExpressRoute per creare una rete privata tra le reti regionali.

## <a name="high-availability-and-disaster-recovery-with-hsr"></a>Disponibilità elevata e ripristino di emergenza con HSRHigh availability and disaster recovery with HSR 
 
 Questa topologia supporta due nodi per la configurazione di replica di sistema HANA per la disponibilità elevata delle aree locali. Per il ripristino di emergenza, il terzo nodo nell'area di ripristino di emergenza viene sincronizzato con il sito primario utilizzando HSR (modalità asincrona). 

### <a name="architecture-diagram"></a>Diagramma dell'architettura  

![Disponibilità elevata e ripristino di emergenza con HSRHigh availability and disaster recovery with HSR](media/hana-supported-scenario/hana-system-replication-dr-131.png)

### <a name="ethernet"></a>Ethernet
Sono preconfigurate le interfacce di rete seguenti:

| Interfaccia logica NIC | Tipo di SKU | Nome con sistema operativo SUSE | Nome con sistema operativo RHEL | Caso d'uso|
| --- | --- | --- | --- | --- |
| Una  | TIPO I | eth0.tenant | eno1.tenant | Da client a HLI/HSR |
| b | TIPO I | eth2.tenant | eno3.tenant | Configurato ma non in uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Da nodo a archiviazioneNode-to-storage |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurato ma non in uso |
| Una  | TIPO II | vlan\<tenantNo> | team0.tenant | Da client a HLI/HSR |
| b | TIPO II | vlan\<tenantNo-2> | team0.tenant+2 | Configurato ma non in uso |
| C | TIPO II | vlan\<tenantNo1> | team0.tenant+1 | Da nodo a archiviazioneNode-to-storage |
| D | TIPO II | > vlan\<di tenantNo | team0.tenant+3 | Configurato ma non in uso |

### <a name="storage"></a>Archiviazione
I seguenti punti di montaggio sono preconfigurati:

| Mount point (Punto di montaggio) | Caso d'uso | 
| --- | --- |
|**Nel sito primario**|
|/hana/shared/SID | Installazione HANA per siD di produzione | 
|/hana/data/SID/mnt00001 | Installazione dei file di dati per il SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di registro per il SID di produzione | 
|/hana/logbackups/SID | Log di rollforward per SID di produzione |
|**Nel sito di ripristino di emergenza**|
|/hana/shared/SID | Installazione HANA per siD di produzione | 
|/hana/data/SID/mnt00001 | Installazione dei file di dati per il SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di registro per il SID di produzione | 
|/hana/logbackups/SID | Log di rollforward per SID di produzione |


### <a name="key-considerations"></a>Considerazioni sulle chiavi
- /usr/SAP/SID è un collegamento simbolico a /hana/shared/SID.
- Nel sito di ripristino di emergenza: i volumi e i punti di montaggio vengono configurati (contrassegnati come "istanza DI DR PROD") per l'installazione dell'istanza HANA di produzione nell'unità RDI di ripristino di emergenza. 
- Il nodo del sito primario viene sincronizzato con il nodo di ripristino di emergenza utilizzando la replica di sistema HANA. 
- [La copertura globale](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) viene usata per collegare i circuiti ExpressRoute per creare una rete privata tra le reti regionali.

## <a name="high-availability-and-disaster-recovery-with-hsr-cost-optimized"></a>Disponibilità elevata e ripristino di emergenza con HSR (ottimizzazione dei costi)High availability and disaster recovery with HSR (cost optimized)
 
 Questa topologia supporta due nodi per la configurazione di replica di sistema HANA per la disponibilità elevata delle aree locali. Per il ripristino di emergenza, il terzo nodo nell'area di ripristino di emergenza viene sincronizzato con il sito primario utilizzando HSR (modalità asincrona), mentre un'altra istanza (ad esempio, QA) sta già esaurendo dal nodo di ripristino di emergenza. 

### <a name="architecture-diagram"></a>Diagramma dell'architettura  

![Disponibilità elevata e ripristino di emergenza con HSR (ottimizzazione dei costi)High availability and disaster recovery with HSR (cost optimized)](media/hana-supported-scenario/hana-system-replication-dr-cost-optimized-141.png)

### <a name="ethernet"></a>Ethernet
Sono preconfigurate le interfacce di rete seguenti:

| Interfaccia logica NIC | Tipo di SKU | Nome con sistema operativo SUSE | Nome con sistema operativo RHEL | Caso d'uso|
| --- | --- | --- | --- | --- |
| Una  | TIPO I | eth0.tenant | eno1.tenant | Da client a HLI/HSR |
| b | TIPO I | eth2.tenant | eno3.tenant | Configurato ma non in uso |
| C | TIPO I | eth1.tenant | eno2.tenant | Da nodo a archiviazioneNode-to-storage |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurato ma non in uso |
| Una  | TIPO II | vlan\<tenantNo> | team0.tenant | Da client a HLI/HSR |
| b | TIPO II | vlan\<tenantNo-2> | team0.tenant+2 | Configurato ma non in uso |
| C | TIPO II | vlan\<tenantNo1> | team0.tenant+1 | Da nodo a archiviazioneNode-to-storage |
| D | TIPO II | > vlan\<di tenantNo | team0.tenant+3 | Configurato ma non in uso |

### <a name="storage"></a>Archiviazione
I seguenti punti di montaggio sono preconfigurati:

| Mount point (Punto di montaggio) | Caso d'uso | 
| --- | --- |
|**Nel sito primario**|
|/hana/shared/SID | Installazione HANA per siD di produzione | 
|/hana/data/SID/mnt00001 | Installazione dei file di dati per il SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di registro per il SID di produzione | 
|/hana/logbackups/SID | Log di rollforward per SID di produzione |
|**Nel sito di ripristino di emergenza**|
|/hana/shared/SID | Installazione HANA per siD di produzione | 
|/hana/data/SID/mnt00001 | Installazione dei file di dati per il SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di registro per il SID di produzione | 
|/hana/logbackups/SID | Log di rollforward per SID di produzione |
|/hana/shared/QA-SID | Installazione HANA per Il SiD QA | 
|/hana/data/QA-SID/mnt00001 | Installazione dei file di dati per il SID QA | 
|/hana/log/QA-SID/mnt00001 | Installazione dei file di registro per il SID QA |
|/hana/logbackups/QA-SID | Log di rollforward per SID QA |

### <a name="key-considerations"></a>Considerazioni sulle chiavi
- /usr/SAP/SID è un collegamento simbolico a /hana/shared/SID.
- Nel sito di ripristino di emergenza: i volumi e i punti di montaggio vengono configurati (contrassegnati come "istanza DI DR PROD") per l'installazione dell'istanza HANA di produzione nell'unità RDI di ripristino di emergenza. 
- Nel sito di ripristino di emergenza: i dati, i backup del log, il log e i volumi condivisi per il controllo qualità (contrassegnati come "installazione dell'istanza QA") sono configurati per l'installazione dell'istanza QA.
- Il nodo del sito primario viene sincronizzato con il nodo di ripristino di emergenza utilizzando la replica di sistema HANA. 
- [La copertura globale](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) viene usata per collegare i circuiti ExpressRoute per creare una rete privata tra le reti regionali.

## <a name="scale-out-with-dr-using-hsr"></a>Scalabilità orizzontale con DR con HSRScale-out with DR using HSR
 
Questa topologia supporta più nodi in una configurazione scale-out con ripristino di emergenza. Questa topologia può essere richiesta con o senza il nodo standby. Il nodo del sito primario viene sincronizzato con il nodo del sito di ripristino di emergenza utilizzando HANA System Replication (modalità asincrona).


### <a name="architecture-diagram"></a>Diagramma dell'architettura  

[![Scalabilità orizzontale con DR con HSRScale-out with DR using HSR](media/hana-supported-scenario/scale-out-dr-hsr-151.png)](media/hana-supported-scenario/scale-out-dr-hsr-151.png#lightbox)


### <a name="ethernet"></a>Ethernet
Sono preconfigurate le interfacce di rete seguenti:

| Interfaccia logica NIC | Tipo di SKU | Nome con sistema operativo SUSE | Nome con sistema operativo RHEL | Caso d'uso|
| --- | --- | --- | --- | --- |
| Una  | TIPO I | eth0.tenant | eno1.tenant | Da client a HLI/HSR |
| b | TIPO I | eth2.tenant | eno3.tenant | Comunicazione da nodo a nodo |
| C | TIPO I | eth1.tenant | eno2.tenant | Da nodo a archiviazioneNode-to-storage |
| D | TIPO I | eth4.tenant | eno4.tenant | Configurato ma non in uso |
| Una  | TIPO II | vlan\<tenantNo> | team0.tenant | Da client a HLI/HSR |
| b | TIPO II | vlan\<tenantNo-2> | team0.tenant+2 | Comunicazione da nodo a nodo |
| C | TIPO II | vlan\<tenantNo1> | team0.tenant+1 | Da nodo a archiviazioneNode-to-storage |
| D | TIPO II | > vlan\<di tenantNo | team0.tenant+3 | Configurato ma non in uso |

### <a name="storage"></a>Archiviazione
I seguenti punti di montaggio sono preconfigurati:

| Mount point (Punto di montaggio) | Caso d'uso | 
| --- | --- |
|**Nel nodo primario**|
|/hana/shared | Installazione HANA per siD di produzione | 
|/hana/data/SID/mnt00001 | Installazione dei file di dati per il SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di registro per il SID di produzione | 
|/hana/logbackups/SID | Log di rollforward per SID di produzione |
|**Nel nodo ripristino di emergenza**|
|/hana/shared | Installazione HANA per siD di produzione | 
|/hana/data/SID/mnt00001 | Installazione dei file di dati per il SID di produzione | 
|/hana/log/SID/mnt00001 | Installazione dei file di registro per il SID di produzione | 
|/hana/logbackups/SID | Log di rollforward per SID di produzione |


### <a name="key-considerations"></a>Considerazioni sulle chiavi
- /usr/SAP/SID è un collegamento simbolico a /hana/shared/SID.
- Nel sito di ripristino di emergenza: i volumi e i punti di montaggio sono configurati per l'installazione dell'istanza HANA di produzione nell'unità HLI di ripristino di emergenza. 
- Il nodo del sito primario viene sincronizzato con il nodo di ripristino di emergenza utilizzando la replica di sistema HANA. 
- [La copertura globale](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) viene usata per collegare i circuiti ExpressRoute per creare una rete privata tra le reti regionali.


## <a name="next-steps"></a>Passaggi successivi
- [Infrastruttura e connettività](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity) per istanze di grandi dimensioni HANA
- [Disponibilità elevata e ripristino](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) di emergenza per istanze di grandi dimensioni HANAHigh availability and disaster recovery for HANA Large Instances
