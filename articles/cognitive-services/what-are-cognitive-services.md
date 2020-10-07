---
title: Informazioni su Servizi cognitivi di Azure
titleSuffix: Azure Cognitive Services
description: Servizi cognitivi di Azure sono servizi cloud con API REST ed SDK delle librerie client che è possibile usare con Microsoft Azure per creare applicazioni intelligenti.
services: cognitive-services
author: nitinme
manager: nitinme
keywords: servizi cognitivi, intelligenza cognitiva, soluzioni cognitive, servizi di intelligenza artificiale, riconoscimento cognitivo, funzionalità cognitive
ms.service: cognitive-services
ms.subservice: ''
ms.topic: overview
ms.date: 08/28/2020
ms.author: nitinme
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: bcb3fdbe12a2704e585a9b13484c2528c9cdc559
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "90894440"
---
# <a name="what-are-azure-cognitive-services"></a>Informazioni su Servizi cognitivi di Azure

I Servizi cognitivi di Azure sono servizi basati sul cloud con API REST ed SDK delle librerie client che consentono agli sviluppatori di integrare l'intelligenza cognitiva nelle applicazioni senza la necessità di competenze o conoscenze dirette di intelligenza artificiale e data science. I Servizi cognitivi di Azure consentono agli sviluppatori di aggiungere facilmente funzionalità cognitive alle applicazioni con soluzioni cognitive in grado di vedere, ascoltare, parlare, comprendere e persino di iniziare a ragionare.

Il catalogo dei servizi di intelligenza artificiale che supportano il riconoscimento cognitivo è suddiviso in cinque categorie principali:

* Visione
* Sintesi vocale
* Lingua
* Ricerca Web
* Decisione

L'elenco aggiornato della nuova documentazione è disponibile in [Novità della documentazione di Servizi cognitivi](whats-new-docs.md).

## <a name="vision-apis"></a>API Visione

