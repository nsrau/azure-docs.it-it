<properties 
	pageTitle="Usare i parametri del servizio Web di Azure Machine Learning | Microsoft Azure" 
	description="Come usare i parametri del servizio Web di Azure Machine Learning per modificare il comportamento del modello quando si accede al servizio Web." 
	services="machine-learning" 
	documentationCenter="" 
	authors="raymondlaghaeian" 
	manager="jhubbard" 
	editor="cgronlun"/>  

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/06/2016" 
	ms.author="raymondl;garye"/>

#Usare i parametri del servizio Web di Azure Machine Learning

Un servizio Web di Azure Machine Learning viene creato mediante la pubblicazione di un esperimento contenente moduli con parametri configurabili. In alcuni casi può essere utile modificare il comportamento del modulo mentre è in esecuzione il servizio Web. *I parametri del servizio Web* consentono di eseguire questa operazione.

Un esempio comune è la configurazione del modulo [Import Data][reader] per consentire all’utente del servizio Web pubblicato di specificare un’origine dati diversa quando si accede al servizio Web oppure la configurazione del modulo [Export Data][writer] in modo che sia possibile specificare una destinazione differente. Altri esempi includono la modifica del numero di bit per il modulo [Feature Hashing][feature-hashing] o il numero di funzionalità desiderate per il modulo [Filter-Based Feature Selection][filter-based-feature-selection].

È possibile impostare i parametri del servizio Web e associarli a uno o più parametri di modulo nell’esperimento, e specificare se sono obbligatori o facoltativi. L'utente del servizio web può quindi fornire valori per questi parametri quando si chiama il servizio web.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


##Come impostare e usare i parametri del servizio Web

È possibile definire un parametro del servizio Web facendo clic sull'icona accanto al parametro di un modulo e selezionando "Set as web service parameter". Verrà creato un nuovo parametro del servizio Web che verrà connesso al parametro del modulo. Quando si accede al servizio Web, l'utente può quindi specificare un valore per il parametro del servizio Web che verrà applicato al parametro del modulo.

Dopo aver definito un parametro del servizio Web, questo parametro sarà disponibile per qualsiasi altro parametro del modulo nell’esperimento. Se si definisce un parametro del servizio Web associato a un parametro per un modulo, è possibile usare lo stesso parametro per qualsiasi altro modulo, purché il parametro preveda lo stesso tipo di valore. Se ad esempio il parametro del servizio Web è un valore numerico, è possibile usarlo solo per i parametri del modulo che prevedono un valore numerico. Quando l'utente imposta un valore per il parametro del servizio Web, tale valore verrà applicato a tutti i parametri del modulo associati.

È possibile decidere se specificare un valore predefinito per il parametro del servizio Web. In questo caso per l'utente del servizio Web il parametro sarà facoltativo. Se si non specifica un valore predefinito, è necessario immettere un valore quando si accede al servizio Web.

La documentazione dell'API per il servizio Web includerà informazioni per l'utente del servizio Web su come specificare il parametro del servizio Web a livello di codice quando accede al servizio.

>[AZURE.NOTE] La documentazione dell'API per un servizio Web classico è disponibile tramite il collegamento **pagina della Guida dell'API** nel **DASHBOARD** del servizio Web in Machine Learning Studio. La documentazione dell'API per un nuovo servizio Web è disponibile tramite il portale dei [servizi Web di Azure Machine Learning](https://services.azureml.net/Quickstart) o nelle pagine **Consume** (Uso) e **API Swagger** per il servizio Web.


##Esempio

Si supponga ad esempio di disporre di un esperimento con un modulo [Export Data][writer] che invia informazioni all'archiviazione BLOB di Azure. Verrà definito un parametro del servizio Web denominato "percorso BLOB" che consente all'utente del servizio Web di modificare il percorso dell'archiviazione BLOB quando si accede al servizio.

1.	In Machine Learning Studio fare clic sul modulo [Export Data][writer] per selezionarlo. Le relative proprietà verranno visualizzate nel riquadro delle proprietà a destra dell'area di disegno dell'esperimento.

2.	Specificare il tipo di archiviazione:

    - In **Please specify data destination** (Specificare la destinazione dei dati) selezionare "Azure Blob Storage" (Archivio BLOB di Azure).
    - In **Please specify authentication type** selezionare "Account".
    - Immettere le informazioni dell'account per l'archiviazione BLOB di Azure.
    <p />

3.	Fare clic sull'icona a destra del **Path to blob beginning with container parameter**. L'aspetto sarà simile al seguente:

	![Icona del parametro del servizio Web][icon]

    Selezionare "Set as web service parameter".

    Verrà aggiunta una voce in **Web Service Parameters** nella parte inferiore del riquadro Proprietà con il nome "Path to blob beginning with container". Questo sarà il parametro del servizio Web associato al parametro del modulo [Export Data][writer].

4.	Per rinominare il parametro del servizio Web, fare clic sul nome, digitare "Blob path" e quindi premere **INVIO**.
 
5.	Per specificare un valore predefinito per il parametro del servizio Web, fare clic sull'icona a destra del nome, selezionare "Provide default value", immettere un valore (ad esempio, "container1/output1.csv") e quindi premere **INVIO**.

	![Parametro del servizio Web][parameter]

6.	Fare clic su **Run**.

7.	Fare clic su **Deploy Web Service** (Distribuisci servizio Web) e selezionare **Deploy Web Service [Classic]** (Distribuisci servizio Web [Classico]) o **Deploy Web Service [New]** (Distribuisci servizio Web [Nuovo]) per distribuire il servizio Web.

È ora possibile specificare una nuova destinazione per il modulo [Export Data][writer] quando si accede al servizio Web.

##Altre informazioni

Per un esempio più dettagliato, vedere la voce [Parametri del servizio Web](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) nel [blog di Machine Learning](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx).

Per altre informazioni sull'accesso a un servizio Web di Machine Learning, vedere l'articolo relativo all'[utilizzo di un servizio Web pubblicato di Machine Learning](machine-learning-consume-web-services.md).



<!-- Images -->  
[icon]: ./media/machine-learning-web-service-parameters/icon.png
[parameter]: ./media/machine-learning-web-service-parameters/parameter.png


<!-- Module References -->  
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
 

<!---HONumber=AcomDC_0914_2016-->