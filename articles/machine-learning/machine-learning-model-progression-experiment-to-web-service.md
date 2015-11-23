<properties
	pageTitle="Come un modello di Machine Learning passa da esperimento a servizio Web operativo | Microsoft Azure"
	description="Panoramica dei meccanismi in base ai quali il modello di Azure Machine Learning passa da un esperimento di sviluppo a un servizio Web operativo."
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/10/2015"
	ms.author="garye"/>


# Come un modello di Machine Learning passa da esperimento a servizio Web operativo

Un ***esperimento*** è un canvas in Azure Machine Learning Studio che consente di sviluppare in modo interattivo, eseguire, testare ed eseguire l'iterazione di un modello di analisi predittiva mentre lo si crea. È disponibile un'ampia gamma di moduli che è possibile usare per inserire i dati nell'esperimento, manipolare i dati, eseguire il training di un modello con gli algoritmi di Machine Learning, assegnare un punteggio al modello, valutare i risultati e generare l'output dei valori finali.

Una volta verificato l'esperimento, è possibile distribuirlo come ***servizio Web di Azure*** a cui gli utenti possono inviare nuovi dati e da cui possono ricevere i risultati.

In questo articolo verrà fornita una panoramica dei meccanismi in base ai quali il modello di Machine Learning passa da un esperimento di sviluppo a un servizio Web operativo.

