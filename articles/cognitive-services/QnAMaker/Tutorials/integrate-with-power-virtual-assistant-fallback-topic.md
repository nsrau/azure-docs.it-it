---
title: "Esercitazione: Eseguire l'integrazione con Power Virtual Agents - QnA Maker"
titleSuffix: Azure Cognitive Services
description: Questa esercitazione illustra come migliorare la qualità della knowledge base con l'apprendimento attivo. È possibile rivedere, accettare o rifiutare oppure aggiungere senza rimuovere o modificare le domande esistenti.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 03/11/2020
ms.author: diberry
ms.openlocfilehash: 4557dee995c8a01067f7e6ad0e79bb7115b6ecdb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402823"
---
# <a name="tutorial-add-your-knowledge-base-to-power-virtual-agents"></a>Esercitazione: Aggiungere una knowledge base a Power Virtual Agents
Creare ed estendere un bot [Power Virtual Agents](https://powervirtualagents.microsoft.com/) per fornire risposte da una knowledge base.

In questa esercitazione verranno illustrate le procedure per:

<!-- green checkmark -->
> [!div class="checklist"]
> * Creare un bot Power Virtual Agents
> * Creare un argomento di fallback di sistema
> * Aggiungere QnA Maker come azione a un argomento sotto forma di flusso Power Automate
> * Creare una soluzione Power Automate
> * Aggiungere un flusso Power Automate alla soluzione
> * Pubblicare Power Virtual Agents
> * Testare Power Virtual Agents e ricevere una risposta dalla knowledge base di QnA Maker

## <a name="integrate-an-agent-with-a-knowledge-base"></a>Integrare un agente con una knowledge base

[Power Virtual Agents](https://powervirtualagents.microsoft.com/) consente ai team di creare bot avanzati usando un'interfaccia grafica guidata senza codice. Non sono necessari data scientist o sviluppatori.

In Power Virtual Agents si crea un agente con una serie di argomenti (aree di interesse) per rispondere alle domande degli utenti eseguendo azioni. Se non si trovano risposte, un fallback del sistema può restituirne una.

Configurare l'agente per inviare la domanda alla knowledge base come parte dell'azione di un argomento oppure come parte del percorso dell'argomento di *fallback di sistema*. In entrambi i casi viene usata un'azione per connettersi alla knowledge base e restituire una risposta.

## <a name="power-automate-connects-to-generateanswer-action"></a>Connessione di Power Automate all'azione `GenerateAnswer`

Per connettere l'agente alla knowledge base, usare Power Automate per creare l'azione. Power Automate offre un flusso di processo che si connette all'API `GenerateAnswer` di QnA Maker.

Dopo che è stato progettato e salvato, il flusso è disponibile in una soluzione Power Automate. Usare tale soluzione come azione nell'agente.

## <a name="connect-an-agent-to-your-knowledge-base"></a>Connettere un agente alla knowledge base

Di seguito è riportata una panoramica della procedura per connettere un agente di Power Virtual Agents a una knowledge base di QnA Maker.

* Nel portale di [QnA Maker](https://www.qnamaker.ai/):
    * Creare e pubblicare la knowledge base.
    * Copiare i dettagli della knowledge base, tra cui l'ID e la chiave e l'host dell'endpoint di runtime.
* Nel portale di [Power Virtual Agents](https://powerva.microsoft.com/):
    * Creare un argomento dell'agente.
    * Chiamare un'azione (nel flusso Power Automate).
* Nel portale di [Power Automate](https://us.flow.microsoft.com/):
    * Creare un flusso con un connettore a [GenerateAnswer di QnA Maker](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/).
        * Informazioni relative alla knowledge base pubblicata di QnA Maker:
            * ID della knowledge Base
            * Host dell'endpoint della risorsa di QnA Maker
            * Chiave dell'endpoint della risorsa di QnA Maker
        * Input: query dell'utente
        * Output: risposta della knowledge base
    * Creare una soluzione e aggiungere il flusso.
* Tornare a Power Virtual Agents:
    * Selezionare l'output della soluzione come messaggio per un argomento.

## <a name="create-and-publish-a-knowledge-base"></a>Creare e pubblicare una knowledge base

1. Seguire la [guida di avvio rapido](../Quickstarts/create-publish-knowledge-base.md) per creare una knowledge base. Non completare l'ultima sezione relativa alla creazione di un bot. Usare invece questa esercitazione per creare un bot con Power Virtual Agents.

    > [!div class="mx-imgBorder"]
    > ![Screenshot delle impostazioni della knowledge base pubblicata](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

    Immettere le impostazioni della knowledge base pubblicata disponibili nella pagina **Settings** (Impostazioni) del portale di [QnA Maker](https://www.qnamaker.ai/). Queste informazioni saranno necessarie nel [passaggio in Power Automate](#create-a-power-automate-flow-to-connect-to-your-knowledge-base) per configurare la connessione a `GenerateAnswer` di QnA Maker.

1. Nella pagina **Settings** (Impostazioni) del portale di QnA Maker trovare la chiave e l'host dell'endpoint e l'ID knowledge base.

## <a name="create-an-agent-in-power-virtual-agents"></a>Creare un agente in Power Virtual Agents

1. [Accedere a Power Virtual Agents](https://go.microsoft.com/fwlink/?LinkId=2108000&clcid=0x409). Usare l'account di posta elettronica aziendale o dell'istituto di istruzione.
1. Se è il primo bot, verrà visualizzata la pagina **Home** dell'agente. Se non è il primo, selezionare il bot nell'area in alto a destra nella pagina e quindi **+ New Bot** (+ Nuovo bot).

    > [!div class="mx-imgBorder"]
    > ![Screenshot della pagina Home di Power Virtual Agents](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-home.png)

1. Immettere le impostazioni della knowledge base pubblicata disponibili nella pagina **Settings** (Impostazioni) del portale di [QnA Maker](https://www.qnamaker.ai/).

## <a name="topics-provided-in-the-bot"></a>Argomenti inclusi nel bot

L'agente usa la raccolta di argomenti per rispondere alle domande nell'area di interesse specificata. In questa esercitazione l'agente include molti argomenti, divisi in argomenti utente e argomenti di sistema.

> [!div class="mx-imgBorder"]
> ![Screenshot degli argomenti inclusi nell'agente](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topics-provided.png)


## <a name="create-the-system-fallback-topic"></a>Creare l'argomento di fallback di sistema

Anche se l'agente può connettersi alla knowledge base da qualsiasi argomento, questa esercitazione usa l'argomento di *fallback di sistema*. L'argomento di fallback viene usato quando l'agente non è in grado di trovare una risposta. L'agente passa il testo dell'utente all'API `GenerateAnswer` di QnA Maker, riceve la risposta dalla knowledge base e la visualizza all'utente come messaggio.

1. Nell'angolo in alto a destra del portale di [Power Virtual Agents](https://powerva.microsoft.com/#/) selezionare la pagina **Settings** (Impostazioni), ossia l'icona a forma di ingranaggio. Selezionare quindi **System Fallback** (Fallback di sistema).

    > [!div class="mx-imgBorder"]
    > ![Screenshot della voce di menu di Power Virtual Agents per il fallback di sistema](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-system-fallback.png)

1. Selezionare **+ Add** (+ Aggiungi) per aggiungere un argomento di fallback di sistema.

    > [!div class="mx-imgBorder"]
    > ![Screenshot dell'aggiunta di un argomento di fallback](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-add-fallback-topic.png)

1. Dopo aver aggiunto l'argomento, selezionare **Go to Fallback topic** (Vai all'argomento di fallback) per creare l'argomento di fallback nel canvas di creazione.

    > [!TIP]
    > Se è necessario tornare all'argomento di fallback, questo è disponibile nella sezione **Topics** (Argomenti) tra gli **argomenti di sistema**.

## <a name="use-the-authoring-canvas-to-add-an-action"></a>Usare il canvas di creazione per aggiungere un'azione

Usare il canvas di creazione di Power Virtual Agents per connettere l'argomento di fallback alla knowledge base. L'argomento inizia con il testo utente non riconosciuto. Aggiungere un'azione che passa tale testo a QnA Maker e quindi mostra la risposta come messaggio. L'ultimo passaggio per la visualizzazione di una risposta è trattato come [passaggio separato](#add-your-solutions-flow-to-power-virtual-agents) più avanti in questa esercitazione.

Questa sezione crea il flusso della conversazione sull'argomento di fallback.

1. La nuova azione di fallback potrebbe già includere elementi del flusso della conversazione. Eliminare l'elemento **Escalate** (Escalation) selezionando il menu **Options** (Opzioni).

    > [!div class="mx-imgBorder"]
    > ![Screenshot dell'argomento di fallback in Power Virtual Agents](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-fallback-topic-delete-escalate.png)

1. Selezionare il connettore **+** proveniente dalla casella **Message** (Messaggio) e quindi **Call an action** (Chiama un'azione).

    > [!div class="mx-imgBorder"]
    > ![Screenshot di Call an action (Chiama un'azione)](../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png)

1. Selezionare **Crea un flusso**. Il processo passa al portale di Power Automate.

    > [!div class="mx-imgBorder"]
    > ![Screenshot di Create a flow (Crea un flusso)](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)

## <a name="create-a-power-automate-flow-to-connect-to-your-knowledge-base"></a>Creare un flusso Power Automate per la connessione alla knowledge base

La procedura seguente crea un flusso Power Automate che:
* Accetta il testo utente in ingresso e lo invia a QnA Maker.
* Assegna la risposta principale di QnA Maker a una variabile e invia tale variabile come risposta all'agente.

1. In **Power Automate** viene automaticamente avviato il **Modello di Flow**. Nell'elemento del flusso di **Power Virtual Agents** selezionare **Modifica** per configurare la variabile di input proveniente dall'agente nella knowledge base. La variabile di input basata su testo è la domanda di testo inviata dall'utente e proveniente dall'agente.

    > [!div class="mx-imgBorder"]
    > ![Screenshot dell'opzione di Power Automate per configurare la variabile di input come stringa di testo](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable.png)

1. Aggiungere un input di testo e assegnare alla variabile il nome `InputText` e la descrizione `IncomingUserQuestion`. Questa denominazione consente di distinguere il testo di input dal testo di output creato in seguito.

    > [!div class="mx-imgBorder"]
    > ![Screenshot dell'opzione di Power Automate per configurare il nome e la descrizione della variabile di input](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable-name-and-description.png)

1. Selezionare il connettore **+** proveniente dalla casella **Power Virtual Agents** per inserire un nuovo passaggio nel flusso, prima di **Restituisci i valori a Power Virtual Agents**. Selezionare quindi **Aggiungi un'azione**.

1. Cercare `Qna` per trovare le azioni di **QnA Maker** e quindi selezionare **Generate answer** (Genera risposta).

    > [!div class="mx-imgBorder"]
    > ![Screenshot di Generate answer (Genera risposta)](../media/how-to-integrate-power-virtual-agent/generate-answer-action-selected.png)

    Le impostazioni di connessione necessarie per QnA Maker vengono visualizzate nelle impostazioni dell'azione e della domanda dell'agente.

    > [!div class="mx-imgBorder"]
    > ![Screenshot delle impostazioni di connessione necessarie](../media/how-to-integrate-power-virtual-agent/generate-answer-knowledge-base-settings.png)

1. Configurare l'azione con l'ID knowledge base e l'host e la chiave dell'endpoint. Questi valori sono disponibili nella pagina **Settings** (Impostazioni) della knowledge base, nel portale di QnA Maker.

    > [!div class="mx-imgBorder"]
    > ![Screenshot delle impostazioni della knowledge base pubblicata](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

1. Per configurare la **domanda**, selezionare la casella di testo e quindi `InputText` nell'elenco.

1. Per inserire un nuovo passaggio nel flusso, selezionare il connettore **+** proveniente dalla casella dell'azione **Generate answer** (Genera risposta). Selezionare quindi **Aggiungi un'azione**.

1. Per aggiungere una variabile per acquisire il testo della risposta restituito da `GenerateAnswer`, cercare e selezionare l'azione `Initialize variable`.

    Impostare il nome della variabile su `OutgoingQnAAnswer` e selezionare il tipo come **Stringa**. Non impostare **Valore**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot dell'inizializzazione della variabile di output](../media/how-to-integrate-power-virtual-agent/initialize-output-variable-for-qna-answer.png)

1. Per inserire un nuovo passaggio nel flusso, selezionare il connettore **+** proveniente dalla casella dell'azione **Inizializza variabile**. Selezionare quindi **Aggiungi un'azione**.

1. Per impostare l'intera risposta JSON della knowledge base sulla variabile, cercare e selezionare l'azione `Apply to each`. Selezionare `answers` di `GenerateAnswer`.

1. Per restituire solo la risposta principale, nella stessa casella **Applica a ogni** selezionare **Aggiungi un'azione**. Cercare e selezionare **Imposta variabile**.

    Nella casella **Imposta variabile** selezionare la casella di testo **Nome** e quindi **OutgoingQnAAnswer** nell'elenco.

    Selezionare la casella di testo **Valore** e quindi **Answers Answer** nell'elenco.

    > [!div class="mx-imgBorder"]
    > ![Screenshot dell'impostazione del nome e del valore per la variabile ](../media/how-to-integrate-power-virtual-agent/power-automate-flow-apply-to-each-set-variable.png)

1. Per restituire la variabile e il relativo valore, selezionare l'elemento del flusso **Restituisci i valori a Power Virtual Agents**. Selezionare quindi **Modifica** > **Aggiungere un output**. Selezionare il tipo di output **Testo** e quindi immettere `FinalAnswer` come **Titolo**. Selezionare la casella di testo **Valore** e quindi la variabile `OutgoingQnAAnswer`.

    > [!div class="mx-imgBorder"]
    > ![Screenshot dell'impostazione del valore restituito](../media/how-to-integrate-power-virtual-agent/power-automate-flow-return-value.png)

1. Selezionare **Salva** per salvare il flusso.

## <a name="create-a-solution-and-add-the-flow"></a>Creare una soluzione e aggiungere il flusso

Affinché l'agente possa trovare il flusso e connettervisi, è necessario che il flusso sia incluso in una soluzione Power Automate.

1. Sempre nel portale di Power Automate selezionare **Soluzioni** nel riquadro di spostamento sinistro.

1. Selezionare **+ Nuova soluzione**.

1. Immettere un nome visualizzato. L'elenco di soluzioni include tutte le soluzioni dell'organizzazione o dell'istituto di istruzione. Scegliere una convenzione di denominazione che consenta di filtrare solo le proprie soluzioni. Si può ad esempio aggiungere al nome della soluzione il proprio indirizzo di posta elettronica come prefisso: `jondoe-power-virtual-agent-qnamaker-fallback`.

1. Selezionare il server di pubblicazione nell'elenco di opzioni.

1. Accettare i valori predefiniti per il nome e la versione.

1. Selezionare **Crea** per completare il processo.

## <a name="add-your-flow-to-the-solution"></a>Aggiungere il flusso alla soluzione

1. Nell'elenco di soluzioni selezionare la soluzione appena creata. Dovrebbe trovarsi all'inizio dell'elenco. In caso contrario, eseguire una ricerca in base al nome dell'indirizzo di posta elettronica, che fa parte del nome della soluzione.

1. Nella soluzione selezionare **+ Aggiungi esistente** e quindi **Flusso** nell'elenco.

1. Trovare il flusso e quindi selezionare **Aggiungi** per completare il processo. Se sono presenti molti flussi, esaminare la colonna **Modificato** per trovare quello più recente.

## <a name="add-your-solutions-flow-to-power-virtual-agents"></a>Aggiungere il flusso della soluzione a Power Virtual Agents

1. Tornare nella scheda del browser contenente l'agente in Power Virtual Agents. Il canvas di creazione dovrebbe essere ancora aperto.

1. Per inserire un nuovo passaggio nel flusso, sotto la casella dell'azione **Message** (Messaggio) selezionare il connettore **+** . Selezionare quindi **Call an action** (Chiama un'azione).

1. Nella nuova azione selezionare il valore di input **UnrecognizedTriggerPhrase**. Il testo viene passato dall'agente al flusso.

    > [!div class="mx-imgBorder"]
    > ![Screenshot dell'opzione di Power Virtual Agents per selezionare la frase trigger non riconosciuta](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-select-unrecognized-trigger-phrase.png)

1. Per inserire un nuovo passaggio nel flusso, sotto la casella **Action** (Azione) selezionare il connettore **+** . Selezionare quindi **Show a message** (Mostra un messaggio).

1. Immettere il testo del messaggio `Your answer is:`. Selezionare `FinalAnswer` come variabile di contesto usando la funzione della barra degli strumenti sul posto.

    > [!div class="mx-imgBorder"]
    > ![Screenshot dell'opzione di Power Virtual Agents per immettere il testo del messaggio](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-show-message-final-answer.png)

1. Selezionare **Save** (Salva) sulla barra degli strumenti contestuale per salvare i dettagli del canvas di creazione per l'argomento.

Il canvas finale dell'agente avrà l'aspetto seguente:

> [!div class="mx-imgBorder"]
> ![Screenshot del canvas finale dell'agente](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## <a name="test-the-agent"></a>Testare l'agente

1. Nel riquadro di test attivare **Track between topics** (Traccia tra argomenti). In questo modo è possibile controllare l'avanzamento tra argomenti, nonché all'interno di un singolo argomento.

1. Testare l'agente immettendo il testo utente nell'ordine indicato di seguito. Il canvas di creazione segnala i passaggi corretti con un segno di spunta verde.

    |Ordine delle domande|Domande di test|Scopo|
    |--|--|--|
    |1|Ciao|Avviare la conversazione|
    |2|Orari di apertura|Argomento di esempio. Viene configurato automaticamente senza operazioni aggiuntive da parte dell'utente.|
    |3|Sì|In risposta a `Did that answer your question?`|
    |4|Eccellenti|In risposta a `Please rate your experience.`|
    |5|Sì|In risposta a `Can I help with anything else?`|
    |6|Cos'è una knowledge base?|Questa domanda attiva l'azione di fallback, che invia il testo alla knowledge base per la risposta. Viene quindi visualizzata la risposta. |

> [!div class="mx-imgBorder"]
> ![Screenshot del canvas finale dell'agente](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test-tracked.png)

## <a name="publish-your-bot"></a>Pubblicare il bot

Per rendere l'agente disponibile a tutti i membri dell'istituto di istruzione o dell'organizzazione, è necessario pubblicarlo.

1. Nella barra di spostamento a sinistra selezionare **Publish** (Pubblica). Selezionare quindi **Publish** (Pubblica) nella pagina.

1. Provare il bot nel sito Web demo. Cercare il collegamento sotto **Publish** (Pubblica).

    Viene aperta una nuova pagina Web con il bot. Chiedere al bot la stessa domanda di test: `What is a knowledge base?`

    > [!div class="mx-imgBorder"]
    > ![Screenshot del canvas finale dell'agente](../media/how-to-integrate-power-virtual-agent/demo-chat-bot.png)

## <a name="share-your-bot"></a>Condividere il bot

Per condividere il sito Web demo, configurarlo come canale.

1. Nella barra di spostamento a sinistra selezionare **Manage** (Gestisci) > **Channels** (Canali).

1. Selezionare **Demo website** (Sito Web demo) nell'elenco dei canali.

1. Copiare il collegamento e selezionare **Salva**. Incollare il collegamento al sito Web demo in un messaggio di posta elettronica per i membri dell'organizzazione o dell'istituto di istruzione.

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo aver completato le operazioni con la knowledge base, rimuovere le risorse di QnA Maker dal portale di Azure.

## <a name="next-step"></a>Passaggio successivo

[Ottenere analisi sulla Knowledge Base](../How-To/get-analytics-knowledge-base.md)

Altre informazioni su:
* [Power Virtual Agents](https://docs.microsoft.com/power-virtual-agents/)
* [Power Automate](https://docs.microsoft.com/power-automate/)
* [Connettore di QnA Maker](https://us.flow.microsoft.com/connectors/shared_cognitiveservicesqnamaker/qna-maker/) e [impostazioni per il connettore](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)