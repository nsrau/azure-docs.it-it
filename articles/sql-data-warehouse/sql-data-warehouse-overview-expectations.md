<properties
   pageTitle="Aspettative di anteprima di SQL Data Warehouse | Microsoft Azure"
   description="Riepilogo delle funzionalità di anteprima pubblica e degli obiettivi di disponibilità generale di SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="happynicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/05/2016"
   ms.author="nicw;barbkess;sonyama"/>


# Aspettative di anteprima di SQL Data Warehouse

In questo articolo sono descritte le funzionalità di SQL Data Warehouse in anteprima e gli obiettivi del servizio per la disponibilità a livello generale. Queste informazioni verranno aggiornate ogni qual volta che si presentano miglioramenti delle funzionalità di anteprima pubblica.

Obiettivi per SQL Data Warehouse:

- Prestazioni prevedibili e scalabilità lineare fino a petabyte di dati
- Affidabilità elevata per tutte le operazioni di data warehouse
- Tempi minimi dal caricamento dei dati alle analisi sui dati per i dati relazionali e non relazionali

Microsoft è impegnata a raggiungere questi obiettivi durante l'anteprima di SQL Data Warehouse.

## Prestazioni prevedibili e scalabili

L’SQL Data Warehouse di Azure introduce le unità Data Warehouse (DWUs) come modo per misurare le risorse di elaborazione (CPU, memoria, archiviazione, i/o) disponibile per il data warehouse. Se si aumenta il numero di DWU, si aumentano anche le risorse. Man mano che aumenta il numero di DWU, l’SQL Data Warehouse esegue operazioni in parallelo (ad esempio, caricamento dati o query) tra le risorse più distribuite. Questo riduce la latenza e migliora le prestazioni.

Qualsiasi data warehouse ha 2 metriche delle prestazioni fondamentali:

- Frequenza di caricamento. Il numero di record che possono essere caricati nel data warehouse al secondo. Misuriamo il numero di record che può essere importato tramite PolyBase dall'archiviazione Blob di Azure a una tabella con un indice columnstore cluster.
- Velocità di acquisizione. Il numero di record che possono essere recuperati in sequenza dal data warehouse al secondo. Misuriamo il numero di record restituiti da una query su un indice columnstore cluster.

Stiamo misurando alcuni miglioramenti importanti sulle prestazioni e presto condivideremo le frequenze previste. Durante il periodo di anteprima verranno apportati miglioramenti (ad esempio, aumento della compressione e della memorizzazione nella cache) per aumentare i tassi e per garantire una scalabilità prevedibile.

## Protezione dati

SQL Data Warehouse archivia tutti i dati in Archiviazione di Azure tramite risorse di archiviazione con ridondanza locale. Nel data center locale vengono mantenute più copie sincrone dei dati per garantire una protezione trasparente degli stessi in caso di problemi localizzati.

## Backups

SQL Data Warehouse esegue il backup di tutti i dati almeno ogni 8 ore usando snapshot di Archiviazione di Azure. Tali snapshot vengono conservati per 7 giorni. È quindi possibile ripristinare i dati tornando indietro di almeno 21 momenti specifici degli ultimi 7 giorni e arrivando fino all'ora in cui è stato acquisito l'ultimo snapshot. È possibile ripristinare dati da uno snapshot utilizzando PowerShell o le API REST.

## Affidabilità delle query

SQL Data Warehouse si basa su un'architettura MPP (Massively Parallel Processing). SQL Data Warehouse rileva automaticamente e consente di ridurre gli errori dei nodi di calcolo e controllo. Tuttavia, un'operazione come ad esempio il caricamento di dati o una query potrebbe non riuscire a causa di un errore del nodo o della migrazione. Durante l'anteprima, stiamo predisponendo miglioramenti continui per completare con successo le operazioni nonostante gli errori di nodo.


## Aggiornamenti e tempo di inattività

SQL Data Warehouse verrà aggiornato periodicamente per aggiungere nuove funzionalità e installare aggiornamenti critici. Questi aggiornamenti possono comportare interruzioni del servizio e per il momento la pianificazione degli aggiornamenti non è prevedibile. Se questo processo causa troppe interruzioni del servizio, è consigliabile [creare un ticket di supporto][] per ricevere informazioni su una soluzione alternativa a questo processo.


## Passaggi successivi

[Introduzione][] ad anteprima pubblica.

<!--Image references-->

<!--Article references-->
[creare un ticket di supporto]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Introduzione]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0608_2016-->