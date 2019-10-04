---
title: Personalizzare le valutazioni per la valutazione di Azure Migrate server | Microsoft Docs
description: Viene descritto come personalizzare le valutazioni create con Azure Migrate server Assessment
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/15/2019
ms.author: raynew
ms.openlocfilehash: 2cfac978b0a5af20e9e2fa1e32a7361488f20fbe
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234279"
---
# <a name="customize-an-assessment"></a>Personalizzare una valutazione

Questo articolo descrive come personalizzare le valutazioni create da Azure Migrate server assessment.

[Azure migrate](migrate-services-overview.md) offre un hub centrale per tenere traccia dell'individuazione, della valutazione e della migrazione delle app e dei carichi di lavoro locali e delle macchine virtuali del cloud privato/pubblico in Azure. L'hub fornisce strumenti Azure Migrate per la valutazione e la migrazione, nonché offerte di fornitori di software indipendenti (ISV) di terze parti.

È possibile usare lo strumento Azure Migrate server assessment per creare valutazioni per le macchine virtuali VMware locali e le VM Hyper-V, in preparazione per la migrazione ad Azure.

## <a name="about-assessments"></a>Informazioni sulle valutazioni

Esistono due tipi di valutazione che è possibile eseguire utilizzando Azure Migrate server assessment.

**Valutazione** | **Dettagli** | **Dati**
--- | --- | ---
**Basato sulle prestazioni** | Valutazioni basate sui dati sulle prestazioni raccolti | **Dimensioni macchina virtuale consigliate**: In base ai dati di utilizzo della CPU e della memoria.<br/><br/> **Tipo di disco consigliato (disco gestito standard o Premium)** : In base ai IOPS e alla velocità effettiva dei dischi locali.
**Come in locale** | Valutazioni basate sul dimensionamento locale. | **Dimensioni macchina virtuale consigliate**: In base alle dimensioni della macchina virtuale locale<br/><br> **Tipo di disco consigliato**: In base all'impostazione del tipo di archiviazione selezionata per la valutazione.


## <a name="how-is-an-assessment-done"></a>Come viene eseguita una valutazione?

Una valutazione effettuata in Azure Migrate server Assessment prevede tre fasi. La valutazione inizia con un'analisi di idoneità, seguita dalla determinazione della dimensione e termina con una stima dei costi mensili. Un computer passa alla fase successiva soltanto se supera quella precedente. Ad esempio, se un computer non supera il controllo di idoneità di Azure, viene contrassegnato come non idoneo per Azure e la dimensione e i costi non vengono calcolati. [Altre informazioni.](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation)

## <a name="whats-in-an-assessment"></a>Elementi inclusi nella valutazione

