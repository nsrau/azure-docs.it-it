---
title: Trasformazione Selezione per il flusso di dati di mapping di Azure Data Factory
description: Trasformazione Selezione per il flusso di dati di mapping di Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 15c74637a2dc42ec44f582878b5505d94637cd7b
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314201"
---
# <a name="azure-data-factory-mapping-data-flow-select-transformation"></a>Trasformazione Selezione per il flusso di dati di mapping di Azure Data Factory
[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Usare questa trasformazione per la selettività delle colonne (riducendo il numero di colonne), le colonne alias e i nomi di flusso e riordinare le colonne.

## <a name="how-to-use-select-transformation"></a>Come usare selezione trasformazione
La trasformazione Selezione consente di creare un alias per un intero flusso, o per le colonne in tale flusso, di assegnare nomi diversi (alias) e quindi di fare riferimento a questi nuovi nomi in un secondo momento nel flusso di dati. Questa trasformazione è utile per scenari di self-join. Per implementare un self join in un flusso di dati di Azure Data Factory, prendere un flusso, creare un ramo del flusso con "New Branch"(Nuovo ramo) e quindi aggiungere subito dopo una trasformazione "Select" (Selezione). Tale flusso avrà ora un nuovo nome che è possibile usare per il join con il flusso originale, creando così un self join:

![Self-join](media/data-flow/selfjoin.png "Self-join")

Nel diagramma precedente la trasformazione Selezione si trova nella parte superiore. Viene usata per creare un alias del flusso originale in "OrigSourceBatting". Nella trasformazione di join evidenziata sotto di essa si noterà che questo flusso di alias SELECT viene usato come join a destra, consentendo di fare riferimento alla stessa chiave sia nella parte sinistra & destra del Inner join.

È possibile usare la trasformazione Selezione anche come modo per deselezionare le colonne dal flusso di dati. Ad esempio, se nel sink sono definite 6 colonne, ma se ne vogliono selezionare solo 3 specifiche per la trasformazione e per poi inserirle nel flusso per il sink, è possibile selezionare solo quelle 3 tramite la trasformazione Selezione.

> [!NOTE]
> È necessario disattivare "Select All" (Seleziona tutto) per selezionare solo colonne specifiche

![Trasformazione Selezione](media/data-flow/select001.png "Selezionare un alias")

## <a name="options"></a>Opzioni
* L'impostazione predefinita per "Select" (Selezione) prevede l'inclusione di tutte le colonne in ingresso mantenendo i nomi originali. È possibile definire un alias per il flusso impostando il nome della trasformazione Selezione.
* Per impostare un alias per singole colonne, deselezionare "Select All" (Seleziona tutto) e usare il mapping delle colonne nella parte inferiore.
* Scegliere Ignora duplicati per eliminare le colonne duplicate dai metadati di input o di output.

![Ignora duplicati](media/data-flow/select-skip-dup.png "Ignora duplicati")

## <a name="next-steps"></a>Passaggi successivi
* Dopo aver utilizzato SELECT per rinominare, riordinare e utilizzare le colonne alias, utilizzare la [trasformazione sink](data-flow-sink.md) per inserire i dati in un archivio dati.
