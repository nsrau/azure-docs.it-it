---
title: Garantire la continuità aziendale & il ripristino di emergenza usando le aree abbinate di Azure
description: Garantire la resilienza delle applicazioni usando l'associazione a livello di area di Azure
author: jpconnock
manager: angrobe
ms.service: multiple
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: jeconnoc
ms.openlocfilehash: 778943dad9a04632797d5d9165b6f1f9a3eb9850
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79248255"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Continuità aziendale e ripristino di emergenza nelle aree geografiche abbinate di Azure

## <a name="what-are-paired-regions"></a>Definizione di aree abbinate

Un'area di Azure è costituita da un set di data center distribuiti in un perimetro definito dalla latenza e connessi tramite una rete a bassa latenza dedicata.  Ciò garantisce che i servizi di Azure all'interno di un'area di Azure offrano le migliori prestazioni e sicurezza possibili.  

Una geografia di Azure definisce un'area del mondo che contiene almeno un'area di Azure. Le aree geografiche definiscono un mercato discreto, in genere contenente due o più aree, che conservano i limiti di residenza e di conformità dei dati.  Altre informazioni sull'infrastruttura globale di Azure sono disponibili [qui](https://azure.microsoft.com/global-infrastructure/regions/)

Una coppia locale è costituita da due aree all'interno della stessa area geografica. Azure serializza gli aggiornamenti della piattaforma (manutenzione pianificata) tra coppie di aree, assicurando l'aggiornamento di una sola area in ogni coppia alla volta. Se un'interruzione interessa più aree, per il ripristino verrà assegnata priorità almeno un'area di ogni coppia.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Alcuni servizi di Azure sfruttano ulteriormente le aree abbinate per garantire la continuità aziendale e proteggersi dalla perdita di dati.  Azure offre diverse [soluzioni di archiviazione](./storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) che sfruttano le aree abbinate per garantire la disponibilità dei dati. Ad esempio, l' [archiviazione con ridondanza geografica](./storage/common/storage-redundancy.md#geo-redundant-storage) (GRS) di Azure replica automaticamente i dati in un'area secondaria, assicurando che i dati siano durevoli anche nel caso in cui l'area primaria non sia recuperabile. 

Si noti che non tutti i servizi di Azure replicano automaticamente i dati, né tutti i servizi di Azure eseguono automaticamente il fallback da un'area non riuscita alla propria coppia.  In questi casi, il recupero e la replica devono essere configurati dal cliente.

## <a name="can-i-select-my-regional-pairs"></a>È possibile selezionare le coppie di aree personali?

No. Alcuni servizi di Azure si basano sulle coppie di aree, ad esempio l' [archiviazione con ridondanza](./storage/common/storage-redundancy.md)di Azure. Questi servizi non consentono di creare nuove associazioni a livello di area.  Analogamente, poiché Azure controlla le priorità di manutenzione e ripristino pianificate per le coppie di aree, non è possibile definire coppie di aree personalizzate per sfruttare i vantaggi offerti da questi servizi. Tuttavia, è possibile creare una soluzione di ripristino di emergenza personalizzata compilando i servizi in un numero qualsiasi di aree e sfruttando i servizi di Azure per associarli. 

Ad esempio, è possibile usare i servizi di Azure come [AzCopy](./storage/common/storage-use-azcopy-v10.md) per pianificare i backup dei dati in un account di archiviazione in un'area diversa.  Con il servizio DNS di Azure [e gestione traffico di Azure](./networking/disaster-recovery-dns-traffic-manager.md), i clienti possono progettare un'architettura resiliente per le applicazioni che sopravvivranno alla perdita dell'area primaria.

## <a name="am-i-limited-to-using-services-within-my-regional-pairs"></a>Sono limitati a usare I servizi nelle coppie di aree?

No. Anche se un determinato servizio di Azure può basarsi su una coppia di aree, è possibile ospitare gli altri servizi in qualsiasi area che soddisfi le esigenze aziendali.  Una soluzione di archiviazione GRS di Azure può abbinare i dati in Canada Central a un peer in Canada orientale, usando risorse di calcolo situate negli Stati Uniti orientali.  

## <a name="must-i-use-azure-regional-pairs"></a>È necessario usare le coppie di aree di Azure?

