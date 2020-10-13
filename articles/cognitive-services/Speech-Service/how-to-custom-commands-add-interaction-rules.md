---
title: 'Procedura: aggiungere una conferma a un comando personalizzato'
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come implementare le conferme per un comando nei comandi personalizzati.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 7d6c0928196c9e8e1abf6aa7f724a58753ce3d2a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87289046"
---
# <a name="add-interaction-rules"></a>Aggiungere regole di interazione

Questo articolo illustra **le regole di interazione**. Si tratta di regole aggiuntive per gestire situazioni più specifiche o complesse. Sebbene sia possibile creare regole di interazione personalizzate, in questo articolo si utilizzano le regole di interazione per gli scenari di destinazione seguenti:

* Conferma di comandi
* Aggiunta di una correzione in un passaggio ai comandi

Per altre informazioni sulle regole di interazione, vedere la sezione [riferimenti](./custom-commands-references.md) .

## <a name="prerequisites"></a>Prerequisiti

È necessario avere completato i passaggi descritti negli articoli seguenti:
> [!div class="checklist"]
> * [Procedura: creare un'applicazione con semplici comandi](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Procedura: aggiungere parametri ai comandi](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="add-confirmations-to-a-command"></a>Aggiungere conferme a un comando

Per aggiungere una conferma, utilizzare il comando **setemperature** . Per ottenere la conferma, è possibile creare regole di interazione attenendosi alla procedura seguente.

1. Selezionare il comando **setemperature** nel riquadro sinistro.
1. Aggiungere regole di interazione selezionando **Aggiungi** nel riquadro centrale. Quindi selezionare **Interaction rules**il  >  **comando conferma**regole di interazione.

    Questa azione aggiunge tre regole di interazione che chiederanno all'utente di confermare la data e l'ora dell'allarme ed è prevista una conferma (sì/no) per la prossima volta.

    1. Modificare la regola di interazione del **comando Confirm** in base alla configurazione seguente:
        1. Rinominare il **nome** per **confermare la temperatura**.
        1. Aggiungere una nuova condizione come temperatura dei **parametri obbligatori**  >  **Temperature**.
        1. Aggiungere una nuova azione come **tipo**  >  **Invia risposta vocale**  >  **impostare la temperatura come {temperatura} gradi?**
        1. Lasciare il valore predefinito della **conferma prevista dall'utente** nella sezione delle **aspettative** .
      
         > [!div class="mx-imgBorder"]
         > ![Crea risposta parametro obbligatoria](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. Modificare la regola di interazione **conferma riuscita** per gestire una conferma riuscita (utente detto sì).
      
          1. Modificare il **nome** in **temperatura di conferma completata**.
          1. Lasciare la condizione di **conferma** già esistente riuscita.
          1. Aggiungere una nuova condizione come **tipo**  >  **temperatura parametri richiesta**  >  **Temperature**.
          1. Lasciare il valore predefinito **stato post-esecuzione** come **regole di completamento esecuzione**.

    1. Modificare la regola di interazione di **conferma negata** per gestire gli scenari quando la conferma viene negata (l'utente ha detto no).

          1. Modificare il **nome** in **temperatura di conferma negata**.
          1. Lasciare la condizione di **conferma già esistente negata** .
          1. Aggiungere una nuova condizione come **tipo**  >  **temperatura parametri richiesta**  >  **Temperature**.
          1. Aggiungere una nuova azione come **tipo**  >  **Invia risposta vocale**  >  **nessun problema. Quale temperatura?**
          1. Lasciare il valore predefinito di **stato post-esecuzione** in **attesa dell'input dell'utente**.

> [!IMPORTANT]
> In questo articolo è stata usata la funzionalità di conferma predefinita. È anche possibile aggiungere manualmente le regole di interazione una alla volta.
   

### <a name="try-out-the-changes"></a>Prova le modifiche

Selezionare **Train**, attendere il completamento del training e selezionare **test**.

- **Input**: impostare la temperatura su 80 gradi
- **Output**: impostare la temperatura su 80 gradi?
- **Input**: No
- **Output**: nessun problema. Quale temperatura?
- **Input**: 72 gradi
- **Output**: impostare la temperatura su 72 gradi?
- **Input**: Sì
- **Output**: OK, impostazione della temperatura su 83 gradi


## <a name="implement-corrections-in-a-command"></a>Implementare le correzioni in un comando

In questa sezione viene configurata una correzione in un solo passaggio, che viene usata dopo che l'azione di evasione è già stata eseguita. Viene inoltre visualizzato un esempio di come una correzione è abilitata per impostazione predefinita, nel caso in cui il comando non venga ancora completato. Per aggiungere una correzione quando il comando non è stato completato, aggiungere il nuovo parametro **AlarmTone**.

Selezionare il comando **sealarm** dal riquadro sinistro e aggiungere il nuovo parametro **AlarmTone**.
        
- **Nome**  >  **AlarmTone**
- **Tipo**  >  di **Stringa** di
- **Valore predefinito**  >  **Chimes**
- **Configurazione**  >  di **Accetta valori di input predefiniti dal catalogo interno**
- Valori di input **predefiniti**  >  **Chimes**, **Jingle**e **echo** come singoli input predefiniti


Aggiornare quindi la risposta per il parametro **DateTime** per **impostare la sveglia con Tone As {AlarmTone}. Per quanto tempo?**. Modificare quindi la regola di completamento come indicato di seguito:

1. Selezionare la regola di completamento esistente **ConfirmationResponse**.
1. Nel riquadro destro passare il puntatore del mouse sull'azione esistente e selezionare **modifica**.
1. Aggiornare la risposta vocale a **OK, set di allarme per {DateTime}. Il tono di allarme è {AlarmTone}.**

### <a name="try-out-the-changes"></a>Prova le modifiche

Selezionare **Train**, attendere il completamento del training e selezionare **test**.
Provare le espressioni seguenti:

- **Input**: impostare un allarme.
- **Output**: pronto per impostare l'allarme con un segnale acustico. Per quanto tempo?
- **Input**: impostare un allarme con il tono come jingle per le 9.00 di domani.
- **Output**: OK, l'avviso è impostato su 2020-05-30 09:00:00. Il tono di allarme è Jingle.

> [!IMPORTANT]
> Il tono di allarme potrebbe essere modificato senza alcuna configurazione esplicita in un comando continuo, ad esempio quando il comando non è stato ancora completato. *Una correzione è abilitata per impostazione predefinita per tutti i parametri del comando, indipendentemente dal numero di turni se il comando è ancora da soddisfare.*

### <a name="correction-when-command-is-completed"></a>Correzione quando il comando viene completato

La piattaforma Custom Commands fornisce anche la funzionalità per la correzione di un solo passaggio anche quando il comando è stato completato. Questa funzionalità non è abilitata per impostazione predefinita. Deve essere configurata in modo esplicito. Usare la procedura seguente per configurare una correzione in un solo passaggio.

1. Nel comando **sealarm** aggiungere una regola di interazione del tipo **Aggiorna comando precedente** per aggiornare l'allarme impostato in precedenza. Rinominare il **nome** predefinito della regola di interazione per **aggiornare la sveglia precedente**.
1. Lascia la condizione predefinita il **comando precedente deve essere aggiornato** così com'è.
1. Aggiungere una nuova condizione come **tipo**di  >  **parametro**  >  **DateTime**obbligatorio.
1. Aggiungere una nuova azione come **tipo**  >  **Invia risposta vocale**  >  **semplice editor**che  >  **Aggiorna l'ora di allarme precedente a {DateTime}.**
1. Lasciare il valore predefinito **stato post-esecuzione** come **comando completato**.

### <a name="try-out-the-changes"></a>Prova le modifiche

Selezionare **Train**, attendere il completamento del training e selezionare **test**.

- **Input**: impostare un allarme.
- **Output**: pronto per impostare l'allarme con un segnale acustico. Per quanto tempo?
- **Input**: impostare un allarme con il tono come jingle per le 9.00 di domani.
- **Output**: OK, l'avviso è impostato su 2020-05-21 09:00:00. Il tono di allarme è Jingle.
- **Input**: No, 08:00.
- **Output**: aggiornamento dell'ora di allarme precedente a 2020-05-29 08:00.

> [!NOTE]
> In un'applicazione reale, nella sezione **azioni** di questa regola di correzione, sarà anche necessario restituire un'attività al client o chiamare un endpoint HTTP per aggiornare l'ora di allarme nel sistema. Questa azione deve essere responsabile solo dell'aggiornamento dell'ora di allarme e non di altri attributi del comando. In questo caso, si tratta del segnale acustico.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedura: aggiungere modelli di generazione della lingua per le risposte vocali](./how-to-custom-commands-add-language-generation-templates.md)
