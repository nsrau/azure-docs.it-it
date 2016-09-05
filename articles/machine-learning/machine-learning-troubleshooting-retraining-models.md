<properties
	pageTitle="Risoluzione dei problemi relativi alla ripetizione del training di un servizio Web classico di Azure Machine Learning | Microsoft Azure"
	description="Identificare e correggere i problemi comuni rilevati durante la ripetizione del training del modello per un servizio Web di Azure Machine Learning."
	services="machine-learning"
	documentationCenter=""
	authors="VDonGlover"
	manager=""
	editor=""/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/05/2016"
	ms.author="v-donglo"/>  

#Risoluzione dei problemi relativi alla ripetizione del training di un servizio Web classico di Azure Machine Learning

## Panoramica sulla ripetizione del training

Un esperimento predittivo distribuito come servizio Web di assegnazione dei punteggi è un modello statico. Quando sono disponibili nuovi dati o quando il consumer dell'API ha i propri dati, è necessario ripetere il training del modello.

Per una procedura dettagliata completa del processo di ripetizione del training di un servizio Web classico, vedere [Ripetere il training dei modelli di Machine Learning a livello di codice](machine-learning-retrain-models-programmatically.md).

## Processo di ripetizione del training

Quando è necessario ripetere il training del servizio Web, si devono aggiungere alcune parti:

* Un servizio Web distribuito dall'esperimento di training. L'esperimento deve avere un modulo **Web service output** (Output servizio Web) collegato all'output del modulo **Train Model** (Training modello).

	![Collegare l'output del servizio Web al modello di training.][image1]  

* Un nuovo endpoint aggiunto al servizio Web di assegnazione dei punteggi. È possibile aggiungere l'endpoint a livello di codice usando il codice di esempio a cui si fa riferimento nell'argomento Ripetere il training dei modelli di Machine Learning a livello di codice o tramite il portale di Azure classico.

È quindi possibile usare il codice C# di esempio dalla pagina della guida dell'API del servizio Web di training per ripetere il training del modello. Una volta valutati e approvati i risultati, si aggiorna il servizio Web di assegnazione di punteggi del modello con training usando il nuovo endpoint aggiunto.

Quando tutto è pronto, i passaggi principali da eseguire per ripetere il training del modello sono i seguenti:

1.	Chiamare il servizio Web di training: la chiamata è per il servizio Esecuzione batch (BES, Batch Execution Service), non per il servizio di richiesta-risposta (RRS, Request Response Service). È possibile usare il codice C# di esempio nella pagina della guida dell'API per eseguire la chiamata.
2.	Trovare i valori di *BaseLocation*, *RelativeLocation* e *SasBlobToken*: questi valori vengono restituiti nell'output della chiamata al servizio Web di training. ![Visualizzazione dell'output dell'esempio di ripetizione del training e dei valori di BaseLocation, RelativeLocation e SasBlobToken.][image6]
3.	Aggiornare l'endpoint aggiunto dal servizio Web di assegnazione dei punteggi con il nuovo modello con training: usando il codice di esempio fornito in Ripetere il training dei modelli di Machine Learning a livello di codice, aggiornare il nuovo endpoint aggiunto al modello di assegnazione dei punteggi con il nuovo modello con training dal servizio Web di training.

## Ostacoli comuni

### Verificare di avere l'URL PATCH corretto

L'URL PATCH in uso deve essere quello associato al nuovo endpoint di assegnazione dei punteggi aggiunto al servizio Web di assegnazione dei punteggi. È possibile ottenere l'URL PATCH in due modi:

**Opzione 1: Uso di C#**

Per ottenere l'URL PATCH corretto:

1.	Eseguire il codice di esempio [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs).
2.	Dall'output di AddEndpoint, trovare il valore *HelpLocation* e copiare l'URL.

	![HelpLocation nell'output dell'esempio addEndpoint.][image2]  

3.	Incollare l'URL in un browser per passare a una pagina contenente i collegamenti della guida per il servizio Web.
4.	Fare clic sul collegamento **Aggiorna risorsa** per aprire la pagina della guida per l'applicazione di patch.

**Opzione 2: Uso del portale di Azure**

1.	Accedere al [portale di Azure classico](https://manage.windowsazure.com).
2.	Aprire la scheda Machine Learning. ![Scheda Machine Learning.][image4]
3.	Fare clic sul nome dell'area di lavoro, quindi su **Servizi Web**.
4.	Fare clic sul servizio Web di assegnazione dei punteggi in uso. Se il nome del servizio Web non è stato modificato, terminerà con [Scoring Exp.].
5.	Fare clic su Aggiungi endpoint.
6.	Una volta aggiunto l'endpoint, fare clic sul nome dell'endpoint. Fare quindi clic su **Aggiorna risorsa** per aprire la pagina della guida per l'applicazione di patch.

>[AZURE.NOTE] Se è stato aggiunto l'endpoint al servizio Web di training anziché al servizio Web predittivo, si riceverà l'errore seguente quando si fa clic sul collegamento **Aggiorna risorsa**: Siamo spiacenti, ma questa funzionalità non è supportata o disponibile in questo contesto. Questo servizio Web non dispone di alcuna risorsa aggiornabile. Ci scusiamo per l'inconveniente e stiamo lavorando per migliorare questo flusso di lavoro.

![Dashboard del nuovo endpoint.][image3]  

La pagina della guida di PATCH contiene l'URL PATCH da usare e fornisce il codice di esempio che è possibile usare per chiamarlo.

![URL della patch.][image5]  

### Verificare di aggiornare l'endpoint di assegnazione dei punteggi corretto

* Non applicare la patch al servizio Web di training: l'operazione di applicazione della patch deve essere eseguita sul servizio Web di assegnazione dei punteggi.
* Non applicare la patch all'endpoint predefinito nel servizio Web: l'operazione di applicazione della patch deve essere eseguita sul nuovo endpoint di servizio Web di assegnazione dei punteggi aggiunto.

Per verificare in quale servizio Web si trova l'endpoint, visitare il portale di Azure classico.

>[AZURE.NOTE] Assicurarsi di aggiungere l'endpoint al servizio Web predittivo, non al servizio Web di training. Se sono stati distribuiti correttamente sia un servizio Web di training che uno predittivo, verranno visualizzati due servizi Web elencati separatamente. Il servizio Web predittivo deve terminare con "[predictive exp.]".

1.	Accedere al [portale di Azure classico](https://manage.windowsazure.com).
2.	Aprire la scheda Machine Learning. ![Interfaccia utente dell'area di lavoro di Machine Learning.][image4]
3.	Selezionare l'area di lavoro.
4.	Fare clic su **Servizi Web**.
5.	Selezionare il servizio Web predittivo.
6.	Verificare che il nuovo endpoint sia stato aggiunto al servizio Web.

### Controllare l'area di lavoro in cui si trova il servizio Web per assicurarsi che sia nell'area corretta

1.	Accedere al [portale di Azure classico](https://manage.windowsazure.com).
2.	Scegliere Machine Learning dal menu. ![Interfaccia utente dell'area di Machine Learning.][image4]
3.	Verificare la posizione dell'area di lavoro.

<!-- Image Links -->  

[image1]: ./media/machine-learning-troubleshooting-retraining-a-model/ml-studio-tm-connnected-to-web-service-out.png
[image2]: ./media/machine-learning-troubleshooting-retraining-a-model/addEndpoint-output.png
[image3]: ./media/machine-learning-troubleshooting-retraining-a-model/azure-portal-update-resource.png
[image4]: ./media/machine-learning-troubleshooting-retraining-a-model/azure-portal-machine-learning-tab.png
[image5]: ./media/machine-learning-troubleshooting-retraining-a-model/ml-help-page-patch-url.png
[image6]: ./media/machine-learning-troubleshooting-retraining-a-model/retraining-output.png

<!---HONumber=AcomDC_0824_2016-->