**Proprietà** | **Dettagli**
--- | ---
**Posizione di destinazione** | Area di Azure in cui si vuole eseguire la migrazione.<br/> Server Assessment supporta attualmente le aree di destinazione seguenti: Australia orientale, Australia sudorientale, Brasile meridionale, Canada centrale, Canada orientale, India centrale, Stati Uniti centrali, Cina orientale, Cina settentrionale, Asia orientale, Stati Uniti orientali, East Uniti, Germania centrale, Germania nord-orientale, Giappone orientale, Giappone occidentale, Corea centrale, Corea meridionale, Nord Stati Uniti centrali, Europa settentrionale, Stati Uniti centro-meridionali, Asia sudorientale, India meridionale, Regno Unito meridionale, Regno Unito occidentale, US Gov Arizona, US Gov Texas, US Gov Virginia, Stati Uniti centro-occidentali, Europa occidentale, India occidentale, Stati Uniti occidentali e Uniti occidentali.
**Tipo di archiviazione** | È possibile usare questa proprietà per specificare il tipo di dischi che si vuole spostare in Azure.<br/><br/> Per il dimensionamento delle sedi locali, è possibile specificare il tipo di archiviazione di destinazione come dischi gestiti da Premium, dischi gestiti da SDD Standard o dischi gestiti da HDD Standard. Per il dimensionamento basato sulle prestazioni, è possibile specificare il tipo di disco di destinazione come dischi automatici, gestiti con gestione Premium, dischi gestiti da HDD Standard o dischi gestiti da SDD Standard.<br/><br/> Quando si specifica il tipo di archiviazione automatico, la preferenza dei dischi è basata sui dati delle relative prestazioni (numero di operazioni di I/O al secondo e velocità effettiva). Se si specifica il tipo di archiviazione Premium o standard, la valutazione indicherà uno SKU del disco all'interno del tipo di archiviazione selezionato. Se si vuole ottenere un contratto di Service per macchine virtuali a istanza singola pari al 99,9%, è possibile specificare il tipo di archiviazione come dischi gestiti Premium. che garantisce che tutti i dischi nella valutazione vengano raccomandati come Managed Disks Premium. Azure
**Istanze riservate (RI)** | Questa proprietà consente di specificare se sono presenti [istanze riservate](https://azure.microsoft.com/pricing/reserved-vm-instances/) in Azure. le stime dei costi nella valutazione vengono quindi effettuate in sconti per il ri. Le istanze riservate sono attualmente supportate solo per l'offerta con pagamento in base al consumo in Azure Migrate.
**Criterio di dimensionamento** | Il criterio da usare per dimensionare correttamente le VM per Azure. È possibile eseguire il ridimensionamento in *base alle prestazioni* o ridimensionare le macchine virtuali *in locale*, senza considerare la cronologia delle prestazioni.
**Cronologia delle prestazioni** | Durata da considerare per la valutazione dei dati sulle prestazioni dei computer. Questa proprietà è applicabile solo quando il criterio di dimensionamento è *basato sulle prestazioni*.
**Utilizzo percentile** | Valore percentile dell'esempio di prestazioni da tenere in considerazione per il dimensionamento corretto. Questa proprietà è applicabile solo quando il ridimensionamento è *basato sulle prestazioni*.
**Serie macchina virtuale** |     È possibile specificare la serie di macchine virtuali che si intende tenere in considerazione per il dimensionamento corretto. Se ad esempio si ha un ambiente di produzione di cui non si intende eseguire la migrazione a macchine virtuali serie A in Azure, è possibile escludere la serie A dall'elenco o dalla serie e il dimensionamento viene eseguito solo nella serie selezionata.
**Fattore di comfort** | Azure Migrate server Assessment considera un buffer (fattore di comfort) durante la valutazione. che viene applicato ai dati sull'utilizzo delle VM (CPU, memoria, disco e rete). Il fattore di comfort tiene conto di aspetti come utilizzo stagionale, breve cronologia delle prestazioni e probabile aumento dell'utilizzo futuro.<br/><br/> Da una VM con 10 core e un utilizzo del 20%, ad esempio, si ottiene normalmente una VM con 2 core. Con un fattore di comfort pari a 2.0x, invece, il risultato è una VM con 4 core.
**Offerta** | [Offerta di Azure](https://azure.microsoft.com/support/legal/offer-details/) sottoscritta. Azure Migrate stima il costo di conseguenza.
**Valuta** | Valuta di fatturazione.
**Sconto (%)** | Qualsiasi sconto specifico della sottoscrizione ricevuto oltre all'offerta Azure.<br/> L'impostazione predefinita è 0%.
**Tempo di attività macchina virtuale** | Se non si prevede di eseguire ininterrottamente le macchine virtuali in Azure, è possibile specificare la durata (numero di giorni al mese e numero di ore al giorno) dell'esecuzione. Il costo verrà stimato di conseguenza.<br/> Il valore predefinito è di 31 giorni al mese e di 24 ore al giorno.
**Vantaggio Azure Hybrid** | Specificare se si dispone di Software Assurance e si è idonei per [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Se il valore è impostato su Sì, alle macchine virtuali Windows si applicano i prezzi di Azure per sistemi non Windows. Il valore predefinito è Yes.


## <a name="edit-assessment-properties"></a>Modificare le proprietà di valutazione

Per modificare le proprietà della valutazione dopo la creazione di una valutazione, procedere come segue:

1. Nel progetto Azure Migrate fare clic su **Server**.
2. In **Azure migrate: Server Assessment**, fare clic sul conteggio assessment.
3. In **valutazione**fare clic sulla valutazione pertinente > **modificare le proprietà**.
5. Personalizzare le proprietà di valutazione in base alla tabella precedente.
6. Fare clic su **Salva** per aggiornare la valutazione.


Quando si crea una valutazione, è anche possibile modificare le proprietà di valutazione.


## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni](concepts-assessment-calculation.md) sul modo in cui vengono calcolate le valutazioni.
