---
title: 'Guida introduttiva: creare un comando personalizzato (anteprima)'
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come creare e testare un'applicazione di comandi personalizzati ospitata.
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: bfe871ce6f7cd2fbd6ada4b825e41ebdf5ac3f12
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507561"
---
# <a name="quickstart-create-a-custom-command-preview"></a>Guida introduttiva: creare un comando personalizzato (anteprima)

In questo articolo si apprenderà come creare e testare un'applicazione di comandi personalizzati ospitata.
L'applicazione rileverà un enunciato come "accendere la TV" e rispondere con un semplice messaggio "OK, acceso il televisore".

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione vocale. [Prova il servizio di riconoscimento vocale gratuitamente](~/articles/cognitive-services/speech-service/get-started.md).

  > [!NOTE]
  > Durante l'anteprima, solo l'area westus2 è supportata per le chiavi di sottoscrizione.

- Chiave di creazione di un [Language Understanding](https://www.luis.ai/home) (Luis):
  1. Aprire un Web browser e passare alla [portale di Azure](https://portal.azure.com)
  1. Selezionare Crea una risorsa
  1. Cercare e selezionare [Language Understanding](https://aka.ms/sc-luis-all)
  1. Selezionare Creazione e modifica nelle opzioni crea
  1. Dopo la distribuzione della risorsa, passare alla risorsa e copiare la chiave dalla sezione avvio rapido o chiavi

      > [!div class="mx-imgBorder"]
      > ![creare una risorsa di creazione](media/custom-speech-commands/resources-lu-authoring.png)

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Vai a speech studio per i comandi personalizzati

1. Aprire il Web browser e passare a [speech studio](https://speech.microsoft.com/)
1. Immettere le credenziali per accedere al portale

   - La visualizzazione predefinita è l'elenco di sottoscrizioni vocali
     > [!NOTE]
     > Se non viene visualizzata la pagina Seleziona sottoscrizione, è possibile spostarsi in questa posizione scegliendo "risorse vocali" dal menu impostazioni della barra superiore.

1. Selezionare la sottoscrizione vocale, quindi selezionare **Vai a Studio**
1. Selezionare i **comandi personalizzati (anteprima)**

La visualizzazione predefinita è un elenco delle applicazioni dei comandi personalizzati create.

## <a name="create-a-custom-commands-project"></a>Creare un progetto di comandi personalizzati

1. Selezionare **nuovo progetto** per creare un nuovo progetto

   > [!div class="mx-imgBorder"]
   > ![creare un nuovo progetto](media/custom-speech-commands/create-new-project.png)

1. Immettere il nome e la lingua del progetto, quindi selezionare **Avanti** per continuare.
1. Immettere la chiave di authoring LUIS
1. Al termine della creazione, selezionare il progetto

La visualizzazione dovrebbe ora essere una panoramica dell'applicazione comandi personalizzata.

## <a name="create-a-new-command"></a>Crea un nuovo comando

A questo punto è possibile creare un comando. Verrà ora usato un esempio che accetta un singolo enunciato, `turn on the tv`e risponderà con il messaggio `Ok, turning on the TV`.

1. Creare un nuovo comando selezionando l'icona `+` accanto ai comandi e assegnarle il nome `TurnOn`
1. Selezionare **Salva**

> [!div class="mx-imgBorder"]
> ![creare un comando](media/custom-speech-commands/create-add-command.png)

Un comando è un set di:

| Gruppo            | Descrizione                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Frasi di esempio | Espressioni di esempio che l'utente può pronunciare per attivare questo comando                                                                 |
| Parametri       | Informazioni necessarie per completare il comando                                                                                |
| Regole di completamento | Azioni da intraprendere per completare il comando. Ad esempio, per rispondere all'utente o comunicare con un altro servizio Web |
| Regole avanzate   | Regole aggiuntive per la gestione di situazioni più specifiche o complesse                                                              |

### <a name="add-a-sample-sentence"></a>Aggiungere una frase di esempio

Si inizierà con frasi di esempio e si fornirà un esempio di ciò che l'utente può pronunciare:

```
turn on the tv
```

Per il momento non sono disponibili parametri, quindi è possibile passare alle regole di completamento.

### <a name="add-a-completion-rule"></a>Aggiungere una regola di completamento

Aggiungere ora una regola di completamento per rispondere all'utente che indica che viene eseguita un'azione.

> [!div class="mx-imgBorder"]
> ![creare una regola di completamento](media/custom-speech-commands/create-basic-completion-response-rule.png)

| Impostazione    | Valore consigliato                        | Descrizione                                        |
| ---------- | -------------------------------------- | -------------------------------------------------- |
| Nome regola  | "ConfirmationResponse"                 | Nome che descrive lo scopo della regola          |
| Condizioni | Nessuna                                   | Condizioni che determinano quando la regola può essere eseguita    |
| Azioni    | SpeechResponse "OK, accendere la TV" | Azione da eseguire quando la condizione della regola è true |

## <a name="try-it-out"></a>Prova

Testare il comportamento usando il pannello test chat.

> [!div class="mx-imgBorder"]
> ![test con webchat](media/custom-speech-commands/create-basic-test-chat.png)

- Digitare: "accendere la TV"
- Risposta prevista: "OK, accensione della TV"

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Guida introduttiva: creare un comando personalizzato con parametri (anteprima)](./quickstart-custom-speech-commands-create-parameters.md)
