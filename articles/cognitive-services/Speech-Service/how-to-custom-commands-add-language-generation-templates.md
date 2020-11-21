---
title: 'Procedura: usare modelli di generazione della lingua per le risposte vocali-servizio di riconoscimento vocale'
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come usare i modelli di generazione del linguaggio con comandi personalizzati. I modelli di generazione del linguaggio consentono di personalizzare le risposte inviate al client e di introdurre la varianza nelle risposte.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 40c5e3474d3992108ef61d34e745bc63c1f7a713
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020947"
---
# <a name="add-language-generation-templates-for-speech-responses"></a>Aggiungere modelli di generazione della lingua per le risposte vocali

Questo articolo illustra come usare i modelli di generazione del linguaggio con comandi personalizzati. I modelli di generazione del linguaggio consentono di personalizzare le risposte inviate al client e di introdurre la varianza nelle risposte. La personalizzazione della generazione della lingua può essere eseguita tramite:

- Uso dei modelli di generazione del linguaggio
- Utilizzo di espressioni adattive

## <a name="prerequisites"></a>Prerequisiti

È necessario avere completato i passaggi descritti negli articoli seguenti:

> [!div class="checklist"]
> * [Procedura: creare un'applicazione con semplici comandi](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Procedura: aggiungere parametri ai comandi](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="language-generation-templates-overview"></a>Panoramica sui modelli di generazione della lingua

I modelli di comandi personalizzati sono basati sui [modelli LG](/azure/bot-service/file-format/bot-builder-lg-file-format#templates)di botframework (. Poiché i comandi personalizzati creano un nuovo modello LG quando necessario, ovvero per le risposte vocali nei parametri o nelle azioni, non è necessario specificare il nome del modello LG. Quindi, invece di definire il modello come:

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

È sufficiente definire il corpo del modello senza il nome, come indicato di seguito.

> [!div class="mx-imgBorder"]
> ![esempio di editor di modelli](./media/custom-commands/template-editor-example.png)


Questa modifica introduce la variazione delle risposte vocali inviate al client. Quindi, per lo stesso enunciato, la risposta vocale corrispondente verrebbe selezionata in modo casuale dalle opzioni fornite.

Sfruttare i vantaggi dei modelli LG consente inoltre di definire risposte vocali complesse per i comandi mediante espressioni adattive. Per altri dettagli, vedere il [formato dei modelli LG](/azure/bot-service/file-format/bot-builder-lg-file-format#templates) . Per impostazione predefinita, i comandi personalizzati supportano tutte le funzionalità con le differenze minime seguenti:

* Nelle entità dei modelli LG sono rappresentate come $ {EntityName}. Nei comandi personalizzati non vengono usate entità, ma i parametri possono essere usati come variabili con una delle rappresentazioni $ {parameterName} o {ParameterName}
* La composizione e l'espansione del modello non sono supportate nei comandi personalizzati. Questo perché non si modifica mai `.lg` direttamente il file, ma solo le risposte dei modelli creati automaticamente.
* Le funzioni personalizzate inserite da LG non sono supportate nei comandi personalizzati. Le funzioni predefinite sono ancora supportate.
* Le opzioni (Strict, replaceNull & lineBreakStyle) non sono supportate nei comandi personalizzati.

## <a name="add-template-responses-to-turnonoff-command"></a>Aggiungi risposte modello al comando TurnOnOff

Modificare il comando **TurnOnOff** per aggiungere un nuovo parametro con la configurazione seguente:

| Impostazione            | Valore consigliato       | 
| ------------------ | --------------------- | 
| Nome               | `SubjectContext`         | 
| È globale          | unchecked             | 
| Necessario           | unchecked               | 
| Type               | string                |
| Valore predefinito      | `all` |
| Configurazione      | Accetta valori di input predefiniti dal catalogo interno | 
| Valori di input predefiniti | `room`, `bathroom`, `all`|

### <a name="modify-completion-rule"></a>Modifica regola di completamento

Modificare la sezione **azioni** della regola di completamento esistente **ConfirmationResponse**. Nella finestra popup **Modifica azione** passare a **Editor modelli** e sostituire il testo con l'esempio seguente.

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

Eseguire il **Training** e il **test** dell'applicazione come indicato di seguito. Si noti la variazione delle risposte a causa dell'utilizzo di più alternative del valore del modello e anche dell'utilizzo di espressioni adattive.

* Input: accendere la TV
* Output: OK, attivazione della TV
* Input: accendere la TV
* Output: completato, acceso alla TV
* Input: spegnere le spie
* Output: OK, spegnimento di tutte le luci
* Input: disattiva le luci dello spazio
* Output: OK, spegnimento delle luci dello spazio

## <a name="use-custom-voice"></a>Usare la voce personalizzata

Un altro modo per personalizzare le risposte ai comandi personalizzati consiste nel selezionare una voce di output personalizzata. Usare la procedura seguente per passare la voce predefinita a una voce personalizzata.

1. Nell'applicazione comandi personalizzati selezionare **Impostazioni** dal riquadro sinistro.
1. Selezionare **voce personalizzata** dal riquadro centrale.
1. Selezionare la voce personalizzata o pubblica desiderata dalla tabella.
1. Selezionare **Salva**.

> [!div class="mx-imgBorder"]
> ![Frasi di esempio con parametri](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - Per le **voci pubbliche**, i **tipi neurali** sono disponibili solo per aree specifiche. Per verificare la disponibilità, vedere [le voci standard e neurali per area/endpoint](./regions.md#standard-and-neural-voices).
> - Per le **voci personalizzate**, è possibile crearle dalla pagina del progetto Voice personalizzato. Vedere [Introduzione alla voce personalizzata](./how-to-custom-voice.md).

A questo punto, l'applicazione risponderà alla voce selezionata, anziché alla voce predefinita.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Integrare i comandi personalizzati con l'SDK di riconoscimento vocale](./how-to-custom-commands-setup-speech-sdk.md).