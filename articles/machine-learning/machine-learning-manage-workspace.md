<properties 
	pageTitle="Gestire un'area di lavoro di Machine Learning | Microsoft Azure" 
	description="Gestione dell'accesso alle aree di lavoro di Azure Machine Learning e distribuzione e gestione dei servizi Web API ML" 
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
	ms.date="07/10/2015" 
	ms.author="garye"/>


# Gestire un'area di lavoro di Azure Machine Learning 
Tramite il portale di gestione di Azure è possibile gestire le aree di lavoro di Machine Learning per:

- Monitorare la modalità d'uso dell'area di lavoro.
- Configurare l'area di lavoro per consentire o negare l'accesso.
- Gestire i servizi Web creati nell'area di lavoro.
- Eliminare l'area di lavoro.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Nella scheda Dashboard viene inoltre fornita una panoramica dell'utilizzo dell'area di lavoro e un riepilogo rapido delle informazioni relative all'area di lavoro.

> [AZURE.TIP]Nella scheda **WEB SERVICES** di Azure Machine Learning Studio è possibile aggiungere, aggiornare o eliminare un servizio Web di Machine Learning.

Per gestire un'area di lavoro:

1.	Accedere all'account Microsoft Azure, usando l'account associato alla sottoscrizione di Azure.
2.	Nel [portale di gestione di Azure](https://manage.windowsazure.com/) nel riquadro dei servizi di Microsoft Azure fare clic su **MACHINE LEARNING**.
3.	Fare clic sull'area di lavoro da gestire.

La pagina dell'area di lavoro contiene tre schede:

- **DASHBOARD**: permette di visualizzare i dati di utilizzo e le informazioni sull'area di lavoro.
- **CONFIGURE**: permette di gestire l'accesso all'area di lavoro.
- **WEB SERVICES**: permette di gestire i servizi Web pubblicati da questa area di lavoro.

  
## Per monitorare la modalità d'uso dell'area di lavoro

Fare clic sulla scheda **DASHBOARD**.

Dal dashboard è possibile visualizzare l'utilizzo complessivo dell'area di lavoro e ottenere un riepilogo rapido delle informazioni dell'area di lavoro.

- Il grafico **COMPUTE** mostra le risorse di calcolo in uso nell'area di lavoro. È possibile modificare la visualizzazione per mostrare i valori relativi o assoluti, nonché cambiare l'intervallo di tempo visualizzato nel grafico.
- **Usage overview** mostra l'archiviazione di Azure attualmente in uso nell'area di lavoro.
- **Quick glance** visualizza un riepilogo delle informazioni dell'area di lavoro e collegamenti utili.

> [AZURE.NOTE]Il link all’**Accesso a ML Studio** consente di aprire Machine Learning Studio usando l'account Microsoft con cui è stato eseguito l'accesso. L'account Microsoft usato per accedere al portale di Azure per creare un'area di lavoro non è automaticamente autorizzato ad aprire quell'area di lavoro. Per aprire un'area di lavoro, è necessario essere connessi con l'account Microsoft definito come proprietario dell'area di lavoro oppure ricevere un invito dal proprietario per partecipare all'area di lavoro.


## Per concedere o sospendere l'accesso agli utenti ##

Fare clic sulla scheda **CONFIGURE**.

Dalla scheda di configurazione è possibile:
 
- Sospendere l'accesso all'area di lavoro di Machine Learning facendo clic su DENY. Gli utenti non saranno più in grado di aprire l'area di lavoro in Machine Learning Studio. Per ripristinare l'accesso, fare clic su ALLOW.
- Modificare il proprietario dell'area di lavoro specificando un account Microsoft diverso. 

Per gestire utenti che hanno accesso all'area di lavoro in Machine Learning Studio, fare clic su **Accesso a ML Studio** nella scheda **DASHBOARD** (vedere la nota sopra relativa a **Accesso a ML Studio**). Verrà aperta l'area di lavoro in Machine Learning Studio. A questo punto, fare clic sulla scheda **SETTINGS** e quindi su **USERS**. È possibile fare clic su **INVITE MORE USERS** per concedere agli utenti l'accesso all'area di lavoro oppure selezionare un utente e fare clic su **REMOVE**.


## Per gestire i servizi Web in questa area di lavoro

Fare clic sulla scheda **WEB SERVICES**.

Verrà visualizzato un elenco di servizi Web pubblicati da questa area di lavoro. Per gestire un servizio Web, fare clic sul nome nell'elenco per aprire la pagina corrispondente.

Per un servizio Web possono essere definiti uno o più endpoint.

- È possibile definire endpoint aggiuntivi oltre a "Default". Per aggiungere un endpoint, fare clic su **ADD ENDPOINT** nella parte inferiore della pagina.

- Per eliminare un endpoint, tranne l'endpoint "Default", fare clic in un punto qualsiasi, eccetto il nome, nella riga dell'endpoint quindi fare clic su **DELETE ENDPOINT** nella parte inferiore della pagina. L'endpoint verrà rimosso dal servizio Web.
 
    > [AZURE.NOTE]Se in un'applicazione è in uso l'endpoint di servizio Web quando questo viene eliminato, al successivo tentativo di accesso al servizio da parte dell'applicazione verrà visualizzato un errore .

Fare clic sul nome dell'endpoint di servizio Web per aprirlo. Il grafico relativo all'utilizzo mostra le risorse di calcolo e di stima usate dall'endpoint di servizio Web. È possibile modificare la visualizzazione per mostrare i valori relativi o assoluti, nonché cambiare l'intervallo di tempo visualizzato nel grafico.

Questa pagina include anche le informazioni necessarie per accedere all'endpoint tramite l'API REST del servizio Web. Per altre informazioni, vedere [Come utilizzare un servizio Web di Azure Machine Learning][consume].

Da questa pagina è anche possibile pubblicare il servizio Web nel Marketplace dati di Azure. Per altre informazioni, vedere [Pubblicare il servizio Web di Azure Machine Learning in Azure Marketplace][marketplace].

[consume]: machine-learning-consume-web-services.md
[marketplace]: machine-learning-publish-web-service-to-azure-marketplace.md

 

<!---HONumber=August15_HO6-->