>[AZURE.NOTE]È possibile sviluppare e distribuire i modelli di Machine Learning in altri modi, ma questo articolo descrive come farlo con Machine Learning Studio. Per informazioni su come creare un servizio Web predittivo con R, vedere il post di blog sulla [compilazione e la distribuzione di app Web predittive con RStudio e Azure ML](http://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx).

Anche se Azure Machine Learning Studio è progettato principalmente per sviluppare e distribuire un *modello di analisi predittiva*, è possibile usare Studio per sviluppare un esperimento che non include un modello di analisi predittiva. Un esperimento, ad esempio, potrebbe limitarsi a inserire i dati, manipolarli e quindi generare l'output dei risultati. Proprio come un esperimento di analisi predittiva, è possibile distribuirlo come esperimento non predittivo, ma il processo è più semplice perché l'esperimento non eseguirà il training né assegnerà un punteggio a un modello di Machine Learning. Anche se non è l'uso tipico di Studio, verrà incluso nella spiegazione seguente per poter offrire una panoramica completa del funzionamento di Studio.

## Sviluppo e distribuzione di un servizio Web predittivo

Ecco le fasi seguite da una soluzione tipica mentre viene sviluppata e distribuita con Machine Learning Studio:

![](media\machine-learning-model-progression-experiment-to-web-service\model-stages-from-experiment-to-web-service.png)

*Figura 1 - Fasi di un modello di analisi predittiva tipico*

### Esperimento di training

L'***esperimento di training*** è il canvas dell'esperimento iniziale in Machine Learning Studio. Lo scopo dell'esperimento di training è di fornire un'area in cui sviluppare, testare, eseguire l'iterazione e infine il training di un modello di Machine Learning. È possibile eseguire il training anche di più modelli contemporaneamente mentre si cerca la soluzione migliore, ma, una volta terminata la sperimentazione, si selezionerà un solo modello sottoposto a training e si elimineranno gli altri dall'esperimento. Per un esempio di sviluppo di un esperimento di analisi predittiva, vedere [Sviluppare una soluzione di analisi predittiva per la valutazione del rischio di credito in Azure Machine Learning](https://azure.microsoft.com/it-IT/documentation/articles/machine-learning-walkthrough-develop-predictive-solution/).

### Esperimento predittivo

Dopo avere creato un modello sottoposto a training nell'esperimento di training, fare clic su **Set Up Web Service** in Machine Learning Studio che eseguirà il processo di conversione dell'esperimento di training in un ***esperimento predittivo***. Lo scopo dell'esperimento predittivo è di usare il modello sottoposto a training per assegnare un punteggio ai nuovi dati, con l'obiettivo di diventare infine operativo come servizio Web di Azure.

Questa conversione viene eseguita automaticamente con i passaggi seguenti:

-   Conversione del set di moduli usati per il training in un singolo modulo che viene salvato come modello sottoposto a training

-   Eliminazione di eventuali moduli estranei non correlati all'assegnazione dei punteggi

-   Aggiunta di porte di input e di output che verranno usate dal servizio Web finale

È possibile apportare altre modifiche per preparare l'esperimento predittivo alla distribuzione come servizio Web. Se, ad esempio, è necessario che il servizio Web generi l'output solo di un subset di risultati, è possibile aggiungere un modulo filtro prima della porta di output.

In questo processo di conversione l'esperimento di training non viene rimosso. Al termine del processo, Studio includerà due schede: una per l'esperimento di training e una per l'esperimento predittivo. In questo modo, prima di distribuire il servizio Web, è possibile apportare modifiche all'esperimento di training e ricompilare l'esperimento predittivo. In alternativa, è possibile salvare una copia dell'esperimento di training per iniziare un'altra linea di sperimentazione.

>[AZURE.NOTE]Quando si fa clic su **Set Up Web Service**, si avvia un processo automatico per convertire l'esperimento di training in un esperimento predittivo e questo è l'ideale nella maggior parte dei casi. Se però l'esperimento di training è complesso (ad esempio, con più percorsi per il training uniti insieme), è preferibile eseguire questa conversione manualmente. Per altri dettagli su come funziona questo processo di conversione, vedere [Convertire un esperimento di training di Machine Learning in un esperimento predittivo](https://azure.microsoft.com/it-IT/documentation/articles/machine-learning-convert-training-experiment-to-scoring-experiment/).

### Servizio Web

Una volta verificato che l'esperimento predittivo sia pronto, fare clic su **Deploy Web Service** per rendere operativo il modello distribuendolo come ***servizio Web di Azure***. Gli utenti ora possono inviare i dati al modello con l'API REST del servizio Web e ricevere i risultati. Per altre informazioni in proposito, vedere [Come usare un servizio Web di Azure Machine Learning distribuito da un esperimento di Machine Learning](https://azure.microsoft.com/it-IT/documentation/articles/machine-learning-consume-web-services/).

Una volta distribuito il servizio Web, l'esperimento predittivo e il servizio Web rimangono connessi ed è possibile passare dall'uno all'altro:

|***In questa pagina…***|***fare clic su…***|***per aprire questa pagina…***|
| ------------------- | --------------- | ---------------------- |
|experiment canvas in Studio|**Go to web service**|web service configuration in Studio|
|web service configuration in Studio|**View latest**|experiment canvas in Studio|
|web service configuration in Studio|**Manage endpoints…**|endpoint management nel portale di Azure|
|endpoint management nel portale di Azure|**Edit in Studio**|experiment canvas in Studio|

![](media\machine-learning-model-progression-experiment-to-web-service\connections-between-experiment-and-web-service.png)

*Figura 2 - Connessioni tra un esperimento e il servizio Web*

## Caso non tipico: creazione di un servizio Web non predittivo

Se l'esperimento non esegue il training di un modello di analisi predittiva, non è necessario creare sia un esperimento di training che un esperimento di assegnazione dei punteggi. Esiste un solo esperimento ed è possibile distribuirlo come servizio Web (Machine Learning Studio rileva se l'esperimento contiene un modello predittivo analizzando i moduli usati).

Dopo avere eseguito l'iterazione dell'esperimento e averlo verificato:

1.  Fare clic su **Set Up Web Service**: vengono aggiunti automaticamente nodi di input e di output

2.  Fare clic su **Run**

3.  Fare clic su **Deploy Web Service**

Il servizio Web ora è distribuito ed è possibile accedervi e gestirlo proprio come un servizio Web predittivo.

## Pulsanti del servizio Web

In Machine Learning Studio i pulsanti del servizio Web (**Set Up Web Service** e **Deploy Web Service**) cambiano il nome e la funzione a seconda del punto raggiunto nel processo di sviluppo.

**L'esperimento contiene un modello predittivo**

Se l'esperimento esegue il training di un modello predittivo e gli assegna un punteggio, i pulsanti del servizio Web eseguono queste funzioni:

|**Tipo di esperimento**|**Pulsante**|**Risultato**|
| -------------------- | -------- | -------------- |
|Esperimento in fase di sviluppo|**Set Up Web Service**|Fornisce due opzioni|
|&nbsp;|- **Predictive Web Service**|Converte l'esperimento sia in un esperimento di training che in un esperimento di assegnazione dei punteggi|
|&nbsp;|- **Retraining Web Service**|Converte l'esperimento in un esperimento di ripetizione del training (vedere la sezione "Aggiornamento" più avanti)|
|Esperimento di training|**Set Up Web Service**|Fornisce due opzioni|
|&nbsp;|- **Update Predictive Experiment**|Aggiorna l'esperimento predittivo associato con le modifiche apportate all'esperimento di training|
|&nbsp;|- **Retraining Web Service**|Converte l'esperimento di training in un esperimento di ripetizione del training (vedere la sezione "Aggiornamento" più avanti)|
|&nbsp;|-*oppure*- **Deploy Web Service**|Se l'esperimento di ripetizione del training è stato configurato per la distribuzione, questa opzione lo distribuisce come servizio Web|
|Esperimento predittivo|**Deploy Web Service**|Distribuisce l'esperimento predittivo come servizio Web|

**L'esperimento *non* contiene un modello predittivo**

Se l'esperimento non esegue il training di un modello predittivo né gli assegna un punteggio, i pulsanti del servizio Web sono molto più semplici:

|**Tipo di esperimento**|**Pulsante**|**Risultato**|
| -------------------- | -------- | -------------- |
|Esperimento in fase di sviluppo|**Set Up Web Service**|Prepara l'esperimento per la distribuzione come servizio Web|
|Esperimento preparato per la distribuzione|**Deploy Web Service**|Distribuisce l'esperimento come servizio Web, apre la pagina di configurazione del servizio Web|

## Aggiornamento del servizio Web

Ora che l'esperimento è stato distribuito come servizio Web, che cosa accade se è necessario aggiornarlo?

Dipende da che cosa è necessario aggiornare:

**Si vuole cambiare l'input o l'output oppure si vuole modificare il modo in cui il servizio Web manipola i dati**

Se non si intende cambiare il modello, ma solo il modo in cui il servizio Web gestisce i dati, è possibile modificare l'esperimento predittivo e quindi fare ancora clic su **Deploy Web Service**. Il servizio Web si arresterà, l'esperimento predittivo aggiornato verrà distribuito e il servizio Web verrà nuovamente avviato.

Ecco un esempio: si supponga che l'esperimento predittivo restituisca l'intera riga di dati di input con il risultato previsto. È possibile che si preferisca che il servizio Web restituisca solo il risultato. È possibile aggiungere un modulo **Project Columns** nell'esperimento predittivo, subito prima della porta di output, per escludere le colonne diverse da quella del risultato. Quando si fa di nuovo clic su **Deploy Web Service**, il servizio Web viene aggiornato.

**Si vuole ripetere il training del modello con i nuovi dati**

Per conservare il modello di Machine Learning, ripetendone però il training con i nuovi dati, sono disponibili due opzioni:

1.  **Ripetere il training del modello mentre il servizio Web è in esecuzione**: per ripetere il training del modello mentre il servizio Web predittivo è in esecuzione, è possibile apportare alcune modifiche all'esperimento di training per renderlo un ***esperimento di ripetizione del training***, quindi distribuirlo come servizio ***Web di ripetizione del training***. Per istruzioni in proposito, vedere [Ripetere il training dei modelli di Machine Learning a livello di codice](https://azure.microsoft.com/it-IT/documentation/articles/machine-learning-retrain-models-programmatically/).

2.  **Tornare all'esperimento di training originale e usare dati di training diversi per sviluppare il modello**: l'esperimento predittivo è collegato al servizio Web, ma l'esperimento di training non è direttamente collegato in questo modo. Se si modifica l'esperimento di training originale e si fa clic su **Set Up Web Service**, verrà creato un *nuovo* esperimento predittivo che, se distribuito, creerà un *nuovo* servizio Web. Non verrà semplicemente aggiornato il servizio Web originale.

    Se quindi è necessario modificare l'esperimento di training, aprirlo e fare clic su **Save As** per creare una copia. In questo modo l'esperimento di training, l'esperimento predittivo e il servizio Web originali rimarranno invariati. Ora è possibile creare un nuovo servizio Web con le modifiche. Una volta distribuito il nuovo servizio Web, è possibile decidere se arrestare il servizio Web precedente o se lasciarlo in esecuzione insieme a quello nuovo.

**Si vuole eseguire il training di un modello diverso**

Per apportare modifiche all'esperimento predittivo originale, ad esempio selezionare un algoritmo di Machine Learning diverso, provare un metodo di training diverso e così via, è necessario attenersi alla seconda procedura descritta sopra per ripetere il training del modello: aprire l'esperimento di training, fare clic su **Save As** per creare una copia e quindi avviare il nuovo percorso di sviluppo del modello, creando l'esperimento predittivo e distribuendo il servizio Web. Verrà creato un nuovo servizio Web non correlato a quello originale. È possibile decidere se lasciare in esecuzione uno dei due oppure entrambi.

## Per altre informazioni

Per altri dettagli su questo processo, vedere gli articoli seguenti:

-   Conversione dell'esperimento: [Convertire un esperimento di training di Machine Learning in un esperimento predittivo](https://azure.microsoft.com/it-IT/documentation/articles/machine-learning-convert-training-experiment-to-scoring-experiment/)

-   Distribuzione del servizio Web: [Distribuire un servizio Web di Azure Machine Learning](https://azure.microsoft.com/it-IT/documentation/articles/machine-learning-publish-a-machine-learning-web-service/)

-   Ripetizione del training del modello: [Ripetere il training dei modelli di Machine Learning a livello di codice](https://azure.microsoft.com/it-IT/documentation/articles/machine-learning-retrain-models-programmatically/)

Per esempi dell'intero processo, vedere:

-   [Esercitazione di Machine Learning: Creare il primo esperimento in Azure Machine Learning Studio](https://azure.microsoft.com/it-IT/documentation/articles/machine-learning-create-experiment/)

-   [Procedura dettagliata: Sviluppare una soluzione di analisi predittiva per la valutazione del rischio di credito in Azure Machine Learning](https://azure.microsoft.com/it-IT/documentation/articles/machine-learning-walkthrough-develop-predictive-solution/)

<!---HONumber=Nov15_HO3-->