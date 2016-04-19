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
   ms.date="04/12/2016"
   ms.author="mausher;barbkess;sonyama;nicw"/>

# Aspettative di anteprima di SQL Data Warehouse

In questo articolo vengono descritte le funzionalità di anteprima SQL Data Warehouse e i nostri obiettivi per il servizio per la disponibilità generale (GA). Queste informazioni verranno aggiornate ogni qual volta che si presentano miglioramenti delle funzionalità di anteprima pubblica.

Obiettivi per SQL Data Warehouse:

- Prestazioni prevedibili e scalabilità lineare fino a petabyte di dati.
- Elevata affidabilità per tutte le operazioni Data Warehouse, supportate da un contratto di servizio (SLA).

Lavoreremo continuamente per raggiungere questi obiettivi prima di promuovere SQL Data Warehouse per la disponibilità generale.

## Prestazioni prevedibili e scalabili

L’SQL Data Warehouse di Azure introduce le unità Data Warehouse (DWUs) come modo per misurare le risorse di elaborazione (CPU, memoria, archiviazione, i/o) disponibile per il data warehouse. Se si aumenta il numero di DWU, si aumentano anche le risorse. Man mano che aumenta il numero di DWU, l’SQL Data Warehouse esegue operazioni in parallelo (ad esempio, caricamento dati o query) tra le risorse più distribuite. Questo riduce la latenza e migliora le prestazioni.

Qualsiasi data warehouse ha 2 metriche delle prestazioni fondamentali:

- Frequenza di caricamento. Il numero di record che possono essere caricati nel data warehouse al secondo. Misuriamo il numero di record che può essere importato tramite PolyBase dall'archiviazione Blob di Azure a una tabella con un indice columnstore cluster.
- Velocità di acquisizione. Il numero di record che possono essere recuperati in sequenza dal data warehouse al secondo. Misuriamo il numero di record restituiti da una query su un indice columnstore cluster.


Stiamo misurando alcuni miglioramenti importanti sulle prestazioni e presto condivideremo le frequenze previste. Durante il periodo di anteprima verranno apportati miglioramenti (ad esempio, aumento della compressione e della memorizzazione nella cache) per aumentare i tassi e per garantire una scalabilità prevedibile.


## Affidabilità elevata supportata da un contratto di servizio

### Protezione dati

SQL Data Warehouse archivia tutti i dati in Archiviazione di Azure tramite BLOB con ridondanza geografica. Nell'area di Azure locale vengono mantenute tre copie sincrone dei dati per garantire una protezione trasparente degli stessi in caso di problemi localizzati, quali malfunzionamenti delle unità di archiviazione. Inoltre, in un'area di Azure remota vengono mantenute tre copie asincrone allo scopo di proteggere i dati qualora si verificassero problemi nell'area (ripristino di emergenza). L'area locale e quella remota vengono abbinate per mantenere latenze di sincronizzazione accettabili, ad esempio Stati Uniti orientali e Stati Uniti occidentali.


### Backups

SQL Data Warehouse esegue il backup di tutti i dati almeno ogni 8 ore usando snapshot di Archiviazione di Azure. Tali snapshot vengono conservati per 7 giorni. È quindi possibile ripristinare i dati tornando indietro di almeno 21 momenti specifici degli ultimi 7 giorni e arrivando fino all'ora in cui è stato acquisito l'ultimo snapshot. È possibile ripristinare dati da uno snapshot utilizzando PowerShell o le API REST.

Gli snapshot vengono copiati in modo asincrono in un'area di Azure remota per assicurare una maggiore recuperabilità in caso di errori a livello dell'area stessa (ripristino di emergenza).


### Completamento della query

SQL Data Warehouse archivia i dati in uno o più nodi di calcolo che contengono alcuni dati utente e controllano l’esecuzione della query su tali dati. Come parte dell'architettura di elaborazione parallela massiva (MPP), le query vengono eseguite in parallelo tra i nodi di calcolo. SQL Data Warehouse rileva automaticamente e consente di ridurre gli errori del nodo di calcolo. Tuttavia, durante l'anteprima, un'operazione (ad esempio, il caricamento dei dati o query) può non riuscire a causa di errori del singolo nodo. Durante l'anteprima, stiamo predisponendo miglioramenti continui per completare con successo le operazioni nonostante gli errori di nodo.


## Passaggi successivi

[Introduzione][] ad anteprima pubblica.

<!--Image references-->

<!--Article references-->
[Introduzione]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0413_2016-->