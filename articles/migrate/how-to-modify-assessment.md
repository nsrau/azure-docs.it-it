---
title: Personalizzare le valutazioni per la valutazione di Azure Migrate Server | Microsoft Docs
description: Viene descritto come personalizzare le valutazioni create con la valutazione di Azure eseguire la migrazione di Server
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: 8b200ce3d6e73a575b1b89d82a9323d58f435a48
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807916"
---
# <a name="customize-an-assessment"></a>Personalizzare una valutazione

Questo articolo descrive come personalizzare le valutazioni create dalla valutazione di Azure Migrate Server.

[Azure Migrate](migrate-services-overview.md) fornisce un hub centrale per tenere traccia di individuazione, valutazione e migrazione delle App in locale e i carichi di lavoro e cloud privati/pubblici macchine virtuali di Azure. L'hub fornisce gli strumenti di Azure Migrate per la valutazione e migrazione, nonché le offerte di fornitori di software indipendenti di terze parti.

È possibile utilizzare lo strumento di valutazione di Server di eseguire la migrazione di Azure per creare le valutazioni per le macchine virtuali VMware locali e macchine virtuali Hyper-V, in preparazione per la migrazione ad Azure. 

## <a name="about-assessments"></a>Sulle valutazioni

Esistono due tipi di valutazioni che è possibile eseguire usando Azure eseguire la migrazione di Server Assessment.

**Valutazione** | **Dettagli** | **Dati**
--- | --- | ---
**Basato sulle prestazioni** | Valutazioni in base ai dati sulle prestazioni raccolti | **Dimensioni VM consigliate**: Basate sui dati di utilizzo della CPU e memoria.<br/><br/> **Tipo di disco (disco gestito di standard o premium) consigliato**: Base di IOPS e velocità effettiva dei dischi in locale.
**Come in locale** | Valutazioni in base a un'istanza locale di ridimensionamento. | **Dimensioni VM consigliate**: In base alla dimensione di macchina virtuale in locale<br/><br> **Tipo di disco consigliato**: Base all'impostazione di tipo archiviazione che scelto per la valutazione.


## <a name="how-is-an-assessment-done"></a>Come viene eseguita una valutazione?

Una valutazione di Azure Migrate si articola in tre fasi. La valutazione inizia con un'analisi di idoneità, seguita dalla determinazione della dimensione e termina con una stima dei costi mensili. Un computer passa alla fase successiva soltanto se supera quella precedente. Ad esempio, se un computer non supera il controllo di idoneità di Azure, viene contrassegnato come non idoneo per Azure e la dimensione e i costi non vengono calcolati.

## <a name="whats-in-an-assessment"></a>Elementi inclusi nella valutazione

