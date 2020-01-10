---
title: Gestire i criteri di apprendimento-personalizzatore
description: Questo articolo contiene le risposte alle domande frequenti sulla risoluzione dei problemi relativi a personalizzazione.
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: 707ba9fe7b73ef74e21321533ba02b11f2bad850
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75843482"
---
# <a name="manage-learning-policy"></a>Gestire i criteri di apprendimento

Le impostazioni dei criteri di apprendimento determinano gli _iperparametri_ del training del modello. Il criterio di apprendimento è definito in un file di `.json`.

## <a name="import-a-new-learning-policy"></a>Importa un nuovo criterio di apprendimento

1. Aprire il [portale di Azure](https://portal.azure.com)e selezionare la risorsa di personalizzazione.
1. Selezionare **modello e impostazioni di apprendimento** nella sezione **Gestione risorse** .
1. Per le **impostazioni di importazione di apprendimento** selezionare il file creato con il formato JSON specificato in precedenza, quindi selezionare il pulsante **carica** .

    Attendere la notifica che il criterio di apprendimento è stato caricato correttamente.

## <a name="export-a-learning-policy"></a>Esportare un criterio di formazione

1. Aprire il [portale di Azure](https://portal.azure.com)e selezionare la risorsa di personalizzazione.
1. Selezionare **modello e impostazioni di apprendimento** nella sezione **Gestione risorse** .
1. Per **Import Learning Settings** selezionare il pulsante **Export Learning Settings** . In questo modo il file di `json` viene salvato nel computer locale.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sui [concetti](concept-active-learning.md#learning-settings) relativi ai criteri di apprendimento

[Informazioni sulla disponibilità in base all'area geografica](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)