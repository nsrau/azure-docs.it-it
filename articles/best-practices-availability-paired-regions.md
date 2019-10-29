---
title: Continuità aziendale & ripristino di emergenza-aree abbinate di Azure
description: Informazioni sulle coppie di aree di Azure per assicurare la resilienza delle applicazioni in caso di errori del data center.
author: rayne-wiselman
manager: carmon
ms.service: multiple
ms.topic: article
ms.date: 07/01/2019
ms.author: raynew
ms.openlocfilehash: 90111325677e1bdd12a03081ad7513a34f68fd40
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044131"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Continuità aziendale e ripristino di emergenza nelle aree geografiche abbinate di Azure

## <a name="what-are-paired-regions"></a>Definizione di aree abbinate

Azure è disponibile in più aree geografiche del mondo. Un'area geografica di Azure è un'area definita del mondo che include almeno un'area di Azure. Un'area di Azure all'interno di un'area geografica include uno o più data center.

Ogni area di Azure è associata a un'altra area con la stessa ubicazione geografica, che insieme formano una coppia di aree. L'eccezione è il Brasile meridionale che è associato a un'area fuori dalla relativa ubicazione geografica. In tutta la coppie di aree, Azure serializza gli aggiornamenti della piattaforma (manutenzione programmata) in modo che venga aggiornata una sola area abbinata alla volta. Se si verifica un'interruzione che interessa più aree, almeno un'area per ogni coppia avrà priorità per il recupero.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Figura 1: coppie di aree di Azure

| Area geografica | Aree abbinate |  |
|:--- |:--- |:--- |
| Asia |Asia orientale |Asia sud-orientale |
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
| Corea |Corea centrale |Corea meridionale |
| America del Nord |Stati Uniti Orientali |Stati Uniti occidentali |
| America del Nord |Stati Uniti orientali 2 |Stati Uniti centrali |
| America del Nord |Stati Uniti centro-settentrionali |Stati Uniti centro-meridionali |
| America del Nord |Stati Uniti occidentali 2 |Stati Uniti centro-occidentali 
| Sudafrica | Sudafrica settentrionale | Sudafrica occidentale
| Regno Unito |Regno Unito occidentale |Regno Unito meridionale |
| Emirati Arabi Uniti | Emirati Arabi Uniti settentrionali | Emirati Arabi Uniti centrali
| Dipartimento della difesa degli Stati Uniti |US DoD (area orientale) |US DoD (area centrale) |
| Azure per enti pubblici |US Gov Arizona |US Gov Texas |
| Azure per enti pubblici |Governo degli Stati Uniti - Iowa |US Gov Virginia |
| Azure per enti pubblici |US Gov Virginia |US Gov Texas |

Tabella 1 - Mapping di coppie di aree di Azure

- L'India occidentale viene abbinata solo in una direzione. L'area secondaria dell'area India occidentale è India meridionale, mentre l'area secondaria dell'India meridionale è India centrale.
- L'area Brasile meridionale è unica perché è abbinata a un'area esterna alla specifica area geografica. L'area secondaria del Brasile meridionale è Stati Uniti centro-meridionali. L'area secondaria degli Stati Uniti centro-meridionali non è il Brasile meridionale.
- L'area secondaria del US Gov Iowa è US Gov Virginia.
- L'area secondaria del US Gov Virginia è US Gov Texas.
- US Gov Texas ' area secondaria è US Gov Arizona.


È consigliabile configurare la continuità aziendale e il ripristino di emergenza (BCDR) tra le coppie di aree per sfruttare i vantaggi dei criteri di isolamento e disponibilità di Azure. Per le applicazioni che supportano più aree attive, è consigliabile usare entrambe le aree in una coppia di aree, laddove possibile. Per le applicazioni che supportano più regioni attive, si consiglia di utilizzare entrambe le regioni in una coppia, ove possibile. 

## <a name="an-example-of-paired-regions"></a>Esempio di aree abbinate
La Figura 2 mostra un'ipotetica applicazione che utilizza la coppia internazionali per il ripristino di emergenza. I numeri in verde evidenziano le attività tra aree di tre servizi di Azure (calcolo, archiviazione e database di Azure) e come vengono configurate per la replica tra aree. I vantaggi esclusivi della distribuzione tra aree abbinate sono evidenziali dai numeri in arancione.

