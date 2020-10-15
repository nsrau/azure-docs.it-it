---
title: Aree e zone di disponibilità in Azure
description: Informazioni sulle aree e zone di disponibilità in Azure per soddisfare i requisiti tecnici e normativi.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 08/27/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: 5178bf6955203c6a5625b941dd0102f2f4458abc
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078375"
---
# <a name="regions-and-availability-zones-in-azure"></a>Aree e zone di disponibilità in Azure

I servizi Microsoft Azure sono disponibili a livello globale per guidare le operazioni cloud a un livello ottimale. È possibile scegliere l'area migliore per le proprie esigenze in base a considerazioni tecniche e normative: funzionalità del servizio, residenza dei dati, requisiti di conformità e latenza.

## <a name="terminology"></a>Terminologia

Per comprendere meglio le aree e zone di disponibilità in Azure, è utile comprendere i termini o i concetti chiave.

| Termine o concetto | Descrizione |
| --- | --- |
| region | Set di data center distribuiti in un perimetro definito dalla latenza e connessi tramite una rete a bassa latenza regionale dedicata. |
| geography | Un'area del mondo che contiene almeno un'area di Azure. Le aree geografiche definiscono un mercato discreto che mantiene i limiti di residenza e di conformità dei dati. Le aree geografiche consentono ai clienti con esigenze specifiche a livello di residenza dei dati e conformità di mantenere vicini i propri dati e le proprie applicazioni. Le aree geografiche sono a tolleranza d'errore per resistere a un errore di area completa attraverso la connessione all'infrastruttura di rete a capacità elevata dedicata. |
| Zona di disponibilità | Percorsi fisici univoci all'interno di un'area. Ogni zona è costituita da uno o più data center dotati di impianti indipendenti per l'alimentazione, il raffreddamento e la connettività di rete. |
| area consigliata | Area che fornisce la più ampia gamma di funzionalità del servizio ed è progettata per supportare zone di disponibilità ora o in futuro. Queste sono indicate nel portale di Azure come **consigliato**. |
| area alternativa (altro) | Area che estende il footprint di Azure entro un limite di residenza dei dati in cui esiste anche un'area consigliata. Le aree alternative consentono di ottimizzare la latenza e forniscono una seconda area per le esigenze di ripristino di emergenza. Non sono progettati per supportare la zone di disponibilità (anche se Azure esegue una valutazione regolare di queste aree per determinare se devono diventare aree consigliate). Queste sono indicate nel portale di Azure come **altro**. |
| servizio Foundation | Un servizio di base di Azure disponibile in tutte le aree in cui l'area è disponibile a livello generale. |
| servizio mainstream | Un servizio di Azure disponibile in tutte le aree consigliate entro 12 mesi dalla disponibilità generale a livello di area/servizio o alla disponibilità basata su richiesta in aree alternative. |
| servizio specializzato | Un servizio di Azure che è una disponibilità basata su richiesta tra le aree supportate da hardware personalizzato o specializzato. |
| servizio a livello di area | Un servizio di Azure distribuito a livello di area e consente al cliente di specificare l'area in cui verrà distribuito il servizio. Per un elenco completo, vedere [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=all). |
| servizio non a livello di area | Un servizio di Azure per il quale non esiste alcuna dipendenza da una specifica area di Azure. I servizi non a livello di area vengono distribuiti in due o più aree e se si verifica un errore a livello di area, l'istanza del servizio in un'altra area continua a servire i clienti. Per un elenco completo, vedere [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=all). |

## <a name="regions"></a>Regioni

Un'area è un set di centri dati distribuiti in un perimetro definito dalla latenza e connessi tramite una rete a bassa latenza regionale dedicata. Azure offre la flessibilità necessaria per distribuire le applicazioni in cui è necessario, tra cui più aree, per offrire resilienza tra aree diverse. Per altre informazioni, vedere [Panoramica del pilastro di resilienza](/azure/architecture/framework/resiliency/overview).

