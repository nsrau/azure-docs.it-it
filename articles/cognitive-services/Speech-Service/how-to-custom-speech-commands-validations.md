---
title: 'Procedura: aggiungere convalide ai parametri di comando personalizzato (anteprima)How To: Add convalids to Custom Command parameters (Preview)'
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76156455"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>Procedura: aggiungere convalide ai parametri di comando personalizzato (anteprima)How To: Add convalids to Custom Command parameters (Preview)

In questo articolo verrà illustrato come aggiungere convalide ai parametri e richiedere la correzione.

## <a name="prerequisites"></a>Prerequisiti

È necessario aver completato i passaggi nei seguenti articoli:

- [Guida introduttiva: Creare un comando personalizzato (anteprima)Quickstart: Create a Custom Command (Preview)](./quickstart-custom-speech-commands-create-new.md)
- [Guida introduttiva: Creare un comando personalizzato con parametri (anteprima)Quickstart: Create a Custom Command with Parameters (Preview)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>Creare un comando SetTemperatureCreate a SetTemperature Command

Per illustrare le convalide, è possibile creare un nuovo comando che consente all'utente di impostare la temperatura.

1. Aprire l'applicazione Comandi personalizzati creata in precedenza in [Speech StudioOpen](https://speech.microsoft.com/) your previously created Custom Commands application in Speech Studio
1. Creare un nuovo comando **SetTemperatureCreate** a new Command SetTemperature
1. Aggiungere un parametro per la temperatura di destinazione
1. Aggiungere una convalida per il parametro temperature
   > [!div class="mx-imgBorder"]
   > ![Aggiungere una convalida dell'intervalloAdd a range validation](media/custom-speech-commands/validations-add-temperature.png)

   | Impostazione           | Valore consigliato                                          | Descrizione                                                                                      |
   | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
   | Nome              | Temperatura                                              | Nome descrittivo per il parametro Command                                                    |
   | Obbligatoria          | true                                                     | Casella di controllo che indica se è necessario un valore per questo parametro prima di completare il comando |
   | Modello di risposta | "- Che temperatura vorresti?"                     | Un prompt per richiedere il valore di questo parametro quando non è noto                              |
   | Type              | Number                                                   | Il tipo di parametro, ad esempio Number, String o Date Time                                      |
   | Convalida        | Valore minimo: 60, Valore massimo: 80                             | Per i parametri Number, l'intervallo di valori consentito per il parametro                             |
   | Modello di risposta | "- Mi dispiace, posso impostare solo tra 60 e 80 gradi"      | Richiedi di richiedere un valore aggiornato se la convalida non riesce                                       |

1. Aggiungere alcune frasi di esempio

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Aggiungere una regola di completamento per confermare il risultatoAdd a Completion rule to confirm result

   | Impostazione    | Valore consigliato                                           | Descrizione                                        |
   | ---------- | --------------------------------------------------------- | -------------------------------------------------- |
   | Nome regola  | Messaggio di conferma                                      | Un nome che descriva lo scopo della regola          |
   | Condizioni | Parametro obbligatorio - Temperatura                          | Condizioni che determinano quando la regola può essere eseguita    |
   | Azioni    | SpeechResponse - "- Ok, impostando su gradi di temperatura" | Azione da eseguire quando la condizione della regola è vera |

> [!TIP]
> In questo esempio viene utilizzata una risposta vocale per confermare il risultato. Per esempi sul completamento del comando con un'azione client, vedere: Procedura: soddisfare i comandi nel client con Speech SDK (anteprima)For examples on completing the Command with a client action see: [How To: Fulfill Commands on the client with the Speech SDK (Preview)](./how-to-custom-speech-commands-fulfill-sdk.md)

## <a name="try-it-out"></a>Provare il servizio

Selezionate il pannello Test e provate alcune interazioni.

- Ingresso: Impostare la temperatura a 72 gradi
- Uscita: "Ok, impostando a 72 gradi"

- Ingresso: Impostare la temperatura a 45 gradi
- Uscita: "Mi dispiace, posso impostare solo tra 60 e 80 gradi"
- Ingresso: facciamo di 72 gradi invece
- Uscita: "Ok, impostando a 72 gradi"

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedura: aggiungere una conferma a un comando personalizzato (anteprima)How To: Add a confirmation to a Custom Command (Preview)](./how-to-custom-speech-commands-confirmations.md)
