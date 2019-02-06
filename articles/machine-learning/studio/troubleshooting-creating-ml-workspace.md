---
title: "Risoluzione dei problemi: Creare e connettersi a un'area di lavoro di Machine Learning Studio" titleSuffix: Descrizione di Azure Machine Learning Studio: Questa guida offre soluzioni per alcuni problemi frequenti relativi alla configurazione di aree di lavoro di Azure Machine Learning Studio.
services: machine-learning ms.service: machine-learning ms.subservice: studio ms.topic: article

author: ericlicoding ms.author: amlstudiodocs ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro ms.date: 20/03/2017
---
# <a name="troubleshooting-guide-create-and-connect-to-an-azure-machine-learning-studio-workspace"></a>Guida alla risoluzione dei problemi: Creare e connettersi a un'area di lavoro di Azure Machine Learning Studio
Questa guida offre soluzioni per alcuni problemi frequenti relativi alla configurazione di aree di lavoro di Azure Machine Learning Studio.



## <a name="workspace-owner"></a>Proprietario dell'area di lavoro
Per aprire un'area di lavoro in Machine Learning Studio, è necessario essere connessi con l'account Microsoft usato per creare l'area di lavoro oppure ricevere un invito dal proprietario per partecipare all'area di lavoro. Dal portale di Azure è possibile gestire l'area di lavoro e anche configurare l'accesso.

Per altre informazioni sulla gestione di un'area di lavoro, vedere [Gestire un'area di lavoro di Azure Machine Learning].

[Gestire un'area di lavoro di Azure Machine Learning]: manage-workspace.md

## <a name="allowed-regions"></a>Aree geografiche disponibili
Machine Learning è attualmente disponibile in un numero limitato di aree. Se la sottoscrizione non include una di queste aree, è possibile che venga visualizzato l'errore "Non sono presenti sottoscrizioni nelle aree consentite".

Per richiedere l'aggiunta di un'area alla sottoscrizione, creare una nuova richiesta al supporto tecnico Microsoft dal portale di Azure, scegliere **Fatturazione** come tipo di problema, quindi seguire le istruzioni per inviare la richiesta.

## <a name="storage-account"></a>Account di archiviazione
Il servizio Machine Learning necessita di un account di archiviazione per archiviare i dati. È possibile usare un account di archiviazione esistente oppure creare un nuovo account di archiviazione quando si crea la nuova area di lavoro di Machine Learning, se la quota disponibile è sufficiente per la creazione di un nuovo account di archiviazione.

Dopo aver creato una nuova area di lavoro di Machine Learning, è possibile accedere a Machine Learning Studio usando l'account Microsoft specificato per creare l'area di lavoro. Se viene visualizzato il messaggio di errore relativo all'area di lavoro non trovata, analogo a quanto mostrato nella schermata sottostante, usare la procedura seguente per eliminare i cookie dal browser.

![Area di lavoro non trovata][screen3]

**Per eliminare i cookie dal browser**

1. Se si usa Internet Explorer, fare clic sul pulsante **Strumenti** nell'angolo superiore destro, quindi selezionare **Opzioni Internet**.  

![Opzioni Internet][screen4]

2. Nella scheda **Generale** fare clic su **Elimina**.

![Generale][screen5]

3. Nella finestra di dialogo **Elimina cronologia esplorazioni** assicurarsi che l'opzione **Cookie e dati di siti Web** sia selezionata, quindi fare clic su **Elimina**.

![Eliminare i cookie][screen6]

Dopo l'eliminazione dei cookie, riavviare il browser e quindi passare alla pagina [Microsoft Azure Machine Learning](https://studio.azureml.net) . Quando vengono richiesti nome utente e password, immettere lo stesso account Microsoft usato per creare l'area di lavoro.

## <a name="comments"></a>Commenti

L'obiettivo consiste nel semplificare al massimo l'esperienza di Machine Learning. Inserire eventuali commenti e problemi nel [forum su Azure Machine Learning](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) per contribuire al miglioramento del prodotto.

[screen1]:media/troubleshooting-creating-ml-workspace/screen1.png
[screen2]:media/troubleshooting-creating-ml-workspace/screen2.png
[screen3]:media/troubleshooting-creating-ml-workspace/screen3.png
[screen4]:media/troubleshooting-creating-ml-workspace/screen4.png
[screen5]:media/troubleshooting-creating-ml-workspace/screen5.png
[screen6]:media/troubleshooting-creating-ml-workspace/screen6.png