## <a name="availability-zones"></a>Zone di disponibilità

Una zona di disponibilità è un'offerta a disponibilità elevata che protegge le applicazioni e i dati dagli errori dei data center. Le zone di disponibilità sono località fisiche esclusive all'interno di un'area di Azure. Ogni zona è costituita da uno o più data center dotati di impianti indipendenti per l'alimentazione, il raffreddamento e la connettività di rete. Per garantire la resilienza sono presenti almeno tre zone separate in tutte le aree abilitate. La separazione fisica delle zone di disponibilità all'interno di un'area consente di proteggere le applicazioni e i dati da eventuali guasti del data center. I servizi con ridondanza della zona replicano le applicazioni e i dati tra aree di disponibilità per garantire la protezione da singoli punti di errore. Con le zone di disponibilità, Azure offre un contratto di servizio con tempo di attività delle VM del 99,99% tra i migliori del settore. La versione completa del [contratto di servizio di Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) descrive la disponibilità garantita di Azure nel suo complesso.

Una zona di disponibilità in un'area di Azure è una combinazione di un dominio di errore e un dominio di aggiornamento. Ad esempio, se si creano tre o più macchine virtuali in tre aree in un'area di Azure, le macchine virtuali vengono distribuite in modo efficace in tre domini di errore e tre domini di aggiornamento. La piattaforma Azure riconosce questa distribuzione tra i domini di aggiornamento per assicurarsi che le macchine virtuali in zone diverse non siano pianificate per l'aggiornamento nello stesso momento.

È possibile configurare la disponibilità elevata nell'architettura delle applicazioni includendo le risorse di calcolo, archiviazione, rete e dati all'interno di una zona e replicandole in altre zone. I servizi di Azure che supportano le zone di disponibilità rientrano in due categorie:

- **Servizi di zona** : una risorsa è bloccata a una zona specifica, ad esempio macchine virtuali, dischi gestiti, indirizzi IP standard o
- **Servizi con ridondanza della zona** : quando la piattaforma Azure viene replicata automaticamente tra le zone, ad esempio l'archiviazione con ridondanza della zona, il database SQL.

Per garantire la continuità aziendale completa in Azure, creare l'architettura delle applicazioni combinando le zone di disponibilità con coppie di aree di Azure. È possibile replicare in modo sincrono le applicazioni e i dati usando le zone di disponibilità all'interno di un'area di Azure per la disponibilità elevata ed eseguire repliche asincrone tra le aree di Azure per la protezione con ripristino di emergenza.
 
![Visualizzazione concettuale di una zona che diventa indisponibile in un'area](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> Gli identificatori delle zone di disponibilità (i numeri 1, 2 e 3 nell'immagine precedente) vengono mappati logicamente alle zone fisiche effettive per ogni sottoscrizione in modo indipendente. Ciò significa che Zona 1 di disponibilità in una determinata sottoscrizione possono fare riferimento a una zona fisica diversa rispetto alla disponibilità Zona 1 in una sottoscrizione diversa. Di conseguenza, è consigliabile non fare affidamento sugli ID della zona di disponibilità tra sottoscrizioni diverse per la selezione host per macchina virtuale.

## <a name="region-and-service-categories"></a>Aree e categorie di servizi

L'approccio di Azure sulla disponibilità dei servizi di Azure tra le aree è più adatto per la descrizione tramite l'espressione dei servizi resi disponibili nelle aree consigliate e nelle aree alternative.

- **Area consigliata** : area che fornisce la più ampia gamma di funzionalità del servizio ed è progettata per supportare zone di disponibilità ora o in futuro. Queste sono indicate nel portale di Azure come **consigliato**.
- **Area alternativa (altra)** : area che estende il footprint di Azure entro un limite di residenza dei dati in cui esiste anche un'area consigliata. Le aree alternative consentono di ottimizzare la latenza e forniscono una seconda area per le esigenze di ripristino di emergenza. Non sono progettati per supportare la zone di disponibilità (anche se Azure esegue una valutazione regolare di queste aree per determinare se devono diventare aree consigliate). Queste sono indicate nel portale di Azure come **altro**.

