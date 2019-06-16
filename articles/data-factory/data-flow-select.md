---
title: Trasformazione Selezione per il flusso di dati di mapping di Azure Data Factory
description: Trasformazione Selezione per il flusso di dati di mapping di Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: bc83b41067d587adce41658a2c4b3d68969750ba
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61364463"
---
# <a name="azure-data-factory-mapping-data-flow-select-transformation"></a>Trasformazione Selezione per il flusso di dati di mapping di Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Usare questa trasformazione per la selezione delle colonne (riduzione del numero di colonne) o per definire alias per i nomi di colonne e flussi.

La trasformazione Selezione consente di creare un alias per un intero flusso, o per le colonne in tale flusso, di assegnare nomi diversi (alias) e quindi di fare riferimento a questi nuovi nomi in un secondo momento nel flusso di dati. Questa trasformazione è utile per scenari di self-join. Per implementare un self join in un flusso di dati di Azure Data Factory, prendere un flusso, creare un ramo del flusso con "New Branch"(Nuovo ramo) e quindi aggiungere subito dopo una trasformazione "Select" (Selezione). Tale flusso avrà ora un nuovo nome che è possibile usare per il join con il flusso originale, creando così un self join:

![Self-join](media/data-flow/selfjoin.png "Self-join")

Nel diagramma precedente la trasformazione Selezione si trova nella parte superiore. Viene usata per creare un alias del flusso originale in "OrigSourceBatting". Nella trasformazione Join evidenziata sotto, è possibile vedere che questo flusso alias Select (Selezione) viene usato come join di destra, consentendo di fare riferimento alla medesima chiave sia nel lato sinistro che destro dell'inner join.

È possibile usare la trasformazione Selezione anche come modo per deselezionare le colonne dal flusso di dati. Ad esempio, se nel sink sono definite 6 colonne, ma se ne vogliono selezionare solo 3 specifiche per la trasformazione e per poi inserirle nel flusso per il sink, è possibile selezionare solo quelle 3 tramite la trasformazione Selezione.

> [!NOTE]
> È necessario disattivare "Select All" (Seleziona tutto) per selezionare solo colonne specifiche

Opzioni

L'impostazione predefinita per "Select" (Selezione) prevede l'inclusione di tutte le colonne in ingresso mantenendo i nomi originali. È possibile definire un alias per il flusso impostando il nome della trasformazione Selezione.

Per impostare un alias per singole colonne, deselezionare "Select All" (Seleziona tutto) e usare il mapping delle colonne nella parte inferiore.

![Trasformazione Selezione](media/data-flow/select001.png "Selezionare un alias")
