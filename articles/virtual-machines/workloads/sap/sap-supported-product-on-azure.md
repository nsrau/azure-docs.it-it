---
title: 'SAP su Azure: quale software SAP è supportato in Azure'
description: Spiega quale software SAP è supportato per la distribuzione in AzureExplains what SAP software is supported to be deployed on Azure
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
ms.date: 03/16/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f0aaa13ff4d3331378cc17cd0cde29be43822397
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460790"
---
# <a name="what-sap-software-is-supported-for-azure-deployments"></a>Supporto del software SAP per le distribuzioni di AzureWhat SAP software is supported for Azure deployments
Questo articolo descrive come scoprire quale software SAP è supportato per le distribuzioni di Azure e quali sono le versioni del sistema operativo o DBMS necessarie.

La valutazione, se il software SAP corrente è supportato e quali versioni del sistema operativo e DBMS sono supportate con il software SAP in Azure, è necessario accedere a:

- Note di supporto SAP
- Matrice di disponibilità del prodotto SAP



## <a name="general-restrictions-for-sap-workload"></a>Restrizioni generali per il carico di lavoro SAPGeneral restrictions for SAP workload
I servizi di Azure IaaS che possono essere usati per il carico di lavoro SAP sono limitati all'hardware x86-64 o x64. Non esistono offerte basate su CPU Sparc o Power che si applicano al carico di lavoro SAP. I clienti che utilizzano applicazioni su sistemi operativi proprietari di architetture hardware come IBM mainframe o AS400, o in cui sono in uso i sistemi operativi HP-UX, Solaris o AIX, devono modificare le applicazioni SAP, tra cui DBMS, seguenti sistemi operativi:

- Server Windows a 64 bit per la piattaforma x86-64
- SUSE linux 64bit per la piattaforma x86-64
- Cappello rosso Linux 64Bit per la piattaforma x86-64
- Oracle Linux 64bit per la piattaforma x86-64

In combinazione con il software SAP, non sono supportate altre versioni del sistema operativo o distribuzioni Linux. I dettagli esatti su versioni e casi specifici sono documentati più avanti nel documento.


