<properties
   pageTitle="Uso del connettore Azure Machine Learning nelle app per la logica| Servizio app di Microsoft Azure"
   description="Informazioni su come creare e configurare il connettore Azure Machine Learning e usarlo in un'app per la logica nel servizio app di Azure"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/31/2016"
   ms.author="jehollan"/>
   
# Panoramica
>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione dello schema 2014-12-01-preview delle app per la logica.

Il connettore Azure ML per le app per la logica consente di chiamare le API di Azure ML per l'assegnazione del punteggio batch (servizio Esecuzione batch) e la ripetizione del training. Queste funzionalità in combinazione con i trigger delle app per la logica consentono di pianificare processi batch e impostare la ripetizione pianificata del training dei modelli.

 ![][1]
 
## Introduzione all'uso del connettore Azure Machine Learning e relativa aggiunta all'app per la logica
Per iniziare, creare un esperimento in Azure ML Studio, quindi configurare e distribuire un servizio Web. È quindi possibile usare l'URL dell'API e la chiave dell'URL del post BES che si trova nella Guida di Esecuzione batch. ([Procedura dettagliata di Machine Learning](../machine-learning/machine-learning-walkthrough-5-publish-web-service.md))

Per eseguire un processo BES tramite il connettore, aggiungere il connettore Azure ML all'app per la logica. Immettere quindi le informazioni necessarie. Vedere di seguito per altre informazioni. Per impostare la ripetizione del training, aggiungere un altro connettore Azure ML e fornire i parametri di input. Vedere la pagina relativa alla [configurazione della ripetizione del training di un modello](../machine-learning/machine-learning-retrain-models-programmatically.md).

## Esecuzione di un processo di Esecuzione batch di Azure ML
Il connettore Azure ML fornisce le quattro opzioni seguenti per l'esecuzione dei processi di Esecuzione batch (BES):
1.	Processo batch con input e output: l'esperimento ha moduli di input e output del servizio Web
2.	Processo batch senza input e output: l'esperimento non ha un modulo di input o output del servizio Web (ad esempio, usa i moduli Reader e Writer)
3.	Processo batch con solo input: l'esperimento ha un modulo di input del servizio Web, ma nessun modulo di output (ad esempio, usa un modulo Writer)
4.	Processo batch con solo output: l'esperimento non ha un modulo input del servizio Web, ma ha un modulo di output (ad esempio, usa un modulo Reader) Si noti che BES è una richiesta asincrona il cui completamento può richiedere tempo, a seconda delle dimensioni dei dati e della complessità del modello. Al completamento del processo, il connettore restituisce il risultato di output.

### Eseguire Esecuzione Batch: con input e output
Se l'esperimento di Studio ha moduli di input e output del servizio Web, è necessario [fornire informazioni sull'account di archiviazione BLOB e la posizione](../machine-learning/machine-learning-consume-web-services.md). È anche possibile includere parametri globali (servizio Web) se configurati nell'esperimento ([Parametri del servizio Web di Machine Learning](../machine-learning/machine-learning-web-service-parameters.md)).

![][2]

Fare clic sui puntini di sospensione per mostrare e nascondere i campi dei parametri globali. In questo modo è possibile fornire uno o più parametri globali (servizio Web) in un elenco di campi e valori delimitati da virgole.

![][3]

Altre varianti nei processi BES, ad esempio un processo senza input o output del servizio Web, sono disponibili anche tramite il connettore.

## Configurazione della ripetizione del training

Usare l'azione Set Up Retraining per impostare la ripetizione occasionale o pianificata del training del modello ML. In combinazione con un processo di Esecuzione batch creato dal connettore, è possibile completare i passaggi per il training e l'aggiornamento del modello di un servizio Web. In questo flusso di lavoro si userà il connettore due volte.
1.	Il primo connettore viene usato per eseguire il processo BES per ripetere il training del modello e restituire l'output. L'output di questa esecuzione avrà l'URL del nuovo modello (.ilearner). Se è stata impostata in un esperimento, è anche possibile restituire l'URL dell'output del modulo di valutazione, che è un file CSV. Nel passaggio successivo è possibile usare i dati nell'output del modulo di valutazione per prendere la decisione di sostituire o meno il modello nel servizio Web (ad esempio, se Accuracy > 0,85).
1.	Il secondo connettore viene usato per configurare la ripetizione del training. Usa parametri dall'output del primo connettore in modo da verificare facoltativamente la condizione del modello di aggiornamento e aggiornare il servizio Web con il modello appena sottoposto a training.
  *	È ad esempio possibile accedere all'output del processo BES con l'URL del modello appena sottoposto a training immettendo `@{body('besconnector').Results.output2.FullURL}` nel campo relativo all'URL del modello per cui è stato ripetuto il training. Si presuppone che il nome dell'output del servizio Web nell'esperimento di training sia output2.
  *	Come nome della risorsa usare il nome completo del modello sottoposto a training salvato nell'esperimento predittivo, ad esempio MyTrainedModel [modello sottoposto a training]
  *	Nel campo relativo alla chiave dei risultati della valutazione è possibile immettere qualsiasi parametro restituito nell'output del modulo di valutazione dell'esperimento di training, se è stato incluso. Per vedere l'elenco dei parametri disponibili, visualizzare i risultati del modulo Evaluate nell'esperimento di training in Azure ML Studio. Per un esperimento di classificazione, sono inclusi Accuracy, Precision, Recall, F-Score, AUC, Average Log Loss e Training Log Loss.

![][4]
 
### Ripetizione pianificata del training
 
Con i trigger dell'app per la logica è possibile impostare l'esecuzione del connettore in base a una pianificazione. Questo può abilitare la ripetizione del training di un modello a intervalli regolari, quando arrivano nuovi dati. Il processo BES ripete il training del modello e l'azione Retraining aggiorna il modello dopo aver completato la ripetizione del training.
 
![][5]
 
## Output del connettore 
 
**BES**: dopo avere completato il processo batch, l'output del connettore includerà le informazioni seguenti per l'output di ogni servizio Web.
 
 ![][6]
 
Questo non sarà disponibile se non è stato incluso un output del servizio Web. Ad esempio, si usa un modulo Writer per scrivere in un database dall'esperimento in Studio.

**Ripetizione del training**: una volta completata la ripetizione del training, l'output includerà le informazioni seguenti.

![][7]

## Riepilogo

Con il connettore Azure ML per le app per la logica è possibile impostare l'esecuzione su richiesta o con una pianificazione ricorrente dei processi di assegnazione dei punteggi batch e ripetizione del training. La combinazione delle due azioni può classificare i dati e ripetere il training, valutare e aggiornare il modello del servizio Web automaticamente, senza dover scrivere codice.

 <!--Image references-->
[1]: ./media/app-service-logic-connector-azureml/img1.png
[2]: ./media/app-service-logic-connector-azureml/img2.png
[3]: ./media/app-service-logic-connector-azureml/img3.png
[4]: ./media/app-service-logic-connector-azureml/img4.png
[5]: ./media/app-service-logic-connector-azureml/img5.png
[6]: ./media/app-service-logic-connector-azureml/img6.png
[7]: ./media/app-service-logic-connector-azureml/img7.png

<!---HONumber=AcomDC_0803_2016-->