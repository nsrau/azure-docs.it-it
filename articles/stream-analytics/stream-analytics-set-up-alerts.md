<properties 
	pageTitle="Avvisi di Analisi di flusso | Microsoft Azure" 
	description="Informazioni sugli avvisi di Analisi di flusso" 
	keywords="analisi di big data, servizio cloud, internet delle cose, servizio gestito, elaborazione del flusso, analisi di flusso, dati del flusso"
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="11/06/2015" 
	ms.author="jeffstok"/>


# Configurazione degli avvisi

## Pagina Monitoraggio

È possibile configurare una regola per attivare un avviso quando una metrica raggiunge una condizione specificata dall'utente.

Ad esempio, “se gli eventi di Output per gli ultimi 15 minuti è <100, inviare una notifica tramite posta elettronica all’ID di posta elettronica: xyz@company.com”.

Le regole possono essere configurate sulle metriche tramite il portale o possono essere configurate [a livello di codice](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) sui dati dei log delle operazioni.

## Configurazione degli avvisi tramite il portale di Azure

Esistono due modi per configurare gli avvisi nel portale di gestione di Azure:

1.	Scheda **Monitoraggio** del processo dell’analisi di flusso  
2.	Il Log delle operazioni in servizi di gestione  

## Avvisi tramite la scheda monitoraggio del processo nel portale

1.	Selezionare la metrica nella scheda di monitoraggio, fare clic sul pulsante **Aggiungi regola** nella parte inferiore del dashboard e configurare le regole desiderate.  

    ![Dashboard](./media/stream-analytics-set-up-alerts/01-stream-analytics-set-up-alerts.png)

2.	Definire il nome e la descrizione dell'avviso

    ![Creare una regola](./media/stream-analytics-set-up-alerts/02-stream-analytics-set-up-alerts.png)

3.	Immettere soglie, finestra valutazione avviso e azioni per l'avviso

    ![Definire le condizioni](./media/stream-analytics-set-up-alerts/03-stream-analytics-set-up-alerts.png)

## Configurazione degli avvisi tramite i log delle operazioni

1.	Visitare la scheda **Avvisi** nei servizi di gestione nel [Portale di Azure](https://manage.windowsazure.com).  
2.	Fare clic su **Aggiungi regola**  

    ![Criteri](./media/stream-analytics-set-up-alerts/04-stream-analytics-set-up-alerts.png)

3.	Definire il nome e la descrizione dell'avviso. Selezionare 'Analisi di flusso' come Tipo di servizio e il nome del processo come Nome del servizio.

    ![Definire l’avviso](./media/stream-analytics-set-up-alerts/05-stream-analytics-set-up-alerts.png)

## Impostazione degli avvisi nel portale di anteprima di Azure ##

Nel portale di anteprima di Azure individuare il processo di analisi di flusso per cui si desidera essere avvisati e fare clic sulla sezione **Monitoraggio**. Nel pannello **Metrica** fare clic sul comando **Aggiungi avviso**.

  ![Configurazione del portale di anteprima di Azure](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)

È possibile denominare la regola di avviso e scegliere una descrizione che verrà visualizzata nella notifica inviata tramite posta elettronica.

Quando si seleziona Metriche, è possibile scegliere una condizione e un valore soglia per la metrica.

  ![Metrica di selezione del portale di anteprima di Azure](./media/stream-analytics-set-up-alerts/07-stream-analytics-set-up-alerts.png)

Per informazioni dettagliate sulla configurazione degli avvisi nel portale di anteprima di Azure, vedere [Ricevere notifiche di avviso](./azure-portal/insights-receive-alert-notifications.md).

## Ottenere aiuto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/it-IT/home?forum=AzureStreamAnalytics)

## Passaggi successivi

- [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
- [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
- [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
- [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Nov15_HO3-->