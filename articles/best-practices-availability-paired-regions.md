<properties
	pageTitle="Continuità aziendale e ripristino di emergenza nelle aree geografiche abbinate di Azure | Microsoft Azure"
	description="Le coppie di aree di Azure assicurano la resilienza delle applicazioni in caso di errori del data center."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/20/2016"
    ms.author="raynew"/>

# Continuità aziendale e ripristino di emergenza nelle aree geografiche abbinate di Azure

## Definizione di aree abbinate

Azure è disponibile in più aree geografiche del mondo. Un'area geografica di Azure è un'area definita del mondo che include almeno un'area di Azure. Un'area di Azure all'interno di un'area geografica include uno o più data center.

Ogni area di Azure è abbinata a un'altra area all'interno della stessa area geografica (con l'eccezione del Brasile meridionale), che insieme creano una coppia di aree.


![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Figura 1: Diagramma di una coppia di aree di Azure



| Area geografica | Aree abbinate | |
| :-------------| :-------------   | :-------------   |
| America del Nord | Stati Uniti centro-settentrionali | Stati Uniti centro-meridionali |
| America del Nord | Stati Uniti orientali | Stati Uniti occidentali |
| America del Nord | Stati Uniti orientali 2 | Stati Uniti centrali |
| Europa | Europa settentrionale | Europa occidentale |
| Asia | Asia sudorientale | Asia orientale |
| Cina | Cina orientale | Cina settentrionale |
| Giappone | Giappone orientale | Giappone occidentale |
| Brasile | Brasile meridionale (1) | Stati Uniti centro-meridionali |
| Australia | Australia orientale | Australia sudorientale|
| Governo degli Stati Uniti | Governo degli Stati Uniti - Iowa | Governo degli Stati Uniti - Virginia |
| India | India centrale | India meridionale |
| Canada | Canada centrale | Canada orientale |

Tabella 1 - Mapping di coppie di aree di Azure

> (1) L'area Brasile meridionale è unica, perché è abbinata a un'area esterna alla propria area geografica. L'area secondaria del Brasile meridionale è Stati Uniti centro-meridionali, ma l'area secondaria di Stati Uniti centro-meridionali non è il Brasile meridionale.

È consigliabile replicare i carichi di lavoro tra le coppie di aree per sfruttare i vantaggi dei criteri di isolamento e disponibilità di Azure. Ad esempio, gli aggiornamenti di sistema di Azure pianificati vengono distribuiti in sequenza (non contemporaneamente) tra le aree abbinate. Ciò significa che anche nel raro caso di un aggiornamento non corretto, non saranno interessate contemporaneamente entrambe le aree. Inoltre, nell'improbabile caso di un'interruzione su vasta scala, viene data priorità al ripristino di almeno un'area di ogni coppia.

## Esempio di aree abbinate
La Figura 2 mostra un'ipotetica applicazione che utilizza la coppia internazionali per il ripristino di emergenza. I numeri in verde evidenziano le attività tra aree di tre servizi di Azure (calcolo, archiviazione e database di Azure) e come vengono configurate per la replica tra aree. I vantaggi esclusivi della distribuzione tra aree abbinate sono evidenziali dai numeri in arancione.


![Panoramica dei vantaggi delle aree abbinate](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Figura 2: ipotetica coppia di aree di Azure

## Attività tra aree
Come indicato nella figura 2.

![1Green](./media/best-practices-availability-paired-regions/1Green.png) **Calcolo di Azure (PaaS)**: è necessario eseguire anticipatamente il provisioning di risorse di calcolo aggiuntive per assicurare che siano disponibili in un'altra area nel caso di un'emergenza. Per altre informazioni, vedere [Indicazioni tecniche sulla resilienza di Azure](./resiliency/resiliency-technical-guidance.md).

![2Green](./media/best-practices-availability-paired-regions/2Green.png) **Archiviazione di Azure**: per impostazione predefinita ( GRS), quando si crea un account di archiviazione di Azure viene configurata l'archiviazione con ridondanza geografica. Con questa opzione di replica, i dati vengono replicati per tre volte all'interno dell'area primaria e per tre volte nell'area abbinata. Per altre informazioni, vedere [Opzioni di ridondanza di Archiviazione di Azure](storage/storage-redundancy.md).


![3Green](./media/best-practices-availability-paired-regions/3Green.png)**Database SQL Azure**: con Azure SQL replica geografica Standard, è possibile configurare la replica asincrona delle transazioni in un'area associata. Con la replica geografica di Premium è possibile configurare la replica per tutte le aree del mondo, tuttavia è consigliabile distribuire queste risorse in un'area abbinata per il ripristino di emergenza. Per altre informazioni, vedere [Panoramica: Replica geografica attiva per il database SQL di Azure](./sql-database/sql-database-geo-replication-overview.md).

![4Green](./media/best-practices-availability-paired-regions/4Green.png)**Gestione risorse di azure (ARM)**-ARM intrinsecamente fornisce isolamento logico dei componenti di gestione del servizio in aree geografiche. In questo modo, è meno probabile che gli errori logici in un'area abbiano un impatto su un'altra.

## Vantaggi delle aree abbinate
Come indicato nella figura 2.

![5Orange](./media/best-practices-availability-paired-regions/5Orange.png) **Isolamento fisico**: quando possibile, per Azure è preferibile una separazione di almeno 480 km tra i data center di una coppia di aree, anche se ciò non è sempre pratico o possibile in tutte le aree geografiche. La separazione dei data center fisici riduce la possibilità che calamità naturali, agitazioni sociali, interruzioni dell'alimentazione o interruzioni della rete fisica interessino entrambe le aree contemporaneamente. L'isolamento è soggetto ai vincoli presenti all'interno dell'area geografica (dimensioni dell'area geografica, disponibilità dell'infrastruttura di rete/alimentazione, normative e così via).

![6Orange](./media/best-practices-availability-paired-regions/6Orange.png)**Replica fornita dalla piattaforma**-replica automatica per l'area associata di fornire alcuni servizi, ad esempio l'archiviazione con ridondanza geografica.

![7Orange](./media/best-practices-availability-paired-regions/7Orange.png) **Ordine di ripristino dell'area**: nel caso di un'interruzione su vasta scala, viene definita la priorità di ripristino di un'area per ogni coppia. Per le applicazioni distribuite in aree abbinate viene garantito che una delle aree sarà ripristinata con priorità. Se un'applicazione viene distribuita in aree non abbinate, il ripristino potrebbe essere ritardato: nel peggiore dei casi le aree scelte potrebbero essere le ultime due a essere ripristinate.

![8Orange](./media/best-practices-availability-paired-regions/8Orange.png) **Aggiornamenti sequenziali**: gli aggiornamenti di sistema di Azure pianificati vengono implementati in aree abbinate in modo sequenziale (non contemporaneamente) per ridurre al minimo i tempi di inattività, l'effetto di bug e gli errori logici nel raro caso di un aggiornamento non valido.


![9Orange](./media/best-practices-availability-paired-regions/9Orange.png) **Residenza dei dati**: un'area si trova all'interno della stessa geografia della propria coppia (a eccezione del Brasile meridionale) per soddisfare i requisiti di residenza dei dati ai fini della giurisdizione per le imposizioni fiscali e normative.

<!---HONumber=AcomDC_0629_2016-->