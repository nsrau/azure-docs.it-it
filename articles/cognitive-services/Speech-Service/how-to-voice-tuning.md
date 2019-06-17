---
title: Ottimizzare l'output di sintesi vocale - servizi di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Abilitare la registrazione nel SDK di riconoscimento vocale.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 8d3e25f8217f3cc8772de9fbbb06a407008ca6f6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65593882"
---
# <a name="fine-tune-text-to-speech-output"></a>Ottimizzare l'output della sintesi vocale

Servizi di riconoscimento vocale Azure consentono di regolare la velocità, pronuncia, volume, il passo e contorno dell'output tramite la sintesi vocale [linguaggio di Markup sintesi della voce (SSML)](speech-synthesis-markup.md). SSML è un linguaggio di markup basato su XML che utilizza tag per informare che il servizio richiede le funzionalità di ottimizzazione. Il messaggio SSML viene quindi inviato nel corpo di ogni richiesta al servizio di sintesi vocale. Per semplificare il processo di personalizzazione, i servizi di riconoscimento vocale offrono ora un' [Voice ottimizzazione](https://aka.ms/voicetuning) output dello strumento che consente di controllare visivamente e ottimizzare sintesi vocale in tempo reale.

Lo strumento di ottimizzazione Voice supporta Microsoft [standard](language-support.md#standard-voices), [neurale](language-support.md#text-to-speech), e [voci personalizzate](how-to-customize-voice-font.md).

## <a name="get-started-with-the-voice-tuning-tool"></a>Iniziare con lo strumento di ottimizzazione vocale

Prima di iniziare l'output di sintesi vocale con lo strumento di ottimizzazione vocali di ottimizzazione, è necessario effettuare le seguenti operazioni:

1. Creare un [account Microsoft gratuito](https://account.microsoft.com/account) se non ne hai già uno.
2. Creare un [account Azure gratuito](https://azure.microsoft.com/free/) se non ne hai già uno. Fare clic su **inizia gratis**e creare un nuovo account di Azure con l'account Microsoft.

3. Creare una sottoscrizione di servizi di riconoscimento vocale nel portale di Azure. Istruzioni dettagliate per la [come creare una risorsa di riconoscimento vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure) sono disponibili.
   >[!NOTE]
   >Quando si crea una risorsa di riconoscimento vocale nel portale di Azure, le informazioni di località di Azure devono corrispondere all'area di sintesi vocale. Neural sintesi vocale supporta un set di subset di posizioni di Azure. Per un elenco completo di supporto, vedere [aree](regions.md#text-to-speech).

   >[!NOTE]
   >È necessario disporre di un F0 o una chiave S0 creata nel portale di Azure prima di poter usare il servizio. Vocali Tuning **non** supportano il [chiave di valutazione gratuita di 30 giorni](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started?branch=release-build-cogserv-speech-services#free-trial).

4. Accedi per il [Voice ottimizzazione](https://aka.ms/voicetuning) portale e collegare la sottoscrizione di servizi di riconoscimento vocale. Scegliere una singola sottoscrizione di servizi di riconoscimento vocale e quindi creare un progetto.
5. Selezionare **nuova ottimizzazione**. Seguire quindi questa procedura:

   * Individuare e selezionare **tutte le sottoscrizioni**.  
   * Selezionare **Connect existing subscription** (Connetti sottoscrizione esistente).  
     ![Connettere una sottoscrizione esistente](./media/custom-voice/custom-voice-connect-subscription.png).
   * Immettere la chiave di sottoscrizione di servizi di riconoscimento vocale di Azure e quindi selezionare **Add**. Le chiavi di sottoscrizione sono disponibili nel portale di personalizzazione di riconoscimento vocale dal [pagina di sottoscrizione](https://go.microsoft.com/fwlink/?linkid=2090458). È anche possibile leggere le chiavi nel riquadro di gestione delle risorse nel [portale di Azure](https://portal.azure.com/).
   * Se hai più sottoscrizioni di servizi di riconoscimento vocale che si intende usare, ripetere questi passaggi per ogni sottoscrizione.

## <a name="customize-the-text-to-speech-output"></a>Personalizzare l'output di sintesi vocale

Ora che è stato creato gli account e collegato la sottoscrizione, è possibile avviare l'ottimizzazione dell'output di sintesi vocale.

1. Scegliere una voce.
2. Immettere il testo da modificare.
3. Prima di iniziare ad apportare modifiche, riprodurre l'audio per acquisire familiarità con l'output.
4. Selezionare la parola/frase che si desidera ridefinire e inizia a sperimentare con diverse funzioni basate su SSML.

>[!TIP]
> Per informazioni dettagliate sulla regolazione SSML e l'ottimizzazione dell'output vocale, vedere [linguaggio di Markup sintesi della voce (SSML)](speech-synthesis-markup.md).

## <a name="limitations"></a>Limitazioni

Ottimizzazione vocale neurale è leggermente diverso rispetto all'ottimizzazione per la voce Standard e personalizzate.

* Intonazione non è supportata per la voce neurale.
* Funzionalità del passo e volume funzionano solo con frasi complete. Queste funzionalità non sono disponibili a livello di word.
* Per la frequenza, alcune voci neurali possono essere ottimizzate basate su parole, mentre altri richiedono di selezionare frasi intere.

> [!TIP]
> Lo strumento di ottimizzazione vocali fornisce informazioni contestuali sulla funzionalità e l'ottimizzazione.

## <a name="next-steps"></a>Passaggi successivi
* [Creare una risorsa di riconoscimento vocale in Azure](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure)
* [Avviare l'ottimizzazione vocale](https://speech.microsoft.com/app.html#/VoiceTuning)
* [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md)
