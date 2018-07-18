---
title: Esaminare espressioni di endpoint per usare l'apprendimento attivo di Language Understanding (LUIS) - Azure | Microsoft Docs
description: Usare la funzionalità di apprendimento attivo denominata "Esamina espressioni di endpoint" per migliorare più velocemente le previsioni delle prestazioni.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
ms.openlocfilehash: b9672e8e63fb601d4411a342b7f3c00e30f9e002
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/11/2018
ms.locfileid: "35379132"
---
# <a name="enable-active-learning-by-reviewing-endpoint-utterances"></a>Abilitare l'apprendimento attivo grazie alla revisione delle espressioni di endpoint
L'apprendimento attivo è una delle tre strategie per migliorare la precisione delle previsioni e fra esse la più facile da attuare. 

## <a name="what-is-active-learning"></a>Che cos'è l'apprendimento attivo
L'apprendimento attivo è un processo composto da due passaggi. Nel primo passaggio, LUIS seleziona le espressioni da convalidare che riceve nell'endpoint dell'app. Il secondo passaggio viene eseguito dal proprietario o dal collaboratore dell'applicazione per convalidare l'espressione selezionata per la fase di [revisione](label-suggested-utterances.md), incluse la finalità corretta e tutte le entità interne a quest'ultima. Dopo aver esaminato l'espressione, esegui il training e pubblica di nuovo l'app. 

## <a name="which-utterances-are-on-the-review-list"></a>Quali espressioni sono incluse nell'elenco di revisione
LUIS aggiunge espressioni all'elenco di revisione quando la finalità di attivazione principale ha un punteggio basso o i due punteggi principali delle finalità sono troppo ravvicinati. 

## <a name="where-are-the-utterances-from"></a>Da dove provengono le espressioni
Le espressioni di endpoint provengono dalle query degli utenti finali sull'endpoint HTTP dell'applicazione. Se l'app non è pubblicata o non ha ancora ricevuto riscontri, non è presente alcuna espressione da esaminare. Se non ricevi alcun riscontro di endpoint per una finalità o un'entità specifica, non è presente alcuna espressione da esaminare che li contenga. 

## <a name="schedule-review-periodically"></a>Pianificare periodicamente le revisioni
La revisione delle espressioni consigliate non deve essere eseguita ogni giorno, ma deve far parte della manutenzione di routine di LUIS. 

## <a name="delete-review-items-programmatically"></a>Eliminare gli elementi di revisione a livello di codice
Se l'app è grande, è possibile scegliere di esaminare alcune espressioni e di eliminare le restanti dall'elenco a livello di codice. Questa operazione viene eseguita in primo luogo [consultando](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) l'elenco e quindi [eliminando](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) le espressioni per ID.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulle modalità di [revisione](Label-Suggested-Utterances.md) delle espressioni di endpoint
