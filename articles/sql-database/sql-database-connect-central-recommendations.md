<properties 
	pageTitle="Connettersi al Database SQL: procedure consigliate | Microsoft Azure" 
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
	ms.date="01/07/2016" 
	ms.author="genemi"/>


# Connessione al database SQL: procedure consigliate e linee guida per la progettazione


Questo argomento è un ottimo strumento per iniziare a utilizzare la connettività dei client al database SQL di Azure. Vengono forniti collegamenti agli esempi di codice per le varie tecnologie che è possibile utilizzare per connettersi e interagire con il database SQL. Rientrano in queste tecnologie Enterprise Library, JDBC e PHP e molte altre. Le informazioni contenute in questa sezione si applicano indipendentemente dalla tecnologia specifica utilizzata per connettersi al database SQL.


<a id="a-tech-independent-recommend" name="a-tech-independent-recommend"></a>

## Indicazioni non correlate alla tecnologia


- Le discussioni[Linee guida per la connessione al database SQL di Azure a livello di programmazione](http://msdn.microsoft.com/library/azure/ee336282.aspx) includono:
 - [Porte e firewall](sql-database-configure-firewall-settings.md)
 - Stringhe di connessione
- Le discussioni [Gestione delle risorse nel database SQL di Azure](http://msdn.microsoft.com/library/azure/dn338083.aspx) includono:
 - Governance delle risorse
 - Applicazione dei limiti
 - Limitazione


<a id="b-authentication-recommend" name="b-authentication-recommend"></a>

## Indicazioni di autenticazione


- Utilizzare l'autenticazione del Database di SQL Azure, non l'autenticazione di Windows che non è disponibile nel Database di SQL Azure.
- Specificare un database particolare, invece di usare il database predefinito *master*.
 - Non è possibile usare l'istruzione **USE myDatabaseName;** di Transact-SQL sul database SQL per passare a un altro database.


### Utenti indipendenti


Quando si aggiunge un utente al database SQL, sono disponibili diverse opzioni:

- Aggiungere un *account di accesso* con una password al database **master** e quindi aggiungere un *utente* corrispondente a uno o più database nello stesso server.

- Aggiungere un *utente indipendente* con una password a uno o più database senza collegamenti agli *account di accesso* nel database **master**.


L'approccio dell'utente indipendente presenta vantaggi e svantaggi:

- Il vantaggio è che un database può facilmente essere spostato da un server di database SQL di Azure a un altro, quando tutti gli utenti nel database sono indipendenti.

- Lo svantaggio è la maggiore difficoltà nell'amministrazione di routine. Ad esempio:
 - È più complesso eliminare più utenti indipendenti che eliminare un account di accesso.
 - Un utente indipendente in diversi database potrebbe avere più password da ricordare o da aggiornare.


Per altre informazioni, vedere [Utenti di database indipendente: rendere portabile un database](http://msdn.microsoft.com/library/ff929188.aspx).


<a id="c-connection-recommend" name="c-connection-recommend"></a>

## Indicazioni di connessione


- Nella logica di connessione client sostituire il timeout predefinito affinché sia pari a 30 secondi.
 - Il valore predefinito di 15 secondi è troppo breve per connessioni che dipendono da Internet.


- Nel computer che ospita il programma client, verificare che il firewall consenta le comunicazioni TCP in uscita sulla porta 1433.


- Se il programma client si connette a SQL Database V12 mentre il client viene eseguito in una macchina virtuale (VM) di Azure, è necessario aprire gli intervalli di porta 11000-11999 e 14000-14999 nella macchina virtuale: Per informazioni dettagliate, fare clic [qui](sql-database-develop-direct-route-ports-adonet-v12.md).


- Per gestire gli *errori temporanei*, aggiungere la [logica di *ripetizione dei tentativi*](#TransientFaultsAndRetryLogicGm) ai programmi client che interagiscono con il database SQL di Azure.


### Pool di connessioni


Se si usa un [pool di connessioni](http://msdn.microsoft.com/library/8xx3tyca.aspx), chiudere la connessione nel momento in cui il programma non la usa attivamente, né si prepara a riusarla.

A meno che la connessione non venga riutilizzata dal programma per un'altra operazione immediatamente e senza pause, si consiglia il modello seguente:

- Aprire una connessione.
- Eseguire un'operazione tramite la connessione.
- Chiudere la connessione.


### Porte diverse da 1433 in V12


Le connessioni client al database SQL V12 di Azure talvolta ignorano il proxy e interagiscono direttamente con il database. Le porte diverse da 1433 diventano importanti. Per informazioni dettagliate, vedere:<br/> [Porte superiori a 1433 per ADO.NET 4.5 e database SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md)


Nella sezione successiva è possibile trovare ulteriori informazioni sulla gestione degli errori temporanei e la logica di ripetizione.



<a name="TransientFaultsAndRetryLogicGm" id="TransientFaultsAndRetryLogicGm"></a>

& nbsp;

## Errori temporanei e logica di ripetizione dei tentativi


Il sistema Azure è in grado di riconfigurare dinamicamente i server quando si verificano carichi di lavoro intensi nel servizio del database SQL.

Una riconfigurazione potrebbe tuttavia causare la perdita della connessione del programma client al database SQL. Questo errore è denominato *errore temporaneo*.

Se il programma client dispone di logica di ripetizione dei tentativi, è possibile provare a ristabilire una connessione dopo aver dato all’errore temporaneo del tempo per correggere stesso.

È consigliabile attendere 5 secondi prima di riprovare. Al primo tentativo con un ritardo inferiore a 5 secondi, si rischia di sovraccaricare il servizio cloud. Per ogni tentativo successivo, aumentare in modo esponenziale il ritardo, fino a un massimo di 60 secondi.

Per i client che usano ADO.NET, è disponibile una discussione sul *periodo di blocco* in [Pool di connessioni di SQL Server (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).


Per esempi di codice che illustrano la logica di ripetizione dei tentativi, vedere:

- [Esempi di codice di avvio rapido del client per il database SQL](sql-database-develop-quick-start-client-code-samples.md)


### Numeri di errore per gli errori temporanei


Quando si verifica un errore del database SQL, viene generata un'eccezione [SqlException](http://msdn.microsoft.com/library/system.data.sqlclient.sqlexception.aspx). L'eccezione **SqlException** contiene un codice di errore numerico nella relativa proprietà **Number**. Se il codice di errore identifica un errore temporaneo, il programma deve ritentare la chiamata.


- [Messaggi di errore per programmi client di Database SQL](sql-database-develop-error-messages.md#bkmk_connection_errors)
 - Nella sezione **Errori temporanei, errori di perdita della connessione** è riportato un elenco degli errori temporanei per i quali si garantisce una ripetizione automatica.
 - Riprovare ad esempio se si verifica il numero di errore 40613, con un testo simile a<br/>*Il database 'mydatabase' nel server 'theserver' non è attualmente disponibile.*


Per altre informazioni, vedere:

- [Sviluppo per il database SQL di Azure: procedure](http://msdn.microsoft.com/library/azure/ee621787.aspx)

<!--  (per Penny Lee, 2016/01/07.  MightyPen==GeneMi)
- [Troubleshoot connection problems to Azure SQL Database](http://support.microsoft.com/kb/2980233/)
-->


<a id="e-technologies" name="e-technologies"></a>

## Tecnologie


Gli argomenti seguenti contengono collegamenti a esempi di codice per diversi linguaggi e tecnologie di driver che è possibile utilizzare per connettersi al database SQL di Azure dal programma client.


Vengono forniti vari esempi di codice per i client che eseguono Windows, Linux e Mac OS X.


**Esempi generali:** sono disponibili [esempi di codice](sql-database-develop-quick-start-client-code-samples.md) per un'ampia gamma di linguaggi di programmazione, tra cui PHP, Python, Node.js e .NET CSharp. Inoltre, sono forniti esempi per i client che eseguono Windows, Linux e Mac OS X.


**Scalabilità elastica:** per informazioni sulla connettività ai database con scalabilità elastica, vedere:

- [Introduzione alla funzionalità di scalabilità elastica del database SQL di Azure (anteprima)](sql-database-elastic-scale-get-started.md)
- [Routing dipendente dei dati](sql-database-elastic-scale-data-dependent-routing.md)


**Librerie driver:** per informazioni sulle librerie dei driver di connessione, incluse le versioni consigliate, vedere:

- [Raccolte di connessioni per database SQL e SQL Server](sql-database-libraries.md)

<!---HONumber=AcomDC_0114_2016-->