---
title: 'SAP in Azure: quale software SAP è supportato in Azure'
description: Illustra il software SAP che è supportato per la distribuzione in Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8b2f4e7a16c967b26b545d1405f973bf8b8afaae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82086131"
---
# <a name="what-sap-software-is-supported-for-azure-deployments"></a>Quale software SAP è supportato per le distribuzioni di Azure
Questo articolo descrive come scoprire quale software SAP è supportato per le distribuzioni di Azure e quali sono le versioni del sistema operativo necessarie o le versioni DBMS.

La valutazione, se il software SAP corrente è supportato e quali sono le versioni del sistema operativo e DBMS supportate con il software SAP in Azure, sarà necessario accedere a:

- Note di supporto SAP
- Matrice di disponibilità del prodotto SAP



## <a name="general-restrictions-for-sap-workload"></a>Restrizioni generali per il carico di lavoro SAP
I servizi IaaS di Azure che possono essere usati per il carico di lavoro SAP sono limitati a hardware x86-64 o x64. Non sono disponibili offerte basate sulla CPU SPARC o Power valide per il carico di lavoro SAP. I clienti che eseguono le proprie applicazioni su sistemi operativi proprietari di architetture hardware come IBM mainframe o AS400, o dove sono in uso i sistemi operativi HP-UX, Solaris o AIX, devono modificare le applicazioni SAP, incluso DBMS, in uno dei sistemi operativi seguenti:

- Windows Server 64bit per la piattaforma x86-64
- SUSE Linux a 64 bit per la piattaforma x86-64
- Red Hat Linux a 64 bit per la piattaforma x86-64
- Oracle Linux a 64 bit per la piattaforma x86-64

In combinazione con il software SAP, non sono supportate altre versioni del sistema operativo o distribuzioni Linux. I dettagli esatti su versioni e casi specifici sono documentati più avanti nel documento.


## <a name="you-start-here"></a>Inizia qui
Il punto di partenza per l'utente è la [Nota di supporto SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Man mano che si esamina questa nota SAP dall'alto verso il basso, vengono visualizzate diverse aree del software e delle macchine virtuali supportate

La prima sezione elenca i requisiti minimi per i rilasci operativi supportati con il software SAP in macchine virtuali di Azure in generale. Se non si soddisfano i requisiti minimi e si eseguono versioni precedenti di questi sistemi operativi, è necessario aggiornare la versione del sistema operativo a tale versione minima o anche a versioni più recenti. È corretto che Azure in generale supporti le versioni precedenti di alcuni di questi sistemi operativi. Tuttavia, le limitazioni o le versioni minime elencate sono basate su test e qualifiche eseguite e non verranno ulteriormente estese. 


> [!NOTE]
>Sono disponibili alcuni tipi di VM specifici, le istanze large di HANA o i carichi di lavoro SAP che richiederanno versioni del sistema operativo più recenti. Casi come quelli che verranno citati in tutto il documento. Casi come questi sono chiaramente documentati in note SAP o in altre pubblicazioni SAP.

La sezione seguente elenca le piattaforme SAP generali supportate con le versioni supportate e più importanti dei kernel SAP supportati. Elenca gli stack NetWeaver/ABAP o Java supportati e, che necessitano di versioni minime del kernel. In Azure sono supportati stack ABAP più recenti, ma non sono necessarie versioni minime del kernel poiché le modifiche per Azure sono state implementate dall'inizio dello sviluppo degli stack più recenti

È necessario verificare:

- Se le applicazioni SAP in esecuzione sono coperte dalle versioni minime indicate. In caso contrario, è necessario definire una nuova versione di destinazione, archiviare la matrice di disponibilità del prodotto SAP, le compilazioni del sistema operativo e le combinazioni DBMS supportate con la nuova versione di destinazione. Quindi, è possibile scegliere la versione corretta del sistema operativo e la versione DBMS
- Se è necessario aggiornare i kernel SAP in un passaggio ad Azure
- Se è necessario aggiornare i pacchetti di supporto SAP. In particolare per i pacchetti di supporto che possono essere necessari per i casi in cui è necessario passare a una versione DBMS più recente


La sezione successiva include informazioni dettagliate su altri prodotti SAP e versioni DBMS supportate da SAP in Azure per Windows e Linux. 

> [!NOTE]
> Le versioni minime dei diversi sistemi DBMS vengono scelte attentamente e potrebbero non sempre riflettere l'intera gamma di sistemi DBMS. Per definire tali versioni minime sono state prese in considerazione molte considerazioni relative ai carichi di lavoro SAP. Non è possibile testare e qualificare versioni DBMS precedenti. 

> [!NOTE]
> Le versioni minime elencate rappresentano la versione precedente dei sistemi operativi e le versioni del database. Si consiglia vivamente di utilizzare le versioni più recenti del sistema operativo e le versioni di database. In numerosi casi, le versioni più recenti del sistema operativo e del database hanno portato a tenere in considerazione il caso di utilizzo dell'esecuzione nel cloud pubblico e il codice adattato per l'esecuzione nel cloud pubblico o in modo più specifico in Azure

