---
title: 'Procedura: aggiungere convalide a parametri di comando personalizzati (anteprima)'
titleSuffix: Azure Cognitive Services
description: In questo articolo, aggiungere convalide ai parametri di comando personalizzati
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: 64e092405686caca7baeaf58f19d577a3f80e169
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506934"
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
   > ![aggiungere un](media/custom-speech-commands/validations-add-temperature.png) di convalida dell'intervallo

   | Impostazione           | Valore consigliato                                          | Descrizione                                                                                      |
   | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
   | Name              | Temperatura                                              | Nome descrittivo per il parametro Command                                                    |
   | Obbligatorio          | true                                                     | Casella di controllo che indica se è necessario un valore per questo parametro prima di completare il comando |
   | Modello di risposta | "Quale temperatura desideri?"                       | Messaggio di richiesta per richiedere il valore di questo parametro quando non è noto                              |
   | Tipo              | Number                                                   | Tipo di parametro, ad esempio numero, stringa o data e ora                                      |
   | Convalida        | Valore minimo: 60, valore massimo: 80                             | Per i parametri number, l'intervallo di valori consentito per il parametro                              |
   | Modello di risposta | "Mi dispiace, posso impostare solo tra 60 e 80 gradi"        | Richiedi un valore aggiornato se la convalida ha esito negativo                                       |

1. Aggiungere alcune frasi di esempio

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Aggiungere una regola di completamento per confermare il risultato

   | Impostazione    | Valore consigliato                                         | Descrizione                                        |
   | ---------- | ------------------------------------------------------- | -------------------------------------------------- |
   | Nome regola  | Messaggio di conferma                                    | Nome che descrive lo scopo della regola          |
   | Condizioni | Parametro obbligatorio-temperatura                        | Condizioni che determinano quando la regola può essere eseguita    |
   | Azioni    | SpeechResponse-"OK, impostazione su {temperature} gradi" | Azione da eseguire quando la condizione della regola è true |

> [!TIP]
> In questo esempio viene utilizzata una risposta vocale per confermare il risultato. Per esempi sul completamento del comando con un'azione client, vedere [procedura: eseguire l'evasione dei comandi sul client con l'SDK di riconoscimento vocale (anteprima)](./how-to-custom-speech-commands-fulfill-sdk.md)

## <a name="try-it-out"></a>Prova

Selezionare il pannello test e provare alcune interazioni.

- Input: impostare la temperatura su 72 gradi
- Output: "OK, impostazione su 72 gradi"

- Input: impostare la temperatura su 45 gradi
- Output: "Mi dispiace, posso impostare solo tra 60 e 80 gradi"
- Input: renderlo 72 gradi
- Output: "OK, impostazione su 72 gradi"
