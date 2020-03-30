---
title: Elenco di controllo per la pianificazione e la distribuzione di carichi di lavoro SAP | Microsoft Docs
description: Elenco di controllo per la pianificazione delle distribuzioni del carico di lavoro SAP in Azure e la distribuzione dei carichi di lavoroChecklist for planning SAP workload deployments to Azure and deploying the workloads
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
ms.date: 02/13/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7b8c1b0bcc74d73f1f869972488ba7c5dfe610d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060073"
---
# <a name="sap-workloads-on-azure-planning-and-deployment-checklist"></a>Carichi di lavoro SAP in Azure: elenco di controllo per la pianificazione e la distribuzione

Questo elenco di controllo è progettato per i clienti che spostano le applicazioni SAP NetWeaver, S/4HANA e Hybris nell'infrastruttura di Azure come servizio. Per tutta la durata del progetto, un cliente e/o un partner SAP devono esaminare l'elenco di controllo. È importante notare che molti dei controlli vengono completati all'inizio del progetto e durante la fase di pianificazione. Al termine della distribuzione, le modifiche semplici nell'infrastruttura di Azure distribuita o nelle versioni software SAP possono diventare complesse.

Esaminare l'elenco di controllo in corrispondenza delle attività cardine principali durante il progetto. In questo modo vi permetterà di rilevare piccoli problemi prima che diventino problemi di grandi dimensioni. Avrai anche abbastanza tempo per riprogettare e testare le modifiche necessarie. Non considerare completo questo elenco di controllo. A seconda della situazione, potrebbe essere necessario eseguire molti altri controlli.

L'elenco di controllo non include attività indipendenti da Azure.The checklist doesn't include tasks that are independent from Azure. Ad esempio, le interfacce dell'applicazione SAP cambiano durante uno spostamento alla piattaforma Azure o a un provider di hosting.

Questo elenco di controllo può essere utilizzato anche per i sistemi già distribuiti. Nuove funzionalità, ad esempio Acceleratore di scrittura e zone di disponibilità e nuovi tipi di macchine virtuali potrebbero essere stati aggiunti dopo la distribuzione. È quindi utile esaminare periodicamente l'elenco di controllo per assicurarsi di essere a conoscenza delle nuove funzionalità della piattaforma Azure.So it's useful to review the checklist periodically to ensure you're aware of new features in the Azure platform.

## <a name="project-preparation-and-planning-phase"></a>Fase di preparazione e pianificazione del progetto
Durante questa fase, si pianifica la migrazione del carico di lavoro SAP alla piattaforma Azure.During this phase, you plan the migration of your SAP workload to the Azure platform. Come minimo, durante questa fase è necessario creare i seguenti documenti e definire e discutere i seguenti elementi della migrazione:

