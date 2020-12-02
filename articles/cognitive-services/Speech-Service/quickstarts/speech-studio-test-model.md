---
title: Testare un modello usando file audio - Speech Studio
titleSuffix: Azure Cognitive Services
description: Questa procedura illustra come usare Speech Studio per testare il riconoscimento vocale in un file audio.
services: cognitive-services
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/04/2020
ms.author: v-demjoh
ms.openlocfilehash: 572b3b3459e1d837130f3c987d45ee45629f37ad
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96485077"
---
# <a name="test-a-model-using-an-audio-file-in-speech-studio"></a>Testare un modello usando un file audio in Speech Studio

In questa procedura si usa Speech Studio per convertire il parlato di un file audio in testo. Speech Studio consente di testare, confrontare, migliorare e distribuire modelli di riconoscimento vocale usando testo correlato, audio con trascrizioni etichettate manualmente e linee guida di pronuncia fornite dall'utente.

## <a name="prerequisites"></a>Prerequisiti

Prima di usare il portale del Servizio di riconoscimento vocale, [seguire queste istruzioni per creare un account Azure e sottoscrivere il servizio Voce](../custom-speech-overview.md#set-up-your-azure-account). Questa sottoscrizione unificata consente di accedere a riconoscimento vocale, sintesi vocale, traduzione vocale e il portale di Riconoscimento vocale personalizzato.

## <a name="download-an-audio-file"></a>Scaricare un file audio

Seguire questa procedura per scaricare un file audio contenente il parlato e comprimerlo in un file ZIP.

1. Per scaricare il f **[ile WAV di esempio da questo collegamento](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav)** , fare clic con il pulsante destro sul collegamento e scegliere **Salva destinazione con nome**. Fare clic su **Salva** per scaricare il file `whatstheweatherlike.wav`.
2. In Esplora file o in una finestra del terminale con uno strumento di compressione in file ZIP, creare un file ZIP denominato `whatstheweatherlike.zip` contenente il file `whatstheweatherlike.wav` scaricato. In Windows è possibile aprire Esplora risorse, passare alla cartella `Downloads`, fare clic con il pulsante destro del mouse su `whatstheweatherliike.wav`, scegliere **Invia a**, fare clic su **Cartella compressa** e premere INVIO per accettare il nome file predefinito.

## <a name="create-a-project-in-the-custom-speech-portal"></a>Creare un progetto nel portale di Riconoscimento vocale personalizzato

Seguire questa procedura per creare un progetto contenente il file ZIP di un file audio.

1. Aprire [Speech Studio](https://speech.microsoft.com/) e fare clic su **Nuovo progetto**. Digitare un nome per il progetto e fare clic su **Crea**. Il progetto verrà visualizzato nell'elenco di Riconoscimento vocale personalizzato.
2. Fare clic sul nome del progetto. Nella scheda Dati fare clic su **Carica dati**.
3. Per impostazione predefinita, il tipo di dati di riconoscimento vocale è **Solo audio**, quindi fare clic su **Avanti**.
4. Assegnare un nome al nuovo set di dati di riconoscimento vocale `MyZipOfAudio` e fare clic su **Avanti**.
5. Fare clic su **Sfoglia file...** , passare al file `whatstheweatherlike.zip` e fare clic su **Apri**.
6. Fare clic sul pulsante **Upload** . Il browser carica il file ZIP in Speech Studio e Speech Studio elabora il contenuto.

## <a name="test-a-model"></a>Test di un modello

Dopo che Speech Studio ha completato l'elaborazione del file ZIP, è possibile riprodurre l'audio di origine mentre si esamina la trascrizione per cercare errori o omissioni. Per esaminare la qualità della trascrizione nel browser, seguire questa procedura.

1. Fare clic sulla scheda **Test** e quindi su **Aggiungi test**.
2. In questo test verrà controllata la qualità dei dati solo audio, quindi fare clic su **Avanti** per accettare questo tipo di test.
3. Assegnare al test il nome `MyModelTest` e fare clic su **Avanti**.
4. Fare clic sul pulsante di opzione a sinistra di `MyZipOfAudio`, quindi fare clic su **Avanti**.
5. Per impostazione predefinita, nell'elenco a discesa **Modello 1** viene proposto il modello di riconoscimento più recente, quindi fare clic su **Crea**. Dopo l'elaborazione del contenuto del set di dati audio, lo stato del test passerà a **Riuscito**.
6. Fare clic su **MyModelTest**. Verranno visualizzati i risultati del riconoscimento vocale. Fare clic sul triangolo che punta verso destra all'interno del cerchio per ascoltare l'audio e confrontarlo con il testo nel cerchio.

## <a name="download-detailed-results"></a>Scaricare i risultati dettagliati

È possibile scaricare i file che descrivono le trascrizioni in modo molto più dettagliato. I file includono le parti del discorso lessicali nei file audio e i file JSON che contengono i dettagli relativi a offset, durata e attendibilità della trascrizione per ogni parola. Per visualizzare i file, seguire questa procedura.

1. Fare clic su **Download**.
2. Nella finestra di dialogo di download deselezionare **Audio** e fare clic su **Scarica**.
3. Decomprimere il file ZIP scaricato ed esaminare i file estratti.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come migliorare l'accuratezza del riconoscimento vocale tramite il [training di un modello personalizzato](../how-to-custom-speech-test-and-train.md).