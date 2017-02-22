---
title: Effettuare la conversione in un esperimento predittivo in Azure Machine Learning | Documentazione Microsoft
description: "Come convertire un esperimento di training di Machine Learning usato per il training del modello di analisi predittiva in un esperimento predittivo che può essere distribuito come servizio Web."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: eb943c45-541a-401d-844a-c3337de82da6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2016
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 66fb3dc316ce25aea4dff4add5c25b7f0f56ad7a
ms.openlocfilehash: 1ed2ee17e6b4d0256707bc63ac450b33ad9ef162


---
# <a name="convert-a-machine-learning-training-experiment-to-a-predictive-experiment"></a>Convertire un esperimento di training di Machine Learning in un esperimento predittivo
Azure Machine Learning consente di compilare, testare e distribuire soluzioni di analisi predittiva.

Dopo avere creato ed eseguito l'iterazione di un *esperimento di training* per eseguire il training del modello di analisi predittiva e si è pronti a usarlo per assegnare un punteggio a nuovi dati, è necessario preparare e semplificare l'esperimento per l'assegnazione di punteggi. È quindi possibile distribuire questo *esperimento predittivo* come servizio Web di Azure, per consentire agli utenti di inviare dati al modello e ricevere le relative stime.

Effettuando la conversione in un esperimento predittivo, si prepara il modello sottoposto a training per la distribuzione come servizio Web. Gli utenti del servizio Web possono inviare dati di input al modello che poi restituirà i risultati della stima. Quindi se si effettua la conversione in un esperimento predittivo, è consigliabile valutare in che modo il modello verrà usato da altri utenti.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Il processo di conversione di un esperimento di training in un esperimento predittivo prevede tre passaggi:

1. Salvare il modello di Machine Learning sottoposto a training e quindi sostituire l'algoritmo di Machine Learning e i moduli [Train Model][train-model] (Modello di training) con il proprio modello sottoposto a training salvato.
2. Organizzare l'esperimento solo per i moduli necessari alla valutazione. Un esperimento di training include un numero di moduli necessario al training. Tali moduli non sono più necessari quando il modulo è stato sottoposto a training ed è pronto per la valutazione.
3. Definire il punto dell'esperimento in cui accettare i dati dall'utente servizio Web e i dati da restituire.

## <a name="set-up-web-service-button"></a>Pulsante Set Up Web Service
Dopo avere eseguito l'esperimento (pulsante **RUN** (ESEGUI) nella parte inferiore dell'area di disegno dell'esperimento), il pulsante **Set Up Web Service** (Configura servizio Web), disponibile selezionando l'opzione **Predictive Web Service** (Servizio Web predittivo), eseguirà automaticamente i tre passaggi per la conversione dell'esperimento di training in un esperimento predittivo:

1. Salva il modello sottoposto a training come modulo nella sezione **Trained Models** (Modelli sottoposti a training) della tavolozza del modulo (a sinistra dell'area di disegno dell'esperimento), quindi sostituisce l'algoritmo di Machine Learning e i moduli [Train Model][train-model] con il modello sottoposto a training salvato.
2. Rimuove i moduli chiaramente non necessari. L'esempio include i moduli [Split Data][split] (Dividi dati), il secondo modulo [Score Model][score-model] (Modello di punteggio) ed [Evaluate Model][evaluate-model] (Modello di valutazione).
3. Crea i moduli di input e output del servizio Web e li aggiunge alle posizioni predefinite nell'esperimento.

Ad esempio, il seguente esperimento esegue il training di un modello di albero delle decisioni incrementato a due classi utilizzando dati di classificazione di esempio:

![esperimento di training][figure1]

I moduli nell'esperimento eseguono fondamentalmente quattro diverse funzioni:

![Funzioni del modulo][figure2]

Quando si converte l'esperimento di training in un esperimento predittivo, alcuni di questi moduli non sono più necessari o hanno uno scopo diverso:

* **Data** (Dati): i dati in questo set di dati di esempio non vengono usati durante l'assegnazione di un punteggio. L'utente del servizio Web fornisce i dati da classificare. Tuttavia, i metadati di questo set di dati, ad esempio i tipi di dati, vengono usati dal modello sottoposto a training. È quindi necessario mantenere il set di dati nell'esperimento predittivo perché possa fornire questi metadati.
* **Prep** : in base ai dati che verranno inviati per la classificazione, i moduli potrebbero non essere necessari per elaborare i dati in ingresso.
  
    In questo esempio, nel set di dati di esempio potrebbero mancare dei valori e sono incluse delle colonne non necessarie per il eseguire il training del modello. Sono stati quindi inclusi un modulo [Clean Missing Data][clean-missing-data] (Pulisci dati mancanti) per gestire i valori mancanti e un modulo [Select Columns in Dataset][select-columns] (Seleziona colonne in set di dati) per escludere le colonne aggiuntive dal flusso di dati. Se si è certi che i dati inviati per la classificazione tramite il servizio Web non presentino valori mancanti, è possibile rimuovere il modulo [Clean Missing Data][clean-missing-data]. Tuttavia, poiché il modulo [Select Columns in Dataset][select-columns] consente di definire il set di funzionalità da classificare, il modulo deve essere mantenuto.
* **Train** (Training): questi moduli vengono usati per eseguire il training del modello. Quando si fa clic su **Set Up Web Service** (Configura servizio Web), questi moduli vengono sostituiti con un modulo del modello sottoposto a training singolo. Il nuovo modulo viene salvato nella sezione **Trained Models** (Modelli sottoposti a training) della tavolozza dei moduli.
* **Score** : in questo esempio il modulo Split viene usato per suddividere il flusso di dati in un set di dati di test e dati di training. Nell'esperimento predittivo non è necessario, quindi può essere rimosso. In modo analogo, il secondo modulo [Score Model][score-model] e il modulo [Evaluate Model][evaluate-model] vengono usati per confrontare i risultati dei dati di test, quindi questi moduli non sono necessari nell'esperimento predittivo. Il modulo rimanente [Score Model][score-model] è però necessario per restituire un risultato relativo all'assegnazione del punteggio tramite il servizio Web.

