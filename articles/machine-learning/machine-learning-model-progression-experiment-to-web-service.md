---
title: Come un modello di Azure Machine Learning diventa un servizio Web | Documentazione Microsoft
description: Panoramica dei meccanismi in base a cui il modello di Azure Machine Learning passa da esperimento di sviluppo a servizio Web operativo.
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 25e0c025-f8b0-44ab-beaf-d0f2d485eb91
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: garye
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: 008952b4fee6bffe1e20259dc86fd6d5122777ec
ms.contentlocale: it-it
ms.lasthandoff: 06/07/2017

---
# <a name="how-a-machine-learning-model-progresses-from-an-experiment-to-an-operationalized-web-service"></a>Modalità di passaggio di un modello di Machine Learning da esperimento a servizio Web operativo
Azure Machine Learning Studio fornisce un'area di disegno che consente di sviluppare, eseguire, testare e ripetere un ***esperimento*** che rappresenta un modello di analisi predittiva. Sono disponibili diversi moduli che consentono di:

* Immettere i dati nell'esperimento
* Manipolare i dati
* Eseguire il training di un modello usando algoritmi di Machine Learning
* Assegnare un punteggio al modello
* Valutare i risultati
* Restituire i valori finali

Quando l'esperimento soddisfa le esigenze, è possibile distribuirlo come ***servizio Web classico di Azure Machine Learning*** o come ***nuovo servizio Web di Azure Machine Learning***, per consentire agli utenti di inviare nuovi dati e ricevere i risultati.

Questo articolo fornisce una panoramica dei meccanismi in base ai quali il modello di Machine Learning passa da esperimento di sviluppo a servizio Web operativo.

