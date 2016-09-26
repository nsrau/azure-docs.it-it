<properties
	pageTitle="Creare un'area di lavoro di Machine Learning | Microsoft Azure"
	description="Come creare un'area di lavoro per Azure Machine Learning Studio."
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/16/2016"
	ms.author="garye;bradsev;ahgyger"/>


# Creare e condividere un'area di lavoro di Azure Machine Learning

Questo menu si collega ad argomenti che descrivono come configurare i diversi ambienti di scienza dei dati usati da Cortana Analytics Process (CAP).

[AZURE.INCLUDE [data-science-environment-setup](../../includes/cap-setup-environments.md)]

Per usare Azure Machine Learning Studio, è necessario disporre di un'area di lavoro di Machine Learning. Quest'area di lavoro contiene tutti gli strumenti necessari per la creazione, la gestione e la pubblicazione di esperimenti.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Per creare un'area di lavoro

1. Accedere al [portale di Microsoft Azure classico].

> [AZURE.NOTE] Per accedere, è necessario essere l'amministratore di una sottoscrizione di Azure. I proprietari di un'area di lavoro di Machine Learning non possono accedere al [portale di Microsoft Azure classico]. Per informazioni dettagliate, vedere [Privilegi dell'amministratore della sottoscrizione di Azure e del proprietario dell'area di lavoro](#subscriptionvsworkspace).

2. Nel riquadro dei servizi di Microsoft Azure fare clic su **MACHINE LEARNING**.

    ![Servizio Machine Learning][1]

3. Fare clic su **+NUOVO** nella parte inferiore della finestra.
4. Fare clic su **SERVIZI DATI**, quindi su **MACHINE LEARNING** e infine su **CREAZIONE RAPIDA**.

	![Creazione rapida di una nuova area di lavoro][3]

5. Immettere un **NOME AREA DI LAVORO** per l'area di lavoro.
6. Specificare un valore per **LOCALITÀ** di Azure, quindi immettere un **ACCOUNT DI ARCHIVIAZIONE** di Azure esistente o selezionare **Crea un nuovo account di archiviazione** per crearne uno nuovo.
7. Fare clic su **CREA UN'AREA DI LAVORO ML**.

Dopo avere creato l'area di lavoro di Machine Learning, questa sarà visualizzata nella pagina **Machine Learning**.

## Condivisione di un'area di lavoro di Azure Machine Learning

Dopo la creazione di un'area di lavoro di Machine Learning, è possibile invitare utenti all'area di lavoro e condividere l'accesso all'area di lavoro e a tutti i rispettivi esperimenti. Sono supportati due ruoli per gli utenti:

- **Utente**: un utente dell'area di lavoro può creare, aprire, modificare ed eliminare set di dati, esperimenti e servizi Web nell'area di lavoro.
- **Proprietario**: un proprietario può invitare, rimuovere ed elencare utenti con accesso all'area di lavoro, oltre a specificare le operazioni consentite a un utente. L'utente potrà anche accedere ai notebook.

### Per condividere un'area di lavoro
1. Accedere a [Machine Learning Studio]
2. Nel riquadro di Machine Learning Studio fare clic su **SETTINGS**
3. Fare clic su **USERS**
4. Fare clic su **INVITE MORE USERS**

    ![Invitare altri utenti][4]

5. Immettere uno o più indirizzi di posta elettronica. È necessario che l'utente abbia un account Microsoft valido, ad esempio name@outlook.com, o un account aziendale, da Azure Active Directory.
6. Fare quindi clic sul pulsante con il segno di spunta.

Ogni utente aggiunto riceverà un messaggio di posta elettronica con istruzioni per l'accesso all'area di lavoro condivisa.

Per informazioni sulla gestione dell'area di lavoro, vedere [Gestire un'area di lavoro di Azure Machine Learning]. Se si verifica un problema durante la creazione dell'area di lavoro, vedere [Guida per la risoluzione dei problemi: Creare un'area di lavoro di Machine Learning e connettersi a essa].

## <a name="subscriptionvsworkspace"></a>Privilegi dell'amministratore della sottoscrizione di Azure e del proprietario dell'area di lavoro

La tabella seguente chiarisce la differenza tra amministratore di una sottoscrizione di Azure e proprietario dell'area di lavoro.

| Azioni | Amministratore della sottoscrizione di Azure | Proprietario dell'area di lavoro |
| --------------			|:------------------------:| :----------------:|
| Accesso al [portale di Microsoft Azure classico]| Sì | No |
| Creazione di una nuova area di lavoro | Sì | No |
| Eliminazione di un'area di lavoro | Sì | No |
| Aggiunta di un endpoint a un servizio Web | Sì | No |
| Eliminazione di un endpoint da un servizio Web | Sì | No |
| Modifica della concorrenza per un servizio Web | Sì | No |
| Accesso a [Machine Learning Studio] | No * | Sì |


> [AZURE.NOTE] * L'amministratore di una sottoscrizione di Azure viene automaticamente aggiunto all'area di lavoro che crea in qualità di proprietario. Il ruolo di amministratore di una sottoscrizione di Azure non garantisce tuttavia l'accesso a qualsiasi area di lavoro inclusa nella sottoscrizione.

<!-- ![List of Machine Learning workspaces][2] -->

<!--Anchors-->
[To create a workspace]: #createworkspace

<!--Image references-->
[1]: media/machine-learning-create-workspace/cw1.png
[2]: media/machine-learning-create-workspace/cw2.png
[3]: media/machine-learning-create-workspace/cw4.png
[4]: media/machine-learning-create-workspace/cw5.png


<!--Link references-->
[Gestire un'area di lavoro di Azure Machine Learning]: machine-learning-manage-workspace.md
[Guida per la risoluzione dei problemi: Creare un'area di lavoro di Machine Learning e connettersi a essa]: machine-learning-troubleshooting-creating-ml-workspace.md
[Machine Learning Studio]: https://studio.azureml.net/
[portale di Microsoft Azure classico]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0914_2016-->