No. I clienti possono sfruttare i servizi di Azure per progettare un servizio resiliente senza basarsi sulle coppie di aree di Azure.  Tuttavia, è consigliabile configurare il ripristino di emergenza per la continuità aziendale (BCDR) tra coppie di aree per trarre vantaggio dall' [isolamento](./security/fundamentals/isolation-choices.md) e migliorare la [disponibilità](./availability-zones/az-overview.md). Per le applicazioni che supportano più aree attive, è consigliabile usare entrambe le aree in una coppia di aree, ove possibile. In questo modo si garantisce la disponibilità ottimale per le applicazioni e il tempo di recupero ridotto in caso di emergenza. Quando possibile, progettare l'applicazione per la [resilienza massima](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview) e la facilità di [ripristino di emergenza](https://docs.microsoft.com/azure/architecture/framework/resiliency/backup-and-recovery).

## <a name="azure-regional-pairs"></a>Coppie di aree di Azure

| Area geografica | Coppia di aree A | Coppia locale B  |
|:--- |:--- |:--- |
| Asia Pacifico |Asia orientale (Hong Kong) | Asia sudorientale (Singapore) |
| Australia |Australia orientale |Australia sud-orientale |
| Australia |Australia centrale |Australia centrale 2 |
| Brasile |Brasile meridionale |Stati Uniti centro-meridionali |
| Canada |Canada centrale |Canada orientale |
| Cina |Cina settentrionale |Cina orientale|
| Cina |Cina settentrionale 2 |Cina orientale 2|
| Europa |Europa settentrionale (Irlanda) |Europa occidentale (Paesi Bassi) |
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
| Norvegia | Norvegia orientale | Norvegia occidentale |
| Sud Africa | Sudafrica settentrionale |Sudafrica occidentale |
| Svizzera | Svizzera settentrionale |Svizzera occidentale |
| Regno Unito |Regno Unito occidentale |Regno Unito meridionale |
| Emirati Arabi Uniti | Emirati Arabi Uniti settentrionali | Emirati Arabi Uniti centrali
| Dipartimento della difesa degli Stati Uniti |US DoD (area orientale) |US DoD (area centrale) |
| US Gov |US Gov Arizona |US Gov Texas |
| US Gov |US Gov Iowa |US Gov Virginia |
| US Gov |US Gov Virginia |US Gov Texas |

> [!Important]
> - L'India occidentale viene abbinata solo in una direzione. L'area secondaria dell'area India occidentale è India meridionale, mentre l'area secondaria dell'India meridionale è India centrale.
> - Il Brasile meridionale è univoco perché è associato a un'area esterna alla relativa geografia. L'area secondaria del Brasile meridionale è Stati Uniti centro-meridionali. L'area secondaria degli Stati Uniti centro-meridionali non è il Brasile meridionale.


## <a name="an-example-of-paired-regions"></a>Esempio di aree abbinate
Nell'immagine seguente viene illustrata un'applicazione ipotetica che usa la coppia di aree per il ripristino di emergenza. I numeri verdi evidenziano le attività tra aree di tre servizi di Azure (calcolo, archiviazione e database di Azure) e come vengono configurate per la replica tra le aree. I vantaggi esclusivi della distribuzione tra aree abbinate sono evidenziali dai numeri in arancione.

![Panoramica dei vantaggi delle aree abbinate](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Figura 2: ipotetica coppia di aree di Azure

## <a name="cross-region-activities"></a>Attività tra aree
Come indicato nella figura 2.

1. **Calcolo di Azure (IaaS)** : è necessario effettuare il provisioning di risorse di calcolo aggiuntive in anticipo per garantire che le risorse siano disponibili in un'altra area durante un'emergenza. Per altre informazioni, vedere [Informazioni tecniche sulla resilienza di Azure](https://github.com/uglide/azure-content/blob/master/articles/resiliency/resiliency-technical-guidance.md). 

2. **Archiviazione di Azure** : se si usa Managed disks, è possibile ottenere informazioni sui [backup tra più aree](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines) con backup di Azure e [replicare le macchine virtuali](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication) da un'area a un'altra con Azure Site Recovery. Se si usano gli account di archiviazione, l'archiviazione con ridondanza geografica (GRS) viene configurata per impostazione predefinita quando viene creato un account di archiviazione di Azure. Con questa opzione di replica, i dati vengono replicati per tre volte all'interno dell'area primaria e per tre volte nell'area abbinata. Per altre informazioni, vedere [Opzioni di ridondanza di Archiviazione di Azure](storage/common/storage-redundancy.md).

3. **Database SQL di Azure** : con la replica geografica del database SQL di Azure, è possibile configurare la replica asincrona delle transazioni in qualsiasi area del mondo; Tuttavia, è consigliabile distribuire queste risorse in un'area abbinata per la maggior parte degli scenari di ripristino di emergenza. Per altre informazioni, vedere l'articolo relativo alla [replica geografica nel database SQL di Azure](sql-database/sql-database-geo-replication-overview.md).

4. **Azure Resource Manager** gestione risorse intrinsecamente fornisce l'isolamento logico dei componenti tra le aree. In questo modo, è meno probabile che gli errori logici in un'area abbiano un impatto su un'altra.

## <a name="benefits-of-paired-regions"></a>Vantaggi delle aree abbinate

5. **Isolamento fisico** : quando possibile, Azure preferisce almeno 300 chilometri di separazione tra i Data Center di una coppia di aree, anche se ciò non è pratico o possibile in tutte le aree geografiche. La separazione dei data center fisici riduce la possibilità che calamità naturali, agitazioni sociali, interruzioni dell'alimentazione o interruzioni della rete fisica interessino entrambe le aree contemporaneamente. L'isolamento è soggetto ai vincoli presenti all'interno dell'area geografica (dimensioni dell'area geografica, disponibilità dell'infrastruttura di rete/alimentazione, normative e così via).  

6. **Replica fornita dalla piattaforma** : alcuni servizi, ad esempio l'archiviazione con ridondanza geografica, offrono la replica automatica nell'area abbinata.

7. **Ordine di ripristino dell'area** : nel caso di un'interruzione prolungata, il ripristino di un'area viene assegnato in ordine di priorità a ogni coppia. Per le applicazioni distribuite in aree abbinate viene garantito che una delle aree sarà ripristinata con priorità. Se un'applicazione viene distribuita in aree non abbinate, il ripristino potrebbe essere ritardato: nel peggiore dei casi le aree scelte potrebbero essere le ultime due a essere ripristinate.

8. **Aggiornamenti sequenziali** : gli aggiornamenti di sistema di Azure pianificati vengono implementati in aree abbinate in modo sequenziale (non contemporaneamente) per ridurre al minimo il tempo di inattività, l'effetto di bug e gli errori logici nel raro caso di un aggiornamento non valido.

9. **Residenza dei dati** : un'area si trova all'interno della stessa geografia della propria coppia (ad eccezione del Brasile meridionale) per soddisfare i requisiti di residenza dei dati per finalità fiscali e legali.
