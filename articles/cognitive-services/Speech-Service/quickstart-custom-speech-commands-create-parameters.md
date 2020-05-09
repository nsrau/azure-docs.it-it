---
title: 'Guida introduttiva: creare un comando personalizzato con parametri (anteprima)-servizio riconoscimento vocale'
titleSuffix: Azure Cognitive Services
description: In questo articolo verranno aggiunti i parametri a un'applicazione comandi personalizzata.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: bf77616123f9311f7384fea515f250e47b354c8c
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82853588"
---
# <a name="quickstart-create-a-custom-commands-application-with-parameters-preview"></a>Guida introduttiva: creare un'applicazione di comandi personalizzati con parametri (anteprima)

Nell' [articolo precedente](./quickstart-custom-speech-commands-create-new.md)è stata creata una semplice applicazione di comandi personalizzati senza parametri.

In questo articolo si estenderà l'applicazione per usare i parametri in modo che sia in grado di gestire l'attivazione e la disattivazione di più dispositivi.

## <a name="create-parameters"></a>Create Parameters

1. Aprire il progetto [creato in precedenza](./quickstart-custom-speech-commands-create-new.md)
1. Modificare il comando esistente per attivare e disattivare più dispositivi.
1. Poiché il comando ora gestisce on e off, rinominare il comando in `TurnOnOff`.
   - Nel riquadro sinistro selezionare il `TurnOn` comando e quindi fare clic sull' `...` icona accanto a `+ New command` nella parte superiore del riquadro.
   
   - Selezionare `Rename` l'icona. Nella finestra popup del **comando Rinomina** modificare **nome** in `TurOnOff`. Selezionare quindi **Salva**.

1. Si crea quindi un nuovo parametro per indicare se l'utente vuole attivare o disattivare il dispositivo.
   - Selezionare `+ Add` l'icona presente nella parte superiore del riquadro centrale. Dall'elenco a discesa selezionare Parameter ( **parametro**).
   - Nel riquadro più a destra è possibile visualizzare la sezione **parametri** di configurazione.
   - Aggiungere un valore per il **nome**.
   - Controllare la casella di controllo **obbligatorio** . Nella finestra **Aggiungi risposta per un parametro obbligatorio** selezionare **Editor semplice** e alla **prima variazione**, Aggiungi
        ```
        On or Off?
        ```
   - Selezionare **Aggiorna**.

       > [!div class="mx-imgBorder"]
       > ![Crea risposta parametro obbligatoria](media/custom-speech-commands/add-required-on-off-parameter-response.png)
   
   - Successivamente, configurare il resto delle proprietà del parametro come indicato di seguito e selezionare `Save` per salvare la configurazione di tutte le configurazioni nel parametro.
       

       | Configurazione      | Valore consigliato     | Descrizione                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | Nome               | OnOff           | Nome descrittivo per il parametro                                                                           |
       | È globale          | unchecked       | Casella di controllo che indica se un valore per questo parametro viene applicato globalmente a tutti i comandi nell'applicazione|
       | Obbligatoria           | checked         | Casella di controllo che indica se è necessario un valore per questo parametro prima di completare il comando |
       | Risposta per il parametro obbligatorio      |Editor semplice-> on o off?      | Messaggio di richiesta per richiedere il valore di questo parametro quando non è noto |
       | Type               | string          | Tipo di parametro, ad esempio numero, stringa, data/ora o geografia   |
       | Configurazione      | Accetta valori di input predefiniti dal catalogo interno | Per le stringhe, questo limita gli input a un set di valori possibili. |
       | Valori di input predefiniti     | on, off             | Set di valori possibili e relativi alias         |
       
        > [!div class="mx-imgBorder"]
        > ![Crea parametro](media/custom-speech-commands/create-on-off-parameter.png)

   - Quindi, selezionare di `+ Add` nuovo l'icona per aggiungere un secondo parametro per rappresentare il nome dei dispositivi con la configurazione seguente.
   

       | Impostazione            | Valore consigliato       | Descrizione                                                                                               |
       | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
       | Nome               | SubjectDevice         | Nome descrittivo per il parametro                                                                     |
       | È globale          | unchecked             | Casella di controllo che indica se un valore per questo parametro viene applicato globalmente a tutti i comandi nell'applicazione |
       | Obbligatoria           | checked               | Casella di controllo che indica se è necessario un valore per questo parametro prima di completare il comando          |
       | Editor semplice      | Quale dispositivo?    | Messaggio di richiesta per richiedere il valore di questo parametro quando non è noto                                       |
       | Type               | string                | Tipo di parametro, ad esempio numero, stringa, data/ora o geografia                                                |
       | Configurazione      | Accetta valori di input predefiniti dal catalogo interno | Per le stringhe, un elenco di stringhe limita gli input a un set di valori possibili.       |
       | Valori di input predefiniti | TV, ventola               | Set di valori possibili e relativi alias                               |
       | Alias (TV)      | televisione, Telly     | Alias facoltativi per ogni possibile valore di valori di input predefiniti                                 |

## <a name="add-example-sentences"></a>Aggiungi frasi di esempio

Con i comandi con parametri, è utile aggiungere frasi di esempio che coprono tutte le possibili combinazioni. Ad esempio:

1. Informazioni complete sui parametri-`turn {OnOff} the {SubjectDevice}`
1. Informazioni sui parametri parziali-`turn it {OnOff}`
1. Nessuna informazione sui parametri-`turn something`

Le frasi di esempio con diversi livelli di informazioni consentono all'applicazione di comandi personalizzati di risolvere sia le risoluzioni unidirezionali sia le risoluzioni a più turni con informazioni parziali.

Tenendo presente questo aspetto, modificare le frasi di esempio in modo da usare i parametri come indicato di seguito.

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```
> [!TIP]
> Nell'editor frasi di esempio usare le parentesi graffe per fare riferimento ai parametri. - `turn {OnOff} the {SubjectDevice}`Usare TAB per il completamento automatico supportato da parametri creati in precedenza.

## <a name="add-parameters-to-completion-rules"></a>Aggiungere parametri alle regole di completamento

Modificare la regola di completamento creata nella [Guida introduttiva precedente](./quickstart-custom-speech-commands-create-new.md).

1. Nella sezione **condizioni** aggiungere una nuova condizione selezionando **+ Aggiungi una condizione**
1. Nella nuova **condizione popup nuovo**selezionare `Required parameters` dall'elenco a discesa **tipo** . Nell'elenco di controllo seguente selezionare sia `OnOff` che. `SubjectDevice`
1. Fare clic su **Crea**.
1. Nella sezione **azioni** modificare l'azione Invia risposta vocale esistente passando il puntatore del mouse sull'azione e facendo clic sull'icona di modifica. Questa volta, si userà i nuovi parametri e creati `OnOff` `SubjectDevice`

    ```
    Ok, turning {OnOff} the {SubjectDevice}
    ```

## <a name="try-it-out"></a>Procedura
1. Selezionare `Train` l'icona presente nella parte superiore del riquadro destro.

1. Una volta completato il training, selezionare `Test`.
    - Verrà visualizzato un nuovo test della finestra **dell'applicazione** .
    - Provare alcune interazioni.

        - Input: spegnere la TV
        - Output: OK, spegnimento della TV        
        - Input: spegnere la televisione
        - Output: OK, spegnimento della TV
        - Input: Disattiva
        - Output: quale dispositivo?
        - Input: TV
        - Output: OK, spegnimento della TV

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Guida introduttiva: usare comandi personalizzati con la voce personalizzata (anteprima)](./quickstart-custom-speech-commands-select-custom-voice.md)
