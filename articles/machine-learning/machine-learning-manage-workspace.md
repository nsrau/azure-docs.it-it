---
title: Gestire un'area di lavoro di Machine Learning | Microsoft Docs
description: Gestione dell'accesso alle aree di lavoro di Azure Machine Learning e distribuzione e gestione dei servizi Web API ML
services: machine-learning
documentationcenter: ''
author: garyericson
manager: jhubbard
editor: cgronlun

ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: garye

---
# <a name="manage-an-azure-machine-learning-workspace"></a>Gestire un'area di lavoro di Azure Machine Learning
> [!NOTE]
> Le procedure descritte in questo articolo sono relative ai servizi Web classici di Azure Machine Learning. Per informazioni sulla gestione dei servizi Web nel portale dei servizi Web di Machine Learning, vedere [Gestire un servizio Web usando il portale dei servizi Web di Azure Machine Learning](machine-learning-manage-new-webservice.md).
> 
> 

Tramite il portale di Azure classico è possibile gestire le aree di lavoro di Machine Learning per:

* Monitorare la modalità d'uso dell'area di lavoro.
* Configurare l'area di lavoro per consentire o negare l'accesso.
* Gestire i servizi Web creati nell'area di lavoro
* Eliminare l'area di lavoro.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Nella scheda Dashboard viene inoltre fornita una panoramica dell'utilizzo dell'area di lavoro e un riepilogo rapido delle informazioni relative all'area di lavoro.  

> [!TIP]
> Nella scheda **WEB SERVICES** (SERVIZI WEB) di Azure Machine Learning Studio è possibile aggiungere, aggiornare o eliminare un servizio Web di Machine Learning.
> 
> 

Per gestire un'area di lavoro:

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com/) usando l'account di Microsoft Azure associato alla sottoscrizione di Azure.
2. Nel riquadro dei servizi di Microsoft Azure fare clic su **MACHINE LEARNING**.
3. Fare clic sull'area di lavoro da gestire.

La pagina dell'area di lavoro contiene tre schede:

* **DASHBOARD** : permette di visualizzare i dati di utilizzo e le informazioni sull'area di lavoro.
* **CONFIGURE** : permette di gestire l'accesso all'area di lavoro.
* **WEB SERVICES** (SERVIZI WEB): permette di gestire i servizi Web pubblicati da questa area di lavoro.

## <a name="to-monitor-how-the-workspace-is-being-used"></a>Per monitorare la modalità d'uso dell'area di lavoro
Fare clic sulla scheda **DASHBOARD** .

Dal dashboard è possibile visualizzare l'utilizzo complessivo dell'area di lavoro e ottenere un riepilogo rapido delle informazioni dell'area di lavoro.

* Il grafico **COMPUTE** mostra le risorse di calcolo in uso nell'area di lavoro. È possibile modificare la visualizzazione per mostrare i valori relativi o assoluti, nonché cambiare l'intervallo di tempo visualizzato nel grafico.
* **Usage overview** mostra l'archiviazione di Azure attualmente in uso nell'area di lavoro.
* **Quick glance** visualizza un riepilogo delle informazioni dell'area di lavoro e collegamenti utili.

> [!NOTE]
> Il link all’ **Accesso a ML Studio** consente di aprire Machine Learning Studio usando l'account Microsoft con cui è stato eseguito l'accesso. L'account Microsoft usato per accedere al portale di Azure classico per creare un'area di lavoro non è automaticamente autorizzato ad aprire tale area di lavoro. Per aprire un'area di lavoro, è necessario essere connessi con l'account Microsoft definito come proprietario dell'area di lavoro oppure ricevere un invito dal proprietario per partecipare all'area di lavoro.
> 
> 

## <a name="to-grant-or-suspend-access-for-users"></a>Per concedere o sospendere l'accesso agli utenti
Fare clic sulla scheda **CONFIGURE** .

Dalla scheda di configurazione è possibile:

* Sospendere l'accesso all'area di lavoro di Machine Learning facendo clic su DENY. Gli utenti non saranno più in grado di aprire l'area di lavoro in Machine Learning Studio. Per ripristinare l'accesso, fare clic su ALLOW.

