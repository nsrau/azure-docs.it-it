---
title: Personalizzare le valutazioni per la valutazione del server di Azure Migrate Documenti Microsoft
description: Descrive come personalizzare le valutazioni create con Azure Migrate Server Assessment
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: 2cfac978b0a5af20e9e2fa1e32a7361488f20fbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68234279"
---
# <a name="customize-an-assessment"></a>Personalizzare una valutazione

Questo articolo descrive come personalizzare le valutazioni create da Azure Migrate Server Assessment.This article describes how to customize assessments created by Azure Migrate Server Assessment.

[Azure Migrate](migrate-services-overview.md) offre un hub centrale per tenere traccia dell'individuazione, della valutazione e della migrazione ad Azure di carichi di lavoro e app locali, oltre che di VM del cloud privato/pubblico. L'hub fornisce gli strumenti di Azure Migrate per la valutazione e la migrazione, nonché offerte di ISV terzi.

È possibile usare lo strumento di valutazione del server di Azure Migrate per creare valutazioni per le macchine virtuali VMware locali e le macchine virtuali Hyper-V, in preparazione per la migrazione ad Azure.You can use the Azure Migrate Server Assessment tool to create assessments for on-premises VMware VMs and Hyper-VMs, in preparation for migration to Azure.

## <a name="about-assessments"></a>Informazioni sulle valutazioni

È possibile eseguire due tipi di valutazioni con Valutazione server di Azure Migrate.

**Valutazione** | **Dettagli** | **Dati**
--- | --- | ---
**Basata sulle prestazioni** | Valutazioni basate sui dati sulle prestazioni raccolti | **Dimensioni consigliate per le macchine virtuali:** in base ai dati di utilizzo della CPU e della memoria.<br/><br/> **Tipo di disco consigliato (disco gestito standard o Premium):** in base alle operazioni di I/O al secondo e alla velocità effettiva dei dischi locali.
**Come in locale** | Valutazioni basate sul dimensionamento locale. | **Dimensioni vm consigliate:** in base alle dimensioni della macchina virtuale localeRecommended VM size : Based on the on-premises VM size<br/><br> **Tipo di disco consigliato**: In base all'impostazione del tipo di archiviazione selezionata per la valutazione.


## <a name="how-is-an-assessment-done"></a>Come viene effettuata una valutazione?

Una valutazione eseguita in Azure Migrate Server Assessment prevede tre fasi. La valutazione inizia con un'analisi di idoneità, seguita dalla determinazione della dimensione e termina con una stima dei costi mensili. Un computer passa alla fase successiva soltanto se supera quella precedente. Ad esempio, se un computer non supera il controllo di idoneità di Azure, viene contrassegnato come non idoneo per Azure e la dimensione e i costi non vengono calcolati. [Scopri di più.](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation)

## <a name="whats-in-an-assessment"></a>Elementi inclusi nella valutazione

