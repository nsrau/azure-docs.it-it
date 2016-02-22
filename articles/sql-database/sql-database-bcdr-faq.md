<properties 
   pageTitle="FAQ sulla continuità aziendale del database SQL" 
   description="Domande frequenti, con relative risposte, poste dai clienti sulle funzionalità incorporate e facoltative per la continuità aziendale e il ripristino di emergenza con il database SQL di Azure." 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="02/09/2016"
   ms.author="elfish"/>

# Domande frequenti sulla continuità aziendale

## 1\. Cosa accade al periodo di conservazione del punto di ripristino in caso di downgrade o aggiornamento in base al livello di servizio?
Dopo il downgrade a un livello di prestazioni inferiore, il periodo di conservazione del punto di ripristino viene limitato immediatamente al periodo di conservazione del livello di prestazioni del database corrente.

Se invece il livello di servizio del database viene aggiornato, il periodo di conservazione inizia a estendersi solo dopo l'aggiornamento del database.

Se ad esempio si esegue il downgrade del database da P1 a S3, il periodo di conservazione cambierà immediatamente da 35 a 14 giorni e tutti i punti di ripristino precedenti al nuovo periodo di 14 giorni non saranno più disponibili. Successivamente, se il database viene di nuovo aggiornato a P1, il periodo di conservazione inizierà da 14 giorni e comincerà ad estendersi fino a 35 giorni.

## 2\. Quanto dura il periodo di conservazione di un database rimosso? 
Il periodo di conservazione è determinato dal livello di servizio associato al database prima della rimozione o dal numero di giorni in cui il database esiste ancora (viene usato il valore più basso).

## 3\. Come si ripristina un server eliminato?

Non è attualmente disponibile alcun supporto per il ripristino di un server eliminato.

## 4\. Quanto tempo occorre per ripristinare un database?

Il tempo di ripristino di un database dipende da più fattori, ad esempio la dimensione del database, il numero di log delle transazioni, la larghezza di banda della rete e così via. Per la maggior parte dei database, il ripristino richiede al massimo 12 ore.

## 5\. È possibile cambiare il periodo di conservazione del punto di ripristino del database?

No.

## 6\. Come si individua il punto di ripristino disponibile per il database?

Per il ripristino da errori dell'utente, l'ora corrente è il punto di ripristino disponibile più recente. Per individuare quello meno recente, usare [Get Database](https://msdn.microsoft.com/library/dn505708.aspx) (*RecoveryPeriodStartDate*), che consente di ottenere il punto di ripristino più vecchio, senza replica geografica.

Per il ripristino da interruzione del servizio, usare [Get Recoverable Database](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*), che consente di ottenere l'ultimo punto di ripristino con replica geografica.

## 7\. Come si esegue il ripristino in blocco dei database nel server?

Non è disponibile una funzionalità incorporata per il ripristino in blocco. Lo script [Database SQL di Azure: Full Server Recovery](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) è un esempio di un modo per eseguire questa operazione.

## 8\. Qual è la differenza tra replica geografica standard e replica geografica attiva?

Per la replica geografica standard, il database secondario non è leggibile. È disponibile solo per il failover durante le interruzioni del servizio.

Per la replica geografica attiva, tutti i database secondari (fino a un massimo di quattro) sono leggibili.

## 9\. Cos'è il ritardo di replica quando si usa la replica geografica standard o quella attiva?

Usare la visualizzazione a gestione dinamica (DMV) [sys.dm\_geo\_replication\_link\_status](https://msdnstage.redmond.corp.microsoft.com/library/mt575504.aspx) per ottenere l'ultima ora di replica, l'ultimo intervallo di replica e altre informazioni sul collegamento di replica.

<!---HONumber=AcomDC_0211_2016-->