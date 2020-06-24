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
ms.openlocfilehash: 5f66e29e4c1bc85981202251e0de8288f4baee4e
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85307838"
---
# <a name="add-interaction-rules"></a>Aggiungere regole di interazione

Questo articolo illustra **le regole di interazione**. Si tratta di regole aggiuntive per gestire situazioni più specifiche o complesse. Sebbene sia possibile creare regole di interazione personalizzate, in questo articolo si utilizzano le regole di interazione per gli scenari di destinazione seguenti:

* Conferma di comandi
* Aggiunta della correzione di un passaggio ai comandi

Per ulteriori informazioni sulle regole di interazione, vedere la sezione [riferimenti](./custom-commands-references.md) .

## <a name="prerequisites"></a>Prerequisiti

È necessario avere completato i passaggi descritti negli articoli seguenti:
> [!div class="checklist"]
> * [Procedura: creare un'applicazione con semplici comandi](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Procedura: aggiungere parametri ai comandi](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="add-confirmations-to-a-command"></a>Aggiungere conferme a un comando

Per aggiungere una conferma, utilizzare il comando **setemperature** . Per ottenere la conferma, è possibile creare regole di interazione attenendosi alla procedura seguente.

1. Selezionare il comando **setemperature** dal riquadro sinistro.
2. Aggiungere regole di interazione selezionando **Aggiungi** nel riquadro centrale, quindi selezionando **regole di interazione**  >  **confermare comando**.

    Questa operazione consente di aggiungere 3 regole di interazione. questa regola chiederà all'utente di confermare la data e l'ora dell'allarme ed è prevista una conferma (sì/no) per la prossima volta.

    1. Modificare la regola di interazione del **comando Confirm** in base alla configurazione seguente
        1. Rinominare il **nome** in **`Confirm Temperature`** .
        1. Aggiungere una nuova condizione come **parametri obbligatori > temperatura**
        1. Aggiungere una nuova azione come- **Type > inviare risposta vocale > `Are you sure you want to set the temperature as {Temperature} degrees?` **
        1. Lasciare il valore predefinito della **conferma prevista dall'utente** nella sezione delle aspettative.
      
         > [!div class="mx-imgBorder"]
         > ![Crea risposta parametro obbligatoria](media/custom-speech-commands/add-validation-set-temperature.png)
    

    1. Modificare la regola di interazione **conferma riuscita** per gestire una conferma riuscita (utente detto sì).
      
          1. Modificare il **nome** in **`Confirmation temperature succeeded`** .
          1. Lasciare la condizione di **conferma** già esistente riuscita.
          1. Aggiungere una nuova condizione come **tipo > parametri obbligatori > temperatura**
          1. Lasciare il valore predefinito **stato post-esecuzione** come **regole di completamento esecuzione**.

    1. Modificare la **conferma negata** (utente detto no) per gestire gli scenari quando la conferma viene negata.

          1. Modificare il **nome** in **`Confirmation temperature denied`** .
          1. Lasciare la condizione di **conferma già esistente negata** .
          1. Aggiungere una nuova condizione come **tipo > parametri obbligatori > temperatura**
          1. Aggiungere una nuova azione come- **Type > inviare risposta vocale > `No problem. What temperature then?` **
          1. Lasciare il valore predefinito di **stato post-esecuzione** in **attesa dell'input dell'utente**.

> [!IMPORTANT]
> In questo articolo è stata usata la funzionalità di conferma incorporata. In alternativa, è possibile ottenere lo stesso aggiungendo manualmente le regole di interazione, una alla volta.
   

### <a name="try-out-the-changes"></a>Prova le modifiche

Selezionare **Train**, attendi training completato e selezionare **test**.

- Input: impostare la temperatura su 80 gradi
- Output: OK 80?
- Input: No
- Output: nessun problema. quale temperatura?
- Input: 83 gradi
- Output: OK 83?
- Input: Sì
- Output: OK, impostazione della temperatura su 83 gradi


## <a name="implementing-corrections-in-a-command"></a>Implementazione di correzioni in un comando

In questa sezione viene configurata la correzione in un passaggio, che viene usata una volta che l'azione di evasione è già stata eseguita. Viene anche visualizzato un esempio di come la correzione sia abilitata per impostazione predefinita, nel caso in cui il comando non sia stato ancora completato. Per aggiungere una correzione quando il comando non è stato completato, aggiungere un nuovo parametro **AlarmTone**.

Selezionare il comando **sealarm** dal riquadro sinistro e aggiungere un nuovo parametro **AlarmTone**.
        
- **Nome** > `AlarmTone`
- **Digitare** > stringa
- **Valore predefinito** > `Chimes`
- La **configurazione** > accettare valori di input predefiniti dal catalogo interno
- **Valori di input predefiniti**  >  `Chimes` , `Jingle` e `Echo` . Ognuno come input predefiniti singoli.


Aggiornare quindi la risposta per il parametro DateTime a `Ready to set alarm with tone as {AlarmTone}. For what time?` . Modificare quindi la regola di completamento come indicato di seguito.

1. Selezionare la regola di completamento esistente **ConfirmationResponse**.
1. Nel riquadro destro passare il puntatore del mouse sull'azione esistente e selezionare **modifica** pulsante.
1. Aggiorna risposta vocale a`Ok, alarm set for {DateTime}. The alarm tone is {AlarmTone}.`

### <a name="try-out-the-changes"></a>Prova le modifiche

Selezionare **Train**, attendere il completamento del training e selezionare **test**.
Provare le espressioni seguenti:

- Input: impostare un allarme
- Output: pronto per impostare l'allarme con un segnale acustico. Per quanto tempo?
- Input: impostare un allarme con un tono come jingle per le 09:00 domani
- Output: OK, l'avviso è impostato su 2020-05-30 09:00:00. Il tono di allarme è Jingle.

> [!IMPORTANT]
> Si noti che il tono di allarme potrebbe essere modificato senza alcuna configurazione esplicita in un comando in corso, ad esempio quando il comando non è stato ancora completato. **Per impostazione predefinita, la correzione è abilitata per tutti i parametri del comando, indipendentemente dal numero di turni se il comando deve ancora essere adempiuto.**

### <a name="correction-when-command-is-completed"></a>Correzione quando il comando viene completato

La piattaforma Custom Commands fornisce anche la funzionalità di correzione in un passaggio anche quando il comando è stato completato. Questa funzionalità non è tuttavia abilitata per impostazione predefinita e deve essere configurata in modo esplicito. Usare la procedura seguente per configurare la correzione in un solo passaggio.

1. Nel comando **sealarm** aggiungere una regola di interazione di tipo **Aggiorna comando precedente** per aggiornare l'allarme impostato in precedenza. Rinominare il **nome** predefinito della regola di interazione per **aggiornare la sveglia precedente**.
1. Lascia la condizione predefinita il **comando precedente deve essere aggiornato** così com'è.
1.  Aggiungere una nuova condizione come **tipo > parametro obbligatorio > DateTime**.
1. Aggiungere una nuova azione come **tipo > inviare risposta vocale > semplice editor > `Updating previous alarm time to {DateTime}.` **
1. Lasciare il valore predefinito di post-esecuzione stato come **comando completato**.

### <a name="try-out-the-changes"></a>Prova le modifiche

Selezionare **Train**, attendi training completato e selezionare **test**.

- Input: impostare un allarme
- Output: pronto per impostare l'allarme con un segnale acustico. A che ora?
- Input: impostare un allarme con un tono come jingle per le 09:00 domani
- Output: OK, l'avviso è impostato su 2020-05-21 09:00:00. Il tono di allarme è Jingle.
- Input: No, 8.00
- Output: aggiornamento dell'ora di allarme precedente a 2020-05-29 08:00.

> [!NOTE]
> In un'applicazione reale, nella sezione azioni di questa regola di correzione, sarà anche necessario restituire un'attività al client o chiamare un endpoint HTTP per aggiornare l'ora di allarme nel sistema. Questa azione deve essere singolarmente responsabile della semplice aggiornamento dell'ora di allarme e non di qualsiasi altro attributo del comando, in questo caso un tono di allarme.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedura: aggiungere modelli di generazione della lingua per le risposte vocali](./how-to-custom-commands-add-language-generation-templates.md)