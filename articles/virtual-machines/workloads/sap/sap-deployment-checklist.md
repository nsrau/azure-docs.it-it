---
title: Elenco di controllo per la pianificazione e la distribuzione di carichi di lavoro SAP | Microsoft Docs
description: Elenco di controllo per la pianificazione delle distribuzioni del carico di lavoro SAP in Azure e la distribuzione dei carichi
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/08/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 097429e9c761d447a7164c813a6c84d3f07f0ab6
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73891411"
---
# <a name="sap-workloads-on-azure-planning-and-deployment-checklist"></a>Carichi di lavoro SAP in Azure: elenco di controllo di pianificazione e distribuzione

Questo elenco di controllo è progettato per i clienti che trasferiscono le applicazioni SAP NetWeaver, S/4HANA e hybris nell'infrastruttura distribuita come servizio di Azure. Per tutta la durata del progetto, un cliente e/o un partner SAP dovrebbe rivedere l'elenco di controllo. È importante notare che molti controlli vengono completati all'inizio del progetto e durante la fase di pianificazione. Al termine della distribuzione, le modifiche semplici nell'infrastruttura di Azure distribuita o nelle versioni del software SAP possono diventare complesse.

Esaminare l'elenco di controllo in corrispondenza delle attività cardine principali durante il progetto. Questa operazione consentirà di rilevare piccoli problemi prima che diventino problemi di grandi dimensioni. Si avrà anche tempo sufficiente per riprogettare e testare le modifiche necessarie. Non considerare questo elenco di controllo completato. A seconda della situazione, potrebbe essere necessario eseguire molti più controlli.

L'elenco di controllo non include attività indipendenti da Azure. Ad esempio, le interfacce dell'applicazione SAP cambiano durante un passaggio alla piattaforma Azure o a un provider di hosting.

Questo elenco di controllo può essere usato anche per i sistemi già distribuiti. È possibile che siano state aggiunte nuove funzionalità, ad esempio acceleratore di scrittura e zone di disponibilità, e che i nuovi tipi di VM siano stati aggiunti dopo la distribuzione di. È quindi utile esaminare periodicamente l'elenco di controllo per assicurarsi di essere a conoscenza delle nuove funzionalità della piattaforma Azure.

## <a name="project-preparation-and-planning-phase"></a>Fase di preparazione e pianificazione del progetto
Durante questa fase si pianifica la migrazione del carico di lavoro SAP alla piattaforma Azure. Come minimo, durante questa fase è necessario creare i documenti seguenti e definire e discutere gli elementi seguenti della migrazione:

1. Documento di progettazione di alto livello. Questo documento deve contenere:
    - Inventario corrente di componenti e applicazioni SAP e inventario di applicazioni di destinazione per Azure.
    - Una matrice di assegnazione di responsabilità (RACI) che definisce le responsabilità e le assegnazioni delle parti coinvolte. Inizia a un livello elevato e lavora a livelli più granulari durante la pianificazione e le prime distribuzioni.
    - Architettura della soluzione di alto livello.
    - Decisione su quali aree di Azure distribuire. Vedere l' [elenco delle aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/). Per informazioni sui servizi disponibili in ogni area, vedere [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/).
    - Un'architettura di rete per la connessione da locale ad Azure. Inizia a familiarizzare con il [progetto Virtual Datacenter per Azure](https://docs.microsoft.com/azure/architecture/vdc/).
    - Principi di sicurezza per l'esecuzione di dati aziendali ad alto effetto in Azure. Per informazioni sulla sicurezza dei dati, iniziare dalla [documentazione sulla sicurezza di Azure](https://docs.microsoft.com/azure/security/).
