---
title: Elenco di controllo per la pianificazione e la distribuzione di carichi di lavoro SAP | Microsoft Docs
description: elenco di controllo per la pianificazione e la distribuzione di carichi di lavoro SAP in Azure
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
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: abe04a88f3337ed26b98815b5633fd5d719b7ce3
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234215"
---
# <a name="sap-workload-on-azure-planning-and-deployment-checklist"></a>Elenco di controllo per la pianificazione e la distribuzione di carichi di lavoro SAP in Azure 

Questo elenco di controllo è stato progettato per i clienti che eseguono la migrazione delle proprie applicazioni SAP NetWeaver, S/4HANA e Hybris all'infrastruttura di Azure distribuita come servizio.  L'elenco dovrebbe essere esaminato da un cliente e/o da un partner SAP nel corso del progetto. È importante che molti dei controlli siano eseguiti all'inizio del progetto e nella fase di pianificazione. Al termine della distribuzione, alcune modifiche elementari all'infrastruttura di Azure o alle versioni di software SAP distribuite possono diventare complesse. Esaminare questo elenco di controllo quando si eseguono attività cardine nel corso di un progetto.  In questo modo è possibile rilevare lievi problemi prima che diventino più gravi e si ha tempo sufficiente per riprogettare e testare le modifiche necessarie. L'elenco di controllo non ha la pretesa di essere esauriente. In base alle singole situazioni, può essere necessario eseguire molti più controlli. 

L'elenco di controllo non include le attività che sono indipendenti da Azure.  Esempio: le interfacce delle applicazioni SAP cambiano durante il passaggio al cloud pubblico di Azure o a un provider di hosting.    

Questo elenco di controllo può essere usato anche per i sistemi già distribuiti. Dopo la distribuzione possono essere state aggiunte nuove funzionalità, ad esempio l'acceleratore di scrittura, le zone di disponibilità e nuovi tipi di macchina virtuale.  È pertanto utile esaminare l'elenco di controllo periodicamente per essere sicuri di essere a conoscenza delle nuove funzionalità della piattaforma Azure. 

## <a name="project-preparation-and-planning-phase"></a>Fase di preparazione e pianificazione del progetto
In questa fase viene pianificata una migrazione del carico di lavoro SAP nel cloud pubblico di Azure. Il set minimo di entità ed elementi descritti e definiti in questa fase include quanto segue:

