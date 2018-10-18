---
title: Introduzione al Servizio di riconoscimento vocale personalizzato in Azure | Microsoft Docs
description: Sottoscrivere il Servizio di riconoscimento vocale personalizzato e collegare le attività del servizio a una sottoscrizione di Azure per eseguire il training di un modello e procedere con la distribuzione.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: 4bbd4c57556fd4bfd176c915e26be4a4d198418a
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2018
ms.locfileid: "49339934"
---
# <a name="get-started-with-custom-speech-service"></a>Introduzione al Servizio di riconoscimento vocale personalizzato

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

Questo articolo illustra le principali funzionalità del Servizio di riconoscimento vocale personalizzato e spiega come compilare, distribuire e usare modelli acustici e linguistici per le esigenze della propria applicazione. Per accedere a una documentazione più esaustiva e a istruzioni dettagliate, iscriversi al portale del Servizio di riconoscimento vocale personalizzato.

## <a name="samples"></a>Esempi  
Un esempio utile per comprendere il funzionamento del servizio di riconoscimento vocale è disponibile [qui](https://github.com/Microsoft/Cognitive-Custom-Speech-Service).

## <a name="prerequisites"></a>Prerequisiti  

### <a name="subscribe-to-custom-speech-service-and-get-a-subscription-key"></a>Sottoscrivere il Servizio di riconoscimento vocale personalizzato e ottenere una chiave di sottoscrizione
Prima di provare l'esempio menzionato sopra, è necessario sottoscrivere il Servizio di riconoscimento vocale personalizzato e ottenere una chiave di sottoscrizione. Vedere [Sottoscrizioni](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/CustomSpeech) o le spiegazioni [qui](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-subscribe.md). In questa esercitazione è possibile usare sia la chiave primaria sia quella secondaria. Seguire le procedura consigliata per garantire la segretezza e la sicurezza della chiave API.

### <a name="get-the-client-library-and-example"></a>Scaricare la libreria client e l'esempio
È possibile scaricare una libreria client e un esempio tramite [SDK](https://www.microsoft.com/cognitive-services/en-us/SDK-Sample?api=bing%20speech&category=sdk). Il file ZIP scaricato deve essere estratto in una cartella a propria scelta. Molti utenti scelgono la cartella di Visual Studio 2015.

## <a name="creating-a-custom-acoustic-model"></a>Creazione di un modello acustico personalizzato
Per personalizzare il modello acustico per un particolare contesto, è necessario disporre di una raccolta di dati di riconoscimento vocale. Questa raccolta è costituita da un set di file audio con i dati per il riconoscimento vocale e da un file di testo con le trascrizioni di ciascun file audio. I dati audio devono essere rappresentativi del contesto in cui si vuole usare il servizio di riconoscimento.

Ad esempio, se si vuole usare il riconoscimento vocale in una fabbrica rumorosa, i file audio dovrebbero contenere le voci di persone che parlano in una fabbrica rumorosa.
Se si è interessati a ottimizzare le prestazioni per un unico parlante, ad esempio se si vogliono trascrivere i famosi discorsi radiofonici del presidente Franklin D. Roosevelt, i file audio devono consistere di numerosi esempi di tale voce parlante.

Una descrizione dettagliata sulla procedura di creazione di un modello acustico personalizzato è disponibile [qui](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-acoustic-model.md).

## <a name="creating-a-custom-language-model"></a>Creazione di un modello linguistico personalizzato
La procedura di creazione di un modello linguistico personalizzato è simile a quella per creare un modello acustico, con la differenza che non ci sono dati audio bensì solo testo. Il testo dovrebbe essere costituito da numerosi esempi di query o espressioni che ci si aspetta vengano pronunciate dagli utenti oppure pronunciate o digitate dagli utenti connessi all'applicazione.

Una descrizione dettagliata sulla procedura di creazione di un modello linguistico personalizzato è disponibile [qui](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-language-model.md).

## <a name="creating-a-custom-speech-to-text-endpoint"></a>Creazione di un endpoint personalizzato per il riconoscimento vocale
Dopo aver creato modelli acustici e/o linguistici personalizzati, è possibile distribuirli in un endpoint personalizzato di riconoscimento vocale. Per creare un nuovo endpoint personalizzato, fare clic su "Distribuzioni" nel menu "CRIS" all'inizio della pagina. Verrà visualizzata la tabella "Distribuzioni" degli endpoint personalizzati correnti. Se non sono stati ancora creati endpoint, la tabella sarà vuota. Le impostazioni locali correnti sono indicate nel titolo della tabella. Se si vuole creare una distribuzione per una lingua diversa, fare clic su "Change Locale" (Cambia impostazioni locali). È possibile trovare altre informazioni sulle lingue supportate nella sezione che spiega come cambiare le impostazioni locali.

Una descrizione dettagliata sulla procedura di creazione di un endpoint personalizzato di riconoscimento vocale è disponibile [qui](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-endpoint.md).

## <a name="using-a-custom-speech-endpoint"></a>Uso di un endpoint personalizzato di riconoscimento vocale
Le richieste possono essere inviate a un endpoint di riconoscimento vocale CRIS in un modo molto simile a quanto avviene con l'endpoint di riconoscimento vocale Microsoft Cognitive Services predefinito. Si noti che questi endpoint sono funzionalmente identici agli endpoint predefiniti dell'API Riconoscimento vocale. Pertanto, le stesse funzionalità disponibili tramite la libreria client o l'API REST per l'API Riconoscimento vocale sono disponibili anche per l'endpoint personalizzato.

Una descrizione dettagliata su come usare un endpoint personalizzato di riconoscimento vocale è disponibile [qui](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-use-endpoint.md).


Tenere presente che gli endpoint creati tramite CRIS possono elaborare quantità diverse di richieste simultanee a seconda del livello a cui è associata la sottoscrizione. Se vengono richiesti più riconoscimenti di quanto supportato, viene restituito il codice di errore 429 (Troppe richieste). Per altre informazioni, vedere [Prezzi di Azure](https://www.microsoft.com/cognitive-services/en-us/pricing). Esiste inoltre una quota mensile di richieste per il livello gratuito. Se si accede all'endpoint nel livello gratuito superando la quota mensile, il servizio restituisce il codice di errore 403 Accesso negato.

Il servizio presuppone che l'audio venga trasmesso in tempo reale. Se viene inviato più velocemente, la richiesta sarà considerata in esecuzione finché non sarà trascorsa la sua durata in tempo reale.

* [Overview](cognitive-services-custom-speech-home.md)
* [Domande frequenti](cognitive-services-custom-speech-faq.md)
* [Glossario](cognitive-services-custom-speech-glossary.md)
