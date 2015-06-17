<properties 
	pageTitle="Connessioni al database SQL di Azure: indicazioni principali" 
	description="Un argomento principale in cui vengono forniti collegamenti ad argomenti più specifici sulle varie unità, come ADO.NET e PHP, per connettersi al database SQL di Azure." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/19/2015" 
	ms.author="genemi"/>


#Connessioni al database SQL: indicazioni principali


<!--
GeneMi , 2015-March-19 Thursday 15:41pm
sql-database-connect-central-recommendations.md
sql-database-connect-*.md

Re SqlException, not .HResult, rather .Number.
-->


In questo argomento vengono forniti collegamenti agli esempi di codice per le varie tecnologie che è possibile utilizzare per connettersi e interagire con il database SQL di Azure. Rientrano in queste tecnologie Enterprise Library, JDBC e PHP. Vengono fornite indicazioni in genere applicabili indipendentemente dalla tecnologia di connessione specifica.


##Indicazioni non correlate alla tecnologia


Le informazioni contenute in questa sezione si applicano indipendentemente dalla tecnologia specifica utilizzata per connettersi al database SQL.


- In [Linee guida per la connessione al database SQL di Azure a livello di programmazione](http://msdn.microsoft.com/library/azure/ee336282.aspx) sono trattati gli argomenti seguenti:
 - Porte
 - Firewall
 - Stringhe di connessione
- In [Gestione delle risorse nel database SQL di Azure](https://msdn.microsoft.com/library/azure/dn338083.aspx) sono trattati gli argomenti seguenti:
 - Governance delle risorse
 - Applicazione dei limiti
 - Limitazione


Indipendentemente dal tipo di tecnologia di connessione utilizzata, determinate impostazioni del firewall per il server del database SQL e di ogni singolo database, riguardano:


- [Firewall del database SQL di Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx)


###Indicazioni rapide


####Connessione


- Nella logica di connessione client sostituire il timeout predefinito affinché sia pari a 30 secondi.
 - Il valore predefinito di 15 secondi è troppo breve per connessioni che dipendono da Internet.
- Se si utilizza un [pool di connessioni](http://msdn.microsoft.com/library/8xx3tyca.aspx), chiudere la connessione nel momento in cui il programma non la usa attivamente, né si prepara a riusarla.
 - A meno che la connessione non venga riutilizzata dal programma per un'altra operazione immediatamente, senza pause, si consiglia il modello seguente:
<br/><br/>Aprire una connessione.
<br/>Eseguire un'operazione tramite la connessione.
<br/>Chiudere la connessione.<br/><br/>
- Usare la logica di ripetizione dei tentativi con la logica di connessione, ma solo per gli errori temporanei. Quando si utilizza il database SQL, è possibile che i tentativi di aprire una connessione o di inviare una query abbiano esito negativo per vari motivi.
 - Un motivo permanente di errore può risiedere nel fatto che la stringa di connessione non sia valida.
 - Un motivo temporaneo di errore può risiedere nel fatto che il sistema del database SQL di Azure necessiti del bilanciamento del carico complessivo. Il motivo temporaneo si risolve autonomamente, ovvero attraverso nuovi tentativi del programma.
 - Quando si tenta di inviare nuovamente una query, prima di tutto chiudere la connessione, quindi aprirne un'altra.
- Assicurarsi che il [firewall del database SQL di Azure](http://msdn.microsoft.com/library/ee621782.aspx) consenta la comunicazione TCP in uscita sulla porta 1433.
 - È possibile configurare le impostazioni del [firewall](http://msdn.microsoft.com/library/azure/ee621782.aspx) su un server del database SQL o per un singolo database.


####Autenticazione


- Usare l'autenticazione del database SQL, non quella di Windows.
- Specificare un database particolare, invece di usare il  *master* database predefinito.
- Talvolta è necessario fornire un nome utente dotato del suffisso *@nomeserver*, altre volte invece è possibile omettere il suffisso. Dipende dal modo in cui l'API o lo strumento in uso sono stati scritti.
 - Verificare i dettagli su ogni singola tecnologia.
- Effettuare la connessione specificando un utente in un [database indipendente](http://msdn.microsoft.com/library/ff929071.aspx).
 - Questo approccio fornisce scalabilità e prestazioni migliori, senza la necessità di un account di accesso per il database master.
 - Non è possibile usare l'istruzione **USE myDatabaseName;** di Transact-SQL sul database SQL.


###Errori temporanei


Alcuni errori di connessione del database SQL sono permanenti e non esiste nessun motivo per ritentare immediatamente la connessione. Altri errori sono temporanei e sono consigliati alcuni tentativi automatizzati dal programma. Esempi:


- *Permanente:* Se il nome del server del database SQL viene digitato in modo errato quando si tenta di effettuare la connessione, non ha senso ritentare.
- *Temporaneo:* Se la connessione attiva al database SQL viene interrotta in modo forzato dai sistemi di bilanciamento del carico o di limitazione di Azure, si consiglia di ritentare.


La classe [SqlException](https://msdn.microsoft.com/library/system.data.sqlclient.sqlexception.aspx) generata dalla chiamata al database SQL contiene un codice di errore numerico nella sua proprietà **Number**. Se il codice di errore rientra nell'elenco degli errori temporanei, il programma deve ritentare la chiamata.


- Nella sezione [Messaggi di errore (database SQL di Azure)](http://msdn.microsoft.com/library/azure/ff394106.aspx): **errori di perdita della connessione** è riportato un elenco di errori temporanei per i quali si garantisce un ulteriore tentativo.
 - Ad esempio, ritentare se si verifica il numero errore 40613, che indica<br/>*Database 'mydatabase' sul server 'theserver' è attualmente non disponibile.*


Per ulteriore assistenza quando si riscontra un errore di connessione permanente o temporaneo, vedere:


- [Risoluzione dei problemi di connessione nel database SQL di Azure](http://support.microsoft.com/it-it/kb/2980233/it-it)


##Tecnologie


Nell'argomento seguente sono riportati collegamenti agli esempi di codice per varie tecnologie di connessione:


- [Sviluppo nel database SQL di Azure: Procedure](http://msdn.microsoft.com/library/azure/ee621787.aspx)


Per ADO.NET con Enterprise Library e le classi di gestione degli errori temporanei, vedere:


- [Procedura: Effettuare una connessione affidabile a un database SQL di Azure](http://msdn.microsoft.com/library/azure/dn864744.aspx)


Per la funzionalità di scalabilità elastica, vedere:


- [Introduzione alla funzionalità di scalabilità elastica del database SQL di Azure (anteprima)](sql-database-elastic-scale-get-started.md)
- [Routing dipendente dei dati](sql-database-elastic-scale-data-dependent-routing.md)


##Post incompleti o non aggiornati


In questa sezione sono riportati collegamenti a post di blog o risorse simili, pertanto possono essere incompleti o non aggiornati. Tuttavia possono fornire informazioni generali.


- [Logica di ripetizione tentativi per errori temporanei nel database SQL di Microsoft Azure](http://social.technet.microsoft.com/wiki/contents/articles/4235.retry-logic-for-transient-failures-in-windows-azure-sql-database.aspx)

<!-- -->


<!--HONumber=49--> 