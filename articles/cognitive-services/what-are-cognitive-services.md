---
title: Informazioni su Servizi cognitivi di Azure
titleSuffix: Azure Cognitive Services
description: I Servizi cognitivi rendono accessibile l'intelligenza artificiale a tutti gli sviluppatori senza richiedere esperienza di Machine Learning e Data Science. È sufficiente eseguire una chiamata API dall'applicazione per aggiungere la possibilità di vedere (ricerca e riconoscimento immagini avanzati), ascoltare, parlare, cercare e prendere decisioni nelle app.
services: cognitive-services
author: nitinme
manager: nitinme
keywords: servizi cognitivi, intelligenza cognitiva, soluzioni cognitive, servizi di intelligenza artificiale, riconoscimento cognitivo, funzionalità cognitive
ms.service: cognitive-services
ms.subservice: ''
ms.topic: overview
ms.date: 10/22/2020
ms.author: nitinme
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 82531466c80917087ca007900ca79b3485f38a21
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546263"
---
# <a name="what-are-azure-cognitive-services"></a>Informazioni su Servizi cognitivi di Azure

Servizi cognitivi di Azure sono servizi cloud con API REST ed SDK delle librerie client disponibili per aiutare a integrare l'intelligenza cognitiva nelle applicazioni. È possibile aggiungere funzionalità cognitive nelle applicazioni senza la necessità di competenze di intelligenza artificiale o data science. I Servizi cognitivi di Azure comprendono vari servizi di intelligenza artificiale che consentono di creare soluzioni cognitive in grado di visualizzare, ascoltare, parlare, comprendere e persino prendere decisioni.

## <a name="categories-of-cognitive-services"></a>Categorie di Servizi cognitivi

Il catalogo dei servizi cognitivi che supportano il riconoscimento cognitivo è suddiviso in cinque categorie principali:

* Visione
* Sintesi vocale
* Lingua
* Decisione
* Ricerca

Le sezioni seguenti di questo articolo contengono un elenco dei servizi che fanno parte di questi cinque categorie.

## <a name="vision-apis"></a>API Visione