### <a name="comparing-region-types"></a>Confronto tra tipi di area

I servizi di Azure sono raggruppati in tre categorie: fondamentale, principale e specializzato. I criteri generali di Azure per la distribuzione dei servizi in una determinata area sono principalmente basati sul tipo di area, sulle categorie di servizi e sulla domanda dei clienti:

- **Fondamento** : disponibile in tutte le aree consigliate e alternative quando l'area è disponibile a livello generale oppure entro 12 mesi da un nuovo servizio di base che diventa disponibile a livello generale.
- **Mainstream** : disponibile in tutte le aree consigliate entro 12 mesi dalla disponibilità generale a livello di area/servizio. basati su richiesta in aree alternative (molte sono già distribuite in un ampio subset di aree alternative).
- Offerte di servizi mirate **specializzate** , spesso incentrate sul settore o basate su hardware personalizzato o specializzato. Disponibilità basata su richiesta tra le aree (molte sono già distribuite in un ampio subset di aree consigliate).

Per visualizzare i servizi distribuiti in una determinata area, nonché la roadmap futura per l'anteprima o la disponibilità generale dei servizi in un'area, vedere [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=all).

Se un'offerta di servizio non è disponibile in un'area specifica, è possibile condividere il proprio interesse contattando il rappresentante Microsoft.

| Tipo di area | Non a livello di area | Fondamentale | Mainstream | Specializzata | Zone di disponibilità | Residenza dei dati |
| --- | --- | --- | --- | --- | --- | --- |
| Consigliato | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Basata su richiesta | :heavy_check_mark: | :heavy_check_mark: |
| Alternativo | :heavy_check_mark: | :heavy_check_mark: | Basata su richiesta | Basata su richiesta | N/D | :heavy_check_mark: |

### <a name="services-by-category"></a>Servizi per categoria

Come indicato in precedenza, Azure classifica i servizi in tre categorie: fondamentale, mainstream e specializzato. Le categorie di servizio vengono assegnate a livello generale. Spesso i servizi avviano il ciclo di vita come servizio specializzato e gli aumenti di richiesta e utilizzo possono essere promossi al mainstream o alla base. La tabella seguente elenca la categoria per i servizi come base, mainstream o specializzata. Per la tabella è necessario tenere presente quanto segue:

- Alcuni servizi non sono a livello di area. Per informazioni e un elenco di servizi non regionali, vedere [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/).
- Le macchine virtuali di generazione meno recenti non sono elencate. Per ulteriori informazioni, vedere la documentazione relativa alle [generazioni precedenti di dimensioni di macchine virtuali](../virtual-machines/sizes-previous-gen.md).