Per gestire account aggiuntivi che hanno accesso all'area di lavoro in Machine Learning Studio, fare clic su **Accedi a ML Studio** nella scheda **DASHBOARD** (vedere la nota precedente relativa al comando **Accedi a ML Studio**). Verrà aperta l'area di lavoro in Machine Learning Studio. A questo punto, fare clic sulla scheda **SETTINGS** (IMPOSTAZIONI) e quindi su **USERS** (UTENTI). È possibile fare clic su **INVITE MORE USERS** (INVITA ALTRI UTENTI) per concedere agli utenti l'accesso all'area di lavoro oppure selezionare un utente e fare clic su **REMOVE** (RIMUOVI).

## <a name="to-manage-web-services-in-this-workspace"></a>Per gestire i servizi Web in questa area di lavoro
Fare clic sulla scheda **WEB SERVICES** .

Verrà visualizzato un elenco di servizi Web pubblicati da questa area di lavoro.
Per gestire un servizio Web, fare clic sul nome nell'elenco per aprire la pagina corrispondente.

Per un servizio Web possono essere definiti uno o più endpoint.

* È possibile definire altri endpoint oltre a quelli di "Default". Per aggiungere l'endpoint, fare clic su **Manage Endpoints** (Gestisci endpoint) nella parte inferiore del dashboard per aprire il portale dei servizi Web di Azure Machine Learning.
* Per eliminare un endpoint, escluso l'endpoint "Default" che non può essere eliminato, fare clic nella casella di controllo all'inizio della riga dell'endpoint e quindi su **DELETE** (ELIMINA). L'endpoint verrà rimosso dal servizio Web.
  
  > [!NOTE]
  > Se in un'applicazione è in uso l'endpoint di servizio Web quando questo viene eliminato, al successivo tentativo di accesso al servizio da parte dell'applicazione verrà visualizzato un errore .
  > 
  > 

Fare clic sul nome di un endpoint di servizio Web per aprirlo. 

Nel dashboard è possibile visualizzare l'utilizzo complessivo del servizio Web in un periodo di tempo. È possibile selezionare il periodo da visualizzare dal menu a discesa Periodo in alto a destra dei grafici di utilizzo. Il dashboard visualizza le informazioni seguenti:

* **Requests Over Time** (Richieste nel tempo) visualizza un grafico con il numero di richieste nel periodo di tempo selezionato. Può aiutare a identificare se si verificano picchi di utilizzo.
* **Request-Response Requests** (Richieste richiesta-risposta) visualizza il numero totale di chiamate di richiesta-risposta ricevute dal servizio nel periodo di tempo selezionato e il numero di richieste con errore.
* **Average Request-Response Compute Time** (Tempo medio di calcolo richiesta-risposta) visualizza una media del tempo necessario per eseguire le richieste ricevute.
* **Batch Requests** (Richieste batch) visualizza il numero totale di richieste batch ricevute dal servizio nel periodo di tempo selezionato e il numero di richieste con errore.
* **Average Job Latency** (Latenza processo media) visualizza una media del tempo necessario per eseguire le richieste ricevute.
* **Errors** (Errori) visualizza il numero complessivo di errori che si sono verificati nelle chiamate al servizio Web.
* **Services Costs** (Costi servizi) visualizza le spese per il piano di fatturazione associato al servizio.

Dalla pagina di configurazione è possibile aggiornare le proprietà seguenti:

* **Description** (Descrizione) consente di immettere una descrizione per il servizio Web. La descrizione è un campo obbligatorio.
* **Logging** (Registrazione) consente di abilitare o disabilitare la registrazione nell'endpoint. Per altre informazioni sulla registrazione, vedere [Abilitare la registrazione per i servizi Web di Machine Learning](machine-learning-web-services-logging.md).
* **Enable Sample data** (Abilita dati di esempio) consente di fornire dati di esempio che è possibile usare per testare il servizio di richiesta-risposta. Se il servizio Web è stato creato in Machine Learning Studio, i dati di esempio vengono prelevati dai dati usati per il training del modello. Se il servizio è stato creato a livello di codice, i dati vengono ricavati dai dati di esempio forniti come parte del pacchetto JSON.

[utilizzare]: machine-learning-consume-web-services.md
[marketplace]: machine-learning-publish-web-service-to-azure-marketplace.md



<!--HONumber=Oct16_HO2-->


