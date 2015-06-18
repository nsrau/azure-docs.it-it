<properties 
	pageTitle="Pubblicare un servizio Web di Machine Learning | Azure" 
	description="Come convertire un esperimento di training in un esperimento di assegnazione dei punteggi, prepararlo per la pubblicazione e pubblicarlo come servizio Web di Azure Machine Learning." 
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
	ms.date="03/04/2015" 
	ms.author="garye"/>

#Pubblicare un servizio Web di Azure Machine Learning

Azure Machine Learning consente di compilare, testare e distribuire soluzioni di analisi predittiva.

In generale, questo avviene in tre passaggi:

- **[Creare un esperimento di training]** - Azure Machine Learning Studio è un ambiente di sviluppo visivo di collaborazione che consente di eseguire il training e il test di un modello di analisi predittiva usando i dati di training forniti.
- **[Convertirlo in un esperimento di classificazione]** - Dopo aver sottoposto il modello a training con i dati esistenti, preparare e semplificare l'esperimento per la classificazione quando si è pronti a usarlo per valutare nuovi dati.
- **[Pubblicarlo come servizio Web]** - Con un semplice clic è possibile pubblicare l'esperimento di classificazione come servizio Web di Azure. Gli utenti possono inviare dati al modello e ricevere le relative previsioni.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Dopo aver pubblicato il servizio Web, è possibile:

- **[Accedervi]** tramite l'API del servizio Web
- **[Gestirlo]** tramite il portale di gestione di Azure e
- **[Aggiornarlo]** se il modello viene modificato

[Creare un esperimento di training]: #create-a-training-experiment
[Convertirlo in un esperimento di classificazione]: #convert-the-training-experiment-to-a-scoring-experiment
[Pubblicarlo come servizio Web]: #publish-the-scoring-experiment-as-a-web-service
[Accedervi]: #access-the-web-service
[Gestirlo]: #manage-the-web-service-in-the-azure-management-portal
[Aggiornarlo]: #update-the-web-service


##Creare un esperimento di training

Per il training di un modello di analisi predittiva, usare Azure Machine Learning Studio per creare un esperimento di training, in cui vengono inclusi diversi moduli per caricare i dati di training, prepararli, applicare gli algoritmi di apprendimento e valutare i risultati. È possibile eseguire l'iterazione dell'esperimento e provare diversi algoritmi di apprendimento per confrontare e valutare i risultati.

Il processo di creazione e gestione degli esperimenti di training è trattato in modo più approfondito altrove. Vedere gli articoli seguenti per altre informazioni ed esempi:

