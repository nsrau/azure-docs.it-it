---
title: "Procedura: creare un'applicazione con comandi semplici-servizio di riconoscimento vocale"
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come creare e testare un'applicazione di comandi personalizzati ospitati usando semplici comandi.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: d166257dd28773d89a4f1fd56de3cb1a22242523
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87284167"
---
# <a name="create-application-with-simple-commands"></a>Creare un'applicazione con comandi semplici

In questo articolo vengono illustrate le operazioni seguenti:
 - Creare un'applicazione vuota
 - Aggiornare le risorse LUIS
 - Aggiungere alcuni comandi di base all'applicazione comandi personalizzati

## <a name="create-empty-application"></a>Crea applicazione vuota
Creare un'applicazione di comandi personalizzata vuota. Per informazioni dettagliate, vedere la [Guida introduttiva](quickstart-custom-commands-application.md). Questa volta, invece di importare un progetto, viene creato un progetto vuoto.

1. Nella casella **nome** immettere il nome `Smart-Room-Lite` del progetto (o un altro elemento di propria scelta).
1. Nell'elenco **lingua** selezionare **inglese (Stati Uniti)**.
1. Selezionare o creare una risorsa LUIS di propria scelta.

   > [!div class="mx-imgBorder"]
   > ![Creare un progetto](media/custom-commands/create-new-project.png)

## <a name="update-luis-resources-optional"></a>Aggiornare le risorse LUIS (facoltativo)

È possibile aggiornare la risorsa di creazione selezionata nella finestra **nuovo progetto** e impostare una risorsa di stima. La risorsa di stima viene utilizzata per il riconoscimento quando viene pubblicata l'applicazione dei comandi personalizzati. Non è necessaria una risorsa di stima durante le fasi di sviluppo e test.

## <a name="add-turnon-command"></a>Aggiungi comando accendere

Nell'applicazione dei comandi personalizzati **Smart Room-Lite** vuota appena creata, aggiungere un semplice comando che elabora un enunciato, `turn on the tv` e risponde con il messaggio `Ok, turning the tv on` .

1. Creare un nuovo comando selezionando **nuovo comando** nella parte superiore del riquadro sinistro. Verrà visualizzata la finestra **nuovo comando** .
1. Specificare il valore per il campo **nome** come **accendere**.
1. Selezionare **Crea**.

Il riquadro centrale elenca le diverse proprietà del comando. È possibile configurare le proprietà seguenti del comando. Per la spiegazione di tutte le proprietà di configurazione di un comando, vedere [riferimenti](./custom-commands-references.md).

| Configurazione            | Descrizione                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Frasi di esempio** | Espressioni di esempio che l'utente può pronunciare per attivare questo comando                                                                 |
| **Parameters**       | Informazioni necessarie per completare il comando                                                                                |
| **Regole di completamento** | Azioni da intraprendere per completare il comando. Ad esempio, per rispondere all'utente o comunicare con un altro servizio Web. |
| **Regole di interazione**   | Regole aggiuntive per la gestione di situazioni più specifiche o complesse                                                              |


> [!div class="mx-imgBorder"]
> ![Creazione di un comando](media/custom-commands/add-new-command.png)

### <a name="add-example-sentences"></a>Aggiungi frasi di esempio

Si inizierà con la sezione **frasi di esempio** e si fornirà un esempio di ciò che l'utente può pronunciare.

1. Selezionare la sezione **frasi di esempio** nel riquadro centrale.
1. Nel riquadro più a destra aggiungere esempi:

    ```
    turn on the tv
    ```

1.  Selezionare **Salva** nella parte superiore del riquadro.

Per il momento non sono disponibili parametri, quindi è possibile passare alla sezione **regole di completamento** .

### <a name="add-a-completion-rule"></a>Aggiungere una regola di completamento

Successivamente, il comando deve avere una regola di completamento. Questa regola indica all'utente che viene eseguita un'azione di evasione. Per altre informazioni sulle regole e sulle regole di completamento, vedere [riferimenti](./custom-commands-references.md).

