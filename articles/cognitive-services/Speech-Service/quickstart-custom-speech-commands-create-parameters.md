---
title: "Guida introduttiva: creare un'app di anteprima dei comandi personalizzati con parametri-servizio vocale"
titleSuffix: Azure Cognitive Services
description: In questo articolo verranno aggiunti parametri a un'applicazione di comandi personalizzati in modo che sia possibile attivare e disattivare più dispositivi.
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: nitinme
ms.openlocfilehash: 940b3604487fbef7736c0d8f4f9299563b0b86d5
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2020
ms.locfileid: "84509305"
---
# <a name="quickstart-create-a-custom-commands-preview-application-with-parameters"></a>Guida introduttiva: creare un'applicazione di anteprima dei comandi personalizzati con i parametri

Nell' [articolo precedente](./quickstart-custom-speech-commands-create-new.md)è stata creata una semplice applicazione di comandi personalizzati senza parametri.

In questo articolo si estenderà l'applicazione con i parametri in modo che sia possibile attivare e disattivare più dispositivi.

## <a name="create-parameters"></a>Creare un parametro

1. Aprire il progetto creato nell' [articolo precedente](./quickstart-custom-speech-commands-create-new.md).

   Il comando esistente verrà modificato in modo che possa essere usato per attivare e disattivare più dispositivi.
1. Poiché il comando ora gestisce sia on che off, rinominarlo in **TurnOnOff**.
   1. Nel riquadro sinistro selezionare il comando **accendere** , quindi fare clic sul pulsante con i puntini di sospensione (**...**) accanto a **nuovo comando** nella parte superiore del riquadro.
   
   1. Selezionare **Rinomina**. Nella finestra **Rinomina comando** modificare il **nome** in **TurOnOff**. Selezionare **Salva**.

1. Creare un parametro per indicare se l'utente vuole attivare o disattivare il dispositivo.
   1. Selezionare **Aggiungi** nella parte superiore del riquadro centrale. Nell'elenco a discesa selezionare **Parameter (parametro**).
   1. Nel riquadro di destra, nella sezione **parametri** , aggiungere un valore nella casella **nome** .
   1. Selezionare **required**. Nella finestra **Aggiungi risposta per un parametro obbligatorio** selezionare **Editor semplice**. Nella **prima casella variante** immettere il testo seguente:
        ```
        On or Off?
        ```
   1. Selezionare **Aggiorna**.

       > [!div class="mx-imgBorder"]
       > ![Crea risposta parametro obbligatoria](media/custom-speech-commands/add-required-on-off-parameter-response.png)
   
1. Configurare il resto delle proprietà del parametro come indicato di seguito:
       

    | Configurazione      | Valore consigliato     | Descrizione                                                      |
    | ------------------ | ----------------| ---------------------------------------------------------------------|
    | **Nome**               | **OnOff**           | Nome descrittivo per il parametro.                                                                  |
    | **È globale**          | Cancellato       | Casella di controllo che indica se un valore per il parametro viene applicato globalmente a tutti i comandi nell'applicazione.|
    | **Richiesto**           | Selezionato         | Casella di controllo che indica se è necessario un valore per il parametro.  |
    | **Risposta per il parametro obbligatorio**      |**Editor semplice-> on o off?**      | Messaggio di richiesta per richiedere il valore del parametro quando non è noto. |
    | **Tipo**               | **Stringa**          | Tipo di parametro. Ad esempio, Number, String, data time, geography.   |
    | **Configuration**      | **Accetta valori di input predefiniti dal catalogo interno** | Per le stringhe, questa impostazione limita gli input a un set di valori possibili. |
    | **Valori di input predefiniti**     | **acceso**, **disattivato**             | Set di valori possibili e relativi alias.         |
       


    > [!div class="mx-imgBorder"]
    > ![Crea parametro](media/custom-speech-commands/create-on-off-parameter.png)

