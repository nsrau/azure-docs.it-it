---
title: Aggiungere convalide nell'anteprima comandi personalizzati-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come aggiungere convalide a un parametro del comando in un'app di anteprima di comandi personalizzati.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: 6686016f109fad4ee8b7f4e494b1374a6003658c
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310411"
---
# <a name="add-validations-to-a-command-parameter-in-a-custom-commands-preview-application"></a>Aggiungere convalide a un parametro di comando in un'applicazione di anteprima comandi personalizzati

In questo articolo si apprenderà come aggiungere convalide ai parametri e richiedere la correzione.

## <a name="prerequisites"></a>Prerequisiti

Completare i passaggi descritti negli articoli seguenti:

> [!div class="checklist"]
 
> * [Guida introduttiva: creare un'app di anteprima per i comandi personalizzati](./quickstart-custom-speech-commands-create-new.md)
> * [Guida introduttiva: creare un'app di anteprima dei comandi personalizzati con i parametri](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>Creare un comando setemperature

Per dimostrare le convalide, creare un nuovo comando che consenta agli utenti di impostare la temperatura.

1. In [speech studio](https://speech.microsoft.com/)aprire l'app Custom Commands Preview creata.
1. Creare un nuovo comando di **temperatura** .
1. Aggiungere un parametro di temperatura con la seguente configurazione:

   | Configurazione parametri           | Valore consigliato    |Descrizione                 |                                    
   | ----------------- | ----------------------------------| -------------|
   | **Nome**              | **Temperatura**                       | Nome descrittivo per il parametro                                |
   | **Richiesto**          | Selezionato                           | Casella di controllo che indica se è necessario un valore per questo parametro prima di completare il comando |
   | **Risposta per il parametro obbligatorio**     | **Editor semplice: > la temperatura desiderata?**  | Messaggio di richiesta per richiedere il valore di questo parametro quando non è noto |
   | **Type**              | **Number**                            | Tipo di parametro, ad esempio Number, String, DateTime o geography   |

1. Aggiungere una convalida per il parametro temperature.

    1. Nella pagina configurazione **parametri** per il parametro temperatura selezionare **Aggiungi una convalida** nella sezione **convalide** .

    1. Nella finestra popup **nuova convalida** configurare la convalida come indicato di seguito:
  
       | Configurazione parametri         | Valore consigliato                                          | Descrizione                                                                        |
       | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
       | **Min Value (Valore minimo)**        | **60**               | Per i parametri number, il valore minimo che questo parametro può assumere |
       | **Max Value (Valore massimo)**        | **80**               | Per i parametri number, il valore massimo che questo parametro può assumere |
       | **Risposta di errore-editor semplice**| **Prima variazione: mi dispiace, posso impostare solo tra 60 e 80 gradi**      | Richiedi un nuovo valore se la convalida ha esito negativo                                       |

       > [!div class="mx-imgBorder"]
       > ![Aggiungere una convalida dell'intervallo](media/custom-speech-commands/validations-add-temperature.png)

1. Selezionare **Create** (Crea).

1. Aggiungere alcune frasi di esempio.

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Aggiungere una regola di completamento con la configurazione seguente. Questa regola conferma il risultato.

   | Impostazione    | Valore consigliato                                           |Descrizione                                     |
   | ---------- | --------------------------------------------------------- |-----|
   | Nome       | Messaggio di conferma                                      |Nome che descrive lo scopo della regola |
   | **Condizioni** | **Parametri obbligatori-temperatura**                       |Condizioni che determinano quando la regola può essere eseguita    |   
   | **Actions**    | **Invia risposta vocale-OK, impostazione della temperatura su {temperature} gradi** | Azione da eseguire quando la condizione della regola è true |

> [!TIP]
> In questo esempio viene utilizzata una risposta vocale per confermare il risultato. Per esempi sul completamento del comando con un'azione client, vedere [procedura: eseguire il comando per l'esecuzione di comandi sul client con l'SDK di riconoscimento vocale](./how-to-custom-speech-commands-fulfill-sdk.md).

## <a name="try-it-out"></a>Procedura

1. Selezionare il pulsante **Train** (Esegui il training).

1. Al termine del training, selezionare **test**, quindi provare le interazioni seguenti:

    - Input: impostare la temperatura su 72 gradi
    - Output: OK, impostazione della temperatura su 72 gradi
    - Input: impostare la temperatura su 45 gradi
    - Output: mi dispiace, posso impostare solo tra 60 e 80 gradi
    - Input: renderlo 72 gradi
    - Output: OK, impostazione della temperatura su 72 gradi

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere conferme a un comando in un'app di anteprima di comandi personalizzati](./how-to-custom-speech-commands-confirmations.md)
