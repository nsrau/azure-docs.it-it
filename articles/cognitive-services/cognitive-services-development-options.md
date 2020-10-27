---
title: Opzioni di sviluppo dei servizi cognitivi di Azure
description: Informazioni su come usare i servizi cognitivi di Azure con diverse opzioni di sviluppo e distribuzione, ad esempio librerie client, API REST, app per la logica, Power automatizzate, funzioni di Azure, servizio app Azure, Azure Databricks e molto altro ancora.
services: cognitive-services
manager: nitinme
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 05d3ca7cf532b739b943e2a87d5ab29ae66cabd7
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92548573"
---
# <a name="cognitive-services-development-options"></a>Opzioni di sviluppo di servizi cognitivi

Questo documento fornisce una panoramica generale delle opzioni di sviluppo e distribuzione che consentono di iniziare a usare i servizi cognitivi di Azure.

I servizi cognitivi di Azure sono servizi di intelligenza artificiale basati sul cloud che consentono agli sviluppatori di creare informazioni sulle applicazioni e sui prodotti senza una conoscenza approfondita di machine learning. Con servizi cognitivi potrai accedere alle funzionalità o ai modelli di intelligenza artificiale compilati, sottoposti a training e aggiornati da Microsoft per l'uso nelle tue applicazioni. In molti casi, è anche possibile personalizzare i modelli in base alle esigenze aziendali. 

I servizi cognitivi sono organizzati in quattro categorie: decisione, linguaggio, riconoscimento vocale e visione. In genere si accede a questi servizi tramite le API REST, le librerie client e gli strumenti personalizzati, ad esempio le interfacce della riga di comando, forniti da Microsoft. Tuttavia, questo è solo un percorso di successo. Tramite Azure è inoltre possibile accedere a diverse opzioni di sviluppo, ad esempio:

* Strumenti di automazione e integrazione, ad esempio app per la logica e automazione dell'alimentazione.
* Opzioni di distribuzione come funzioni di Azure e il servizio app. 
* Contenitori Docker di servizi cognitivi per l'accesso sicuro.
* Strumenti come Apache Spark, Azure Databricks, Azure sinapsi Analytics e Azure Kubernetes Service per scenari di Big Data. 

Prima di passare alla pagina, è importante tenere presente che i servizi cognitivi vengono usati principalmente per due attività distinte. In base all'attività che si desidera eseguire, è possibile scegliere tra diverse opzioni di sviluppo e distribuzione. 