**Proprietà** | **Dettagli**
--- | ---
**Posizione di destinazione** | Area di Azure in cui si vuole eseguire la migrazione.<br/> Server Assessment supporta attualmente queste regioni di destinazione: Australia Orientale, Australia Sud-Est, Brasile Sud, Canada Centrale, Canada Orientale, India centrale, Stati Uniti centrali, Cina orientale, Cina nord, Asia orientale, Stati Uniti orientali, Stati Uniti orientali2, Germania centrale, Germania nord-orientale, Giappone orientale, Giappone-Est, Corea del Nord, Corea del Sud, Stati Uniti centro-settentrionali, Stati Uniti centro-meridionali, Asia sud-orientale, India meridionale, Regno Unito meridionale, Regno Unito occidentale, US Gov Arizona, US Gov Texas, US Gov Virginia, Stati Uniti centro-occidentali, Europa occidentale, India occidentale, Stati Uniti occidentali e Stati Uniti occidentali2.
**Tipo di archiviazione** | You can use this property to specify the type of disks you want to move to, in Azure.<br/><br/> Per il dimensionamento as-on-premise, è possibile specificare il tipo di archiviazione di destinazione come dischi gestiti da Premium, dischi gestiti SSD standard o dischi gestiti da HDD standard. Per il dimensionamento basato sulle prestazioni, è possibile specificare il tipo di disco di destinazione come dischi automatici, gestiti da Premium, dischi gestiti da HDD standard o dischi gestiti sSD standard.<br/><br/> Quando si specifica il tipo di archiviazione automatico, la preferenza dei dischi è basata sui dati delle relative prestazioni (numero di operazioni di I/O al secondo e velocità effettiva). Se si specifica il tipo di archiviazione come premium/standard, la valutazione consiglierà uno SKU del disco all'interno del tipo di archiviazione selezionato. Se si vuole ottenere un'istanza singola del servizio SLA VM del 99,9%, è possibile specificare il tipo di archiviazione come dischi gestiti da Premium.If you want to achieve a single instance VM SLA of 99.9%, you may want to specify the storage type as Premium-managed disks. che garantisce che tutti i dischi nella valutazione vengano raccomandati come Managed Disks Premium. Azure
**Istanze riservate (RI)** | Questa proprietà consente di specificare se si dispone di [istanze riservate](https://azure.microsoft.com/pricing/reserved-vm-instances/) in Azure, le stime dei costi nella valutazione vengono quindi eseguite prendendo in considerazione gli sconti RI. Le istanze riservate sono attualmente supportate solo per l'offerta con pagamento in base al consumo in Azure Migrate.
**Criterio di dimensionamento** | Criterio da usare per le macchine virtuali di dimensioni corrette per Azure.The criterion to be used to right-size VMs for Azure. È possibile eseguire il dimensionamento *basato sulle prestazioni* o ridimensionare le macchine virtuali *come*locali, senza considerare la cronologia delle prestazioni.
**Cronologia delle prestazioni** | Durata da considerare per la valutazione dei dati sulle prestazioni delle macchine. Questa proprietà è applicabile solo quando il criterio di ridimensionamento è *basato sulle prestazioni.*
**Utilizzo percentile** | Valore percentile dell'esempio di prestazioni da tenere in considerazione per il dimensionamento corretto. Questa proprietà è applicabile solo quando il ridimensionamento è *basato sulle prestazioni.*
**Serie macchina virtuale** |     È possibile specificare la serie di macchine virtuali che si intende tenere in considerazione per il dimensionamento corretto. Se ad esempio si ha un ambiente di produzione di cui non si intende eseguire la migrazione a macchine virtuali serie A in Azure, è possibile escludere la serie A dall'elenco o dalla serie e il dimensionamento viene eseguito solo nella serie selezionata.
**Fattore di comfort** | Azure Migrate Server Assessment considera un buffer (fattore di comfort) durante la valutazione. che viene applicato ai dati sull'utilizzo delle VM (CPU, memoria, disco e rete). Il fattore di comfort tiene conto di aspetti come utilizzo stagionale, breve cronologia delle prestazioni e probabile aumento dell'utilizzo futuro.<br/><br/> Da una VM con 10 core e un utilizzo del 20%, ad esempio, si ottiene normalmente una VM con 2 core. Con un fattore di comfort pari a 2.0x, invece, il risultato è una VM con 4 core.
**Offerta** | [Offerta di Azure](https://azure.microsoft.com/support/legal/offer-details/) sottoscritta. Azure Migrate stima il costo di conseguenza.
**Valuta** | Valuta di fatturazione.
**Sconto (%)** | Qualsiasi sconto specifico della sottoscrizione ricevuto oltre all'offerta Azure.<br/> L'impostazione predefinita è 0%.
**Tempo di attività macchina virtuale** | Se non si prevede di eseguire ininterrottamente le macchine virtuali in Azure, è possibile specificare la durata (numero di giorni al mese e numero di ore al giorno) dell'esecuzione. Il costo verrà stimato di conseguenza.<br/> Il valore predefinito è di 31 giorni al mese e di 24 ore al giorno.
**Vantaggio Azure Hybrid** | Specificare se si dispone della garanzia del software e si è idonei per il [vantaggio Azure Hybrid.](https://azure.microsoft.com/pricing/hybrid-use-benefit/) Se il valore è impostato su Sì, alle macchine virtuali Windows si applicano i prezzi di Azure per sistemi non Windows. Il valore predefinito è Yes.


## <a name="edit-assessment-properties"></a>Modificare le proprietà di valutazione

Per modificare le proprietà di valutazione dopo la creazione di una valutazione, eseguire le operazioni seguenti:To edit assessment properties after creating an assessment, do the following:

1. Nel progetto Azure Migrate fare clic su **Server**.
2. In **Azure Migrate: Server Assessment**fare clic sul conteggio delle valutazioni.
3. In **Valutazione**fare clic sulla valutazione pertinente > **Modifica proprietà**.
5. Personalizzare le proprietà di valutazione in base alla tabella precedente.
6. Fare clic su **Salva** per aggiornare la valutazione.


È inoltre possibile modificare le proprietà di valutazione durante la creazione di una valutazione.


## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni](concepts-assessment-calculation.md) sul modo in cui vengono calcolate le valutazioni.
