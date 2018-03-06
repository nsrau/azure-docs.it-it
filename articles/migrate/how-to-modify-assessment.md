---
title: Personalizzare le impostazioni di valutazione di Azure Migrate | Microsoft Docs
description: Descrive come configurare ed eseguire una valutazione per la migrazione di macchine virtuali VMware in Azure usando Azure Migration Planner
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 02/26/2018
ms.author: raynew
ms.openlocfilehash: efb4ad59d25a0c1209e4f0f6cd406c2f0d48159c
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="customize-an-assessment"></a>Personalizzare una valutazione

[Azure Migrate](migrate-overview.md) crea valutazioni con proprietà predefinite. Dopo aver creato una valutazione, è possibile modificare le proprietà predefinite seguendo le istruzioni contenute in questo articolo.


## <a name="edit-assessment-properties"></a>Modificare le proprietà di valutazione

1. Nella pagina **Valutazioni** del progetto di migrazione selezionare la valutazione e fare clic su **Modifica proprietà**.
2. Modificare le proprietà in base alla tabella seguente:

    **Impostazione** | **Dettagli** | **Default**
    --- | --- | ---
    **Posizione di destinazione** | Area di Azure in cui si vuole eseguire la migrazione.<br/><br/> Azure Migrate supporta attualmente 30 aree, tra cui Asia orientale, Asia sud-orientale, Australia orientale, Australia sud-orientale, Brasile meridionale, Canada centrale, Canada orientale, Cina orientale, Cina settentrionale, Corea centrale, Corea meridionale, Europa occidentale, Europa settentrionale, Germania centrale, Germania nordorientale, Giappone occidentale, Giappone orientale, India centrale, India meridionale, India occidentale, Regno Unito meridionale, Regno Unito occidentale, Stati Uniti centrali, Stati Uniti centro-meridionali, Stati Uniti centro-occidentali, Stati Uniti centro-settentrionali, Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti occidentali e Stati Uniti occidentali 2. |  L'area predefinita è Stati Uniti occidentali 2.
    **Ridondanza dell'archiviazione** | Tipo di ridondanza dell'archiviazione che le macchine virtuali di Azure useranno dopo la migrazione. | Il valore predefinito è [Archiviazione con ridondanza locale](../storage/common/storage-redundancy.md#locally-redundant-storage). Azure Migrate supporta solo valutazioni basate su dischi gestiti e questi ultimi supportano solo l'archiviazione con ridondanza locale, per questo motivo la proprietà attualmente prevede solo l'opzione di archiviazione con ridondanza locale. 
    **Criterio di dimensionamento** | Criterio che Azure Migrate deve usare per definire correttamente le dimensioni delle macchine virtuali per Azure. È possibile eseguire il dimensionamento *basato sulle prestazioni* o definire le dimensioni delle macchine virtuali *come in locale*, senza considerare la cronologia delle prestazioni. | L'opzione predefinita è il dimensionamento basato sulle prestazioni.
    **Cronologia delle prestazioni** | Durata da considerare per la valutazione delle prestazioni delle macchine virtuali. Questa proprietà è applicabile solo quando il criterio di dimensionamento è *basato sulle prestazioni*. | Il valore predefinito è un giorno.
    **Utilizzo percentile** | Valore percentile dell'esempio di prestazioni da tenere in considerazione per il dimensionamento corretto. Questa proprietà è applicabile solo quando il criterio di dimensionamento è *basato sulle prestazioni*.  | Il valore predefinito è 95° percentile.
    **Piano tariffario** | È possibile specificare il [piano tariffario (Basic o Standard)](../virtual-machines/windows/sizes-general.md) per le macchine virtuali di Azure di destinazione. Se, ad esempio, si prevede di eseguire la migrazione di un ambiente di produzione, considerare il livello Standard, che offre macchine virtuali con bassa latenza, anche se a un costo superiore. D'altra parte, se si ha un ambiente di sviluppo e test, può essere preferibile il livello Basic, che ha macchine virtuali con latenza maggiore e costi minori. | Per impostazione predefinita, viene usato il livello [Standard](../virtual-machines/windows/sizes-general.md).
    **Fattore di comfort** | Durante la valutazione, Azure Migrate considera un buffer (fattore di comfort), che viene applicato ai dati sull'utilizzo delle VM (CPU, memoria, disco e rete). Il fattore di comfort tiene conto di aspetti come utilizzo stagionale, breve cronologia delle prestazioni e probabile aumento dell'utilizzo futuro.<br/><br/> Da una VM con 10 core e un utilizzo del 20%, ad esempio, si ottiene normalmente una VM con 2 core. Con un fattore di comfort pari a 2.0x, invece, il risultato è una VM con 4 core. | L'impostazione predefinita è 1.3x.
    **Offerta** | [Offerta Azure](https://azure.microsoft.com/support/legal/offer-details/) sottoscritta. | L'opzione predefinita è [Pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/).
    **Valuta** | Valuta di fatturazione. | La valuta predefinita è il dollaro statunitense.
    **Sconto (%)** | Qualsiasi sconto specifico della sottoscrizione ricevuto oltre all'offerta Azure. | L'impostazione predefinita è 0%.
    **Vantaggio Azure Hybrid** | Specificare se si dispone di licenze Software Assurance e se si è idonei per l'opzione [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Se il valore è impostato su Sì, alle macchine virtuali Windows si applicano i prezzi di Azure per sistemi non Windows. | Il valore predefinito è Yes.

3. Fare clic su **Salva** per aggiornare la valutazione.


## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni](concepts-assessment-calculation.md) sul modo in cui vengono calcolate le valutazioni.