![Panoramica dei vantaggi delle aree abbinate](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Figura 2: ipotetica coppia di aree di Azure

## <a name="cross-region-activities"></a>Attività tra aree
Come indicato nella figura 2.

![IaaS](./media/best-practices-availability-paired-regions/1Green.png) **Calcolo di Azure (IaaS)** : è necessario eseguire anticipatamente il provisioning di risorse di calcolo aggiuntive per assicurare che siano disponibili in un'altra area in caso di emergenza. Per altre informazioni, vedere [Informazioni tecniche sulla resilienza di Azure](resiliency/resiliency-technical-guidance.md).

Archiviazione ![](./media/best-practices-availability-paired-regions/2Green.png) **archiviazione di Azure** : se si usano dischi gestiti, è possibile ottenere informazioni sui [backup tra più aree](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines) con backup di Azure e [replicare le macchine virtuali](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication) da un'area a un'altra con Azure Site Recovery. Se si usano gli account di archiviazione, l'archiviazione con ridondanza geografica (GRS) viene configurata per impostazione predefinita quando viene creato un account di archiviazione di Azure. Con questa opzione di replica, i dati vengono replicati per tre volte all'interno dell'area primaria e per tre volte nell'area abbinata. Per altre informazioni, vedere [Opzioni di ridondanza di Archiviazione di Azure](storage/common/storage-redundancy.md).

![SQL di Azure](./media/best-practices-availability-paired-regions/3Green.png) **Database SQL di Azure**: con la replica geografica del database SQL di Azure è possibile configurare la replica asincrona delle transazioni per tutte le aree del mondo. È tuttavia consigliabile distribuire queste risorse in un'area abbinata per la maggior parte degli scenari di ripristino di emergenza. Per altre informazioni, vedere l'articolo relativo alla [replica geografica nel database SQL di Azure](sql-database/sql-database-geo-replication-overview.md).

![Resource Manager](./media/best-practices-availability-paired-regions/4Green.png)**Azure Resource Manager**: Resource Manager fornisce implicitamente l'isolamento logico dei componenti tra le aree. In questo modo, è meno probabile che gli errori logici in un'area abbiano un impatto su un'altra.

## <a name="benefits-of-paired-regions"></a>Vantaggi delle aree abbinate
Come indicato nella figura 2.  

![Isolamento](./media/best-practices-availability-paired-regions/5Orange.png)
**Isolamento fisico**: quando possibile, per Azure è preferibile una separazione di almeno 480 km tra i data center di una coppia di aree, anche se ciò non è sempre pratico o possibile in tutte le aree geografiche. La separazione dei data center fisici riduce la possibilità che calamità naturali, agitazioni sociali, interruzioni dell'alimentazione o interruzioni della rete fisica interessino entrambe le aree contemporaneamente. L'isolamento è soggetto ai vincoli presenti all'interno dell'area geografica (dimensioni dell'area geografica, disponibilità dell'infrastruttura di rete/alimentazione, normative e così via).  

![Replica](./media/best-practices-availability-paired-regions/6Orange.png)
**Replica fornita dalla piattaforma**: alcuni servizi, ad esempio l'archiviazione con ridondanza geografica, offrono la replica automatica nell'area abbinata.

![Ripristino](./media/best-practices-availability-paired-regions/7Orange.png)
**Ordine di ripristino dell'area**: nel caso di un'interruzione su vasta scala, viene definita la priorità di ripristino di un'area per ogni coppia. Per le applicazioni distribuite in aree abbinate viene garantito che una delle aree sarà ripristinata con priorità. Se un'applicazione viene distribuita in aree non abbinate, il ripristino potrebbe essere ritardato: nel peggiore dei casi le aree scelte potrebbero essere le ultime due a essere ripristinate.

![Aggiornamenti](./media/best-practices-availability-paired-regions/8Orange.png)
**Aggiornamenti sequenziali**: gli aggiornamenti di sistema di Azure pianificati vengono implementati nelle aree abbinate in modo sequenziale (non contemporaneamente) per ridurre al minimo i tempi di inattività, l'effetto dei bug e gli errori logici nel raro caso di un aggiornamento non valido.

![Dati](./media/best-practices-availability-paired-regions/9Orange.png)
 **Residenza dei dati**: un'area si trova all'interno della stessa geografia della propria coppia (a eccezione del Brasile meridionale) per soddisfare i requisiti di residenza dei dati ai fini della giurisdizione per le imposizioni fiscali e normative.