2.  Documento di progettazione tecnica. Questo documento deve contenere:
    - Diagramma a blocchi per la soluzione.
    - Dimensionamento dei componenti di calcolo, archiviazione e rete in Azure. Per informazioni sul ridimensionamento delle macchine virtuali di Azure, vedere la [Nota di supporto sap #1928533](https://launchpad.support.sap.com/#/notes/1928533).
    - Architettura della continuità aziendale e del ripristino di emergenza.
    - Informazioni dettagliate sulle versioni del pacchetto di supporto per sistema operativo, database, kernel e SAP. Non è necessariamente vero che ogni versione del sistema operativo supportata da SAP NetWeaver o S/4HANA è supportata nelle macchine virtuali di Azure. Lo stesso vale per le versioni DBMS. Controllare le seguenti origini per allineare e, se necessario, aggiornare le versioni di SAP, le versioni DBMS e le versioni del sistema operativo per garantire il supporto di SAP e Azure. È necessario disporre di combinazioni di versione supportate da SAP e Azure per ottenere supporto completo da SAP e Microsoft. Se necessario, è necessario pianificare l'aggiornamento di alcuni componenti software. Altre informazioni sui software SAP, OS e DBMS supportati sono disponibili qui:
        - [Note sul supporto SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Questa nota definisce le versioni minime del sistema operativo supportate nelle VM di Azure. Definisce inoltre le versioni minime del database necessarie per la maggior parte dei database non HANA. Infine, fornisce il ridimensionamento SAP per i tipi di VM di Azure supportati da SAP.
        - [Note sul supporto SAP #2039619](https://launchpad.support.sap.com/#/notes/2039619). Questa nota definisce la matrice di supporto Oracle per Azure. Oracle supporta solo Windows e Oracle Linux come sistemi operativi guest in Azure per carichi di lavoro SAP. Questa istruzione di supporto è valida anche per il livello dell'applicazione SAP che esegue le istanze di SAP. Tuttavia, Oracle non supporta la disponibilità elevata per i servizi centrali SAP in Oracle Linux tramite pacemaker. Se è necessaria la disponibilità elevata per ASC in Oracle Linux, è necessario usare il gruppo di protezione per la protezione dei dati per Linux. Per informazioni dettagliate sulla certificazione SAP, vedere la nota sul supporto SAP [#1662610-dettagli del supporto per il gruppo di protezione](https://launchpad.support.sap.com/#/notes/1662610)dei dati per l'it per Linux. Per Windows, la soluzione supportata da SAP Windows Server failover clustering per i servizi centrali SAP è supportata insieme a Oracle come livello DBMS.
        - [Note sul supporto SAP #2235581](https://launchpad.support.sap.com/#/notes/2235581). Questa nota fornisce la matrice di supporto per SAP HANA in versioni diverse del sistema operativo.
        - Le VM di Azure supportate da SAP HANA e le [istanze large di Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) sono elencate nel [sito Web SAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).
        - [Matrice di disponibilità del prodotto SAP](https://support.sap.com/en/).
        - Note SAP per altri prodotti specifici di SAP.     
    - Per i sistemi di produzione SAP sono consigliate rigorose soluzioni a tre livelli. Non è consigliabile combinare ASC e server app in una macchina virtuale. L'uso di configurazioni di cluster a più SID per i servizi SAP Central è supportato nei sistemi operativi guest Windows in Azure. Questa configurazione, tuttavia, non è supportata per i servizi SAP Central nei sistemi operativi Linux in Azure. È possibile trovare la documentazione per lo scenario del sistema operativo guest Windows in questi articoli:
        - [Disponibilità elevata multi-SID dell'istanza SAP ASCS/SCS con Windows Server Failover Clustering e i dischi condivisi in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk)
        - [Disponibilità elevata multi-SID dell'istanza ASCS/SCS di SAP con Windows Server Failover Clustering e condivisione file in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share)
    - Architettura di disponibilità elevata e ripristino di emergenza.
        - In base a RTO e RPO, è necessario definire l'aspetto dell'architettura di disponibilità elevata e ripristino di emergenza.
        - Per una disponibilità elevata all'interno di una zona, controllare il sistema DBMS da offrire in Azure. La maggior parte dei pacchetti DBMS offre metodi sincroni di una hot standby sincrona, consigliata per i sistemi di produzione. Vedere anche la documentazione relativa a SAP per database diversi, a partire dalle [considerazioni per la distribuzione DBMS di macchine virtuali di Azure per carichi di lavoro SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) e documenti correlati.
           L'uso di Windows Server failover clustering con una configurazione di dischi condivisi per il livello DBMS come, ad esempio, [descritto per SQL Server](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017), non è supportato. Usare invece soluzioni come:
           - [SQL Server Always On](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups)
           - [Oracle Data Guard](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
           - [Replica di sistema HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        - Per il ripristino di emergenza tra aree di Azure, esaminare le soluzioni offerte dai diversi fornitori di sistemi DBMS. La maggior parte di essi supporta la replica asincrona o log shipping.
        - Per il livello dell'applicazione SAP, determinare se verranno eseguiti i sistemi di test di regressione aziendale, che idealmente sono repliche delle distribuzioni di produzione, nella stessa area di Azure o nell'area di ripristino di emergenza. Nel secondo caso, è possibile definire come destinazione il sistema di regressione aziendale come destinazione del ripristino di emergenza per le distribuzioni di produzione.
        - Se si decide di non collocare i sistemi non di produzione nel sito di ripristino di emergenza, esaminare Azure Site Recovery come metodo per replicare il livello dell'applicazione SAP nell'area di ripristino di emergenza di Azure. Per altre informazioni, vedere la pagina relativa alla [configurazione del ripristino di emergenza per una distribuzione di app SAP NetWeaver](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)a più livelli.
        - Se si decide di usare una configurazione HADR combinata usando [zone di disponibilità di Azure](https://docs.microsoft.com/azure/availability-zones/az-overview), acquisire familiarità con le aree di Azure in cui zone di disponibilità sono disponibili. Prendere in considerazione anche le restrizioni che possono essere introdotte dalle latenze di rete più elevate tra due zone di disponibilità.  
3.  Inventario di tutte le interfacce SAP (SAP e non SAP).
4.  Progettazione dei servizi di base. Questa progettazione deve includere gli elementi seguenti:
    - Progettazione Active Directory e DNS.
    - Topologia di rete in Azure e assegnazione di sistemi SAP diversi.
    - Struttura di [accesso basata sui ruoli](https://docs.microsoft.com/azure/role-based-access-control/overview) per i team che gestiscono l'infrastruttura e le applicazioni SAP in Azure.
    - Topologia del gruppo di risorse.
    - [Strategia di assegnazione di tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#tags-and-billing).
    - Convenzioni di denominazione per le macchine virtuali e altri componenti dell'infrastruttura e/o nomi logici.
5.  Contratto Microsoft supporto tecnico Premier. Identificare Microsoft Technical Account Manager (TAM). Per i requisiti di supporto SAP, vedere la [Nota di supporto sap #2015553](https://launchpad.support.sap.com/#/notes/2015553).
6.  Il numero di sottoscrizioni di Azure e la quota di core per le sottoscrizioni. [Aprire le richieste di supporto per aumentare le quote delle sottoscrizioni di Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) in base alle esigenze.
7.  Riduzione dei dati e piano di migrazione dei dati per la migrazione dei dati SAP in Azure. Per i sistemi SAP NetWeaver, SAP offre linee guida su come limitare il volume di grandi quantità di dati. Vedere [questa guida SAP](https://help.sap.com/http.svc/rc/2eb2fba8f8b1421c9a37a8d7233da545/7.0/en-US/Data_Management_Guide_Version_70E.PDF) sulla gestione dei dati nei sistemi SAP ERP. Parte del contenuto si applica anche ai sistemi NetWeaver e S/4HANA in generale.
8.  Approccio di distribuzione automatizzato. L'obiettivo dell'automazione delle distribuzioni di infrastruttura in Azure consiste nel distribuire in modo deterministico e ottenere risultati deterministici. Molti clienti usano gli script basati su PowerShell o sull'interfaccia della riga di comando. Esistono tuttavia diverse tecnologie open source che è possibile usare per distribuire l'infrastruttura di Azure per SAP e persino installare il software SAP. È possibile trovare esempi su GitHub:
    - [Distribuzioni SAP automatizzate nel cloud di Azure](https://github.com/Azure/sap-hana)
    - [Installazione di SAP HANA](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  Definire una normale cadenza di revisione della progettazione e della distribuzione tra il cliente, l'integratore di sistemi, Microsoft e altre parti coinvolte.

 
## <a name="pilot-phase-strongly-recommended"></a>Fase pilota (scelta consigliata)
 
È possibile eseguire un progetto pilota prima o durante la pianificazione e la preparazione del progetto. È anche possibile usare la fase pilota per testare gli approcci e le progettazioni effettuate durante la fase di pianificazione e preparazione. Ed è possibile espandere la fase pilota per renderlo un modello di prova reale.

Si consiglia di configurare e convalidare una soluzione HADR completa e la progettazione della sicurezza durante una distribuzione pilota. Alcuni clienti eseguono test di scalabilità durante questa fase. Gli altri clienti usano le distribuzioni dei sistemi SAP sandbox come fase pilota. Si presuppone che sia già stato identificato un sistema di cui si vuole eseguire la migrazione ad Azure per il progetto pilota.

1. Ottimizza il trasferimento dei dati in Azure. La scelta ottimale dipende molto dallo scenario specifico. Il trasferimento da locale a [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) è più veloce se il circuito ExpressRoute dispone di larghezza di banda sufficiente. In altri scenari, il trasferimento tramite Internet è più veloce.
2. Per una migrazione della piattaforma SAP eterogenea che prevede l'esportazione e l'importazione di dati, testare e ottimizzare le fasi di esportazione e importazione. Per migrazioni di grandi dimensioni in cui SQL Server è la piattaforma di destinazione, è possibile trovare [indicazioni](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070). È possibile usare Migration monitor/SWPM se non è necessario un aggiornamento combinato della versione. È possibile usare il processo [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) quando si combina la migrazione con un aggiornamento della versione SAP. A tale scopo, è necessario soddisfare alcuni requisiti per la combinazione di piattaforma DBMS di origine e di destinazione. Questo processo è documentato nell' [opzione di migrazione del database (DMO) di SUM 2,0 SP03](https://launchpad.support.sap.com/#/notes/2631152).
   1.  Esporta nell'origine, esporta il caricamento del file in Azure e importa le prestazioni. Ingrandisci sovrapposizione tra l'esportazione e l'importazione.
   2.  Valutare il volume del database sulle piattaforme di destinazione e destinazione ai fini del dimensionamento dell'infrastruttura.
   3.  Convalidare e ottimizzare la tempistica.
1. Convalida tecnica.
   1. Tipi di VM.
        - Esaminare le risorse nelle note di supporto SAP, nella directory dell'hardware SAP HANA e in SAP PAM. Assicurarsi che non siano state apportate modifiche alle macchine virtuali supportate per Azure, le versioni del sistema operativo supportate per questi tipi di VM e le versioni SAP e DBMS supportate.
        - Verificare nuovamente il dimensionamento dell'applicazione e dell'infrastruttura distribuite in Azure. Se si stanno comportando le applicazioni esistenti, è spesso possibile derivare i succhi necessari dall'infrastruttura usata e dalla [pagina Web di benchmark SAP](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) e confrontarli con i numeri di SAP elencati nella [Nota di supporto SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Tenere presente anche [questo articolo sulle valutazioni di SAP](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208) .
        - Valutare e testare il dimensionamento delle macchine virtuali di Azure in relazione alla massima velocità effettiva di archiviazione e alla velocità effettiva di rete dei tipi di VM scelti durante la fase di pianificazione. Qui è possibile trovare i dati:
           -  [Dimensioni per le macchine virtuali Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json): È importante considerare il *numero massimo di velocità effettiva dei dischi non memorizzati nella cache* per il dimensionamento.
           -  [Dimensioni delle macchine virtuali Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). È importante considerare il *numero massimo di velocità effettiva dei dischi non memorizzati nella cache* per il dimensionamento.
   2. Archiviazione.
        - Come minimo, usare l' [archiviazione di Azure SDD standard](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) per le macchine virtuali che rappresentano i livelli dell'applicazione SAP e per la distribuzione di sistemi DBMS che non sono sensibili alle prestazioni.
        - In generale, non è consigliabile usare i [dischi di Azure HDD standard](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd).
        - Usare [archiviazione Premium di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) per tutte le macchine virtuali DBMS che sono sensibili alle prestazioni in remoto.
        - Usare [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/).
        - Usare l'acceleratore di scrittura di Azure per le unità di log DBMS con serie M. Tenere presente i limiti e l'utilizzo di acceleratore di scrittura, come documentato in [acceleratore di scrittura](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).
        - Per i diversi tipi di DBMS, consultare la [documentazione di DBMS generica relativa a SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) e la documentazione specifica di DBMS a cui fa riferimento il documento generico.
        - Per altre informazioni sulle SAP HANA, vedere [SAP Hana configurazioni e operazioni dell'infrastruttura in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
        - Non montare mai dischi dati di Azure in una macchina virtuale Linux di Azure tramite l'ID dispositivo. Usare invece l'identificatore univoco universale (UUID). Prestare attenzione quando si usano, ad esempio, strumenti grafici per il montaggio di dischi dati di Azure. Controllare le voci in/etc/fstab per assicurarsi che l'UUID venga usato per montare i dischi. Altre informazioni sono disponibili in [questo articolo](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#connect-to-the-linux-vm-to-mount-the-new-disk).
   3. Rete.
        - Testare e valutare l'infrastruttura di rete virtuale e la distribuzione delle applicazioni SAP nelle diverse reti virtuali di Azure.
        -  Valutare l'approccio dell'architettura di rete virtuale Hub e spoke o l'approccio di microsegmentazione all'interno di una singola rete virtuale di Azure. Basa la valutazione su:
               1. Costi dello scambio di dati tra [reti virtuali di Azure con peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Per informazioni sui costi, vedere [prezzi di rete virtuale](https://azure.microsoft.com/pricing/details/virtual-network/).
               2. Vantaggi di una disconnessione rapida del peering tra reti virtuali di Azure anziché modificare il gruppo di sicurezza di rete per isolare una subnet in una rete virtuale. Questa valutazione si verifica nei casi in cui le applicazioni o le macchine virtuali ospitate in una subnet della rete virtuale sono diventati un rischio per la sicurezza.
                3. Registrazione centrale e controllo del traffico di rete tra l'ambiente locale, il mondo esterno e il data center virtuale creato in Azure.
        - Valutare e testare il percorso dei dati tra il livello applicazione SAP e il livello DBMS di SAP.
            -  Il posizionamento delle [appliance virtuali di rete di Azure](https://azure.microsoft.com/solutions/network-appliances/) nel percorso di comunicazione tra l'applicazione SAP e il livello DBMS dei sistemi SAP basati su SAP NetWeaver, hybris o S/4HANA non è supportato.
            -  La posizione del livello dell'applicazione SAP e del sistema DBMS SAP in diverse reti virtuali di Azure con peering non è supportata.
            -  È possibile usare le regole del gruppo di sicurezza delle [applicazioni e del gruppo di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/security-overview) per definire le route tra il livello applicazione SAP e il livello DBMS di SAP.
        - Assicurarsi che la [rete accelerata di Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) sia abilitata nelle macchine virtuali usate nel livello applicazione SAP e nel livello DBMS di SAP. Tenere presente che per supportare Rete accelerata in Azure sono necessari diversi livelli di sistema operativo:
            - Windows Server 2012 R2 o versione successiva.
            - SUSE Linux 12 SP3 o versione successiva.
            - RHEL 7,4 o versione successiva.
            - Oracle Linux 7.5. Se si usa il kernel RHCKL, è necessario rilasciare 3.10.0-862.13.1. EL7. Se si usa il kernel Oracle UEK, è richiesta la versione 5.
        - Testare e valutare la latenza di rete tra le macchine virtuali del livello applicazione SAP e le VM DBMS in base alle note di supporto SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) e [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Valutare i risultati in base alle indicazioni sulla latenza di rete nella [Nota del supporto SAP #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). La latenza di rete deve essere nell'intervallo moderato o valido. Le eccezioni si applicano al traffico tra le macchine virtuali e le unità di istanze large di HANA, come descritto in [questo articolo](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance).
        - Assicurarsi che le distribuzioni di ILB siano configurate per l'uso di Direct Server Return. Questa impostazione consente di ridurre la latenza quando Azure gli ILB viene usato per le configurazioni a disponibilità elevata nel livello DBMS.
        - Se si usa Azure Load Balancer insieme ai sistemi operativi guest Linux, verificare che il parametro di rete Linux **net. IPv4. tcp_timestamps** sia impostato su **0**. Questa raccomandazione è in conflitto con le raccomandazioni nelle versioni precedenti di [SAP nota #2382421](https://launchpad.support.sap.com/#/notes/2382421). La nota SAP è ora aggiornata per indicare che questo parametro deve essere impostato su **0** per funzionare con i bilanciamenti del carico di Azure.
        - Per ottenere una latenza di rete ottimale, provare a usare i [gruppi di posizionamento prossimità di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) Per altre informazioni, vedere [gruppi di posizionamento di prossimità di Azure per la latenza di rete ottimale con le applicazioni SAP](sap-proximity-placement-scenarios.md).
   4. Distribuzioni a disponibilità elevata e di ripristino di emergenza.
        - Se si distribuisce il livello applicazione SAP senza definire una zona di disponibilità di Azure specifica, assicurarsi che tutte le macchine virtuali che eseguono istanze di finestra di dialogo SAP o istanze middleware di un singolo sistema SAP vengano distribuite in un [set di disponibilità](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability).
        - Se non è necessaria la disponibilità elevata per i servizi SAP Central e DBMS, è possibile distribuire queste macchine virtuali nello stesso set di disponibilità del livello applicazione SAP.
        - Se si proteggono i servizi SAP Central e il livello DBMS per la disponibilità elevata tramite la replica passiva, inserire i due nodi per i servizi SAP Central in un set di disponibilità separato e i due nodi DBMS in un altro set di disponibilità.
        - Se si distribuisce in zone di disponibilità di Azure, non è possibile usare i set di disponibilità. Tuttavia, è necessario assicurarsi di distribuire i nodi di servizi centrali attivi e passivi in due zone di disponibilità diversi. Usare zone di disponibilità con la latenza più bassa tra di essi.
          Tenere presente che è necessario usare Load Balancer Standard di [Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) per il caso d'uso della creazione di cluster di failover di Windows o pacemaker per il livello DBMS e servizi centrali SAP in zone di disponibilità. Non è possibile usare [Load Balancer di base](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) per le distribuzioni di zona.
   5. Impostazioni di timeout.
        - Controllare le tracce per sviluppatori SAP NetWeaver delle istanze di SAP per assicurarsi che non vi siano interruzioni di connessione tra il server di Accodamento e i processi di lavoro SAP. È possibile evitare queste interruzioni di connessione impostando questi due parametri del registro di sistema:
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000. Per ulteriori informazioni, vedere [KeepAliveTime](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)).
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000. Per ulteriori informazioni, vedere [keepAliveInterval](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)).
        - Per evitare i timeout GUI tra le interfacce GUI SAP locali e i livelli dell'applicazione SAP distribuiti in Azure, verificare se questi parametri sono impostati nel profilo predefinito. PFL o nell'istanza:
            - rdisp/keepalive_timeout = 3600
            - rdisp/keepalive = 20
        - Per evitare l'interferenza delle connessioni stabilite tra il processo di Accodamento SAP e i processi di lavoro SAP, è necessario impostare il parametro **accodare/encni/set_so_keepalive** su **true**. Vedere anche la [Nota SAP #2743751](https://launchpad.support.sap.com/#/notes/2743751).  
        - Se si usa una configurazione del cluster di failover di Windows, assicurarsi che il tempo di risposta sui nodi non reattivi sia impostato correttamente per Azure. L'articolo [ottimizzazione delle soglie di rete del cluster di failover](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) elenca i parametri e il modo in cui influiscono sulle sensibilità del failover. Supponendo che i nodi del cluster si trovino nella stessa subnet, è necessario modificare i parametri seguenti:
            - SameSubNetDelay = 2000
            - SameSubNetThreshold = 15
            - RoutingHistorylength = 30
1. Testare le procedure di disponibilità elevata e ripristino di emergenza.
   1. Simulare le situazioni di failover arrestando le macchine virtuali (sistemi operativi guest di Windows) o inserendo i sistemi operativi in modalità panico (sistemi operativi guest Linux). Questo passaggio consente di stabilire se le configurazioni di failover funzionano come previsto.
   1. Misurare il tempo necessario per eseguire un failover. Se i tempi sono troppo lunghi, prendere in considerazione quanto segue:
        - Per SUSE Linux, usare i dispositivi SBD invece di Azure Fence Agent per velocizzare il failover.
        - Per SAP HANA, se il ricaricamento dei dati richiede troppo tempo, provare a eseguire il provisioning di una maggiore larghezza di banda di archiviazione.
   3. Verificare la sequenza di backup/ripristino e la tempistica e apportare le correzioni necessarie. Verificare che i tempi di backup siano sufficienti. È anche necessario testare le attività di ripristino e ripristino orario. Assicurarsi che i tempi di ripristino si trovino entro i contratti di RTO quando la RTO si basa su un processo di ripristino del database o della macchina virtuale.
   4. Testare l'architettura e la funzionalità di ripristino di emergenza tra aree diverse.
1. Controlli di sicurezza.
   1. Testare la validità dell'architettura del controllo degli accessi in base al ruolo (RBAC) di Azure. L'obiettivo è separare e limitare l'accesso e le autorizzazioni di diversi team. Ad esempio, i membri del team di base SAP dovrebbero essere in grado di distribuire le macchine virtuali e assegnare dischi da archiviazione di Azure in una rete virtuale di Azure specificata. Il team di base SAP, tuttavia, non deve essere in grado di creare le proprie reti virtuali o di modificare le impostazioni delle reti virtuali esistenti. I membri del team di rete non devono essere in grado di distribuire le macchine virtuali in reti virtuali in cui sono in esecuzione le macchine virtuali di applicazioni SAP e DBMS. Né i membri di questo team possono modificare gli attributi delle macchine virtuali o persino eliminare le VM o i dischi.  
   1.  Verificare che il [gruppo di sicurezza di rete e](https://docs.microsoft.com/azure/virtual-network/security-overview) le regole ASC funzionino come previsto e proteggano le risorse protette.
   1.  Assicurarsi che tutte le risorse che devono essere crittografate lo siano effettivamente. Definire e implementare i processi per eseguire il backup dei certificati, archiviare e accedere a tali certificati e ripristinare le entità crittografate.
   1.  Usare [crittografia dischi di Azure](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq) per i dischi del sistema operativo laddove possibile da un punto di vista del supporto del sistema operativo.
   1.  Assicurarsi che non si stiano utilizzando troppi livelli di crittografia. In alcuni casi, è consigliabile usare crittografia dischi di Azure insieme a uno dei metodi di Transparent Data Encryption DBMS per proteggere dischi o componenti diversi nello stesso server.  Ad esempio, in un server SAP DBMS, è possibile abilitare crittografia dischi di Azure (ADE) nel disco di avvio del sistema operativo (se il sistema operativo supporta ADE) e i dischi dati non usati dai file di persistenza dei dati DBMS.  Un esempio consiste nell'usare ADE sul disco che contiene le chiavi di crittografia Transparent Data Encryption.
1. Test delle prestazioni. In SAP, in base alla traccia e alle misurazioni SAP, effettuare i confronti seguenti:
   - Laddove applicabile, confrontare i primi 10 report online con l'implementazione corrente.
   - Laddove applicabile, confrontare i primi 10 processi batch con l'implementazione corrente.
   - Confrontare i trasferimenti di dati tramite le interfacce nel sistema SAP. Concentrarsi sulle interfacce in cui si sa che il trasferimento passa ora tra diverse località, ad esempio da locale ad Azure.


## <a name="non-production-phase"></a>Fase non di produzione 
In questa fase si presuppone che, dopo un progetto pilota o un modello di verifica (POC), si inizierà a distribuire sistemi SAP non di produzione in Azure. Incorpora tutti i concetti appresi ed esperienza durante il periodo di prova per questa distribuzione. Tutti i criteri e i passaggi elencati per verifica si applicano anche a questa distribuzione.

Durante questa fase, in genere si distribuiscono sistemi di sviluppo, sistemi di testing unità e sistemi di test di regressione aziendale in Azure. Si consiglia di utilizzare almeno un sistema non di produzione in una linea di applicazione SAP con la configurazione della disponibilità elevata completa che avrà il sistema di produzione futuro. Di seguito sono riportati alcuni passaggi aggiuntivi che è necessario completare durante questa fase:  

1.  Prima di spostare i sistemi dalla vecchia piattaforma ad Azure, raccogliere i dati sull'utilizzo delle risorse, ad esempio l'utilizzo della CPU, la velocità effettiva di archiviazione e i dati di IOPS. In particolare, raccogliere questi dati dalle unità di livello DBMS, ma anche raccoglierli dalle unità livello applicazione. Misurare anche la latenza di rete e di archiviazione.
2.  Registrare gli schemi temporali di utilizzo della disponibilità dei sistemi. L'obiettivo è determinare se i sistemi non di produzione devono essere disponibili tutto il giorno, ogni giorno o se sono presenti sistemi non di produzione che possono essere arrestati in determinate fasi di una settimana o un mese.
3.  Testare e determinare se si vuole creare immagini del sistema operativo per le macchine virtuali in Azure o se si vuole usare un'immagine dalla raccolta di immagini condivise di Azure. Se si usa un'immagine dalla raccolta immagini condivise, assicurarsi di usare un'immagine che rifletta il contratto di supporto con il fornitore del sistema operativo. Per alcuni fornitori di sistemi operativi, la raccolta di immagini condivise consente di importare le proprie immagini di licenza. Per le altre immagini del sistema operativo, il supporto è incluso nei prezzi indicati da Azure. Se si decide di creare immagini di sistema operativo personalizzate, è possibile trovare la documentazione in questi articoli:
    -   [Creare un'immagine generalizzata di una macchina virtuale Windows distribuita in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
    -   [Creare un'immagine generalizzata di una VM Linux distribuita in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
3.  Se si usano le immagini SUSE e Red Hat Linux dalla raccolta immagini condivise, è necessario usare le immagini per SAP fornite dai fornitori di Linux nella raccolta di immagini condivise.
4.  Assicurarsi di soddisfare i requisiti di supporto SAP per i contratti di supporto Microsoft. Vedere la [Nota di supporto SAP #2015553](https://launchpad.support.sap.com/#/notes/2015553). Per le istanze large di HANA, vedere [requisiti di onboarding](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-onboarding-requirements).
4.  Assicurarsi che gli utenti giusti ricevano le [notifiche di manutenzione pianificata](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/) in modo da poter scegliere i tempi di inattività migliori.
5.  Controllare spesso le presentazioni di Azure nei canali come [Channel 9](https://channel9.msdn.com/) per le nuove funzionalità che potrebbero essere valide per le distribuzioni.
6.  Controllare le note SAP correlate ad Azure, ad esempio la [Nota di supporto #1928533](https://launchpad.support.sap.com/#/notes/1928533), per i nuovi SKU di VM e le versioni di sistema operativo e DBMS appena supportate. Confrontare i prezzi dei nuovi tipi di VM con quelli dei tipi di VM precedenti, in modo da poter distribuire le macchine virtuali con il miglior rapporto prezzo/prestazioni.
7.  Ricontrollare le note di supporto SAP, la SAP HANA directory hardware e SAP PAM. Assicurarsi che non siano state apportate modifiche alle macchine virtuali supportate per Azure, versioni del sistema operativo supportate in tali macchine virtuali e versioni supportate di SAP e DBMS.
8.  Visitare [il sito Web SAP per i](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) nuovi SKU con certificazione Hana in Azure. Confrontare i prezzi dei nuovi SKU con quelli pianificati per l'uso. Apportare infine le modifiche necessarie per usare quelle con il miglior rapporto prezzo/prestazioni.
9.  Adattare gli script di distribuzione per usare nuovi tipi di VM e incorporare le nuove funzionalità di Azure che si vuole usare.
10. Dopo la distribuzione dell'infrastruttura, testare e valutare la latenza di rete tra le macchine virtuali del livello applicazione SAP e le macchine virtuali DBMS, in base alle note di supporto SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) e [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Valutare i risultati in base alle indicazioni sulla latenza di rete nella [Nota del supporto SAP #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). La latenza di rete deve essere nell'intervallo moderato o valido. Le eccezioni si applicano al traffico tra le macchine virtuali e le unità di istanze large di HANA, come descritto in [questo articolo](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance). Assicurarsi che nessuna delle restrizioni indicate in [considerazioni relative alla distribuzione DBMS di macchine virtuali di Azure per carichi di lavoro SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#azure-network-considerations) e alle [configurazioni e alle operazioni dell'infrastruttura SAP Hana in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) si applichi alla propria distribuzione.
11. Assicurarsi che le macchine virtuali vengano distribuite al [gruppo di posizionamento di prossimità di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)corretto, come descritto in [gruppi di posizionamento di prossimità di Azure per la latenza di rete ottimale con le applicazioni SAP](sap-proximity-placement-scenarios.md).
11. Eseguire tutti gli altri controlli elencati per la fase di prova prima di applicare il carico di lavoro.
12. Con l'applicazione del carico di lavoro, registrare il consumo di risorse dei sistemi in Azure. Confrontare questo consumo con i record della piattaforma precedente. Modificare il dimensionamento delle VM per le distribuzioni future se si riscontrano notevoli differenze. Tenere presente che, quando si ridimensionano, verranno ridotte anche le larghezze di banda di archiviazione e di rete delle macchine virtuali.
    - [Dimensioni per le macchine virtuali Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - [Dimensioni delle macchine virtuali Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. Sperimentare la funzionalità di copia del sistema e i processi. L'obiettivo è semplificare la copia di un sistema di sviluppo o di un sistema di test, in modo che i team del progetto possano ottenere rapidamente nuovi sistemi. Prendere in considerazione l'uso di [SAP lama](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+Landscape+Management+%28SAP+LaMa%29+at+a+Glance) per queste attività.
14. Ottimizza e perfeziona l'accesso, le autorizzazioni e i processi in base al ruolo di Azure del team per assicurarti la separazione dei compiti. Allo stesso tempo, assicurarsi che tutti i team possano eseguire le attività nell'infrastruttura di Azure.
15. Procedure di esercitazione, test e ripristino di emergenza a disponibilità elevata per consentire al personale di eseguire queste attività. Identifica le carenze e adatta le nuove funzionalità di Azure che stai integrando nelle tue distribuzioni.

 
## <a name="production-preparation-phase"></a>Fase di preparazione produzione 
In questa fase, raccogliere le informazioni acquisite e apprese durante le distribuzioni non di produzione e applicarle alle distribuzioni di produzione future. È anche necessario preparare il lavoro del trasferimento dei dati tra il percorso di hosting corrente e Azure.

1.  Completare gli aggiornamenti della versione SAP necessari per i sistemi di produzione prima di trasferirsi in Azure.
1.  Accettare i proprietari aziendali nei test funzionali e aziendali che devono essere eseguiti dopo la migrazione del sistema di produzione.
1.  Verificare che i test siano completati con i sistemi di origine nel percorso di hosting corrente. Evitare di eseguire test per la prima volta dopo che il sistema è stato spostato in Azure.
1.  Testare il processo di migrazione dei sistemi di produzione in Azure. Se non si intende trasferire tutti i sistemi di produzione in Azure durante lo stesso intervallo di tempo, compilare gruppi di sistemi di produzione che devono trovarsi nella stessa posizione di hosting. Test della migrazione dei dati. Ecco alcuni metodi comuni:
    - Usare metodi DBMS come backup/ripristino in combinazione con SQL Server Always On, la replica di sistema HANA o il log shipping per inizializzare e sincronizzare il contenuto del database in Azure.
    - Usare il backup/ripristino per i database più piccoli.
    - Usare SAP Migration monitor, integrato in SAP SWPM, per eseguire migrazioni eterogenee.
    - Usare il processo [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) se è necessario combinare la migrazione con un aggiornamento della versione di SAP. Tenere presente che non tutte le combinazioni del sistema DBMS di origine e del sistema DBMS di destinazione sono supportate. È possibile trovare altre informazioni nelle note di supporto SAP specifiche per le diverse versioni di DMO. Ad esempio, l' [opzione di migrazione del database (DMO) di SUM 2,0 SP04](https://launchpad.support.sap.com/#/notes/2644872).
    - Verificare se la velocità effettiva di trasferimento dei dati è migliore tramite Internet o tramite ExpressRoute, nel caso in cui sia necessario spostare i backup o i file di esportazione SAP. Se si spostano i dati attraverso Internet, potrebbe essere necessario modificare alcune delle regole del gruppo di sicurezza di rete/del gruppo di sicurezza delle applicazioni necessarie per i sistemi di produzione futuri.
1.  Prima di trasferire i sistemi dalla vecchia piattaforma ad Azure, raccogliere i dati sull'utilizzo delle risorse. I dati utili includono l'utilizzo della CPU, la velocità effettiva di archiviazione e i dati di IOPS. In particolare, raccogliere questi dati dalle unità di livello DBMS, ma anche raccoglierli dalle unità livello applicazione. Misurare anche la latenza di rete e di archiviazione.
1.  Ricontrollare le note di supporto SAP, la SAP HANA directory hardware e SAP PAM. Assicurarsi che non siano state apportate modifiche alle macchine virtuali supportate per Azure, le versioni del sistema operativo supportate in tali macchine virtuali e le versioni SAP e DBMS supportate.
1.  Aggiornare gli script di distribuzione per prendere in considerazione le decisioni più recenti effettuate sui tipi di VM e sulle funzionalità di Azure.
1.  Dopo la distribuzione dell'infrastruttura e delle applicazioni, verificare che:
    - Sono stati distribuiti i tipi di VM corretti con gli attributi e le dimensioni di archiviazione corretti.
    - Le macchine virtuali si trovano nelle versioni e nelle patch del sistema operativo corrette e desiderate e sono uniformi.
    - Le macchine virtuali vengono finalizzate come richiesto e in modo uniforme.
    - Sono state installate e distribuite le patch e le versioni dell'applicazione corrette.
    - Le macchine virtuali sono state distribuite in set di disponibilità di Azure come pianificato.
    - Archiviazione Premium di Azure viene usata per i dischi sensibili alla latenza o in cui è richiesto il [contratto di contratto con una singola macchina virtuale del 99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) .
    - Azure acceleratore di scrittura è stato distribuito correttamente.
        - Assicurarsi che, all'interno delle macchine virtuali, gli spazi di archiviazione o i set di striping siano stati compilati correttamente tra i dischi che richiedono acceleratore di scrittura.
        - Controllare la [configurazione di RAID software in Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid).
        - Controllare la [configurazione di LVM in macchine virtuali Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm).
    - [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/) viene usato in modo esclusivo.
    - Le macchine virtuali sono state distribuite nei set di disponibilità corretti e zone di disponibilità.
    - La [rete accelerata di Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) è abilitata nelle macchine virtuali usate nel livello applicazione SAP e nel livello DBMS di SAP.
    - Nessuna [appliance virtuale di rete di Azure](https://azure.microsoft.com/solutions/network-appliances/) si trova nel percorso di comunicazione tra l'applicazione SAP e il livello DBMS dei sistemi SAP basati su SAP NetWeaver, hybris o S/4HANA.
    - Il gruppo di sicurezza delle applicazioni e le regole del gruppo di sicurezza di rete consentono la comunicazione come desiderato e pianificato e bloccano la comunicazione laddove necessario
    - Le impostazioni di timeout sono impostate correttamente, come descritto in precedenza.
    - Le VM vengono distribuite nel [gruppo di posizionamento di prossimità di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)corretto, come descritto in [gruppi di posizionamento di prossimità di Azure per una latenza di rete ottimale con le applicazioni SAP](sap-proximity-placement-scenarios.md).
    - La latenza di rete tra VM a livello di applicazione SAP e VM DBMS viene testata e convalidata come descritto nelle note di supporto SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) e [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Valutare i risultati in base alle indicazioni sulla latenza di rete nella [Nota del supporto SAP #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). La latenza di rete deve essere nell'intervallo moderato o valido. Le eccezioni si applicano al traffico tra le macchine virtuali e le unità di istanze large di HANA, come descritto in [questo articolo](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance).
    - La crittografia è stata implementata laddove necessario e con il metodo di crittografia appropriato.
    - Le interfacce e altre applicazioni possono connettere la nuova infrastruttura distribuita.
1.  Creare un PlayBook per la reazione alla manutenzione pianificata di Azure. Determinare l'ordine in cui i sistemi e le macchine virtuali devono essere riavviati per la manutenzione pianificata.
    

## <a name="go-live-phase"></a>Fase di Go-Live
Durante la fase di Go-Live, assicurarsi di seguire i PlayBook sviluppati durante le fasi precedenti. Eseguire i passaggi testati e pratici. Non accettare modifiche dell'ultimo minuto nelle configurazioni e nei processi. Eseguire anche i passaggi seguenti:

1. Verificare il corretto funzionamento del monitoraggio del portale di Azure e di altri strumenti di monitoraggio. È consigliabile Windows Performance Monitor (PerfMon) per Windows e SAR per Linux.
    - Contatori CPU.
        - Tempo medio CPU, totale (tutte le CPU)
        - Tempo medio della CPU, ogni singolo processore (128 processori in macchine virtuali M128)
        - Tempo kernel CPU, ogni singolo processore
        - Tempo utente CPU, ogni singolo processore
    - Memoria.
        - Memoria libera
        - Pagina memoria al secondo
        - Pagina memoria in uscita al secondo
    - Disk (Disco).
        - Disco letto in KBps, per singolo disco
        - Letture disco al secondo, per singolo disco
        - Lettura disco in microsecondi/lettura, per singolo disco
        - Scritture su disco in KBps, per singolo disco
        - Scritture disco/sec, per singolo disco
        - Scritture su disco in microsecondi/lettura, per singolo disco
    - Rete.
        - Pacchetti di rete in/secondo
        - Pacchetti di rete in uscita al secondo
        - KB di rete in/secondo
        - KB/sec rete
1.  Dopo la migrazione dei dati, eseguire tutti i test di convalida concordati con i proprietari dell'azienda. Accettare i risultati dei test di convalida solo quando si hanno risultati per i sistemi di origine originali.
1.  Controllare se le interfacce funzionano e se altre applicazioni possono comunicare con i sistemi di produzione appena distribuiti.
1.  Verificare il sistema di trasporto e correzione tramite SAP Transaction STMS.
1.  Eseguire i backup del database dopo il rilascio del sistema per la produzione.
1.  Eseguire i backup delle VM per le macchine virtuali a livello di applicazione SAP dopo che il sistema è stato rilasciato per la produzione.
1.  Per i sistemi SAP che non facevano parte della fase corrente di Go-Live ma che comunicano con i sistemi SAP spostati in Azure durante questa fase di Go-Live, è necessario reimpostare il buffer dei nomi host in SM51. In questo modo, i vecchi indirizzi IP memorizzati nella cache associati ai nomi delle istanze dell'applicazione spostati in Azure vengono rimossi.  


## <a name="post-production"></a>Post-produzione
Questa fase riguarda il monitoraggio, il funzionamento e l'amministrazione del sistema. Dal punto di vista di SAP, si applicano le normali attività che era necessario completare nel percorso di hosting precedente. Completare anche queste attività specifiche di Azure:

1. Esaminare le fatture di Azure per i sistemi a ricarica elevata.
2. Ottimizzazione dell'efficienza prezzo/prestazioni sul lato VM e sul lato archiviazione.
3. Ottimizzare i momenti in cui è possibile arrestare i sistemi.  


## <a name="next-steps"></a>Passaggi successivi
Vedere i seguenti articoli:

- [Guida alla pianificazione e all'implementazione di Macchine virtuali di Azure per SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Distribuzione di Macchine virtuali di Azure per SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Considerazioni sulla distribuzione DBMS di macchine virtuali di Azure per carichi di lavoro SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

