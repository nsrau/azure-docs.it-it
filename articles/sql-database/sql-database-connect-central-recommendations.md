<properties 
	pageTitle="Connessione al database SQL: collegamenti, procedure consigliate e linee guida per la progettazione"
	description="Un argomento di partenza che raggruppa collegamenti e indicazioni per i programmi client che si connettono al database SQL di Azure da tecnologie quali ADO.NET e PHP."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor=""/>


<tags 
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/05/2015"
	ms.author="genemi"/>


# Connessione al database SQL: collegamenti, procedure consigliate e linee guida per la progettazione


Questo argomento è un ottimo strumento per iniziare a utilizzare la connettività dei client al database SQL di Azure. Vengono forniti collegamenti agli esempi di codice per le varie tecnologie che è possibile utilizzare per connettersi e interagire con il database SQL. Rientrano in queste tecnologie Enterprise Library, JDBC e PHP e molte altre. Le informazioni contenute in questa sezione si applicano indipendentemente dalla tecnologia specifica utilizzata per connettersi al database SQL.


## Indicazioni non correlate alla tecnologia


- Le discussioni[Linee guida per la connessione al database SQL di Azure a livello di programmazione](http://msdn.microsoft.com/library/azure/ee336282.aspx) includono:
 - [Porte e firewall](https://azure.microsoft.com/it-it/documentation/articles/sql-database-configure-firewall-settings/)
 - Stringhe di connessione
- Le discussioni [Gestione delle risorse nel database SQL di Azure](https://msdn.microsoft.com/library/azure/dn338083.aspx) includono:
 - Governance delle risorse
 - Applicazione dei limiti
 - Limitazione




## Indicazioni di autenticazione


- Utilizzare l'autenticazione del Database di SQL Azure, non l'autenticazione di Windows che non è disponibile nel Database di SQL Azure.
- Specificare un database particolare, invece di usare il database predefinito *master*.
- Effettuare la connessione specificando un utente in un [database indipendente](http://msdn.microsoft.com/library/ff929071.aspx).
 - Questo approccio fornisce scalabilità e prestazioni migliori, senza la necessità di un account di accesso per il database master.
 - Non è possibile usare l'istruzione **USE myDatabaseName;** di Transact-SQL sul database SQL.


## Indicazioni di connessione


- Nella logica di connessione client sostituire il timeout predefinito affinché sia pari a 30 secondi.
 - Il valore predefinito di 15 secondi è troppo breve per connessioni che dipendono da Internet.
- Assicurarsi che il [firewall del database SQL di Azure](http://msdn.microsoft.com/library/ee621782.aspx) consenta la comunicazione TCP in uscita sulla porta 1433.
 - È possibile configurare le impostazioni del [firewall](http://msdn.microsoft.com/library/azure/ee621782.aspx) su un server del database SQL o per un singolo database.
- Se si utilizza un [pool di connessioni](http://msdn.microsoft.com/library/8xx3tyca.aspx), chiudere la connessione nel momento in cui il programma non la usa attivamente, né si prepara a riusarla.
 - A meno che la connessione non venga riutilizzata dal programma per un'altra operazione immediatamente, senza pause, si consiglia il modello seguente: <br/><br/>Aprire una connessione. <br/>Eseguire un'operazione tramite la connessione. <br/>Chiudere la connessione.<br/><br/>
- Usare la logica di ripetizione dei tentativi con la logica di connessione, ma solo per gli errori temporanei. Quando si utilizza il database SQL, è possibile che i tentativi di aprire una connessione o di inviare una query abbiano esito negativo per vari motivi.
 - Un motivo permanente di errore può risiedere nel fatto che la stringa di connessione non sia valida.
 - Un motivo temporaneo di errore può risiedere nel fatto che il sistema del database SQL di Azure necessiti del bilanciamento del carico complessivo. Il motivo temporaneo si risolve autonomamente, ovvero attraverso nuovi tentativi del programma.
 - Quando si tenta di inviare nuovamente una query, prima di tutto chiudere la connessione, quindi aprirne un'altra.


### Porte diverse da 1433 in V12


Le connessioni client al database SQL V12 di Azure talvolta ignorano il proxy e interagiscono direttamente con il database. Le porte diverse da 1433 diventano importanti. Per ulteriori informazioni vedere:<br/> [Porte superiori a 1433 per ADO.NET 4.5, ODBC 11 e database SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md)


Nella sezione successiva è possibile trovare ulteriori informazioni sulla gestione degli errori temporanei e la logica di ripetizione.


## Errori temporanei e logica di riesecuzione


I servizi cloud come Azure e il relativo servizio SQL Database implicano la sfida infinita di bilanciamento del carico di lavoro e della gestione delle risorse. Se due database trasferiti dallo stesso computer sono coinvolti in un’elaborazione eccezionalmente gravosa in tempi di sovrapposizione, il sistema di gestione potrebbe rilevare la necessità dello spostamento del carico di lavoro di un database a un'altra risorsa che ha capacità in eccesso.


Durante lo spostamento, il database potrebbe essere temporaneamente non disponibile. Questo potrebbe bloccare le nuove connessioni o causare la perdita di connessione del programma client. Lo spostamento di risorse è temporaneo e potrebbe risolversi in un paio di minuti o in alcuni secondi. Al termine dello spostamento, il programma client può ristabilire la connessione e riprendere il proprio lavoro. La sospensione dell'elaborazione è migliore di un errore evitabile del programma client.


Quando si verifica un errore con il Database SQL, viene generata un'eccezione [SqlException](https://msdn.microsoft.com/library/system.data.sqlclient.sqlexception.aspx). La `SqlException` contiene un codice di errore numerico nella relativa proprietà **Numero**. Se il codice di errore identifica un errore temporaneo, il programma deve ritentare la chiamata.


- [Messaggi di errore per programmi client di Database SQL](sql-database-develop-error-messages.md)
 - Nella sezione **errori temporanei, errori di perdita della connessione** è riportato un elenco degli errori temporanei per i quali si garantisce una ripetizione automatica.
 - Ad esempio, riprovare se si verifica il numero di errore 40613, con un testo simile a<br/>*Il database 'miodatabase' nel server 'ilserver' non è attualmente disponibile.*


Gli *errori* temporanei sono talvolta denominati *guasti* temporanei. In questo argomento si considerano questi due termini come sinonimi.


Per ulteriore assistenza quando si riscontra un errore di connessione, permanente o temporaneo, vedere:


- [Risoluzione dei problemi di connessione nel database SQL di Azure](http://support.microsoft.com/kb/2980233/)


Per i collegamenti agli argomenti di esempio di codice che illustrano la logica di riesecuzione, vedere:


- [Esempi di codice di avvio rapido del client per il database SQL](sql-database-develop-quick-start-client-code-samples.md)


<a id="gatewaynoretry" name="gatewaynoretry">&nbsp;</a>


## Logica di tentativi e proxy middleware


Il proxy middleware che funge da intermediario tra V11 e il client ADO.NET 4.5 gestisce un piccolo sottoinsieme di errori temporanei normalmente con logica di ripetizione tentativi. Nei casi in cui il proxy si connette al secondo tentativo, il programma client non è consapevole che il primo tentativo non riuscito.


Il proxy V12 gestisce un sottoinsieme più piccolo di errori temporanei. In altri casi V12 il proxy viene ignorato per la velocità superiore di connessione diretta al Database SQL. Per un programma client ADO.NET 4.5, queste modifiche rendono il Database SQL V12 di Azure più simile a Microsoft SQL Server.


Per esempi di codice che illustrino la logica di ripetizione tentativi, vedere: <br/>[Esempi di codice di avvio rapido del client per il database SQL](sql-database-develop-quick-start-client-code-samples.md).


> [AZURE.TIP]In un ambiente di produzione, è consigliabile che i client che si connettono al database SQL V11 o V12 di Azure implementino nel loro codice una logica di ripetizione tentativi. Il codice può essere personalizzato oppure può sfruttare un’API come la libreria Enterprise.


## Tecnologie


Gli argomenti seguenti contengono collegamenti a esempi di codice per diversi linguaggi e tecnologie di driver che è possibile utilizzare per connettersi al database SQL di Azure dal programma client.


Vengono forniti vari esempi di codice per i client che eseguono Windows, Linux e Mac OS X.


**Esempi generali:** sono disponibili esempi di codice per un'ampia gamma di linguaggi di programmazione, tra cui PHP, Python, Node.js e .NET CSharp. Inoltre, sono forniti esempi per i client che eseguono Windows, Linux e Mac OS X.


- [Sviluppo client ed esempi di codice di avvio rapido per il database SQL](sql-database-develop-quick-start-client-code-samples.md)
- [Sviluppo per il database SQL di Azure: procedure](http://msdn.microsoft.com/library/azure/ee621787.aspx)


**Scalabilità elastica:** per informazioni sulla connettività ai database con scalabilità elastica, vedere:


- [Introduzione alla funzionalità di scalabilità elastica del database SQL di Azure (anteprima)](sql-database-elastic-scale-get-started.md)
- [Routing dipendente dei dati](sql-database-elastic-scale-data-dependent-routing.md)


**Librerie driver:** per informazioni sulle librerie dei driver di connessione, incluse le versioni consigliate, vedere:


- [Raccolte di connessioni per database SQL e SQL Server](sql-database-libraries.md)


## Vedere anche


- [Creare il primo database SQL di Azure](sql-database-get-started.md)

 

<!---HONumber=August15_HO9-->