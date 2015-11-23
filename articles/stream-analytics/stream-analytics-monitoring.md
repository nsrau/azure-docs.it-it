<properties 
	pageTitle="Informazioni sul monitoraggio dei processi di Analisi di flusso | Microsoft Azure" 
	description="Informazioni sul monitoraggio dei processi di Analisi di flusso" 
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
	ms.date="11/10/2015" 
	ms.author="jeffstok"/>

# Informazioni sul monitoraggio dei processi di Analisi di flusso

## Pagina Monitoraggio

Il portale di gestione di Azure e il portale di anteprima di Azure evidenziano entrambi metriche delle prestazioni chiave che possono essere utilizzate per monitorare e risolvere i problemi del processo.

Nel portale di gestione di Azure, Fare clic sulla scheda **Monitoraggio** di un processo di Analisi di flusso per visualizzare queste metriche. Si verifica un ritardo di al massimo 1 minuto nella visualizzazione delle metriche delle prestazioni nella pagina Monitoraggio.

  ![Dashboard di monitoraggio](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)

Nel portale di anteprima di Azure esplorare il processo di analisi di flusso di cui si desidera vedere le metriche e visualizzare la sezione **Monitoraggio**.

  ![Dashboard di monitoraggio del portale di anteprima di Azure](./media/stream-analytics-monitoring/06-stream-analytics-monitoring.png)

La prima volta che viene creato un processo di analisi di flusso in un'area, è necessario configurare la diagnostica per tale area. A tale scopo, fare clic nella sezione **Monitoraggio** e verrà visualizzato il pannello **Diagnostica**. Qui è possibile abilitare la diagnostica e specificare un account di archiviazione per i dati di monitoraggio.

  ![Configurazione diagnostica del portale di anteprima di Azure](./media/stream-analytics-monitoring/07-stream-analytics-monitoring.png)

## Metriche disponibili per l'analisi di flusso  

| Metrica | Definizione |
|--------|-------------|
| % utilizzo unità di streaming | L'utilizzo delle unità di streaming assegnate a un processo dalla scheda Scalabilità del processo. Se questo indicatore raggiunge l'80% o più, esiste una forte probabilità che l'elaborazione di eventi possa essere ritardata o interrotta. |
| Eventi di input | Quantità di dati ricevuta dal processo di Analisi di flusso, in termini di conteggio degli eventi. Può essere usata per convalidare l'invio degli eventi all'origine di input. |
| Byte evento di input | Quantità di dati ricevuta dal processo di Analisi di flusso, in termini di velocità effettiva in byte |
| Eventi di output | Quantità di dati inviata dal processo di Analisi di flusso alla destinazione di output, in termini di conteggio degli eventi. |
| Eventi non ordinati | Numero di eventi non ordinati ricevuti che sono stati eliminati o a cui è stato assegnato un timestamp modificato, in base ai Criteri di ordinamento eventi. Può essere influenzato dalla configurazione dell'impostazione della Finestra di tolleranza elementi non in ordine. |
| Errori di conversione dati | numero di errori di conversione dei dati rilevati da un processo di Analisi di flusso. |
| Ultimi eventi di input | Numero di eventi che arrivano in ritardo dall'origine che sono stati eliminati oppure il cui timestamp è stato modificato, in base alla configurazione dei Criteri di ordinamento eventi dell'impostazione della Finestra di tolleranza elementi non in ordine. |
## Personalizzazione del monitoraggio nel portale di gestione di Azure ##

In un grafico è possibile visualizzare fino a 6 metriche.

Per passare dalla visualizzazione di valori relativi (solo il valore finale per ogni metrica) e valori assoluti (asse Y visualizzato), selezionare Relative o Absolute nella parte superiore del grafico.

  ![Assoluto relativo](./media/stream-analytics-monitoring/02-stream-analytics-monitoring.png)

È possibile visualizzare le metriche nel grafico Monitoraggio in aggregazioni di 1 ora, 12 ore, 24 ore o 7 giorni.

Per modificare l'intervallo di tempo visualizzato sul grafico delle metriche, selezionare 1 ora, 24 ore o 7 giorni nella parte superiore del grafico.

  ![Scala temporale](./media/stream-analytics-monitoring/03-stream-analytics-monitoring.png)

È possibile impostare regole che possono inviare una notifica per posta elettronica nel caso in cui il processo supera una soglia definita.

## Personalizzazione del monitoraggio nel portale di anteprima di Azure ##

È possibile modificare il tipo di grafico, le metriche visualizzate e l’intervallo di tempo nelle impostazioni di Modifica grafico. Per altre informazioni, vedere [Come personalizzare il monitoraggio](./azure-portal/insights-how-to-customize-monitoring.md).

  ![Scala temporale del portale di anteprima di Azure](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)

## Stato processo

Lo stato dei processi di Analisi di flusso può essere esaminato nel portale di Azure quando si visualizza un elenco di processi. Per visualizzare l'elenco dei processi, fare clic sull'icona Analisi di flusso nel Portale di Azure.

| Stato | Definizione |
|--------|------------|
| Data di creazione | Un processo è stato creato, tuttavia non è stato avviato. |
| Starting | Un utente ha fatto clic su Start the job e il processo viene avviato |
| Running | il processo è stato allocato, è in corso l'elaborazione dell'input oppure è in attesa dell'elaborazione dell'input. Se lo stato di un processo è In esecuzione ma non viene generato alcun output, è probabile che la finestra temporale di elaborazione dati sia ampia o che la logica di query sia complessa. È anche possibile che al momento non siano stati inviati dati al processo. |
| Arresto in corso | Un utente ha fatto clic su Stop the job e il processo viene arrestato. |
| Stopped | Il processo è stato arrestato. |
| Degraded | Questo stato indica che durante un processo di analisi di flusso si sono verificati errori temporanei (per es. errori di input/output, errori di elaborazione, errori di conversione ecc.). Il processo è ancora in esecuzione, tuttavia vengono generati molti errori. Questo processo richiede l'attenzione cliente e il cliente potrà visualizzare gli errori nei log delle operazioni. |
| Failed | Indica che il processo non è riuscito a causa di errori e che l'elaborazione è stata arrestata. Il cliente deve esaminare i log delle operazioni per eseguire il debug degli errori. |
| Deleting | Indica che è in corso l'eliminazione del processo. |

## Diagnosi

Nel portale di gestione di Azure, il dashboard di processo contiene informazioni su dove è necessario cercare la diagnosi, vale a dire gli input, gli output e/o il log delle operazioni. È possibile fare clic sul collegamento per passare alla posizione appropriata per esaminare la diagnosi.

  ![Errore](./media/stream-analytics-monitoring/04-stream-analytics-monitoring.png)

Facendo clic sulla risorsa di input o output si ottengono informazioni di diagnostica dettagliate. Queste vengono aggiornate con le ultime informazioni di diagnostica durante l'esecuzione del processo.

  ![Diagnostica](./media/stream-analytics-monitoring/05-stream-analytics-monitoring.png)

## Ottenere aiuto
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/it-IT/home?forum=AzureStreamAnalytics)

## Passaggi successivi

- [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
- [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
- [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
- [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=Nov15_HO3-->