1. Documento di progettazione di alto livello. Il presente documento deve contenere:
    - L'inventario corrente dei componenti e delle applicazioni SAP e un inventario delle applicazioni di destinazione per Azure.The current inventory of SAP components and applications, and a target application inventory for Azure.
    - Matrice di assegnazione di responsabilità (RACI) che definisce le responsabilità e le assegnazioni delle parti coinvolte. Iniziare a un livello elevato e lavorare a livelli più granulari durante la pianificazione e le prime distribuzioni.
    - Un'architettura di soluzione di alto livello.
    - Decisione sulle aree di Azure da distribuire. Vedere [l'elenco delle aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/). Per informazioni sui servizi disponibili in ogni area, vedere [Prodotti disponibili per regione.](https://azure.microsoft.com/global-infrastructure/services/)
    - Architettura di rete per la connessione dall'ambiente locale ad Azure.A networking architecture to connect from on-premises to Azure. Iniziare a familiarizzare con il blueprint del [virtual data center per Azure.](https://docs.microsoft.com/azure/architecture/vdc/)
    - Principi di sicurezza per l'esecuzione di dati aziendali ad alto impatto in Azure.Security principles for running high-impact business data in Azure. Per altre informazioni sulla sicurezza dei dati, iniziare con la [documentazione relativa](https://docs.microsoft.com/azure/security/)alla sicurezza di Azure.
2.  Documento di progettazione tecnica. Il presente documento deve contenere:
    - Diagramma a blocchi per la soluzione.
    - Ridimensionamento dei componenti di elaborazione, archiviazione e rete in Azure.The sizing of compute, storage, and networking components in Azure. Per il dimensionamento SAP delle macchine virtuali di Azure, vedere [nota del supporto SAP #1928533.](https://launchpad.support.sap.com/#/notes/1928533)
    - Architettura di continuità aziendale e ripristino di emergenza.
    - Informazioni dettagliate sulle versioni del sistema operativo, del database, del kernel e dei pacchetti di supporto SAP. Non è necessariamente vero che ogni versione del sistema operativo supportata da SAP NetWeaver o S/4HANA è supportata nelle macchine virtuali di Azure. Lo stesso vale per le versioni DBMS. Controllare le origini seguenti per allineare e, se necessario, aggiornare le versioni SAP, le versioni DBMS e le versioni del sistema operativo per garantire il supporto di SAP e Azure. È necessario disporre di combinazioni di rilascio supportate da SAP e Azure per ottenere il supporto completo da SAP e Microsoft.You need to have release combinations supported by SAP and Azure to get full support from SAP and Microsoft. Se necessario, è necessario pianificare l'aggiornamento di alcuni componenti software. Ulteriori dettagli sul software SAP, OS e DBMS supportato sono documentati qui:
        - [Nota di supporto SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Questa nota definisce le versioni minime del sistema operativo supportate nelle macchine virtuali di Azure.This note defines the minimum OS releases supported on Azure VMs. Definisce inoltre le versioni minime dei database necessarie per la maggior parte dei database non HANA. Infine, fornisce il ridimensionamento SAP per i tipi di macchine virtuali di Azure supportati da SAP.
        - [Nota di supporto SAP #2015553 .](https://launchpad.support.sap.com/#/notes/2015553) Questa nota definisce i criteri di supporto relativi alle relazioni di archiviazione e supporto di Azure necessarie con Microsoft.This note defines support policies around Azure storage and support relationship needed with Microsoft.
        - [Nota di supporto SAP #2039619](https://launchpad.support.sap.com/#/notes/2039619). Questa nota definisce la matrice di supporto Oracle per Azure.This note defines the Oracle support matrix for Azure. Oracle supporta solo Windows e Oracle Linux come sistemi operativi guest in Azure per i carichi di lavoro SAP. Questa istruzione di supporto si applica anche al livello applicazione SAP che esegue istanze SAP. Tuttavia, Oracle non supporta la disponibilità elevata per SAP Central Services in Oracle Linux tramite Pacemaker.However, Oracle doesn't support high availability for SAP Central Services in Oracle Linux through Pacemaker. Se è necessaria un'elevata disponibilità per ASCS su Oracle Linux, è necessario utilizzare SIOS Protection Suite per Linux. Per dati dettagliati sulla certificazione SAP, vedere NOTa #1662610 - Dettagli sul supporto per [SIOS Protection Suite for Linux](https://launchpad.support.sap.com/#/notes/1662610). Per Windows, la soluzione Windows Server Failover Clustering supportata da SAP per SAP Central Services è supportata insieme a Oracle come livello DBMS.
        - [Nota di supporto SAP #2235581](https://launchpad.support.sap.com/#/notes/2235581). Questa nota fornisce la matrice di supporto per SAP HANA in diverse versioni del sistema operativo.
        - Le macchine virtuali di Azure supportate da SAP HANA e [le istanze di grandi dimensioni di HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) sono elencate nel [sito Web SAP.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
        - [Matrice di disponibilità del prodotto SAP](https://support.sap.com/en/).
        - [Nota del supporto SAP #2555629 - SAP HANA 2.0 Dynamic Tiering - Supporto per hypervisor e cloud](https://launchpad.support.sap.com/#/notes/2555629)
        - [Nota del supporto SAP #1662610 - Dettagli di supporto per SIOS Protection Suite per Linux](https://launchpad.support.sap.com/#/notes/1662610)
        - Note SAP per altri prodotti specifici di SAP.     
    - Per i sistemi di produzione SAP sono consigliati progetti rigorosi a tre livelli. Non è consigliabile combinare ASCS e/o DBMS e/o server applicazioni in una macchina virtuale. L'uso di configurazioni cluster multi-SID per SAP Central Services è supportato nei sistemi operativi guest Windows in Azure.Using multi-SID cluster configurations for SAP Central Services is supported on Windows guest operating systems on Azure. Ma questa configurazione non è supportata per SAP Central Services nei sistemi operativi Linux in Azure.But this configuration isn't supported for SAP Central Services on Linux operating systems on Azure. È possibile trovare la documentazione per lo scenario del sistema operativo guest Windows in questi articoli:You can find documentation for the Windows guest OS scenario in these articles:
        - [Disponibilità elevata multi-SID dell'istanza SAP ASCS/SCS con Windows Server Failover Clustering e i dischi condivisi in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk)
        - [Disponibilità elevata multi-SID dell'istanza ASCS/SCS di SAP con Windows Server Failover Clustering e condivisione file in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share)
    - Architettura di disponibilità elevata e ripristino di emergenza.
        - In base a RTO e RPO, definire l'aspetto dell'architettura di disponibilità elevata e ripristino di emergenza.
        - Per la disponibilità elevata all'interno di una zona, verificare cosa offre il DBMS desiderato in Azure.For high availability within a zone, check what the desired DBMS has to offer in Azure. La maggior parte dei pacchetti DBMS offre metodi sincroni di uno hot standby sincrono, che è consigliabile per i sistemi di produzione. Consultare anche la documentazione relativa a SAP per database diversi, a partire da Considerazioni per la [distribuzione DBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) di Macchine virtuali di Azure per carichi di lavoro SAP e documenti correlati.
           L'utilizzo di Clustering di failover di Windows Server con una configurazione del disco condiviso per il livello DBMS, come, ad esempio, [descritto per SQL Server,](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017)non è supportato. Invece, utilizzare soluzioni come:
           - [SQL Server AlwaysOn](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups)
           - [Oracle Data Guard](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
           - [Replica di sistema HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        - Per il ripristino di emergenza tra aree di Azure, esaminare le soluzioni offerte da diversi fornitori DBMS. La maggior parte di essi supporta la replica asincrona o la distribuzione dei log.
        - Per il livello dell'applicazione SAP, determinare se si eseguiranno i sistemi di test di regressione aziendale, che idealmente sono repliche delle distribuzioni di produzione, nella stessa area di Azure o nell'area di ripristino di emergenza. Nel secondo caso, è possibile scegliere come destinazione di ripristino di emergenza per le distribuzioni di produzione.
        - Se si decide di non inserire i sistemi non di produzione nel sito di ripristino di emergenza, esaminare Azure Site Recovery come metodo per replicare il livello dell'applicazione SAP nell'area di ripristino di emergenza di Azure.If you decide to not place the non-production systems in the DR site, look into Azure Site Recovery as a method for replicating the SAP application layer into the Azure DR region. Per altre informazioni, vedere Configurare il ripristino di emergenza per una distribuzione di [app SAP NetWeaver](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)a più livelli .
        - Se si decide di usare una configurazione HADR combinata usando le zone di [disponibilità di Azure,](https://docs.microsoft.com/azure/availability-zones/az-overview)acquisire familiarità con le aree di Azure in cui sono disponibili le zone di disponibilità. Prendere in considerazione anche le restrizioni che possono essere introdotte aumentando le latenze di rete tra due zone di disponibilità.  
3.  Un inventario di tutte le interfacce SAP (SAP e non SAP).
4.  Progettazione di servizi di fondazione. Questa progettazione deve includere i seguenti elementi:
    - Progettazione di Active Directory e DNS.
    - Topologia di rete all'interno di Azure e assegnazione di sistemi SAP diversi.
    - Struttura di accesso basata sui ruoli per i team che gestiscono l'infrastruttura e le applicazioni SAP in [Azure.Role-based access](https://docs.microsoft.com/azure/role-based-access-control/overview) structure for teams that manage infrastructure and SAP applications in Azure.
    - Topologia del gruppo di risorse.
    - [Strategia di tagging](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#tags-and-billing).
    - Convenzioni di denominazione per le macchine virtuali e altri componenti dell'infrastruttura e/o nomi logici.
5.  Contratto di supporto Microsoft Premier. Identificare Microsoft Technical Account Manager (TAM). Per i requisiti di supporto SAP, vedere [#2015553 nota di supporto SAP](https://launchpad.support.sap.com/#/notes/2015553).
6.  Numero di sottoscrizioni di Azure e quota core per le sottoscrizioni. [Aprire le richieste di supporto per aumentare le quote delle sottoscrizioni di Azure](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) in base alle esigenze.
7.  Data reduction and data migration plan for migrating SAP data into Azure. Per i sistemi SAP NetWeaver, SAP include linee guida su come limitare il volume di grandi quantità di dati. Vedere [questa guida SAP](https://wiki.scn.sap.com/wiki/download/attachments/247399467/DVM_%20Guide_7.2.pdf?version=1&modificationDate=1549365516000&api=v2) sulla gestione dei dati nei sistemi SAP ERP. Alcuni dei contenuti si applicano anche ai sistemi NetWeaver e S/4HANA in generale.
8.  Un approccio di distribuzione automatizzata. L'obiettivo dell'automazione delle distribuzioni di infrastruttura in Azure è distribuire in modo deterministico e ottenere risultati deterministici. Molti clienti usano script basati su powerShell o CLI. Ma esistono varie tecnologie open source che è possibile usare per distribuire l'infrastruttura di Azure per SAP e persino installare il software SAP. Puoi trovare esempi su GitHub:
    - [Distribuzioni SAP automatizzate nel cloud di Azure](https://github.com/Azure/sap-hana)
    - [Installazione SAP HANA](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  Definire una cadenza di revisione di progettazione e distribuzione regolare tra il cliente, l'integratore di sistema, Microsoft e altre parti coinvolte.

 
## <a name="pilot-phase-strongly-recommended"></a>Fase pilota (fortemente consigliata)
 
È possibile eseguire un progetto pilota prima o durante la pianificazione e la preparazione del progetto. È inoltre possibile utilizzare la fase pilota per testare gli approcci e i progetti eseguiti durante la fase di pianificazione e preparazione. E si può espandere la fase pilota per renderlo una vera prova di concetto.

È consigliabile configurare e convalidare una soluzione HADR completa e la progettazione della sicurezza durante una distribuzione pilota. Alcuni clienti eseguono test di scalabilità durante questa fase. Altri clienti utilizzano le distribuzioni dei sistemi sandbox SAP come fase pilota. Si presuppone che sia già stato identificato un sistema di cui si vuole eseguire la migrazione ad Azure per il progetto pilota.

1. Ottimizzare il trasferimento dei dati in Azure.Optimize data transfer to Azure. La scelta ottimale dipende fortemente dallo scenario specifico. Il trasferimento da locale tramite [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) è più veloce se il circuito ExpressRoute dispone di larghezza di banda sufficiente. In altri scenari, il trasferimento tramite Internet è più veloce.
2. Per una migrazione eterogenea della piattaforma SAP che prevede l'esportazione e l'importazione di dati, testare e ottimizzare le fasi di esportazione e importazione. Per le migrazioni di grandi dimensioni in cui SQL ServerSQL Server è la piattaforma di destinazione, è possibile trovare [suggerimenti.](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070) È possibile utilizzare Migration Monitor/SWPM se non è necessario un aggiornamento di rilascio combinato. È possibile usare il processo [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) quando si combina la migrazione con un aggiornamento del rilascio SAP. A tale scopo, è necessario soddisfare determinati requisiti per la combinazione di piattaforma DBMS di origine e di destinazione. Questo processo è documentato in [DMO (Database Migration Option) di SUM 2.0 SP03.](https://launchpad.support.sap.com/#/notes/2631152)
   1.  Esportare all'origine, esportare il caricamento dei file in Azure e importare le prestazioni. Massimizza la sovrapposizione tra esportazione e importazione.
   2.  Valutare il volume del database sulle piattaforme di destinazione e destinazione ai fini del dimensionamento dell'infrastruttura.
   3.  Convalidare e ottimizzare la temporizzazione.
1. Convalida tecnica.
   1. tipi di macchine virtuali.
        - Esaminare le risorse nelle note di supporto SAP, nella directory hardware SAP HANA e di nuovo nel PAM SAP. Assicurarsi che non siano presenti modifiche alle macchine virtuali supportate per Azure, versioni del sistema operativo supportate per tali tipi di macchine virtuali e versioni SAP e DBMS supportate.
        - Verificare nuovamente il dimensionamento dell'applicazione e dell'infrastruttura distribuite in Azure. Se si spostano applicazioni esistenti, è spesso possibile derivare il SAPS necessario dall'infrastruttura utilizzata e dalla [pagina Web di benchmark SAP](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) e confrontarla con i numeri SAPS elencati nella [nota](https://launchpad.support.sap.com/#/notes/1928533)di supporto SAP #1928533 . Tenere inoltre presente [questo articolo sulle classificazioni SAPS.](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208)
        - Valutare e testare il dimensionamento delle macchine virtuali di Azure in relazione alla velocità effettiva di archiviazione massima e alla velocità effettiva di rete dei tipi di vm scelti durante la fase di pianificazione. Puoi trovare i dati qui:
           -  [Dimensioni per le macchine virtuali Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). È importante considerare la velocità effettiva massima del *disco non memorizzato nella cache* per il ridimensionamento.
           -  [Dimensioni per le macchine virtuali Linux in Azure.](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) È importante considerare la velocità effettiva massima del *disco non memorizzato nella cache* per il ridimensionamento.
   2. Archiviazione.
        - Come minimo, usare [l'archiviazione SSD standard](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) di Azure per le macchine virtuali che rappresentano i livelli dell'applicazione SAP e per la distribuzione di DBMS non sensibili alle prestazioni.
        - In generale, non è consigliabile usare [i dischi HDD standard](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd)di Azure.
        - Usare [Archiviazione Premium](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) di Azure per tutte le macchine virtuali DBMS sensibili alle prestazioni in remoto.
        - Usare [i dischi gestiti di Azure](https://azure.microsoft.com/services/managed-disks/).
        - Usare l'acceleratore di scrittura di Azure per le unità di log DBMS con serie M. Tenere presente i limiti e l'utilizzo dell'acceleratore di scrittura, come documentato in [Scrivi acceleratore](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).
        - Per i diversi tipi DBMS, consultare la [documentazione DBMS generica correlata a SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) e la documentazione specifica di DBMS a cui punta il documento generico.
        - Per altre informazioni su SAP HANA, vedere [Configurazioni e operazioni dell'infrastruttura SAP HANA in Azure.For](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)more information about SAP HANA, see SAP HANA infrastructure configurations and operations on Azure .
        - Non montare mai dischi dati di Azure in una macchina virtuale Linux di Azure tramite l'ID dispositivo. Usare invece l'identificatore univoco universale (UUID). Prestare attenzione quando si usano, ad esempio, strumenti grafici per il montaggio di dischi dati di Azure. Controllare le voci in /etc/fstab per assicurarsi che l'UUID viene utilizzato per montare i dischi. Ulteriori dettagli sono disponibili in [questo articolo](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#connect-to-the-linux-vm-to-mount-the-new-disk).
   3. Funzionalità di rete.
        - Testare e valutare l'infrastruttura di rete virtuale e la distribuzione delle applicazioni SAP attraverso o all'interno delle diverse reti virtuali di Azure.Test and evaluate your virtual network infrastructure and the distribution of your SAP applications across or within the different Azure virtual networks.
        -  Valutare l'approccio dell'architettura di rete virtuale hub-and-spoke o l'approccio di microsegmentazione all'interno di una singola rete virtuale di Azure.Evaluate the hub-and-spoke virtual network architecture approach or the microsegmentation approach within a single Azure virtual network. Basare questa valutazione su:
               1. Costi dello scambio di dati tra reti virtuali di [Azure con peering.](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) Per informazioni sui costi, vedere [Prezzi della rete virtuale](https://azure.microsoft.com/pricing/details/virtual-network/).
               2. Vantaggi di una disconnessione rapida del peering tra reti virtuali di Azure anziché modificare il gruppo di sicurezza di rete per isolare una subnet all'interno di una rete virtuale. Questa valutazione è per i casi in cui le applicazioni o le macchine virtuali ospitate in una subnet della rete virtuale sono diventate un rischio per la sicurezza.
                3. Registrazione e controllo centralizzati del traffico di rete tra il traffico locale, il mondo esterno e il virtual data center creato in Azure.Central logging and auditing of network traffic between on-premises, the outside world, and the virtual datacenter you built in Azure.
        - Valutare e testare il percorso dei dati tra il livello dell'applicazione SAP e il livello SAP DBMS.
            -  Il posizionamento delle appliance virtuali di rete di Azure nel percorso di comunicazione tra l'applicazione SAP e il livello DBMS dei sistemi SAP [basati](https://azure.microsoft.com/solutions/network-appliances/) su SAP NetWeaver, Hybris o S/4HANA non è supportato.
            -  Il posizionamento del livello applicazione SAP e del DBMS SAP in diverse reti virtuali di Azure di cui non viene eseguito il peering non è supportato.
            -  È possibile utilizzare le regole del gruppo di [sicurezza dell'applicazione e](https://docs.microsoft.com/azure/virtual-network/security-overview) del gruppo di sicurezza di rete per definire le route tra il livello dell'applicazione SAP e il livello SAP DBMS.
        - Assicurarsi che [la rete accelerata](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) di Azure sia abilitata nelle macchine virtuali usate nel livello dell'applicazione SAP e nel livello DBMS SAP. Tenere presente che per supportare Rete accelerata in Azure sono necessari diversi livelli di sistema operativo:
            - Windows Server 2012 R2 o versioni successive.
            - SUSE Linux 12 SP3 o versione successiva.
            - RHEL 7.4 o versione successiva.
            - Oracle Linux 7.5. Se si utilizza il kernel RHCKL, è necessaria la versione 3.10.0-862.13.1.el7. Se si utilizza il kernel Oracle UEK, è necessaria la versione 5.
        - Testare e valutare la latenza di rete tra le macchine virtuali a livello di applicazione SAP e le macchine virtuali DBMS in base alle note di supporto SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) e [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Valutare i risultati rispetto alle linee guida sulla latenza di rete in [#1100926 del supporto SAP.](https://launchpad.support.sap.com/#/notes/1100926/E) La latenza di rete deve essere compresa nell'intervallo moderato o valido. Le eccezioni si applicano al traffico tra macchine virtuali e unità di istanza di grandi dimensioni HANA, come documentato in [questo articolo](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance).
        - Assicurarsi che le distribuzioni ILB siano configurate per l'utilizzo di Direct Server Return. Questa impostazione riduce la latenza quando gli Information IlB di Azure vengono usati per le configurazioni a disponibilità elevata nel livello DBMS.
        - Se si usa Azure Load Balancer con i sistemi operativi guest Linux, verificare che il parametro di rete Linux **net.ipv4.tcp_timestamps** sia impostato su **0**. Questa raccomandazione è in conflitto con i suggerimenti nelle versioni precedenti di [SAP #2382421](https://launchpad.support.sap.com/#/notes/2382421). La nota SAP è stata aggiornata per indicare che questo parametro deve essere impostato su 0 per funzionare con i servizi di bilanciamento del carico di Azure.The SAP note is now updated to state that this parameter needs to be set to **0** to work with Azure load balancers.
        - È consigliabile usare i gruppi di posizionamento di [prossimità](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) di Azure per ottenere una latenza di rete ottimale. Per altre informazioni, vedere Gruppi di posizionamento di prossimità di Azure per una latenza di [rete ottimale con le applicazioni SAP.](sap-proximity-placement-scenarios.md)
   4. Distribuzioni di disponibilità elevata e ripristino di emergenza.
        - Se si distribuisce il livello applicazione SAP senza definire una zona di disponibilità di Azure specifica, assicurarsi che tutte le macchine virtuali che eseguono istanze di finestre di dialogo SAP o middleware di un singolo sistema SAP siano distribuite in un set di [disponibilità.](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)
        - Se non è necessaria la disponibilità elevata per SAP Central Services e DBMS, è possibile distribuire queste macchine virtuali nello stesso set di disponibilità del livello di applicazione SAP.
        - Se si proteggono sap Central Services e il livello DBMS per la disponibilità elevata utilizzando la replica passiva, posizionare i due nodi per SAP Central Services in un set di disponibilità separato e i due nodi DBMS in un altro set di disponibilità.
        - Se si esegue la distribuzione in zone di disponibilità di Azure, non è possibile usare i set di disponibilità. Tuttavia, è necessario assicurarsi di distribuire i nodi Servizi centrali attivi e passivi in due diverse zone di disponibilità. Usare le zone di disponibilità con la latenza più bassa tra di esse.
          Tenere presente che è necessario usare [Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) per il caso d'uso di stabilire cluster di failover di Windows o Pacemaker per il livello DBMS e SAP Central Services tra le zone di disponibilità. Non è possibile usare Il servizio di [bilanciamento del carico di base](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) per le distribuzioni di aree di zona.
   5. Impostazioni di timeout.
        - Controllare le tracce per sviluppatori SAP NetWeaver delle istanze SAP per assicurarsi che non vi siano interruzioni di connessione tra il server di accodamento e i processi di lavoro SAP. È possibile evitare queste interruzioni di connessione impostando questi due parametri del Registro di sistema:You can avoid these connection breaks by setting these two registry parameters:
            - HKLM, SYSTEM, CurrentControlSet, Servizi, Tcpip, Parametri, KeepAliveTime, 120000. Per ulteriori informazioni, vedere [KeepAliveTime](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)).
            - HKLM, SYSTEM, CurrentControlSet, Servizi, Tcpip, Parametri, KeepAliveInterval, 120000. Per ulteriori informazioni, vedere [KeepAliveInterval](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)).
        - Per evitare timeout GUI tra le interfacce SAP GUI locali e i livelli dell'applicazione SAP distribuiti in Azure, verificare se questi parametri sono impostati nel file default.pfl o nel profilo dell'istanza:To avoid GUI timeouts between on-premises SAP GUI interfaces and SAP application layers deployed in Azure, check whether these parameters are set in the default.pfl or the instance profile:
            - rdisp/keepalive_timeout = 3600
            - rdisp/keepalive = 20
        - Per evitare l'interruzione delle connessioni stabilite tra il processo di accodamento SAP e i processi di lavoro SAP, è necessario impostare il parametro **enque/encni/set_so_keepalive** su **true**. Vedere anche [nota SAP #2743751](https://launchpad.support.sap.com/#/notes/2743751).  
        - Se si usa una configurazione del cluster di failover di Windows, assicurarsi che il tempo di reazione su nodi non rispondenti sia impostato correttamente per Azure.If you use a Windows failover cluster configuration, make sure that the time to react on non-responsive nodes is set correctly for Azure. L'articolo Ottimizzazione delle [soglie](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) di rete del cluster di failover elenca i parametri e il modo in cui influiscono sulle sensibilità del failover. Supponendo che i nodi del cluster si trovino nella stessa subnet, è necessario modificare questi parametri:Assuming the cluster nodes are in the same subnet, you should change these parameters:
            - SameSubNetDelay - 2000
            - SameSubNetThreshold = 15
            - RoutingHistorylength 30
    6. Impostazioni del sistema operativo o patch
        - Per l'esecuzione di HANA su SAP, leggere le note e la documentazione seguenti:For running HANA on SAP, read these notes and documentations:
            -   [Nota del supporto SAP #2814271 - SAP HANA Backup non riesce in Azure con errore di checksum](https://launchpad.support.sap.com/#/notes/2814271)
            -   [Nota del supporto SAP #2753418- Potenziale riduzione delle prestazioni a causa del fallback del timer](https://launchpad.support.sap.com/#/notes/2753418)
            -   [Nota del supporto SAP #2791572 - Riduzione delle prestazioni a causa della mancanza del supporto VDSO per Hyper-V in Azure](https://launchpad.support.sap.com/#/notes/2791572)
            -   [Nota del supporto SAP #2382421 - Ottimizzazione della configurazione di rete a livello di HANA e oS](https://launchpad.support.sap.com/#/notes/2382421)
            -   [Nota del supporto SAP #2694118 - Red Hat Enterprise Linux HA Add-On in Azure](https://launchpad.support.sap.com/#/notes/2694118)
            -   [Nota del supporto SAP #1984787 - SUSE LINUX Enterprise Server 12: Note sull'installazione](https://launchpad.support.sap.com/#/notes/1984787)
            -   [Nota del supporto SAP #2002167 - Red Hat Enterprise Linux 7.x: installazione e aggiornamento](https://launchpad.support.sap.com/#/notes/0002002167)
            -   [SAP Support Note #2292690 - SAP HANA DB: Recommended OS settings for RHEL 7](https://launchpad.support.sap.com/#/notes/0002292690) (Nota di supporto n. 2247020 - SAP HANA DB: impostazioni del sistema operativo consigliate per RHEL 7)
            -   [Nota del supporto SAP #2772999 - Red Hat Enterprise Linux 8.x: installazione e configurazione](https://launchpad.support.sap.com/#/notes/2772999)
            -   [Nota sul supporto SAP #2777782 - SAP HANA DB: Impostazioni consigliate del sistema operativo per RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
            -   [Nota del supporto #2578899 SAP - SUSE Linux Enterprise Server 15: Nota sull'installazione](https://launchpad.support.sap.com/#/notes/2578899)
            -   [Nota di supporto SAP #2455582 - Linux: esecuzione di applicazioni SAP compilate con GCC 6.x](https://launchpad.support.sap.com/#/notes/0002455582)
            -    [Nota del supporto SAP #2729475 - HWCCT non riuscito con errore "Hypervisor non è supportato" nelle macchine virtuali di Azure certificate per SAP HANA](https://launchpad.support.sap.com/#/notes/2729475)
1. Testare le procedure di disponibilità elevata e ripristino di emergenza.
   1. Simulare le situazioni di failover arrestando le macchine virtuali (sistemi operativi guest Windows) o mettendo i sistemi operativi in modalità panico (sistemi operativi guest Linux). Questo passaggio consente di capire se le configurazioni di failover funzionano come previsto.
   1. Misurare il tempo necessario per eseguire un failover. Se i tempi sono troppo lunghi, considerare:
        - Per SUSE Linux, usare i dispositivi SBD anziché l'agente di Azure Fence per velocizzare il failover.
        - Per SAP HANA, se il ricaricamento dei dati richiede troppo tempo, prendere in considerazione il provisioning di una maggiore larghezza di banda di archiviazione.
   3. Testare la sequenza di backup/ripristino e la temporizzazione e apportare le correzioni, se necessario. Assicurarsi che i tempi di backup siano sufficienti. È inoltre necessario testare le attività di ripristino e ripristino temporale. Assicurarsi che i tempi di ripristino rientrino nei contratti di servizio RTO ovunque l'oggetto RTO si trovi su un processo di ripristino di database o di macchine virtuali.
   4. Testare la funzionalità e l'architettura di ripristino di emergenza tra aree.
1. Controlli di sicurezza.
   1. Testare la validità dell'architettura del controllo degli accessi in base al ruolo di Azure.Test the validity of your Azure role-based access control (RBAC) architecture. L'obiettivo è quello di separare e limitare l'accesso e le autorizzazioni di diversi team. Ad esempio, i membri del team SAP Basis devono essere in grado di distribuire macchine virtuali e assegnare dischi da Archiviazione di Azure in una determinata rete virtuale di Azure.For example, SAP Basis team members should be able to deploy VMs and assign disks from Azure Storage into a given Azure virtual network. Tuttavia, il team SAP Basis non dovrebbe essere in grado di creare le proprie reti virtuali o di modificare le impostazioni delle reti virtuali esistenti. I membri del team di rete non devono essere in grado di distribuire le macchine virtuali in reti virtuali in cui sono in esecuzione macchine virtuali dell'applicazione SAP e DBMS. Né i membri di questo team devono essere in grado di modificare gli attributi delle macchine virtuali o persino eliminare macchine virtuali o dischi.  
   1.  Verificare che il gruppo di sicurezza di rete e le regole [ASC](https://docs.microsoft.com/azure/virtual-network/security-overview) funzionino come previsto e schermare le risorse protette.
   1.  Assicurarsi che tutte le risorse che devono essere crittografate lo siano effettivamente. Definire e implementare processi per eseguire il backup dei certificati, archiviare e accedere a tali certificati e ripristinare le entità crittografate.
   1.  Se possibile, usare [Crittografia disco](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq) di Azure per i dischi del sistema operativo, indipendentemente dal punto di vista del supporto del sistema operativo.
   1.  Assicurarsi che non si sta utilizzando troppi livelli di crittografia. In alcuni casi, ha senso usare Crittografia disco di Azure con uno dei metodi DBMS Transparent Data Encryption per proteggere diversi dischi o componenti nello stesso server.  Ad esempio, in un server SAP DBMS, la crittografia del disco di Azure (ADE) può essere abilitata nel disco di avvio del sistema operativo (se il sistema operativo supporta ADE) e nei dischi dati non utilizzati dai file di persistenza dei dati DBMS.  Un esempio consiste nell'utilizzare ADE sul disco che contiene le chiavi di crittografia DBMS TDE.
1. Test delle prestazioni. In SAP, basato su tracciabilità e misurazioni SAP, effettuare i seguenti confronti:
   - Se applicabile, confronta i primi 10 report online con l'implementazione corrente.
   - Se applicabile, confrontare i primi 10 processi batch con l'implementazione corrente.
   - Confrontare i trasferimenti di dati tramite interfacce nel sistema SAP. Concentrarsi sulle interfacce in cui si è a conoscenza del trasferimento tra posizioni diverse, ad esempio da locale ad Azure.Focus on interfaces where you know the transfer is going between different locations, like from on-premises to Azure.


## <a name="non-production-phase"></a>Fase di non produzione 
In questa fase si presuppone che dopo un progetto pilota o una prova di concetto (POC) di successo si stia iniziando a distribuire sistemi SAP non di produzione in Azure.In this phase, we assume that after a successful pilot or proof of concept (POC, you're starting to deploy non-production SAP systems to Azure. Incorpora tutto ciò che hai imparato e sperimentato durante il POC in questa distribuzione. Tutti i criteri e i passaggi elencati per i POC si applicano anche a questa distribuzione.

Durante questa fase, in genere si distribuiscono sistemi di sviluppo, sistemi di unit test e sistemi di test di regressione aziendale in Azure.During this phase, you usually deploy development systems, unit testing systems, and business regression testing systems to Azure. È consigliabile che almeno un sistema non di produzione in una linea di applicazione SAP disponga della configurazione a disponibilità elevata completa del sistema di produzione futuro. Di seguito sono riportati alcuni passaggi aggiuntivi che è necessario completare durante questa fase:Here are some additional steps that you need to complete during this phase:  

1.  Prima di spostare i sistemi dalla piattaforma precedente ad Azure, raccogliere i dati sul consumo delle risorse, ad esempio l'utilizzo della CPU, la velocità effettiva di archiviazione e i dati di IOPS. Soprattutto raccogliere questi dati dalle unità livello DBMS, ma anche raccogliere dalle unità a livello di applicazione. Misurare anche la latenza di rete e di archiviazione.
2.  Registrare i modelli di tempo di utilizzo della disponibilità dei sistemi. L'obiettivo è capire se i sistemi non di produzione devono essere disponibili tutto il giorno, ogni giorno o se ci sono sistemi non di produzione che possono essere arrestati durante determinate fasi di una settimana o di un mese.
3.  Testare e determinare se si desidera creare immagini del sistema operativo personalizzate per le macchine virtuali in Azure o se si vuole usare un'immagine dalla raccolta immagini condivise di Azure.Test and determine whether you want to create your own OS images for your VMs in Azure or whether you want to use an image from the Azure Shared Image Gallery. Se si utilizza un'immagine della Raccolta immagini condivise, assicurarsi di utilizzare un'immagine che rifletta il contratto di supporto con il fornitore del sistema operativo. Per alcuni fornitori di sistemi operativi, Shared Image Gallery consente di portare le proprie immagini di licenza. Per altre immagini del sistema operativo, il supporto è incluso nel prezzo citato da Azure.For other OS images, support is included in the price quoted by Azure. Se si decide di creare immagini di sistema operativo personalizzate, è possibile trovare la documentazione in questi articoli:
    -   [Creare un'immagine generalizzata di una macchina virtuale Windows distribuita in AzureBuild a generalized image of a Windows VM deployed in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
    -   [Creare un'immagine generalizzata di una macchina virtuale Linux distribuita in AzureBuild a generalized image of a Linux VM deployed in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
3.  Se si utilizzano immagini SUSE e Red Hat Linux dalla Raccolta immagini condivise, è necessario utilizzare le immagini per SAP fornite dai fornitori Linux nella raccolta immagini condivise.
4.  Assicurarsi di soddisfare i requisiti di supporto SAP per i contratti di supporto Microsoft. Vedere [sap nota #2015553](https://launchpad.support.sap.com/#/notes/2015553). Per le istanze di grandi dimensioni HANA, vedere [Requisiti di onboarding](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-onboarding-requirements).
4.  Assicurati che le persone giuste ottenghino notifiche di [manutenzione pianificata](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/) in modo da poter scegliere i migliori tempi di inattività.
5.  Controllare spesso la presenza di presentazioni di Azure in canali come [Channel 9](https://channel9.msdn.com/) per le nuove funzionalità che potrebbero essere applicate alle distribuzioni.
6.  Controllare le note SAP correlate ad Azure, ad esempio [nota #1928533 di supporto,](https://launchpad.support.sap.com/#/notes/1928533)per i nuovi SKU delle macchine virtuali e le nuove versioni OS e DBMS supportate. Confrontare i prezzi dei nuovi tipi di macchine virtuali con quelli dei tipi di macchine virtuali meno recenti, in modo da poter distribuire le macchine virtuali con il miglior rapporto prezzo/prestazioni.
7.  Ricontrollare le note di supporto SAP, la directory hardware SAP HANA e SAP PAM. Assicurarsi che non siano state apportate modifiche nelle macchine virtuali supportate per Azure, nelle versioni del sistema operativo supportate in tali macchine virtuali e nelle versioni SAP e DBMS supportate.
8.  Controllare [il sito Web SAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) per i nuovi SKU certificati HANA in Azure.Check the SAP website for new HANA-certified SKUs in Azure. Confronta i prezzi dei nuovi SKU con quelli che hai pianificato di utilizzare. Infine, apportare le modifiche necessarie per utilizzare quelli che hanno il miglior rapporto prezzo/prestazioni.
9.  Adattare gli script di distribuzione per usare nuovi tipi di macchine virtuali e incorporare le nuove funzionalità di Azure che si vuole usare.
10. Dopo la distribuzione dell'infrastruttura, testare e valutare la latenza di rete tra le macchine virtuali a livello di applicazione SAP e le macchine virtuali DBMS, in base alle note del supporto SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) e [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Valutare i risultati rispetto alle linee guida sulla latenza di rete in [#1100926 del supporto SAP.](https://launchpad.support.sap.com/#/notes/1100926/E) La latenza di rete deve essere compresa nell'intervallo moderato o valido. Le eccezioni si applicano al traffico tra macchine virtuali e unità di istanza di grandi dimensioni HANA, come documentato in [questo articolo](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance). Assicurarsi che nessuna delle restrizioni menzionate in [Considerazioni per](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#azure-network-considerations) la distribuzione DBMS di Macchine virtuali di Azure per i carichi di lavoro SAP e le configurazioni e le operazioni [dell'infrastruttura SAP HANA in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) si applichino alla distribuzione.
11. Assicurarsi che le macchine virtuali vengano distribuite nel gruppo di [posizionamento](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)di prossimità di Azure corretto, come descritto in Gruppi di posizionamento di prossimità di Azure per una latenza di [rete ottimale con le applicazioni SAP.](sap-proximity-placement-scenarios.md)
11. Eseguire tutti gli altri controlli elencati per la fase di prova del concetto prima di applicare il carico di lavoro.
12. Man mano che si applica il carico di lavoro, registrare il consumo di risorse dei sistemi in Azure.As the workload applies, record the resource consumption of the systems in Azure. Confronta questo consumo con i record della tua vecchia piattaforma. Modificare il dimensionamento delle macchine virtuali delle distribuzioni future se si verificano ampie differenze. Tenere presente che anche quando si esegue il downsize, l'archiviazione e le larghezze di banda di rete delle macchine virtuali.
    - [Dimensioni per le macchine virtuali Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - [Dimensioni delle macchine virtuali Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. Sperimentare con la funzionalità di copia del sistema e processi. L'obiettivo è semplificare la copia di un sistema di sviluppo o di test, in modo che i team di progetto possano ottenere rapidamente nuovi sistemi. Prendere in considerazione l'utilizzo di [SAP LaMa](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+Landscape+Management+%28SAP+LaMa%29+at+a+Glance) per queste attività.
14. Ottimizza e affina l'accesso, le autorizzazioni e i processi basati sui ruoli di Azure del tuo team per assicurarti di avere la separazione dei compiti. Allo stesso tempo, assicurati che tutti i team possano eseguire le attività nell'infrastruttura di Azure.At the same time, make sure that all teams can perform their tasks in the Azure infrastructure.
15. Esercitare, testare e documentare le procedure di esercizio e ripristino di emergenza per consentire al personale di eseguire queste attività. Identificare le carenze e adattare le nuove funzionalità di Azure che si stanno integrando nelle distribuzioni.

 
## <a name="production-preparation-phase"></a>Fase di preparazione della produzione 
In questa fase, raccogliere ciò che è stato sperimentato e appreso durante le distribuzioni non di produzione e applicarlo alle distribuzioni di produzione future. È inoltre necessario preparare il lavoro del trasferimento dei dati tra la posizione di hosting corrente e Azure.You also need to prepare the work of the data transfer between your current hosting location and Azure.

1.  Completare gli aggiornamenti di rilascio SAP necessari dei sistemi di produzione prima di passare ad Azure.Complete necessary SAP release upgrades of your production systems before moving to Azure.
1.  Concordare con i proprietari di aziende sui test funzionali e aziendali che devono essere condotti dopo la migrazione del sistema di produzione.
1.  Assicurarsi che questi test vengano completati con i sistemi di origine nel percorso di hosting corrente. Evitare di eseguire test per la prima volta dopo lo spostamento del sistema in Azure.Avoid conducting tests for the first time after the system is moved to Azure.
1.  Testare il processo di migrazione dei sistemi di produzione in Azure.Test the process of migrating production systems to Azure. Se non si spostano tutti i sistemi di produzione in Azure durante lo stesso intervallo di tempo, creare gruppi di sistemi di produzione che devono trovarsi nello stesso percorso di hosting. Testare la migrazione dei dati. Ecco alcuni metodi comuni:
    - Usare metodi DBMS come backup/ripristino in combinazione con SQL Server Always On, Replica di sistema HANA o Log shipping per eseguire il seeding e sincronizzare il contenuto del database in Azure.Use DBMS methods like backup/restore in combination with SQL Server Always On, HANA System Replication, or Log shipping to seed and synchronize database content in Azure.
    - Utilizzare il backup/ripristino per database di dimensioni inferiori.
    - Utilizzare SAP Migration Monitor, integrato in SAP SWPM, per eseguire migrazioni eterogenee.
    - Utilizzare il processo [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) se è necessario combinare la migrazione con un aggiornamento del rilascio SAP. Tenere presente che non tutte le combinazioni di DBMS di origine e DBMS di destinazione sono supportate. Ulteriori informazioni sono disponibili nelle note di supporto SAP specifiche per le diverse versioni di DMO. Ad esempio, [L'opzione DMO (Database Migration Option) di SUM 2.0 SP04](https://launchpad.support.sap.com/#/notes/2644872).
    - Verificare se la velocità effettiva di trasferimento dei dati è migliore tramite Internet o ExpressRoute, nel caso in cui sia necessario spostare i backup o i file di esportazione SAP. Se si spostano dati tramite Internet, potrebbe essere necessario modificare alcune delle regole del gruppo di sicurezza di sicurezza di rete o del gruppo di sicurezza dell'applicazione che è necessario disporre per i sistemi di produzione futuri.
1.  Prima di spostare i sistemi dalla piattaforma precedente ad Azure, raccogliere i dati sul consumo delle risorse. I dati utili includono l'utilizzo della CPU, la velocità effettiva di archiviazione e i dati IOPS. Soprattutto raccogliere questi dati dalle unità livello DBMS, ma anche raccogliere dalle unità a livello di applicazione. Misurare anche la latenza di rete e di archiviazione.
1.  Ricontrollare le note di supporto SAP e le impostazioni del sistema operativo necessarie, la directory hardware SAP HANA e SAP PAM. Assicurarsi che non siano state apportate modifiche nelle macchine virtuali supportate per Azure, nelle versioni del sistema operativo supportate in tali macchine virtuali e nelle versioni SAP e DBMS supportate.
1.  Aggiornare gli script di distribuzione per prendere in considerazione le decisioni più recenti prese sui tipi di macchine virtuali e le funzionalità di Azure.Update deployment scripts to take into account the latest decisions you've made on VM types and Azure functionality.
1.  Dopo aver distribuito l'infrastruttura e le applicazioni, verificare che:After deploying infrastructure and applications, validate that:
    - Sono stati distribuiti i tipi di macchine virtuali corretti, con gli attributi e le dimensioni di archiviazione corretti.
    - Le macchine virtuali si trovano nelle versioni e nelle patch del sistema operativo corretto e desiderato e sono uniformi.
    - Le macchine virtuali sono indurite in base alle esigenze e in modo uniforme.
    - Sono state installate e distribuite le versioni e le patch delle applicazioni corrette.
    - Le macchine virtuali sono state distribuite nei set di disponibilità di Azure come pianificato.
    - Archiviazione Premium di Azure viene usata per i dischi sensibili alla latenza o in cui è richiesto il servizio di archiviazione a [macchina virtuale singola del 99,9%.](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)
    - Acceleratore di scrittura di Azure viene distribuito correttamente.
        - Assicurarsi che, all'interno delle macchine virtuali, degli spazi di archiviazione o dei set di striping siano stati creati correttamente tra dischi che richiedono L'acceleratore di scrittura.
        - Controllare la [configurazione del software RAID su Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid).
        - Controllare la [configurazione di LVM in macchine virtuali Linux in Azure.](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)
    - [I dischi gestiti](https://azure.microsoft.com/services/managed-disks/) di Azure vengono usati in modo esclusivo.
    - Le macchine virtuali sono state distribuite nei set di disponibilità e nelle zone di disponibilità corretti.
    - [La rete accelerata](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) di Azure è abilitata nelle macchine virtuali usate nel livello dell'applicazione SAP e nel livello DBMS SAP.
    - Nessuna appliance virtuale di rete di Azure è nel percorso di comunicazione tra l'applicazione SAP e il livello DBMS dei sistemi SAP [basati](https://azure.microsoft.com/solutions/network-appliances/) su SAP NetWeaver, Hybris o S/4HANA.
    - Le regole dei gruppi di sicurezza delle applicazioni e dei gruppi di sicurezza di rete consentono la comunicazione come desiderato e pianificato e bloccano la comunicazione dove necessario.
    - Le impostazioni di timeout sono impostate correttamente, come descritto in precedenza.
    - Le macchine virtuali vengono distribuite al gruppo di [posizionamento](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)di prossimità di Azure corretto, come descritto in Gruppi di posizionamento di prossimità di Azure per una latenza di [rete ottimale con le applicazioni SAP.](sap-proximity-placement-scenarios.md)
    - La latenza di rete tra le macchine virtuali a livello di applicazione SAP e le macchine virtuali DBMS viene testata e convalidata come descritto nelle note di supporto SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) e [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Valutare i risultati rispetto alle linee guida sulla latenza di rete in [#1100926 del supporto SAP.](https://launchpad.support.sap.com/#/notes/1100926/E) La latenza di rete deve essere compresa nell'intervallo moderato o valido. Le eccezioni si applicano al traffico tra macchine virtuali e unità di istanza di grandi dimensioni HANA, come documentato in [questo articolo](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance).
    - La crittografia è stata implementata ove necessario e con il metodo di crittografia appropriato.
    - Le interfacce e altre applicazioni possono connettere l'infrastruttura appena distribuita.
1.  Creare un playbook per reagire alla manutenzione pianificata di Azure.Create a playbook for reacting to planned Azure maintenance. Determinare l'ordine in cui i sistemi e le macchine virtuali devono essere riavviati per la manutenzione pianificata.
    

## <a name="go-live-phase"></a>Fase Go-live
Durante la fase di go-live, assicurati di seguire i playbook che hai sviluppato durante le fasi precedenti. Eseguire i passaggi testati e praticati. Non accettare modifiche dell'ultimo minuto nelle configurazioni e nei processi. Completare anche questi passaggi:

1. Verificare il corretto funzionamento del monitoraggio del portale di Azure e di altri strumenti di monitoraggio. Si consiglia Performance Monitor di Windows (perfmon) per Windows e SAR per Linux.
    - contatori della CPU.
        - Tempo medio CPU, totale (tutte le CPU)
        - Tempo medio CPU, ogni singolo processore (128 processori in macchine virtuali M128)
        - tempo kernel CPU, ogni singolo processore
        - CPU utente, ogni singolo processore
    - Memoria.
        - Memoria libera
        - Pagina Memoria in/secondo
        - Pagina di uscita memoria/secondo
    - Disk (Disco).
        - Lettura su disco in KBps, per singolo disco
        - Letture disco al secondo, per singolo disco
        - Lettura su disco in microsecondi/lettura, per singolo disco
        - Scrittura su disco in KBps, per singolo disco
        - Scrittura su disco/secondo, per singolo disco
        - Scrittura su disco in microsecondi/lettura, per singolo disco
    - Network.
        - Pacchetti di rete in/secondo
        - Pacchetti di rete in uscita al secondo
        - KB di rete in/secondo
        - KB di rete in uscita al secondo
1.  Dopo la migrazione dei dati, eseguire tutti i test di convalida concordati con i proprietari dell'azienda. Accettare i risultati dei test di convalida solo quando si dispone di risultati per i sistemi di origine originali.
1.  Verificare se le interfacce funzionano e se altre applicazioni possono comunicare con i sistemi di produzione appena distribuiti.
1.  Controllare il sistema di trasporto e correzione tramite STMS della transazione SAP.
1.  Eseguire i backup del database dopo il rilascio del sistema per la produzione.
1.  Eseguire i backup delle macchine virtuali per le macchine virtuali a livello di applicazione SAP dopo il rilascio del sistema per la produzione.
1.  Per i sistemi SAP che non facevano parte della fase di go-live corrente ma che comunicano con i sistemi SAP spostati in Azure durante questa fase di go-live, è necessario reimpostare il buffer dei nomi host in SM51. In questo modo verranno rimossi i vecchi indirizzi IP memorizzati nella cache associati ai nomi delle istanze dell'applicazione spostate in Azure.Doing so will remove the old cached IP addresses associated with the names of the application instances you moved to Azure.  


## <a name="post-production"></a>Post-produzione
Questa fase riguarda il monitoraggio, il funzionamento e l'amministrazione del sistema. Da un punto di vista SAP, si applicano le normali attività che è necessario completare nella vecchia posizione di hosting. Completare anche queste attività specifiche di Azure:Complete these Azure-specific tasks as well:

1. Esaminare le fatture di Azure per i sistemi di ricarica elevata.
2. Ottimizza l'efficienza prezzo/prestazioni sul lato VM e storage.
3. Ottimizzare i tempi di arresto dei sistemi.  


## <a name="next-steps"></a>Passaggi successivi
 Vedere i seguenti articoli:

- [Guida alla pianificazione e all'implementazione di macchine virtuali di Azure per SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Distribuzione di Macchine virtuali di Azure per SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Considerazioni per la distribuzione DBMS di Macchine virtuali di Azure per carichi di lavoro SAPConsiderations for Azure Virtual Machines DBMS deployment for SAP workloads](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

