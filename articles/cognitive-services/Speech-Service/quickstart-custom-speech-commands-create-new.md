---
title: 'Guida introduttiva: creare un comando personalizzato (anteprima)-servizio riconoscimento vocale'
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come creare e testare un'applicazione di comandi personalizzati ospitata.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: f31d7279b73bab7aefda4c4b6570500d05cb89d7
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872517"
---
# <a name="quickstart-create-a-custom-commands-app-preview"></a>Guida introduttiva: creare un'app Commands personalizzata (anteprima)

In questa Guida introduttiva si apprenderà come creare e testare un'applicazione di comandi personalizzata.
L'applicazione creata elaborerà espressioni come "accendere la TV" e rispondere con un semplice messaggio "OK, acceso il televisore".

## <a name="prerequisites"></a>Prerequisiti

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Creare una risorsa Voce di Azure<span class="docon docon-navigate-external x-hidden-focus"></span></a>

  > [!NOTE]
  > A questo punto, i comandi personalizzati supportano solo le sottoscrizioni vocali nelle aree westus, westus2 e Neur.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Vai a speech studio per i comandi personalizzati

1. Aprire il Web browser e passare a [speech studio](https://speech.microsoft.com/)
1. Immettere le credenziali per accedere al portale.

   - La visualizzazione predefinita è l'elenco di sottoscrizioni vocali.
     > [!NOTE]
     > Se non viene visualizzata la pagina Seleziona sottoscrizione, è possibile spostarsi in questa posizione scegliendo "risorse vocali" dal menu impostazioni della barra superiore.

1. Selezionare la sottoscrizione vocale, quindi selezionare **Vai a Studio**.
1. Selezionare **comandi personalizzati**.

     - La visualizzazione predefinita è un elenco delle applicazioni dei comandi personalizzati presenti nella sottoscrizione selezionata.

## <a name="create-a-custom-commands-project"></a>Creare un progetto di comandi personalizzati

1. Selezionare **nuovo progetto** per creare un nuovo progetto.

   > [!div class="mx-imgBorder"]
   > ![Creare un progetto](media/custom-speech-commands/create-new-project.png)

1. Immettere il nome del progetto.
1. Selezionare lingua dall'elenco a discesa.
1. Selezionare una risorsa di creazione dall'elenco a discesa. Se non sono presenti risorse di creazione valide, crearne una facendo clic su **Crea nuova risorsa**di creazione Luis.

   > [!div class="mx-imgBorder"]
   > ![Creare una risorsa](media/custom-speech-commands/create-new-resource.png)

   - Immettere il nome della risorsa, gruppo di risorse.
   - Scegliere valore per località e piano tariffario dall'elenco a discesa.

      > [!NOTE]
      > È possibile creare gruppi di risorse immettendo il nome del gruppo di risorse desiderato nel campo "gruppo di risorse". Il gruppo di risorse verrà creato quando si seleziona **Crea** .

1. Selezionare quindi **Crea** per creare il progetto.
1. Al termine della creazione, selezionare il progetto.

    - La visualizzazione dovrebbe ora essere una panoramica dell'applicazione dei comandi personalizzati appena creati.

## <a name="update-luis-resources-optional"></a>Aggiornare le risorse LUIS (facoltativo)

È possibile aggiornare la risorsa di creazione impostata nella finestra nuovo progetto e impostare una risorsa di stima. La risorsa di stima viene utilizzata per il riconoscimento dopo la pubblicazione dell'applicazione comandi personalizzata. Non è necessaria una risorsa di stima per le fasi di sviluppo e test.

1. Selezionare **Settings (impostazioni** ) nel riquadro a sinistra e quindi passare alla sezione **Luis Resources** nel riquadro centrale.
1. Selezionare una risorsa di stima o crearne una selezionando **Crea nuova risorsa**.
1. Selezionare **Salva**.
    
    > [!div class="mx-imgBorder"]
    > ![Imposta risorse LUIS](media/custom-speech-commands/set-luis-resources.png)


> [!NOTE]
> Poiché la risorsa di creazione supporta solo 1.000 richieste di endpoint di stima al mese, sarà necessario impostare una risorsa di stima LUIS prima di pubblicare l'applicazione dei comandi personalizzati.


## <a name="create-a-new-command"></a>Crea un nuovo comando

Verrà ora creato un semplice comando che accetta un singolo enunciato, `turn on the tv`e risponde con il messaggio `Ok, turning on the tv`.

1. Creare un nuovo comando selezionando l' `+ New command` icona presente nella parte superiore del riquadro sinistro. Viene visualizzata una nuova finestra popup denominata **nuovo comando**.
1. Specificare il valore per **Name** il campo nome `TurnOn`come.
1. Selezionare **Create** (Crea).

Il riquadro centrale integra le diverse proprietà di un comando:


| Configurazione            | Descrizione                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Frasi di esempio | Espressioni di esempio che l'utente può pronunciare per attivare questo comando                                                                 |
| Parametri       | Informazioni necessarie per completare il comando                                                                                |
| Regole di completamento | Azioni da intraprendere per completare il comando. Ad esempio, per rispondere all'utente o comunicare con un altro servizio Web. |
| Regole di interazione   | Regole aggiuntive per la gestione di situazioni più specifiche o complesse                                                              |


> [!div class="mx-imgBorder"]
> ![Creazione di un comando](media/custom-speech-commands/create-add-command.png)


### <a name="add-example-sentences"></a>Aggiungi frasi di esempio

Si inizierà con la sezione frasi di esempio e si fornirà un esempio di ciò che l'utente può pronunciare.

1. Selezionare le **frasi di esempio** nel riquadro centrale e nel riquadro più a destra aggiungere esempi:

    ```
    turn on the tv
    ```

1. Selezionare `Save` l'icona presente nella parte superiore del riquadro.

Per il momento non sono disponibili parametri, quindi è possibile passare alla sezione **regole di completamento** .

### <a name="add-a-completion-rule"></a>Aggiungere una regola di completamento

A questo punto aggiungere una regola di completamento con la configurazione seguente. Questa regola indica l'utente che viene eseguita un'azione di evasione.


| Impostazione    | Valore consigliato                          | Descrizione                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| Nome regola  | ConfirmationResponse                  | Nome che descrive lo scopo della regola          |
| Condizioni | Nessuno                                     | Condizioni che determinano quando la regola può essere eseguita    |
| Azioni    | SpeechResponse "-OK, accensione della TV" | Azione da eseguire quando la condizione della regola è true |

1. Creare una nuova regola di completamento selezionando `+Add` l'icona nella parte superiore del riquadro centrale.
1. Specificare il valore nella sezione **nome** .
1. Aggiungere un'azione
   1. Creare una nuova azione selezionando **+ Aggiungi un'azione** nella sezione **azioni** .
   1. Nella finestra popup **nuova azione** selezionare `Send speech response` dalle opzioni a discesa per **tipo**.
   1. Scegliere `Simple editor` per il campo **risposta** .
       - Nel **primo campo Variazione** specificare valore per Response come`Ok, turning on the tv`

   > [!div class="mx-imgBorder"]
   > ![Creare una risposta vocale](media/custom-speech-commands/create-speech-response-action.png)

1. Fare clic su **Salva** per salvare la regola.
1. Nella sezione **regole di completamento** selezionare **Salva** per salvare tutte le modifiche. 

> [!div class="mx-imgBorder"]
> ![Creare una regola di completamento](media/custom-speech-commands/create-basic-completion-response-rule.png)



## <a name="try-it-out"></a>Procedura

Testare il comportamento usando il pannello test chat
1. Selezionare `Train` l'icona presente nella parte superiore del riquadro destro.
1. Una volta completato il training, selezionare `Test`. Verrà visualizzato un nuovo test della finestra **dell'applicazione** .
    - Digitare: accendere la TV
    - Risposta prevista: OK, accensione della TV


> [!div class="mx-imgBorder"]
> ![Test con web chat](media/custom-speech-commands/create-basic-test-chat.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Guida introduttiva: creare un comando personalizzato con parametri (anteprima)](./quickstart-custom-speech-commands-create-parameters.md)
