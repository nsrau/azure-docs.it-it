---
title: 'Procedura: aggiungere convalide a parametri di comando personalizzati'
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
ms.openlocfilehash: 2b7fd608156ab269cfc0c85c6c508fa9d5eebc83
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857209"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>Procedura: aggiungere convalide a parametri di comando personalizzati (anteprima)

In questo articolo si aggiungeranno le convalide ai parametri e verrà richiesto di eseguire la correzione.

## <a name="prerequisites"></a>Prerequisiti

È necessario avere completato i passaggi descritti negli articoli seguenti:

> [!div class="checklist"]
> * [Guida introduttiva: creare un comando personalizzato](./quickstart-custom-speech-commands-create-new.md)
> * [Guida introduttiva: creare un comando personalizzato con i parametri](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>Creare un comando setemperature

Per dimostrare le convalide, è possibile creare un nuovo comando che consente agli utenti di impostare la temperatura.

1. Aprire l'applicazione comandi personalizzati creati in precedenza in [speech studio](https://speech.microsoft.com/)
1. Crea un nuovo comando`SetTemperature`
1. Aggiungere un parametro per la temperatura di destinazione.

   | Configurazione parametri           | Valore consigliato    |Descrizione                 |                                    
   | ----------------- | ----------------------------------| -------------|
   | Nome              | Temperatura                       | Nome descrittivo per il parametro                                |
   | Obbligatoria          | checked                           | Casella di controllo che indica se è necessario un valore per questo parametro prima di completare il comando |
   | Risposta per il parametro obbligatorio     | Editor semplice: > la temperatura desiderata?  | Messaggio di richiesta per richiedere il valore di questo parametro quando non è noto |
   | Type              | Numero                            | Tipo di parametro, ad esempio numero, stringa, data/ora o geografia   |

1. Aggiungere una convalida per il parametro temperature.

    - Nella pagina configurazione **parametri** per `Temperature` il parametro, selezionare `Add a validation` una sezione convalida.
    - Inserire i valori nella finestra popup **nuova convalida** come indicato di seguito e selezionare **Crea**.

  
       | Configurazione parametri         | Valore consigliato                                          | Descrizione                                                                        |
       | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
       | Min Value (Valore minimo)        | 60               | Per i parametri number, il valore minimo che questo parametro può assumere |
       | Max Value (Valore massimo)        | 80               | Per i parametri number, il valore massimo che questo parametro può assumere |
       | Risposta di errore-editor semplice| Prima variazione: mi dispiace, posso impostare solo tra 60 e 80 gradi      | Richiedi un nuovo valore se la convalida ha esito negativo                                       |

       > [!div class="mx-imgBorder"]
       > ![Aggiungere una convalida dell'intervallo](media/custom-speech-commands/validations-add-temperature.png)

1. Aggiungere alcune frasi di esempio

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Aggiungere una regola di completamento per confermare il risultato

   | Impostazione    | Valore consigliato                                           |Descrizione                                     |
   | ---------- | --------------------------------------------------------- |-----|
   | Nome       | Messaggio di conferma                                      |Nome che descrive lo scopo della regola |
   | Condizioni | Parametri obbligatori-`Temperature`                       |Condizioni che determinano quando la regola può essere eseguita    |   
   | Azioni    | Invia risposta vocale-`Ok, setting temperature to {Temperature} degrees` | Azione da eseguire quando la condizione della regola è true |

> [!TIP]
> In questo esempio viene utilizzata una risposta vocale per confermare il risultato. Per esempi sul completamento del comando con un'azione client, vedere [procedura: eseguire l'evasione dei comandi sul client con l'SDK di riconoscimento vocale](./how-to-custom-speech-commands-fulfill-sdk.md)


## <a name="try-it-out"></a>Procedura
1. Selezionare `Train` l'icona presente nella parte superiore del riquadro destro.

1. Una volta completato il training, selezionare `Test` e provare alcune interazioni.

    - Input: impostare la temperatura su 72 gradi
    - Output: OK, impostazione della temperatura su 72 gradi
    - Input: impostare la temperatura su 45 gradi
    - Output: mi dispiace, posso impostare solo tra 60 e 80 gradi
    - Input: renderlo 72 gradi
    - Output: OK, impostazione della temperatura su 72 gradi

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedura: aggiungere una conferma a un comando personalizzato (anteprima)](./how-to-custom-speech-commands-confirmations.md)