- [Creare un semplice esperimento in Azure Machine Learning Studio](machine-learning-create-experiment.md)
- [Sviluppare una soluzione predittiva con Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md)
- [Importare dati di training in Azure Machine Learning Studio](machine-learning-import-data.md)
- [Gestire iterazioni dell'esperimento in Azure Machine Learning Studio](machine-learning-manage-experiment-iterations.md)

##Convertire l'esperimento di training in un esperimento di classificazione

Dopo aver eseguito il training del modello, è possibile usarlo per valutare nuovi dati. A tale scopo, è necessario convertire l'esperimento di training in un esperimento di classificazione. Effettuando la conversione a un esperimento di classificazione, si prepara il modello sottoposto a training per essere pubblicato come servizio Web di classificazione. Gli utenti del servizio Web possono inviare dati di input al modello che poi restituirà i risultati della stima. Pertanto, effettuando la conversione a un esperimento di valutazione, è possibile valutare il modo in cui il modello verrà usato da altri utenti.

![Eseguire la conversione a un esperimento di assegnazione dei punteggi](./media/machine-learning-publish-a-machine-learning-web-service/figure-1.png)

Per ulteriori dettagli su come eseguire questa conversione, vedere [Conversione di un esperimento di training di Machine Learning in un esperimento di classificazione](machine-learning-convert-training-experiment-to-scoring-experiment.md)


##Pubblicazione dell'esperimento di classificazione come servizio Web

Ora che l'esperimento di classificazione è stato sufficientemente preparato, è possibile pubblicarlo come servizio Web di Azure. Usando il servizio Web, gli utenti possono inviare dati al modello che poi restituirà le previsioni.

Per pubblicare l'esperimento di classificazione, fare clic su **Esegui** nella parte inferiore dell'area di disegno dell'esperimento, quindi fare clic su **PUBBLICA SERVIZIO WEB**. Il servizio Web viene configurato e viene visualizzato il dashboard del servizio Web.

![Pubblicare il servizio Web](./media/machine-learning-publish-a-machine-learning-web-service/figure-2.png)

Per verificare il servizio Web, fare clic sul collegamento **Test** nella scheda **DASHBOARD**. Viene visualizzata una finestra di dialogo che richiede i dati di input per il servizio. Si tratta delle colonne necessarie per l'esperimento di classificazione. Immettere un set di dati e quindi fare clic su **OK**. Il risultato generato dal servizio Web viene visualizzato in fondo al dashboard.

![Testare il servizio Web](./media/machine-learning-publish-a-machine-learning-web-service/figure-3.png)

Nella scheda **CONFIGURAZIONE** è possibile modificare il nome visualizzato per il servizio e assegnarvi una descrizione. Il nome e la descrizione vengono visualizzati nel portale di gestione di Azure, in cui vengono gestiti i servizi Web. È anche possibile abilitare la registrazione per diagnosticare eventuali problemi rilevati durante l'uso del servizio Web. Per altre informazioni, vedere [Abilitare la registrazione per i servizi Web di Machine Learning](machine-learning-web-services-logging.md).

![Configurare il servizio Web](./media/machine-learning-publish-a-machine-learning-web-service/figure-4.png)

È anche possibile inserire una descrizione dei dati di input e output e dei parametri del servizio Web immettendo una stringa per ciascuna colonna in **SCHEMA INPUT**, **SCHEMA OUTPUT** e **PARAMETRO DEL SERVIZIO WEB**. Queste descrizioni vengono usate nella documentazione del codice di esempio fornita per il servizio Web.

##Accedere al servizio Web

Dopo aver pubblicato il servizio Web da Machine Learning Studio, è possibile inviare dati al servizio e ricevere risposte in modo programmatico.

Il dashboard offre tutte le informazioni necessarie per accedere al servizio Web. Ad esempio, la chiave API viene offerta per consentire l'accesso autorizzato al servizio, mentre le pagine della guida API hanno lo scopo di aiutare nella scrittura di codice.

Per altre informazioni su come accedere a un servizio Web di Machine Learning, vedere [Come usare un servizio Web di Azure Machine Learning pubblicato](machine-learning-consume-web-services.md).


##Gestire il servizio Web nel portale di gestione di Azure

Nel portale di gestione di Azure è possibile gestire i servizi Web facendo clic sul servizio **Machine Learning**, aprendo l'area di lavoro Machine Learning e quindi aprendo il servizio Web dalla scheda **SERVIZI WEB**. Da questa pagina è possibile monitorare il servizio Web, aggiornarlo ed eliminarlo. È anche possibile aggiungere un secondo endpoint per il servizio Web oltre all'endpoint predefinito creato al momento della pubblicazione.

Per altre informazioni, vedere [Gestire un'area di lavoro di Machine Learning di Azure](machine-learning-manage-workspace.md).
<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning web service endpoints using the REST API, see **Azure machine learning web service endpoints**. 
-->


##Aggiornare il servizio Web

È possibile apportare modifiche al servizio Web, ad esempio aggiornare il modello con ulteriori dati di training e ripubblicarlo, sovrascrivendo il servizio Web originale.

Per aggiornare il servizio Web, aprire l'esperimento di assegnazione dei punteggi originale usato per pubblicare il servizio Web originale e creare una copia modificabile facendo clic su **SALVA CON NOME**. Eseguire le modifiche e fare clic su **PUBBLICA SERVIZIO WEB**. Poiché l'esperimento è stato pubblicato in precedenza, Machine Learning Studio chiede se si desidera sovrascrivere il servizio esistente. Facendo clic su **SÌ**, il servizio Web esistente si arresta e al suo posto viene pubblicato il nuovo esperimento di assegnazione dei punteggi.

> [AZURE.NOTE]Se sono state apportate modifiche di configurazione al servizio Web originale, ad esempio immettendo un nuovo nome o descrizione, è necessario immettere nuovamente questi valori.


<!--HONumber=54--> 