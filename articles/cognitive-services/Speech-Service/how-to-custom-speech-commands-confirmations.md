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
ms.openlocfilehash: afa197c83b4f66f12863de4185ef7763447f3ed9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456498"
---
# <a name="how-to-add-a-confirmation-to-a-custom-command-preview"></a>Procedura: aggiungere una conferma a un comando personalizzato (anteprima)

In questo articolo si apprenderà come aggiungere una conferma a un comando.

## <a name="prerequisites"></a>Prerequisiti

È necessario avere completato i passaggi descritti negli articoli seguenti:

- [Guida introduttiva: creare un comando personalizzato (anteprima)](./quickstart-custom-speech-commands-create-new.md)
- [Guida introduttiva: creare un comando personalizzato con parametri (anteprima)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-setalarm-command"></a>Creare un comando sealarm

Per dimostrare le convalide, viene creato un nuovo comando che consente all'utente di impostare un allarme.

1. Aprire l'applicazione comandi personalizzati creati in precedenza in [speech studio](https://speech.microsoft.com/)
1. Crea un nuovo comando **Sealarm**
1. Aggiungere un parametro denominato DateTime

   | Impostazione           | Valore consigliato                                          | Description                                                                                      |
   | ----------------- | ---------------------------------------------------------| ------------------------------------------------------------------------------------------------ |
   | Nome              | Data e ora                                                 | Nome descrittivo per il parametro Command                                                    |
   | Obbligatorio          | true                                                     | Casella di controllo che indica se è necessario un valore per questo parametro prima di completare il comando |
   | Modello di risposta | "-Che tempo?"                                           | Messaggio di richiesta per richiedere il valore di questo parametro quando non è noto                              |
   | Tipo              | Data e ora                                                 | Tipo di parametro, ad esempio numero, stringa o data e ora                                      |
   | Impostazioni predefinite data     | Se la data non è presente, usare oggi                             |                                                                                                  |
   | Impostazioni predefinite temporali     | Se l'ora non è presente, usare l'inizio della giornata                      |                                                                                                  | 

1. Aggiungere alcune frasi di esempio
   
   ```
    set an alarm for {DateTime}
    set alarm {DateTime}
    alarm for {DateTime}
   ```

1. Aggiungere una regola di completamento per confermare il risultato

   | Impostazione    | Valore consigliato                                         | Description                                        |
   | ---------- | ------------------------------------------------------- | -------------------------------------------------- |
   | Nome regola  | Imposta allarme                                               | Nome che descrive lo scopo della regola          |
   | Azioni    | SpeechResponse-"-OK, set di avvisi per {DateTime}"       | Azione da eseguire quando la condizione della regola è true |

## <a name="try-it-out"></a>Prova

Selezionare il pannello test e provare alcune interazioni.

- Input: impostare la sveglia per domani a mezzogiorno
- Output: "OK, set di avvisi per 12/06/2019 12:00:00"

- Input: impostare un allarme
- Output: "ora?"
- Input: 17.00
- Output: "OK, set di avvisi per 12/05/2019 17:00:00"

## <a name="add-the-advanced-rules-for-confirmation"></a>Aggiungere le regole avanzate per la conferma

1. Aggiungere una regola avanzata per la conferma. 

    Questa regola chiederà all'utente di confermare la data e l'ora dell'allarme ed è prevista una conferma (sì/no) per la prossima volta.

   | Impostazione               | Valore consigliato                                                                  | Description                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nome regola             | Conferma data e ora                                                                | Nome che descrive lo scopo della regola          |
   | Condizioni            | Parametro obbligatorio-DateTime                                                    | Condizioni che determinano quando la regola può essere eseguita    |   
   | Azioni               | SpeechResponse-"-si vuole impostare un allarme per {DateTime}?"       | Azione da eseguire quando la condizione della regola è true |
   | Stato dopo l'esecuzione | Attendere l'input                                                                   | Stato dell'utente dopo il turno                  |
   | Aspettative          | Conferma                                                                     | Previsione per il prossimo turno                      |

1. Aggiungere una regola avanzata per gestire una conferma riuscita (utente detto sì)

   | Impostazione               | Valore consigliato                                                                  | Description                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nome regola             | Conferma accettata                                                            | Nome che descrive lo scopo della regola          |
   | Condizioni            | SuccessfulConfirmation & parametro obbligatorio-DateTime                           | Condizioni che determinano quando la regola può essere eseguita    |   
   | Stato dopo l'esecuzione | Pronto per il completamento                                                             | Stato dell'utente dopo il turno                   |

1. Aggiungere una regola avanzata per gestire una conferma negata (l'utente ha detto no)

   | Impostazione               | Valore consigliato                                                                  | Description                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nome regola             | Conferma negata                                                                   | Nome che descrive lo scopo della regola          |
   | Condizioni            | DeniedConfirmation & parametro obbligatorio-DateTime                               | Condizioni che determinano quando la regola può essere eseguita    |   
   | Azioni               | ClearParameter-DateTime & SpeechResponse-"-nessun problema, quale ora?"     | Azione da eseguire quando la condizione della regola è true |
   | Stato dopo l'esecuzione | Attendere l'input                                                                   | Stato dell'utente dopo il turno                   |
   | Aspettative          | ElicitParameters-DateTime                                                      | Previsione per il prossimo turno                      |

## <a name="try-it-out"></a>Prova

Selezionare il pannello test e provare alcune interazioni.

- Input: impostare la sveglia per domani a mezzogiorno
- Output: "impostare un allarme per 12/07/2019 12:00:00?"
- Input: No
- Output: "nessun problema, quale ora?"
- Input: 17.00
- Output: "impostare un allarme per 12/06/2019 17:00:00?"
- Input: Sì
- Output: "OK, set di avvisi per 12/06/2019 17:00:00"

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedura: aggiungere una correzione in un passaggio a un comando personalizzato (anteprima)](./how-to-custom-speech-commands-one-step-correction.md)