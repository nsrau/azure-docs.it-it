<properties
	pageTitle="Utilizzare un servizio Web di Machine Learning da Excel | Microsoft Azure"
	description="Utilizzo di un servizio Web Azure Machine Learning da Excel | Azure"
	services="machine-learning"
	documentationCenter=""
	authors="LuisCabrer"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/07/2015"
	ms.author="tedway"/>


# Utilizzo di un servizio Web Azure Machine Learning da Excel

 Azure Machine Learning Studio semplifica la chiamata dei servizi Web direttamente da Excel senza dover di scrivere codice.

 Se si esegue Excel 2013 (o versione successiva) oppure è possibile salvare un file in SharePoint o OneDrive per l'utilizzo con Excel Online, si consiglia il [componente aggiuntivo Excel](machine-learning-excel-add-in-for-web-services.md).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Passi

1. Pubblicazione di un servizio Web. [Questa pagina](machine-learning-walkthrough-5-publish-web-service.md) spiega come eseguire tale operazione. Attualmente la funzionalità Cartella di lavoro di Excel è supportata solo per i servizi di richiesta/risposta con un unico output cioè, una singola etichetta di valutazione.

2. Dopo aver creato un servizio Web, fare clic sulla sezione **WEB SERVICES** sulla sinistra di Studio e quindi selezionare il servizio Web da utilizzare tramite Excel.

3. Nella scheda **DASHBOARD** per il servizio Web è presente una riga per il servizio **REQUEST/RESPONSE**. Se il servizio ha un singolo output, è necessario vedere il collegamento **Download Excel Workbook** in quella riga.

	![][1]

4. Fare clic su **Download Excel Workbook** e aprire la cartella di lavoro in Excel.

5. Viene visualizzato un avviso di sicurezza. Fare clic sul pulsante **Abilita modifica**.

	![][2]

6. Viene visualizzato un avviso di sicurezza. Fare clic sul pulsante **Abilita contenuto** per eseguire le macro nel foglio di calcolo.

	![][3]

7. Una volta attivate le macro, viene generata una tabella. Le colonne in blu sono necessarie come input nel servizio Web di richiesta-risposta (RRS) o **PARAMETRI**. Si noti che l'output del servizio RRS, **PREDICTED VALUES** è verde. Quando vengono riempite tutte le colonne per una determinata riga, la cartella di lavoro può automaticamente chiamare l'API di valutazione e visualizzare i risultati corrispondenti.

	![][4]

7. Per valutare più di una riga, compilare la seconda riga con i dati, così verranno elaborati i valori stimati. È anche possibile incollare più righe contemporaneamente.

8. A questo punto è possibile utilizzare tutte le funzionalità di Excel (grafici, Power Map, formattazione condizionale e così via) con i valori stimati.


## Condivisione della cartella di lavoro

Affinché le macro funzionino correttamente, la CHIAVE DI ACCESSO deve far parte del foglio di calcolo. Ciò significa che è necessario condividere la cartella di lavoro solo con le entità o i singoli utenti considerati attendibili.

## Aggiornamenti automatici

Una chiamata RRS viene effettuata nei due casi seguenti:

1. La prima volta che una riga include contenuto in tutti i propri **PARAMETRI**.

2. Ogni volta che uno dei **PARAMETRI** viene modificato in una riga con tutti i **PARAMETRI** immessi.

[1]: ./media/machine-learning-consuming-from-excel/excellink.png
[2]: ./media/machine-learning-consuming-from-excel/enableeditting.png
[3]: ./media/machine-learning-consuming-from-excel/enablecontent.png
[4]: ./media/machine-learning-consuming-from-excel/sampletable.png

<!---HONumber=Sept15_HO2-->