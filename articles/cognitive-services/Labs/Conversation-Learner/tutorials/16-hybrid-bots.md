---
title: Come usare Conversation Learner con altre tecnologie di creazione bot - Servizi cognitivi Microsoft | Microsoft Docs
titleSuffix: Azure
description: Informazioni su come usare Conversation Learner con altre tecnologie di creazione bot.
services: cognitive-services
author: mattm
manager: larsliden
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 07/13/2018
ms.author: nitinme
ms.openlocfilehash: d6af927e395532e43c7cc51c39665e2e42ac6781
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66389970"
---
# <a name="how-to-use-conversation-learner-with-other-bot-building-technologies"></a>Come usare Conversation Learner con altre tecnologie di creazione bot

Questa esercitazione illustra come usare Conversation Learner con altre tecnologie di creazione bot e come è possibile condividere la memoria o lo stato tra queste tecnologie. 

## <a name="video"></a>Video

[![Anteprima dell'esercitazione sui bot ibridi](https://aka.ms/cl_Tutorial_v3_Hybrid_Applications_Preview)](https://aka.ms/cl_Tutorial_v3_Hybrid_Applications)

## <a name="requirements"></a>Requisiti
Per creare i dialoghi di log, questa esercitazione richiede l'uso dell'emulatore del bot, non dell'interfaccia utente Web dei dialoghi di log. Altre informazioni su come configurare Bot Framework Emulator sono disponibili [qui](https://docs.microsoft.com/azure/bot-service/bot-service-debug-emulator?view=azure-bot-service-4.0). 

Per questa esercitazione è necessario che il bot di esercitazione ibrido sia in esecuzione:

    npm run tutorial-hybrid

## <a name="details"></a>Dettagli

Mentre Conversation Learner ha il controllo, tutto lo stato relativo alla sessione di Conversation Learner deve essere archiviato nel gestore della memoria di Conversation Learner. Ciò è necessario perché l'apprendimento automatico usa lo stato per determinare come condurre la conversazione. Lo stato esterno può essere passato in Conversation Learner in OnSessionStartCallback che viene chiamato all'avvio della sessione. Lo stato interno può essere restituito da OnSessionEndCallback al termine della sessione.

È possibile considerare Conversation Learner simile a una chiamata di funzione che prende uno stato iniziale e restituisce valori.

In questo esempio si creerà un bot ibrido con due sistemi diversi:
1. Un modello di Conversation Learner <br/>
    Il modello di Conversation Learner viene usato per determinare l'azione successiva del bot in base alla sessione corrente. Questa parte del bot prende una parte dello stato iniziale `isOpen` (che indica se un negozio è aperto o chiuso) e restituisce un'altra parte dello stato `purchaseItem` (il nome di un articolo acquistato all'utente).

2. Corrispondenza di testo <br />
    Viene semplicemente esaminato il testo in ingresso per stringhe specifiche e viene inviata la risposta. Questa parte del bot gestisce gli altri meccanismi di archiviazione del bot ed è responsabile dell'avvio della sessione di Conversation Learner. In particolare gestisce tre variabili: `usingConversationLearner`, `storeIsOpen` e `purchaseItem`.

Iniziare esaminando il modello usato in questa demo.

### <a name="open-the-demo"></a>Aprire la demo

Nell'interfaccia utente Web fare clic su "Import tutorials" (Importa esercitazioni) e selezionare il modello denominato "Tutorial-16-HybridBot".

## <a name="entities"></a>Entità

Aprire la pagina delle entità e notare due entità: `isOpen` e `purchaseItem`

Per comprendere l'uso di queste entità, aprire il file: `C:\<installedpath>\src\demos\tutorialHybrid.ts` per esaminare i callback.

Si noti che il codice in `OnSessionStartCallback` copia il valore `storeIsOpen` da una risorsa di archiviazione di conversazioni BotBuilder come valore dell'entità `isOpen`, in modo che sia disponibile per Conversation Learner. Vedere il codice seguente:

![](../media/tutorial17_sessionstart.PNG)

Allo stesso modo, il codice in `OnSessionEndCallback` (se la sessione è stata terminata a causa di un'attività acquisita e non a causa di un timeout) consente di copiare il valore dell'entità `purchaseItem` in una risorsa di archiviazione BotBuilder `purchaseItem`. Vedere il codice seguente:

![](../media/tutorial17_sessionend.PNG)

Esaminare le azioni.

## <a name="actions"></a>Azioni

Si noti che il modello a dispone di quattro azioni.

Le regole definite per le azioni sono le seguenti:

- Se è impostata l'entità `isOpen`, il Bot richiederà "What would you like to buy?" (Cosa si desidera acquistare?) ed effettuerà l'archiviazione nello slot `puchaseItem`.
- Se `isOpen` non è impostato, il bot segnalerà "I'm sorry we're closed".
- Le altre due azioni sono di tipo `END_SESSION`.
- L'azione END_SESSION indica a Conversation Learner che la conversazione è stata completata.

### <a name="overall-bot-logic"></a>Logica complessiva del Bot

Prima di tutto, se il flag `usingConversationLearner` dello stato del Bot è stato impostato, il controllo passa a Conversation Learner. In caso contrario, il controllo passa a qualcos'altro.  Questo esempio mostra una semplice corrispondenza di testo, ma può trattarsi di qualsiasi altra tecnologia Bot incluse LUIS, QnA Maker o anche un'altra istanza di Conversation Learner.

È necessario un metodo per l'utente per aprire e chiudere il negozio: viene effettuato un confronto tra stringhe con "open store" e "close store" e viene impostato il flag "storeIsOpen".

Successivamente, è necessario un modo per attivare il passaggio del controllo al modello di Conversation Learner. Quando si effettua una corrispondenza alla stringa "shop" vengono eseguite le operazioni seguenti:
- Impostazione del flag `usingConversationLearner` nella memoria del Bot.
- Chiamata del metodo "StartSession" sul modello di Conversation Learner.  Verrà attivata "onSessionStartCallback" che inizializzerà il valore dell'entità `isOpen`

Vedere qui di seguito:

![](../media/tutorial17_useConversationLearner.PNG)

È anche possibile eseguire una corrispondenza di testo a "history" che visualizzerà l'ultimo elemento acquistato.
Infine, se viene digitata qualsiasi altra cosa, vengono visualizzati i comandi utente disponibili

## <a name="train-dialog"></a>Dialogo di training

Per questa esercitazione, il modello è già con training preliminare.  Verrà eseguito il test del bot completo per visualizzare l'effetto pratico dei callback di inizio e fine sessione.

## <a name="testing-the-bot"></a>Test del Bot

A differenza dei bot dei singoli modelli di Conversation Leaner non sarà possibile eseguire il test dell'interfaccia utente di Conversation Leaner poiché è possibile visualizzare solo ciò che viene gestito dal modello di Conversation Leaner.

### <a name="install-the-bot-framework-emulator"></a>Installare Bot Framework Emulator

- Passare a [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator).
- Scaricare e installare l'emulatore.

### <a name="configure-the-emulator"></a>Configurare l'emulatore

- Aprire l'emulatore e verificare che l'URL abbia come destinazione la stessa porta in cui è in esecuzione il bot. Probabilmente: `http://localhost:3978/api/messages`

### <a name="test"></a>Test 

#### <a name="scenario-1-store-is-closed"></a>Scenario 1: il negozio è chiuso.
1. Immettere "shop". La gestione avviene mediante la corrispondenza di testo e verrà dato il controllo al modello di Conversation Learner.
2. Immettere "hello".  Poiché il valore `isOpen` non è impostato, il bot invierà il messaggio "I'm sorry we're closed" (Spiacenti il negozio è chiuso) e terminerà la sessione.

#### <a name="scenario-2-store-is-open"></a>Scenario 2: il negozio è aperto.
1. Immettere "open store".  `isOpen` verrà impostato su true.
1. Immettere "shop".
1. Immettere "hello".  Poiché il valore `isOpen` è impostato su true, il bot invierà il messaggio "What would you like to buy?" (Cosa si desidera acquistare?)
1. Immettere "chair". "chair" verrà salvato nella memoria CL come entità `purchaseItem`. Viene richiamato il callback di fine sessione che copia questo valore nell'archivio delle conversazioni.
1. Immettere "history".  Il bot invierà il messaggio "You bought chair" (Hai acquistato una sedia) perché questo era l'ultimo `purchaseItem`.

## <a name="conclusion"></a>Conclusioni

Con le nozioni apprese è possibile combinare lo strumento Conversation Learner con qualsiasi altra tecnologia per la compilazione bot.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creazione di rami e annullamento](./17-branch-undo.md)