1. Selezionare **Salva** per salvare le impostazioni.

 1. Selezionare di nuovo **Aggiungi** per aggiungere un secondo parametro. Questo parametro rappresenta il nome del dispositivo. Usare queste impostazioni:
   

       | Impostazione            | Valore consigliato       | Descrizione                                                                                               |
       | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
       | **Nome**               | **SubjectDevice**         | Nome descrittivo per il parametro.                                                                     |
       | **È globale**          | Cancellato             | Casella di controllo che indica se un valore per il parametro viene applicato globalmente a tutti i comandi nell'applicazione. |
       | **Richiesto**           | Selezionato               | Casella di controllo che indica se è necessario un valore per il parametro.          |
       | **Editor semplice**      | **Quale dispositivo?**    | Messaggio di richiesta per richiedere il valore del parametro quando non è noto.                                       |
       | **Tipo**               | **Stringa**                | Tipo di parametro. Ad esempio, Number, String, data time, geography.                                                |
       | **Configuration**      | **Accetta valori di input predefiniti dal catalogo interno** | Per le stringhe, questa impostazione limita gli input a un set di valori possibili.       |
       | **Valori di input predefiniti** | **TV**, **ventola**               | Set di valori possibili e relativi alias.                               |
       | **Alias** (TV)      | **televisione**, **Telly**     | Alias facoltativi per ogni valore di input predefinito.                                 |

## <a name="add-example-sentences"></a>Aggiungi frasi di esempio

Per i comandi con parametri, è utile aggiungere frasi di esempio che coprono tutte le possibili combinazioni. Ad esempio:

- Informazioni complete sui parametri:`turn {OnOff} the {SubjectDevice}`
- Informazioni sul parametro parziale:`turn it {OnOff}`
- Nessuna informazione sui parametri:`turn something`

Le frasi di esempio con diverse quantità di informazioni consentono all'applicazione comandi personalizzati di risolvere sia le risoluzioni unidirezionali che le risoluzioni a più turni con informazioni parziali.

Tenendo presente questo aspetto, modificare le frasi di esempio in modo da usare i parametri come indicato di seguito:

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```
> [!TIP]
> Nell'editor frasi di esempio usare le parentesi graffe per fare riferimento ai parametri: `turn {OnOff} the {SubjectDevice}` .
>
> Utilizzare la scheda per il completamento automatico definito da parametri creati in precedenza.

## <a name="add-parameters-to-completion-rules"></a>Aggiungere parametri alle regole di completamento

Modificare la regola di completamento creata nella [Guida introduttiva precedente](./quickstart-custom-speech-commands-create-new.md).

1. Nella sezione **condizioni** selezionare **Aggiungi una condizione**.
1. Nella finestra **nuova condizione** selezionare **parametri obbligatori**nell'elenco **tipo** . Nell'elenco selezionare **OnOff** e **SubjectDevice**.
1. Selezionare **Crea**.
1. Nella sezione **azioni** modificare l'azione **Invia risposta vocale** esistente passando il puntatore del mouse sull'azione e selezionando il pulsante modifica. Questa volta usare i nuovi `OnOff` parametri e `SubjectDevice` :

    ```
    Ok, turning {OnOff} the {SubjectDevice}
    ```

## <a name="try-it-out"></a>Procedura
1. Selezionare **Train** nella parte superiore del riquadro destro.

1. Al termine del training, selezionare **test**.
    
    Verrà visualizzato un test della finestra **dell'applicazione** .

1. Provare alcune interazioni.

        - Input: turn off the tv
        - Output: Ok, turning off the tv        
        - Input: turn off the television
        - Output: Ok, turning off the tv
        - Input: turn it off
        - Output: Which device?
        - Input: the tv
        - Output: Ok, turning off the tv

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Guida introduttiva: usare comandi personalizzati con la voce personalizzata (anteprima)](./quickstart-custom-speech-commands-select-custom-voice.md)
