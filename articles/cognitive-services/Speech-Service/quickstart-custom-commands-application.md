---
title: 'Guida introduttiva: creare un assistente vocale con comandi personalizzati'
titleSuffix: Azure Cognitive Services
description: Questa Guida introduttiva illustra come creare e testare un'applicazione di comandi personalizzati di base tramite speech studio.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 05b47a786fe845460177b66b5bd54cdb140c246e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87289416"
---
# <a name="create-a-voice-assistant-using-custom-commands"></a>Creare un assistente vocale con comandi personalizzati

Questa Guida introduttiva illustra come creare e testare un'applicazione di comandi personalizzati di base tramite speech studio. Sarà anche possibile accedere a questa applicazione da un'app client di Windows. I **comandi personalizzati** semplificano la creazione di app avanzate per i comandi vocali ottimizzate per esperienze di interazione vocali-First. Offre un'esperienza di creazione unificata, un modello di hosting automatico e una complessità relativamente inferiore, che ti aiuta a concentrarti sulla creazione della soluzione migliore per gli scenari di comandi vocali.

## <a name="region-availability"></a>Disponibilità a livello di area
A questo punto, i comandi personalizzati supportano le sottoscrizioni vocali create in queste aree:
* Stati Uniti Occidentali
* Stati Uniti occidentali 2
* Stati Uniti orientali
* Stati Uniti Orientali 2
* Europa settentrionale
* Europa occidentale
* Stati Uniti centro-occidentali
* Central India
* Asia orientale
* Asia sud-orientale

