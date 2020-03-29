---
title: 'Guida introduttiva: Creare un comando personalizzato con parametri (anteprima) - Servizio di riconoscimento vocale'
titleSuffix: Azure Cognitive Services
description: In questo articolo verranno aggiunti parametri a un'applicazione Comandi personalizzati.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 96312bac369cfa5fe3cb8a00fd63ecfbec624918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80348538"
---
# <a name="quickstart-create-a-custom-command-with-parameters-preview"></a>Guida introduttiva: Creare un comando personalizzato con parametri (anteprima)Quickstart: Create a Custom Command with parameters (Preview)

[Nell'articolo precedente](./quickstart-custom-speech-commands-create-new.md)è stato creato un nuovo progetto Comandi personalizzati per rispondere ai comandi senza parametri.

In questo articolo, estenderemo questa applicazione con parametri in modo che possa gestire l'accensione e la disattivazione di più dispositivi.

## <a name="create-parameters"></a>Create Parameters

1. Aprire il progetto [creato in precedenza](./quickstart-custom-speech-commands-create-new.md)
1. Poiché il comando ora gestirà on e off, rinominare il comando in "TurnOnOff"
   - Passare il mouse sul nome del comando e selezionare l'icona di modifica per modificare il nome
1. Creare un nuovo parametro per indicare se l'utente desidera attivare o disattivare il dispositivo
   - Selezionare `+` l'icona accanto alla sezione Parametri

   > [!div class="mx-imgBorder"]
   > ![Crea parametro](media/custom-speech-commands/create-on-off-parameter.png)

   | Impostazione            | Valore consigliato     | Descrizione                                                                                               |
   | ------------------ | ------------------- | --------------------------------------------------------------------------------------------------------- |
   | Nome               | Onoff               | Un nome descrittivo per il parametro                                                                     |
   | È globale          | unchecked           | Casella di controllo che indica se un valore per questo parametro viene applicato globalmente a tutti i comandi nel progetto |
   | Obbligatoria           | checked             | Casella di controllo che indica se è necessario un valore per questo parametro prima di completare il comando          |
   | Modello di risposta  | "- Acceso o spento?"      | Un prompt per richiedere il valore di questo parametro quando non è noto                                       |
   | Type               | string              | Il tipo di parametro, ad esempio Number, String o Date Time                                               |
   | Configurazione      | Elenco stringhe         | Per le stringhe, un elenco di stringhe limita gli input a un set di valori possibiliFor Strings, a String List limitss inputs to a set of possible values                                      |
   | Valori dell'elenco di stringhe | on, off             | Per un parametro String List, il set di valori possibili e i relativi sinonimi                                |

   - Selezionare quindi `+` nuovamente l'icona per aggiungere un secondo parametro per rappresentare il nome dei dispositivi. Per questo esempio, un televisore e un ventilatore

   | Impostazione            | Valore consigliato       | Descrizione                                                                                               |
   | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
   | Nome               | SubjectDevice         | Un nome descrittivo per il parametro                                                                     |
   | È globale          | unchecked             | Casella di controllo che indica se un valore per questo parametro viene applicato globalmente a tutti i comandi nel progetto |
   | Obbligatoria           | checked               | Casella di controllo che indica se è necessario un valore per questo parametro prima di completare il comando          |
   | Modello di risposta  | "- Quale dispositivo?"     | Un prompt per richiedere il valore di questo parametro quando non è noto                                       |
   | Type               | string                | Il tipo di parametro, ad esempio Number, String o Date Time                                               |
   | Configurazione      | Elenco stringhe           | Per le stringhe, un elenco di stringhe limita gli input a un set di valori possibiliFor Strings, a String List limitss inputs to a set of possible values                                      |
   | Valori dell'elenco di stringhe | tv, ventilatore               | Per un parametro String List, il set di valori possibili e i relativi sinonimi                                |
   | Sinonimi (tv)      | televisione, televisione     | Sinonimi facoltativi per ogni valore possibile di un parametro di elenco di stringheOptional synonyms for each possible value of a String List Parameter                                      |

## <a name="add-sample-sentences"></a>Aggiungi frasi di esempio

Con i parametri, è utile aggiungere frasi campione che coprono tutte le possibili combinazioni. Ad esempio:

1. Informazioni complete sui parametri -`"turn {OnOff} the {SubjectDevice}"`
1. Informazioni parziali sui parametri -`"turn it {OnOff}"`
1. Nessuna informazione sui parametri -`"turn something"`

Frasi di esempio con quantità diverse di informazioni consentono all'applicazione Comandi personalizzati di risolvere sia le risoluzioni one-shot che le risoluzioni a più turni con informazioni parziali.

Con questo in mente, modificare le frasi di esempio per utilizzare i parametri come suggerito di seguito.

> [!TIP]
> Nell'editor Frasi di esempio utilizzare le parentesi graffe per fare riferimento ai parametri. - `turn {OnOff} the {SubjectDevice}`Utilizzare il completamento tramite tabulazione per fare riferimento ai parametri creati in precedenza.

> [!div class="mx-imgBorder"]
> ![Frasi di esempio con parametri](media/custom-speech-commands/create-parameter-sentences.png)

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

## <a name="add-parameters-to-completion-rule"></a>Aggiungere parametri alla regola di completamento

Modificare la regola di completamento creata nella [guida introduttiva precedente:](./quickstart-custom-speech-commands-create-new.md)

1. Aggiungere una nuova condizione e selezionare Parametro obbligatorio. Selezionare `OnOff` entrambi e`SubjectDevice`
1. Modificare l'azione Risposta `OnOff` `SubjectDevice`vocale da utilizzare e:

   ```
   - Ok, turning {OnOff} the {SubjectDevice}
   ```

## <a name="try-it-out"></a>Provare il servizio

Apri il pannello di chat Test e prova alcune interazioni.

- Ingresso: spegnere la tv
- Uscita: Ok, spegnere la tv

- Ingresso: spegnere il televisore
- Uscita: Ok, spegnere la tv

- Ingresso: disattivarlo
- Uscita: Quale dispositivo?
- Ingresso: la tv
- Uscita: Ok, spegnere la tv

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Guida introduttiva: Utilizzare comandi personalizzati con la voce personalizzata (anteprima)](./quickstart-custom-speech-commands-select-custom-voice.md)
