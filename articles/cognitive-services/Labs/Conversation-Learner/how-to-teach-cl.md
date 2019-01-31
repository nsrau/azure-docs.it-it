---
title: Come eseguire il training con Conversation Learner - Servizi cognitivi Microsoft | Microsoft Docs
titleSuffix: Azure
description: Informazioni su come eseguire il training con Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 8c55bb27ce5a413c5ceae22371ad61a5acf47281
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55220264"
---
# <a name="how-to-teach-with-conversation-learner"></a>Come eseguire il training con Conversation Learner 

Questo documento illustra i segnali che Conversation Learner è in grado di riconoscere e descrive come viene eseguito il training.  

Il training può essere suddiviso in due passaggi distinti: estrazione di entità e selezione dell'azione.

## <a name="entity-extraction"></a>Estrazione di entità

Dietro le quinte, per l'estrazione di entità Conversation Learner usa [LUIS](https://www.luis.ai).  Se si ha familiarità con LUIS, la stessa esperienza si applica all'estrazione di entità in Conversation Learner.

I modelli di estrazione di entità riconoscono il *contenuto* e il *contesto* in un'espressione dell'utente.  Se la parola "Seattle" è stata etichettata come città in un'espressione dell'utente come "What's the weather in Seattle", ad esempio, l'estrazione di entità può riconoscere lo stesso contenuto ("Seattle") come città in un'altra espressione come "Population of Seattle", anche se le espressioni sono molto diverse.  Se la parola "Francis" è stata riconosciuta come un nome in "Schedule a meeting with Francis", invece, un nuovo nome non rilevato in precedenza può essere riconosciuto in un contesto simile come "Set up a meeting with Robin".  L'apprendimento automatico deduce quando deve considerare il contenuto, il contesto o entrambi, in base agli esempi di training.

Attualmente, l'estrazione di entità riconosce solo il contenuto dell'espressione corrente.  A differenza della selezione dell'azione, illustrata di seguito, non rileva la cronologia del dialogo, come i turni precedenti del sistema o dell'utente oppure le entità riconosciute in precedenza.  Di conseguenza, il comportamento dell'estrazione di entità viene "condiviso" tra tutte le espressioni.  Se in un caso la parola "Apple" viene etichettata come entità di tipo "Fruit" nell'espressione dell'utente "I want Apple", ad esempio, il modello di estrazione di entità prevede che in questa espressione ("I want Apple") la parola "Apple" sia sempre etichettata come "Fruit".

Se l'estrazione di entità non ha il comportamento previsto, sono disponibili questi possibili rimedi:

- Per prima cosa, provare ad aggiungere altri esempi di training, in particolare esempi che rivelino il contesto tipico dell'entità (parole circostanti) o eccezioni.
- Valutare la possibilità di aggiungere la proprietà "entità prevista" a un'azione, se appropriato.  Per altri dettagli, vedere l'esercitazione sulle entità previste.
- Anche se è possibile aggiungere l'elaborazione manuale a `EntityExtractionCallback` per estrarre le entità con il codice, questo è l'approccio meno consigliato perché non consente di sfruttare i miglioramenti dell'apprendimento automatico con l'evoluzione del sistema.

## <a name="action-selection"></a>Selezione dell'azione

Per la selezione dell'azione viene usata una rete neurale ricorrente, che accetta come input tutta la cronologia della conversazione.  La selezione dell'azione è quindi un processo con stato che riconosce i valori delle entità e le espressioni dell'utente e del sistema precedenti.  

Nel processo di apprendimento vengono naturalmente preferiti alcuni segnali.  In altri termini, se Conversation Learner può basare una decisione di selezione dell'azione su segnali con preferenza superiore, userà tali segnali. In caso contrario, userà i segnali con preferenza inferiore.

La tabella seguente mostra tutti i segnali in Conversation Learner e quelli usati nella selezione dell'azione.  Si noti che l'ordine delle parole nelle espressioni dell'utente viene ignorato.

Segnale | Preferenza (1=preferenza massima) | Note
--- | --- | --- 
Azione del sistema nel turno precedente | 1 | 
Entità presenti nel turno corrente | 1 | 
Primo turno o meno | 1 |
Corrispondenza esatta di parole nell'espressione corrente dell'utente | 2 | 
Parole di significato simile nell'espressione corrente dell'utente | 3 | 
Azioni del sistema prima del turno precedente | 4 |
Entità presenti nei turni precedenti al turno corrente | 4 | 
Espressioni dell'utente prima del turno corrente | 5 | 

> [!NOTE]
> La selezione dell'azione non si basa sul contenuto delle azioni del sistema, come il testo, il contenuto delle schede oppure il nome o il comportamento delle API, ma solo sulla loro identità.  Di conseguenza, la modifica del contenuto di un'azione non influisce sul comportamento del modello di selezione dell'azione.
>
> Inoltre, non viene usato il contenuto/valore delle entità e ne viene usata solo la presenza/assenza.

Se la selezione dell'azione non ha il comportamento previsto, sono disponibili questi possibili rimedi:

- Aggiungere altri dialoghi di training, in particolare dialoghi che illustrino i segnali da considerare nella selezione dell'azione.  Ad esempio, se nella selezione dell'azione deve essere preferito un segnale rispetto a un altro, fornire esempi che mostrino il segnale preferito nello stesso stato e una variazione negli altri segnali.  Per l'apprendimento di alcune sequenze potrebbero essere necessari più dialoghi di training.
- Aggiungere entità obbligatorie e non qualificanti alle definizioni delle azioni.  Questo limita i casi in cui le azioni sono disponibili e può essere utile per integrare regole business e alcuni modelli di buon senso. 

## <a name="updates-to-models"></a>Aggiornamenti dei modelli

Ogni volta che si aggiunge o si modifica un'entità, un'azione o un dialogo di training nell'interfaccia utente, questa operazione genera una richiesta di ripetizione del training sia del modello di estrazione di entità sia di quello di selezione dell'azione.  Tale richiesta viene inserita in una coda e la ripetizione del training viene eseguita in modo asincrono.  Quando è disponibile un nuovo modello, questo viene usato da tale momento in poi per l'estrazione di entità e la selezione dell'azione.  Il processo di ripetizione del training richiede in genere circa 5 secondi, ma può essere necessario più tempo se il modello è complesso o il carico nel servizio di training è elevato.

Dato che il training viene eseguito in modo asincrono, è possibile che le modifiche apportate non si riflettano immediatamente.  Se l'estrazione di entità o la selezione dell'azione non ha il comportamento previsto in base alle modifiche apportate negli ultimi 5-10 secondi, questa caratteristica potrebbe essere la causa.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Limiti e valori predefiniti](./cl-values-and-boundaries.md)
