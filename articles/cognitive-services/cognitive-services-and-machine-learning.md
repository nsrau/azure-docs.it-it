---
title: Servizi cognitivi e Machine Learning
titleSuffix: Azure Cognitive Services
description: Informazioni su Servizi cognitivi di Azure rispetto ad altre offerte di Azure per Machine Learning.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: diberry
ms.openlocfilehash: bae772e9fcc9f275fcf593cfd5bf9d0506a89f7c
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68594678"
---
# <a name="cognitive-services-and-machine-learning"></a>Servizi cognitivi e Machine Learning

Servizi cognitivi fornisce funzionalità di machine learning per risolvere i problemi generali, ad esempio l'analisi del testo per i sentimenti emotivi o l'analisi delle immagini per riconoscere oggetti o visi. Per usare questi servizi, non è necessario un apprendimento automatico speciale o data science Knowledge base. 

[Servizi cognitivi](welcome.md) è un gruppo di servizi, ognuno dei quali supporta funzionalità di stima generalizzate diverse. I servizi sono divisi in categorie diverse che consentono di trovare il servizio corretto. 

|Categoria servizio|Scopo|
|--|--|
|[Decisione](https://azure.microsoft.com/services/cognitive-services/directory/decision/)|Crea app che espongono raccomandazioni per un processo decisionale informato ed efficiente.|
|[Lingua](https://azure.microsoft.com/services/cognitive-services/directory/lang/)|Consenti alle tue app di elaborare il linguaggio naturale con script predefiniti, valutare i sentimenti e scoprire come riconoscere cosa vogliono gli utenti.|
|[Ricerca](https://azure.microsoft.com/services/cognitive-services/directory/search/)|Aggiungi API di ricerca Bing alle tue app e sfrutta la possibilità di combinare miliardi di pagine Web, immagini, video e notizie con una singola chiamata API.|
|[Sintesi vocale](https://azure.microsoft.com/services/cognitive-services/directory/speech/)|Converti il parlato in testo e il testo in parlato simile al linguaggio naturale. Traduci da una lingua a un'altra e consenti la verifica voce e il riconoscimento del parlante.|
|[Visione artificiale](https://azure.microsoft.com/services/cognitive-services/directory/vision/)|Riconosci, identifica, aggiungi sottotitoli, indicizza e modera immagini, video e contenuti di tipo input penna.|
||||

Usare servizi cognitivi quando:

* Può usare una soluzione generalizzata.
* Accedere alla soluzione da un'API REST di programmazione o da un SDK. 

Usare un'altra soluzione di apprendimento automatico quando si:

* È necessario scegliere l'algoritmo ed è necessario eseguire il training su dati molto specifici.

## <a name="what-is-machine-learning"></a>Che cos'è l'apprendimento automatico?

Machine Learning è un concetto in cui è possibile riunire i dati e un algoritmo per risolvere una necessità specifica. Una volta sottoposto a training i dati e l'algoritmo, l'output è un modello che è possibile utilizzare di nuovo con dati diversi. Il modello con training fornisce informazioni dettagliate in base ai nuovi dati. 

Il processo di creazione di un sistema di apprendimento automatico richiede una certa conoscenza di Machine Learning o data science.

Machine Learning viene fornito usando i [prodotti e i servizi Azure Machine Learning (AML)](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning?context=azure/machine-learning/studio/context/ml-context).

## <a name="what-is-a-cognitive-service"></a>Che cos'è un servizio cognitivo?

Un servizio cognitivo fornisce parte o tutti i componenti in una soluzione di apprendimento automatico: dati, algoritmo e modello sottoposto a training. Questi servizi sono destinati a richiedere una conoscenza generale dei dati senza che sia necessaria un'esperienza di apprendimento automatico o data science. Questi servizi forniscono sia API REST che SDK basati su linguaggio. Di conseguenza, è necessario disporre di una Knowledge base per il linguaggio di programmazione per l'utilizzo dei servizi.

## <a name="how-are-cognitive-services-and-azure-machine-learning-aml-similar"></a>Come sono simili i servizi cognitivi e Azure Machine Learning (AML)?

Entrambi hanno lo scopo di applicare l'intelligenza artificiale per migliorare le operazioni aziendali, anche se il modo in cui ognuno fornisce questo comportamento nelle rispettive offerte è diverso. 

In genere, i destinatari sono diversi:

* I servizi cognitivi sono destinati agli sviluppatori senza esperienza di machine learning.
* Azure Machine Learning è adattato ai data scientist. 

## <a name="how-is-a-cognitive-service-different-from-machine-learning"></a>In che modo un servizio cognitivo è diverso da Machine Learning?

Un servizio cognitivo fornisce un modello sottoposto a training. In questo modo, i dati e un algoritmo vengono combinati, disponibili da un'API REST o da un SDK. È possibile implementare questo servizio entro pochi minuti, a seconda dello scenario.  Un servizio cognitivo fornisce risposte a problemi generali come le frasi chiave nel testo o nell'identificazione dell'elemento nelle immagini. 

Machine Learning è un processo che in genere richiede un periodo di tempo più lungo per l'implementazione corretta. Questo tempo è dedicato alla raccolta dei dati, alla pulizia, alla trasformazione, alla selezione degli algoritmi, al training del modello e alla distribuzione per ottenere lo stesso livello di funzionalità fornito da un servizio cognitivo. Con machine learning, è possibile fornire risposte a qualsiasi tipo di problema, inclusi problemi specifici o altamente specializzati. Questi problemi di apprendimento automatico richiedono familiarità con una o più delle seguenti operazioni: oggetto, Machine Learning, data science.

## <a name="what-kind-of-data-do-you-have"></a>Quali tipi di dati sono disponibili?

I servizi cognitivi, come un gruppo di servizi, possono richiedere nessuno, alcuni o tutti i dati personalizzati per il modello sottoposto a training. 

### <a name="no-additional-training-data-required"></a>Non sono necessari dati di training aggiuntivi

I servizi che forniscono un modello completamente sottoposto a training possono essere considerati come _Black Box_. Non è necessario conoscere il modo in cui funzionano o i dati usati per eseguirne il training. È possibile importare i dati in un modello completamente sottoposto a training per ottenere una stima. 

### <a name="some-or-all-training-data-required"></a>Sono necessari alcuni o tutti i dati di training

Alcuni servizi consentono di importare i propri dati, quindi di eseguire il training di un modello. In questo modo è possibile estendere il modello usando i dati e l'algoritmo del servizio con i propri dati. L'output corrisponde alle esigenze. Quando si portano i propri dati, potrebbe essere necessario contrassegnare i dati in modo specifico per il servizio. Se, ad esempio, si esegue il training di un modello per identificare i fiori, è possibile fornire un catalogo di immagini floreali insieme alla posizione del fiore in ogni immagine per eseguire il training del modello. 

Un servizio può _consentire_ di fornire dati per migliorare i propri dati. Un servizio può _richiedere_ la fornitura di dati. 

### <a name="real-time-or-near-real-time-data-required"></a>Dati in tempo reale o quasi in tempo reale necessari

Un servizio può richiedere dati in tempo reale o quasi in tempo reale per creare un modello efficace. Questi servizi elaborano quantità significative di dati del modello. 

## <a name="service-requirements-for-the-data-model"></a>Requisiti del servizio per il modello di dati

I dati seguenti categorizzano ogni servizio in base al tipo di dati che consente o richiede.

|Servizio cognitivo|Non sono necessari dati di training|Sono disponibili alcuni o tutti i dati di training|Raccolta dati in tempo reale o quasi in tempo reale|
|--|--|--|--|
|[Rilevamento anomalie](./Anomaly-Detector/overview.md)|x|x|x|
|Ricerca Bing |x|||
|[Visione artificiale](./Computer-vision/Home.md)|x|||
|[Content Moderator](./Content-Moderator/overview.md)|x||x|
|[Visione personalizzata](./Custom-Vision-Service/home.md)||x||
|[Viso](./Face/Overview.md)|x|x||
|[Riconoscimento moduli](./form-recognizer/overview.md)||x||
|[Lettore immersivo](./immersive-reader/overview.md)|x|||
|[Riconoscimento input penna](./Ink-recognizer/overview.md)|x|x||
|[Language Understanding (LUIS)](./LUIS/what-is-luis.md)||x||
|[Personalizer](./personalizer/what-is-personalizer.md)|x*|x*|x|
|[QnA Maker](./QnAMaker/Overview/overview.md)||x||
|[Riconoscimento voce](./speaker-recognition/home.md)||x||
|[Sintesi vocale (TTS)](speech-service/text-to-speech.md)|x|x||
|[Riconoscimento vocale vocale (STT)](/speech-service/speech-to-text.md)|x|x||
|[Traduzione vocale](speech-service/speech-translation.md)|x|||
|[Analisi del testo](./text-analytics/overview.md)|x|||
|[Traduzione testuale](./translator/translator-info-overview.md)|x|||
|[Traduzione testuale-convertitore personalizzato]()||x||

\* Il Personalizzatore necessita solo dei dati di training raccolti dal servizio (in quanto Funziona in tempo reale) per valutare i criteri e i dati. Il Personalizzatore non necessita di set di impostazioni cronologici di grandi dimensioni per il training iniziale o di batch. 

## <a name="where-can-you-use-cognitive-services"></a>Dove è possibile usare servizi cognitivi?
 
I servizi vengono usati in qualsiasi applicazione che può effettuare chiamate API REST o SDK. Esempi di applicazioni includono siti Web, bot, realtà virtuale o mista, applicazioni desktop e per dispositivi mobili. 

## <a name="how-is-cognitive-search-related-to-cognitive-services"></a>In che modo la ricerca cognitiva è correlata ai servizi cognitivi?

[Ricerca di Azure](../search/search-what-is-azure-search.md) USA Servizi cognitivi per fornire questa funzionalità. Servizi cognitivi viene esposto in ricerca di Azure attraverso [competenze predefinite](../search/cognitive-search-predefined-skills.md) che racchiudono le singole API. È possibile usare una risorsa gratuita per le procedure dettagliate, ma pianificare la creazione e il fissaggio di una [risorsa fatturabile](../search/cognitive-search-attach-cognitive-services.md) per volumi più grandi.

## <a name="how-can-you-use-cognitive-services"></a>Come è possibile usare servizi cognitivi?

Ogni servizio fornisce informazioni sui dati. È possibile combinare i servizi per concatenare soluzioni come la conversione vocale (audio) in testo, tradurre il testo in molti linguaggi, quindi utilizzare le lingue tradotte per ottenere risposte da una Knowledge base. Sebbene i servizi cognitivi possano essere usati per creare soluzioni intelligenti in modo autonomo, possono anche essere combinati con progetti di apprendimento automatico tradizionali per integrare i modelli o accelerare il processo di sviluppo. 

Servizi cognitivi che forniscono modelli esportati per altri strumenti di Machine Learning:

|Servizio cognitivo|Informazioni sul modello|
|--|--|
|[Visione personalizzata](./custom-vision-service/home.md)|[Esportazione](./Custom-Vision-Service/export-model-python.md) per Tensorflow per Android, CoreML per IOS11, ONNX per Windows ml|


## <a name="next-steps"></a>Passaggi successivi

* Creare l'account del servizio cognitivo nell' [portale di Azure](cognitive-services-apis-create-account.md) o con l'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli).
* Informazioni su come eseguire l' [autenticazione](authentication.md) a un servizio cognitivo.
* Usare la [registrazione diagnostica](diagnostic-logging.md) per l'identificazione e il debug dei problemi. 
* Distribuire un servizio cognitivo in un [contenitore](cognitive-services-container-support.md)docker.
* [Aggiornamenti dei servizi](https://azure.microsoft.com/updates/?product=cognitive-services)aggiornati.
