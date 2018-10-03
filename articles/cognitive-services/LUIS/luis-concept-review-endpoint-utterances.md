---
title: Esaminare espressioni di endpoint per usare l'apprendimento attivo di Language Understanding (LUIS)
titleSuffix: Azure Cognitive Services
description: L'apprendimento attivo è una delle tre strategie per migliorare la precisione delle previsioni e fra esse la più facile da attuare. Con l'apprendimento attivo, è possibile verificare la correttezza della finalità e dell'entità delle espressioni di endpoint. LUIS sceglie espressioni di endpoint di cui è certo.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 78cc2a8a2b9295654d0c6264cbf4a4d634b16544
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038171"
---
# <a name="enable-active-learning-by-reviewing-endpoint-utterances"></a>Abilitare l'apprendimento attivo grazie alla revisione delle espressioni di endpoint
L'apprendimento attivo è una delle tre strategie per migliorare la precisione delle previsioni e fra esse la più facile da attuare. Con l'apprendimento attivo, è possibile verificare la correttezza della finalità e dell'entità delle espressioni di endpoint. LUIS sceglie espressioni di endpoint di cui è certo.

## <a name="what-is-active-learning"></a>Che cos'è l'apprendimento attivo
L'apprendimento attivo è un processo composto da due passaggi. Nel primo passaggio, LUIS seleziona le espressioni da convalidare che riceve nell'endpoint dell'app. Il secondo passaggio viene eseguito dal proprietario o dal collaboratore dell'applicazione per convalidare l'espressione selezionata per la fase di [revisione](luis-how-to-review-endoint-utt.md), incluse la finalità corretta e tutte le entità interne a quest'ultima. Dopo aver esaminato l'espressione, esegui il training e pubblica di nuovo l'app. 

## <a name="which-utterances-are-on-the-review-list"></a>Quali espressioni sono incluse nell'elenco di revisione
LUIS aggiunge espressioni all'elenco di revisione quando la finalità di attivazione principale ha un punteggio basso o i due punteggi principali delle finalità sono troppo ravvicinati. 

## <a name="single-pool-for-utterances-per-app"></a>Singolo pool per espressioni per app
L'elenco **Esaminare le espressioni endpoint** non cambia in base alla versione. È disponibile un singolo pool di espressioni da esaminare, indipendentemente dalla versione dell'espressione che si sta modificando attivamente o dalla versione dell'app pubblicata nell'endpoint. 

## <a name="where-are-the-utterances-from"></a>Da dove provengono le espressioni
Le espressioni di endpoint provengono dalle query degli utenti finali sull'endpoint HTTP dell'applicazione. Se l'app non è pubblicata o non ha ancora ricevuto riscontri, non è presente alcuna espressione da esaminare. Se non ricevi alcun riscontro di endpoint per una finalità o un'entità specifica, non è presente alcuna espressione da esaminare che li contenga. 

## <a name="schedule-review-periodically"></a>Pianificare periodicamente le revisioni
La revisione delle espressioni consigliate non deve essere eseguita ogni giorno, ma deve far parte della manutenzione di routine di LUIS. 

## <a name="delete-review-items-programmatically"></a>Eliminare gli elementi di revisione a livello di codice
Se l'app è grande, è possibile scegliere di esaminare alcune espressioni e di eliminare le restanti dall'elenco a livello di codice. Questa operazione viene eseguita in primo luogo [consultando](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) l'elenco e quindi [eliminando](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) le espressioni per ID.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulle modalità di [revisione](luis-how-to-review-endoint-utt.md) delle espressioni di endpoint