**Proprietà** | **Dettagli**
--- | ---
**Posizione di destinazione** | Area di Azure in cui si vuole eseguire la migrazione.<br/> Azure Migrate supporta attualmente queste aree di destinazione: Australia orientale, Australia sud-orientale, Brasile meridionale, Canada centrale, Canada orientale, India centrale, Stati Uniti centrali, Cina orientale, Cina settentrionale, Asia orientale, Stati Uniti orientali, Stati Uniti orientali 2, Germania centrale, Germania nord-orientale, Giappone orientale, Giappone occidentale, Corea centrale, Corea meridionale, Nord Stati Uniti centrali, Europa settentrionale, Stati Uniti centro-meridionali, Asia sud-orientale, India meridionale, Regno Unito meridionale, Regno Unito occidentale, Stati Uniti Arizona Gov, US Gov Texas, US Gov Virginia, Stati Uniti centro occidentali, Europa occidentale, India occidentale, Stati Uniti occidentali e Stati Uniti occidentali 2.<br/> Per impostazione predefinita, l'area di destinazione è impostata su Stati Uniti occidentali 2.
**Tipo di archiviazione** | Premium/standard HHD/Standard a dischi SSD dischi.<br/> Quando si specifica il tipo di archiviazione come automatico in una valutazione, i dati sulle prestazioni dei dischi (numero di IOPS e velocità effettiva) si basa la raccomandazione del disco.<br/> Se si specifica il tipo di archiviazione come Premium o Standard, consiglia la valutazione un disco SKU all'interno del tipo di archiviazione selezionato.<br/> Se si desidera ottenere una singola istanza VM SLA pari al 99,9%, è possibile impostare il tipo di archiviazione come dischi gestiti Premium. Quindi tutti i dischi nella valutazione verranno generate indicazioni come dischi gestiti Premium. <br/> Azure Migrate supporta solo dischi gestiti per la valutazione della migrazione.<br/> 
**Istanze riservate (RI)** | Specificare questa proprietà se si hanno istanze riservate di in Azure. Le stime di costo nella valutazione prenderà in considerazione gli sconti delle istanze riservate. Le istanze riservate sono attualmente supportate solo per offerte di pagamento a consumo in Azure Migrate.
**Criterio di dimensionamento** | Consente di dimensionare le macchine virtuali. Ridimensionamento può essere basata sulle prestazioni, oppure **come in locale**, senza considerare la cronologia delle prestazioni.
**Cronologia delle prestazioni** | La durata da considerare per la valutazione delle prestazioni della macchina virtuale. Questa proprietà è applicabile solo quando il ridimensionamento è basato sulle prestazioni.
**Utilizzo percentile** | Il valore percentile dell'esempio di prestazioni che viene usato per corretto dimensionamento delle macchine virtuali. Questa proprietà è applicabile solo quando il ridimensionamento è basato sulle prestazioni.
**Serie macchina virtuale** | Serie di VM usata per le stime delle dimensioni. Se si ha un ambiente di produzione di cui non si intende eseguire la migrazione a VM serie A in Azure, ad esempio, si può escludere la serie A dall'elenco o dalle serie. Il dimensionamento sarà basato solo sulla serie selezionata.
**Fattore di comfort** | Valutazione di Server eseguire la migrazione di Azure considera un buffer (fattore di comfort) durante la valutazione. che viene applicato ai dati sull'utilizzo delle VM (CPU, memoria, disco e rete). Il fattore di comfort tiene conto di aspetti come utilizzo stagionale, breve cronologia delle prestazioni e probabile aumento dell'utilizzo futuro.<br/><br/> Da una VM con 10 core e un utilizzo del 20%, ad esempio, si ottiene normalmente una VM con 2 core. Con un fattore di comfort pari a 2.0x, invece, il risultato è una VM con 4 core.
**Offerta** | [Offerta di Azure](https://azure.microsoft.com/support/legal/offer-details/) sottoscritta. Azure Migrate stima il costo di conseguenza.
**Valuta** | Valuta di fatturazione. 
**Sconto (%)** | Qualsiasi sconto specifico della sottoscrizione ricevuto oltre all'offerta Azure.<br/> L'impostazione predefinita è 0%.
**Tempo di attività macchina virtuale** | Se non si prevede di eseguire ininterrottamente le macchine virtuali in Azure, è possibile specificare la durata (numero di giorni al mese e numero di ore al giorno) dell'esecuzione. Il costo verrà stimato di conseguenza.<br/> Il valore predefinito è di 31 giorni al mese e di 24 ore al giorno.
**Vantaggio Azure Hybrid** | Specifica se dispone di software assurance e sono idonei [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Se il valore è impostato su Sì, alle macchine virtuali Windows si applicano i prezzi di Azure per sistemi non Windows. | Il valore predefinito è Yes.


## <a name="edit-assessment-properties"></a>Modificare le proprietà di valutazione

Per modificare le proprietà di valutazione dopo la creazione di una valutazione, eseguire le operazioni seguenti:

1. Nel progetto Azure Migrate, fare clic su **server**.
2. In **Azure Migrate: Server Assessment**, scegliere il numero di valutazioni.
3. Nelle **Assessment**, fare clic su valutazione pertinente > **modificare le proprietà**.
5. Personalizzare le proprietà di valutazione in base alla tabella precedente.
6. Fare clic su **Salva** per aggiornare la valutazione.


È anche possibile modificare le proprietà di valutazione durante la creazione di una valutazione.


## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni](concepts-assessment-calculation.md) sul modo in cui vengono calcolate le valutazioni.
