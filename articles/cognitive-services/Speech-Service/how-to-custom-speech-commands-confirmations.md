---
title: 'Procedura: aggiungere una conferma a un comando personalizzato (anteprima)'
titleSuffix: Azure Cognitive Services
description: In questo articolo viene illustrato come implementare le conferme per un comando nei comandi personalizzati.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: bf1b79c1b5d7b9dfd93b354c6b6ff5a512bb74a5
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858219"
---
# <a name="how-to-add-a-confirmation-to-a-custom-command-preview"></a>Procedura: aggiungere una conferma a un comando personalizzato (anteprima)

In questo articolo si apprenderà come aggiungere una conferma a un comando.

## <a name="prerequisites"></a>Prerequisiti

È necessario avere completato i passaggi descritti negli articoli seguenti:
> [!div class="checklist"]
> *  [Guida introduttiva: creare un comando personalizzato (anteprima)](./quickstart-custom-speech-commands-create-new.md)
> * [Guida introduttiva: creare un comando personalizzato con parametri (anteprima)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-setalarm-command"></a>Creare un comando sealarm

Per illustrare le conferme, viene creato un nuovo comando che consente all'utente di impostare un allarme.

1. Aprire l'applicazione comandi personalizzati creati in precedenza in [speech studio](https://speech.microsoft.com/).
1. Creare un nuovo comando `SetAlarm`.
1. Aggiungere un parametro denominato `DateTime` con la configurazione seguente.

   | Impostazione                           | Valore consigliato                     |  Descrizione                 |
   | --------------------------------- | -----------------------------------------------------| ------------|
   | Nome                              | Datetime                                | Nome descrittivo per il parametro                                |
   | Obbligatoria                          | checked                                 | Casella di controllo che indica se è necessario un valore per questo parametro prima di completare il comando |
   | Risposta per il parametro obbligatorio   | Editor semplice-> il tempo?                              | Messaggio di richiesta per richiedere il valore di questo parametro quando non è noto |
   | Type                              | Datetime                                | Tipo di parametro, ad esempio numero, stringa, data/ora o geografia   |
   | Impostazioni predefinite data                     | Se la data non è presente, usare oggi            | Valore predefinito della variabile da utilizzare se non fornito dall'utente.  |  
   | Impostazioni predefinite temporali                     | Se l'ora non è presente, usare l'inizio della giornata     |  Valore predefinito della variabile da utilizzare se non fornito dall'utente.|

1. Aggiungere alcune frasi di esempio.
   
    ```
    set an alarm for {DateTime}
    set alarm {DateTime}
    alarm for {DateTime}
   ```

1. Aggiungere una regola di completamento per confermare il risultato.

   | Impostazione    | Valore consigliato                               |Descrizione                                     |
   | ---------- | ------------------------------------------------------- |-----|
   | Nome regola  | Imposta allarme                                               |    Nome che descrive lo scopo della regola |
   | Azioni    | Invia risposta vocale-OK, set di avvisi per {DateTime} "    |Azione da eseguire quando la condizione della regola è true

## <a name="try-it-out"></a>Procedura

1. Selezionare `Train` l'icona presente nella parte superiore del riquadro destro.

1. Al termine del training, selezionare `Test`.
    - Input: impostare la sveglia per domani a mezzogiorno
    - Output: OK, set di avvisi per 2020-05-02 12:00:00
    - Input: impostare un allarme
    - Output: ora
    - Input: 17.00
    - Output: OK, set di avvisi per 2020-05-01 17:00:00

## <a name="add-the-advanced-rules-for-confirmation"></a>Aggiungere le regole avanzate per la conferma

Vengono convalidate tramite l'aggiunta di regole di interazione.

1. Nel comando esistente `SetAlarm` aggiungere una regola di **interazione** per icona di `+Add` selezione nel riquadro centrale e quindi selezionare **regole** -> di interazione**conferma comando**.

    Questa regola chiederà all'utente di confermare la data e l'ora dell'allarme ed è prevista una conferma (sì/no) per la prossima volta.

   | Impostazione               | Valore consigliato                                                                  | Descrizione                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nome regola             | Conferma data e ora                                                                | Nome che descrive lo scopo della regola          |
   | Condizioni            | Parametro obbligatorio-> DateTime                                                    | Condizioni che determinano quando la regola può essere eseguita    |   
   | Azioni               | Inviare risposta vocale-> si desidera impostare un allarme per {DateTime}?     | Azione da eseguire quando la condizione della regola è true |
   | Aspettative          | Prevista conferma dall'utente                                                 | Previsione per il prossimo turno                      |
   | Stato di post-esecuzione  | Attendi l'input dell'utente                                                            | Stato dell'utente dopo il turno                  |
  
      > [!div class="mx-imgBorder"]
      > ![Crea risposta parametro obbligatoria](media/custom-speech-commands/add-validation-set-temperature.png)

1. Aggiungere un'altra regola di interazione per gestire una conferma riuscita (utente detto sì)

   | Impostazione               | Valore consigliato                                                                  | Descrizione                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nome regola             | Conferma accettata                                                            | Nome che descrive lo scopo della regola          |
   | Condizioni            | Conferma completata & parametro obbligatorio-> DateTime                      | Condizioni che determinano quando la regola può essere eseguita    |   
   | Stato di post-esecuzione | Eseguire le regole di completamento                                                          | Stato dell'utente dopo il turno                   |

1. Aggiungere una regola avanzata per gestire una conferma negata (l'utente ha detto no)

   | Impostazione               | Valore consigliato                                                                  | Descrizione                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nome regola             | Conferma negata                                                                   | Nome che descrive lo scopo della regola          |
   | Condizioni            | La conferma è stata negata & parametro obbligatorio-> DateTime                               | Condizioni che determinano quando la regola può essere eseguita    |   
   | Azioni               | Cancellare il valore del parametro-> DateTime & inviare risposta vocale-> nessun problema, quale ora?  | Azione da eseguire quando la condizione della regola è true |
   | Stato dopo l'esecuzione | Attendere l'input                                                                   | Stato dell'utente dopo il turno                   |
   | Aspettative          | Previsto input/i parametri dall'utente-> DateTime                           | Previsione per il prossimo turno                      |

## <a name="try-out-the-changes"></a>Prova le modifiche

Selezionare `Train`, attendere il completamento del training e `Test`selezionare.

- Input: impostare la sveglia per domani a mezzogiorno
- Output: impostare un allarme per 2020-05-02 12:00:00?
- Input: No
- Output: nessun problema, quale ora?
- Input: 17.00
- Output: "impostare un allarme per 2020-05-01 17:00:00?"
- Input: Sì
- Output: OK, set di avvisi per 2020-05-01 17:00:00

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedura: aggiungere una correzione in un passaggio a un comando personalizzato (anteprima)](./how-to-custom-speech-commands-one-step-correction.md)