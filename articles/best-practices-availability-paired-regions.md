---
title: Garantire la continuità aziendale & il ripristino di emergenza usando le aree associate di AzureEnsure business continuity & disaster recovery using Azure Paired Regions
description: Garantire la resilienza delle applicazioni usando l'associazione regionale di AzureEnsure application resiliency using Azure regional pairing
author: jpconnock
manager: angrobe
ms.service: multiple
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: jeconnoc
ms.openlocfilehash: 778943dad9a04632797d5d9165b6f1f9a3eb9850
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248255"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Continuità aziendale e ripristino di emergenza nelle aree geografiche abbinate di Azure

## <a name="what-are-paired-regions"></a>Definizione di aree abbinate

Un'area di Azure è costituita da un set di data center distribuiti all'interno di un perimetro definito dalla latenza e connessi tramite una rete dedicata a bassa latenza.  Ciò garantisce che i servizi di Azure all'interno di un'area di Azure offrano le migliori prestazioni e sicurezza possibili.  

Un'area geografica di Azure definisce un'area del mondo contenente almeno un'area di Azure.An Azure geography defines an area of the world containing at least one Azure region. Le aree geografiche definiscono un mercato discreto, in genere contenente due o più aree, che conservai i limiti di residenza e conformità dei dati.  Ulteriori informazioni sull'infrastruttura globale di Azure [sono disponibili qui](https://azure.microsoft.com/global-infrastructure/regions/)

Una coppia di regioni è costituita da due regioni all'interno della stessa area geografica. Azure serializza gli aggiornamenti della piattaforma (manutenzione pianificata) tra coppie regionali, assicurando che solo un'area in ogni coppia venga aggiornata alla volta. Se un'interruzione interessa più aree, almeno un'area in ogni coppia verrà assegnata una priorità per il ripristino.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Alcuni servizi di Azure sfruttano ulteriormente le aree abbinate per garantire la continuità aziendale e la protezione contro la perdita di dati.  Azure offre diverse [soluzioni di archiviazione](./storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) che sfruttano le aree abbinate per garantire la disponibilità dei dati. Ad esempio, [Azure Geo-redundant Storage](./storage/common/storage-redundancy.md#geo-redundant-storage) (GRS) replica automaticamente i dati in un'area secondaria, assicurando che i dati siano durevoli anche nel caso in cui l'area primaria non sia recuperabile. 

Si noti che non tutti i servizi di Azure replicano automaticamente i dati, né tutti i servizi di Azure eseguono automaticamente il failback da un'area di cui è stata eriuscita alla relativa coppia.  In questi casi, il ripristino e la replica devono essere configurati dal cliente.

## <a name="can-i-select-my-regional-pairs"></a>Posso selezionare le mie coppie regionali?

No. Alcuni servizi di Azure si basano su coppie regionali, ad esempio [l'archiviazione ridondante](./storage/common/storage-redundancy.md)di Azure. Questi servizi non consentono di creare nuovi abbinamenti regionali.  Analogamente, poiché Azure controlla la definizione delle priorità di manutenzione e ripristino pianificate per le coppie regionali, non è possibile definire coppie di aree a livello di utente personalizzate per sfruttare questi servizi. Tuttavia, è possibile creare la propria soluzione di ripristino di emergenza creando servizi in un numero qualsiasi di aree e sfruttando i servizi di Azure per associarli. 

Ad esempio, è possibile usare servizi di Azure, ad esempio [AzCopy](./storage/common/storage-use-azcopy-v10.md) per pianificare i backup dei dati in un account di archiviazione in un'area diversa.  Usando DNS di Azure e Gestione traffico di [Azure,](./networking/disaster-recovery-dns-traffic-manager.md)i clienti possono progettare un'architettura resiliente per le applicazioni che sopravviveranno alla perdita dell'area primaria.

## <a name="am-i-limited-to-using-services-within-my-regional-pairs"></a>Sono limitato all'utilizzo dei servizi all'interno delle mie coppie regionali?

No. Anche se un determinato servizio di Azure può basarsi su una coppia di aree, è possibile ospitare gli altri servizi in qualsiasi area che soddisfi le esigenze aziendali.  Una soluzione di archiviazione Azure GRS può associare i dati in Canada Central a un peer in Canada East durante l'utilizzo di risorse di calcolo situate negli Stati Uniti orientali.  

## <a name="must-i-use-azure-regional-pairs"></a>È necessario usare le coppie di aree internazionali di Azure?

No. I clienti possono sfruttare i servizi di Azure per progettare un servizio resiliente senza basarsi sulle coppie regionali di Azure.Customers can leverage Azure services to architect a resilient service without relying on Azure's regional pairs.  Tuttavia, è consigliabile configurare il ripristino di emergenza con continuità aziendale (BCDR) tra coppie regionali per trarre vantaggio [dall'isolamento](./security/fundamentals/isolation-choices.md) e migliorare la [disponibilità.](./availability-zones/az-overview.md) Per le applicazioni che supportano più aree attive, è consigliabile usare entrambe le aree di una coppia, dove possibile. Ciò garantisce una disponibilità ottimale per le applicazioni e tempi di ripristino ridotti al minimo in caso di emergenza. Quando possibile, progettare l'applicazione per [la massima resilienza](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview) e facilità di ripristino di [emergenza](https://docs.microsoft.com/azure/architecture/framework/resiliency/backup-and-recovery).

## <a name="azure-regional-pairs"></a>Coppie regionali di AzureAzure Regional Pairs

| Area geografica | Coppia Regionale A | Coppia regionale B  |
|:--- |:--- |:--- |
| Asia-Pacifico |Asia orientale (Hong Kong) | Sud-Est asiatico (Singapore) |
| Australia |Australia orientale |Australia sud-orientale |
| Australia |Australia centrale |Australia centrale 2 |
| Brasile |Brasile meridionale |Stati Uniti centro-meridionali |
| Canada |Canada centrale |Canada orientale |
| Cina |Cina settentrionale |Cina orientale|
| Cina |Cina settentrionale 2 |Cina orientale 2|
| Europa |Nord Europa (Irlanda) |Europa occidentale (Paesi Bassi) |
| Francia |Francia centrale|Francia meridionale|
| Germania |Germania centrale |Germania nord-orientale |
| India |India centrale |India meridionale |
| India |India occidentale |India meridionale |
| Giappone |Giappone orientale |Giappone occidentale |
| Corea del Sud |Corea centrale |Corea meridionale |
| America del Nord |Stati Uniti orientali |Stati Uniti occidentali |
| America del Nord |Stati Uniti orientali 2 |Stati Uniti centrali |
| America del Nord |Stati Uniti centro-settentrionali |Stati Uniti centro-meridionali |
| America del Nord |Stati Uniti occidentali 2 |Stati Uniti centro-occidentali |
| Norvegia | Norvegia orientale | Norvegia Ovest |
| Sud Africa | Sudafrica settentrionale |Sudafrica Ovest |
| Svizzera | Svizzera Nord |Svizzera Ovest |
| Regno Unito |Regno Unito occidentale |Regno Unito meridionale |
| Emirati Arabi Uniti | Emirati Arabi Uniti settentrionali | Emirati Arabi Uniti centrali
| Dipartimento della difesa degli Stati Uniti |US DoD (area orientale) |US DoD (area centrale) |
| US Gov |US Gov Arizona |US Gov Texas |
| US Gov |US Gov Iowa |US Gov Virginia |
| US Gov |US Gov Virginia |US Gov Texas |

> [!Important]
> - L'India occidentale è accoppiata in una sola direzione. L'area secondaria dell'area India occidentale è India meridionale, mentre l'area secondaria dell'India meridionale è India centrale.
> - Brasile Sud è unico perché è accoppiato con una regione al di fuori della sua geografia. La regione secondaria del Brasile meridionale è gli Stati Uniti centro-meridionali. La regione secondaria degli Stati Uniti centro-meridionali non è il Brasile meridionale.


## <a name="an-example-of-paired-regions"></a>Esempio di aree abbinate
L'immagine seguente illustra un'ipotetica applicazione che usa la coppia regionale per il ripristino di emergenza. I numeri verdi evidenziano le attività tra aree di tre servizi di Azure (calcolo di Azure, archiviazione e database) e la modalità di replica tra aree. I vantaggi esclusivi della distribuzione tra aree abbinate sono evidenziali dai numeri in arancione.

![Panoramica dei vantaggi delle aree abbinate](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Figura 2: ipotetica coppia di aree di Azure

## <a name="cross-region-activities"></a>Attività tra aree
Come indicato nella figura 2.

1. **Calcolo di Azure (IaaS):** è necessario eseguire in anticipo il provisioning di risorse di calcolo aggiuntive per assicurarsi che le risorse siano disponibili in un'altra area durante un'emergenza. Per altre informazioni, vedere [Informazioni tecniche sulla resilienza di Azure](https://github.com/uglide/azure-content/blob/master/articles/resiliency/resiliency-technical-guidance.md). 

2. **Archiviazione di Azure:** se si usano dischi gestiti, vedere Informazioni sui backup tra aree con Backup di Azure e sulla replica delle macchine virtuali da un'area a un'altra con Azure Site Recovery.Azure Storage - If you're using managed disks, learn about [cross-region backups](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines) with Azure Backup, and [replicating VMs](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication) from one region to another with Azure Site Recovery. Se si usano account di archiviazione, l'archiviazione con ridondanza geografica (GRS) viene configurata per impostazione predefinita quando viene creato un account di archiviazione di Azure.If you're using storage accounts, then geo-redundant storage (GRS) is configured by default when an Azure Storage account is created. Con questa opzione di replica, i dati vengono replicati per tre volte all'interno dell'area primaria e per tre volte nell'area abbinata. Per altre informazioni, vedere Opzioni di [ridondanza di Archiviazione di Azure](storage/common/storage-redundancy.md).For more information, see Azure Storage Redundancy Options .

3. Database SQL di **Azure:** con la replica geografica del database SQL di Azure, è possibile configurare la replica asincrona delle transazioni in qualsiasi area del mondo. Tuttavia, è consigliabile distribuire queste risorse in un'area associata per la maggior parte degli scenari di ripristino di emergenza. Per altre informazioni, vedere l'articolo relativo alla [replica geografica nel database SQL di Azure](sql-database/sql-database-geo-replication-overview.md).

4. **Azure Resource Manager**: Resource Manager fornisce implicitamente l'isolamento logico dei componenti tra le aree. In questo modo, è meno probabile che gli errori logici in un'area abbiano un impatto su un'altra.

## <a name="benefits-of-paired-regions"></a>Vantaggi delle aree abbinate

5. **Isolamento fisico:** quando possibile, Azure preferisce almeno 300 miglia di separazione tra i data center in una coppia di aree, anche se questo non è pratico o possibile in tutte le aree geografiche. La separazione dei data center fisici riduce la possibilità che calamità naturali, agitazioni sociali, interruzioni dell'alimentazione o interruzioni della rete fisica interessino entrambe le aree contemporaneamente. L'isolamento è soggetto ai vincoli presenti all'interno dell'area geografica (dimensioni dell'area geografica, disponibilità dell'infrastruttura di rete/alimentazione, normative e così via).  

6. **Replica fornita dalla piattaforma:** alcuni servizi, ad esempio Archiviazione con ridondanza geografica, forniscono la replica automatica all'area associata.

7. **Ordine** di ripristino dell'area: in caso di interruzione generale, il ripristino di un'area viene assegnata una priorità a ogni coppia. Per le applicazioni distribuite in aree abbinate viene garantito che una delle aree sarà ripristinata con priorità. Se un'applicazione viene distribuita in aree non abbinate, il ripristino potrebbe essere ritardato: nel peggiore dei casi le aree scelte potrebbero essere le ultime due a essere ripristinate.

8. **Aggiornamenti sequenziali:** gli aggiornamenti di sistema di Azure pianificati vengono implementati in aree abbinate in sequenza (non contemporaneamente) per ridurre al minimo i tempi di inattività, l'effetto dei bug e gli errori logici nel raro caso di un aggiornamento non valido.

9. **Residenza** dei dati : una regione risiede all'interno della stessa area geografica della coppia (ad eccezione del Brasile meridionale) per soddisfare i requisiti di residenza dei dati ai fini fiscali e di giurisdizione delle forze dell'ordine.