1. Documento di progettazione di alto livello, che deve contenere:
    1. L'inventario corrente dei componenti e delle applicazioni SAP e l'inventario delle applicazioni di destinazione in Azure
    2. Creazione e analisi di una matrice di assegnazione delle responsabilità, che definisce le responsabilità e le assegnazioni delle varie parti coinvolte. Iniziare in modo generale e passare quindi a livelli sempre più granulari attraverso le fasi di pianificazione e distribuzione preliminare
    2. Un'architettura di alto livello della soluzione
    3. Informazioni sulla scelta delle aree di Azure in cui eseguire la distribuzione. Per l'elenco delle aree di Azure, vedere [Aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/). Per i servizi disponibili in ognuna delle aree di Azure, vedere l'articolo [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/)
    4. Architettura di rete per la connessione da locale ad Azure. Per iniziare ad acquisire familiarità, vedere [Data center virtuale di Azure e piano di controllo aziendale](https://docs.microsoft.com/azure/architecture/vdc/)
    5. Principi di sicurezza per l'esecuzione di dati con impatto aziendale elevato in Azure. Per informazioni di riferimento, iniziare con [Documentazione sulla sicurezza di Azure](https://docs.microsoft.com/azure/security/)
2.  Documento di progettazione tecnica, che contiene:
    1.  Un diagramma a blocchi della soluzione 
    2.  Dimensionamento dei componenti di calcolo, archiviazione e rete in Azure. Per il dimensionamento definito da SAP per le macchine virtuali di Azure, vedere la nota di supporto SAP [1928533](https://launchpad.support.sap.com/#/notes/1928533) 
    3.  Architettura per la continuità aziendale e il ripristino di emergenza
    4.  Informazioni dettagliate sulle versioni di sistema operativo, database, kernel e pacchetti di supporto SAP. Non si può dare per scontato che qualsiasi versione di sistema operativo supportata da SAP NetWeaver o S/4HANA lo sia anche nelle macchine virtuali di Azure. La stessa considerazione vale per le versioni del sistema DBMS. È obbligatorio verificare le origini seguenti per un corretto allineamento e, se necessario, aggiornare le versioni di SAP, del sistema DBMS o del sistema operativo per rientrare in uno scenario supportato da SAP e Azure. È indispensabile rimanere entro i limiti delle combinazioni di versioni supportate da SAP e Azure per ottenere supporto completo da SAP e Microsoft. Se necessario, pianificare l'aggiornamento di alcuni dei componenti software. Altri dettagli sul software di SAP, del sistema operativo e del sistema DBMS supportato sono riportati in questi documenti:
        1.  Nota di supporto SAP [1928533](https://launchpad.support.sap.com/#/notes/1928533). Questa nota definisce le versioni minime di sistema operativo supportate nelle macchine virtuali di Azure e le versioni minime di database necessarie per la maggior parte dei database non HANA. La nota presenta anche il dimensionamento di SAP per i diversi tipi di macchine virtuali di Azure supportati da SAP.
        2.  Nota di supporto SAP [2039619](https://launchpad.support.sap.com/#/notes/2039619). La nota definisce la matrice di supporto di Oracle in Azure. Tenere presente che Oracle supporta solo Windows e Oracle Linux come sistema operativo guest in Azure per carichi di lavoro SAP. Questa informativa sul supporto è applicabile per il livello applicazione SAP che esegue anche istanze di SAP. Oracle, tuttavia, non supporta la disponibilità elevata per SAP Central Services in Oracle Linux tramite Pacemaker. Se è necessaria la disponibilità elevata per ASCS in Oracle Linux, è necessario usare SIOS Protection Suite for Linux. Per i dati di certificazione SAP dettagliati, vedere la nota del supporto SAP [#1662610 - Support details for SIOS Protection Suite for Linux](https://launchpad.support.sap.com/#/notes/1662610) (N. 1662610 - Dettagli di supporto per SIOS Protection Suite for Linux). Per Windows, la soluzione di failover del cluster di failover Windows con supporto SAP è supportata solo in combinazione con Oracle come livello DBMS. 
        3.  Nota di supporto SAP [2235581](https://launchpad.support.sap.com/#/notes/2235581) per ottenere la matrice di supporto SAP HANA nelle diverse versioni di sistema operativo
        4.  Le [istanze Large di HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) e le macchine virtuali di Azure supportate da SAP HANA sono elencate [qui](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
        5.  [Product Availability Matrix (PAM) SAP](https://support.sap.com/en/)
        6.  Altre note SAP relative ad altri prodotti specifici di SAP  
    5.  È consigliabile rispettare rigorosamente la progettazione a 3 livelli per i sistemi di produzione SAP. La combinazione di server ASCS + APP nella stessa macchina virtuale non è consigliata.  L'uso di configurazioni di cluster multi-SID per SAP Central Services è supportato con Windows come sistema operativo guest in Azure. Queste configurazioni non sono invece supportate con i sistemi operativi Linux in Azure. Documentazione per il caso di Windows come sistema operativo guest sono reperibili negli articoli seguenti:
        1.  [Disponibilità elevata multi-SID dell'istanza SAP ASCS/SCS con Windows Server Failover Clustering e i dischi condivisi in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk)
        2.  [Disponibilità elevata multi-SID dell'istanza ASCS/SCS di SAP con Windows Server Failover Clustering e condivisione file in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share)
    6.  Architettura per la disponibilità elevata e il ripristino di emergenza
        1.  Definire la struttura che deve avere l'architettura per la disponibilità elevata e il ripristino di emergenza in base agli obiettivi RTO e RPO
        2.  Per la disponibilità elevata all'interno della stessa zona, controllare ciò che può offrire il sistema DBMS desiderato in Azure. La maggior parte dei sistemi DBMS offre metodi sincroni di un server hot standby sincrono, che è consigliabile usare per i sistemi di produzione. Consultare anche la documentazione relativa a SAP sui vari database, a partire da [Considerazioni sulla distribuzione DBMS di macchine virtuali di Azure per un carico di lavoro SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) e i documenti correlati
            1.  **NON** è supportato l'uso del servizio Cluster di failover di Windows con una configurazione a dischi condivisi per il livello DBMS, come l'esempio per SQL Server descritto [qui](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017). Sono supportate invece soluzioni come:
                1.  [SQL Server AlwaysOn](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups) 
                2.  [Oracle Data Guard](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
                3.  [Replica di sistema HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        3.  Per il ripristino di emergenza tra diverse aree di Azure, verificare le possibilità offerte dai diversi fornitori di sistemi DBMS. La maggior parte dei fornitori supporta la replica asincrona o il log shipping
        4.  Per il livello applicazione SAP, definire se eseguire i sistemi di test di regressione disponibili in azienda, che teoricamente sono repliche delle distribuzioni di produzione, nella stessa area di Azure o nell'area di ripristino di emergenza. In quest'ultimo caso, è possibile definire il sistema di regressione aziendale come destinazione di ripristino di emergenza per la fase di produzione
        5.  Se si decide di non collocare sistemi non di produzione nel sito di ripristino di emergenza, valutare l'uso di Azure Site Recovery come metodo fattibile per la replica del livello applicazione SAP nell'area di ripristino di emergenza di Azure. Vedere anche [Configurare il ripristino di emergenza per una distribuzione di applicazioni SAP NetWeaver multilivello](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap) 
        6.  Se si decide di usare una configurazione combinata di disponibilità elevata e ripristino di emergenza sfruttando le [zone di disponibilità di Azure](https://docs.microsoft.com/azure/availability-zones/az-overview), acquisire familiarità con le aree di Azure in cui è possibile usare le zone di disponibilità e con le restrizioni che possono essere introdotte da un aumento delle latenze di rete tra due zone di disponibilità  
3.  Il cliente o il partner deve creare un inventario di tutte le interfacce (SAP e non SAP). 
4.  Progettazione dell'architettura dei servizi di base, che include gli elementi seguenti:
    1.  Progettazione di Active Directory e DNS
    2.  Topologia di rete all'interno di Azure e assegnazione di diversi sistemi SAP
    3.  Struttura degli [accessi in base al ruolo](https://docs.microsoft.com/azure/role-based-access-control/overview) per i diversi team che gestiscono l'infrastruttura e le applicazioni SAP in Azure
    3.  Topologia dei gruppi di risorse 
    4.  [Strategia di assegnazione dei tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#tags-and-billing)
    5.  Convenzione di denominazione per le macchine virtuali e altri componenti e/o nomi logici dell'infrastruttura
5.  Contratto di supporto tecnico Premier Microsoft: identificare il Microsoft Technical Account Manager (TAM). Per i requisiti del supporto di SAP, leggere la nota di supporto SAP [2015553](https://launchpad.support.sap.com/#/notes/2015553) 
6.  Definire il numero di sottoscrizioni di Azure e la quota di core per le diverse sottoscrizioni. [Aprire richieste di supporto per aumentare le quote delle sottoscrizioni di Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) in base alle esigenze 
7.  Piano di riduzione e migrazione dei dati per la migrazione dei dati SAP in Azure. Per i sistemi SAP NetWeaver, SAP include linee guida su come mantenere limitato il volume di un numero elevato di dati. SAP ha pubblicato [questa guida di approfondimento](https://help.sap.com/http.svc/rc/2eb2fba8f8b1421c9a37a8d7233da545/7.0/en-US/Data_Management_Guide_Version_70E.PDF) sulla gestione dei dati nei sistemi SAP ERP. Alcuni contenuti, tuttavia, si applicano ai sistemi NetWeaver e S/4HANA in generale.
8.  Definire l'approccio da adottare per la distribuzione automatizzata. L'obiettivo dell'automazione per le distribuzioni di infrastruttura in Azure è quello di distribuire in modo deterministico e ottenere risultati deterministici. Molti clienti usano script basati su Power Shell o sull'interfaccia della riga di comando. Esistono tuttavia diverse tecnologie open source che è possibile usare per distribuire l'infrastruttura di Azure per SAP e persino installare il software SAP. Esempi sono disponibili in GitHub:
    1.  [Distribuzioni SAP automatizzate nel cloud di Azure](https://github.com/Azure/sap-hana)
    2.  [Installazione di SAP HANA](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  Definire un piano per verificare con frequenza regolare la progettazione e la distribuzione insieme all'integratore di sistemi, a Microsoft e a eventuali altre parti coinvolte

 
## <a name="pilot-phase-strongly-recommended"></a>Fase pilota (fortemente consigliata)
 
La fase pilota può essere eseguita prima della fase di pianificazione e preparazione del progetto o in parallelo con essa. Questa fase può anche usata per testare approcci e scelte di progettazione adottati durante la fase di pianificazione e preparazione. La fase pilota può essere estesa fino a diventare un vero e proprio modello di verifica. Durante una distribuzione pilota è consigliabile configurare e convalidare una soluzione completa di disponibilità elevata e ripristino di emergenza e la progettazione di un sistema di sicurezza. In alcuni casi, i clienti possono eseguire anche test di scalabilità in questa fase. Altri clienti usano la distribuzione dei sistemi sandbox SAP come fase pilota. Si presuppone quindi che sia stato identificato un sistema di cui eseguire la migrazione in Azure allo scopo di implementare una fase pilota.

1. Ottimizzare il trasferimento dei dati in Azure. La massima dipendenza dai casi dei clienti trasferiti tramite [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) dall'ambiente locale è stata più veloce se il circuito Express aveva una larghezza di banda sufficiente. Per altri clienti, il passaggio attraverso Internet risulta più veloce
2. Nel caso di una migrazione di piattaforme SAP eterogenee, che implica un'esportazione e un'importazione dei dati del database, testare e ottimizzare le fasi di esportazione e importazione. Per le migrazioni di grandi dimensioni, con SQL Server come piattaforma di destinazione, sono disponibili consigli in [questo post di blog](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070). È possibile adottare l'approccio di Migration Monitor/SWPM nel caso in cui non sia necessario un aggiornamento combinato delle versioni oppure usare il processo di [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) quando si combina la migrazione con un aggiornamento delle versioni SAP rispettando determinate combinazioni delle piattaforme DBMS di origine e destinazione, come descritto ad esempio in [Database Migration Option (DMO) of SUM 2.0 SP03](https://launchpad.support.sap.com/#/notes/2631152) (Opzione di migrazione del database di SUM 2.0 SP03). 
   1.  Esportazione nell'origine, caricamento dei file di esportazione in Azure e prestazioni dell'importazione.  Ottimizzare la sovrapposizione tra esportazione e importazione
   2.  Valutare il volume del database tra la piattaforma di destinazione e quella di origine in modo da usare questa valutazione per il dimensionamento dell'infrastruttura    
   3.  Verificare e ottimizzare la tempistica 
3. Verifica tecnica 
   1. Tipi di macchina virtuale
      1.  Convalidare le risorse nelle note di supporto SAP, SAP HANA directory hardware e SAP PAM di nuovo per assicurarsi che non siano state apportate modifiche alle macchine virtuali supportate per Azure, versioni del sistema operativo supportate per tali tipi di VM e versioni SAP e DBMS supportate
      2.  Verificare nuovamente il dimensionamento dell'applicazione e dell'infrastruttura distribuite in Azure. Se si spostano applicazioni esistenti, è spesso possibile ricavare i valori SAPS necessari dall'infrastruttura in uso e dalla [pagina Web dei benchmark SAP](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) e confrontarli con i valori SAPS elencati nella nota di supporto SAP [1928533](https://launchpad.support.sap.com/#/notes/1928533). Tenere inoltre presente [questo articolo](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208)
      3.  Valutare e testare il dimensionamento delle macchine virtuali di Azure in relazione alla massima velocità effettiva di archiviazione e di rete dei diversi tipi di macchina virtuale selezionati nella fase di pianificazione. I dati sono disponibili negli articoli seguenti:
          1.  [Dimensioni per le macchine virtuali Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json): per il dimensionamento è importante prendere in considerazione la **massima velocità effettiva del disco senza memorizzazione nella cache**
          2.  [Dimensioni delle macchine virtuali Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json): per il dimensionamento è importante prendere in considerazione la **massima velocità effettiva del disco senza memorizzazione nella cache**
   2. Archiviazione
      1.  Usare l' [archiviazione di Azure SDD standard](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) come minimo per le macchine virtuali che rappresentano i livelli dell'applicazione SAP e per la distribuzione DBMS non sensibile alle prestazioni
      2.  Si consiglia di non usare i [dischi di HDD standard di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) in generale
      2.  Usare [archiviazione Premium di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) per tutte le macchine virtuali DBMS che sono sensibili alle prestazioni in remoto
      2.  Usare [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/)
      3.  Usare l'acceleratore di scrittura di Azure per le unità di log DBMS con serie M. Tenere presenti i limiti e l'utilizzo dell'acceleratore di scrittura, come descritto in [Abilitare l'acceleratore di scrittura](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)
      4.  Per i diversi tipi di sistemi DBMS, vedere la [documentazione generica sui sistemi DBMS correlati a SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) e la documentazione specifica di DBMS a cui fa riferimento la documentazione generica
      5.  Per SAP HANA, altri dettagli sono riportati in [Configurazioni e operazioni dell'infrastruttura SAP HANA in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
      6.  Non montare mai dischi dati di Azure in una macchina virtuale Linux di Azure tramite l'ID dispositivo. Usare invece l'identificatore univoco universale (UUID). Prestare attenzione quando si usano, ad esempio, strumenti grafici per il montaggio di dischi dati di Azure. Eseguire una doppia verifica delle voci in /etc/fstab per verificare che i dischi siano stati montati usando l'identificatore univoco universale
          1.  Altri dettagli sono disponibili [qui](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#connect-to-the-linux-vm-to-mount-the-new-disk)
   3. Rete
      1.  Testare e valutare l'infrastruttura di rete virtuale e la distribuzione delle applicazioni SAP attraverso o all'interno delle diverse reti virtuali di Azure
          1.  Valutare l'approccio dell'architettura di rete virtuale hub-spoke o la microsegmentazione all'interno di una singola rete virtuale di Azure in base ai fattori seguenti
              1.  Costi dovuti allo scambio di dati tra [reti virtuali di Azure con peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Per i costi, controllare [Prezzi di Rete virtuale](https://azure.microsoft.com/pricing/details/virtual-network/)
              2.  Vantaggio della disconnessione rapida del peering tra reti virtuali di Azure rispetto alla modifica del gruppo di sicurezza di rete per isolare una subnet all'interno di una rete virtuale per i casi in cui le applicazioni o le macchine virtuali ospitate in una subnet della rete virtuale sono diventate un rischio per la sicurezza
              3.  Registrazione e controllo del traffico di rete in maniera centralizzata tra l'ambiente locale, il mondo esterno e il data center virtuale creato in Azure
          2.  Valutare e testare il percorso dei dati tra il livello applicazione SAP e il livello DBMS SAP. 
              1.  Il posizionamento di [appliance virtuali di rete di Azure](https://azure.microsoft.com/solutions/network-appliances/) nel percorso di comunicazione tra il livello applicazione SAP e il livello DBMS di uno dei sistemi SAP NetWeaver, Hybris o S/4HANA basati su SAP non è supportato
              2.  Il posizionamento del livello applicazione SAP e il livello DBMS SAP in reti virtuali di Azure diverse senza peering non è supportato
              3.  Le [regole dei gruppi di sicurezza delle applicazioni e dei gruppi di sicurezza di rete di Azure](https://docs.microsoft.com/azure/virtual-network/security-overview) sono supportate per la definizione di route tra il livello applicazione SAP e il livello DBMS SAP
          3.  Verificare che [Rete accelerata di Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) sia abilitata sulle macchine virtuali usate nel livello applicazione SAP e nel livello DBMS SAP. Tenere presente che per supportare Rete accelerata in Azure sono necessari diversi livelli di sistema operativo:
              1.  Windows Server 2012 R2 o versioni successive
              2.  SUSE Linux 12 SP3 o versioni successive
              3.  RHEL 7.4 o versioni successive
              4.  Oracle Linux 7.5. Se si usa il kernel RHCKL, è necessaria la versione 3.10.0-862.13.1.el7. Se si usa il kernel Oracle UEK, è necessaria la versione 5
          4.   Testare e valutare la latenza di rete tra la macchina virtuale del livello applicazione SAP e la macchina virtuale del livello DBMS in base alle note di supporto SAP [500235](https://launchpad.support.sap.com/#/notes/500235) e [1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Valutare i risultati rispetto alle indicazioni sulla latenza di rete della nota di supporto SAP [1100926](https://launchpad.support.sap.com/#/notes/1100926/E). I risultati devono essere compresi nell'intervallo tra una media e buona latenza di rete. Fa eccezione il traffico tra le macchine virtuali e le istanze Large di HANA, come descritto [qui](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)
          5.   Verificare che le distribuzioni di bilanciamento del carico interno siano configurate per l'uso di Direct Server Return. Questa impostazione riduce la latenza nei casi in cui vengano usati i servizi di bilanciamento del carico interno di Azure per le configurazioni a disponibilità elevata nel livello DBMS
          6.   Se si usa Azure Load Balancer insieme ai sistemi operativi guest Linux, verificare che il parametro di rete Linux **net. IPv4. TCP _timestamps** sia impostato su **0**. Rispetto ai consigli nelle versioni precedenti di SAP Nota [#2382421](https://launchpad.support.sap.com/#/notes/2382421). Nel frattempo, la nota SAP viene aggiornata per riflettere il fatto che il parametro deve essere impostato su 0 per funzionare insieme ai bilanciamenti del carico di Azure.
          7.   Provare a usare il [gruppo di posizionamento di prossimità di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) come descritto nell'articolo gruppi di posizionamento di prossimità di Azure per la latenza di [rete ottimale con le applicazioni SAP](sap-proximity-placement-scenarios.md) per ottenere la latenza di rete ottimale.
   4. Distribuzioni per la disponibilità elevata e il ripristino di emergenza 
      1. Se si distribuisce il livello applicazione SAP senza definire una specifica zona di disponibilità di Azure, verificare che tutte le macchine virtuali che eseguono l'istanza di dialogo SAP o le istanze di middleware di un singolo sistema SAP vengano distribuite in un [set di disponibilità](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). 
         1.   Nel caso in cui non sia richiesta la disponibilità elevata per SAP Central Services e il livello DBMS, queste macchine virtuali possono essere distribuite nello stesso set di disponibilità del livello applicazione SAP
      2. Se si proteggono SAP Central Services e il livello DBMS per la disponibilità elevata con repliche passive, configurare due nodi per SAP Central Services in un unico set di disponibilità separato e due nodi per DBMS in un altro set di disponibilità
      3. Se si esegue la distribuzione in zone di disponibilità di Azure, non è possibile sfruttare i set di disponibilità. È tuttavia necessario assicurarsi di distribuire i nodi attivi e passivi di Central Services in due diverse zone di disponibilità tra cui è stata registrata una latenza minima.
         1.   Tenere presente che è necessario usare il servizio [Azure Load Balancer Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) nel caso in cui si creino cluster di failover Windows o Pacemaker per il livello DBMS e SAP Central Services tra le zone di disponibilità. Il servizio [Load Balancer Basic](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) non può essere usato per distribuzioni a livello di zona 
   5. Impostazioni di timeout
      1. Controllare le tracce per gli sviluppatori di SAP NetWeaver in relazione alle diverse istanze di SAP e assicurarsi che non siano segnalate interruzioni di connessione tra il server di accodamento e i processi di lavoro SAP. Queste interruzioni di connessione possono essere evitate impostando i due parametri del Registro di sistema seguenti:
         1.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000 - vedere anche [questo articolo](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10))
         2.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000 - vedere anche [questo articolo](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) 
      2. Per evitare timeout tra le interfacce utente grafiche di SAP distribuite in locale e i livelli applicazione SAP distribuiti in Azure, verificare se in default.pfl o nel profilo dell'istanza sono impostati i parametri seguenti:
         1.   rdisp/keepalive_timeout = 3600
         2.   rdisp/keepalive = 20
      3. Per evitare l'interferenza delle connessioni stabilite tra il processo di Accodamento SAP e SAP workprocesse, il parametro accodare/encni/set_so_keepalive deve essere impostato su "true". Vedere anche la [Nota SAP #2743751](https://launchpad.support.sap.com/#/notes/2743751)  
      3. Se si usa una configurazione di cluster di failover Windows, assicurarsi che il tempo necessario per reagire in caso di mancata risposta dei nodi sia impostato correttamente per Azure. L'articolo di Microsoft [Tuning Failover Cluster Network Thresholds](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) (Ottimizzazione delle soglie di rete dei cluster di failover) include l'elenco dei parametri e descrive come questi influiscono sulla sensibilità del failover. Supponendo che i nodi del cluster si trovino nella stessa subnet, è necessario modificare i parametri seguenti:
         1.   SameSubNetDelay = 2000
         2.   SameSubNetThreshold = 15
         3.   RoutingHistorylength = 30
4. Testare le procedure per la disponibilità elevata e il ripristino di emergenza
   1. Simulare situazioni di failover arrestando le macchine virtuali (sistema operativo guest Windows) o attivando la modalità di errore grave nei sistemi operativi (sistema operativo guest Linux) per determinare se le configurazioni di failover funzionano come previsto. 
   2. Misurare il tempo necessario per eseguire un failover. Se il tempo è troppo lungo, prendere in considerazione quanto segue:
      1.   Per SUSE Linux, usare i dispositivi SBD anziché l'agente di isolamento di Azure per accelerare il failover
      2.   Per SAP HANA, se è necessario troppo tempo per ricaricare i dati, valutare l'opportunità di effettuare il provisioning di maggiore larghezza di banda di archiviazione
   3. Testare la sequenza di backup/ripristino e ottimizzarla, se necessario. Assicurarsi che siano sufficienti non solo i tempi di backup ma anche quelli di ripristino. Testare quindi anche la procedura di ripristino per verificare che i tempi delle attività siano compresi nei contratti di servizio relativi all'obiettivo del tempo di ripristino, in cui quest'ultimo viene calcolato in base a un processo di ripristino di un database o di una macchina virtuale
   4. Testare l'architettura e la funzionalità del ripristino di emergenza tra aree
5. Controlli di sicurezza
   1.  Testare la validità dell'architettura del controllo degli accessi in base al ruolo (RBAC) di Azure che è stata implementata. L'obiettivo è di separare e limitare l'accesso e le autorizzazioni di team diversi. Ad esempio, i membri del team di SAP Basis devono essere in grado di distribuire macchine virtuali e assegnare dischi dall'archiviazione di Azure a una determinata rete virtuale di Azure, ma non devono essere in grado di creare proprie reti virtuali o modificare le impostazioni di reti virtuali esistenti. D'altro canto, i membri del team di rete non devono essere in grado di distribuire macchine virtuali nelle reti virtuali in cui sono in esecuzione macchine virtuali del livello applicazione SAP e del livello DBMS, né devono essere in grado di modificare gli attributi delle macchine virtuali o addirittura eliminare macchine virtuali o dischi.  
   2.  Verificare che le regole dei [gruppi di sicurezza di rete e dei gruppi di sicurezza delle applicazioni](https://docs.microsoft.com/azure/virtual-network/security-overview) funzionino come previsto e siano in grado di schermare le risorse protette
   3.  Assicurarsi che tutte le risorse che devono essere crittografate lo siano effettivamente. Definire ed eseguire processi per eseguire il backup dei certificati, archiviare e accedere a tali certificati e ripristinare le entità crittografate. 
   4.  Usare [Crittografia dischi di Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq) e/o per i dischi del sistema operativo, dove possibile, da una punto di vista del supporto del sistema operativo
   5.  Verificare che non siano stati usati troppi livelli di crittografia. Non ha molto senso usare Crittografia dischi di Azure e contemporaneamente implementare metodi di Transparent Data Encryption del sistema DBMS
6. Test delle prestazioni
   1.  Nei dati di SAP relativi alle tracce e alle misurazioni, confrontare i migliori 10 report online con l'implementazione corrente, se applicabile 
   2.  Nei dati di SAP relativi alle tracce e alle misurazioni, confrontare i migliori 10 processi batch con l'implementazione corrente, se applicabile 
   3.  Nei dati di SAP relativi alle tracce e alle misurazioni, confrontare i trasferimenti dei dati nel sistema SAP attraverso le interfacce. Concentrarsi sulle interfacce in cui si è certi che il trasferimento ora passa da una posizione all'altra, ad esempio da locale ad Azure 


## <a name="non-production-phase"></a>Fase non di produzione 
In questa fase si presuppone che, dopo l'esito positivo di un progetto pilota o di un modello di verifica, si inizi a distribuire sistemi SAP non di produzione in Azure. Tutte le conoscenze e le esperienze ottenute dai modelli di verifica devono essere adattate a questa distribuzione. Anche tutti i criteri e i passaggi elencati nel modello di verifica si applicano a questo tipo di distribuzioni. In questa fase, in genere si distribuiscono in Azure i sistemi di sviluppo, i sistemi di unit test e i sistemi di test di regressione aziendali. È consigliabile configurare per la disponibilità elevata almeno un sistema non di produzione in una linea applicativa SAP poiché il futuro sistema di produzione avrà questo tipo di configurazione. Durante questa fase è inoltre necessario considerare i passaggi aggiuntivi seguenti:  

1.  Prima di spostare i sistemi dalla piattaforma precedente ad Azure, raccogliere i dati sull'utilizzo delle risorse, come l'utilizzo della CPU, la velocità effettiva di archiviazione e le operazioni di I/O al secondo, soprattutto dalle unità del livello DBMS, ma anche dalle unità del livello applicazione. Misurare anche la latenza di rete e di archiviazione.
2.  Registrare i ritmi temporali di utilizzo della disponibilità dei sistemi. L'obiettivo è di determinare se i sistemi non di produzione devono essere disponibili 24 al giorno, 7 giorni su 7, o se alcuni sistemi non di produzione possono essere arrestati durante alcuni periodi di una settimana o di un mese
3.  Eseguire test e stabilire se si vogliono creare immagini di sistema operativo personalizzate per le macchine virtuali in Azure o se si preferisce usare un'immagine dalla raccolta immagini di Azure. Nel secondo caso, assicurarsi di scegliere l'immagine corretta, in conformità al contratto di supporto con il fornitore del sistema operativo. Per alcuni fornitori di sistemi operativi, le raccolte di Azure offrono immagini di licenza di tipo Bring Your Own License. Per altre immagini di sistema operativo, il supporto è incluso nel prezzo offerto da Azure. Se si decide di creare immagini di sistema operativo personalizzate, è possibile trovare la documentazione in questi articoli:
    1.  È possibile creare un'immagine generalizzata di una macchina virtuale Windows distribuita in Azure in base a [questa documentazione](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
    2.  È possibile creare un'immagine generalizzata di una macchina virtuale Linux distribuita in Azure in base a [questa documentazione](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
3.  Se si usano immagini SUSE e Red Hat Linux dalla raccolta di macchine virtuali di Azure, è necessario usare le immagini per SAP messe a disposizione dai fornitori di Linux nella raccolta di macchine virtuali di Azure
4.  Assicurarsi di soddisfare i requisiti di supporto di SAP in relazione ai contratti di supporto tecnico Microsoft. Per informazioni al riguardo, vedere la nota di supporto SAP [2015553](https://launchpad.support.sap.com/#/notes/2015553). Per le istanze Large di HANA, vedere il documento [Requisiti di onboarding](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-onboarding-requirements)
4.  Assicurarsi di inviare alle persone giuste le [notifiche di manutenzione pianificata](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/), in modo da poter scegliere il tempo di inattività e riavviare in tempo le macchine virtuali
5.  Controllare regolarmente la documentazione di Azure e le presentazioni di Microsoft su canali come [Channel9](https://channel9.msdn.com/) per tenersi aggiornati sulle nuova funzionalità applicabili alle proprie distribuzioni
6.  Controllare le note SAP correlate ad Azure, ad esempio la nota di supporto [1928533](https://launchpad.support.sap.com/#/notes/1928533) per nuovi SKU di macchine virtuali o una nuova versione di sistema operativo o DBMS supportata. Confrontare i nuovi tipi di macchina virtuale con i tipi precedenti in termini di prezzo, in modo da poter distribuire macchine virtuali con il miglior rapporto prezzo/prestazioni
7.  Convalidare le risorse nelle note di supporto SAP, SAP HANA directory hardware e SAP PAM per assicurarsi che non siano state apportate modifiche alle macchine virtuali supportate per Azure, versioni del sistema operativo supportate in tali macchine virtuali e versioni supportate di SAP e DBMS
8.  Controllare [qui](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) se sono disponibili nuovi SKU certificati per HANA in Azure e confrontare i prezzi con quelli pianificati e infine apportare eventuali modifiche per ottenere le unità con il miglior rapporto prezzo/prestazioni 
9.  Adattare gli script di distribuzione per sfruttare i nuovi tipi di macchina virtuale e includere le nuove funzionalità di Azure desiderate
10. Dopo la distribuzione dell'infrastruttura, testare e valutare la latenza di rete tra la macchina virtuale del livello applicazione SAP e la macchina virtuale del livello DBMS in base alle note di supporto SAP [500235](https://launchpad.support.sap.com/#/notes/500235) e [1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Valutare i risultati rispetto alle indicazioni sulla latenza di rete della nota di supporto SAP [1100926](https://launchpad.support.sap.com/#/notes/1100926/E). I risultati devono essere compresi nell'intervallo tra una media e buona latenza di rete. Fa eccezione il traffico tra le macchine virtuali e le istanze Large di HANA come descritto [qui](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance). Assicurarsi che nessuna delle restrizioni indicate in [Considerazioni sulla distribuzione DBMS di macchine virtuali di Azure per un carico di lavoro SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#azure-network-considerations) e [Configurazioni e operazioni dell'infrastruttura SAP HANA in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) siano applicabili alla propria distribuzione
11. Assicurarsi che le macchine virtuali vengano distribuite usando il [gruppo di posizionamento di prossimità di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) corretto, come descritto nell'articolo [gruppi di posizionamento di prossimità di Azure per la latenza di rete ottimale con le applicazioni SAP](sap-proximity-placement-scenarios.md)
11. Eseguire tutti gli altri controlli, come indicato nella fase di Modello di verifica prima di applicare il carico di lavoro
12. Quando viene applicato il carico di lavoro, registrare l'utilizzo delle risorse dei sistemi in Azure e confrontarlo con i risultati ottenuti dalla piattaforma precedente. Se si notano differenze significative, regolare il dimensionamento delle macchine virtuali per le future distribuzioni. Tenere presente che, in caso di riduzione delle dimensioni, verranno ridotte anche la larghezza di banda di archiviazione e quella di rete di una macchina virtuale:
    1.  [Dimensioni per le macchine virtuali Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json): 
    2.  [Dimensioni delle macchine virtuali Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. Lavorare ai processi e alle funzionalità di copia dei sistemi. L'obiettivo è di semplificare la copia di un sistema di sviluppo o di test in modo che i team di progetto possano ottenere rapidamente i nuovi sistemi. Prendere in considerazione [SAP LaMa](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+Landscape+Management+%28SAP+LaMa%29+at+a+Glance) come strumento per eseguire queste attività.
14. Ottimizzare e perfezionare gli accessi, le autorizzazioni e i processi in base al ruolo di Azure per il proprio team per definire una corretta separazione dei compiti e al tempo stesso consentire a tutti i team di eseguire le proprie attività nell'infrastruttura di Azure.
15. Provare, testare e documentare le procedure per la disponibilità elevata e il ripristino di emergenza per consentire al personale di eseguire tali attività. Identificare i punti deboli e adattare le nuove funzionalità di Azure che si è deciso di integrare nelle proprie distribuzioni

 
## <a name="production-preparation-phase"></a>Fase di preparazione alla produzione 
In questa fase si vuole raccogliere tutte le esperienze e le conoscenze acquisite durante le distribuzioni non di produzione per applicarle in futuro alle distribuzioni di produzione. Oltre alle operazioni descritte nelle fasi precedenti, è necessario preparare l'attività di trasferimento dei dati tra la posizione di hosting corrente e Azure. 

1.  Prima di passare ad Azure, eseguire tutti gli aggiornamenti delle versioni SAP necessari per i sistemi di produzione
2.  Accordarsi con i proprietari dell'azienda riguardo ai test funzionali e aziendali da eseguire dopo la migrazione del sistema di produzione
    1.  Verificare che tutti questi test vengano eseguiti con i sistemi di origine nella posizione di hosting corrente. È opportuno evitare che i test vengano eseguiti per la prima volta dopo che il sistema è stato spostato in Azure
2.  Testare il processo di migrazione dei sistemi di produzione in Azure. Se non si spostano tutti i sistemi di produzione in Azure nello stesso intervallo di tempo, creare gruppi di sistemi di produzione che devono trovarsi nella stessa posizione di hosting. Provare e testare la migrazione dei dati. Di seguito sono elencati i metodi più diffusi:
    1.  Usare i metodi DBMS, ad esempio backup e ripristino in combinazione con SQL Server AlwaysOn, HANA System Replication o Log shipping per effettuare il seeding del contenuto di database e sincronizzarlo in Azure
    2.  Usare Backup/Ripristino per i database di dimensioni ridotte
    3.  Usare SAP Migration Monitor implementato nello strumento SAP SWPM per eseguire migrazioni eterogenee
    4.  Usare il processo [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) se si vuole combinare la migrazione con un aggiornamento di versione SAP. Tenere presente che non tutte le combinazioni tra DBMS di origine e di destinazione sono supportate. Per altre informazioni, vedere le note di supporto SAP specifiche per le diverse versioni di DMO. Ad esempio, [Database Migration Option (DMO) of SUM 2.0 SP04](https://launchpad.support.sap.com/#/notes/2644872) (Opzione di migrazione del database di SUM 2.0 SP04)
    5.  Verificare se il trasferimento dei dati attraverso Internet è migliore di quello tramite ExpressRoute in termini di velocità effettiva nel caso in cui sia necessario spostare i backup o file di esportazione di SAP. In caso di spostamento dei dati attraverso Internet, può essere opportuno modificare alcune regole dei gruppi di sicurezza di rete o dei gruppi di sicurezza delle applicazioni che è necessario predisporre per sistemi di produzione futuri
3.  Prima di spostare i sistemi dalla piattaforma precedente ad Azure, raccogliere i dati sull'utilizzo delle risorse, come l'utilizzo della CPU, la velocità effettiva di archiviazione e le operazioni di I/O al secondo, soprattutto dalle unità del livello DBMS, ma anche dalle unità del livello applicazione. Misurare anche la latenza di rete e di archiviazione.
4.  Convalidare le risorse nelle note di supporto SAP, SAP HANA directory hardware e SAP PAM per assicurarsi che non siano state apportate modifiche alle macchine virtuali supportate per Azure, versioni del sistema operativo supportate in tali macchine virtuali e versioni supportate di SAP e DBMS 
4.  Adattare gli script di distribuzione in base alle modifiche più recenti che si è deciso di apportare per i tipi di macchina virtuale e le funzionalità di Azure
5.  Dopo la distribuzione dell'infrastruttura e delle applicazioni, passare in rassegna questa serie di controlli:
    1.  Verificare che siano stati distribuiti i tipi di macchina virtuale corretti con le dimensioni di archiviazione e gli attributi corretti
    2.  Controllare che le macchine virtuali abbiano le versioni di sistema operativo e le patch corrette e siano uniformi
    3.  Controllare che la protezione avanzata delle macchine virtuali sia uniforme e configurata come richiesto
    4.  Controllare che siano state installate e distribuite le versioni e le patch corrette delle applicazioni
    5.  Verificare che le macchine virtuali siano state distribuite nei set di disponibilità di Azure come pianificato
    6.  Verificare che Archiviazione Premium di Azure sia stata usata per i dischi sensibili alla latenza o dove è richiesto un [contratto di servizio del 99,9% per singola macchina virtuale](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)
    7.  Verificare che l'acceleratore di scrittura di Azure sia stato distribuito correttamente
        1.  Controllare che, nella macchina virtuale, gli spazi di archiviazione o i set di striping siano stati creati correttamente nei dischi che necessitano del supporto dell'acceleratore di scrittura di Azure
            1.  Vedere [Configurare RAID software in Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
            2.  Vedere [Configurare LVM in una macchina virtuale Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)
    8.  Controllare che l'archiviazione [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) sia stata usata in modo esclusivo
    9.  Verificare che macchine virtuali siano state distribuite nelle zone di disponibilità e nei set di disponibilità corretti
    10. Assicurarsi che [Rete accelerata di Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) sia abilitata sulle macchine virtuali usate nel livello applicazione SAP e nel livello DBMS SAP
    11. Verificare che non siano presenti [appliance virtuali di rete di Azure](https://azure.microsoft.com/solutions/network-appliances/) nel percorso di comunicazione tra il livello applicazione SAP e il livello DBMS di uno dei sistemi SAP NetWeaver, Hybris o basati su S/4HANA
    12. Controllare che le regole dei gruppi di sicurezza di rete e dei gruppi di sicurezza delle applicazioni consentano le comunicazioni in conformità ai requisiti e alla pianificazione e blocchino le comunicazioni dove necessario
    13. Verificare che le impostazioni di timeout descritte in precedenza siano state impostate correttamente
    14. Assicurarsi che le macchine virtuali vengano distribuite usando il [gruppo di posizionamento di prossimità di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) corretto, come descritto nell'articolo [gruppi di posizionamento di prossimità di Azure per la latenza di rete ottimale con le applicazioni SAP](sap-proximity-placement-scenarios.md)
    15. Testare e valutare la latenza di rete tra la macchina virtuale del livello applicazione SAP e la macchina virtuale del livello DBMS in base alle note di supporto SAP [500235](https://launchpad.support.sap.com/#/notes/500235) e [1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Valutare i risultati rispetto alle indicazioni sulla latenza di rete della nota di supporto SAP [1100926](https://launchpad.support.sap.com/#/notes/1100926/E). I risultati devono essere compresi nell'intervallo tra una media e buona latenza di rete. Fa eccezione il traffico tra le macchine virtuali e le istanze Large di HANA, come descritto [qui](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)
    15. Controllare che la crittografia sia stata distribuita dove necessario e con il metodo di crittografia necessario
    16. Verificare se le interfacce e le altre applicazioni possono connettersi alla nuova infrastruttura distribuita
6.  Creare un playbook per reagire alla manutenzione pianificata di Azure. Definire l'ordine dei sistemi e delle macchine virtuali da riavviare in caso di manutenzione pianificata
    

## <a name="go-live-phase"></a>Fase operativa
Per la fase operativa, è necessario assicurarsi di seguire i playbook che sono stati sviluppati nelle fasi precedenti. Eseguire i passaggi sottoposti a test e training. Non accettare modifiche dell'ultimo minuto alle configurazioni e al processo. Applicare anche le misure seguenti:

1. Verificare il corretto funzionamento del monitoraggio del portale di Azure e di altri strumenti di monitoraggio.  È consigliabile usare gli strumenti Perfmon (Windows) o SAR (Linux): 
    1.  Contatori CPU 
        1.  Tempo medio utilizzo CPU: totale (tutte le CPU)
        2.  Tempo medio utilizzo CPU: ogni singolo processore (quindi 128 processori su macchina virtuale m128)
        3.  Tempo CPU kernel: ogni singolo processore
        4.  Tempo CPU utente: ogni singolo processore
    5.  Memoria 
        1.  Memoria libera
        2.  Pagine in ingresso/sec
        3.  Pagine in uscita/sec
    4.  Disco 
        1.  kB lettura disco/sec: per singola attività 
        2.  Letture disco/sec: per singola attività
        3.  ms lettura disco/lettura: per singola attività
        4.  kB scrittura disco/sec: per singola attività 
        5.  Scritture disco/sec: per singola attività
        6.  ms scrittura disco/scrittura: per singola attività
    5.  Rete 
        1.  Pacchetti di rete in ingresso/sec
        2.  Pacchetti di rete in uscita/sec
        3.  kB di rete in ingresso/sec
        4.  kB di rete in uscita/sec 
2.  Dopo la migrazione dei dati, eseguire tutti i test di convalida concordati con i proprietari dell'azienda. Accettare i risultati dei test di convalida solo se i risultati riguardano i sistemi di origine originali
3.  Controllare se le interfacce funzionano e se altre applicazioni possono comunicare con i nuovi sistemi di produzione distribuiti
4.  Verificare il sistema di trasporto e correzione tramite SAP transaction STMS
5.  Eseguire i backup dei database dopo che il sistema è stato rilasciato per la produzione
6.  Eseguire i backup delle macchine virtuali del livello applicazione SAP dopo che il sistema è stato rilasciato per la produzione
7.  Per i sistemi SAP che non fanno parte della fase operativa corrente, ma che comunicano con i sistemi SAP di cui è stata eseguita la migrazione in Azure in questa fase operativa, è necessario reimpostare il buffer dei nomi host in SM51. Questo passaggio consente di eliminare i precedenti indirizzi IP memorizzati nella cache associati ai nomi delle istanze di applicazione che sono state spostate in Azure  


## <a name="post-production"></a>Post-produzione
Le attività di questa fase riguardano principalmente il monitoraggio, il funzionamento e l'amministrazione del sistema. Da un punto di vista SAP, sono previste le normali attività che era necessario eseguire nella precedente posizione di hosting. Per Azure è opportuno eseguire le attività seguenti:

1. Analizzare le fatture di Azure per i sistemi con tariffazione elevata
2. Ottimizzare il rapporto prezzo/prestazioni sul lato macchine virtuali e sul lato archiviazione
3. Ottimizzare i tempi di arresto dei sistemi  


## <a name="next-steps"></a>Fasi successive
Consultare la documentazione:

- [Guida alla pianificazione e all'implementazione di Macchine virtuali di Azure per SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Distribuzione di Macchine virtuali di Azure per SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Considerazioni sulla distribuzione DBMS di macchine virtuali di Azure per un carico di lavoro SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