1. Selezionare la regola di completamento predefinita **eseguita** e modificarla come indicato di seguito:

    
    | Impostazione    | Valore consigliato                          | Descrizione                                        |
    | ---------- | ---------------------------------------- | -------------------------------------------------- |
    | **Nome**       | ConfirmationResponse                  | Nome che descrive lo scopo della regola          |
    | **Condizioni** | Nessuno                                     | Condizioni che determinano quando la regola può essere eseguita    |
    | **Actions**    | Inviare risposta vocale > semplice editor > prima variazione > `Ok, turning the tv on` | Azione da eseguire quando la condizione della regola è true |
    



   > [!div class="mx-imgBorder"]
   > ![Creare una risposta vocale](media/custom-commands/create-speech-response-action.png)

1. Selezionare **Save (Salva** ) per salvare l'azione.
1. Nella sezione **regole di completamento** selezionare **Salva** per salvare tutte le modifiche. 


    > [!NOTE]
    > Non è necessario usare la regola di completamento predefinita fornita con il comando. Se necessario, è possibile eliminare la regola di completamento predefinita esistente e aggiungere una regola personalizzata.

### <a name="try-it-out"></a>Provare il servizio

Testare il comportamento usando il pannello test chat
1. Selezionare l'icona del **Training** presente nella parte superiore del riquadro destro.
1. Una volta completato il training, selezionare **test**. Provare l'espressione seguente tramite Voice/Text:
    - Input: accendere la TV
    - Output: OK, attivazione della TV


> [!div class="mx-imgBorder"]
> ![Test con web chat](media/custom-commands/create-basic-test-chat.png)

> [!TIP]
> Nel pannello test è possibile selezionare **Turn Details (dettagli** ) per informazioni su come è stato elaborato questo input voce/testo.  

## <a name="add-settemperature-command"></a>Aggiungi comando setemperature

A questo punto, aggiungere un altro comando **setemperature** che accettano un singolo enunciato, `set the temperature to 40 degrees` e rispondono con il messaggio `Ok, setting temperature to 40 degrees` .

Seguire i passaggi illustrati per il comando **accendere** per creare un nuovo comando usando la frase di esempio "**impostare la temperatura su 40 gradi**".

Modificare quindi le regole di completamento **eseguite** esistenti come indicato di seguito:

| Impostazione    | Valore consigliato                          |
| ---------- | ---------------------------------------- |
| Nome  | ConfirmationResponse                  |
| Condizioni | Nessuno                                     |
| Azioni    | Inviare risposta vocale > semplice editor > prima variazione > `Ok, setting temperature to 40 degrees` |

Selezionare **Save (Salva** ) per salvare tutte le modifiche apportate al comando.

## <a name="add-setalarm-command"></a>Aggiungi comando sealarm
Creare un nuovo comando **sealarm** usando la frase di esempio "**impostare un allarme per le 9.00 domani**". Modificare quindi le regole di completamento **eseguite** esistenti come indicato di seguito:

| Impostazione    | Valore consigliato                          |
| ---------- | ---------------------------------------- |
| Nome regola  | ConfirmationResponse                  |
| Condizioni | Nessuno                                     |
| Azioni    | Inviare risposta vocale > semplice editor > prima variazione >`Ok, setting an alarm for 9 am tomorrow` |

Selezionare **Save (Salva** ) per salvare tutte le modifiche apportate al comando.

## <a name="try-it-out"></a>Provare il servizio

Testare il comportamento usando il pannello test chat
1. Selezionare il pulsante **Train** (Esegui il training). Una volta completato il training, selezionare **test** e provare:
    - Digitare: impostare la temperatura su 40 gradi
    - Risposta prevista: OK, impostazione della temperatura su 40 gradi
    - Digitare: accendere la TV
    - Risposta prevista: OK, attivazione della TV
    - Digitare: impostare un allarme per le 09:00 domani
    - Risposta prevista: OK, impostazione di un allarme per le 09:00 domani

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedura: aggiungere parametri ai comandi](./how-to-custom-commands-add-parameters-to-commands.md)
