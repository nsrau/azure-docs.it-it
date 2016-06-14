<properties
	pageTitle="Visualizzare e risolvere i problemi di Analisi di flusso | Microsoft Azure"
	description="Informazioni su come visualizzare una pipeline dei processi di Analisi di flusso per risolvere i problemi in modo autonomo mediante la funzionalità Diagramma diagnosi."
	keywords=""
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"
/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="05/31/2016"
	ms.author="jeffstok"
/>


# Visualizzare e risolvere i problemi di Analisi di flusso

In Analisi di flusso, come in altre tecnologie basate su cloud, è talvolta necessario eseguire operazioni di risoluzione dei problemi per comprendere il motivo per cui un processo non produce l'output previsto o non ne produce alcuno. Tenendo presente tale premessa, Stream Analytics offre la possibilità di visualizzare un processo di streaming. Questa funzionalità si rivela utile anche come strumento di modellazione e risulta vantaggiosa per gli utenti che hanno la necessità di documentare il proprio lavoro.

Nel pannello di visualizzazione sono visibili non solo gli input ma anche la query in fase di esecuzione e quindi tutti gli output configurati. È possibile visualizzare con maggiore evidenza i problemi di connettività o configurazione. Può inoltre essere utile avere una rappresentazione visiva della configurazione.

## Uso dello strumento Diagramma diagnosi

Per accedere a questo visualizzatore è sufficiente fare clic sul pulsante Diagramma diagnosi nel pannello Impostazioni del processo di Analisi di flusso.

![stream-analytics-troubleshoot-visualization-diagnosis-diagram](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-diagnosis-diagram.png)

Ogni input e output è contraddistinto dal colore per indicare lo stato corrente del componente, come mostrato di seguito.

![stream-analytics-troubleshoot-visualization-input-map](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-input-map.png)

Se l'utente desidera esaminare i passaggi intermedi della query per comprendere i modelli del flusso di dati all'interno di un processo, lo strumento di visualizzazione mostra la scomposizione della query nei passaggi che la compongono nonché la sequenza del flusso. Facendo clic su ciascun passaggio della query verrà visualizzata la sezione corrispondente in un apposito riquadro di modifica, come mostrato di seguito.

![stream-analytics-troubleshoot-visualization-intermediate-steps](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-intermediate-steps.png)




## Passaggi successivi

- [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
- [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
- [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
- [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Informazioni di riferimento sulle API REST di gestione di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!---HONumber=AcomDC_0601_2016-->