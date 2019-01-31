---
title: Informazioni su Custom Translator
titleSuffix: Azure Cognitive Services
description: Custom Translator offre funzionalità simili a quelle offerte da Microsoft Translator Hub per la traduzione automatica statistica (SMT, Statistical Machine Translation), ma in esclusiva per i sistemi di traduzione automatica neurale (NMT, Neural Machine Translation).
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: overview
ms.openlocfilehash: e613f660d4d1abfd45b11d71c2abfda53331cc31
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55211205"
---
# <a name="what-is-custom-translator"></a>Informazioni su Custom Translator

[Custom Translator](https://portal.customtranslator.azure.ai) è una funzionalità del servizio Microsoft Translator, che consente alle agenzie di traduzione, agli sviluppatori di app e ai provider di servizi linguistici di creare sistemi di traduzione automatica neurale (NMT). I sistemi di traduzione personalizzata si integrano senza problemi nelle applicazioni, nei flussi di lavoro e nei siti Web esistenti. [Custom Translator](https://portal.customtranslator.azure.ai/) offre funzionalità simili a quelle offerte da [Microsoft Translator Hub](https://hub.microsofttranslator.com/) per la traduzione automatica statistica (SMT, Statistical Machine Translation), ma in esclusiva per i sistemi di traduzione automatica neurale (NMT, Neural Machine Translation).

I sistemi di traduzione basati su [Custom Translator](https://portal.customtranslator.azure.ai) sono disponibili tramite la stessa, [sicura](https://cognitive.uservoice.com/knowledgebase/articles/1147537-api-and-customization-confidentiality) [API Traduzione testuale V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) di Microsoft Translator basata sul cloud e a prestazioni e scalabilità elevate che supporta miliardi di traduzioni ogni giorno. 

Custom Translator supporta più di tre dozzine di lingue ed include il mapping diretto alle lingue disponibili per NMT. Per un elenco completo, vedere [Lingue di Microsoft Translator]( https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

## <a name="features"></a>Funzionalità

Custom Translator fornisce diverse funzionalità per la creazione di sistemi di traduzione personalizzati e per il successivo accesso.

|Funzionalità  |DESCRIZIONE  |
|---------|---------|
|[Sfruttamento dei vantaggi della tecnologia di traduzione automatica neurale](https://blogs.msdn.microsoft.com/translation/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/)     |  È possibile migliorare la traduzione sfruttando la traduzione automatica neurale offerta da Custom Translator.       |
|[Creazione di sistemi in grado di riconoscere la terminologia aziendale](what-are-parallel-documents.md)     |  È possibile personalizzare e creare sistemi di traduzione con documenti paralleli, in grado di comprendere le terminologie usate in un'azienda e un settore specifico.       |
|[Uso di un dizionario per la creazione di modelli](what-is-dictionary.md)     |   Se non è disponibile alcun training set, è possibile eseguire il training di un modello usando solo i dati di un dizionario.       |
|[Collaborazione con altri utenti](how-to-manage-settings.md#share-your-workspace)     |   È possibile collaborare con il proprio team condividendo il proprio lavoro con diverse persone.     |
|[Accesso ai modelli di traduzione personalizzati](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)     |  Le applicazioni o i programmi esistenti possono accedere ai modelli di traduzione personalizzati in qualsiasi momento tramite l'API Traduzione testuale di Microsoft Translator V3.       |

## <a name="get-better-translations"></a>Traduzioni migliori

Microsoft Translator ha rilasciato [Traduzione automatica neurale (NMT)](https://blogs.msdn.microsoft.com/translation/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/) nel 2016. NMT ha offerto progressi significativi a livello di qualità della traduzione rispetto alla tecnologia di [traduzione automatica statistica (SMT)](https://en.wikipedia.org/wiki/Statistical_machine_translation) standard di settore. Poiché NMT acquisisce meglio il contesto delle frasi complete prima di tradurle, offre traduzioni di qualità superiore, più vicine al linguaggio naturale e più scorrevoli. [Custom Translator](https://portal.customtranslator.azure.ai) offre NMT per i modelli personalizzati, per consentire di ottenere traduzioni di qualità migliore.

È possibile usare documenti tradotti in precedenza per creare un sistema di traduzione. Questi documenti includono terminologia e stile specifici del dominio, migliori rispetto a un sistema di traduzione generico. Gli utenti possono caricare documenti ALIGN, PDF, LCL, HTML, HTM, XLF, TMX, XLIFF, TXT, DOCX e XLSX.

Custom Translator accetta anche dati paralleli a livello di documento, per rendere più efficaci la raccolta e la preparazione dei dati. Se gli utenti hanno accesso alle versioni dello stesso contenuto in più lingue, ma in documenti separati, Custom Translator sarà in grado di associare automaticamente frasi tra documenti.

Se vengono forniti quantità e tipi di dati appropriati per il training, non è raro riscontrare miglioramenti compresi tra 5 e 10 punti sul [punteggio BLEU](what-is-bleu-score.md) con Custom Translator.

## <a name="be-productive-and-cost-effective"></a>Produttività e convenienza

Con [Custom Translator](https://portal.customtranslator.azure.ai) il training e la distribuzione di un sistema personalizzato non richiedono alcuna competenza di programmazione. 

Usando il portale sicuro di [Custom Translator](https://portal.customtranslator.azure.ai) gli utenti possono caricare dati di training, eseguire il training dei sistemi, testare i sistemi e distribuirli in un ambiente di produzione tramite un'interfaccia utente intuitiva. Il sistema sarà quindi disponibile per l'uso su larga scala entro poche ore. Il tempo effettivo dipende dalle dimensioni dei dati di training.

È anche possibile accedere a [Custom Translator](https://portal.customtranslator.azure.ai) a livello di codice tramite un'[API dedicata](https://custom-api.cognitive.microsofttranslator.com/swagger/), attualmente disponibile in anteprima. L'API consente agli utenti di gestire la creazione o l'aggiornamento del training su base regolare tramite la rispettiva app o il rispettivo servizio Web.

Il costo per l'uso di un modello personalizzato per la traduzione di contenuto è basato sul piano tariffario per l'API Traduzione testuale dell'utente. Per informazioni dettagliate sul piano tariffario, vedere la [pagina Web dei prezzi dell'API Traduzione testuale](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/) di Servizi cognitivi.

## <a name="securely-translate-anytime-anywhere-on-all-your-apps-and-services"></a>Traduzione sicura in qualsiasi momento, ovunque, in tutte le app e i servizi

È possibile accedere ai sistemi personalizzati e integrarli senza problemi in qualsiasi prodotto o flusso di lavoro aziendale e in qualsiasi dispositivo tramite la tecnologia REST standard dell'API Traduzione testuale di Microsoft Translator.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sui [dettagli prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).

- Per informazioni sulla creazione di un modello di traduzione in Custom Translator, vedere [Avvio rapido](quickstart-build-deploy-custom-model.md).