## <a name="prerequisites"></a>Prerequisiti

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Creare una risorsa vocale di Azure in un'area che supporta i comandi personalizzati.<span class="docon docon-navigate-external x-hidden-focus"></span></a> Per un elenco delle aree supportate, vedere la sezione relativa alla **disponibilità dell'area** precedente.
> * Scaricare il file JSON di esempio di [Smart Room Lite](https://aka.ms/speech/cc-quickstart) .
> * Scaricare la versione più recente del [client Windows Voice Assistant](https://aka.ms/speech/va-samples-wvac).

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Vai a speech studio per i comandi personalizzati

1. In un Web browser passare a [speech studio](https://speech.microsoft.com/).
1. Immettere le credenziali per accedere al portale.

   La visualizzazione predefinita è l'elenco di sottoscrizioni vocali.
   > [!NOTE]
   > Se non viene visualizzata la pagina Seleziona sottoscrizione, è possibile spostarsi in questa posizione scegliendo "risorse vocali" dal menu impostazioni della barra superiore.

1. Selezionare la sottoscrizione vocale, quindi selezionare **Vai a Studio**.
1. Selezionare **comandi personalizzati**.

   La visualizzazione predefinita è un elenco delle applicazioni dei comandi personalizzati presenti nella sottoscrizione selezionata.

## <a name="import-an-existing-application-as-a-new-custom-commands-project"></a>Importare un'applicazione esistente come nuovo progetto di comandi personalizzati

1. Selezionare **nuovo progetto** per creare un progetto.

1. Nella casella **nome** immettere il nome `Smart-Room-Lite` del progetto (o un altro elemento di propria scelta).
1. Nell'elenco **lingua** selezionare **inglese (Stati Uniti)**.
1. Selezionare **Esplora file** e nella finestra Sfoglia selezionare il **SmartRoomLite.jssu** file.

    > [!div class="mx-imgBorder"]
    > ![Creare un progetto](media/custom-commands/import-project.png)

1.  Nell'elenco di risorse per la **creazione di Luis** selezionare una risorsa di creazione. Se non sono presenti risorse di creazione valide, crearne una selezionando  **Crea nuova risorsa**di creazione Luis.

    > [!div class="mx-imgBorder"]
    > ![Creare una risorsa](media/custom-commands/create-new-luis-resource.png)
    
    
    1. Nella casella **nome risorsa** immettere il nome della risorsa.
    1. Nell'elenco **gruppo di risorse** selezionare un gruppo di risorse.
    1. Nell'elenco **percorso** selezionare una località.
    1. Nell'elenco piano **tariffario** selezionare un livello.
    
    
    > [!NOTE]
    > È possibile creare gruppi di risorse immettendo il nome del gruppo di risorse desiderato nel campo "gruppo di risorse". Il gruppo di risorse verrà creato quando si seleziona **Crea** .


1. Selezionare quindi **Crea** per creare il progetto.
1. Al termine della creazione del progetto, selezionare il progetto.
Verrà ora visualizzata una panoramica della nuova applicazione comandi personalizzati.

## <a name="try-out-some-voice-commands"></a>Provare alcuni comandi vocali
1. Selezionare **Train** nella parte superiore del riquadro destro.
1. Al termine del training, selezionare **test** e provare le espressioni seguenti:
    - Accendere la TV
    - Imposta la temperatura su 80 gradi
    - Disattiva
    - TV
    - Imposta un allarme per le 17.00

## <a name="integrate-custom-commands-application-in-an-assistant"></a>Integrare l'applicazione comandi personalizzati in un assistente
Prima di poter accedere a questa applicazione dall'esterno di speech studio, è necessario pubblicare l'applicazione. Per la pubblicazione di un'applicazione, è necessario configurare la risorsa LUIS per la stima.  

### <a name="update-prediction-luis-resource"></a>Aggiorna risorsa LUIS di stima


1. Selezionare **Settings (impostazioni** ) nel riquadro sinistro e selezionare  **risorse Luis** nel riquadro centrale.
1. Selezionare una risorsa di stima o crearne una selezionando **Crea nuova risorsa**.
1. Selezionare **Salva**.
    
    > [!div class="mx-imgBorder"]
    > ![Imposta risorse LUIS](media/custom-commands/set-luis-resources.png)

> [!NOTE]
> Poiché la risorsa di creazione supporta solo 1.000 richieste di endpoint di stima al mese, sarà necessario impostare una risorsa di stima LUIS prima di pubblicare l'applicazione dei comandi personalizzati.

### <a name="publish-the-application"></a>Pubblicare l'applicazione

Selezionare  **pubblica** nella parte superiore del riquadro destro. Una volta completata la pubblicazione, verrà visualizzata una nuova finestra. Annotare il valore dell' **ID applicazione** e della **chiave della risorsa vocale** . Questi due valori saranno necessari per poter accedere all'applicazione dall'esterno di speech studio.

In alternativa, è anche possibile ottenere questi valori selezionando **Impostazioni**  >  **generale** .

### <a name="access-application-from-client"></a>Accedere all'applicazione dal client

Nell'ambito di questo articolo, si utilizzerà il client Windows Voice Assistant scaricato come parte dei prerequisiti. Decomprimere la cartella.
1. Avviare **VoiceAssistantClient.exe**.
1. Creare un nuovo profilo di pubblicazione e immettere il valore per **profilo di connessione**. Nella sezione **General Settings (impostazioni generali** ) immettere Values **Subscription Key** (uguale al valore della **chiave della risorsa vocale** salvato durante la pubblicazione dell'applicazione), **area chiave della sottoscrizione** e **ID app comandi personalizzati**.
    > [!div class="mx-imgBorder"]
    > ![WVAC Crea profilo](media/custom-commands/create-profile.png)
1. Selezionare **Salva e applica profilo**.
1. Ora provare gli input seguenti tramite sintesi vocale/testo
    > [!div class="mx-imgBorder"]
    > ![WVAC Crea profilo](media/custom-commands/conversation.png)


> [!TIP]
> È possibile fare clic sulle voci nel **log attività** per esaminare le risposte non elaborate inviate dal servizio comandi personalizzati.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata usata un'applicazione esistente. Successivamente, nelle [sezioni procedure](how-to-custom-commands-create-application-with-simple-commands.md)si apprenderà come progettare, sviluppare, eseguire il debug, testare e integrare un'applicazione di comandi personalizzati da zero.