* [Opzioni di sviluppo per la stima e l'analisi](#development-options-for-prediction-and-analysis)
* [Strumenti per la personalizzazione e la configurazione di modelli](#tools-to-customize-and-configure-models)

## <a name="development-options-for-prediction-and-analysis"></a>Opzioni di sviluppo per la stima e l'analisi 

Gli strumenti usati per personalizzare e configurare i modelli sono diversi da quelli che verranno usati per chiamare i servizi cognitivi. Per impostazione predefinita, la maggior parte dei servizi cognitivi ti permette di inviare dati e ricevere informazioni dettagliate senza alcuna personalizzazione. Ad esempio: 

* È possibile inviare un'immagine al servizio Visione artificiale per rilevare parole e frasi oppure contare il numero di persone nel frame
* È possibile inviare un file audio al servizio di riconoscimento vocale e ottenere le trascrizioni e tradurre il riconoscimento vocale in testo allo stesso tempo
* È possibile inviare un file PDF al servizio di riconoscimento form e rilevare le tabelle, le celle e il testo all'interno di tali celle e ottenere un output JSON con coordinate e dettagli

Azure offre un'ampia gamma di strumenti progettati per diversi tipi di utenti, molti dei quali possono essere usati con servizi cognitivi. Gli strumenti basati su progettazione sono i più semplici da usare e sono veloci da configurare e automatizzare, ma possono avere limitazioni per quanto riguarda la personalizzazione. Le API REST e le librerie client forniscono agli utenti maggiore controllo e flessibilità, ma richiedono più tempo e competenze per creare una soluzione. Se si usano le API REST e le librerie client, si prevede che l'utente stia lavorando con i linguaggi di programmazione moderni come C#, Java, Python, JavaScript o un altro linguaggio di programmazione più diffuso. 

Verranno ora esaminati i diversi modi in cui è possibile lavorare con i servizi cognitivi.

### <a name="client-libraries-and-rest-apis"></a>Librerie client e API REST

Le librerie client di servizi cognitivi e le API REST consentono di accedere direttamente al servizio. Questi strumenti forniscono l'accesso a livello di codice ai servizi cognitivi, ai modelli di base e in molti casi consentono di personalizzare i modelli e le soluzioni a livello di codice. 

* **Utenti di destinazione** : sviluppatori e data scientist
* **Vantaggi** : offre la massima flessibilità per chiamare i servizi da qualsiasi linguaggio e ambiente. 
* **Interfaccia utente** : N/A-solo codice
* **Sottoscrizioni** : risorse account Azure e servizi cognitivi

Per altre informazioni sulle librerie client disponibili e sulle API REST, usare la Panoramica di [Servizi cognitivi](index.yml) per scegliere e servire e iniziare con una delle guide introduttive per la visione, la decisione, il linguaggio e la sintesi vocale.

### <a name="cognitive-services-for-big-data"></a>Servizi cognitivi per Big Data

Con Servizi cognitivi per Big data è possibile incorporare modelli intelligenti continuamente migliorabili direttamente nei calcoli di Apache Spark e SQL. Questi strumenti liberano gli sviluppatori dalle operazioni di rete di basso livello, in modo che possano dedicarsi alla creazione di applicazioni intelligenti e distribuite. I servizi cognitivi per Big Data supportano le piattaforme e i connettori seguenti: Azure Databricks, Azure sinapsi, Azure Kubernetes Service e connettori dati.

* **Utente/i di destinazione** : data scientist e data Engineers
* **Vantaggi** : i servizi cognitivi di Azure per Big Data consentono agli utenti di canalizzare terabyte di dati attraverso servizi cognitivi usando Apache Spark &trade; . Creare applicazioni intelligenti su larga scala con qualsiasi archivio dati è facile.
* **Interfaccia utente** : N/A-solo codice
* **Sottoscrizioni** : risorse account Azure e servizi cognitivi

Per ulteriori informazioni sui Big data per servizi cognitivi, è consigliabile iniziare con la [Panoramica](./big-data/cognitive-services-for-big-data.md). Se si è pronti per iniziare a creare, provare gli esempi [Python](./big-data/samples-python.md) o [scala](./big-data/samples-scala.md) .

### <a name="azure-functions-and-azure-service-web-jobs"></a>Funzioni di Azure e processi Web dei servizi di Azure

[Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/) e [processi Web del servizio app Azure](https://docs.microsoft.com/azure/app-service/) offrono servizi di integrazione Code-First progettati per gli sviluppatori e basati su [app Azure Services](https://docs.microsoft.com/azure/app-service/). Questi prodotti offrono un'infrastruttura senza server per la scrittura di codice. All'interno del codice è possibile effettuare chiamate ai servizi usando le librerie client e le API REST. 

* **Utenti di destinazione** : sviluppatori e data scientist
* **Vantaggi** : servizio di calcolo senza server che consente di eseguire codice attivato da eventi. 
* **Interfaccia utente** : Sì
* **Sottoscrizioni** : account Azure + risorsa Servizi cognitivi + sottoscrizione di funzioni di Azure

### <a name="azure-logic-apps"></a>App per la logica di Azure 

Le app per la [logica di Azure](https://docs.microsoft.com/azure/logic-apps/) condividono la stessa finestra di progettazione dei flussi di lavoro e i connettori di Power Automate, ma offre funzionalità più avanzate e di controllo, incluse le integrazioni con Visual Studio Power automatizzate rende più semplice l'integrazione con le risorse di servizi cognitivi tramite connettori specifici del servizio che forniscono un proxy o un wrapper per le API. Si tratta degli stessi connettori di quelli disponibili in Power automatizzate. 

* **Utente/i di destinazione** : sviluppatori, integratori, professionisti IT, DevOps
* **Vantaggi** : modello di sviluppo di progettazione-primo (dichiarativo) che fornisce opzioni avanzate e integrazione in una soluzione con basso codice
* **Interfaccia utente** : Sì
* **Sottoscrizioni** : account Azure + risorsa Servizi cognitivi + distribuzione di app per la logica

### <a name="power-automate"></a>Power Automate 

Power automatizzate è un servizio della [piattaforma Power](https://docs.microsoft.com/power-platform/) che consente di creare flussi di lavoro automatizzati tra le app e i servizi senza scrivere codice. Sono disponibili diversi connettori che semplificano l'interazione con la risorsa Servizi cognitivi in una soluzione di automazione del risparmio energia. Power Automate si basa su App per la logica 

* **Utente/i di destinazione** : utenti aziendali (analisti) e amministratori di SharePoint
* **Vantaggi** : automatizzare le attività manuali ripetitive semplicemente registrando i clic del mouse, le sequenze di tasti e i passaggi di copia incolla dal desktop.
* **Strumenti dell'interfaccia utente** : Sì-solo interfaccia utente
* **Sottoscrizioni** : account Azure + risorsa Servizi cognitivi + sottoscrizione Power automatizzata + sottoscrizione Office 365

### <a name="ai-builder"></a>AI Builder 

[Ai Builder](https://docs.microsoft.com/ai-builder/overview) è una funzionalità di Microsoft Power Platform che è possibile usare per migliorare le prestazioni aziendali mediante l'automazione dei processi e la stima dei risultati. Il generatore di intelligenza artificiale offre la potenza di intelligenza artificiale alle tue soluzioni grazie a un'esperienza con un clic. Molti servizi cognitivi come il riconoscitore di moduli, Analisi del testo e Visione artificiale sono stati integrati direttamente e non è necessario creare servizi cognitivi personalizzati. 

* **Utente/i di destinazione** : utenti aziendali (analisti) e amministratori di SharePoint
* **Vantaggi** : una soluzione chiavi in mano che offre la potenza di intelligenza artificiale attraverso un'esperienza con un clic. Non sono necessarie competenze di codifica o data science.
* **Strumenti dell'interfaccia utente** : Sì-solo interfaccia utente
* **Sottoscrizioni** : generatore ai

### <a name="continuous-integration-and-deployment"></a>Integrazione e distribuzione continua

Per gestire le distribuzioni, è possibile usare le azioni di Azure DevOps e GitHub. Nella [sezione seguente](#continuous-integration-and-delivery-with-devops-and-github-actions) vengono illustrati due esempi di integrazione ci/CD per eseguire il training e la distribuzione di modelli personalizzati per il riconoscimento vocale e il servizio Language Understanding (Luis). 

* **Utenti di destinazione** : sviluppatori, data scientist e data Engineers
* **Vantaggi** : consente di regolare, aggiornare e distribuire continuamente le applicazioni e i modelli a livello di codice. Il vantaggio è significativo quando si usano regolarmente i dati per migliorare e aggiornare i modelli di riconoscimento vocale, visione artificiale, lingua e decisione. 
* **Strumenti dell'interfaccia utente** : N/A-solo codice 
* **Sottoscrizioni** : account Azure + risorsa Servizi cognitivi + account github

## <a name="tools-to-customize-and-configure-models"></a>Strumenti per la personalizzazione e la configurazione di modelli

Con l'avanzamento del processo di creazione di un'applicazione o di un flusso di lavoro con servizi cognitivi, potrebbe essere necessario personalizzare il modello per ottenere le prestazioni desiderate. Molti dei nostri servizi ti permettono di creare in base ai modelli predefiniti per soddisfare le tue esigenze aziendali specifiche. Per tutti i nostri servizi personalizzabili, offriamo un'esperienza basata sull'interfaccia utente per l'analisi del processo e le API per la formazione basata sul codice. Ad esempio:

* Si desidera eseguire il training di un modello di Riconoscimento vocale personalizzato per riconoscere correttamente le condizioni mediche con una frequenza degli errori di parola (WER) inferiore al 3%
* Si desidera compilare un classificatore di immagini con Visione personalizzata in grado di distinguere gli alberi coniferi e decidui
* Si vuole creare una voce neurale personalizzata con i dati vocali personali per migliorare l'esperienza utente automatizzata

Gli strumenti usati per il training e la configurazione dei modelli sono diversi da quelli che verranno usati per chiamare i servizi cognitivi. In molti casi, i servizi cognitivi che supportano la personalizzazione forniscono i portali e gli strumenti dell'interfaccia utente progettati per eseguire il training, la valutazione e la distribuzione di modelli. Verranno ora esaminate brevemente alcune opzioni:<br><br>

| Concetto fondamentale | Servizio | Interfaccia utente di personalizzazione | Guida introduttiva |
|--------|---------|------------------|------------|
| Visione | Servizio visione artificiale personalizzato | https://www.customvision.ai/ | [Guida introduttiva](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/quickstarts/image-classification?pivots=programming-language-csharp) | 
| Visione | Riconoscimento modulo | Strumento di etichettatura campioni | [Guida introduttiva](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/quickstarts/label-tool?tabs=v2-0) |
| Decisione | Content Moderator | https://contentmoderator.cognitive.microsoft.com/dashboard | [Guida introduttiva](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/human-in-the-loop) |
| Decisione | Advisor metriche | https://metricsadvisor.azurewebsites.net/  | [Guida introduttiva](https://docs.microsoft.com/azure/cognitive-services/metrics-advisor/quickstarts/web-portal) |
| Decisione | Personalizza esperienze | L'interfaccia utente è disponibile nella portale di Azure nella risorsa di personalizzazione. | [Guida introduttiva](https://docs.microsoft.com/azure/cognitive-services/personalizer/quickstart-personalizer-sdk) |
| Linguaggio | Language Understanding (LUIS) | https://www.luis.ai/ | |
| Linguaggio | QnA Maker | https://www.qnamaker.ai/ | [Guida introduttiva](https://docs.microsoft.com/azure/cognitive-services/qnamaker/quickstarts/create-publish-knowledge-base) |
| Linguaggio | Traduttore/convertitore personalizzato | https://portal.customtranslator.azure.ai/ | [Guida introduttiva](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/quickstart-build-deploy-custom-model) |
| Voce | Comandi personalizzati | https://speech.microsoft.com/ | [Guida introduttiva](https://docs.microsoft.com/azure/cognitive-services/speech-service/custom-commands) |
| Voce | Riconoscimento vocale personalizzato | https://speech.microsoft.com/ | [Guida introduttiva](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-speech) |
| Voce | Voce personalizzata | https://speech.microsoft.com/ | [Guida introduttiva](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice) |  

### <a name="continuous-integration-and-delivery-with-devops-and-github-actions"></a>Integrazione e distribuzione continue con azioni DevOps e GitHub

Language Understanding e il servizio riconoscimento vocale offrono soluzioni di integrazione continua e di distribuzione continua basate sulle azioni di Azure DevOps e GitHub. Questi strumenti vengono usati per il training, il test e la gestione del rilascio automatici dei modelli personalizzati. 

* [CI/CD per Riconoscimento vocale personalizzato](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-speech-continuous-integration-continuous-deployment)
* [CI/CD per LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-devops-automation)

## <a name="on-prem-containers"></a>Contenitori locali 

Molti servizi cognitivi possono essere distribuiti in contenitori per l'accesso locale e l'uso di. L'uso di questi contenitori ti offre la flessibilità necessaria per avvicinare i servizi cognitivi ai tuoi dati, in modo da garantire conformità, sicurezza o altri motivi operativi. Per un elenco completo di contenitori di servizi cognitivi, vedere [contenitori locali per servizi cognitivi](./cognitive-services-container-support.md).

## <a name="next-steps"></a>Passaggi successivi
<!--
* Learn more about low code development options for Cognitive Services -->
* [Creare una risorsa Servizi cognitivi e iniziare a compilare](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Clinux)