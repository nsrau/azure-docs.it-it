<properties 
	pageTitle="Risoluzione dei problemi: Creare e connettersi a un'area di lavoro di Machine Learning | Microsoft Azure" 
	description="Soluzioni per problemi comuni di creazione e connessione a un'area di lavoro di Azure Machine Learning" 
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
	ms.date="07/01/2015" 
	ms.author="garye"/>


#Guida per la risoluzione dei problemi: Creazione e connessione a un'area di lavoro di Machine Learning

Questa guida offre soluzioni per alcuni problemi frequenti relativi alla configurazione di aree di lavoro di Azure Machine Learning.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##Proprietario dell'area di lavoro

Quando si crea una nuova area di lavoro di Machine Learning, l'ID immesso nel campo WORKSPACE OWNER deve essere un account Microsoft valido (in precedenza Windows Live ID), ad esempio, john-contoso@live.com o john-contoso@hotmail.com. Non è possibile usare un account non Microsoft, ad esempio l'account di posta elettronica aziendale. Per creare un account Microsoft gratuito, passare a [www.live.com](http://www.live.com).

Tenere presente che l'account Microsoft usato per accedere al portale di Azure per creare un'area di lavoro non è automaticamente autorizzato ad *aprire* quell'area di lavoro, a meno di specificare quell'account come proprietario. Per aprire un'area di lavoro in Machine Learning Studio, è necessario essere connessi con l'account Microsoft definito come proprietario dell'area di lavoro oppure ricevere un invito dal proprietario per partecipare all'area di lavoro. Dal portale di Azure è tuttavia possibile *gestire* l'area di lavoro, con la possibilità, ad esempio, di modificare il proprietario e configurare l'accesso.

Per altre informazioni sulla gestione di un'area di lavoro, vedere [Gestire un'area di lavoro di Azure Machine Learning].

[Gestire un'area di lavoro di Azure Machine Learning]: machine-learning-manage-workspace.md

##Aree geografiche disponibili

Machine Learning è attualmente disponibile nell'area geografica Stati Uniti centro-meridionali. Se la sottoscrizione non include l'area Stati Uniti centro-meridionali, è possibile che venga visualizzato l'errore "Nessuna sottoscrizione disponibile nelle aree geografiche consentite. Aree consentite: Stati Uniti centro-meridionali".

Per risolvere il problema, come illustrato nella schermata seguente, selezionare **Contatta il supporto Microsoft** nel portale di gestione di Azure, quindi scegliere **Fatturazione** come **TIPO DI SUPPORTO** per richiedere l'aggiunta dell'area geografica alla sottoscrizione.

![Contattare il supporto Microsoft][screen1]

##Account di archiviazione
 
Il servizio Machine Learning necessita di un account di archiviazione per archiviare i dati. È possibile usare un account di archiviazione esistente nell'area Stati Uniti centro-meridionali oppure creare un nuovo account di archiviazione quando si crea la nuova area di lavoro di Machine Learning, se la quota disponibile è sufficiente per la creazione di un nuovo account di archiviazione. Per verificare se è possibile creare un nuovo account di archiviazione nel portale di gestione, passare a **Impostazioni**, quindi a **Utilizzo**.

![Creare un'area di lavoro][screen2]

Dopo la creazione di una nuova area di lavoro di Machine Learning, sarà possibile accedere a Machine Learning Studio usando l'account Microsoft specificato come proprietario dell'area di lavoro. Se viene visualizzato il messaggio di errore relativo all'area di lavoro non trovata, analogo a quanto mostrato nella schermata sottostante, usare la procedura seguente per eliminare i cookie dal browser.

![Area di lavoro non trovata][screen3]

**Per eliminare i cookie dal browser**

	If you use Internet Explorer, click the **Tools** button in the upper-right corner and select **Internet options**.  

	![Internet options][screen4]

	Under the **General** tab, click **Delete…**

	![General tab][screen5]

	In the **Delete Browsing History** dialog box, make sure **Cookies and website data** is selected, and click **Delete**.

	![Delete cookies][screen6]

Dopo l'eliminazione dei cookie, riavviare il browser e quindi passare alla pagina [Microsoft Azure Machine Learning](https://studio.azureml.net). Quando sono richiesti nome utente e password, immettere lo stesso account Microsoft specificato come proprietario dell'area di lavoro.

L'obiettivo consiste nel semplificare al massimo l'esperienza di Machine Learning. Inserire eventuali commenti e problemi nel [forum su Azure Machine Learning](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) per contribuire al miglioramento del prodotto.

[screen1]: media/machine-learning-troubleshooting-creating-ml-workspace/screen1.png
[screen2]: media/machine-learning-troubleshooting-creating-ml-workspace/screen2.png
[screen3]: media/machine-learning-troubleshooting-creating-ml-workspace/screen3.png
[screen4]: media/machine-learning-troubleshooting-creating-ml-workspace/screen4.png
[screen5]: media/machine-learning-troubleshooting-creating-ml-workspace/screen5.png
[screen6]: media/machine-learning-troubleshooting-creating-ml-workspace/screen6.png
 

<!---HONumber=Oct15_HO3-->