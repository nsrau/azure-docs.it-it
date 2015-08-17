<properties 
	pageTitle="Messaggi di errore per programmi client di Database SQL"
	description="Per ciascun errore, viene visualizzato l’ID numerico e il messaggio di testo. In base alle esigenze, è possibile usare riferimenti incrociati personalizzati a testi di messaggi di errore più descrittivi."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor="" />


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/27/2015" 
	ms.author="genemi"/>


# Messaggi di errore per programmi client di Database SQL


<!--
Old Title on MSDN:  Error Messages (Azure SQL Database)
ShortId on MSDN:  ff394106.aspx
Dx 4cff491e-9359-4454-bd7c-fb72c4c452ca
-->


In questo argomento sono elencate diverse categorie di messaggi di errore. La maggior parte delle categorie sono specifiche di Database SQL di Azure e non si applicano a Microsoft SQL Server.


Nel programma client, è possibile fornire all'utente un messaggio alternativo personalizzato per ogni errore specificato.


**Suggerimento:** di estrema importanza è la sezione relativa ai *guasti temporanei*. Con questi errori viene richiesto al programma client di eseguire la logica di *ripetizione tentativi* progettata per ritentare l'operazione.


<a id="bkmk_connection_errors" name="bkmk_connection_errors">&nbsp;</a>


## Guasti temporanei, perdita di connessione e altri errori temporanei

Nella tabella seguente vengono illustrati gli errori di perdita della connessione e altri errori temporanei, che possono verificarsi durante l'utilizzo su Internet di Database SQL Azure.

Gli errori temporanei sono detti anche guasti temporanei. Quando il programma rileva una `SqlException`, può verificare se il valore `sqlException.Number` è elencato in questa sezione di guasti temporanei. Se il valore `Number` indica un guasto temporaneo, il programma può provare a ristabilire una connessione e quindi ripetere l'esecuzione della query tramite la connessione. Per esempi di codice relativi alla logica di ripetizione dei tentativi, vedere:


- [Sviluppo client ed esempi di codice di avvio rapido per il database SQL](sql-database-develop-quick-start-client-code-samples.md)

