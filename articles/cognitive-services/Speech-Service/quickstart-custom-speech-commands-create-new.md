---
title: 'Guida introduttiva: Creare un comando personalizzato (anteprima) - Servizio di riconoscimento vocale'
titleSuffix: Azure Cognitive Services
description: In questo articolo viene creata e testata un'applicazione di comandi personalizzati ospitata.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 4ae8f13b4887bbc41b17defa3f9a20c07ed0cb45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76155588"
---
# <a name="quickstart-create-a-custom-command-preview"></a>Guida introduttiva: Creare un comando personalizzato (anteprima)Quickstart: Create a Custom Command (Preview)

In questo articolo verrà illustrato come creare e testare un'applicazione comandi personalizzati ospitata.
L'applicazione riconoscerà un'espressione come "accendere la tv" e rispondere con un semplice messaggio "Ok, accendere la tv".

## <a name="prerequisites"></a>Prerequisiti

- Un abbonamento Speech.

Se non si dispone di una sottoscrizione vocale, è possibile crearne una passando a [Speech Studio](https://speech.microsoft.com/) e selezionando Crea una risorsa **di riconoscimento vocale**.

  > [!div class="mx-imgBorder"]
  > [![Creare un](media/custom-speech-commands/create-new-subscription.png) progetto](media/custom-speech-commands/create-new-subscription.png#lightbox)

  > [!NOTE]
  > Durante l'anteprima, è supportata solo l'area westus2.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Vai a Speech Studio per i comandi personalizzati

1. Aprire il Web browser e passare a [Speech Studio](https://speech.microsoft.com/)
1. Immettere le credenziali per accedere al portale

   - La visualizzazione predefinita è l'elenco delle sottoscrizioni vocali
     > [!NOTE]
     > Se non vedi la pagina di selezione dell'abbonamento, puoi navigare scegliendo "Risorse vocali" dal menu delle impostazioni nella barra superiore.

1. Seleziona l'abbonamento A voce parlata, quindi seleziona **Vai a Studio**
1. Seleziona **comandi personalizzati (anteprima)Select Custom Commands (Preview)**

La visualizzazione predefinita è un elenco delle applicazioni Comandi personalizzati create.

## <a name="create-a-custom-commands-project"></a>Creare un progetto Di comandi personalizzatiCreate a Custom Commands project

1. Selezionare **Nuovo progetto** per creare un nuovo progetto

   > [!div class="mx-imgBorder"]
   > ![Creare un progetto](media/custom-speech-commands/create-new-project.png)

1. Immettere il nome e la lingua del progetto.
1. Selezionare una risorsa di creazione. Se non sono presenti risorse di creazione valide, crearne una selezionando **Crea nuova risorsa**.

   > [!div class="mx-imgBorder"]
   > ![Creare una risorsa](media/custom-speech-commands/create-new-resource.png)

   1. Immettere il nome della risorsa, il gruppo, la posizione e il piano tariffario.

         > [!NOTE]
         > È possibile creare gruppi di risorse immettendo il nome del gruppo di risorse desiderato nel campo "Gruppo di risorse". Il gruppo di risorse verrà creato quando si seleziona **Crea.The** resource group will be created when Create is selected.

1. Fare clic su **Crea** per creare il progetto.
1. Una volta creato, selezionare il progetto.

La visualizzazione dovrebbe ora essere una panoramica dell'applicazione Comandi personalizzati.

## <a name="update-luis-resources-optional"></a>Aggiornare le risorse LUIS (facoltativo)Update LUIS Resources (Optional)

È possibile aggiornare il set di risorse di creazione nella finestra del nuovo progetto e impostare una risorsa di stima utilizzata per riconoscere gli input durante il runtime.

> [!NOTE]
> È necessario impostare una risorsa di stima prima che l'applicazione richieda stime oltre le 1.000 richieste fornite dalla risorsa di creazione.

> [!div class="mx-imgBorder"]
> ![Impostazione delle risorse LUIS](media/custom-speech-commands/set-luis-resources.png)

1. Passare al riquadro Risorse LUIS selezionando **Impostazioni** nel riquadro sinistro e quindi **Risorse LUIS** dal riquadro centrale.
1. Selezionare una risorsa di stima o crearne una selezionando **Crea nuova risorsa**
1. Selezionare **Salva**

## <a name="create-a-new-command"></a>Creare un nuovo comando

Ora è possibile creare un comando. Usiamo un esempio che accetta una singola `turn on the tv`espressione, e rispondi `Ok, turning on the TV`con il messaggio .

1. Creare un nuovo comando `+` selezionando l'icona accanto ai comandi e assegnargli il nome`TurnOn`
1. Selezionare **Salva**

> [!div class="mx-imgBorder"]
> ![Creare un comando](media/custom-speech-commands/create-add-command.png)

Un comando è un insieme di:

| Gruppo            | Descrizione                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Frasi campione | Espressioni di esempio che l'utente può dire per attivare questo comandoExample utterances the user can say to trigger this Command                                                                 |
| Parametri       | Informazioni necessarie per completare il Comando                                                                                |
| Regole di completamento | Le azioni da intraprendere per adempiere al Comando. Ad esempio, per rispondere all'utente o comunicare con un altro servizio Web |
| Regole avanzate   | Regole aggiuntive per gestire situazioni più specifiche o complesse                                                              |

### <a name="add-a-sample-sentence"></a>Aggiunta di una frase di esempio

Iniziamo con le frasi di esempio e forniamo un esempio di ciò che l'utente può dire:

```
turn on the tv
```

Per ora, non abbiamo parametri in modo da poter passare alle regole di completamento.

### <a name="add-a-completion-rule"></a>Aggiungere una regola di completamentoAdd a Completion Rule

Aggiungere ora una regola di completamento per rispondere all'utente che indica che è in corso un'azione.

1. Creare una nuova regola `+` di completamento selezionando l'icona accanto a Regole di completamento
1. Immettere il nome della regola
1. Aggiungere un'azione
   1. Creare una nuova azione di `+` risposta vocale selezionando l'icona accanto ad Azioni e selezionare`SpeechResponse`
   1. Immettere la risposta

   > [!NOTE]
   > Il testo normale deve iniziare con un trattino. Per maggiori dettagli, [vai qui](https://aka.ms/sc-lg-format)

   > [!div class="mx-imgBorder"]
   > ![Creare una risposta di riconoscimento vocaleCreate a Speech response](media/custom-speech-commands/create-speech-response-action.png)

1. Fare clic su **Salva** per salvare la regola

> [!div class="mx-imgBorder"]
> ![Creare una regola di completamentoCreate a completion rule](media/custom-speech-commands/create-basic-completion-response-rule.png)

| Impostazione    | Valore consigliato                          | Descrizione                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| Nome regola  | "Risposta di conferma"                   | Un nome che descriva lo scopo della regola          |
| Condizioni | nessuno                                     | Condizioni che determinano quando la regola può essere eseguita    |
| Azioni    | SpeechResponse "- Ok, accendo il televisore" | Azione da eseguire quando la condizione della regola è vera |

## <a name="try-it-out"></a>Provare il servizio

Verifica il comportamento utilizzando il pannello di chat Test.

> [!div class="mx-imgBorder"]
> ![Test con chat web](media/custom-speech-commands/create-basic-test-chat.png)

- Si digita: "accendere la tv"
- Risposta prevista: "Ok, accendo la tv"

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Guida introduttiva: Creare un comando personalizzato con parametri (anteprima)Quickstart: Create a Custom Command with Parameters (Preview)](./quickstart-custom-speech-commands-create-parameters.md)