|Nome servizio|Descrizione del servizio|
|:-----------|:------------------|
|[Visione artificiale](https://docs.microsoft.com/azure/cognitive-services/computer-vision/ "Visione artificiale")|Il servizio Visione artificiale consente di accedere ad algoritmi cognitivi avanzati per l'elaborazione delle immagini e la restituzione di informazioni.|
|[Servizio Visione personalizzata](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/home "Servizio visione artificiale personalizzato")|Il Servizio visione artificiale personalizzato consente di creare classificatori di immagini personalizzati.|
|[Viso](https://docs.microsoft.com/azure/cognitive-services/face/ "Viso")| Il servizio Viso fornisce l'accesso ad algoritmi viso avanzati consentendo il rilevamento e il riconoscimento degli attributi del viso.|
|[Riconoscimento modulo](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/ "Riconoscimento modulo")|Il riconoscimento modulo identifica ed estrae le coppie chiave-valore e i dati delle tabelle dai documenti dei moduli, quindi restituisce dati strutturati che includono le relazioni nel file originale.|
|[Riconoscimento input penna](https://docs.microsoft.com/azure/cognitive-services/ink-recognizer/ "Riconoscimento input penna") (in fase di ritiro)|Il riconoscimento input penna consente di riconoscere e analizzare i dati del tratto di input penna digitale, le forme e il contenuto scritto a mano e di restituire una struttura di documento con tutte le entità riconosciute.|
|[Video Indexer](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview "Video Indexer")|Video Indexer consente di estrarre informazioni dettagliate dai video.|

## <a name="speech-apis"></a>Speech API

|Nome servizio|Descrizione del servizio|
|:-----------|:------------------|
|[Servizio di riconoscimento vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/ "Servizio Voce")|Il servizio Voce aggiunge funzionalità di riconoscimento vocale alle applicazioni. Il servizio Voce include varie capacità, come la conversione della voce in testo scritto, la sintesi vocale, la traduzione vocale e altre ancora.|
<!--
|[Speaker Recognition API](https://docs.microsoft.com/azure/cognitive-services/speaker-recognition/home "Speaker Recognition API") (Preview)|The Speaker Recognition API provides algorithms for speaker identification and verification.|
|[Bing Speech](https://docs.microsoft.com/azure/cognitive-services/speech/home "Bing Speech") (Retiring)|The Bing Speech API provides you with an easy way to create speech-enabled features in your applications.|
|[Translator Speech](https://docs.microsoft.com/azure/cognitive-services/translator-speech/ "Translator Speech") (Retiring)|Translator Speech is a machine translation service.|
-->
## <a name="language-apis"></a>API lingua

|Nome servizio|Descrizione del servizio|
|:-----------|:------------------|
|[Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/ "Language Understanding")|Il servizio Language Understanding consente all'applicazione di comprendere la finalità espressa da una persona con le proprie parole.|
|[QnA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/index "QnA Maker")|QnA Maker consente di creare una servizio di domande e risposte da contenuto semi-strutturato.|
|[Analisi del testo](https://docs.microsoft.com/azure/cognitive-services/text-analytics/ "Text Analytics")| Analisi del testo fornisce l'elaborazione del linguaggio naturale su testo non elaborato per l'analisi del sentiment, l'estrazione delle frasi chiave e il rilevamento della lingua.|
|[Translator](https://docs.microsoft.com/azure/cognitive-services/translator/ "Funzione di conversione")|Translator fornisce la traduzione automatica del testo quasi in tempo reale.|
| [Strumento di lettura immersiva](https://docs.microsoft.com/azure/cognitive-services/immersive-reader/ "Strumento di lettura immersiva") | Lo Strumento di lettura immersiva aggiunge capacità di lettura e comprensione dello schermo alle applicazioni. |

## <a name="decision-apis"></a>API Decisione

|Nome servizio|Descrizione del servizio|
|:-----------|:------------------|
|[Rilevamento anomalie](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/ "Rilevamento anomalie") |Rilevamento anomalie permette di monitorare e rilevare le anomalie nei dati di serie temporali.|
|[Content Moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview "Content Moderator")|Content Moderator fornisce il monitoraggio per l'individuazione di contenuto potenzialmente offensivo, indesiderato e rischioso.|
|[Advisor metriche](https://docs.microsoft.com/azure/cognitive-services/metrics-advisor) (anteprima) | Advisor metriche consente di personalizzare il rilevamento anomalie sui dati di serie temporali con più variabili e offre un portale Web completo per facilitare l'uso del servizio.|
|[Personalizza esperienze](https://docs.microsoft.com/azure/cognitive-services/personalizer/ "Personalizza esperienze")|Personalizza esperienze consente di scegliere la migliore esperienza da mostrare agli utenti, apprendendo dal loro comportamento in tempo reale.|

## <a name="search-apis"></a>API di ricerca

> [!NOTE]
> [Ricerca cognitiva di Azure](https://docs.microsoft.com/azure/search/), pur usando Servizi cognitivi per alcune attività, è una tecnologia di ricerca diversa che supporta altri scenari.

|Nome servizio|Descrizione del servizio|
|:-----------|:------------------|
|[Ricerca notizie Bing](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/ "Ricerca notizie Bing")|Ricerca notizie Bing restituisce un elenco di articoli di notizie ritenute pertinenti alla query dell'utente.|
|[Ricerca video Bing](https://docs.microsoft.com/azure/cognitive-services/Bing-Video-Search/ "Ricerca video Bing")|Ricerca video Bing restituisce un elenco di video ritenuti pertinenti alla query dell'utente.|
|[Ricerca Web Bing](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/ "Ricerca Web Bing")|Ricerca Web Bing restituisce un elenco di risultati di ricerca ritenuti pertinenti alla query dell'utente.|
|[Suggerimenti automatici Bing](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest "Suggerimenti automatici Bing")|Suggerimenti automatici Bing consente di inviare un termine di query di ricerca parziale a Bing e ottenere un elenco di query.|
|[Ricerca personalizzata Bing](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search "Ricerca personalizzata Bing")|Ricerca personalizzata Bing consente di creare esperienze di ricerca personalizzate per gli argomenti a cui si è interessati.|
|[Ricerca entità Bing](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/ "Ricerca entità Bing")|Ricerca entità Bing restituisce informazioni sulle entità ritenute pertinenti alla query dell'utente.|
|[Ricerca immagini Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search "Ricerca immagini Bing")|Ricerca immagini Bing restituisce immagini ritenute pertinenti alla query dell'utente.|
|[Ricerca visiva Bing](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search "Ricerca visiva Bing")|Ricerca visiva Bing restituisce informazioni dettagliate su un'immagine come immagini somiglianti, fonti per l'acquisto di prodotti individuati nell'immagine e ricerche correlate.|
|[Ricerca attività commerciali locali Bing](https://docs.microsoft.com/azure/cognitive-services/bing-local-business-search/ "Ricerca attività commerciali locali Bing")| L'API Ricerca attività commerciali locali Bing consente alle applicazioni di trovare informazioni di contatto e sulla posizione di aziende locali in base alle query di ricerca.|
|[Controllo ortografico Bing](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/ "Controllo ortografico Bing")|Controllo ortografico Bing consente di eseguire il controllo grammaticale e ortografico.|

## <a name="development-options"></a>Opzioni di sviluppo 

Con Azure e Servizi cognitivi è possibile accedere a diverse opzioni di sviluppo, tra cui:

* Strumenti di automazione e integrazione, ad esempio App per la logica e Power Automate.
* Opzioni di distribuzione come Funzioni di Azure e il Servizio app. 
* Contenitori Docker per Servizi cognitivi per l'accesso sicuro.
* Strumenti come Apache Spark, Azure Databricks, Azure Synapse Analytics e il servizio Azure Kubernetes per gli scenari Big Data. 

Per altre informazioni, vedere [Opzioni di sviluppo per Servizi cognitivi](./cognitive-services-development-options.md).

## <a name="learn-with-the-quickstarts"></a>Apprendere con le guide di avvio rapido

Iniziare creando una risorsa di Servizi cognitivi con le guide di avvio rapido pratiche usando i metodi seguenti:

* [Azure portal](cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows "Portale di Azure")
* [Interfaccia della riga di comando di Azure](cognitive-services-apis-create-account-cli.md?tabs=windows "Interfaccia della riga di comando di Azure")
* [Librerie client di Azure SDK](cognitive-services-apis-create-account-cli.md?tabs=windows "cognitive-services-apis-create-account-client-library?pivots=programming-language-csharp")
* [Modelli di Azure Resource Manager](resource-manager-template.md?tabs=portal "Modelli di Azure Resource Manager")

<!--
## Subscription management

Once you are signed in with your Microsoft Account, you can access [My subscriptions](https://www.microsoft.com/cognitive-services/subscriptions "My subscriptions") to show the products you are using, the quota remaining, and the ability to add additional products to your subscription.

## Upgrade to unlock higher limits

All APIs have a free tier, which has usage and throughput limits.  You can increase these limits by using a paid offering and selecting the appropriate pricing tier option when deploying the service in the Azure portal. [Learn more about the offerings and pricing](https://azure.microsoft.com/pricing/details/cognitive-services/ "offerings and pricing"). You'll need to set up an Azure subscriber account with a credit card and a phone number. If you have a special requirement or simply want to talk to sales, click "Contact us" button at the top the pricing page.
-->

## <a name="using-cognitive-services-securely"></a>Uso sicuro di Servizi cognitivi

Servizi cognitivi di Azure prevede un modello di sicurezza a più livelli, tra cui l'[autenticazione](authentication.md "autenticazione") tramite credenziali di Azure Active Directory, una chiave di risorsa valida e [reti virtuali di Azure](cognitive-services-virtual-networks.md "Reti virtuali di Azure").

## <a name="containers-for-cognitive-services"></a>Contenitori per Servizi cognitivi

 Servizi cognitivi offre contenitori per la distribuzione nel cloud di Azure o in locale. Informazioni sui [contenitori di Servizi cognitivi](cognitive-services-container-support.md "Contenitori di Servizi cognitivi").

## <a name="regional-availability"></a>Disponibilità a livello di area

Le API nei Servizi cognitivi sono ospitate in una rete in espansione di centri dati gestiti da Microsoft. È possibile trovare la disponibilità a livello di area per ogni API nell'[elenco delle aree di Azure](https://azure.microsoft.com/regions "Elenco di aree di Azure").

Se si sta cercando un'area non ancora supportata, inviare una richiesta di funzionalità nel [forum di UserVoice](https://cognitive.uservoice.com/ "Forum UserVoice").

## <a name="supported-cultural-languages"></a>Lingue supportate

Servizi cognitivi supporta un'ampia gamma di lingue a livello di servizio. È possibile trovare la disponibilità a livello di lingua per ogni API nell'[elenco delle lingue supportate](language-support.md "elenco delle lingue supportate").

## <a name="certifications-and-compliance"></a>Certificazioni e conformità

I Servizi cognitivi hanno ricevuto certificazioni come CSA STAR, FedRAMP moderato e HIPAA BAA. È possibile [scaricare](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942 "download") le certificazioni per eseguire controlli e verifiche della sicurezza.

Per informazioni sulla privacy e la gestione dei dati, accedere al [Centro protezione](https://servicetrust.microsoft.com/ "Centro protezione").

## <a name="support"></a>Supporto

Servizi cognitivi offre diverse opzioni di supporto che consentono di procedere con la creazione di applicazioni intelligenti. Servizi cognitivi può inoltre contare su una grande community di sviluppatori che possono rispondere a domande specifiche. Per un elenco completo delle opzioni disponibili, vedere [Opzioni di supporto tecnico e assistenza di Servizi cognitivi](cognitive-services-support-options.md "Opzioni di supporto tecnico e assistenza di Servizi cognitivi").

## <a name="next-steps"></a>Passaggi successivi

* [Creare un account Servizi cognitivi](cognitive-services-apis-create-account.md "Creare un account di Servizi cognitivi")
* [Novità della documentazione di Servizi cognitivi](whats-new-docs.md "Novità della documentazione di Servizi cognitivi")