- [Procedura: Effettuare una connessione affidabile a un database SQL di Azure](http://msdn.microsoft.com/library/azure/dn864744.aspx)


| Numero di errore | Gravità | Descrizione |
| ---: | ---: | :--- |
| 4060 | 16 | Impossibile aprire il database "%.&#x2a;ls" richiesto dall'account di accesso. Accesso non riuscito. |
|10928|20|ID risorsa: %d. Il limite di %s per il database è %d ed è stato raggiunto. Per ulteriori informazioni, vedere [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637).<br/><br/>L'ID risorsa indica la risorsa che ha raggiunto il limite. Per i thread di lavoro, l’ID risorsa = 1. Per le sessioni, l’ID risorsa = 2.<br/><br/>*Nota:* per altre informazioni su questo errore e su come risolverlo, vedere:<br/>•[Governance delle risorse nel database SQL di Azure](http://msdn.microsoft.com/library/azure/dn338078.aspx). |
|10929|20|ID risorsa: %d. La %s di garanzia minima è %d, il limite massimo è %d e l'utilizzo corrente per il database è %d. Tuttavia, il server attualmente è troppo occupato per supportare richieste superiori a %d per questo database. Per altre informazioni, vedere [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637). In caso contrario, riprovare più tardi.<br/><br/>L'ID risorsa indica la risorsa che ha raggiunto il limite. Per i thread di lavoro, l’ID risorsa = 1. Per le sessioni, l’ID risorsa = 2.<br/><br/>*Nota:* per altre informazioni su questo errore e su come risolverlo, vedere:<br/>•[Governance delle risorse nel database SQL di Azure](http://msdn.microsoft.com/library/azure/dn338078.aspx).|
|40197|17|Il servizio ha rilevato un errore durante l'elaborazione della richiesta. Riprova più tardi. Codice di errore %d.<br/><br/>Questo errore viene visualizzato quando il servizio non è disponibile a causa di aggiornamenti software o hardware, guasti hardware o altri problemi di failover. Nel codice di errore (%d) incorporato nel messaggio di errore 40197 sono contenute ulteriori informazioni sul tipo di errore o failover che si è verificato. Alcuni esempi dei codici di errore incorporati nel messaggio di errore 40197 sono 40020, 40143, 40166 e 40540.<br/><br/>Con la riconnessione al server di database SQL verrà effettuata la connessione automatica a una copia integra del database. L'applicazione deve rilevare l'errore 40197, registrare il codice di errore incorporato (%d) nel messaggio per la risoluzione dei problemi e tentare la riconnessione al database SQL finché le risorse non saranno disponibili e la connessione non sarà stata ristabilita.|
|40501|20|Il servizio è attualmente occupato. Ripetere la richiesta dopo 10 secondi. ID evento imprevisto: %ls. Codice: %d.<br/><br/>*Nota:* per altre informazioni su questo errore e su come risolverlo, vedere:<br/>• [Limitazione del database SQL di Azure](http://msdn.microsoft.com/library/azure/dn338079.aspx).
|40613|17|Il database '%.&#x2a;ls' nel server '%.&#x2a;ls' non è attualmente disponibile. Eseguire nuovamente la connessione in un secondo momento. Se il problema persiste, contattare il supporto tecnico indicando l'ID di traccia della sessione di '%.&#x2a;ls'.|


**Nota:** gli errori della federazione 10053 e 10054 potrebbero anche determinare l’inclusione nella logica di ripetizione tentativi.


## Errori di copia del database


Nella tabella seguente vengono illustrati i diversi errori che è possibile rilevare durante la copia di un database in Database SQL Azure. Per altre informazioni, vedere, [Copia di database nel database SQL di Azure](http://msdn.microsoft.com/library/azure/ff951624.aspx).


|Numero di errore|Gravità|Descrizione|
|---:|---:|:---|
|40635|16|Il client con indirizzo IP '%.&#x2a;ls' è temporaneamente disabilitato.|
|40637|16|La creazione della copia del database è attualmente disabilitata.|
|40561|16|Copia del database non riuscita. Il database di origine o di destinazione non esiste.|
|40562|16|Copia del database non riuscita. Il database di origine è stato rimosso.|
|40563|16|Copia del database non riuscita. Il database di destinazione è stato rimosso.|
|40564|16|Copia del database non riuscita a causa di un errore interno. Rimuovere il database di destinazione e riprovare.|
|40565|16|Copia del database non riuscita. Non è consentita più di una copia simultanea del database dalla stessa origine. Rimuovere il database di destinazione e riprovare in un secondo momento.|
|40566|16|Copia del database non riuscita a causa di un errore interno. Rimuovere il database di destinazione e riprovare.|
|40567|16|Copia del database non riuscita a causa di un errore interno. Rimuovere il database di destinazione e riprovare.|
|40568|16|Copia del database non riuscita. Il database di origine non è più disponibile. Rimuovere il database di destinazione e riprovare.|
|40569|16|Copia del database non riuscita. Il database di destinazione non è più disponibile. Rimuovere il database di destinazione e riprovare.|
|40570|16|Copia del database non riuscita a causa di un errore interno. Rimuovere il database di destinazione e riprovare in un secondo momento.|
|40571|16|Copia del database non riuscita a causa di un errore interno. Rimuovere il database di destinazione e riprovare in un secondo momento.|


## Errori di governance delle risorse


Nella tabella seguente vengono illustrati gli errori causati dall'uso eccessivo delle risorse durante l’utilizzo di Database SQL di Azure. ad esempio:


- Forse la transazione è rimasta aperta troppo a lungo.
- Forse la transazione contiene troppi blocchi.
- Forse il programma utilizza troppa memoria.
- Forse il programma utilizza troppo spazio `TempDb`.


**Suggerimento:** il collegamento seguente fornisce altre informazioni relative alla maggior parte o a tutti gli errori in questa sezione:


- [Limiti delle risorse del database SQL di Azure](http://msdn.microsoft.com/library/azure/dn338081.aspx)


|Numero di errore|Gravità|Descrizione|
|---:|---:|:---|
|40544|20|Il database ha raggiunto la quota delle dimensioni. Partizionare o eliminare dati, eliminare indici o consultare la documentazione per le possibili soluzioni.|
|40549|16|La sessione è stata terminata a causa di una transazione a esecuzione prolungata. Provare ad abbreviare la transazione.|
|40550|16|La sessione è stata terminata perché sono stati acquisiti troppi blocchi. Provare a leggere o modificare meno righe in una singola transazione.|
|40551|16|La sessione è stata terminata a causa dell'utilizzo eccessivo di `TEMPDB`. Provare a modificare la query per ridurre l'utilizzo di spazio della tabella temporanea.<br/><br/>*Suggerimento:* se si usano oggetti temporanei, liberare spazio nel database `TEMPDB` rimuovendo gli oggetti temporanei se non sono più necessari per la sessione.|
|40552|16|La sessione è stata terminata a causa di un utilizzo eccessivo di spazio del log della transazione. Provare a modificare un numero inferiore di righe in una sola transazione.<br/><br/>*Suggerimento:* se si eseguono inserimenti bulk usando l’utilità `bcp.exe` o la classe `System.Data.SqlClient.SqlBulkCopy`, provare a usare le opzioni `-b batchsize` o `BatchSize` per limitare il numero di righe copiate nel server in ogni transazione. In caso di ricompilazione di un indice con l’istruzione `ALTER INDEX`, provare a usare l’opzione `REBUILD WITH ONLINE = ON`.|
|40553|16|La sessione è stata terminata a causa di un utilizzo eccessivo della memoria. Provare a modificare la query per elaborare un numero inferiore di righe.<br/><br/>*Suggerimento:* la riduzione del numero di operazioni `ORDER BY` e `GROUP BY` nel codice Transact-SQL consente di ridurre i requisiti di memoria della query.|


Per altre informazioni sulla governance delle risorse e gli errori correlati, vedere:


- [Governance delle risorse del database SQL di Azure](http://msdn.microsoft.com/library/azure/dn338078.aspx).


## Errori della federazione


Nella tabella seguente vengono illustrati gli errori che è possibile riscontrare durante l'uso delle federazioni. Per altre informazioni, vedere [Gestione di federazioni di database (database SQL di Azure)](http://msdn.microsoft.com/library/azure/hh597455.aspx).


> [AZURE.IMPORTANT]L'implementazione corrente delle federazioni verrà ritirata con i livelli di servizio Web e Business. La versione V12 di Database SQL di Azure non supporta i livelli di servizio Web e Business.
> 
> La funzionalità Scalabilità elastica è progettata per creare applicazioni di partizionamento orizzontale con uno sforzo minimo.
> 
> Per altre informazioni sulla Scalabilità elastica, vedere gli [argomenti relativi alla Scalabilità elastica di Database SQL di Azure](sql-database-elastic-scale-documentation-map.md). Valutare la possibilità di distribuire soluzioni di partizionamento orizzontale personalizzato per l’ottimizzazione di scalabilità, flessibilità e prestazioni. Per altre informazioni sul partizionamento orizzontale personalizzato, vedere [Scalabilità orizzontale dei database SQL di Azure](http://msdn.microsoft.com/library/azure/dn495641.aspx).


|Numero di errore|Gravità|Descrizione|Mitigazione|
|---:|---:|:---|:---|
|266|16|L’istruzione <statement> non è consentita nelle transazioni a istruzioni multiple|Controllare che `@@trancount` sia 0 nella connessione prima di eseguire l'istruzione.|
|2072|16|Il database '%.&#x2a;ls' non esiste|Controllare `sys.databases` per lo stato del database prima di eseguire `USE FEDERATION`.|
|2209|16|%s Errore di sintassi in prossimità di ‘%ls’|`FEDERATED ON` può essere usato solo quando si creano tabelle nei membri federativi.|
|2714|16|Nel database esiste già un oggetto con il nome '%.&#x2a;ls'|Il nome federativo esiste già.|
|10054, 10053|20|Si è verificato un errore a livello di trasporto durante la ricezione dei risultati dal server. Una connessione stabilita è stata interrotta dal software nel computer host|Implementare la logica di ripetizione tentativi nell'applicazione.|
|40530|15|<statement> deve essere l'unica istruzione nel batch|Verificare che nel batch non siano presenti altre istruzioni|
|40604|16|Impossibile elaborare `CREATE DATABASE` perché supererebbe la quota del server|Espandere la quota conteggio database del server|
|45000|16|Operazione <statement> non riuscita. Il nome federazione specificato <federation_name> non è valido|Federation\_name non è conforme alle regole del nome di federazione o non è un identificatore valido|
|45001|16|Operazione <statement> non riuscita. Il nome federazione specificato non esiste|Il nome federazione non esiste|
|45002|16|Operazione <statement> non riuscita. Il nome chiave di federazione specificato <distribution_name> non è valido|Chiave di federazione inesistente o non valida|
|45004|16|Operazione <statement> non riuscita. Il valore specificato non è valido per la chiave di federazione <distribution_name> e la federazione <federation_name>|`USE FEDERATION`: usare un valore limite incluso nel dominio del tipo di dati della chiave di federazione o che non è NULL.<br/><br/>`ALTER FEDERATION SPLIT`: usare un valore valido nel dominio della chiave di federazione che non sia un punto di divisione esistente<br/><br/>`ALTER FEDERATION DROP`: usare un valore valido nel dominio della chiave di federazione che sia un punto di divisione esistente.|
|45005|16|Impossibile eseguire <statement> mentre un'altra operazione di federazione è in corso sulla federazione <federation_name> e sul membro con ID <member_id>|Attendere il completamento dell'operazione simultanea.|
|45006|16|Operazioni <statement> non riuscite. Le relazioni di chiave esterna nelle tabelle di riferimento che fanno riferimento alle tabelle federate non sono consentite nei membri della federazione|Non supportato.|
|45007|16|Operazione <statement> non riuscita. Le relazioni di chiave esterna tra le tabelle di federazione devono includere le colonne chiave di federazione.|Non supportato|
|45008|16|Operazione <statement> non riuscita. Il tipo di dati della chiave di federazione non corrisponde al tipo di dati della colonna|Non supportato.|
|45009|16|Operazione <statement> non riuscita. L’operazione non è supportata nelle connessioni di filtro|Non supportato.|
|45010|16|Operazione <statement> non riuscita. Impossibile aggiornare la chiave federativa|Non supportato.|
|45011|16|Operazione <statement> non riuscita. Impossibile aggiornare lo schema della chiave di federazione|Non supportato.|
|45012|16|Il valore specificato per la chiave di federazione non è valido|Il valore deve essere compreso nell'intervallo indirizzato della connessione.<br/><br/>Se viene filtrato, il valore specificato della chiave di federazione.<br/><br/>Se non viene filtrato, l'intervallo usato dal membro di federazione.|
|45013|16|Il SID già esiste per un nome utente diverso|Il SID per un utente in un membro di federazione viene copiato dal SID dello stesso account utente nella radice di federazione. In determinate condizioni, è possibile che il SID sia già in uso.|
|45014|16|%ls non è supportato in %ls|Operazione non supportata.|
|45022|16|Operazione <statement> non riuscita. Il valore di limite specificato esiste già per la chiave di federazione <distribution_name> e la federazione <federation_name>|Specificare un valore che è già un valore limite.|
|45023|16|Operazione <statement> non riuscita. Il valore di limite specificato non esiste per la chiave di federazione <distribution_name> e la federazione <federation_name>|Specificare un valore che non è già un valore limite.|


## Errori generali


Nella tabella seguente sono elencati tutti gli errori generali che non rientrano nelle categorie precedenti.


|Numero di errore|Gravità|Descrizione|
|---:|---:|:---|
|15006|16|<AdministratorLogin> non è un nome valido perché contiene caratteri non validi.|
|18452|14|Accesso non riuscito. L'accesso proviene da un dominio non trusted e non può essere usato con l'autenticazione di Window.%.&#x2a;ls (Account di accesso di Windows non supportati in questa versione di SQL Server.)|
|18456|14|Accesso non riuscito per l'utente '%.&#x2a;ls'.%.&#x2a;ls%.&#x2a;ls (Accesso non riuscito per l'utente "%.&#x2a;ls". Modifica della password non riuscita La modifica della password durante l'accesso non è supportata in questa versione di SQL Server.)|
|18470|14|Accesso non riuscito per l’utente '%.&#x2a;ls'. Motivo: l'account è disabilitato.%.&#x2a;ls|
|40014|16|Non è possibile usare più database nella stessa transazione.|
|40054|16|Le tabelle senza indice cluster non sono supportate in questa versione di SQL Server. Creare un indice cluster e riprovare.|
|40133|15|Questa operazione non è supportata in questa versione di SQL Server.|
|40506|16|Il SID specificato non è valido per questa versione di SQL Server.|
|40507|16|Non è possibile richiamare '%.&#x2a;ls' con parametri in questa versione di SQL Server.|
|40508|16|L'istruzione USE non è supportata per il passaggio tra database. Usare una nuova connessione per connettersi a un altro database.|
|40510|16|L’istruzione '%.&#x2a;ls' non è supportata in questa versione di SQL Server.|
|40511|16|La funzione predefinita '%.&#x2a;ls' non è supportata in questa versione di SQL Server.|
|40512|16|La funzione deprecata '%ls' non è supportata in questa versione di SQL Server.|
|40513|16|La variabile server '%.&#x2a;ls' non è supportata in questa versione di SQL Server.|
|40514|16|'%ls non è supportato in questa versione di SQL Server.|
|40515|16|Il riferimento al nome server e/o database in '%.&#x2a;ls' non è supportato in questa versione di SQL Server.|
|40516|16|Gli oggetti temporanei globali non sono supportati in questa versione di SQL Server.|
|40517|16|L'opzione dell'istruzione o parola chiave '%.&#x2a;ls' non è supportata in questa versione di SQL Server.|
|40518|16|Il comando DBCC '%.&#x2a;ls' non è supportato in questa versione di SQL Server.|
|40520|16|La classe a protezione diretta '%S\_MSG' non è supportata in questa versione di SQL Server.|
|40521|16|La classe a protezione diretta '%S\_MSG' non è supportata nell’ambito server in questa versione di SQL Server.|
|40522|16|Il tipo di entità database '%.&#x2a;ls' non è supportato in questa versione di SQL Server.|
|40523|16|La creazione dell’utente implicito '%.&#x2a;ls' non è supportata in questa versione di SQL Server. Creare l’utente in modo esplicito prima di utilizzarlo.|
|40524|16|Il tipo di dati '%.&#x2a;ls' non è supportato in questa versione di SQL Server.|
|40525|16|WITH '%.ls’ non è supportato in questa versione di SQL Server.|
|40526|16|Il provider di set di righe '%.&#x2a;ls' non è supportato in questa versione di SQL Server.|
|40527|16|I server collegati non sono supportati in questa versione di SQL Server.|
|40528|16|Non è possibile eseguire il mapping degli utenti a certificati, chiavi asimmetriche o account di accesso Windows in questa versione di SQL Server.|
|40529|16|La funzione predefinita '%.&#x2a;ls' nel contesto di rappresentazione non è supportata in questa versione di SQL Server.|
|40532|11|Impossibile aprire il server "%.&#x2a;ls" richiesto dall'account di accesso. Accesso non riuscito.|
|40553|16|La sessione è stata terminata a causa di un utilizzo eccessivo della memoria. Provare a modificare la query per elaborare un numero inferiore di righe.<br/><br/>*Nota:* la riduzione del numero di operazioni `ORDER BY` e `GROUP BY` nel codice Transact-SQL consente di ridurre i requisiti di memoria della query.|
|40604|16|Impossibile CREATE/ALTER DATABASE perché supererebbe la quota del server.|
|40606|16|Collegamento di database non supportato in questa versione di SQL Server.|
|40607|16|Gli account di accesso Windows non sono supportati in questa versione di SQL Server.|
|40611|16|È possibile definire un massimo di 128 regole firewall per i server.|
|40614|16|L'indirizzo IP iniziale della regola firewall non può superare l'indirizzo IP finale.|
|40615|16|Impossibile aprire il server '{0}' richiesto dall'account di accesso. Non è consentito l'accesso del client con indirizzo IP '{1}' al server. Per consentire l'accesso, usare il portale di Database SQL o eseguire sp\_set\_firewall\_rule nel database master per creare una regola firewall per l'indirizzo IP o l’intervallo di indirizzi. Affinché la modifica diventi effettiva potrebbero essere necessari fino a cinque minuti.|
|40617|16|Il nome della regola firewall che inizia con <rule name> è troppo lungo. La lunghezza massima è 128.|
|40618|16|Il nome della regola firewall non può essere vuoto.|
|40620|16|Accesso non riuscito per l’utente "%.&#x2a;ls". Modifica della password non riuscita La modifica della password durante l'accesso non è supportata in questa versione di SQL Server.|
|40627|20|Operazione in corso nel server '{0}' e nel database '{1}'. Attendere alcuni minuti prima di riprovare.|
|40630|16|Convalida della password non riuscita. La password non soddisfa i criteri perché è troppo corta.|
|40631|16|La password specificata è troppo lunga. La password non deve contenere più di 128 caratteri.|
|40632|16|Convalida della password non riuscita. La password non soddisfa i criteri in quanto non è sufficientemente complessa.|
|40636|16|Impossibile utilizzare un nome di database riservato '%.&#x2a;ls' in questa operazione.|
|40638|16|ID sottoscrizione non valido <subscription-id>. La sottoscrizione non esiste.|
|40639|16|Richiesta non conforme allo schema:<schema error>.|
|40640|20|Eccezione imprevista rilevata dal server.|
|40641|16|Il percorso specificato non è valido.|
|40642|17|Server attualmente troppo occupato. Riprovare più tardi.|
|40643|16|Valore dell'intestazione x-ms-version specificato non valido.|
|40644|14|Impossibile autorizzare l'accesso alla sottoscrizione specificata.|
|40645|16|Il nome del server <servername> non può essere vuoto o Null. Può essere costituito solo da lettere in minuscolo comprese tra a e z, numeri da 0 a 9 e dal segno meno (-) che non può essere la parte iniziale o finale del nome|
|40646|16|L’ID sottoscrizione non può essere vuoto.|
|40647|16|Server nomeserver non disponibile per la sottoscrizione < id-sottoscrizione.|
|40648|17|Esecuzione di un numero eccessivo di richieste. Riprovare più tardi.|
|40649|16|È stato specificato un tipo di contenuto non valido. È supportato solo il codice XML o l’applicazione.|
|40650|16|La sottoscrizione <subscription-id> non esiste o non è pronta per l'operazione.|
|40651|16|Impossibile creare il server perché la sottoscrizione <subscription-id> è disabilitata.|
|40652|16|Impossibile spostare o creare il server. La sottoscrizione <subscription-id> supera la quota del server.|
|40671|17|Si è verificato un errore di comunicazione tra il gateway e il servizio di gestione. Riprovare più tardi.|
|45168|16|Il sistema SQL Azure è in fase di caricamento e sta fissando un limite superiore per le operazioni simultanee CRUD del database per un singolo server (ad esempio, creare il database). Il server specificato nel messaggio di errore ha superato il numero massimo di connessioni simultanee. Riprovare.|
|45169|16|Il sistema SQL Azure è in fase di caricamento e sta fissando un limite superiore per le operazioni simultanee CRUD del server per una singola sottoscrizione (ad esempio, creare il server). La sottoscrizione specificata nel messaggio di errore ha superato il numero massimo di connessioni simultanee e la richiesta è stata negata. Riprovare.|


## Collegamenti correlati

- [Linee guida e limitazioni generali per il database SQL di Azure](http://msdn.microsoft.com/library/azure/ee336245.aspx)
- [Gestione delle risorse](http://msdn.microsoft.com/library/azure/dn338083.aspx)

<!---HONumber=August15_HO6-->