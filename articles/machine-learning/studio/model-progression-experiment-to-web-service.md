---
title: Come un modello diventa un servizio Web
titleSuffix: ML Studio (classic) - Azure
description: Panoramica dei meccanismi di avanzamento del modello di Azure Machine Learning Studio (classico) da un esperimento di sviluppo a un servizio Web.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 03/20/2017
ms.openlocfilehash: e6a8fe7771776a92b3c28a188bc352e3bb105313
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79217912"
---
# <a name="how-a-machine-learning-studio-classic-model-progresses-from-an-experiment-to-a-web-service"></a>Come un modello di Machine Learning Studio (classico) avanza da un esperimento a un servizio Web

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
Azure Machine Learning Studio (classico) fornisce un'area di disegno interattiva che consente di sviluppare, eseguire, testare ed eseguire l'iterazione di un ***esperimento*** che rappresenta un modello di analisi predittiva. Sono disponibili diversi moduli che consentono di:

* Immettere i dati nell'esperimento
* Manipolare i dati
* Eseguire il training di un modello usando algoritmi di Machine Learning
* Assegnare un punteggio al modello
* Valutare i risultati
* Restituire i valori finali

Una volta completato l'esperimento, è possibile distribuirlo come ***servizio web Azure Machine Learning classico*** o come ***nuovo servizio Web Azure Machine Learning*** in modo che gli utenti possano inviare i nuovi dati e ricevere risultati.

Questo articolo fornisce una panoramica dei meccanismi in base ai quali il modello di Machine Learning passa da esperimento di sviluppo a servizio Web operativo.