|Nome servizio|Descrizione del servizio|
|:-----------|:------------------|
|[Visione artificiale](https://docs.microsoft.com/azure/cognitive-services/computer-vision/ "Visione artificiale")|Il servizio Visione artificiale consente di accedere ad algoritmi cognitivi avanzati per l'elaborazione delle immagini e la restituzione di informazioni.|
|[Servizio Visione personalizzata](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/home "Servizio visione artificiale personalizzato")|Il Servizio visione artificiale personalizzato consente di creare classificatori di immagini personalizzati.|
|[Viso](https://docs.microsoft.com/azure/cognitive-services/face/ "Viso")| Il servizio Viso fornisce l'accesso ad algoritmi viso avanzati consentendo il rilevamento e il riconoscimento degli attributi del viso.|
|[Riconoscimento modulo](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/ "Riconoscimento modulo") (anteprima)|Il riconoscimento modulo identifica ed estrae le coppie chiave-valore e i dati delle tabelle dai documenti dei moduli, quindi restituisce dati strutturati che includono le relazioni nel file originale.|
|[Riconoscimento input penna](https://docs.microsoft.com/azure/cognitive-services/ink-recognizer/ "Riconoscimento input penna") (in fase di ritiro)|Il riconoscimento input penna consente di riconoscere e analizzare i dati del tratto di input penna digitale, le forme e il contenuto scritto a mano e di restituire una struttura di documento con tutte le entità riconosciute.|
|[Video Indexer](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview "Video Indexer")|Video Indexer consente di estrarre informazioni dettagliate dai video.|

## <a name="speech-apis"></a>Speech API

|Nome servizio|Descrizione del servizio|
|:-----------|:------------------|
|[Servizio di riconoscimento vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/ "Servizio Voce")|Il servizio Voce aggiunge funzionalità di riconoscimento vocale alle applicazioni.|
|[API Riconoscimento del parlante](https://docs.microsoft.com/azure/cognitive-services/speaker-recognition/home "API Riconoscimento voce") (anteprima)|L'API Riconoscimento del parlante fornisce algoritmi per l'identificazione voce e la verifica.|
|[Riconoscimento vocale Bing](https://docs.microsoft.com/azure/cognitive-services/speech/home "Riconoscimento vocale Bing") (ritiro)|L'API Riconoscimento vocale Bing offre un modo semplice per creare funzionalità abilitate al riconoscimento vocale nelle applicazioni.|
|[Traduzione vocale](https://docs.microsoft.com/azure/cognitive-services/translator-speech/ "Traduzione vocale") (ritiro)|Traduzione vocale è un servizio di traduzione automatica.|

> [!NOTE]
> [Ricerca cognitiva di Azure](https://docs.microsoft.com/azure/search/), pur usando Servizi cognitivi per alcune attività, è una tecnologia di ricerca diversa che supporta altri scenari.


## <a name="language-apis"></a>API lingua

|Nome servizio|Descrizione del servizio|
|:-----------|:------------------|
|[Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/ "Language Understanding")|Language Understanding Intelligent Service (LUIS) consente all'applicazione di comprendere la finalità espressa da una persona con le proprie parole.|
|[QnA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/index "QnA Maker")|QnA Maker consente di creare una servizio di domande e risposte da contenuto semi-strutturato.|
|[Analisi del testo](https://docs.microsoft.com/azure/cognitive-services/text-analytics/ "Text Analytics")|Analisi del testo fornisce l'elaborazione del linguaggio naturale su testo non elaborato per l'analisi del sentiment, l'estrazione delle frasi chiave e il rilevamento della lingua.|
|[Translator](https://docs.microsoft.com/azure/cognitive-services/translator/ "Funzione di conversione")|Translator fornisce la traduzione automatica del testo quasi in tempo reale.|


## <a name="search-apis"></a>API di ricerca

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

## <a name="decision-apis"></a>API Decisione

|Nome servizio|Descrizione del servizio|
|:-----------|:------------------|
|[Rilevamento anomalie](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/ "Rilevamento anomalie") (anteprima)|Rilevamento anomalie permette di monitorare e rilevare le anomalie nei dati di serie temporali.|
|[Content Moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview "Content Moderator")|Content Moderator fornisce il monitoraggio per l'individuazione di contenuto potenzialmente offensivo, indesiderato e rischioso.|
|[Advisor metriche](https://docs.microsoft.com/azure/cognitive-services/metrics-advisor) (anteprima) | Advisor metriche consente di personalizzare il rilevamento anomalie sui dati di serie temporali con più variabili e offre un portale Web completo per facilitare l'uso del servizio.
|[Personalizza esperienze](https://docs.microsoft.com/azure/cognitive-services/personalizer/ "Personalizza esperienze")|Personalizza esperienze consente di scegliere la migliore esperienza da mostrare agli utenti, apprendendo dal loro comportamento in tempo reale.|

## <a name="learn-with-the-quickstarts"></a>Apprendere con le guide di avvio rapido

Le guide di avvio rapido pratiche insegnano a creare una risorsa di Servizi cognitivi usando:

* [Azure portal](cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows "Portale di Azure")
* [Interfaccia della riga di comando di Azure](cognitive-services-apis-create-account-cli.md?tabs=windows "Interfaccia della riga di comando di Azure")
* [Librerie client di Azure SDK](cognitive-services-apis-create-account-cli.md?tabs=windows "cognitive-services-apis-create-account-client-library?pivots=programming-language-csharp")
* [Modelli di Azure Resource Manager](resource-manager-template.md?tabs=portal "Modelli di Azure Resource Manager")

## <a name="subscription-management"></a>Gestione sottoscrizioni

Dopo aver eseguito l'accesso con l'account Microsoft, sarà possibile accedere a [Sottoscrizioni personali](https://www.microsoft.com/cognitive-services/subscriptions "Sottoscrizioni personali") per visualizzare i prodotti usati e la quota rimanente, nonché aggiungere altri prodotti alla sottoscrizione.

## <a name="upgrade-to-unlock-higher-limits"></a>Eseguire l'aggiornamento per usufruire di limiti più elevati

Tutte le API prevedono un livello gratuito con limiti di utilizzo e velocità effettiva.  È possibile aumentare questi limiti usando un'offerta a pagamento e selezionando l'opzione del piano tariffario appropriata quando si distribuisce il servizio nel portale di Azure. [Altre informazioni sulle offerte e i prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/ "Offerte e prezzi"). Sarà necessario configurare un account sottoscrittore di Azure con una carta di credito e un numero di telefono. Se si hanno particolari requisiti o si vuole semplicemente comunicare con gli addetti alle vendite, fare clic sul pulsante "Contattaci" nella parte superiore della pagina dei prezzi.

## <a name="regional-availability"></a>Disponibilità a livello di area

Le API nei Servizi cognitivi sono ospitate in una rete in espansione di centri dati gestiti da Microsoft. È possibile trovare la disponibilità a livello di area per ogni API nell'[elenco delle aree di Azure](https://azure.microsoft.com/regions "Elenco di aree di Azure").

Se si sta cercando un'area non ancora supportata, inviare una richiesta di funzionalità nel [forum di UserVoice](https://cognitive.uservoice.com/ "Forum UserVoice").

## <a name="supported-cultural-languages"></a>Lingue supportate

 Servizi cognitivi supporta un'ampia gamma di lingue a livello di servizio. È possibile trovare la disponibilità a livello di lingua per ogni API nell'[elenco delle lingue supportate](language-support.md "elenco delle lingue supportate").

## <a name="securing-resources"></a>Protezione delle risorse

Servizi cognitivi di Azure prevede un modello di sicurezza a più livelli, tra cui l'[autenticazione](authentication.md "autenticazione") tramite credenziali di Azure Active Directory, una chiave di risorsa valida e [reti virtuali di Azure](cognitive-services-virtual-networks.md "Reti virtuali di Azure").

## <a name="container-support"></a>Supporto dei contenitori

 Servizi cognitivi offre contenitori per la distribuzione nel cloud di Azure o in locale. Informazioni sui [contenitori di Servizi cognitivi](cognitive-services-container-support.md "Contenitori di Servizi cognitivi").

## <a name="certifications-and-compliance"></a>Certificazioni e conformità

I Servizi cognitivi hanno ricevuto certificazioni come CSA STAR, FedRAMP moderato e HIPAA BAA.

È possibile [scaricare](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942 "download") le certificazioni per eseguire controlli e verifiche della sicurezza.

Per informazioni sulla privacy e la gestione dei dati, accedere al [Centro protezione](https://servicetrust.microsoft.com/ "Centro protezione").

## <a name="support"></a>Supporto

Per Servizi cognitivi sono disponibili varie [opzioni di supporto](cognitive-services-support-options.md "opzioni di supporto").




## <a name="next-steps"></a>Passaggi successivi

* [Creare un account Servizi cognitivi](cognitive-services-apis-create-account.md "Creare un account di Servizi cognitivi")