Ecco come appare l'esempio dopo aver fatto clic su **Set Up Web Service**:

![Esperimento predittivo convertito][figure3]

Tutto ciò potrebbe essere sufficiente per preparare l'esperimento alla distribuzione come servizio Web. Tuttavia, potrebbe essere necessario eseguire ulteriori operazioni specifiche per l'esperimento.

### <a name="adjust-input-and-output-modules"></a>Regolazione dei moduli di input e output
Nell'esperimento di training è stato utilizzato un set di dati di training e quindi sono state eseguite delle operazioni di elaborazione per ottenere i dati in un formato utile all'algoritmo Machine Learning necessario. Se i dati che si prevede di ricevere tramite il servizio Web non richiedono questo tipo di elaborazione, è possibile spostare il modulo **Web service input** (Input servizio Web) in un nodo diverso dell'esperimento (fare clic sull'output del modulo **Web service input** e trascinarlo sulla porta di input del modulo appropriato).

Per impostazione predefinita, ad esempio, **Set Up Web Service** (Configura servizio Web) inserisce il modulo **Web service input** (Input del servizio Web) all'inizio del flusso di dati, come illustrato nella figura precedente. Tuttavia, se i dati di input non richiedono questo tipo di elaborazione, è possibile posizionare manualmente il modulo **Web service input** dopo i moduli di elaborazione dei dati:

![Spostamento del modulo web service input][figure4]

I dati di input forniti tramite il servizio Web ora passano direttamente al modulo Score Model senza pre-elaborazione.

In modo analogo, per impostazione predefinita, **Set Up Web Service** inserisce il modulo di output del servizio Web in fondo al flusso di dati. In questo esempio il servizio Web restituisce all'utente l'output del modulo [Score Model][score-model] che include il vettore dei dati di input completo, oltre ai risultati dell'assegnazione del punteggio.

Se però si preferisce restituire un valore diverso, è possibile aggiungere moduli aggiuntivi prima del modulo **Web service output** (Output servizio Web). Ad esempio, se si desidera restituire solo i risultati dell'assegnazione del punteggio e non l'intero vettore dei dati di input, è possibile aggiungere un modulo [Select Columns in Dataset][select-columns] per escludere tutte le colonne tranne i risultati dell'assegnazione del punteggio. Spostare quindi il modulo **Web service output** nell'output del modulo [Select Columns in Dataset][select-columns]. L'esperimento dovrebbe risultare simile al seguente:

![Spostamento del modulo web service output][figure5]

### <a name="add-or-remove-additional-data-processing-modules"></a>Aggiungere o rimuovere i moduli di elaborazione dati aggiuntivi
Se presenti nell'esperimento, è possibile rimuove i moduli di cui si è certi che non saranno utili per la classificazione. Ad esempio, poiché il modulo **Web service input** è stato spostato in un punto successivo ai moduli di elaborazione dei dati, è possibile rimuovere il modulo [Clean Missing Data][clean-missing-data] dall'esperimento predittivo.

L'esperimento predittivo ora appare come illustrato di seguito:

![Rimozione del modulo aggiuntivo][figure6]

> [!TIP]
> Si noti che è non è stato rimosso il set di dati, né il modulo [Select Columns in Dataset][select-columns]. Il modello nel servizio Web non userà i dati nel set di dati originale, ma i metadati definiti nel set di dati, ad esempio il tipo di dati di ogni colonna. Analogamente, il modulo [Select Columns in Dataset][select-columns] identifica quali colonne di dati verranno usate dal modello. Pertanto, entrambi questi moduli devono essere mantenuti nell'esperimento predittivo.

### <a name="add-optional-web-service-parameters"></a>Aggiungere parametri facoltativi al servizio Web
In alcuni casi, è possibile consentire all'utente del servizio Web di modificare il comportamento dei moduli quando si accede al servizio. *I parametri del servizio Web* consentono di eseguire questa operazione.

Un esempio comune è la configurazione di un modulo [Import Data][import-data] (Importazione dati) per consentire all'utente del servizio Web distribuito di specificare un'origine dati diversa quando si accede al servizio Web oppure la configurazione di un modulo [Export Data][export-data] (Esporta dati) in modo che sia possibile specificare una destinazione differente.

È possibile definire i parametri del servizio Web e associarli a uno o più parametri di modulo e specificare se sono obbligatori o facoltativi. Effettuando l'accesso al servizio e modificando adeguatamente le azioni del modulo, l'utente del servizio Web può fornire valori per tali parametri.

Per altre informazioni sui parametri del servizio Web, vedere [Usare i parametri del servizio Web di Azure Machine Learning][webserviceparameters].

[webserviceparameters]: machine-learning-web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Distribuire l'esperimento predittivo come servizio Web
Ora che l'esperimento predittivo è stato sufficientemente preparato, è possibile distribuirlo come servizio Web di Azure. Usando il servizio Web, gli utenti possono inviare dati al modello che poi restituirà le stime.

Per altre informazioni sul processo di distribuzione completo, vedere [Distribuire un servizio Web di Azure Machine Learning][deploy].

[deploy]: machine-learning-publish-a-machine-learning-web-service.md


<!-- Images -->
[figure1]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure1.png
[figure2]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure2.png
[figure3]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure3.png
[figure4]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure4.png
[figure5]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure5.png
[figure6]:./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure6.png


<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/



<!--HONumber=Jan17_HO5-->