## <a name="oracle-dbms-support"></a>Supporto per DBMS Oracle
Il sistema operativo, le versioni DBMS Oracle e le funzionalità Oracle supportate in Azure sono elencate in modo specifico nella [Nota di supporto SAP #2039619](https://launchpad.support.sap.com/#/notes/2039619). Questa nota può essere riassunta come segue:

- La versione minima di Oracle supportata nelle macchine virtuali di Azure certificate per NetWeaver è Oracle 11g Release 2 patchset 3 (11.2.0.4)
- Poiché i sistemi operativi guest solo Windows e Oracle Linux sono idonei. Le versioni esatte del sistema operativo e le versioni minime DBMS correlate sono elencate nella nota
- Il supporto di Oracle Linux si estende anche al client DBMS Oracle. Ciò significa che è necessario eseguire tutti i componenti SAP, ad esempio le istanze di finestra di dialogo di ABAP o Java stack, anche su Oracle Linux. Solo i componenti SAP all'interno di tale sistema SAP che non si connetteranno al sistema DBMS Oracle sarebbero autorizzati a eseguire un sistema operativo Linux diverso
- Oracle RAC non è supportato 
- Oracle ASM è supportato per alcuni casi. I dettagli sono elencati nella nota
- I sistemi SAP non Unicode sono supportati solo con i server applicazioni che eseguono con sistema operativo guest Windows. Il sistema operativo guest del sistema DBMS può essere Oracle Linux o Windows. Il motivo di questa restrizione è evidente quando si verifica la matrice di disponibilità del prodotto SAP (PAM). Per Oracle Linux, SAP non ha mai rilasciato kernel SAP non Unicode

Conoscere le versioni DBMS supportate con l'infrastruttura di Azure di destinazione è necessario controllare la matrice di disponibilità del prodotto SAP se le versioni del sistema operativo e il sistema DBMS necessario sono supportati con le versioni del prodotto SAP che si intende eseguire. 


## <a name="sap-hana-support"></a>Supporto SAP HANA
In Azure sono disponibili due servizi, che possono essere usati per eseguire il database HANA:

- Macchine virtuali di Azure
- [Istanze large di HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)

Per l'esecuzione di SAP HANA, SAP è in grado di soddisfare più e più solide infrastrutture per l'esecuzione di NetWeaver o altre applicazioni SAP e DBMS. Di conseguenza, un numero inferiore di macchine virtuali di Azure è idoneo per l'esecuzione del SAP HANA DBMS. L'elenco dell'infrastruttura di Azure supportata supportata per SAP HANA è disponibile nella [directory SAP Hana hardware](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)denominata. 

> [!NOTE]
> Le unità che iniziano con la letterà s'sono unità di [istanze large di Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) . 

> [!NOTE]
> SAP non ha una certificazione specifica dipendente dal SAP HANA versioni principali. Contrariamente all'opinione comune, lo **scenario di certificazione** delle colonne nelle [piattaforme IaaS certificate Hana](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure), la colonna **non rilascia alcuna dichiarazione relativa alla versione principale o secondaria di Hana certificata**. È necessario presupporre che tutte le unità elencate possano essere usate per HANA 1,0 e HANA 2,0, purché le versioni del sistema operativo certificate per le unità specifiche siano supportate anche dalle versioni di HANA 1,0. 

Per l'utilizzo di SAP HANA, possono essere applicate versioni minime del sistema operativo diverse da quelle per i casi NetWeaver generali. È necessario verificare i sistemi operativi supportati per ogni unità singolarmente, perché potrebbero variare. A tale scopo, fare clic su ogni unità. Verranno visualizzati altri dettagli. Uno dei dettagli elencati è costituito dai diversi sistemi operativi supportati per questa unità specifica.

> [!NOTE]
> Le unità di istanze large di Azure HANA sono più restrittive con i sistemi operativi supportati rispetto alle macchine virtuali di Azure. Le macchine virtuali di Azure possono invece applicare versioni operative più recenti come versioni minime. Questa operazione è particolarmente valida per alcune delle unità VM più grandi che necessitano di modifiche ai kernel Linux

Conoscendo il sistema operativo supportato per l'infrastruttura di Azure, è necessario controllare la [Nota di supporto SAP #2235581](https://launchpad.support.sap.com/#/notes/2235581) per le versioni esatte SAP HANA e i livelli di patch supportati con le unità di Azure di destinazione. 

> [!IMPORTANT]
> Il passaggio del controllo delle versioni esatte SAP HANA e dei livelli di patch supportati è molto importante. In numerosi casi, il supporto di una determinata versione del sistema operativo dipende da un livello di patch specifico dei file eseguibili del SAP HANA.

Quando si conoscono le versioni specifiche di HANA che è possibile eseguire nell'infrastruttura di Azure di destinazione, è necessario archiviare la matrice di disponibilità del prodotto SAP per verificare se sono presenti restrizioni con le versioni del prodotto SAP che supportano le versioni di HANA filtrate.


## <a name="certified-azure-vms-and-hana-large-instance-units-and-business-transaction-throughput"></a>Macchine virtuali di Azure certificate e unità di istanze large di HANA e velocità effettiva delle transazioni aziendali
Oltre a valutare le versioni del sistema operativo supportate, le versioni DBMS e il supporto dipendente versioni del software SAP per le unità di infrastruttura di Azure, è necessario qualificare queste unità in base alla velocità effettiva delle transazioni aziendali, espressa nell'unità "SAP" di SAP. Tutte le dimensioni di SAP dipendono dai calcoli dei succhi. Valutando i sistemi SAP esistenti, in genere è possibile, con l'aiuto del provider dell'infrastruttura, calcolare i succhi delle unità. Per il livello DBMS e per il livello dell'applicazione. Negli altri casi in cui viene creata una nuova funzionalità, un esercizio di ridimensionamento con SAP può rivelare i numeri di SAP necessari per il livello dell'applicazione e il livello DBMS. Dato che il provider di infrastruttura Microsoft è obbligato a fornire la caratterizzazione della velocità effettiva SAP delle diverse unità che sono certificate NetWeaver e/o HANA.

Per le macchine virtuali di Azure, questi numeri di velocità effettiva di SAP sono documentati nella [Nota del supporto SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Per le unità di istanze large di Azure HANA, i numeri di velocità effettiva di SAP sono documentati nella [Nota del supporto SAP #2316233](https://launchpad.support.sap.com/#/notes/2316233)

Esaminando la [Nota di supporto SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533), si applicano le osservazioni seguenti:

- **Per le VM di Azure della serie M e le VM di Azure della serie Mv2, si applicano versioni minime del sistema operativo diverse da quelle per altri tipi di VM di Azure** Il requisito per le versioni più recenti del sistema operativo è basato sulle modifiche che i diversi fornitori di sistemi operativi hanno dovuto fornire nelle versioni del sistema operativo per abilitare i sistemi operativi in esecuzione sui tipi di VM di Azure specifici o ottimizzare le prestazioni e la velocità effettiva del carico di lavoro SAP in questi tipi di VM.
- Sono disponibili due tabelle che specificano tipi di VM diversi. La seconda tabella specifica la velocità effettiva di SAP per i tipi di VM di Azure che supportano solo l'archiviazione standard di Azure. La distribuzione DBMS nelle unità specificate nella seconda tabella della nota non è supportata


## <a name="other-sap-products-supported-on-azure"></a>Altri prodotti SAP supportati in Azure
In generale, il presupposto è che con lo stato dei cloud con iperscalabilità come Azure, la maggior parte del software SAP dovrebbe essere eseguita senza problemi funzionali in Azure. Tuttavia, oltre alla visualizzazione del cloud privato, SAP continua a esprimere il supporto per i diversi prodotti SAP in modo esplicito per i diversi provider di servizi cloud hyerpscale. Di conseguenza, sono presenti note di supporto SAP diverse che indicano il supporto per Azure per prodotti SAP diversi. 

Per la piattaforma Business Objects BI, la [Nota di supporto sap #2145537](https://launchpad.support.sap.com/#/notes/2145537) fornisce un elenco di prodotti SAP Business Objects supportati in Azure. In caso di domande relative a componenti o combinazioni di versioni software e versioni del sistema operativo che sembrano non essere elencate o supportate e che sono più recenti delle versioni minime elencate, è necessario aprire una richiesta di supporto SAP per il componente di cui si richiede il supporto.

Per gli oggetti business Data Services, [Nota di supporto sap #22288344](https://launchpad.support.sap.com/#/notes/2288344) illustra il supporto minimo di SAP Data Services in esecuzione in Azure. 

> [!NOTE]
> Come indicato nella nota del supporto SAP, è necessario archiviare SAP PAM per identificare il livello di pacchetto di supporto corretto per essere supportato in Azure

Il supporto di SAP datahub/Vora in Azure Kubernetes Services (AKS) è illustrato in dettaglio nella [Nota del supporto sap #2464722](https://launchpad.support.sap.com/#/notes/2464722)

Il supporto per SAP BPC 10,1 SP08 è descritto nella [Nota di supporto sap #2451795](https://launchpad.support.sap.com/#/notes/2451795)

Il supporto per SAP hybris Commerce platform in Azure è descritto in dettaglio nella [documentazione di hybris](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/1811/en-US/8c71300f866910149b40c88dfc0de431.html). A partire dal sistema DBMS supportato per la piattaforma SAP hybris Commerce, viene elencato come:

- SQL Server e Oracle sulla piattaforma del sistema operativo Windows. Le stesse versioni minime si applicano a SAP NetWeaver. Per informazioni dettagliate, vedere la [Nota di supporto SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- SAP HANA su Red Hat e SUSE Linux. SAP HANA tipi di VM certificati sono richiesti come descritto in precedenza in [questo documento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure#sap-hana-support). SAP (hybris) Commerce platform è considerato un carico di lavoro OLTP
- SQL Azure DB a partire da SAP (hybris) Commerce platform versione 1811




## <a name="next-steps"></a>Passaggi successivi
Leggere i passaggi successivi in [pianificazione e implementazione di macchine virtuali di Azure per SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)

