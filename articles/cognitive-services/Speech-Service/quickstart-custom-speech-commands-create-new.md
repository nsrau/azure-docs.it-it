---
title: 'Guida introduttiva: creare un servizio personalizzato anteprima app-riconoscimento vocale'
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come creare e testare un'applicazione di anteprima di comandi personalizzati ospitati. L'applicazione elaborerà le espressioni.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: 80111ff370f3a5412b45adc04c82c9dee103c01d
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84142351"
---
# <a name="quickstart-create-a-custom-commands-preview-app"></a>Guida introduttiva: creare un'app di anteprima per i comandi personalizzati

In questa Guida introduttiva si apprenderà come creare e testare un'applicazione di comandi personalizzata.
L'applicazione elaborerà espressioni come "accendere la TV" e rispondere con un semplice messaggio come "OK, turning on the TV".

## <a name="prerequisites"></a>Prerequisiti

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Creare una risorsa Voce di Azure<span class="docon docon-navigate-external x-hidden-focus"></span></a>

  > [!NOTE]
  > A questo punto, i comandi personalizzati supportano solo le sottoscrizioni vocali nelle aree westus, westus2 e Neur.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Vai a speech studio per i comandi personalizzati

1. In un Web browser passare a [speech studio](https://speech.microsoft.com/).
1. Immettere le credenziali per accedere al portale.

   La visualizzazione predefinita è l'elenco di sottoscrizioni vocali.
    > [!NOTE]
    > Se non viene visualizzata la pagina Seleziona sottoscrizione, è possibile accedervi selezionando **risorse vocali** dal menu impostazioni nella parte superiore della schermata.

1. Selezionare la sottoscrizione vocale, quindi selezionare **Vai a Studio**.
1. Selezionare **comandi personalizzati**.

     La visualizzazione predefinita è un elenco delle applicazioni dei comandi personalizzati presenti nella sottoscrizione selezionata.

## <a name="create-a-custom-commands-project"></a>Creare un progetto di comandi personalizzati

1. Selezionare **nuovo progetto** per creare un progetto.

   > [!div class="mx-imgBorder"]
   > ![Creare un progetto](media/custom-speech-commands/create-new-project.png)

1. Nella casella **nome** immettere un nome di progetto.
1. Nell'elenco **lingua** selezionare una lingua.
1. Nell'elenco di risorse per la **creazione di Luis** selezionare una risorsa di creazione. Se non sono presenti risorse di creazione valide, crearne una selezionando **Crea nuova risorsa**di creazione Luis.

   > [!div class="mx-imgBorder"]
   > ![Creare una risorsa](media/custom-speech-commands/create-new-resource.png)

   1. Nella casella **nome risorsa** immettere il nome della risorsa.
   1. Nell'elenco **gruppo di risorse** selezionare un gruppo di risorse.
   1. Nell'elenco **percorso** selezionare una località.
   1. Nell'elenco piano **tariffario** selezionare un livello.

      > [!NOTE]
      > È possibile creare un gruppo di risorse immettendo il nome di un gruppo di risorse nella casella **gruppo di risorse** . Il gruppo di risorse verrà creato quando si seleziona **Crea**.

1. Selezionare **Crea**.
1. Dopo aver creato il progetto, selezionarlo.

    Verrà ora visualizzata una panoramica della nuova applicazione comandi personalizzati.

## <a name="update-luis-resources-optional"></a>Aggiornare le risorse LUIS (facoltativo)

È possibile aggiornare la risorsa di creazione selezionata nella finestra **nuovo progetto** e impostare una risorsa di stima. La risorsa di stima viene utilizzata per il riconoscimento quando viene pubblicata l'applicazione dei comandi personalizzati. Non è necessaria una risorsa di stima durante le fasi di sviluppo e test.

1. Selezionare **Settings (impostazioni** ) nel riquadro sinistro e quindi selezionare **risorse Luis** nel riquadro centrale.
1. Selezionare una risorsa di stima o crearne una selezionando **Crea nuova risorsa**.
1. Selezionare **Salva**.
    
    > [!div class="mx-imgBorder"]
    > ![Imposta risorse LUIS](media/custom-speech-commands/set-luis-resources.png)


> [!NOTE]
> Poiché la risorsa di creazione supporta solo 1.000 richieste di endpoint di stima al mese, è necessario impostare una risorsa di stima LUIS prima di pubblicare l'applicazione dei comandi personalizzati.


## <a name="create-a-command"></a>Creazione di un comando

Verrà ora creato un semplice comando che accetta un singolo enunciato, `turn on the tv` e risponde con il messaggio `Ok, turning on the tv` .

1. Creare un comando selezionando **nuovo comando** nella parte superiore del riquadro sinistro. Verrà visualizzata la finestra **nuovo comando** .
1. Nella casella **nome** immettere **accendere**.
1. Selezionare **Crea**.

Il riquadro centrale elenca le proprietà del comando:


| Configurazione            | Descrizione                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Frasi di esempio** | Esempi di espressioni pronunciate dall'utente per attivare il comando.                                                                 |
| **Parametri**       | Informazioni necessarie per completare il comando.                                                                                |
| **Regole di completamento** | Azioni che verranno eseguite per completare il comando. Ad esempio, rispondendo all'utente o comunicando con un altro servizio Web. |
| **Regole di interazione**   | Regole aggiuntive per gestire situazioni più specifiche o complesse.                                                              |


> [!div class="mx-imgBorder"]
> ![Creazione di un comando](media/custom-speech-commands/create-add-command.png)


### <a name="add-example-sentences"></a>Aggiungi frasi di esempio

Iniziamo con la sezione **frasi di esempio** . Verrà fornito un esempio di ciò che l'utente può pronunciare.

1. Selezionare le **frasi di esempio** nel riquadro centrale. 
1. Nel riquadro destro aggiungere gli esempi seguenti:

    ```
    turn on the tv
    ```

1. Selezionare **Salva** nella parte superiore del riquadro.

Per il momento non sono disponibili parametri, quindi è possibile passare alla sezione **regole di completamento** .

### <a name="add-a-completion-rule"></a>Aggiungere una regola di completamento

Aggiungere ora una regola di completamento con la configurazione seguente. Questa regola indica all'utente che viene eseguita un'azione di evasione.


| Impostazione    | Valore consigliato                          | Descrizione                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| **Nome**  | **ConfirmationResponse**                  | Nome che descrive lo scopo della regola.          |
| **Condizioni** | Nessuno                                     | Condizioni che determinano quando la regola può essere eseguita.    |
| **Actions**    | **Invia risposta vocale-> OK, accendere la TV** | Azione da eseguire quando la condizione della regola è true. |

1. Creare una nuova regola di completamento selezionando **Aggiungi** nella parte superiore del riquadro centrale.
1. Nella casella **Nome** immettere un nome.
1. Aggiungere un'azione.
   1. Creare un'azione selezionando **Aggiungi un'azione** nella sezione **azioni** .
   1. Nella finestra **Modifica azione** selezionare **Invia risposta vocale**nell'elenco **tipo** .
   1. In **risposta**selezionare **Editor semplice**. Nella **prima casella variante** , immettere **OK, accendere la TV**.

   > [!div class="mx-imgBorder"]
   > ![Creare una risposta](media/custom-speech-commands/create-speech-response-action.png)

1. Selezionare **Save (Salva** ) per salvare la regola.
1. Nella sezione **regole di completamento** selezionare **Salva** per salvare tutte le modifiche. 

> [!div class="mx-imgBorder"]
> ![Creare una regola di completamento](media/custom-speech-commands/create-basic-completion-response-rule.png)



## <a name="try-it-out"></a>Procedura

Testare il comportamento usando il pannello di chat di test.
1. Selezionare **Train** nella parte superiore del riquadro destro.
1. Al termine del training, selezionare **test**. Viene visualizzato un nuovo test della finestra **dell'applicazione** .
    - Immettere **Turn on the TV**
    - Risposta prevista: **OK, accensione della TV**


> [!div class="mx-imgBorder"]
> ![Testare il comportamento](media/custom-speech-commands/create-basic-test-chat.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Guida introduttiva: creare un'applicazione di anteprima dei comandi personalizzati con i parametri](./quickstart-custom-speech-commands-create-parameters.md)
