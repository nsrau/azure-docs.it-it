---
title: Ottimizzazione dell'output di sintesi vocale
titleSuffix: Azure Cognitive Services
description: Abilitare la registrazione nell'SDK di riconoscimento vocale.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 4cf2338d76ce31f44eaf3fb235e5f8796602d819
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562745"
---
# <a name="fine-tune-text-to-speech-output"></a>Ottimizzare l'output della sintesi vocale

I servizi di riconoscimento vocale di Azure consentono di regolare la velocità, la pronuncia, il volume, il pitch e il contorno dell'output da sintesi vocale usando [SSML (Speech Synthesis Markup Language)](speech-synthesis-markup.md). SSML è un linguaggio di markup basato su XML che usa tag per informare il servizio sulla funzionalità che richiede l'ottimizzazione. Il messaggio SSML viene quindi inviato nel corpo di ogni richiesta al servizio di sintesi vocale. Per semplificare il processo di personalizzazione, i servizi di riconoscimento vocale offrono ora uno strumento di [ottimizzazione vocale](https://aka.ms/voicetuning) che consente di ispezionare visivamente e ottimizzare gli output da testo a riconoscimento vocale in tempo reale.

Lo strumento di ottimizzazione vocale supporta le voci [standard](language-support.md#standard-voices), [neurale](language-support.md#text-to-speech)e [personalizzate](how-to-customize-voice-font.md)Microsoft.

## <a name="get-started-with-the-voice-tuning-tool"></a>Introduzione allo strumento di ottimizzazione vocale

Prima di iniziare a ottimizzare l'output di sintesi vocale con lo strumento di ottimizzazione vocale, è necessario completare i passaggi seguenti:

1. Se non si dispone già di un account Microsoft, è possibile crearne uno [gratuito](https://account.microsoft.com/account) .
2. Se non si dispone già di un account Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) . Fare clic su **Avvia gratis**e creare un nuovo account Azure usando il account Microsoft.

3. Creare una sottoscrizione di servizi vocali nella portale di Azure. Sono disponibili istruzioni dettagliate per [la creazione di una risorsa di sintesi vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure) .
   >[!NOTE]
   >Quando si crea una risorsa di sintesi vocale nella portale di Azure, le informazioni sulla località di Azure devono corrispondere all'area della voce TTS. La voce neurale TTS supporta un sottoinsieme di località di Azure. Per un elenco completo del supporto, vedere [aree](regions.md#text-to-speech).

   >[!NOTE]
   >Prima di poter usare il servizio, è necessario che nel portale di Azure sia stata creata una chiave F0 o una chiave S0. L'ottimizzazione vocale **non** supporta la [chiave di valutazione gratuita di 30 giorni](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started?branch=release-build-cogserv-speech-services#free-trial).

4. Accedere al portale di [ottimizzazione vocale](https://aka.ms/voicetuning) e connettere la sottoscrizione ai servizi vocali. Scegliere una singola sottoscrizione di servizi vocali e quindi creare un progetto.
5. Selezionare **nuova ottimizzazione**. Seguire quindi questa procedura:

   * Individuare e selezionare **tutte le sottoscrizioni**.  
   * Selezionare **Connect existing subscription** (Connetti sottoscrizione esistente).  
     ![Connettere una sottoscrizione](./media/custom-voice/custom-voice-connect-subscription.png)esistente.
   * Immettere la chiave di sottoscrizione di servizi vocali di Azure, quindi selezionare **Aggiungi**. Le chiavi di sottoscrizione sono disponibili nel portale di personalizzazione vocale dalla [pagina sottoscrizione](https://go.microsoft.com/fwlink/?linkid=2090458). È anche possibile ottenere le chiavi dal riquadro Gestione risorse nel [portale di Azure](https://portal.azure.com/).
   * Se si prevede di usare più di una sottoscrizione di servizi vocali, ripetere questi passaggi per ogni sottoscrizione.

## <a name="customize-the-text-to-speech-output"></a>Personalizzare l'output di sintesi vocale

Ora che sono stati creati gli account e collegato la sottoscrizione, è possibile iniziare a ottimizzare l'output di sintesi vocale.

1. Scegliere una voce.
2. Immettere il testo che si desidera modificare.
3. Prima di iniziare a apportare modifiche, riprodurre l'audio per ottenere un'idea dell'output.
4. Selezionare la parola/frase che si vuole perfezionare e iniziare a sperimentare con diverse funzioni basate su SSML.

>[!TIP]
> Per informazioni dettagliate sulla modifica di SSML e sull'ottimizzazione dell'output vocale, vedere [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md).

## <a name="limitations"></a>Limitazioni

L'ottimizzazione della voce neurale è leggermente diversa rispetto all'ottimizzazione per le voci standard e personalizzate.

* L'intonazione non è supportata per le voci neurali.
* Le funzionalità di pitch e volume funzionano solo con frasi complete. Queste funzionalità non sono disponibili a livello di parola.
* Per la frequenza, alcune voci neurali possono essere ottimizzate in base alle parole, mentre altre richiedono la selezione di frasi intere.

> [!TIP]
> Lo strumento di ottimizzazione vocale fornisce informazioni contestuali sulle funzionalità e sull'ottimizzazione.

## <a name="next-steps"></a>Passaggi successivi
* [Creare una risorsa vocale in Azure](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure)
* [Avvia ottimizzazione voce](https://speech.microsoft.com/app.html#/VoiceTuning)
* [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md)
