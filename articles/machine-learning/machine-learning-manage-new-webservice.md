<properties
	pageTitle="Gestire un nuovo servizio Web di Machine Learning | Microsoft Azure"
	description="Gestione dell'accesso alle aree di lavoro di Azure Machine Learning e distribuzione e gestione dei servizi Web API ML"
	services="machine-learning"
	documentationCenter=""
	authors="vDonGlover"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="v-donglo"/>


# Gestire un nuovo servizio Web di Machine Learning

> [AZURE.NOTE] Le procedure descritte in questo articolo sono relative ai nuovi servizi Web di Azure Machine Learning. Per informazioni sulla gestione dei servizi Web classici, vedere [Gestire un'area di lavoro di Azure Machine Learning](machine-learning-manage-workspace.md).

Tramite il portale di servizi Web di Microsoft Azure Machine Learning è possibile gestire i nuovi servizi Web di Machine Learning. È possibile:

- Monitorare la modalità d'uso del servizio Web.
- Configurare la descrizione, aggiornare le chiavi per il servizio Web, aggiornare la chiave dell'account di archiviazione e abilitare o disabilitare i dati di esempio.
- Eliminare il servizio Web.
- Creare, eliminare o aggiornare i piani di fatturazione.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

> [AZURE.TIP] Nella scheda **WEB SERVICES** di Azure Machine Learning Studio è possibile aggiungere, aggiornare o eliminare un servizio Web di Machine Learning.

Per gestire i servizi Web:

1.	Accedere al [portale dei servizi Web di Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) usando l'account di Microsoft Azure associato alla sottoscrizione di Azure.
2.	Nel menu fare clic su **Servizi Web**.

Verrà visualizzato un elenco di servizi Web distribuiti per la sottoscrizione. Per gestire un servizio Web, fare clic sul nome nell'elenco per aprire la pagina corrispondente.

Nella pagina Servizi Web è possibile:

- Fare clic sul servizio Web per gestirlo.
- Fare clic sul piano di fatturazione per il servizio Web per aggiornarlo.
- Eliminare un servizio Web.
- Copiare un servizio Web in un'altra area.
- Copiare un servizio Web e distribuirlo in un'altra area.

Quando si fa clic su un servizio Web, verrà visualizzata la pagina Avvio rapido del servizio Web. Questa pagina contiene due opzioni di menu che consentono di gestire il servizio Web:

- **DASHBOARD**: consente di visualizzare l'uso del servizio Web.
- **CONFIGURA**: consente di aggiungere testo descrittivo, attivare e disattivare la registrazione degli errori, aggiornare la chiave dell'account di archiviazione associato al servizio Web e abilitare e disabilitare i dati di esempio.

## Monitoraggio della modalità d'uso del servizio Web

Fare clic sulla scheda **DASHBOARD**.

Nel dashboard è possibile visualizzare l'uso complessivo del servizio Web in un periodo di tempo. È possibile selezionare il periodo da visualizzare dal menu a discesa Periodo in alto a destra dei grafici di utilizzo. Il dashboard visualizza le informazioni seguenti:

- **Requests Over Time** (Richieste nel tempo) visualizza un grafico con il numero di richieste nel periodo di tempo selezionato. Può aiutare a identificare se si verificano picchi di utilizzo.
- **Request-Response Requests** (Richieste richiesta-risposta) visualizza il numero totale di chiamate di richiesta-risposta ricevute dal servizio nel periodo di tempo selezionato e il numero di richieste con errore.
- **Average Request-Response Compute Time** (Tempo medio di calcolo richiesta-risposta) visualizza una media del tempo necessario per eseguire le richieste ricevute.
- **Batch Requests** (Richieste batch) visualizza il numero totale di richieste batch ricevute dal servizio nel periodo di tempo selezionato e il numero di richieste con errore.
- **Average Job Latency** (Latenza processo media) visualizza una media del tempo necessario per eseguire le richieste ricevute.
- **Errors** (Errori) visualizza il numero complessivo di errori riscontrati nelle chiamate al servizio Web.
- **Services Costs** (Costi servizi) visualizza le spese per il piano di fatturazione associato al servizio.

## Configurazione del servizio Web ##

Fare clic sull'opzione di menu **CONFIGURA**.

È possibile aggiornare le proprietà seguenti:

* **Descrizione** consente di immettere una descrizione per il servizio Web.
* **Titolo** consente di immettere un titolo per il servizio Web
* **Chiavi** consente di ruotare le chiavi API primarie e secondarie.
* **Chiave account di archiviazione** consente di aggiornare la chiave per l'account di archiviazione associato alle modifiche del servizio Web.
* **Abilita dati di esempio ** consente di fornire dati di esempio che è possibile usare per testare il servizio di richiesta-risposta. Se il servizio Web è stato creato in Machine Learning Studio, i dati di esempio vengono prelevati dai dati usati per il training del modello. Se il servizio è stato creato a livello di programmazione, i dati vengono prelevati dai dati di esempio forniti come parte del pacchetto JSON.

## Gestione dei piani di fatturazione

Fare clic sull'opzione di menu **Piani** della pagina Avvio rapido dei servizi Web. È inoltre possibile scegliere il piano associato al servizio Web specifico per gestire tale piano.

* **Nuovo** consente di creare un nuovo piano.
* **Add/Remove Plan instance** (Aggiungi/Rimuovi istanza di piano) consente di aumentare un piano esistente per aggiungere capacità.
* **Upgrade/DownGrade** (Esegui aggiornamento/downgrade) consente di aumentare un piano esistente per aggiungere capacità.
* **Elimina** consente di eliminare un piano.

Fare clic su un piano per visualizzare il relativo dashboard. Il dashboard indica l'uso dello snapshot o del piano in un periodo di tempo selezionato. Fare clic sull'elenco a discesa **Periodo** nella parte superiore destra delle informazioni del dashboard per selezionare il periodo di tempo.

Il dashboard del piano contiene le informazioni seguenti:

* **Descrizione piano** visualizza le informazioni sui costi e le capacità associate al piano.
* **Plan Usage** (Uso piano) visualizza il numero di transazioni e di ore di calcolo addebitate in base al piano.
* **Servizi Web** visualizza il numero di servizi Web che usano questo piano.
* **Top Web Service By Calls** (Primi servizi Web per chiamate) visualizza i primi quattro servizi Web che stanno effettuando chiamate addebitate in base al piano.
* **Top Web Services by Compute Hrs** (Primi servizi Web per ore di calcolo) visualizza i primi quattro servizi Web che stanno usando le risorse di calcolo che vengono addebitate in base al piano.

<!---HONumber=AcomDC_0720_2016-->