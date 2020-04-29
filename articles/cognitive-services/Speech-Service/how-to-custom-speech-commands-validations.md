---
title: 'Procedura: aggiungere convalide a parametri di comando personalizzati (anteprima)'
titleSuffix: Azure Cognitive Services
description: In questo articolo viene illustrato come aggiungere convalide a un parametro nei comandi personalizzati.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: cf6e4e4f0bfab43fb738f8415022e55fcbcbd05a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76156455"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>Procedura: aggiungere convalide a parametri di comando personalizzati (anteprima)

In questo articolo si apprenderà come aggiungere convalide ai parametri e richiedere la correzione.

## <a name="prerequisites"></a>Prerequisiti

È necessario avere completato i passaggi descritti negli articoli seguenti:

- [Guida introduttiva: creare un comando personalizzato (anteprima)](./quickstart-custom-speech-commands-create-new.md)
- [Guida introduttiva: creare un comando personalizzato con parametri (anteprima)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>Creare un comando setemperature

Per dimostrare le convalide, viene creato un nuovo comando che consente all'utente di impostare la temperatura.

1. Aprire l'applicazione comandi personalizzati creati in precedenza in [speech studio](https://speech.microsoft.com/)
1. Crea un nuovo comando **Setemperature**
1. Aggiungere un parametro per la temperatura di destinazione
1. Aggiungere una convalida per il parametro temperature
   > [!div class="mx-imgBorder"]
   > ![Aggiungere una convalida dell'intervallo](media/custom-speech-commands/validations-add-temperature.png)

   | Impostazione           | Valore consigliato                                          | Descrizione                                                                                      |
   | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
   | Nome              | Temperatura                                              | Nome descrittivo per il parametro Command                                                    |
   | Obbligatoria          | true                                                     | Casella di controllo che indica se è necessario un valore per questo parametro prima di completare il comando |
   | Modello di risposta | "-Quale temperatura desideri?"                     | Messaggio di richiesta per richiedere il valore di questo parametro quando non è noto                              |
   | Type              | Numero                                                   | Tipo di parametro, ad esempio numero, stringa o data e ora                                      |
   | Convalida        | Valore minimo: 60, valore massimo: 80                             | Per i parametri number, l'intervallo di valori consentito per il parametro                             |
   | Modello di risposta | "-Mi dispiace, posso impostare solo tra 60 e 80 gradi"      | Richiedi un valore aggiornato se la convalida ha esito negativo                                       |

1. Aggiungere alcune frasi di esempio

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Aggiungere una regola di completamento per confermare il risultato

   | Impostazione    | Valore consigliato                                           | Descrizione                                        |
   | ---------- | --------------------------------------------------------- | -------------------------------------------------- |
   | Nome regola  | Messaggio di conferma                                      | Nome che descrive lo scopo della regola          |
   | Condizioni | Parametro obbligatorio-temperatura                          | Condizioni che determinano quando la regola può essere eseguita    |
   | Azioni    | SpeechResponse-"-OK, impostazione su {temperature} gradi" | Azione da eseguire quando la condizione della regola è true |

> [!TIP]
> In questo esempio viene utilizzata una risposta vocale per confermare il risultato. Per esempi sul completamento del comando con un'azione client, vedere [procedura: eseguire l'evasione dei comandi sul client con l'SDK di riconoscimento vocale (anteprima)](./how-to-custom-speech-commands-fulfill-sdk.md)

## <a name="try-it-out"></a>Procedura

Selezionare il pannello test e provare alcune interazioni.

- Input: impostare la temperatura su 72 gradi
- Output: "OK, impostazione su 72 gradi"

- Input: impostare la temperatura su 45 gradi
- Output: "Mi dispiace, posso impostare solo tra 60 e 80 gradi"
- Input: renderlo 72 gradi
- Output: "OK, impostazione su 72 gradi"

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedura: aggiungere una conferma a un comando personalizzato (anteprima)](./how-to-custom-speech-commands-confirmations.md)
