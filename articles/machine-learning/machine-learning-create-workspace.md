<properties 
	pageTitle="Creare un'area di lavoro di Machine Learning | Microsoft Azure" 
	description="Come creare un'area di lavoro per Azure Machine Learning Studio." 
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
	ms.date="04/07/2015" 
	ms.author="garye"/>


# Creare un'area di lavoro di Machine Learning di Azure 

Per usare Azure Machine Learning Studio, è necessario disporre di un'area di lavoro di Machine Learning. Quest'area di lavoro contiene tutti gli strumenti necessari per la creazione, la gestione e la pubblicazione di esperimenti.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Per creare un'area di lavoro

1. Accedere con il proprio account di Microsoft Azure.
2. Nel riquadro dei servizi di Microsoft Azure fare clic su **MACHINE LEARNING**.

    ![Servizio Machine Learning][1]

3. Fare clic su **+NUOVO** nella parte inferiore della finestra.
4. Fare clic su **SERVIZI DATI**, quindi su **MACHINE LEARNING** e infine su **CREAZIONE RAPIDA**.

	![Creazione rapida di una nuova area di lavoro][3]

5. Specificare i valori per **NOME AREA DI LAVORO** e **PROPRIETARIO DELL'AREA DI LAVORO**. Il proprietario dell'area di lavoro deve essere un account Microsoft valido (ad esempio, name@outlook.com).

    > [AZURE.NOTE]Successivamente, è possibile condividere gli esperimenti su cui si sta lavorando invitando altri utenti nella propria area di lavoro. Questa operazione può essere eseguita nella pagina **SETTINGS** di Machine Learning Studio. È sufficiente conoscere l'account Microsoft o l'account aziendale di ogni utente.

6. Specificare un valore per **LOCALITÀ** di Azure, quindi immettere un **ACCOUNT DI ARCHIVIAZIONE** di Azure esistente o selezionare **Crea un nuovo account di archiviazione** per crearne uno nuovo.
7. Fare clic su **CREA UN'AREA DI LAVORO ML**.

Dopo avere creato l'area di lavoro di Machine Learning, questa sarà visualizzata nella pagina **Machine Learning**.

Per informazioni sulla gestione dell'area di lavoro, vedere [Gestire un'area di lavoro di Azure Machine Learning]. Se si verifica un problema durante la creazione dell'area di lavoro, vedere [Guida per la risoluzione dei problemi: Creare un'area di lavoro di Machine Learning e connettersi a essa].

[Gestire un'area di lavoro di Azure Machine Learning]: machine-learning-manage-workspace.md
[Guida per la risoluzione dei problemi: Creare un'area di lavoro di Machine Learning e connettersi a essa]: machine-learning-troubleshooting-creating-ml-workspace.md
 
<!-- ![List of Machine Learning workspaces][2] -->

<!--Anchors-->
[To create a workspace]: #createworkspace

<!--Image references-->
[1]: media/machine-learning-create-workspace/cw1.png
[2]: media/machine-learning-create-workspace/cw2.png
[3]: media/machine-learning-create-workspace/cw3.png



<!--Link references-->

<!---HONumber=July15_HO1-->