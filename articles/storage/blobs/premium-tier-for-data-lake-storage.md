---
title: Livello Premium per Azure Data Lake Storage | Microsoft Docs
description: Usare il livello di prestazioni Premium con Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.custom: references_regions
ms.date: 10/28/2020
ms.author: normesta
ms.openlocfilehash: 9704ff7dec02a0de044863ec2da6280d65b30ef0
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043523"
---
# <a name="premium-tier-for-azure-data-lake-storage"></a>Livello Premium per Azure Data Lake Storage

Azure Data Lake Storage Gen2 ora supporta il [livello di prestazioni Premium](storage-blob-performance-tiers.md#premium-performance). Il livello di prestazioni Premium è ideale per le applicazioni e i carichi di lavoro Big Data Analytics che richiedono una latenza bassa e con un numero elevato di transazioni. 

> [!NOTE]
> Il livello Premium per Azure Data Lake Storage è in versione di anteprima pubblica. Vedere [disponibilità a livello](#regional-availability)di area.

## <a name="workloads-that-can-benefit-from-the-premium-performance-tier"></a>Carichi di lavoro che possono trarre vantaggio dal livello di prestazioni Premium

I carichi di lavoro di esempio includono carichi di lavoro interattivi, l'analisi di flusso, l'intelligenza artificiale e l'apprendimento automatico. 

**Carichi di lavoro interattivi** 

Questi carichi di lavoro richiedono aggiornamenti istantanei e commenti degli utenti, ad esempio applicazioni di e-commerce e di mapping, applicazioni video interattive e così via. In un'applicazione di e-commerce, ad esempio, gli elementi visualizzati con minore frequenza probabilmente non vengono memorizzati nella cache. Tuttavia, devono essere immediatamente visualizzati al cliente su richiesta. Un altro esempio è che i data scientist, gli analisti e gli sviluppatori possono derivare informazioni dettagliate sensibili al tempo ancora più rapidamente eseguendo query sui dati archiviati in un account che usa il livello di prestazioni Premium. 

**Analisi di flusso e Internet** 

In uno scenario molto più piccolo, le operazioni di scrittura più piccole potrebbero essere inviate al cloud ogni secondo. È possibile inserire grandi quantità di dati, aggregarli a scopo di analisi, e quindi eliminarli quasi immediatamente. Le funzionalità di inserimento elevato del livello di prestazioni Premium lo rendono efficace per questo tipo di carico di lavoro. 

**Intelligenza artificiale/Machine Learning (AI/ML)** 

AI/ML gestisce il consumo e l'elaborazione di tipi di dati diversi, ad esempio oggetti visivi, sintesi vocale e testo. Questo tipo di calcolo a prestazioni elevate occupa una grande quantità di dati che richiedono una risposta rapida e tempi di inserimento efficienti per l'analisi dei dati. 

## <a name="cost-effectiveness"></a>Convenienza

Il livello di prestazioni Premium ha un costo di archiviazione superiore, ma un costo di transazione inferiore rispetto al livello di prestazioni standard. Se le applicazioni e i carichi di lavoro eseguono un numero elevato di transazioni, il livello di prestazioni Premium può essere economicamente conveniente.

La tabella seguente illustra l'efficacia dei costi del livello Premium per Azure Data Lake Storage. Ogni colonna rappresenta il numero di transazioni in un mese.  Ogni riga rappresenta la percentuale di transazioni di lettura. In ogni cella della tabella è indicata la percentuale di riduzione dei costi associata a una percentuale di transazione di lettura e al numero di transazioni eseguite. 

Ad esempio, supponendo che l'account si trovi nell'area Stati Uniti orientali 2, il numero di transazioni con l'account supera 90M e il 70% di tali transazioni sono transazioni di lettura, il livello di prestazioni Premium è più conveniente.

> [!div class="mx-imgBorder"]
> ![l'immagine va qui](./media/premium-tier-for-data-lake-storage/premium-performance-data-lake-storage-cost-analysis-table.png)

> [!NOTE] 
> Se si preferisce valutare l'efficacia dei costi in base al numero di transazioni al secondo per ogni TB di dati, è possibile usare le intestazioni di colonna visualizzate nella parte inferiore della tabella.

Per ulteriori informazioni sui prezzi, vedere la pagina relativa ai [prezzi Azure Data Lake storage Gen2](https://azure.microsoft.com/pricing/details/storage/data-lake/) .

## <a name="feature-availability"></a>Disponibilità delle funzionalità 

Alcune funzionalità di archiviazione BLOB potrebbero non essere disponibili o possono avere solo un supporto parziale con il livello di prestazioni Premium. Per un elenco completo, vedere [funzionalità di archiviazione BLOB disponibili in Azure Data Lake storage Gen2](data-lake-storage-supported-blob-storage-features.md). Esaminare quindi un elenco di [problemi noti](data-lake-storage-known-issues.md) per valutare eventuali gap nella funzionalità.

## <a name="enabling-the-premium-performance-tier"></a>Abilitazione del livello di prestazioni Premium 

È possibile usare il livello Premium per Azure Data Lake Storage creando un account BlockBlobStorage con l'impostazione **dello spazio dei nomi gerarchica** **abilitata** . Per istruzioni complete, vedere [creare un account di account BlockBlobStorage](storage-blob-create-account-block-blob.md) .

Quando si crea l'account, assicurarsi di scegliere l'opzione prestazioni **Premium** e il tipo di account **BlockBlobStorage** .

> [!div class="mx-imgBorder"]
> ![Crea blockblobstorageacount](./media/premium-tier-for-data-lake-storage/create-block-blob-storage-account.png)

Abilitare l'impostazione **spazio dei nomi gerarchico** nella scheda **Avanzate** della pagina **Crea account di archiviazione** . È necessario abilitare questa impostazione quando si crea l'account. Non è possibile abilitarlo in seguito.

La figura seguente illustra questa impostazione nella pagina **Crea account di archiviazione** .

> [!div class="mx-imgBorder"]
> ![Impostazione dello spazio dei nomi gerarchico](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

## <a name="regional-availability"></a>Disponibilità a livello di area

Il livello Premium per Azure Data Lake Storage è disponibile nelle aree seguenti.

|Area|Ridondanza|
|--|--|
|Stati Uniti orientali|LRS, ZRS|
|Stati Uniti orientali 2|LRS, ZRS|
|Stati Uniti centrali|LRS|
|Stati Uniti occidentali|LRS|
|West US 2|LRS, ZRS|
|Stati Uniti centro-occidentali|LRS|
|Stati Uniti centro-meridionali|LRS|
|Canada centrale|LRS|
|Canada orientale|LRS|
|Europa settentrionale|LRS, ZRS|
|Europa occidentale|LRS, ZRS|
|Regno Unito meridionale|LRS|
|Regno Unito occidentale|LRS|
|Francia centrale|LRS|
|Asia orientale|LRS|
|Corea centrale|LRS|
|Corea meridionale|LRS|
|India centrale|LRS|
|India occidentale|LRS|
|Emirati Arabi Uniti settentrionali|LRS|
|Giappone orientale|LRS|
|Giappone occidentale|LRS|
|Asia sud-orientale|LRS, ZRS|
|Australia orientale|LRS, ZRS|
|Australia sud-orientale|LRS|
|Brasile meridionale|LRS|


## <a name="next-steps"></a>Passaggi successivi

Per Azure Data Lake Storage [annuncio del Blog](https://www.microsoft.com), vedere il livello Premium.