> [!div class="mx-tableFixed"]
> | Fondamentale | Mainstream | Specializzata |
> | --- | --- | --- |
> | Archiviazione account | Gestione API | API di Azure per FHIR |
> | Gateway applicazione | Configurazione app | Azure Analysis Services |
> | Backup di Azure | Servizio app | Servizio Azure Blockchain |
> | Azure Cosmos DB | Automazione | Azure Blueprint |
> | Azure Data Lake Storage Gen2 | Servizi di dominio Azure Active Directory | Database di Azure per MariaDB |
> | Azure ExpressRoute | Azure Bastion | Modulo di protezione hardware dedicato di Azure |
> | database SQL di Azure | Cache Redis di Azure | Azure Dev Spaces |
> | Servizi cloud | Ricerca cognitiva di Azure | Gemelli digitali di Azure |
> | Servizi cloud: Av2-Series | Esplora dati di Azure | Azure Lab Services |
> | Servizi cloud: Dv2-Series | Condivisione dati di Azure | Azure NetApp Files |
> | Servizi cloud: Dv3-Series | Database di Azure per MySQL | Quantum di Azure |
> | Servizi cloud: Ev3-Series | Database di Azure per PostgreSQL | Servizio cloud Spring di Azure |
> | Servizi cloud: indirizzi IP a livello di istanza | Servizio Migrazione del database di Azure | Azure Time Series Insights |
> | Servizi cloud: IP riservato | Azure Databricks | Soluzione Azure VMware di CloudSimple |
> | Archiviazione su disco | Protezione DDoS di Azure | Servizi cloud: serie G |
> | Hub eventi | Azure DevTest Labs | Servizi cloud: serie H |
> | Key Vault | Firewall di Azure | Servizi cognitivi: Visione personalizzata |
> | Bilanciamento del carico | Gestione firewall di Azure | Servizi cognitivi: Riconoscimento del parlante |
> | Bus di servizio | Funzioni di Azure | Data Box Heavy |
> | Service Fabric | Cache HPC di Azure | Data Catalog |
> | Set di scalabilità di macchine virtuali | Hub IoT Azure | Data Factory: Data Factory V1 |
> | Macchine virtuali | Servizio Azure Kubernetes | Data Lake Analytics |
> | Macchine virtuali: Av2-Series | Azure Machine Learning | Azure Machine Learning Studio (versione classica)|
> | Macchine virtuali: Bs-Series | Collegamento privato di Azure | Genomica di Microsoft |
> | Macchine virtuali: DSv2-Series | Azure Red Hat OpenShift | Rendering remoto |
> | Macchine virtuali: DSv3-Series | Servizio Azure SignalR | Ancoraggi nello spazio |
> | Macchine virtuali: Dv2-Series | Azure Site Recovery | StorSimple |
> | Macchine virtuali: Dv3-Series | Hub di Azure Stack | Video Indexer |
> | Macchine virtuali: ESv3-Series | Analisi di flusso di Azure | Macchine virtuali: DASv4-Series |
> | Macchine virtuali: Ev3-Series | Azure Synapse Analytics | Macchine virtuali: DAv4-Series |
> | Macchine virtuali: serie F | Batch | Macchine virtuali: serie DCsv2 |
> | Macchine virtuali: FS-Series | Servizi cloud: serie M | Macchine virtuali: EASv4-Series |
> | Macchine virtuali: indirizzi IP a livello di istanza | Servizi cognitivi | Macchine virtuali: EAv4-Series |
> | Macchine virtuali: IP riservato | Servizi cognitivi: Visione artificiale | Macchine virtuali: serie G |
> | Rete virtuale | Servizi cognitivi: Content Moderator | Macchine virtuali: GS-Series |
> | Gateway VPN | Servizi cognitivi: viso | Macchine virtuali: HBv1-Series |
> |  | Servizi cognitivi: riconoscimento moduli | Macchine virtuali: HBv2-Series |
> |  | Servizi cognitivi: Language Understanding | Macchine virtuali: HCv1-Series |
> |  | Servizi cognitivi: QnA Maker | Macchine virtuali: serie H |
> |  | Servizi cognitivi: servizi vocali | Macchine virtuali: LS-Series |
> |  | Istanze di contenitore | Macchine virtuali: LSv2-Series |
> |  | Registro Container | Macchine virtuali: Mv2-Series |
> |  | Data Factory | Macchine virtuali: NC-Series |
> |  | Griglia di eventi | Macchine virtuali: NCv2-Series |
> |  | HDInsight | Macchine virtuali: NCv3-Series |
> |  | App per la logica | Macchine virtuali: NDs-Series |
> |  | Servizi multimediali | Macchine virtuali: NDv2-Series |
> |  | Network Watcher | Macchine virtuali: NV-Series |
> |  | Hub di notifica | Macchine virtuali: NVv3-Series |
> |  | Power BI Embedded | Macchine virtuali: NVv4-Series |
> |  | Archiviazione BLOB Premium | Macchine virtuali: SAP HANA in istanze Large di Azure |
> |  | Archiviazione file Premium | Visual Studio App Center |
> |  | Archiviazione: Spazio di archiviazione |  |
> |  | archiviazione su disco Ultra |  |
> |  | Macchine virtuali: Ddsv4-Series |  |
> |  | Macchine virtuali: Ddv4-Series |  |
> |  | Macchine virtuali: Dsv4-Series |  |
> |  | Macchine virtuali: Dv4-Series |  |
> |  | Macchine virtuali: Edsv4-Series |  |
> |  | Macchine virtuali: Edv4-Series |  |
> |  | Macchine virtuali: Esv4-Series |  |
> |  | Macchine virtuali: Ev4-Series |  |
> |  | Macchine virtuali: Fsv2-Series |  |
> |  | Macchine virtuali: serie M |  |
> |  | Rete WAN virtuale |  |

