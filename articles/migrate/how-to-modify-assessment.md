---
title: Personalizzare le impostazioni di valutazione di Azure Migrate | Microsoft Docs
description: Descrive come configurare ed eseguire una valutazione per la migrazione di macchine virtuali VMware in Azure usando Azure Migration Planner
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 01/10/2019
ms.author: raynew
ms.openlocfilehash: 8419d7e7a91e4cbfd0eebfe00d35bf498cf5998c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62129824"
---
# <a name="customize-an-assessment"></a>Personalizzare una valutazione

[Azure Migrate](migrate-overview.md) crea valutazioni con proprietà predefinite. Dopo aver creato una valutazione, è possibile modificare le proprietà predefinite seguendo le istruzioni contenute in questo articolo.


## <a name="edit-assessment-properties"></a>Modificare le proprietà di valutazione

1. Nella pagina **Valutazioni** del progetto di migrazione selezionare la valutazione e fare clic su **Modifica proprietà**.
2. Personalizzare le proprietà di valutazione in base ai dettagli seguenti:

    **Impostazione** | **Dettagli** | **Default**
    --- | --- | ---
    **Posizione di destinazione** | Area di Azure in cui si vuole eseguire la migrazione.<br/><br/> Azure Migrate supporta attualmente 30 aree, tra cui Asia orientale, Asia sud-orientale, Australia orientale, Australia sud-orientale, Brasile meridionale, Canada centrale, Canada orientale, Cina orientale, Cina settentrionale, Corea del Sud centrale, Corea del Sud meridionale, Europa occidentale, Europa settentrionale, Germania centrale, Germania nordorientale, Giappone occidentale, Giappone orientale, India centrale, India meridionale, India occidentale, Regno Unito meridionale, Regno Unito occidentale, Governo degli Stati Uniti Arizona, Governo degli Stati Uniti Texas, Governo degli Stati Uniti Virginia, Stati Uniti centrali, Stati Uniti centro-meridionali, Stati Uniti centro-occidentali, Stati Uniti centro-settentrionali, Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti occidentali e Stati Uniti occidentali 2. |  L'area predefinita è Stati Uniti occidentali 2.
    **Tipo di archiviazione** | È possibile usare questa proprietà per specificare il tipo di dischi da spostare in Azure. Per il dimensionamento come in locale, è possibile specificare il tipo di dischi di destinazione come Managed Disks Premium o Managed Disks Standard. Per il dimensionamento basato sulle prestazioni, è possibile specificare il tipo di dischi di destinazione come Automatico, Managed Disks Premium o Managed Disks Standard. Quando si specifica il tipo di archiviazione automatico, la preferenza dei dischi è basata sui dati delle relative prestazioni (numero di operazioni di I/O al secondo e velocità effettiva). Se ad esempio si vuole ottenere un [contratto di servizio per singole istanze di macchina virtuale con tempo di attività pari al 99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/), può essere opportuno specificare il tipo di archiviazione Managed Disks Premium che garantisce che tutti i dischi nella valutazione vengano raccomandati come Managed Disks Premium. Si noti che Azure Migrate supporta solo dischi gestiti per la valutazione della migrazione. | Il valore predefinito è Managed Disks Premium, con il criterio di dimensionamento *as on-premises sizing* (Determinazione della dimensione come in locale).
    **Istanze riservate** |  È anche possibile specificare se sono presenti [istanze riservate](https://azure.microsoft.com/pricing/reserved-vm-instances/) in Azure. Azure Migrate stimerà il costo di conseguenza. Le istanze riservate sono attualmente supportate solo per l'offerta con pagamento in base al consumo in Azure Migrate. | Il valore predefinito per questa proprietà corrisponde a istanze riservate per 3 anni.
    **Criterio di dimensionamento** | Criterio che Azure Migrate deve usare per definire correttamente le dimensioni delle macchine virtuali per Azure. È possibile eseguire il dimensionamento *basato sulle prestazioni* o definire le dimensioni delle macchine virtuali *come in locale*, senza considerare la cronologia delle prestazioni. | L'opzione predefinita è il dimensionamento basato sulle prestazioni.
    **Cronologia delle prestazioni** | Durata da considerare per la valutazione delle prestazioni delle macchine virtuali. Questa proprietà è applicabile solo quando il criterio di dimensionamento è *basato sulle prestazioni*. | Il valore predefinito è un giorno.
    **Utilizzo percentile** | Valore percentile dell'esempio di prestazioni da tenere in considerazione per il dimensionamento corretto. Questa proprietà è applicabile solo quando il criterio di dimensionamento è *basato sulle prestazioni*.  | Il valore predefinito è 95° percentile.
    **Serie macchina virtuale** | È possibile specificare la serie di macchine virtuali che si intende tenere in considerazione per il dimensionamento corretto. Se ad esempio si ha un ambiente di produzione di cui non si intende eseguire la migrazione a macchine virtuali serie A in Azure, è possibile escludere la serie A dall'elenco o dalla serie e il dimensionamento viene eseguito solo nella serie selezionata. | Per impostazione predefinita, sono selezionate tutte le serie di macchine virtuali.
    **Fattore di comfort** | Durante la valutazione, Azure Migrate considera un buffer (fattore di comfort), che viene applicato ai dati sull'utilizzo delle VM (CPU, memoria, disco e rete). Il fattore di comfort tiene conto di aspetti come utilizzo stagionale, breve cronologia delle prestazioni e probabile aumento dell'utilizzo futuro.<br/><br/> Da una VM con 10 core e un utilizzo del 20%, ad esempio, si ottiene normalmente una VM con 2 core. Con un fattore di comfort pari a 2.0x, invece, il risultato è una VM con 4 core. | L'impostazione predefinita è 1.3x.
    **Offerta** | [Offerta Azure](https://azure.microsoft.com/support/legal/offer-details/) sottoscritta. | L'opzione predefinita è [Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/).
    **Valuta** | Valuta di fatturazione. | La valuta predefinita è il dollaro statunitense.
    **Sconto (%)** | Qualsiasi sconto specifico della sottoscrizione ricevuto oltre all'offerta Azure. | L'impostazione predefinita è 0%.
    **Tempo di attività macchina virtuale** | Se non si prevede di eseguire ininterrottamente le macchine virtuali in Azure, è possibile specificare la durata (numero di giorni al mese e numero di ore al giorno) dell'esecuzione. Il costo verrà stimato di conseguenza. | Il valore predefinito è di 31 giorni al mese e di 24 ore al giorno.
    **Vantaggio Azure Hybrid** | Specificare se si dispone di licenze Software Assurance e se si è idonei per l'opzione [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Se il valore è impostato su Sì, alle macchine virtuali Windows si applicano i prezzi di Azure per sistemi non Windows. | Il valore predefinito è Yes.

3. Fare clic su **Salva** per aggiornare la valutazione.

## <a name="faqs-on-assessment-properties"></a>Domande frequenti sulle proprietà di valutazione

### <a name="what-is-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>Qual è la differenza tra il dimensionamento locale e il dimensionamento basato sulle prestazioni?

Quando si specifica che il criterio di dimensionamento deve essere locale, Azure Migrate non considera i dati delle prestazioni delle macchine virtuali e le ridimensiona in base alla configurazione locale. Se il criterio di dimensionamento è basato sulle prestazioni, il dimensionamento viene eseguito in base ai dati di utilizzo, ad esempio se è presente una macchina virtuale locale con 4 core e 8 GB di memoria, con un utilizzo della CPU e della memoria del 50%. Se il criterio di dimensionamento è il dimensionamento locale, è consigliato uno SKU di macchina virtuale di Azure con 4 core e 8 GB di memoria, ma, se il criterio di dimensionamento è basato sulle prestazioni, è consigliato uno SKU di macchina virtuale con 2 core e 4 GB perché, pur consigliando le dimensioni, va considerata la percentuale di utilizzo.

Analogamente, per i dischi, il dimensionamento dei dischi dipende da due proprietà di valutazione: i criteri di dimensionamento e il tipo di archiviazione. Se il criterio di dimensionamento è basato sulle prestazioni e il tipo di archiviazione è automatico, vengono considerati i valori relativi alle operazioni di I/O al secondo e alla velocità effettiva del disco per identificare il tipo di disco di destinazione (Standard o Premium). Se il criterio di dimensionamento è basato sulle prestazioni e il tipo di archiviazione è Premium, è consigliabile un disco Premium. La SKU del disco Premium in Azure viene selezionato in base alle dimensioni del disco locale. La stessa logica si applica al dimensionamento del disco quando il criterio di dimensionamento è quello locale e il tipo di archiviazione è Standard o Premium.

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Qual è l'impatto della cronologia delle prestazioni e dell'utilizzo percentile sulle dimensioni consigliate?

Queste proprietà sono applicabili solo per il dimensionamento basato sulle prestazioni. Azure Migrate raccoglie la cronologia delle prestazioni dei computer locali e la usa per consigliare le dimensioni della macchina virtuale e il tipo di disco in Azure.

- L'appliance dell'agente di raccolta esegue continuamente una profilatura dell'ambiente locale per raccogliere i dati di utilizzo in tempo reale ogni 20 secondi.
- L'appliance esegue il rollup dei campioni raccolti ogni 20 secondi e crea un singolo punto dati ogni 15 minuti. Per creare il singolo punto dati, l'appliance seleziona il valore di picco da tutti i campioni raccolti ogni 20 secondi e lo invia ad Azure.
- Quando si crea una valutazione in Azure, in base alla durata delle prestazioni e al valore percentile della cronologia delle prestazioni, Azure Migrate calcola il valore di utilizzo effettivo e lo usa per il dimensionamento.

Se ad esempio la durata delle prestazioni è stata impostata su 1 giorno e il valore percentile è stato impostato su 95, Azure Migrate usa i punti campione da 15 minuti inviati dall'agente di raccolta per l'ultimo giorno, li ordina in senso crescente e sceglie il valore del 95° percentile come utilizzo effettivo. Il valore del 95° percentile assicura che vengano ignorati eventuali outlier, come potrebbe invece verificarsi se si selezionasse il 99° percentile. Per scegliere il picco nell'utilizzo per il periodo di tempo senza perdere gli outlier, è consigliabile selezionare il 99° percentile.

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni](concepts-assessment-calculation.md) sul modo in cui vengono calcolate le valutazioni.
