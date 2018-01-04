---
title: Personalizzare le impostazioni di valutazione di Azure Migrate | Microsoft Docs
description: Descrive come configurare ed eseguire una valutazione per la migrazione di macchine virtuali VMware in Azure usando Azure Migration Planner
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: ce47790f6214864afdba33eb5cbe3a9e49b81cd5
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2017
---
# <a name="customize-an-assessment"></a>Personalizzare una valutazione

[Azure Migrate](migrate-overview.md) crea valutazioni con impostazioni predefinite. Dopo aver creato una valutazione, è possibile modificare le impostazioni predefinite seguendo le istruzioni contenute in questo articolo.


## <a name="edit-assessment-values"></a>Modificare i valori di valutazione

1. Nella pagina **Valutazioni** del progetto Azure Migrate selezionare la valutazione e quindi fare clic su **Modifica proprietà**.
2. Modificare le impostazioni in base alla tabella seguente.

    **Impostazione** | **Dettagli** | **Default**
    --- | --- | ---
    **Posizione di destinazione** | Area di Azure in cui si vuole eseguire la migrazione. |  L'area predefinita è Stati Uniti occidentali 2.
    **Ridondanza dell'archiviazione** | Tipo di archiviazione che verrà usato dalle macchine virtuali di Azure dopo la migrazione. | Attualmente è supportata solo la replica di tipo [Archiviazione con ridondanza locale](../storage/common/storage-redundancy.md#locally-redundant-storage).
    **Fattore di comfort** | Il fattore di comfort è un buffer usato durante la valutazione. Usarlo per tenere conto di aspetti come l'utilizzo stagionale, una breve cronologia delle prestazioni e il probabile aumento dell'utilizzo futuro. | L'impostazione predefinita è 1.3x.
    **Perfomance history** (Cronologia prestazioni) | Tempo necessario per la valutazione della cronologia delle prestazioni. | Il valore predefinito è un mese.
    **Utilizzo percentile** | Valore percentile da considerare per la cronologia delle prestazioni. | Il valore predefinito è 95%.
    **Piano tariffario** | È possibile specificare il [piano tariffario](https://azure.microsoft.com/blog/basic-tier-virtual-machines-2/) per una macchina virtuale.  | Per impostazione predefinita, viene usato il livello [Standard](../virtual-machines/windows/sizes-general.md).
    **Offerta** | [Offerte di Azure](https://azure.microsoft.com/support/legal/offer-details/) applicabili. | L'opzione predefinita è [Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/).
    **Valuta** | Valuta di fatturazione. | La valuta predefinita è il dollaro statunitense.
    **Sconto (%)** | Qualsiasi sconto specifico della sottoscrizione ricevuto oltre a qualsiasi offerta. | L'impostazione predefinita è 0%.
    **Vantaggio Azure Hybrid Use** | Indica se è stata effettuata la registrazione per il [vantaggio Azure Hybrid Use](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Se il valore è impostato su Sì, alle macchine virtuali Windows si applicano i prezzi di Azure per sistemi non Windows. | Il valore predefinito è Yes.

3. Fare clic su **Salva** per aggiornare la valutazione.


## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni](concepts-assessment-calculation.md) sul modo in cui vengono calcolate le valutazioni.
