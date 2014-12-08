<properties title="Troubleshooting Guide: Creating and connecting to an Azure Machine Learning workspace" pageTitle="Guida per la risoluzione dei problemi: Creazione e connessione a un'area di lavoro di Machine Learning di Azure | Azure" description="Solutions for common issues in creating and connecting to an Azure Machine Learning workspace " metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/25/2014" ms.author="garye" />


#Guida per la risoluzione dei problemi: Creazione e connessione a un'area di lavoro di Machine Learning di Azure

Questa guida offre soluzioni per alcuni problemi frequenti della configurazione di aree di lavoro di Machine Learning di Azure.

##Proprietario dell'area di lavoro

Quando si crea una nuova area di lavoro di Machine Learning di Azure, l'ID immesso nel campo WORKSPACE OWNER deve essere un account Microsoft valido (in precedenza Windows Live ID), ad esempio john-contoso@live.com o john-contoso@hotmail.com. Non è possibile usare un account non Microsoft, ad esempio l'account di posta elettronica aziendale. Per creare un account Microsoft gratuito, passare a [www.live.com](http://www.live.com). 

##Aree geografiche disponibili

Azure Machine Learning è attualmente disponibile in anteprima pubblica nell'area geografica Stati Uniti centro-meridionali. Se la sottoscrizione non include l'area Stati Uniti centro-meridionali, è possibile che sia visualizzato l'errore "Nessuna sottoscrizione disponibile nelle aree geografiche consentite. Aree consentite: Stati Uniti centro-meridionali." 

Per risolvere il problema, selezionare "Contatta il supporto Microsoft" (mostrato di seguito) nel portale di Azure, quindi scegliere **Fatturazione** come **TIPO DI SUPPORTO** per fare in modo che l'area geografica sia aggiunta alla sottoscrizione. Azure Machine Learning aggiungerà più aree geografiche nel corso del tempo.

![Contact Microsoft support][screen1]

##Account di archiviazione
 
Il servizio Azure Machine Learning necessita di un account di archiviazione per archiviare dati. È possibile usare un account di archiviazione esistente nell'area Stati Uniti centro-meridionali oppure creare un nuovo account di archiviazione quando si crea la nuova area di lavoro di Machine Learning, se la quota disponibile è sufficiente per la creazione di un nuovo account di archiviazione. Per verificare se è possibile creare un nuovo account di archiviazione nel portale di Azure, passare a **Impostazioni**, quindi a **Utilizzo**.

![Create workspace][screen2]

Dopo la creazione di una nuova area di lavoro di Machine Learning, sarà possibile accedere a Machine Learning Studio usando l'account Microsoft specificato come proprietario dell'area di lavoro. Se è visualizzato un errore di tipo area di lavoro non trovata, analogo a quanto mostrato nella schermata seguente, eseguire i passaggi seguenti per risolvere il problema.

![Workspace not found][screen3]

1. Scaricare i cookie del browser.

	Se si usa Internet Explorer, fare clic sul pulsante **Strumenti** nell'angolo superiore destro, quindi selezionare **Opzioni Internet**.  

	![Internet options][screen4]

	Nella scheda **Generale** fare clic su **Elimina**.

	![General tab][screen5]

	Nella finestra di dialogo **Elimina cronologia esplorazioni** assicurarsi che l'opzione **Cookie e dati di siti Web** sia selezionata, quindi fare clic su **Elimina**.

	![Delete cookies][screen6]

2. Dopo avere scaricato i cookie, riavviare il browser, quindi passare a [https://studio.azureml.net](https://studio.azureml.net). Quando sono richiesti nome utente e password, immettere lo stesso account Microsoft specificato come proprietario dell'area di lavoro.

Microsoft desidera semplificare al massimo l'esperienza di Machine Learning di Azure. Inserire eventuali commenti e problemi più avanti o nel [forum su Azure Machine Learning](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=MachineLearning) per contribuire a migliorare il prodotto. 

[screen1]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen1.png
[screen2]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen2.png
[screen3]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen3.png
[screen4]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen4.png
[screen5]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen5.png
[screen6]:./media/machine-learning-troubleshooting-creating-ml-workspace/screen6.png