###  <a name="services-resiliency"></a>Resilienza dei servizi

Tutti i servizi di gestione di Azure sono progettati per essere resilienti da errori a livello di area. Nello spettro degli errori, uno o più errori della zona di disponibilità all'interno di un'area hanno un raggio di errore inferiore rispetto a un errore dell'intera area. Azure può eseguire il ripristino da un errore a livello di zona dei servizi di gestione all'interno dell'area o da un'altra area di Azure. Azure esegue una manutenzione critica di una zona alla volta all'interno di un'area, per evitare errori che influiscano sulle risorse dei clienti distribuite tra zone di disponibilità all'interno di un'area.

### <a name="pricing-for-vms-in-availability-zones"></a>Prezzi per le macchine virtuali in zone di disponibilità

Per le macchine virtuali distribuite in una zona di disponibilità non sono previsti costi aggiuntivi. Il contratto di servizio con tempo di attività delle VM del 99,99% viene offerto quando due o più macchine virtuali vengono distribuite tra due o più zone di disponibilità all'interno di un'area di Azure. Vengono applicati addebiti per il trasferimento dei dati da VM a VM tra diverse zone di disponibilità. Per altre informazioni, vedere la pagina [Dettagli sui prezzi per la larghezza di banda](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="get-started-with-availability-zones"></a>Introduzione alle zone di disponibilità

- [Creare una macchina virtuale](../virtual-machines/windows/create-portal-availability-zone.md)
- [Aggiungere un disco gestito usando PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Creare un set di scalabilità di macchine virtuali che usa le zone di disponibilità](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Bilanciare il carico delle macchine virtuali tra zone usando un servizio Load Balancer Standard con un front-end con ridondanza della zona](../load-balancer/quickstart-load-balancer-standard-public-cli.md)
- [Bilanciare il carico delle macchine virtuali all'interno di una zona usando un servizio Load Balancer Standard con un front-end di zona](../load-balancer/quickstart-load-balancer-standard-public-cli.md)
- [Archiviazione con ridondanza della zona](../storage/common/storage-redundancy.md)
- [Database SQL](../azure-sql/database/high-availability-sla.md#zone-redundant-configuration)
- [Ripristino di emergenza geografico di Hub eventi](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Ripristino di emergenza geografico del bus di servizio](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Creare un gateway di rete virtuale con ridondanza della zona](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Aggiungere un'area con ridondanza della zona per Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Introduzione cache di Azure per Redis zone di disponibilità](https://aka.ms/redis/az/getstarted)
- [Creare un'istanza di Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md)
- [Creare un cluster Azure Kubernetes Service (AKS) che usa zone di disponibilità](../aks/availability-zones.md)

## <a name="next-steps"></a>Passaggi successivi

- [Aree che supportano zone di disponibilità in Azure](az-region.md)
- [Modelli di avvio rapido](https://aka.ms/azqs)