<properties 
   pageTitle="Tabella dei limiti di Analisi di flusso"
   description="Descrive i limiti di sistema e le dimensioni consigliate per i componenti e le connessioni di Analisi di flusso."
   services="stream-analytics"
   documentationCenter="NA"
   authors="jeffstokes72"
   manager="paulettm"
   editor="cgronlun" />
<tags 
   ms.service="stream-analytics"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="big-data"
   ms.date="07/25/2016"
   ms.author="jeffstok" />

| Identificatore limite | Limite | Commenti |
|----------------- | ------------|--------- |
| Numero massimo di unità di streaming per sottoscrizione per area | 50 | Per richiedere un aumento delle unità di streaming per la sottoscrizione a oltre 50 è possibile contattare il [supporto Microsoft](https://support.microsoft.com/it-IT). |
| Velocità effettiva massima di un'unità di streaming | 1 MB/s* | La velocità effettiva massima per unità di streaming dipende dallo scenario. La velocità effettiva potrebbe essere inferiore e dipende dalla complessità della query e dal partizionamento. Ulteriori dettagli sono disponibili nell’articolo [Scalabilità dei processi di Analisi di flusso di Azure per aumentare la velocità effettiva](../articles/stream-analytics/stream-analytics-scale-jobs.md). |
| Numero massimo di input per processo | 60 | Esiste un limite rigido di 60 input per il processo di Analisi di flusso. |
| Numero massimo di output per processo | 60 | Esiste un limite rigido di 60 output per il processo di Analisi di flusso. |
| Numero massimo di funzioni per processo | 60 | Esiste un limite rigido di 60 funzioni per il processo di Analisi di flusso. |
| Numero massimo di processi per area | 1500 | Per ogni sottoscrizione può esserci un massimo di 1.500 processi per area geografica. |

<!---HONumber=AcomDC_0727_2016-->