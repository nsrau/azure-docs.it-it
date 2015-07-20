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
	ms.date="05/01/2015" 
	ms.author="genemi"/>


# Connessione al database SQL: collegamenti, procedure consigliate e linee guida per la progettazione


Questo argomento è un ottimo strumento per iniziare a utilizzare la connettività dei client al database SQL di Azure. Vengono forniti collegamenti agli esempi di codice per le varie tecnologie che è possibile utilizzare per connettersi e interagire con il database SQL. Rientrano in queste tecnologie Enterprise Library, JDBC e PHP e molte altre. Vengono fornite indicazioni in genere applicabili indipendentemente dalla tecnologia di connessione specifica o dal linguaggio di programmazione.


## Indicazioni non correlate alla tecnologia


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


## Raccomandazioni: connessione


- Nella logica di connessione client sostituire il timeout predefinito affinché sia pari a 30 secondi.
 - Il valore predefinito di 15 secondi è troppo breve per connessioni che dipendono da Internet.
- Se si utilizza un [pool di connessioni](http://msdn.microsoft.com/library/8xx3tyca.aspx), chiudere la connessione nel momento in cui il programma non la usa attivamente, né si prepara a riusarla.
 - A meno che la connessione non venga riutilizzata dal programma per un'altra operazione immediatamente, senza pause, si consiglia il modello seguente: <br/><br/>Aprire una connessione <br/>Eseguire un'operazione tramite la connessione. <br/>Chiudere la connessione.<br/><br/>
- Usare la logica di ripetizione dei tentativi con la logica di connessione, ma solo per gli errori temporanei. Quando si utilizza il database SQL, è possibile che i tentativi di aprire una connessione o di inviare una query abbiano esito negativo per vari motivi.
 - Un motivo permanente di errore può risiedere nel fatto che la stringa di connessione non sia valida.
 - Un motivo temporaneo di errore può risiedere nel fatto che il sistema del database SQL di Azure necessiti del bilanciamento del carico complessivo. Il motivo temporaneo si risolve autonomamente, ovvero attraverso nuovi tentativi del programma.
 - Quando si tenta di inviare nuovamente una query, prima di tutto chiudere la connessione, quindi aprirne un'altra.
- Assicurarsi che il [firewall del database SQL di Azure](http://msdn.microsoft.com/library/ee621782.aspx) consenta la comunicazione TCP in uscita sulla porta 1433.
 - È possibile configurare le impostazioni del [firewall](http://msdn.microsoft.com/library/azure/ee621782.aspx) su un server del database SQL o per un singolo database.


## Suggerimento: autenticazione


- Usare l'autenticazione del database SQL, non quella di Windows.
- Specificare un database particolare, invece di usare il database predefinito *master*.
- Talvolta è necessario fornire un nome utente dotato del suffisso *@nomeserver*, altre volte invece è possibile omettere il suffisso. Dipende dal modo in cui l'API o lo strumento in uso sono stati scritti.
 - Verificare i dettagli su ogni singola tecnologia.
- Effettuare la connessione specificando un utente in un [database indipendente](http://msdn.microsoft.com/library/ff929071.aspx).
 - Questo approccio fornisce scalabilità e prestazioni migliori, senza la necessità di un account di accesso per il database master.
 - Non è possibile usare l'istruzione **USE myDatabaseName;** di Transact-SQL sul database SQL.


## Si sono verificati errori temporanei


I servizi cloud come Azure e il relativo servizio SQL Database implicano la sfida infinita di bilanciamento del carico di lavoro e della gestione delle risorse. Se due database trasferiti dallo stesso computer sono coinvolti in un’elaborazione eccezionalmente gravosa in tempi di sovrapposizione, il sistema di gestione potrebbe rilevare la necessità dello spostamento del carico di lavoro di un database a un'altra risorsa che ha capacità in eccesso.


Durante lo spostamento, il database potrebbe essere temporaneamente non disponibile. Questo potrebbe bloccare le nuove connessioni o causare la perdita di connessione del programma client. Lo spostamento di risorse è temporaneo e potrebbe risolversi in un paio di minuti o in alcuni secondi. Al termine dello spostamento, il programma client può ristabilire la connessione e riprendere il proprio lavoro. La sospensione dell'elaborazione è migliore di un errore evitabile del programma client.


Quando si verifica un errore con il SQL Database, viene generata un'eccezione [SqlException](https://msdn.microsoft.com/library/system.data.sqlclient.sqlexception.aspx). L’eccezione SqlException contiene un codice di errore numerico nella relativa proprietà **Numero**. Se il codice di errore identifica un errore temporaneo, il programma deve ritentare la chiamata.


- Nella sezione [Messaggi di errore (database SQL di Azure)](http://msdn.microsoft.com/library/azure/ff394106.aspx): **errori di perdita della connessione** è riportato un elenco di errori temporanei per i quali si garantisce un ulteriore tentativo automatico.
 - Ad esempio, riprovare se si verifica il numero di errore 40613, con un testo simile a <br/>*Il database 'miodatabase' nel server 'ilserver' non è attualmente disponibile.*


Gli *errori* temporanei sono talvolta denominati *guasti* temporanei. In questo argomento si considerano questi due termini come sinonimi.


Per ulteriore assistenza quando si riscontra un errore di connessione, permanente o temporaneo, vedere:


- [Risoluzione dei problemi di connessione nel database SQL di Azure](http://support.microsoft.com/kb/2980233/)


## Tecnologie


Gli argomenti seguenti contengono collegamenti a esempi di codice per diversi linguaggi e tecnologie di driver che è possibile utilizzare per connettersi al database SQL di Azure dal programma client.


Vengono forniti vari esempi di codice per i client che eseguono sia Windows che Linux.


**Esempi generali:** sono disponibili esempi di codice per un'ampia gamma di linguaggi di programmazione, tra cui PHP, Python, Node.js e .NET CSharp. Inoltre, sono forniti esempi per i client che eseguono Windows o Linux.


- [Sviluppo client ed esempi di codice di avvio rapido per il database SQL](sql-database-develop-quick-start-client-code-samples.md)
- [Sviluppo per il database SQL di Azure: procedure](http://msdn.microsoft.com/library/azure/ee621787.aspx)


**Logica di ripetizione tentativi:** sono disponibili esempi di codice progettati con la complessità necessaria per gestire errori temporanei con la logica di ripetizione tentativi automatica.


- [Procedura: effettuare una connessione affidabile a un database SQL di Azure](http://msdn.microsoft.com/library/azure/dn864744.aspx)
- [Procedura: connettersi al database SQL di Azure utilizzando ADO.NET con Enterprise Library](http://msdn.microsoft.com/library/azure/dn961167.aspx)
- [Procedura: connettersi al database SQL di Azure mediante ADO.NET](http://msdn.microsoft.com/library/azure/ee336243.aspx)


**Scalabilità elastica:** per informazioni sulla connettività ai database con scalabilità elastica, vedere:


- [Introduzione alla funzionalità di scalabilità elastica del database SQL di Azure (anteprima)](sql-database-elastic-scale-get-started.md)
- [Routing dipendente dei dati](sql-database-elastic-scale-data-dependent-routing.md)


**Librerie driver:** per informazioni sulle librerie dei driver di connessione, incluse le versioni consigliate, vedere:


- [Raccolte di connessioni per database SQL e SQL Server](sql-database-libraries.md)


## Vedere anche


- [Creare il primo database SQL di Azure](sql-database-get-started.md)

 

<!---HONumber=July15_HO2-->