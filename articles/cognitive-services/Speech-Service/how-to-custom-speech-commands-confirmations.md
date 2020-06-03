---
title: Aggiungere conferme in un comando personalizzato anteprima servizio app-riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come aggiungere conferme ai comandi in un'app Custom Commands Preview.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 1cb0624012b22b6cae2c98bfa6ddc9495e09615d
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310469"
---
# <a name="add-confirmations-to-a-command-in-a-custom-commands-preview-application"></a>Aggiungere conferme a un comando in un'applicazione di anteprima comandi personalizzati

In questo articolo si apprenderà come creare conferme per i comandi in un'app di anteprima dei comandi personalizzati.

## <a name="prerequisites"></a>Prerequisiti

Completare i passaggi descritti negli articoli seguenti:
> [!div class="checklist"]
> * [Guida introduttiva: creare un'app di anteprima per i comandi personalizzati](./quickstart-custom-speech-commands-create-new.md)
> * [Guida introduttiva: creare un'app di anteprima dei comandi personalizzati con i parametri](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-setalarm-command"></a>Creare un comando sealarm

Per illustrare le conferme, creare un nuovo comando che imposta un allarme.

1. In [speech studio](https://speech.microsoft.com/)aprire l'app Custom Commands Preview creata.
1. Creare un nuovo comando **sealarm** .
1. Aggiungere un parametro **DateTime** con la seguente configurazione:

   | Impostazione                           | Valore consigliato                     |  Descrizione                 |
   | --------------------------------- | -----------------------------------------------------| ------------|
   | **Nome**                              | **DateTime**                                | Nome descrittivo per il parametro                                |
   | **Richiesto**                          | Selezionato                                 | Casella di controllo che indica se è necessario un valore per questo parametro prima di completare il comando |
   | **Risposta per un parametro obbligatorio**   | **Editor semplice-> il tempo?**                              | Messaggio di richiesta per richiedere il valore di questo parametro quando non è noto |
   | **Type**                              | **DateTime**                                | Tipo di parametro, ad esempio Number, String, DateTime o geography   |
   | **Impostazioni predefinite data**                     | Se la data non è presente, usare la data odierna            | Valore predefinito della variabile da usare se non fornito dall'utente  |  
   | **Impostazioni predefinite temporali**                     | Se manca l'ora, usare l'inizio della giornata     |  Valore predefinito della variabile da usare se non fornito dall'utente|

1. Aggiungere alcune frasi di esempio.
   
    ```
    set an alarm for {DateTime}
    set alarm {DateTime}
    alarm for {DateTime}
   ```

1. Aggiungere una regola di completamento per confermare il risultato. Usare la configurazione seguente:

   | Impostazione    | Valore consigliato                               |Descrizione                                     |
   | ---------- | ------------------------------------------------------- |-----|
   | **Nome regola**  | **Imposta allarme**                                               |    Nome che descrive lo scopo della regola |
   | **Actions**    | **Invia risposta vocale-> OK, set di avvisi per {DateTime}**    |Azione da eseguire quando la condizione della regola è true

## <a name="try-it-out"></a>Procedura

1. Selezionare **Train** nella parte superiore del riquadro destro.

1. Al termine del training, selezionare **test**, quindi provare le interazioni seguenti:
    - Input: impostare la sveglia per domani a mezzogiorno
    - Output: OK, set di avvisi per 2020-05-02 12:00:00
    - Input: impostare un allarme
    - Output: ora
    - Input: 17.00
    - Output: OK, set di avvisi per 2020-05-01 17:00:00

## <a name="add-interaction-rules-for-the-confirmation"></a>Aggiungere regole di interazione per la conferma

Creazione di conferme mediante l'aggiunta di regole di interazione.

1. Nel comando **sealarm** selezionare **Aggiungi** nel riquadro centrale e quindi selezionare **regole di interazione**  >  **conferma comando**.

    Questa regola chiede all'utente di confermare la data e l'ora dell'allarme. Usare queste impostazioni:

   | Impostazione               | Valore consigliato                                                                  | Descrizione                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | **Nome regola**             | **Conferma data e ora**                                                                | Nome che descrive lo scopo della regola          |
   | **Condizioni**            | **Parametro obbligatorio-> DateTime**                                                    | Condizioni che determinano quando la regola può essere eseguita    |   
   | **Actions**               | **Inviare risposta vocale-> si desidera impostare un allarme per {DateTime}?**     | Azione da eseguire quando la condizione della regola è true |
   | **Aspettative**          | **Prevista conferma dall'utente**                                                 | Previsione per il prossimo turno                      |
   | **Stato di post-esecuzione**  | **Attendi l'input dell'utente**                                                            | Stato dell'utente dopo il turno                  |
  
      > [!div class="mx-imgBorder"]
      > ![Crea risposta parametro obbligatoria](media/custom-speech-commands/add-validation-set-temperature.png)

1. Aggiungere una regola di interazione per una conferma accettata (l'utente ha detto "Yes"). Usare la configurazione seguente:

   | Impostazione               | Valore consigliato                                                                  | Descrizione                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | **Nome regola**             | **Conferma accettata**                                                            | Nome che descrive lo scopo della regola          |
   | **Condizioni**            | **Conferma completata & parametro obbligatorio-> DateTime**                      | Condizioni che determinano quando la regola può essere eseguita    |   
   | **Stato di post-esecuzione** | **Eseguire le regole di completamento**                                                          | Stato dell'utente dopo il turno                   |

1. Aggiungere una regola di interazione per una conferma negata (l'utente ha detto "No"). Usare la configurazione seguente:

   | Impostazione               | Valore consigliato                                                                  | Descrizione                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | **Nome regola**             | **Conferma negata**                                                                   | Nome che descrive lo scopo della regola          |
   | **Condizioni**            | **La conferma è stata negata & parametro obbligatorio-> DateTime**                               | Condizioni che determinano quando la regola può essere eseguita    |   
   | **Actions**               | **Cancellare il valore del parametro-> DateTime & inviare risposta vocale-> nessun problema, quale ora?**  | Azione da eseguire quando la condizione della regola è true |
   | **Stato dopo l'esecuzione** | **Attendere l'input**                                                                   | Stato dell'utente dopo il turno                   |
   | **Aspettative**          | **Prevista immissione parametri dall'utente-> DateTime**                           | Previsione per il prossimo turno                      |

## <a name="try-out-the-changes"></a>Prova le modifiche

1. Selezionare il pulsante **Train** (Esegui il training).

1. Al termine del training, selezionare **test**, quindi provare le interazioni seguenti:

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
> [Aggiungere una correzione in un passaggio a un comando in un'app di anteprima di comandi personalizzati](./how-to-custom-speech-commands-one-step-correction.md)