> [!NOTE]
> Esistono altri modi per sviluppare e distribuire modelli di apprendimento automatico, ma questo articolo è incentrato sul modo in cui si usa Machine Learning Studio (classico). Per una descrizione su come creare un servizio Web predittivo classico con R, vedere il post di blog su [compilazione e distribuzione di app Web predittive con RStudio e Azure Machine Learning Studio](https://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx).
>
>

Mentre Azure Machine Learning Studio (classico) è progettato per semplificare lo sviluppo e la distribuzione di un *modello di analisi predittiva*, è possibile usare Studio (classico) per sviluppare un esperimento che non includa un modello di analisi predittiva. Un esperimento, ad esempio, potrebbe limitarsi a inserire i dati, manipolarli e quindi generare l'output dei risultati. Proprio come un esperimento di analisi predittiva, è possibile distribuire questo esperimento non predittivo come servizio Web, ma si tratta di un processo più semplice perché l'esperimento non esegue il training o l'assegnazione di punteggi a un modello di machine learning. Sebbene non sia l'uso tipico di studio (classico) in questo modo, verrà incluso nella discussione, in modo che sia possibile fornire una spiegazione completa del funzionamento di studio (classico).

## <a name="developing-and-deploying-a-predictive-web-service"></a>Sviluppo e distribuzione di un servizio Web predittivo
Ecco le fasi che una soluzione tipica segue durante lo sviluppo e la distribuzione usando Machine Learning Studio (classico):

![Flusso di distribuzione](./media/model-progression-experiment-to-web-service/model-stages-from-experiment-to-web-service.png)

*Figura 1 - Fasi di un modello di analisi predittiva tipico*

### <a name="the-training-experiment"></a>Esperimento di training
L' ***esperimento di training*** è la fase iniziale dello sviluppo del servizio Web in Machine Learning Studio (classico). Lo scopo dell'esperimento di training è di fornire un'area in cui sviluppare, testare, eseguire l'iterazione e infine il training di un modello di Machine Learning. È anche possibile eseguire il training simultaneo di più modelli quando si cerca la soluzione migliore, ma una volta completata la sperimentazione, è possibile selezionare un singolo modello sottoposto a training ed eliminare il resto dall'esperimento. Per un esempio di sviluppo di un esperimento di analisi predittiva, vedere [sviluppare una soluzione di analisi predittiva per la valutazione del rischio di credito in Azure Machine Learning Studio (classico)](tutorial-part1-credit-risk.md).

### <a name="the-predictive-experiment"></a>Esperimento predittivo
Quando si dispone di un modello sottoposto a training nell'esperimento di training, fare clic su **Configura servizio Web** e selezionare **servizio Web predittivo** in Machine Learning Studio (classico) per avviare il processo di conversione dell'esperimento di training in un ***esperimento predittivo***. Lo scopo dell'esperimento predittivo è quello di usare il modello sottoposto a training per assegnare un punteggio ai nuovi dati, con l'obiettivo di renderlo infine operativo come servizio Web di Azure.

Questa conversione viene eseguita automaticamente con i passaggi seguenti:

* Conversione del set di moduli usati per il training in un singolo modulo che viene salvato come modello sottoposto a training
* Eliminazione di eventuali moduli estranei non correlati all'assegnazione dei punteggi
* Aggiunta di porte di input e di output che verranno usate dal servizio Web finale

È possibile apportare altre modifiche per preparare l'esperimento predittivo per la distribuzione come servizio Web. Se, ad esempio, è necessario che il servizio Web restituisca solo un subset di risultati, è possibile aggiungere un modulo di filtro prima della porta di output.

In questo processo di conversione l'esperimento di training non viene rimosso. Al termine del processo, sono disponibili due schede in studio (classico): una per l'esperimento di training e una per l'esperimento predittivo. In questo modo, è possibile apportare modifiche all'esperimento di training prima di distribuire il servizio Web e ricompilare l'esperimento predittivo. In alternativa, è possibile salvare una copia dell'esperimento di training per iniziare un'altra linea di sperimentazione.

> [!NOTE]
> Quando si fa clic su **Predictive Web Service** (Servizio Web predittivo), si avvia un processo automatico di conversione dell'esperimento di training in esperimento predittivo, che nella maggior parte dei casi è un'operazione appropriata. Se però l'esperimento di training è complesso (ad esempio, con più percorsi per il training uniti insieme), è preferibile eseguire questa conversione manualmente. Per ulteriori informazioni, vedere [come preparare il modello per la distribuzione in Azure Machine Learning Studio (classico)](convert-training-experiment-to-scoring-experiment.md).
>
>

### <a name="the-web-service"></a>Servizio Web
Una volta verificato che l'esperimento predittivo è pronto, è possibile distribuire il servizio come servizio Web classico o come nuovo servizio Web basato su Azure Resource Manager. Per rendere operativo il modello distribuendolo come *servizio Web classico di Machine Learning*, fare clic su **Deploy Web Service** (Distribuisci servizio Web) e selezionare **Deploy Web Service [Classic]** (Distribuisci servizio Web [Classico]). Per eseguire la distribuzione come *nuovo servizio Web di Machine Learning*, fare clic su **Deploy Web Service** (Distribuisci servizio Web) e selezionare **Deploy Web Service [New]** (Distribuisci servizio Web [Nuovo]). Gli utenti ora possono inviare i dati al modello con l'API REST del servizio Web e ricevere i risultati. Per altre informazioni, vedere [Come usare un servizio Web di Azure Machine Learning pubblicato da un esperimento di Machine Learning](consume-web-services.md).

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>Caso non tipico: creazione di un servizio Web non predittivo
Se l'esperimento non esegue il training di un modello di analisi predittiva, non è necessario creare un esperimento di training e un esperimento di assegnazione dei punteggi: è disponibile un solo esperimento ed è possibile distribuirlo come servizio Web. Machine Learning Studio (classico) rileva se l'esperimento contiene un modello predittivo analizzando i moduli usati.

Dopo l'iterazione dell'esperimento e la relativa soddisfazione:

1. Fare clic su **Set Up Web Service** (Configura servizio Web) e selezionare **Retraining Web Service** (Servizio Web di ripetizione del training). I nodi di input e di output verranno aggiunti automaticamente.
2. Fare clic su **Esegui**
3. Fare clic su **Deploy Web Service** (Distribuisci servizio Web) e selezionare **Deploy Web Service [Classic]** (Distribuisci servizio Web [Classico]) o **Deploy Web Service [New]** (Distribuisci servizio Web [Nuovo]), a seconda dell'ambiente in cui si vuole eseguire la distribuzione.

Il servizio Web ora è distribuito ed è possibile accedervi e gestirlo proprio come si fa con un servizio Web predittivo.

## <a name="updating-your-web-service"></a>Aggiornamento del servizio Web
Ora che l'esperimento è stato distribuito come servizio Web, cosa accade se è necessario aggiornarlo?

Dipende da che cosa è necessario aggiornare:

**Si vuole modificare l'input o l'output oppure si vuole modificare il modo in cui il servizio Web modifica i dati**

Se non si modifica il modello, ma si sta semplicemente modificando il modo in cui il servizio Web gestisce i dati, è possibile modificare l'esperimento predittivo, quindi fare clic su **Distribuisci servizio Web** e selezionare **Distribuisci servizio Web [classico]** o **Distribuisci servizio Web [nuovo]** . Il servizio Web viene arrestato, l'esperimento predittivo aggiornato viene distribuito e il servizio Web viene riavviato.

Ecco un esempio: si supponga che l'esperimento predittivo restituisca l'intera riga di dati di input con il risultato stimato. È possibile stabilire che il servizio Web deve restituire solo il risultato. È possibile aggiungere un modulo **Project Columns** nell'esperimento predittivo, subito prima della porta di output, per escludere le colonne diverse da quella del risultato. Quando si fa clic su **Deploy Web Service** (Distribuisci servizio Web) e si seleziona di nuovo **Deploy Web Service [Classic]** (Distribuisci servizio Web [Classico]) o **Deploy Web Service [New]** (Distribuisci servizio Web [Nuovo]), il servizio Web viene aggiornato.

**Si vuole ripetere il training del modello con i nuovi dati**

Per conservare il modello di Machine Learning, ripetendone però il training con i nuovi dati, sono disponibili due opzioni:

1. **Ripetere il training del modello mentre il servizio Web è in esecuzione**: per ripetere il training del modello mentre il servizio Web predittivo è in esecuzione, è necessario apportare alcune modifiche all'esperimento di training per renderlo un ***esperimento di ripetizione del training*** e quindi distribuirlo come ***servizio Web di ripetizione del training***. Per istruzioni in proposito, vedere [Ripetere il training dei modelli di Machine Learning a livello di codice](/azure/machine-learning/studio/retrain-machine-learning-model).
2. **Tornare all'esperimento di training originale e usare dati di training diversi per sviluppare il modello**: l'esperimento predittivo è collegato al servizio Web, ma l'esperimento di training non è direttamente collegato in questo modo. Se si modifica l'esperimento di training originale e si fa clic su **Set Up Web Service** (Configura servizio Web), verrà creato un *nuovo* esperimento predittivo che, se distribuito, creerà un *nuovo* servizio Web. Non è sufficiente aggiornare il servizio Web originale.

   Se è necessario modificare l'esperimento di training, aprirlo e fare clic su **Save As** (Salva con nome) per creare una copia. In questo modo l'esperimento di training, l'esperimento predittivo e il servizio Web originali rimarranno invariati. È quindi possibile creare un nuovo servizio Web con le modifiche. Dopo aver distribuito il nuovo servizio Web, è possibile decidere se arrestare il servizio Web precedente o mantenerlo in esecuzione insieme a quello nuovo.

**Si vuole eseguire il training di un modello diverso**

Per apportare modifiche all'esperimento predittivo originale, ad esempio selezionando un algoritmo di Machine Learning diverso, provando un metodo di training diverso e così via, è necessario attenersi alla seconda procedura descritta sopra per ripetere il training del modello: aprire l'esperimento di training, fare clic su **Save As** (Salva con nome) per creare una copia e quindi avviare il nuovo percorso di sviluppo del modello, creazione dell'esperimento predittivo e distribuzione del servizio Web. Verrà così creato un nuovo servizio Web non correlato a quello originale. È possibile decidere se lasciare in esecuzione uno dei due oppure entrambi.

## <a name="next-steps"></a>Passaggi successivi
Per altri dettagli sul processo di sviluppo di un esperimento, vedere gli articoli seguenti:

* conversione dell'esperimento: [come preparare il modello per la distribuzione in Azure Machine Learning Studio (classico)](convert-training-experiment-to-scoring-experiment.md)
* Distribuzione del servizio Web: [Distribuire un servizio Web di Azure Machine Learning](deploy-a-machine-learning-web-service.md)
* Ripetizione del training del modello: [Ripetere il training dei modelli di Machine Learning a livello di codice](/azure/machine-learning/studio/retrain-machine-learning-model)

Per esempi dell'intero processo, vedere:

* [Esercitazione di Machine Learning: creare il primo esperimento in Azure Machine Learning Studio (classico)](create-experiment.md)
* [Procedura dettagliata: sviluppare una soluzione di analisi predittiva per la valutazione del rischio di credito in Azure Machine Learning](tutorial-part1-credit-risk.md)

