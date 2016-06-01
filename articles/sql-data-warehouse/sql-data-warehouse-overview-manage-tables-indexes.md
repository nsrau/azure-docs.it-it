<properties
   pageTitle="Gestire le tabelle e gli indici in Azure SQL Data Warehouse | Microsoft Azure"
   description="Panoramica delle considerazioni, procedure consigliate e attività per la gestione delle tabelle e degli indici in Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/02/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Gestire le tabelle e gli indici in Azure SQL Data Warehouse

Panoramica delle considerazioni, delle procedure consigliate e delle attività per la gestione delle tabelle e degli indici in SQL Data Warehouse.



| Categoria | Attività o considerazione | Descrizione |
| :-----------------------| :---------------------------------------------- | :----------- |
| Indici Columnstore | Ricompilare gli indici dopo il caricamento o l'inserimento dei dati | Per impostazione predefinita, SQL Data Warehouse archivia ogni tabella come un indice columnstore cluster. In questo modo si ottengono prestazioni migliori e la compressione dei dati, in particolare per tabelle di grandi dimensioni. In base a come si inseriscono i dati nell'indice columnstore, è possibile che non tutti i dati vengano archiviati con la compressione columnstore. In questo caso, è possibile che non si ottengano le prestazioni massime degli indici columnstore. <br/><br/>Per assicurarsi che lo stato degli indici columnstore sia integro, vedere [Gestire gli indici columnstore][]. |
| Tabelle con distribuzione hash | Verificare che i dati vengano distribuiti in modo uniforme tra i nodi | Le tabelle con distribuzione hash sono quasi sempre la soluzione migliore per ottimizzare i join e le aggregazioni nelle tabelle di grandi dimensioni. SQL Data Warehouse ha distribuito le tabelle in base a una colonna di distribuzione specificata. Alcune colonne rappresentano chiavi di distribuzione valide e altre no. Si desidera distribuire in modo uniforme le righe. Una disuguaglianza minima, o differenza dei dati, è accettabile, ma se la differenza dei dati è eccessiva, si perdono i vantaggi dell'elaborazione parallela massiva (MPP) che SQL Data Warehouse è progettato per fornire.<br/><br/>Per assicurarsi che le tabelle con distribuzione hash non abbiano un'eccessiva differenza dei dati, vedere [Gestire la differenza dati per le tabelle distribuite][]. |





## Passaggi successivi

Per altri suggerimenti relativi alla gestione, andare alla [Panoramica della gestione][].

<!--Image references-->

<!--Article references-->
[Gestire gli indici columnstore]: sql-data-warehouse-manage-columnstore-indexes.md
[Gestire la differenza dati per le tabelle distribuite]: sql-data-warehouse-manage-distributed-data-skew.md
[Panoramica della gestione]: sql-data-warehouse-overview-manage.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0518_2016-->