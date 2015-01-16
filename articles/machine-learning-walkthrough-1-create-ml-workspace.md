<properties title="Step 1: Create an Azure Machine Learning workspace" pageTitle="Passaggio 1: Creare un'area di lavoro di Machine Learning | Azure" description="Passaggio 1: Creazione di una nuova area di lavoro di Azure Machine Learning Studio" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/06/2014" ms.author="garye" />


Questo è il primo passaggio della procedura dettagliata [Sviluppare una soluzione predittiva con Machine Learning di Azure][develop]:

[develop]: ../machine-learning-walkthrough-develop-predictive-solution/

1.	**Creare un'area di lavoro ML**
2.	[Caricare i dati esistenti][upload-data]
3.	[Creare un nuovo esperimento][create-new]
4.	[Addestrare e valutare i modelli][train-models]
5.	[Pubblicare il servizio Web][publish]
6.	[Accedere al servizio Web][access-ws]

[create-workspace]: ../machine-learning-walkthrough-1-create-ml-workspace/
[upload-data]: ../machine-learning-walkthrough-2-upload-data/
[create-new]: ../machine-learning-walkthrough-3-create-new-experiment/
[train-models]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
[publish]: ../machine-learning-walkthrough-5-publish-web-service/
[access-ws]: ../machine-learning-walkthrough-6-access-web-service/

----------

# Passaggio 1: Creare un'area di lavoro di Machine Learning di Azure

Per usare ML Studio è necessario disporre di un'area di lavoro ML. Quest'area di lavoro contiene tutti gli strumenti necessari per la creazione, la gestione e la pubblicazione di esperimenti.  

##Per creare un'area di lavoro  

1.	Effettuare l'accesso con l'account Microsoft Azure.
2.	Nel pannello dei servizi di Microsoft Azure fare clic su **MACHINE LEARNING**.  
![Create workspace][1]

3.	Fare clic su **CREA UN'AREA DI LAVORO ML**.
4.	Nella pagina **CREAZIONE RAPIDA** immettere le informazioni sull'area di lavoro e fare clic su **CREA UN'AREA DI LAVORO ML**.

	>**NOTA**: il **PROPRIETARIO DELL'AREA DI LAVORO** corrisponde all'account Microsoft dell'utente (ad esempio nome@outlook.com) o all'account dell'organizzazione.

Dopo avere creato l'area di lavoro, questa sarà visualizzata nella pagina **machine learning**.  

>**Suggerimento** - È possibile condividere gli esperimenti su cui si sta lavorando invitando altri utenti nella propria area di lavoro. L'operazione si esegue nella pagina **IMPOSTAZIONI** di ML Studio. Sono necessari solo l'account Microsoft o account dell'organizzazione di ogni utente.

----------

**Passaggi successivi: [Caricare i dati esistenti][upload-data]**

[1]: ./media/machine-learning-walkthrough-1-create-ml-workspace/create1.png
