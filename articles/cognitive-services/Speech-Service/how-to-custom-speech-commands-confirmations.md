---
title: 'Procedura: aggiungere una conferma a un comando personalizzato (anteprima)How to: Add a confirmation to a custom command (Preview)'
titleSuffix: Azure Cognitive Services
description: In questo articolo, come implementare le conferme per un comando nei comandi personalizzati.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: afa197c83b4f66f12863de4185ef7763447f3ed9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75456498"
---
# <a name="how-to-add-a-confirmation-to-a-custom-command-preview"></a>Procedura: aggiungere una conferma a un comando personalizzato (anteprima)How To: Add a confirmation to a Custom Command (Preview)

In questo articolo verrà illustrato come aggiungere una conferma a un comando.

## <a name="prerequisites"></a>Prerequisiti

È necessario aver completato i passaggi nei seguenti articoli:

- [Guida introduttiva: Creare un comando personalizzato (anteprima)Quickstart: Create a Custom Command (Preview)](./quickstart-custom-speech-commands-create-new.md)
- [Guida introduttiva: Creare un comando personalizzato con parametri (anteprima)Quickstart: Create a Custom Command with Parameters (Preview)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-setalarm-command"></a>Creare un comando SetAlarmCreate a SetAlarm command

Per illustrare le convalide, creiamo un nuovo comando che consente all'utente di impostare un allarme.

1. Aprire l'applicazione Comandi personalizzati creata in precedenza in [Speech StudioOpen](https://speech.microsoft.com/) your previously created Custom Commands application in Speech Studio
1. Creare un nuovo comando **SetAlarmCreate** a new Command SetAlarm
1. Aggiungere un parametro denominato DateTimeAdd a parameter called DateTime

   | Impostazione           | Valore consigliato                                          | Descrizione                                                                                      |
   | ----------------- | ---------------------------------------------------------| ------------------------------------------------------------------------------------------------ |
   | Nome              | Datetime                                                 | Nome descrittivo per il parametro Command                                                    |
   | Obbligatoria          | true                                                     | Casella di controllo che indica se è necessario un valore per questo parametro prima di completare il comando |
   | Modello di risposta | "- A che ora?"                                           | Un prompt per richiedere il valore di questo parametro quando non è noto                              |
   | Type              | Datetime                                                 | Il tipo di parametro, ad esempio Number, String o Date Time                                      |
   | Valori predefiniti data     | Se la data non è l'uso oggi                             |                                                                                                  |
   | Impostazioni predefinite ora     | Se manca l'ora di inizio della giornata                      |                                                                                                  | 

1. Aggiungere alcune frasi di esempio
   
   ```
    set an alarm for {DateTime}
    set alarm {DateTime}
    alarm for {DateTime}
   ```

1. Aggiungere una regola di completamento per confermare il risultatoAdd a Completion rule to confirm result

   | Impostazione    | Valore consigliato                                         | Descrizione                                        |
   | ---------- | ------------------------------------------------------- | -------------------------------------------------- |
   | Nome regola  | Impostare l'allarme                                               | Un nome che descriva lo scopo della regola          |
   | Azioni    | SpeechResponse - "- Ok, allarme impostato per "DateTime"       | Azione da eseguire quando la condizione della regola è vera |

## <a name="try-it-out"></a>Provare il servizio

Selezionate il pannello Test e provate alcune interazioni.

- Ingresso: Impostare l'allarme per domani a mezzogiorno
- Uscita: "Ok, allarme impostato per 12/06/2019 12:00:00"

- Ingresso: Impostare un allarme
- Uscita: "A che ora?"
- Ingresso: 17:00
- Uscita: "Ok, allarme impostato per 12/05/2019 17:00:00"

## <a name="add-the-advanced-rules-for-confirmation"></a>Aggiungere le regole avanzate per la conferma

1. Aggiungere una regola avanzata per la conferma. 

    Questa regola chiederà all'utente di confermare la data e l'ora dell'allarme e si aspetta una conferma (sì/no) per il turno successivo.

   | Impostazione               | Valore consigliato                                                                  | Descrizione                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nome regola             | Conferma data ora                                                                | Un nome che descriva lo scopo della regola          |
   | Condizioni            | Parametro obbligatorio - DateTimeRequired Parameter - DateTime                                                    | Condizioni che determinano quando la regola può essere eseguita    |   
   | Azioni               | SpeechResponse - "- Sei sicuro di voler impostare un allarme per "DateTime"?"       | Azione da eseguire quando la condizione della regola è vera |
   | Stato dopo l'esecuzione | Attendere l'input                                                                   | Stato per l'utente dopo il turno                  |
   | Aspettative          | Conferma                                                                     | Aspettativa per il prossimo turno                      |

1. Aggiungere una regola avanzata per gestire una conferma riuscita (l'utente ha detto sì)

   | Impostazione               | Valore consigliato                                                                  | Descrizione                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nome regola             | Conferma accettata                                                            | Un nome che descriva lo scopo della regola          |
   | Condizioni            | SetSuccessfulConfirmation & parametro obbligatorio - DateTime                           | Condizioni che determinano quando la regola può essere eseguita    |   
   | Stato dopo l'esecuzione | Pronto per il completamento                                                             | Stato dell'utente dopo il turno                   |

1. Aggiungere una regola avanzata per gestire una conferma negata (l'utente ha detto di no)Add an advanced rule to handle a confirmation denied (user said no)

   | Impostazione               | Valore consigliato                                                                  | Descrizione                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nome regola             | Conferma negata                                                                   | Un nome che descriva lo scopo della regola          |
   | Condizioni            | DeniedConfirmation & Required Parameter - DateTime                               | Condizioni che determinano quando la regola può essere eseguita    |   
   | Azioni               | ClearParameter - DateTime & SpeechResponse - "- Nessun problema, a che ora?"     | Azione da eseguire quando la condizione della regola è vera |
   | Stato dopo l'esecuzione | Attendere l'input                                                                   | Stato dell'utente dopo il turno                   |
   | Aspettative          | ElicitParameters - DateTime                                                      | Aspettativa per il prossimo turno                      |

## <a name="try-it-out"></a>Provare il servizio

Selezionate il pannello Test e provate alcune interazioni.

- Ingresso: Impostare l'allarme per domani a mezzogiorno
- Uscita: "Sei sicuro di voler impostare un allarme per 12/07/2019 12:00:00?"
- Ingresso: No
- Uscita: "Nessun problema, a che ora?"
- Ingresso: 17:00
- Uscita: "Sei sicuro di voler impostare un allarme per 12/06/2019 17:00:00?"
- Ingresso: Sì
- Uscita: "Ok, allarme impostato per 12/06/2019 17:00:00"

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedura: Aggiungere una correzione in un solo passaggio a un comando personalizzato (anteprima)How To: Add a one-step correction to a Custom Command (Preview)](./how-to-custom-speech-commands-one-step-correction.md)