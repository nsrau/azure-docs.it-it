---
title: "Procedura: aggiungere semplici comandi all'applicazione comandi personalizzati-servizio riconoscimento vocale"
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come aggiungere parametri ai comandi personalizzati
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: d2a14a501ebcf0913804ce39019a3fa4018ca141
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85362374"
---
# <a name="add-parameters-to-commands"></a>Aggiungere parametri ai comandi

In questo articolo si apprenderà come aggiungere parametri ai comandi personalizzati. I parametri sono le informazioni richieste dai comandi per completare un'attività. In scenari complessi, i parametri possono essere usati anche per definire le condizioni che attivano azioni personalizzate.

## <a name="prerequisites"></a>Prerequisiti

> [!div class="checklist"]
> * [Procedura: creare un'applicazione con semplici comandi](./how-to-custom-commands-create-application-with-simple-commands.md)

## <a name="configure-parameters-for-turnon-command"></a>Configurare i parametri per il comando accendere

Modificare il comando **accendere** esistente per attivare e disattivare più dispositivi.

1. Poiché il comando ora gestisce sia lo scenario on che off, rinominare il comando in **TurnOnOff**.
   1. Nel riquadro sinistro selezionare il comando **accendere** , quindi fare clic sul pulsante con i puntini di sospensione (...) accanto a **nuovo comando** nella parte superiore del riquadro.
   
   1. Selezionare **Rinomina**. Nella finestra **Rinomina comando** modificare **nome** in **TurnOnOff**.

1. Successivamente, si aggiunge un nuovo parametro a questo comando che indica se l'utente vuole attivare o disattivare il dispositivo.
   1. Selezionare **Aggiungi** presente nella parte superiore del riquadro centrale. Dall'elenco a discesa selezionare Parameter ( **parametro**).
   1. Nel riquadro di destra, nella sezione **parametri** , aggiungere il valore nella casella **nome** come **OnOff**.
   1. Selezionare **required**. Nella finestra **Aggiungi risposta per un parametro obbligatorio** selezionare **Editor semplice**. Nella **prima variante**aggiungere
        ```
        On or Off?
        ```
   1. Selezionare **Aggiorna**.

       > [!div class="mx-imgBorder"]
       > ![Crea risposta parametro obbligatoria](media/custom-commands/add-required-on-off-parameter-response.png)
   
   1. A questo punto vengono configurate le proprietà dei parametri. Per la spiegazione di tutte le proprietà di configurazione di un comando, vedere [riferimenti](./custom-commands-references.md). Configurare il resto delle proprietà del parametro come indicato di seguito:
      

       | Configurazione      | Valore consigliato     | Descrizione                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | Nome               | `OnOff`           | Nome descrittivo per il parametro                                                                           |
       | È globale          | unchecked       | Casella di controllo che indica se un valore per questo parametro viene applicato globalmente a tutti i comandi nell'applicazione|
       | Obbligatoria           | checked         | Casella di controllo che indica se è necessario un valore per questo parametro prima di completare il comando |
       | Risposta per il parametro obbligatorio      |Editor semplice >`On or Off?`      | Messaggio di richiesta per richiedere il valore di questo parametro quando non è noto |
       | Type               | String          | Tipo di parametro, ad esempio numero, stringa, data/ora o geografia   |
       | Configurazione      | Accetta valori di input predefiniti dal catalogo interno | Per le stringhe, questo limita gli input a un set di valori possibili. |
       | Valori di input predefiniti     | `on`, `off`           | Set di valori possibili e relativi alias         |
       
        
   1. Per aggiungere valori di input predefiniti, selezionare **Aggiungi un input predefinito** e nella finestra **nuovo elemento** digitare **nome** come indicato nella tabella precedente. In questo caso, gli alias non vengono usati, quindi è possibile lasciarli vuoti. 
    > [!div class="mx-imgBorder"]
        > ![Crea parametro](media/custom-commands/create-on-off-parameter.png)
   1. Selezionare **Save (Salva** ) per salvare tutte le configurazioni del parametro.
 
 ### <a name="add-subjectdevice-parameter"></a>Aggiungi parametro SubjectDevice 

   1. Quindi, selezionare di nuovo **Aggiungi** per aggiungere un secondo parametro per rappresentare il nome dei dispositivi che possono essere controllati con questo comando. Utilizzare la configurazione seguente.
   

       | Impostazione            | Valore consigliato       |
       | ------------------ | --------------------- |
       | Nome               | `SubjectDevice`         |
       | È globale          | unchecked             |
       | Obbligatoria           | checked               |
       | Risposta per il parametro obbligatorio     | Editor semplice >`Which device do you want to control?`    | 
       | Type               | String                |          |
       | Configurazione      | Accetta valori di input predefiniti dal catalogo interno | 
       | Valori di input predefiniti | `tv`, `fan`               |
       | Alias ( `tv` )      | `television`, `telly`     |

   1. Selezionare **Salva**

### <a name="modify-example-sentences"></a>Modificare le frasi di esempio

Per i comandi con parametri, è utile aggiungere frasi di esempio che coprono tutte le possibili combinazioni. Ad esempio:

* Informazioni complete sui parametri-`turn {OnOff} the {SubjectDevice}`
* Informazioni sui parametri parziali-`turn it {OnOff}`
* Nessuna informazione sui parametri-`turn something`

Le frasi di esempio con diversi livelli di informazioni consentono all'applicazione di comandi personalizzati di risolvere sia le risoluzioni unidirezionali sia le risoluzioni a più turni con informazioni parziali.

Tenendo presente questo aspetto, modificare le frasi di esempio in modo da usare i parametri come indicato di seguito:

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

Seleziona **Salva**.

> [!TIP]
> Nell'editor frasi di esempio usare le parentesi graffe per fare riferimento ai parametri. - `turn {OnOff} the {SubjectDevice}`Usare TAB per il completamento automatico supportato da parametri creati in precedenza.

### <a name="modify-completion-rules-to-include-parameters"></a>Modificare le regole di completamento per includere i parametri

Modificare la regola di completamento esistente **ConfirmationResponse**.

1. Nella sezione **condizioni** selezionare **Aggiungi una condizione**.
1. Nella finestra **nuova condizione** selezionare **parametri obbligatori**nell'elenco **tipo** . Nell'elenco di controllo seguente selezionare sia **OnOff** che **SubjectDevice**.
1. Selezionare **Crea**.
1. Nella sezione **azioni** modificare l'azione **Invia risposta vocale** esistente passando il puntatore del mouse sull'azione e selezionando il pulsante modifica. Questa volta, usare i parametri **OnOff** e **SubjectDevice** appena creati

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. Seleziona **Salva**.

### <a name="try-it-out"></a>Procedura
1. Selezionare l'icona del **Training** presente nella parte superiore del riquadro destro.

1. Al termine del training, selezionare **test**. Verrà visualizzato un test della finestra **dell'applicazione** .
 Provare alcune interazioni.

    - Input: spegnere la TV
    - Output: OK, spegnimento della TV
    - Input: spegnere la televisione
    - Output: OK, spegnimento della TV
    - Input: Disattiva
    - Output: quale dispositivo si vuole controllare?
    - Input: TV
    - Output: OK, spegnimento della TV

## <a name="configure-parameters-for-settemperature-command"></a>Configurare i parametri per il comando setemperature

Modificare il comando **setemperature** per abilitarlo a impostare la temperatura come indicato dall'utente.

Aggiungere la **temperatura** del nuovo parametro con la configurazione seguente

| Configurazione      | Valore consigliato     |
| ------------------ | ----------------|
| Nome               | `Temperature`           |
| Obbligatoria           | checked         |
| Risposta per il parametro obbligatorio      | Editor semplice >`What temperature would you like?`
| Type               | Number          |


Modificare le espressioni di esempio con i valori seguenti.

```
set the temperature to {Temperature} degrees
change the temperature to {Temperature}
set the temperature
change the temperature
```

Modificare le regole di completamento esistenti in base alla configurazione seguente.

| Configurazione      | Valore consigliato     |
| ------------------ | ----------------|
| Condizioni         | Temperatura > parametro obbligatorio           |
| Azioni           | Invia risposta vocale >`Ok, setting temperature to {Temperature} degrees` |

### <a name="try-it-out"></a>Procedura

Eseguire il **Training** e il **test** delle modifiche con alcune interazioni.

- Input: impostare la temperatura
- Output: quale temperatura si desidera?
- Input: 50 gradi
- Output: OK, impostazione della temperatura su 50 gradi

## <a name="configure-parameters-to-the-setalarm-command"></a>Configurare i parametri per il comando sealarm

Aggiungere un parametro denominato **DateTime** con la configurazione seguente.

   | Impostazione                           | Valore consigliato                     | 
   | --------------------------------- | ----------------------------------------|
   | Nome                              | `DateTime`                               |
   | Obbligatoria                          | checked                                 |
   | Risposta per il parametro obbligatorio   | Editor semplice >`For what time?`            | 
   | Type                              | Datetime                                |
   | Impostazioni predefinite data                     | Se la data non è presente, usare oggi            |
   | Impostazioni predefinite temporali                     | Se l'ora non è presente, usare l'inizio della giornata     |


> [!NOTE]
> In questo articolo sono stati usati principalmente i tipi di parametro String, Number e DateTime. Per un elenco di tutti i tipi di parametro supportati e delle relative proprietà, passare a [References](./custom-commands-references.md).


Modificare le espressioni di esempio con i valori seguenti.

```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

Modificare le regole di completamento esistenti in base alla configurazione seguente.

   | Impostazione    | Valore consigliato                               |
   | ---------- | ------------------------------------------------------- |
   | Azioni    | Invia risposta vocale-`Ok, alarm set for {DateTime}`  |


### <a name="try-it-out"></a>Procedura

Eseguire il **Training** e **testare** le modifiche.
- Input: impostare la sveglia per domani a mezzogiorno
- Output: OK, set di avvisi per 2020-05-02 12:00:00
- Input: impostare un allarme
- Output: ora
- Input: 17.00
- Output: OK, set di avvisi per 2020-05-01 17:00:00


## <a name="try-out-all-the-commands"></a>Provare tutti i comandi

Testare tutti e tre i comandi insieme usando le espressioni correlate a comandi diversi. Si noti che è possibile passare da un comando all'altro.

- Input: impostare un allarme
- Output: per quanto tempo?
- Input: accendere la TV
- Output: OK, attivazione della TV
- Input: impostare un allarme
- Output: per quanto tempo?
- Input: 17.00
- Output: OK, set di avvisi per 2020-05-01 17:00:00

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedura: aggiungere configurazioni ai parametri dei comandi](./how-to-custom-commands-add-parameter-configuration.md)