## <a name="you-start-here"></a>Si inizia da qui
Il punto di partenza per voi è [nota di supporto SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Durante l'analisi di questa nota SAP dall'alto verso il basso, vengono visualizzate diverse aree del software e delle macchine virtuali supportate

Nella prima sezione sono elencati i requisiti minimi per i rilasci operativi supportati con il software SAP nelle macchine virtuali di Azure in generale. Se non si raggiungono questi requisiti minimi ed eseguire versioni precedenti di questi sistemi operativi, è necessario aggiornare la versione del sistema operativo a una versione minima o versioni ancora più recenti. È corretto che Azure in generale supporterebbe le versioni precedenti di alcuni di questi sistemi operativi. Ma le restrizioni o i rilasci minimi elencati si basano su test e qualifiche eseguiti e non saranno estesi più indietro. 


> [!NOTE]
>Esistono alcuni tipi di vm specifici, istanze di grandi dimensioni HANA o carichi di lavoro SAP che richiedono versioni del sistema operativo più recenti. Casi come questo saranno menzionati in tutto il documento. Casi come questo sono chiaramente documentati nelle note SAP o in altre pubblicazioni SAP.

Nella sezione seguente sono elencate le piattaforme SAP generali supportate con le versioni supportate e più importanti i kernel SAP supportati. Elenca gli stack NetWeaver/ABAP o Java supportati da AND, che richiedono versioni minime del kernel. Gli stack ABAP più recenti sono supportati in Azure, ma non sono necessarie versioni minime del kernel poiché le modifiche per Azure sono state implementate dall'inizio dello sviluppo degli stack più recenti

È necessario controllare:

- Se le applicazioni SAP in esecuzione sono coperte dalle versioni minime indicate. In caso contrario, è necessario definire una nuova versione di destinazione, controllare la matrice di disponibilità del prodotto SAP, quali build del sistema operativo e le combinazioni DBMS sono supportate con la nuova versione di destinazione. Così, che è possibile scegliere il giusto rilascio del sistema operativo e DBMS release
- Se è necessario aggiornare i kernel SAP in un passaggio in Azure
- Se è necessario aggiornare i pacchetti di supporto SAP. Soprattutto i pacchetti di supporto di Base che possono essere necessari per i casi in cui è necessario passare a una versione DBMS più recente


La sezione successiva illustra altri dettagli su altri prodotti SAP e versioni DBMS supportate da SAP in Azure per Windows e Linux.The next section goes into more details on other SAP products and DBMS releases that are supported by SAP on Azure for Windows and Linux. 

> [!NOTE]
> Le versioni minime del dbMS diverso vengono scelte con attenzione e potrebbero non riflettere sempre l'intero spettro delle versioni DBMS supportate dai diversi fornitori DBMS in Azure in generale. Molte considerazioni relative al carico di lavoro SAP sono state prese in considerazione per definire tali versioni minime. Non vi è alcuno sforzo per testare e qualificare le versioni DBMS precedenti. 

> [!NOTE]
> Le versioni minime elencate rappresentano la versione precedente dei sistemi operativi e delle versioni di database. È consigliabile utilizzare le versioni più recenti del sistema operativo e le versioni dei database. In molti casi, versioni più recenti del sistema operativo e del database hanno preso in considerazione il caso di utilizzo dell'esecuzione nel cloud pubblico e il codice adattato per ottimizzare l'esecuzione nel cloud pubblico o più specificamente in Azure

## <a name="oracle-dbms-support"></a>Supporto per Oracle DBMS
Le versioni del sistema operativo, le versioni Oracle DBMS e le funzionalità Oracle supportate in Azure sono elencate in modo specifico nella [nota del supporto SAP #2039619](https://launchpad.support.sap.com/#/notes/2039619). L'essenza di quella nota può essere riassunta come:

- Versione Minima di Oracle supportata nelle macchine virtuali di Azure certificate per NetWeaver è Oracle 11g Release 2 Patchset 3 (11.2.0.4)Minimum Oracle release supported on Azure VMs that are certified for NetWeaver is Oracle 11g Release 2 Patchset 3 (11.2.0.4)
- Come sistemi operativi guest solo Windows e Oracle Linux qualificano. Le versioni esatte del sistema operativo e le relative versioni minime DBMS sono elencate nella nota
- Il supporto di Oracle Linux si estende anche al client Oracle DBMS. Ciò significa che tutti i componenti SAP, ad esempio le istanze di finestra di dialogo di ABAP o Java Stack, devono essere eseguiti anche su Oracle Linux. Solo i componenti SAP all'interno di un sistema SAP che non si connetterebbe al sistema Oracle DBMS sarebbero autorizzati a eseguire un sistema operativo Linux diverso
- Oracle RAC non è supportato 
- Oracle ASM è supportato per alcuni casi. I dettagli sono elencati nella nota
- I sistemi SAP non Unicode sono supportati solo con i server applicazioni in esecuzione con il sistema operativo guest Windows. Il sistema operativo guest del sistema DBMS può essere Oracle Linux o Windows. La causa di questa restrizione è evidente quando si controlla la matrice di disponibilità del prodotto SAP (PAM). Per Oracle Linux, SAP non ha mai rilasciato kernel SAP non Unicode

Conoscendo le versioni DBMS supportate con l'infrastruttura di Azure di destinazione è necessario verificare la matrice di disponibilità del prodotto SAP se le versioni del sistema operativo e DBMS necessarie sono supportate con le versioni dei prodotti SAP che si intendeva eseguire. 


## <a name="sap-hana-support"></a>Supporto per SAP HANA
In Azure sono disponibili due servizi che possono essere usati per eseguire il database HANA:In Azure there are two services, which can be used to run HANA database:

- Macchine virtuali di Azure
- [Istanze di grandi dimensioni HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)

Per l'esecuzione di SAP HANA, SAP dispone di un'infrastruttura con condizioni più solide rispetto all'esecuzione di NetWeaver o di altre applicazioni SAP e DBMS. Di conseguenza, un numero inferiore di macchine virtuali di Azure si qualifica per l'esecuzione del DBMS SAP HANA. L'elenco delle infrastrutture di Azure supportate per SAP HANA è disponibile nella cosiddetta [directory hardware SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). 

> [!NOTE]
> Le unità che iniziano con la lettera 'S' sono unità [DI grandi istanze HANA.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) 

> [!NOTE]
> SAP non ha alcuna certificazione specifica a seconda delle versioni principali di SAP HANA. Contrariamente all'opinione comune, lo scenario di **certificazione** della colonna nelle [piattaforme IaaS certificate HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure), la colonna non fa **alcuna dichiarazione sulla versione principale o minore HANA certificata**. È necessario presupporre che tutte le unità elencate che possono essere utilizzate per HANA 1.0 e HANA 2.0, purché le versioni del sistema operativo certificate per le unità specifiche siano supportate anche dalle versioni HANA 1.0. 

Per l'utilizzo di SAP HANA, possono essere applicati rilasci minimi del sistema operativo diversi rispetto ai casi NetWeaver generali. È necessario controllare i sistemi operativi supportati per ogni unità singolarmente poiché questi possono variare. A tale scopo, fare clic su ciascuna unità. Appariranno ulteriori dettagli. Uno dei dettagli elencati sono i diversi sistemi operativi supportati per questa unità specifica.

> [!NOTE]
> Azure HANA Large Instance units are more restrictive with supported operating systems compared to Azure VMs. D'altra parte, le macchine virtuali di Azure possono applicare versioni operative più recenti come versioni minime. Ciò è particolarmente vero per alcune delle unità VM più grandi che richiedevano modifiche ai kernel Linux

Conoscendo il sistema operativo supportato per l'infrastruttura di Azure, è necessario controllare la [nota](https://launchpad.support.sap.com/#/notes/2235581) del supporto SAP #2235581 per le versioni esatte di SAP HANA e i livelli di patch supportati con le unità di Azure di destinazione. 

> [!IMPORTANT]
> Il passaggio per controllare le versioni esatte di SAP HANA e i livelli di patch supportati è molto importante. In molti casi, il supporto di una determinata versione del sistema operativo dipende da un livello di patch specifico degli eseguibili SAP HANA.

Poiché si conoscono le versioni HANA specifiche che è possibile eseguire nell'infrastruttura di Azure di destinazione, è necessario controllare la matrice di disponibilità del prodotto SAP per scoprire se esistono restrizioni con le versioni dei prodotti SAP che supportano le versioni HANA filtrate


## <a name="certified-azure-vms-and-hana-large-instance-units-and-business-transaction-throughput"></a>Macchine virtuali di Azure certificate e unità di istanza di grandi dimensioni HANA e velocità effettiva delle transazioni aziendali
Oltre a valutare le versioni del sistema operativo supportate, le versioni DBMS e le versioni software SAP di supporto dipendenti per le unità dell'infrastruttura di Azure, è necessario qualificare queste unità in base alla velocità effettiva delle transazioni aziendali, espressa nell'unità 'SAP' Sap. Tutto il dimensionamento SAP dipende dai calcoli SAPS. Valutando i sistemi SAP esistenti, in genere è possibile, con l'aiuto del provider dell'infrastruttura, calcolare il SAPS delle unità. Per il livello DBMS e per il livello dell'applicazione. In altri casi in cui vengono create nuove funzionalità, un esercizio di dimensionamento con SAP può rivelare i numeri SAPS necessari per il livello dell'applicazione e il livello DBMS. In qualità di provider di infrastruttura, Microsoft è tenuta a fornire la caratterizzazione della velocità effettiva SAP delle diverse unità certificate NetWeaver e/o HANA.

Per le macchine virtuali di Azure, questi numeri di velocità effettiva SAPS sono documentati in [NOTA del supporto SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Per le unità di istanza di grandi dimensioni di Azure HANA, i numeri di velocità effettiva SAPS sono documentati in [NOTa di supporto SAP #2316233](https://launchpad.support.sap.com/#/notes/2316233)

Esaminando [la nota del supporto SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533), si applicano le seguenti osservazioni:

- Per le macchine virtuali di Azure serie M e le macchine virtuali di **Azure serie Mv2, si applicano versioni minime del sistema operativo diverse rispetto ad altri tipi di macchine virtuali di Azure.For M-Series Azure VMs and Mv2-Series Azure VMs, different minimum OS releases apply than per other Azure VM types.** Il requisito per le versioni più recenti del sistema operativo si basa sulle modifiche che i diversi fornitori di sistemi operativi dovevano fornire nelle versioni del sistema operativo per abilitare i sistemi operativi in esecuzione sui tipi di macchine virtuali di Azure specifici o ottimizzare le prestazioni e velocità effettiva del carico di lavoro SAP su tali tipi di VM
- Sono disponibili due tabelle che specificano tipi di macchine virtuali diversi. La seconda tabella specifica la velocità effettiva SAPS per i tipi di vm azure che supportano solo archiviazione standard di Azure.The second table specifies SAPS throughput for Azure VM types that support Azure standard Storage only. La distribuzione DBMS sulle unità specificate nella seconda tabella della nota non è supportata


## <a name="other-sap-products-supported-on-azure"></a>Altri prodotti SAP supportati in Azure
In general the assumption is that with the state of hyperscale clouds like Azure, most of the SAP software should run without functional problems in Azure. Tuttavia, e al contrario della visualizzazione del cloud privato, SAP esprime ancora il supporto per i diversi prodotti SAP in modo esplicito per i diversi provider di cloud hyerpscale. Di conseguenza, esistono diverse note di supporto SAP che indicano il supporto per Azure per diversi prodotti SAP. 

Per la piattaforma Business Objects BI, [nota #2145537 del supporto SAP](https://launchpad.support.sap.com/#/notes/2145537) fornisce un elenco di prodotti SAP Business Objects supportati in Azure.For Business Objects BI platform, SAP support note #2145537 gives a list of SAP Business Objects products supported on Azure. Se sono presenti domande su componenti o combinazioni di versioni software e versioni del sistema operativo che sembrano non essere elencate o supportate e che sono più recenti rispetto alle versioni minime elencate, è necessario aprire una richiesta di supporto SAP sul componente richiesto sostegno per.

Per Business Objects Data Services, [la nota del supporto SAP #22288344](https://launchpad.support.sap.com/#/notes/2288344) illustra il supporto minimo di SAP Data Services in esecuzione in Azure.For Business Objects Data Services, SAP support note and explains minimum support of SAP Data Services running on Azure. 

> [!NOTE]
> Come indicato nella nota di supporto SAP, è necessario controllare il PAM SAP per identificare il livello di pacchetto di supporto corretto da supportare in Azure

Il supporto SAP Datahub/Vora in Azure Kubernetes Services (AKS) è descritto in dettaglio nella [nota](https://launchpad.support.sap.com/#/notes/2464722) del supporto SAP #2464722

Il supporto per SAP BPC 10.1 SP08 è descritto in [Nota sul supporto SAP #2451795](https://launchpad.support.sap.com/#/notes/2451795)

Il supporto per SAP Hybris Commerce Platform 5.x e 6.x su Azure è descritto in dettaglio nel [Wiki Hybris](https://wiki.hybris.com/display/SUP/Using+the+hybris+Platform+with+the+Cloud)


## <a name="next-steps"></a>Passaggi successivi
Leggere i passaggi successivi nella pianificazione e nell'implementazione di Macchine virtuali di [Azure per SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)

