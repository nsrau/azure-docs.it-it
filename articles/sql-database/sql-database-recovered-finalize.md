<properties
   pageTitle="Finalizzare il database SQL di Azure ripristinato"
   description="Ripristino temporizzato, Database SQL di Microsoft Azure, ripristino del database, recupero del database, portale di Azure classico, portale di Azure classico"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="storage-backup-recovery"
   ms.date="02/09/2016"
   ms.author="carlrab"/>

# Finalizzare il database SQL di Azure ripristinato

## Panoramica

In questo articolo viene fornito un elenco di controllo delle attività che è necessario effettuare prima di riportare un database SQL di Azure appena ripristinato in produzione. L'elenco si applica a database ripristinati da failover di replica geografica, a database eliminati e ripristinati, a database ripristinati in modalità temporizzata o a livello geografico.

## Aggiornare le stringhe di connessione

Accertarsi che le stringhe di connessione dell'applicazione facciano riferimento al database appena ripristinato. Aggiornare le stringhe di connessione, se si verifica una delle situazioni seguenti:

  + Nel database ripristinato viene utilizzato un nome diverso rispetto al nome del database di origine
  + Il database ripristinato si trova in un server diverso rispetto al server di origine

Per altre informazioni sulla modifica delle stringhe di connessione, vedere [Linee guida per la connessione al database SQL di Azure a livello di programmazione](https://msdn.microsoft.com/library/azure/ee336282.aspx) e [Panoramica dello sviluppo di database SQL](sql-database-develop-overview.md).
 
## Modificare le regole del firewall
Verificare le regole del firewall a livello di server e a livello di database, quindi assicurarsi che le connessioni tra i computer client o Azure con il server e il database appena ripristinato siano abilitate Per ulteriori informazioni, vedere [Firewall del database SQL di Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx) e [Procedura: configurare le impostazioni de firewall (database SQL di Azure)](https://msdn.microsoft.com/library/azure/jj553530.aspx).

## Verificare gli account di accesso al server e gli utenti del database

Verificare che tutti gli account di accesso utilizzati dall'applicazione siano presenti sul server che ospita il database ripristinato. Ricreare gli account di accesso mancanti e concedere loro le autorizzazioni appropriate per il database ripristinato. Per ulteriori informazioni, vedere [Gestione di database e account di accesso in database SQL di Azure](https://msdn.microsoft.com/library/azure/ee336235.aspx).

Verificare che ciascun utente del database ripristinato sia associato a un account di accesso a un server valido. Utilizzare l'istruzione ALTER USER per eseguire il mapping dell'utente all'account di accesso a un server valido. Per ulteriori informazioni, vedere [ALTER USER](http://go.microsoft.com/fwlink/?LinkId=397486).


## Avvisi di telemetria di configurazione

Verificare che le impostazioni della regola di avviso esistenti facciano riferimento al database ripristinato. Aggiornare la configurazione se si verifica una delle situazioni seguenti:

  + Nel database ripristinato viene utilizzato un nome diverso rispetto al nome del database di origine
  + Il database ripristinato si trova in un server diverso rispetto al server di origine

Per ulteriori sulle regole di avviso per il database, vedere [Procedura: ricevere notifiche di avviso e gestire le relative regole in Azure](https://msdn.microsoft.com/library/azure/dn306638.aspx).


## Attivare il controllo

Se è necessario il controllo di accesso al database, occorre attivare il controllo dopo il ripristino del database. Un indicatore efficace del fatto che è necessario un controllo è che le applicazioni client utilizzano stringhe di connessione protette in base a un modello di *.database.secure.windows.net. Per ulteriori informazioni, vedere [Introduzione al controllo del database SQL](sql-database-auditing-get-started.md).
 

<!---HONumber=AcomDC_0615_2016-->