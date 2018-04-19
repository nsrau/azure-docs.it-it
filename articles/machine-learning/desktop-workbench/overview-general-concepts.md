---
title: "Panoramica concettuale delle funzionalità di anteprima di Azure Machine Learning | Microsoft Docs"
description: "Una panoramica concettuale delle funzionalità di anteprima di Azure Machine Learning ad esempio sottoscrizioni, account, aree di lavoro, progetti e così via."
services: machine-learning
author: serinakaye
ms.author: serinak
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/06/2017
ms.openlocfilehash: ea9da6f23fd08c09f9e805519487648480816f35
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="azure-machine-learning---concepts"></a>Azure Machine Learning - Concetti

Questo articolo definisce e descrive i concetti necessari per l'uso di Azure Machine Learning. 

![Gerarchia dei concetti](media/overview-general-concepts/hierarchy.png)

- **Sottoscrizione:** una sottoscrizione di Azure consente di accedere alle risorse in Azure. Poiché Azure Machine Learning è perfettamente integrato con il calcolo, l'archiviazione e con molte altre risorse e servizi di Azure, Workbench richiede che ogni utente disponga dell'accesso a una sottoscrizione di Azure valida. Gli utenti devono disporre anche di autorizzazioni sufficienti all'interno di tale sottoscrizione per creare le risorse.


- **Account di sperimentazione:** l'account sperimentazione è una risorsa di Azure necessaria per Azure Machine Learning e un mezzo di fatturazione. Contiene le aree di lavoro in cui sono presenti i progetti. A un account di sperimentazione è possibile aggiungere più utenti indicati come _postazioni_. È necessario avere accesso a un account di sperimentazione per usare Azure Machine Learning Workbench per eseguire esperimenti. 


- **Account di Gestione modelli:** un account di gestione modelli è anche una risorsa di Azure richiesta da Azure Machine Learning per la gestione dei modelli. È possibile usarlo per registrare i modelli e i manifesti, compilare servizi Web in contenitori e distribuirli in locale o nel cloud. È anche l'altro mezzo di fatturazione di Azure Machine Learning.


- **Area di lavoro:** un'area di lavoro è il componente principale per la condivisione e la collaborazione in Azure Machine Learning. I progetti vengono raggruppati all'interno di un'area di lavoro. Un'area di lavoro può quindi essere condivisa con più utenti aggiunti all'account di sperimentazione.


- **Progetto:** In Azure Machine Learning, un progetto è il contenitore logico di tutto il lavoro svolto per risolvere un problema. Esegue il mapping a una singola cartella di file sul disco locale a cui è possibile aggiungere qualsiasi file o sottocartella. Un progetto può essere facoltativamente associato a un repository Git per controllo del codice sorgente e la collaborazione.  

- **Sperimentazione:** In Azure Machine Learning, un esperimento è uno o più file di codice sorgente che possono essere eseguiti da un singolo punto di ingresso. Può contenere attività come l'inserimento di dati, la progettazione delle funzionalità, il training del modello o la valutazione del modello. Azure Machine Learning attualmente supporta solo esperimenti Python o PySpark.


- **Modello:** In Azure Machine Learning i modelli fanno riferimento al prodotto di un esperimento di Machine Learning. Sono ricette che quando applicate correttamente ai dati generano valori stimati. I modelli possono essere distribuiti negli ambienti di test o di produzione e usati per assegnare punteggi ai nuovi dati. Una volta in produzione, i modelli possono essere monitorati per le prestazioni e la desincronizzazione dei dati e sottoposti nuovamente a training in base alle esigenze. 

- **Destinazione di calcolo:** una destinazione di calcolo è la risorsa di calcolo configurata per l'esecuzione degli esperimenti. Può essere il computer locale (Windows o MacOS), un contenitore Docker in esecuzione nel computer locale o in una VM Linux in Azure o un cluster HDInsight Spark.


- **Esegui:** il servizio di sperimentazione definisce un'esecuzione come la durata dell'esecuzione di un esperimento in una destinazione di calcolo. Azure Machine Learning acquisisce automaticamente le informazioni di ogni esecuzione e presenta l'intera cronologia di un determinato esperimento sotto forma di cronologia di esecuzione.

- **Ambiente:** In Azure Machine Learning un ambiente indica una particolare risorsa di calcolo che viene usata per distribuire e gestire i modelli. Può trattarsi del computer locale, una VM Linux di Azure o un cluster Kubernetes in esecuzione nel servizio contenitore di Azure, a seconda della configurazione e del contesto. Il modello è ospitato in un contenitore Docker in esecuzione in questi ambienti ed esposto come endpoint API REST.


- **Modello gestito:** Gestione modelli permette di distribuire i modelli come servizi Web, gestire diverse versioni dei modelli e monitorare prestazioni e metriche associate. I modelli gestiti sono modelli registrati in un account di Gestione modelli di Azure Machine Learning.

- **Manifesti:** quando il sistema di Gestione modelli distribuisce un modello nella produzione include un manifesto che può contenere il modello, le dipendenze, lo script di assegnazione dei punteggi, i dati di esempio e lo schema. Questo manifesto è la ricetta per creare un'immagine contenitore Docker. Usando Gestione modelli è possibile generare automaticamente i manifesti, creare diverse versioni e gestire i manifesti. 


- **Immagini:** è possibile usare i manifesti per generare e rigenerare immagini Docker. Le immagini Docker nei contenitori creano flessibilità per l'esecuzione nel cloud, nei computer locale o nel dispositivo IoT. Le immagini sono compete e includono tutte le dipendenze necessarie per il punteggio di nuovi dati con i modelli. 

- **Servizi:** Gestione modelli consente di distribuire i modelli come servizi Web. La logica e le dipendenze del servizio Web sono incapsulate in un'immagine. Ogni servizio Web è un set di contenitori basati sulle immagini pronto per le richieste di servizio a un determinato URL. Un servizio Web viene conteggiato come una singola distribuzione.
