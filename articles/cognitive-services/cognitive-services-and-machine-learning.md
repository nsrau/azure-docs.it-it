---
title: Servizi cognitivi e Machine Learning
titleSuffix: Azure Cognitive Services
description: Informazioni su Servizi cognitivi di Azure rispetto ad altre offerte di Azure per Machine Learning.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: diberry
ms.openlocfilehash: cde505e4c95de9b9693a0e9d260d7fa84f3e905b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75531480"
---
# <a name="cognitive-services-and-machine-learning"></a>Servizi cognitivi e Machine Learning

Servizi cognitivi offre funzionalità di apprendimento automatico per risolvere problemi generali, ad esempio l'analisi del testo per il sentiment emotivo o l'analisi delle immagini per riconoscere oggetti o volti. Per usare questi servizi non sono necessarie conoscenze speciali di apprendimento automatico o di analisi scientifica dei dati. 

[Servizi cognitivi](welcome.md) è un gruppo di servizi, ognuno dei quali supporta funzionalità di stima diverse e generalizzate. I servizi sono suddivisi in diverse categorie per aiutarti a trovare il servizio giusto. 

|Categoria di servizio|Scopo|
|--|--|
|[Decisione](https://azure.microsoft.com/services/cognitive-services/directory/decision/)|Crea app che espongono raccomandazioni per un processo decisionale informato ed efficiente.|
|[Lingua](https://azure.microsoft.com/services/cognitive-services/directory/lang/)|Permetti alle tue app di elaborare il linguaggio naturale con script predefiniti, valutare il sentiment e riconoscere i desideri degli utenti.|
|[Ricerca](https://azure.microsoft.com/services/cognitive-services/directory/search/)|Aggiungi le API di Ricerca Bing alle tue app e sfrutta la possibilità di esaminare miliardi di pagine Web, immagini, video e notizie con una singola chiamata all'API.|
|[Sintesi vocale](https://azure.microsoft.com/services/cognitive-services/directory/speech/)|Converti il parlato in testo e il testo in parlato simile al linguaggio naturale. Traduci da una lingua a un'altra e consenti la verifica voce e il riconoscimento del parlante.|
|[Visione](https://azure.microsoft.com/services/cognitive-services/directory/vision/)|Riconosci, identifica, aggiungi sottotitoli, indicizza e modera immagini, video e contenuti di tipo input penna.|
||||

Utilizzare i servizi cognitivi quando:

* Può utilizzare una soluzione generalizzata.
* Accedi alla soluzione da un'API REST di programmazione o da un SDK. 

Usare un'altra soluzione di apprendimento automatico quando:Use another machine-learning solution when you:

* Necessità di scegliere l'algoritmo e la necessità di allenarsi su dati molto specifici.

## <a name="what-is-machine-learning"></a>Che cos'è l'apprendimento automatico?

L'apprendimento automatico è un concetto in cui si riuniscono i dati e un algoritmo per risolvere un'esigenza specifica. Dopo il training dei dati e dell'algoritmo, l'output è un modello che è possibile usare nuovamente con dati diversi. Il modello sottoposto a training fornisce informazioni dettagliate basate sui nuovi dati. 

Il processo di creazione di un sistema di apprendimento automatico richiede una certa conoscenza dell'apprendimento automatico o dell'analisi scientifica dei dati.

L'apprendimento automatico viene fornito tramite [i prodotti e i servizi di Azure Machine Learning (AML).](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning?context=azure/machine-learning/studio/context/ml-context)

## <a name="what-is-a-cognitive-service"></a>Che cos'è un servizio cognitivo?

Un servizio cognitivo fornisce parte o tutti i componenti in una soluzione di apprendimento automatico: dati, algoritmo e modello sottoposto a training. Questi servizi hanno lo scopo di richiedere conoscenze generali sui dati senza dover esperienza con l'apprendimento automatico o l'analisi scientifica dei dati. Questi servizi forniscono API REST e SDK basati sul linguaggio. Di conseguenza, è necessario disporre di conoscenze del linguaggio di programmazione per utilizzare i servizi.

## <a name="how-are-cognitive-services-and-azure-machine-learning-aml-similar"></a>In che modo i servizi cognitivi e Azure Machine Learning (AML) sono simili?

Entrambi hanno l'obiettivo finale di applicare l'intelligenza artificiale (AI) per migliorare le operazioni di business, anche se il modo in cui ognuno fornisce questo nelle rispettive offerte è diverso. 

Generalmente, il pubblico è diverso:

* Servizi cognitivi sono per gli sviluppatori senza esperienza di apprendimento automatico.
* Azure Machine Learning è personalizzato per i data scientist. 

## <a name="how-is-a-cognitive-service-different-from-machine-learning"></a>In che modo un servizio cognitivo è diverso dall'apprendimento automatico?

Un servizio cognitivo fornisce un modello di training per l'utente. In questo modo i dati e un algoritmo vengono uniti, disponibili da un'API REST o da un SDK. È possibile implementare questo servizio in pochi minuti, a seconda dello scenario.  Un servizio cognitivo fornisce risposte a problemi generali, ad esempio frasi chiave nel testo o l'identificazione di elementi nelle immagini. 

L'apprendimento automatico è un processo che in genere richiede un periodo di tempo più lungo per implementare correttamente. Questo tempo viene dedicato alla raccolta, alla pulizia, alla trasformazione, alla selezione degli algoritmi, al training dei modelli e alla distribuzione dei dati per ottenere lo stesso livello di funzionalità fornito da un servizio cognitivo. Con l'apprendimento automatico, è possibile fornire risposte a problemi altamente specializzati e/o specifici. I problemi di apprendimento automatico richiedono familiarità con l'argomento specifico e i dati del problema in esame, nonché l'esperienza nella scienza dei dati.

## <a name="what-kind-of-data-do-you-have"></a>Che tipo di dati hai?

Servizi cognitivi, come gruppo di servizi, può richiedere nessuno, alcuni o tutti i dati personalizzati per il modello sottoposto a training. 

### <a name="no-additional-training-data-required"></a>Non sono necessari dati di training aggiuntivi

I servizi che forniscono un modello con training completo possono essere considerati come una _casella nera._ Non è necessario sapere come funzionano o quali dati sono stati utilizzati per addestrarli. Portare i dati a un modello di training completo per ottenere una stima. 

### <a name="some-or-all-training-data-required"></a>Alcuni o tutti i dati di training necessari

Alcuni servizi consentono di portare i propri dati, quindi eseguire il training di un modello. Ciò consente di estendere il modello utilizzando i dati e l'algoritmo del Servizio con i propri dati. L'output corrisponde alle proprie esigenze. Quando si portano i propri dati, potrebbe essere necessario taggare i dati in un modo specifico per il servizio. Ad esempio, se si esegue il training di un modello per identificare i fiori, è possibile fornire un catalogo di immagini di fiori insieme alla posizione del fiore in ogni immagine per addestrare il modello. 

Un servizio può _consentire_ di fornire dati per migliorare i propri dati. Un servizio potrebbe _richiedere_ la fornitura di dati. 

### <a name="real-time-or-near-real-time-data-required"></a>Dati in tempo reale o quasi in tempo reale richiesti

Un servizio potrebbe richiedere dati in tempo reale o quasi in tempo reale per creare un modello efficace. Questi servizi elaborano quantità significative di dati del modello. 

## <a name="service-requirements-for-the-data-model"></a>Requisiti di servizio per il modello di datiService requirements for the data model

I dati seguenti classificano ogni servizio in base al tipo di dati che consente o richiede.

|Servizio cognitivo|Nessun dato di training richiesto|Fornisci alcuni o tutti i dati di training|Raccolta di dati in tempo reale o quasi in tempo reale|
|--|--|--|--|
|[Rilevamento anomalie](./Anomaly-Detector/overview.md)|x|x|x|
|Ricerca Bing |x|||
|[Visione artificiale](./Computer-vision/Home.md)|x|||
|[Moderatore dei contenuti](./Content-Moderator/overview.md)|x||x|
|[Visione personalizzata](./Custom-Vision-Service/home.md)||x||
|[Viso](./Face/Overview.md)|x|x||
|[Riconoscimento modulo](./form-recognizer/overview.md)||x||
|[Strumento di lettura immersiva](./immersive-reader/overview.md)|x|||
|[Riconoscimento input penna](./Ink-recognizer/overview.md)|x|x||
|[Language Understanding (LUIS)](./LUIS/what-is-luis.md)||x||
|[Personalizer](./personalizer/what-is-personalizer.md)|x*|x*|x|
|[Creatore QnA](./QnAMaker/Overview/overview.md)||x||
|[Riconoscitore altoparlante](./speaker-recognition/home.md)||x||
|[Sintesi vocale (TTS)](speech-service/text-to-speech.md)|x|x||
|[Sintesi vocale (STT)](speech-service/speech-to-text.md)|x|x||
|[Traduzione vocale](speech-service/speech-translation.md)|x|||
|[Analisi del testo](./text-analytics/overview.md)|x|||
|[Testo traduttore](./translator/translator-info-overview.md)|x|||
|[Traduttore Testo - Traduttore personalizzato](./translator/custom-translator/overview.md)||x||

Il personalizer ha bisogno solo dei dati di training raccolti dal servizio (in quanto opera in tempo reale) per valutare le norme e i dati. Personalizer non necessita di set di dati cronologici di grandi dimensioni per il training iniziale o batch. 

## <a name="where-can-you-use-cognitive-services"></a>Dove è possibile utilizzare i servizi cognitivi?
 
I servizi vengono usati in qualsiasi applicazione in grado di effettuare chiamate all'API REST o SDK. Esempi di applicazioni includono siti web, bot, realtà virtuale o mista, applicazioni desktop e mobili. 

## <a name="how-is-azure-cognitive-search-related-to-cognitive-services"></a>In che modo Ricerca cognitiva di Azure è correlata ai servizi cognitivi?

[Ricerca cognitiva di Azure](../search/search-what-is-azure-search.md) è un servizio di ricerca cloud separato che usa facoltativamente Servizi cognitivi per aggiungere l'elaborazione di immagini e linguagginaturali ai carichi di lavoro di indicizzazione. Servizi cognitivi è esposto in Ricerca cognitiva di Azure tramite [competenze incorporate](../search/cognitive-search-predefined-skills.md) che eseguono il wrapping di singole API. È possibile usare una risorsa gratuita per le procedure dettagliate, ma pianificare la creazione e il collegamento di una [risorsa fatturabile](../search/cognitive-search-attach-cognitive-services.md) per volumi di dimensioni maggiori.

## <a name="how-can-you-use-cognitive-services"></a>Come si possono utilizzare i Servizi cognitivi?

Ogni servizio fornisce informazioni sui dati. È possibile combinare i servizi insieme per concatenare soluzioni come la conversione vocale (audio) in testo, la traduzione del testo in molte lingue, quindi utilizzando le lingue tradotte per ottenere risposte da una Knowledge Base. Mentre i servizi cognitivi possono essere utilizzati per creare soluzioni intelligenti da soli, possono anche essere combinati con i progetti di apprendimento automatico tradizionali per integrare i modelli o accelerare il processo di sviluppo. 

Servizi cognitivi che forniscono modelli esportati per altri strumenti di apprendimento automatico:Cognitive Services that provide exported models for other machine learning tools:

|Servizio cognitivo|Informazioni sul modello|
|--|--|
|[Visione personalizzata](./custom-vision-service/home.md)|[Esportazione](./Custom-Vision-Service/export-model-python.md) per Tensorflow per Android, CoreML per iOS11, ONNX per Windows ML|

## <a name="learn-more"></a>Altre informazioni

* [Guida all'architettura - Quali sono i prodotti di apprendimento automatico di Microsoft?](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning)
* [Apprendimento automatico - Introduzione al deep learning e all'apprendimento automatico](../machine-learning/concept-deep-learning-vs-machine-learning.md)

## <a name="next-steps"></a>Passaggi successivi

* Creare l'account del servizio cognitivo nel portale di Azure o con [l'interfaccia della riga di comando](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli)di Azure.Create your Cognitive Service account in the Azure [portal](cognitive-services-apis-create-account.md) or with Azure CLI .
* Informazioni su come [eseguire l'autenticazione](authentication.md) a un servizio cognitivo.
* Utilizzare [la registrazione diagnostica](diagnostic-logging.md) per l'identificazione e il debug dei problemi. 
* Distribuire un servizio cognitivo in un [contenitore](cognitive-services-container-support.md)Docker .
* Resta aggiornato con [gli aggiornamenti](https://azure.microsoft.com/updates/?product=cognitive-services)del servizio .
