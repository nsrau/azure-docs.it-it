---
title: Panoramica concettuale di Gestione modelli di Azure Machine Learning | Microsoft Docs
description: Questo documento illustra i concetti di Gestione modelli di Azure Machine Learning.
services: machine-learning
author: nk773
ms.author: padou
manager: mwinkle
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 931dfae740996325cc62071a861e81ef5f67548b
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2018
---
# <a name="azure-machine-learning-model-management"></a>Gestione modelli di Azure Machine Learning

Gestione modelli di Azure Machine Learning consente di gestire e distribuire i flussi di lavoro e i modelli di machine learning. 

Gestione modelli fornisce funzionalità per:
- Gestione della versione dei modelli
- Monitoraggio dei modelli nell'ambiente di produzione
- Distribuzione dei modelli nell'ambiente di produzione mediante l'ambiente di calcolo AzureML con il [servizio contenitore di Azure](https://azure.microsoft.com/services/container-service/) e [Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
- Creazione di contenitori Docker con i modelli e loro test locale
- Riaddestramento dei modelli automatizzato
- Acquisizione dei dati di telemetria del modello per ricavare informazioni utili. 

Gestione modelli di Azure Machine Learning fornisce un registro delle versioni del modello. Fornisce anche flussi di lavoro automatizzati per la creazione di pacchetti e la distribuzione dei contenitori di Machine Learning come API REST. I modelli e le relative dipendenze di runtime sono inclusi nel pacchetto del contenitore Docker basato su Linux con API di stima. 

Gli ambienti di calcolo di Azure Machine Learning consentono di configurare e gestire cluster scalabili per ospitare i modelli. L'ambiente di calcolo è basato sui servizi contenitore di Azure. I servizi contenitore di Azure offrono l'esposizione automatica delle API di Machine Learning come endpoint dell'API REST con le funzionalità seguenti:

- Authentication
- Bilanciamento del carico.
- Scalabilità orizzontale automatica
- Crittografia

Gestione modelli di Azure Machine Learning fornisce queste funzionalità tramite l'interfaccia della riga di comando, l'API e il portale di Azure. 

Gestione modelli di Azure Machine Learning utilizza le informazioni seguenti:

 - File di modello o una directory con i file di modello
 - File di Python creato dall'utente che implementa una funzione per l'assegnazione di un punteggio ai modelli
 - File di dipendenze Conda che elenca le dipendenze di runtime
 - Scelta dell'ambiente di runtime e 
 - File di schema per i parametri di API 

Queste informazioni vengono usate quando si eseguono le azioni seguenti:

- Registrazione di un modello
- Creazione di un manifesto che viene usato durante la compilazione di un contenitore
- Creazione di un'immagine del contenitore Docker
- Distribuzione di un contenitore nel servizio contenitore di Azure
 
La figura seguente mostra una panoramica di come i modelli vengono registrati e distribuiti nel cluster. 

![](media/model-management-overview/modelmanagement.png)

## <a name="create-and-manage-models"></a>Creare e gestire i modelli 
È possibile registrare i modelli con Gestione modelli di Azure Machine Learning per tenere traccia delle versioni dei modelli nell'ambiente di produzione. Per facilitare la governance e la riproducibilità, il servizio acquisisce tutte le dipendenze e le informazioni associate. Per ottenere informazioni approfondite sulle prestazioni, è possibile acquisire i dati di telemetria del modello tramite l'SDK fornito. I dati di telemetria del modello sono archiviati nella risorsa di archiviazione fornita dall'utente. La telemetria del modello può essere usata per analizzare le prestazioni del modello, riaddestrarlo e ottenere informazioni per l'azienda.

## <a name="create-and-manage-manifests"></a>Creare e gestire i manifesti 
I modelli richiedono ulteriori elementi da distribuire nell'ambiente di produzione. Il sistema consente di creare un manifesto che include il modello, le dipendenze, lo script di inferenza (ovvero lo script di assegnazione del punteggio), i dati di esempio, lo schema e così via. Questo manifesto funge da ricetta per creare un'immagine contenitore Docker. Le aziende possono generare automaticamente il manifesto, creare diverse versioni e gestire i relativi manifesti. 

## <a name="create-and-manage-docker-container-images"></a>Creare e gestire le immagini contenitore Docker 
È possibile usare il manifesto del passaggio precedente per creare immagini contenitore basate su Docker nei loro rispettivi ambienti. Le immagini in contenitori basate su Docker offrono alle aziende la flessibilità necessaria per eseguire queste immagini negli ambienti di elaborazione seguenti:

- [Servizio contenitore di Azure basato su Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
- Servizi contenitore locali
- Ambienti di sviluppo
- Dispositivi IoT

Queste immagini in contenitori basate su Docker contengono tutte le dipendenze necessarie per la generazione di stime. 

## <a name="deploy-docker-container-images"></a>Distribuire immagini di contenitore Docker 
Con Gestione modelli di Azure Machine Learning è possibile distribuire le immagini contenitore basate su Docker con un unico comando nel servizio contenitore di Azure gestito dall'ambiente di calcolo ML. Queste distribuzioni vengono create con un server front-end che fornisce le funzionalità seguenti:

- Stime a bassa latenza su larga scala
- Bilanciamento del carico.
- Ridimensionamento automatico degli endpoint ML
- Autorizzazione della chiave API
- Documento API Swagger

È possibile controllare la scala di distribuzione e i dati di telemetria tramite le impostazioni di configurazione seguenti:

- Registrazione di sistema e dati di telemetria del modello per ogni livello di servizio Web. Se abilitata, vengono inviati tutti i log stdout ad [Azure Application Insights](https://azure.microsoft.com/services/application-insights/). I dati di telemetria del modello sono archiviati nella risorsa di archiviazione fornita dall'utente. 
- Limiti di scalabilità automatica e concorrenza. Queste impostazioni aumentano automaticamente il numero di contenitori distribuiti in base al carico all'interno del cluster esistente. Controllano anche la velocità effettiva e la coerenza della latenza di stima.

## <a name="consumption"></a>Consumo 
Gestione modelli di Azure Machine Learning crea l'API REST per il modello distribuito insieme al documento Swagger. È possibile usare i modelli distribuiti chiamando le API REST con la chiave API e il modello come input per ottenere le stime come parte delle applicazioni line-of-business. Il codice di esempio è disponibile in GitHub per i linguaggi Java, [Python](https://github.com/CortanaAnalyticsGallery-Int/digit-recognition-cnn-tf/blob/master/client.py) e C# per chiamare le API REST. L'interfaccia della riga di comando di Gestione modelli di Azure Machine Learning fornisce un modo semplice per usare queste API REST. È possibile usare le API tramite un singolo comando dell'interfaccia della riga di comando, un'applicazione con abilitazione Swagger o mediante Curl. 

## <a name="retraining"></a>Ripetizione dell'addestramento 
Gestione modelli di Azure Machine Learning fornisce API che è possibile usare per ripetere l'addestramento dei modelli. È anche possibile usare le API per aggiornare le distribuzioni esistenti con le versioni aggiornate del modello. Come parte del flusso di lavoro di data science, si ricrea il modello nell'ambiente di sperimentazione. Quindi si registra il modello con Gestione modelli e si aggiornano le distribuzioni esistenti. Gli aggiornamenti vengono eseguiti usando un unico comando UPDATE dell'interfaccia della riga di comando. Il comando UPDATE aggiorna le distribuzioni esistenti senza modificare l'URL o la chiave API. Le applicazioni che usano il modello continuano a funzionare senza modifiche al codice e iniziano a ottenere stime migliori usando il nuovo modello.

Il flusso di lavoro completo che descrive questi concetti è presentato nella figura seguente:

![](media/model-management-overview/modelmanagementworkflow.png)

## <a name="frequently-asked-questions-faq"></a>Domande frequenti 
- Quali tipi di dati sono supportati? È possibile passare matrici NumPy direttamente come input al servizio Web?

   Se si fornisce un file di schema che è stato creato usando l'SDK generate_schema, è possibile passare NumPy e/o Pandas DF. Si può anche passare qualsiasi input JSON serializzabile. Inoltre è possibile passare un'immagine come stringa con codifica binaria.

- Il servizio Web supporta più input o analizza input diversi? 

   Sì, è possibile usare più input inseriti come pacchetto nell'unica richiesta JSON come un dizionario. Ogni input corrisponderebbe a una singola chiave univoca del dizionario.

- La chiamata è attivata da una richiesta al servizio Web, una chiamata bloccante o una chiamata asincrona?

   Se il servizio è stato creato mediante l'opzione in tempo reale come parte dell'interfaccia della riga di comando o dell'API, allora si tratta di una chiamata bloccante/sincrona. Si prevede che sia veloce come in tempo reale. Tuttavia sul lato client è possibile eseguire la chiamata tramite la libreria HTTP asincrona per evitare di bloccare il thread del client.

- Quante richieste possono essere gestite contemporaneamente dal servizio Web?

   Dipende dalla scala del cluster e del servizio Web. È possibile scalare orizzontalmente il servizio a 100 volte le repliche dopodiché potrà gestire molte richieste contemporaneamente. È possibile anche configurare il massimo numero di richieste simultanee per replica in modo da aumentare la velocità effettiva del servizio.

- Quante richieste possono essere tenute in coda dal servizio Web?

   Questo valore è configurabile. Per impostazione predefinita, è impostato su ~10 per singola replica, ma è possibile aumentare/ridurre il valore in base ai requisiti dell'applicazione. In genere, aumentandolo il numero di richieste in coda aumenta la velocità effettiva del servizio, ma peggiora le latenze nei percentili più alti. Per mantenere le latenze coerenti, è possibile impostare la coda su un valore basso (1-5) e aumentare il numero di repliche per gestire la velocità effettiva. È anche possibile attivare la scalabilità automatica per fare in modo che il numero di repliche si regoli automaticamente in base al carico. 

- Lo stesso computer o cluster può essere usato per più endpoint del servizio Web?

   Certo. È possibile eseguire 100 volte i servizi/endpoint nello stesso cluster. 

## <a name="next-steps"></a>Passaggi successivi
Per iniziare a usare Gestione modelli, vedere l'articolo sulla [configurazione di Gestione modelli](deployment-setup-configuration.md).