> [!NOTE]
> È possibile sviluppare e distribuire i modelli di Machine Learning in altri modi, ma questo articolo descrive come farlo con Machine Learning Studio. Per una descrizione su come creare un servizio Web predittivo classico con R, vedere il post di blog su [compilazione e distribuzione di app Web predittive con RStudio e Azure ML](http://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx).
> 
> 

Anche se Azure Machine Learning Studio è progettato principalmente per sviluppare e distribuire un *modello di analisi predittiva*, è possibile usare Studio per sviluppare un esperimento che non include un modello di analisi predittiva. Un esperimento, ad esempio, potrebbe limitarsi a inserire i dati, manipolarli e quindi generare l'output dei risultati. Proprio come un esperimento di analisi predittiva, è possibile distribuire questo esperimento non predittivo come servizio Web, ma il processo è più semplice perché l'esperimento non eseguirà il training né assegnerà un punteggio a un modello di Machine Learning. Anche se non rappresenta l'uso tipico di Studio, questo scenario verrà incluso nella discussione per offrire una panoramica completa del funzionamento di Studio.

## <a name="developing-and-deploying-a-predictive-web-service"></a>Sviluppo e distribuzione di un servizio Web predittivo
Ecco le fasi seguite da una soluzione tipica mentre viene sviluppata e distribuita con Machine Learning Studio:

![Flusso di distribuzione](media/machine-learning-model-progression-experiment-to-web-service/model-stages-from-experiment-to-web-service.png)

*Figura 1 - Fasi di un modello di analisi predittiva tipico*

### <a name="the-training-experiment"></a>Esperimento di training
L'***esperimento di training*** rappresenta la fase iniziale dello sviluppo del servizio Web in Machine Learning Studio. Lo scopo dell'esperimento di training è di fornire un'area in cui sviluppare, testare, eseguire l'iterazione e infine il training di un modello di Machine Learning. È possibile eseguire il training anche di più modelli contemporaneamente mentre si cerca la soluzione migliore, ma, una volta terminata la sperimentazione, si selezionerà un solo modello sottoposto a training e si elimineranno gli altri dall'esperimento. Per un esempio di sviluppo di un esperimento di analisi predittiva, vedere [Sviluppare una soluzione di analisi predittiva per la valutazione del rischio di credito in Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md).

### <a name="the-predictive-experiment"></a>Esperimento predittivo
Dopo avere eseguito il training del modello nell'esperimento di training, fare clic su **Set Up Web Service** (Configura servizio Web) e selezionare **Predictive Web Service** (Servizio Web predittivo) in Machine Learning Studio per avviare il processo di conversione dell'esperimento di training in un ***esperimento predittivo***. Lo scopo dell'esperimento predittivo è quello di usare il modello sottoposto a training per assegnare un punteggio ai nuovi dati, con l'obiettivo di renderlo infine operativo come servizio Web di Azure.

Questa conversione viene eseguita automaticamente con i passaggi seguenti:

* Conversione del set di moduli usati per il training in un singolo modulo che viene salvato come modello sottoposto a training
* Eliminazione di eventuali moduli estranei non correlati all'assegnazione dei punteggi
* Aggiunta di porte di input e di output che verranno usate dal servizio Web finale

È possibile apportare altre modifiche per preparare l'esperimento predittivo per la distribuzione come servizio Web. Se, ad esempio, è necessario che il servizio Web restituisca solo un subset di risultati, è possibile aggiungere un modulo di filtro prima della porta di output.

In questo processo di conversione l'esperimento di training non viene rimosso. Al termine del processo, Studio includerà due schede: una per l'esperimento di training e una per l'esperimento predittivo. In questo modo, è possibile apportare modifiche all'esperimento di training prima di distribuire il servizio Web e ricompilare l'esperimento predittivo. In alternativa, è possibile salvare una copia dell'esperimento di training per iniziare un'altra linea di sperimentazione.

> [!NOTE]
> Quando si fa clic su **Predictive Web Service** (Servizio Web predittivo), si avvia un processo automatico di conversione dell'esperimento di training in esperimento predittivo, che nella maggior parte dei casi è un'operazione appropriata. Se però l'esperimento di training è complesso (ad esempio, con più percorsi per il training uniti insieme), è preferibile eseguire questa conversione manualmente. Per altre informazioni, vedere l'articolo relativo alla [preparazione del modello per la distribuzione in Azure Machine Learning Studio](machine-learning-convert-training-experiment-to-scoring-experiment.md).
> 
> 

### <a name="the-web-service"></a>Servizio Web
Quando l'esperimento predittivo soddisfa le esigenze, è possibile distribuire il servizio come servizio Web classico o nuovo servizio Web basato su Azure Resource Manager. Per rendere operativo il modello distribuendolo come *servizio Web classico di Machine Learning*, fare clic su **Deploy Web Service** (Distribuisci servizio Web) e selezionare **Deploy Web Service [Classic]** (Distribuisci servizio Web [Classico]). Per eseguire la distribuzione come *nuovo servizio Web di Machine Learning*, fare clic su **Deploy Web Service** (Distribuisci servizio Web) e selezionare **Deploy Web Service [New]** (Distribuisci servizio Web [Nuovo]). Gli utenti ora possono inviare i dati al modello con l'API REST del servizio Web e ricevere i risultati. Per altre informazioni, vedere [Come usare un servizio Web di Azure Machine Learning pubblicato da un esperimento di Machine Learning](machine-learning-consume-web-services.md).

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>Caso non tipico: creazione di un servizio Web non predittivo
Se l'esperimento non esegue il training di un modello di analisi predittiva, non è necessario creare sia un esperimento di training che un esperimento di assegnazione dei punteggi. C'è un solo esperimento ed è possibile distribuirlo come servizio Web. Machine Learning Studio rileva se l'esperimento contiene un modello predittivo analizzando i moduli usati.

Dopo avere eseguito l'iterazione dell'esperimento e averlo verificato:

1. Fare clic su **Set Up Web Service** (Configura servizio Web) e selezionare **Retraining Web Service** (Servizio Web di ripetizione del training). I nodi di input e di output verranno aggiunti automaticamente.
2. Fare clic su **Run**
3. Fare clic su **Deploy Web Service** (Distribuisci servizio Web) e selezionare **Deploy Web Service [Classic]** (Distribuisci servizio Web [Classico]) o **Deploy Web Service [New]** (Distribuisci servizio Web [Nuovo]), a seconda dell'ambiente in cui si vuole eseguire la distribuzione.

Il servizio Web ora è distribuito ed è possibile accedervi e gestirlo proprio come si fa con un servizio Web predittivo.

## <a name="updating-your-web-service"></a>Aggiornamento del servizio Web
Ora che l'esperimento è stato distribuito come servizio Web, cosa bisogna fare se è necessario aggiornarlo?

Dipende da che cosa è necessario aggiornare:

**Si vuole cambiare l'input o l'output oppure si vuole modificare il modo in cui il servizio Web manipola i dati**

Se non si intende cambiare il modello, ma solo il modo in cui il servizio Web gestisce i dati, è possibile modificare l'esperimento predittivo e quindi fare clic su **Deploy Web Service** (Distribuisci servizio Web) e selezionare di nuovo **Deploy Web Service [Classic]** (Distribuisci servizio Web [Classico]) o **Deploy Web Service [New]** (Distribuisci servizio Web [Nuovo]). Il servizio Web viene arrestato, l'esperimento predittivo aggiornato viene distribuito e il servizio Web viene riavviato.

Ecco un esempio: si supponga che l'esperimento predittivo restituisca l'intera riga di dati di input con il risultato previsto. È possibile stabilire che il servizio Web deve restituire solo il risultato. È possibile aggiungere un modulo **Project Columns** nell'esperimento predittivo, subito prima della porta di output, per escludere le colonne diverse da quella del risultato. Quando si fa clic su **Deploy Web Service** (Distribuisci servizio Web) e si seleziona di nuovo **Deploy Web Service [Classic]** (Distribuisci servizio Web [Classico]) o **Deploy Web Service [New]** (Distribuisci servizio Web [Nuovo]), il servizio Web viene aggiornato.

**Si vuole ripetere il training del modello con i nuovi dati**

Per conservare il modello di Machine Learning, ripetendone però il training con i nuovi dati, sono disponibili due opzioni:

1. **Ripetere il training del modello mentre il servizio Web è in esecuzione**: per ripetere il training del modello mentre il servizio Web predittivo è in esecuzione, è necessario apportare alcune modifiche all'esperimento di training per renderlo un ***esperimento di ripetizione del training*** e quindi distribuirlo come ***servizio Web di ripetizione del training***. Per istruzioni in proposito, vedere [Ripetere il training dei modelli di Machine Learning a livello di codice](machine-learning-retrain-models-programmatically.md).
2. **Tornare all'esperimento di training originale e usare dati di training diversi per sviluppare il modello**: l'esperimento predittivo è collegato al servizio Web, ma l'esperimento di training non è direttamente collegato in questo modo. Se si modifica l'esperimento di training originale e si fa clic su **Set Up Web Service** (Configura servizio Web), verrà creato un *nuovo* esperimento predittivo che, se distribuito, creerà un *nuovo* servizio Web. Non verrà semplicemente aggiornato il servizio Web originale.
   
   Se è necessario modificare l'esperimento di training, aprirlo e fare clic su **Save As** (Salva con nome) per creare una copia. In questo modo l'esperimento di training, l'esperimento predittivo e il servizio Web originali rimarranno invariati. È quindi possibile creare un nuovo servizio Web con le modifiche. Una volta distribuito il nuovo servizio Web, è possibile decidere se arrestare il servizio Web precedente o se lasciarlo in esecuzione insieme a quello nuovo.

**Si vuole eseguire il training di un modello diverso**

Per apportare modifiche all'esperimento predittivo originale, ad esempio selezionando un algoritmo di Machine Learning diverso, provando un metodo di training diverso e così via, è necessario attenersi alla seconda procedura descritta sopra per ripetere il training del modello: aprire l'esperimento di training, fare clic su **Save As** (Salva con nome) per creare una copia e quindi avviare il nuovo percorso di sviluppo del modello, creazione dell'esperimento predittivo e distribuzione del servizio Web. Verrà così creato un nuovo servizio Web non correlato a quello originale. È possibile decidere se lasciare in esecuzione uno dei due oppure entrambi.

## <a name="next-steps"></a>Passaggi successivi
Per altri dettagli sul processo di sviluppo di un esperimento, vedere gli articoli seguenti:

* Conversione dell'esperimento: vedere l'articolo relativo alla [preparazione del modello per la distribuzione in Azure Machine Learning Studio](machine-learning-convert-training-experiment-to-scoring-experiment.md).
* Distribuzione del servizio Web: [Distribuire un servizio Web di Azure Machine Learning](machine-learning-publish-a-machine-learning-web-service.md)
* Ripetizione del training del modello: [Ripetere il training dei modelli di Machine Learning a livello di codice](machine-learning-retrain-models-programmatically.md)

Per esempi dell'intero processo, vedere:

* [Esercitazione di Machine Learning: Creare il primo esperimento in Azure Machine Learning Studio](machine-learning-create-experiment.md)
* [Procedura dettagliata: Sviluppare una soluzione di analisi predittiva per la valutazione del rischio di credito in Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md)


