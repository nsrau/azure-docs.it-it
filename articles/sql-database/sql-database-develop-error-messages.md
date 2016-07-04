<properties
	pageTitle="Codici di errore SQL: errore di connessione del database | Microsoft Azure"
	description="Informazioni sui codici di errore SQL per le applicazioni client del database SQL, ad esempio errori di connessione comuni del database, problemi di copia del database ed errori generali."
	keywords="codice di errore sql, accesso sql, errore di connessione del database, codici di errore sql"
	services="sql-database"
	documentationCenter=""
	authors="annemill"
	manager="jhubbard"
	editor="" />


<tags
	ms.service="sql-database"
	ms.workload="drivers"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/22/2016"
	ms.author="annemill"/>


# Codici di errore SQL per le applicazioni client del database SQL: errore di connessione e altri problemi del database


<!--
Old Title on MSDN:  Error Messages (Azure SQL Database)
ShortId on MSDN:  ff394106.aspx
Dx 4cff491e-9359-4454-bd7c-fb72c4c452ca
-->


Questo articolo elenca i codici di errore di SQL per le applicazioni client del database SQL, inclusi errori di connessione del database, errori temporanei (noti anche come guasti temporanei), errori di governance delle risorse, errori di copia del database, errori relativi al pool elastico e altri errori. La maggior parte delle categorie sono specifiche di Database SQL di Azure e non si applicano a Microsoft SQL Server.

Nell'applicazione client per ogni errore specificato è possibile inviare all'utente un messaggio personalizzato.

<a id="bkmk_connection_errors" name="bkmk_connection_errors">&nbsp;</a>


## Errori di connessione del database, guasti e altri errori temporanei

La tabella seguente illustra i codici di errore SQL per errori di perdita della connessione e altri errori temporanei che possono verificarsi quando l'applicazione tenta di accedere al database SQL.


### Errori di connessione del database ed errori temporanei più comuni

L'infrastruttura Azure è in grado di riconfigurare dinamicamente i server quando si verificano carichi di lavoro intensi nel servizio del database SQL. Questo comportamento dinamico potrebbe causare la perdita della connessione del programma client al database SQL. Questo tipo di errore è chiamato *errore temporaneo*.

Se il programma client dispone di logica di ripetizione dei tentativi, è possibile provare a ristabilire una connessione dopo aver dato all’errore temporaneo del tempo per correggere stesso. È consigliabile attendere 5 secondi prima di riprovare. Al primo tentativo con un ritardo inferiore a 5 secondi, si rischia di sovraccaricare il servizio cloud. Per ogni tentativo successivo, aumentare in modo esponenziale il ritardo, fino a un massimo di 60 secondi.

Gli errori temporanei solitamente si manifestano sotto forma di uno dei messaggi di errore seguenti dei programmi client:

- Il database <db_name> nel server <Azure_instance> non è attualmente disponibile. Eseguire un altro tentativo di connessione più tardi. Se il problema persiste, contattare il supporto tecnico indicando l'ID traccia sessione di <session_id>

- Il database <db_name> nel server <Azure_instance> non è attualmente disponibile. Eseguire un altro tentativo di connessione più tardi. Se il problema persiste, contattare il supporto tecnico indicando l'ID traccia sessione di <session_id>. (Microsoft SQL Server, Errore: 40613)

- Connessione in corso interrotta forzatamente dall'host remoto.

- System.Data.Entity.Core.EntityCommandExecutionException: errore durante l'esecuzione della definizione del comando. Per altre informazioni, vedere l'eccezione interna. ---> System.Data.SqlClient.SqlException: si è verificato un errore a livello di trasporto durante la ricezione dei risultati dal server. (provider: provider di sessione, errore: 19 - impossibile usare la connessione fisica)

Per esempi di codice relativi alla logica di ripetizione dei tentativi, vedere:

- [Raccolte di connessioni per database SQL e SQL Server](sql-database-libraries.md)

- [Azioni per la risoluzione di errori di connessione e di errori temporanei nel database SQL](sql-database-connectivity-issues.md)

Per i client che usano ADO.NET, è disponibile una discussione sul *periodo di blocco* in [Pool di connessioni di SQL Server (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

### Codici di errore degli errori temporanei


| Codice di errore | Gravità | Descrizione |
| ---: | ---: | :--- |
| 4060 | 16 | Impossibile aprire il database "%.&#x2a;ls" richiesto dall'account di accesso. Accesso non riuscito. |
|40197|17|Il servizio ha rilevato un errore durante l'elaborazione della richiesta. Riprova più tardi. Codice di errore %d.<br/><br/>Questo errore viene visualizzato quando il servizio non è disponibile a causa di aggiornamenti software o hardware, guasti hardware o altri problemi di failover. Nel codice di errore (%d) incorporato nel messaggio di errore 40197 sono contenute ulteriori informazioni sul tipo di errore o failover che si è verificato. Alcuni esempi dei codici di errore incorporati nel messaggio di errore 40197 sono 40020, 40143, 40166 e 40540.<br/><br/>Con la riconnessione al server di database SQL verrà effettuata la connessione automatica a una copia integra del database. L'applicazione deve rilevare l'errore 40197, registrare il codice di errore incorporato (%d) nel messaggio per la risoluzione dei problemi e tentare la riconnessione al database SQL finché le risorse non saranno disponibili e la connessione non sarà stata ristabilita.|
|40501|20|Il servizio è attualmente occupato. Ripetere la richiesta dopo 10 secondi. ID evento imprevisto: %ls. Codice: %d.<br/><br/>*Nota:* per altre informazioni, vedere:<br/>• [Limiti delle risorse del database SQL di Azure](sql-database-resource-limits.md).
|40613|17|Il database '%.&#x2a;ls' nel server '%.&#x2a;ls' non è attualmente disponibile. Eseguire nuovamente la connessione in un secondo momento. Se il problema persiste, contattare il supporto tecnico indicando l'ID di traccia della sessione di '%.&#x2a;ls'.|
|49918|16|Impossibile elaborare una richiesta. Risorse insufficienti per elaborare la richiesta.<br/><br/>Il servizio è attualmente occupato. Si prega di ripetere la richiesta più tardi. |
|49919|16|Il processo non può creare o aggiornare la richiesta. Troppe operazioni di creazione o aggiornamento in corso per ”%Id” della sottoscrizione. <br/><br/>Il servizio è occupato nell’esecuzione di più richieste di creazione o aggiornamento per la sottoscrizione o il server. Le richieste al momento sono bloccate per l'ottimizzazione delle risorse. Eseguire la query [sys.dm\_operation\_status](https://msdn.microsoft.com/library/dn270022.aspx) per le operazioni in sospeso. Attendere che le richieste di creazione o aggiornamento in sospeso siano complete o cancellare una delle richieste in sospeso e ripetere la richiesta in un secondo momento. |
|49920|16|Impossibile elaborare una richiesta. Troppe operazioni di creazione o aggiornamento in corso per "%ld" della sottoscrizione.<br/><br/>Il servizio è occupato nell'esecuzione di più richieste per la presente sottoscrizione. Le richieste al momento sono bloccate per l'ottimizzazione delle risorse. Eseguire la query [sys.dm\_operation\_status](https://msdn.microsoft.com/library/dn270022.aspx) per lo stato delle operazioni. Attendere che le richieste in sospeso siano complete o cancellare una delle richieste in sospeso e ripetere la richiesta in un secondo momento. |


<a id="bkmk_b_database_copy_errors" name="bkmk_b_database_copy_errors">&nbsp;</a>

## Errori di copia del database


Nella tabella seguente vengono illustrati i diversi errori che è possibile rilevare durante la copia di un database in Database SQL Azure. Per altre informazioni, vedere [Copiare un database SQL di Azure](sql-database-copy.md).


|Codice di errore|Gravità|Descrizione|
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


<a id="bkmk_c_resource_gov_errors" name="bkmk_c_resource_gov_errors">&nbsp;</a>

## Errori di governance delle risorse


Nella tabella seguente vengono illustrati gli errori causati dall'uso eccessivo delle risorse durante l’utilizzo di Database SQL di Azure. ad esempio:


- Forse la transazione è rimasta aperta troppo a lungo.
- Forse la transazione contiene troppi blocchi.
- Forse il programma utilizza troppa memoria.
- Forse il programma usa troppo spazio `TempDb`.


**Suggerimento:** il collegamento seguente offre altre informazioni relative alla maggior parte o a tutti gli errori in questa sezione:


- [Limiti delle risorse del database SQL di Azure](sql-database-resource-limits.md)


|Codice di errore|Gravità|Descrizione|
|---:|---:|:---|
|10928|20|ID risorsa: %d. Il limite di %s per il database è %d ed è stato raggiunto. Per altre informazioni, vedere [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637).<br/><br/>L'ID risorsa indica la risorsa che ha raggiunto il limite. Per i thread di lavoro, l’ID risorsa = 1. Per le sessioni, l'ID risorsa = 2.<br/><br/>*Nota:* per altre informazioni su questo errore e su come risolverlo, vedere:<br/>• [Limiti delle risorse del database SQL di Azure](sql-database-resource-limits.md). |
|10929|20|ID risorsa: %d. La %s di garanzia minima è %d, il limite massimo è %d e l'utilizzo corrente per il database è %d. Tuttavia, il server attualmente è troppo occupato per supportare richieste superiori a %d per questo database. Per altre informazioni, vedere [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637). In caso contrario, riprovare più tardi.<br/><br/>L'ID risorsa indica la risorsa che ha raggiunto il limite. Per i thread di lavoro, l’ID risorsa = 1. Per le sessioni, l'ID risorsa = 2.<br/><br/>*Nota:* per altre informazioni su questo errore e su come risolverlo, vedere:<br/>• [Limiti delle risorse del database SQL di Azure](sql-database-resource-limits.md).|
|40544|20|Il database ha raggiunto la quota delle dimensioni. Partizionare o eliminare dati, eliminare indici o consultare la documentazione per le possibili soluzioni.|
|40549|16|La sessione è stata terminata a causa di una transazione a esecuzione prolungata. Provare ad abbreviare la transazione.|
|40550|16|La sessione è stata terminata perché sono stati acquisiti troppi blocchi. Provare a leggere o modificare meno righe in una singola transazione.|
|40551|16|La sessione è stata terminata a causa dell'utilizzo eccessivo di `TEMPDB`. Provare a modificare la query per ridurre l'uso di spazio della tabella temporanea.<br/><br/>*Suggerimento:* se si usano oggetti temporanei, liberare spazio nel database `TEMPDB` rimuovendo gli oggetti temporanei se non sono più necessari per la sessione.|
|40552|16|La sessione è stata terminata a causa di un utilizzo eccessivo di spazio del log della transazione. Provare a modificare un numero inferiore di righe in una sola transazione.<br/><br/>*Suggerimento:* se si eseguono inserimenti bulk usando l’utilità `bcp.exe` o la classe `System.Data.SqlClient.SqlBulkCopy`, provare a usare le opzioni `-b batchsize` o `BatchSize` per limitare il numero di righe copiate nel server in ogni transazione. In caso di ricompilazione di un indice con l'istruzione `ALTER INDEX`, provare a usare l'opzione `REBUILD WITH ONLINE = ON`.|
|40553|16|La sessione è stata terminata a causa di un utilizzo eccessivo della memoria. Provare a modificare la query per elaborare un numero inferiore di righe.<br/><br/>*Suggerimento:* la riduzione del numero di operazioni `ORDER BY` e `GROUP BY` nel codice Transact-SQL consente di ridurre i requisiti di memoria della query.|


Per altre informazioni sulla governance delle risorse e gli errori correlati, vedere:


- [Limiti delle risorse del database SQL di Azure](sql-database-resource-limits.md)


<a id="bkmk_e_general_errors" name="bkmk_e_general_errors">&nbsp;</a>

## Errori relativi al pool elastico

Argomenti correlati:

* [Creare un pool di database elastici (C#)](sql-database-elastic-pool-create-csharp.md) 
* [Gestire un pool di database elastici (C#)](sql-database-elastic-pool-manage-csharp.md) 
* [Creare un pool di database elastici (PowerShell)](sql-database-elastic-pool-create-powershell.md) 
* [Monitorare e gestire un pool di database elastici (PowerShell)](sql-database-elastic-pool-manage-powershell.md).

| ErrorNumber | ErrorSeverity | ErrorFormat | ErrorInserts | ErrorCause | ErrorCorrectiveAction |
| :-- | :-- | :-- | :-- | :-- | :-- |
| 1132 | EX\_RESOURCE | Il pool elastico ha raggiunto il limite di archiviazione. L'utilizzo dell'archiviazione per il pool elastico non può superare (%d) MB. | Limite di spazio del pool elastico in MB. | Tentativo di scrittura dei dati in un database quando viene raggiunto il limite di archiviazione del pool elastico. | Prendere in considerazione l’aumento delle DTU del pool elastico, se possibile, per aumentare il limite di archiviazione, ridurre la memoria utilizzata dai singoli database all'interno del pool elastico o rimuovere i database dal pool elastico. |
| 10929 | EX\_USER | La %s di garanzia minima è %d, il limite massimo è %d e l'utilizzo corrente per il database è %d. Tuttavia, il server attualmente è troppo occupato per supportare richieste superiori a %d per questo database. Per assistenza, vedere [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637). Altrimenti, riprovare più tardi. | Numero minimo DTU per database. Numero massimo DTU per database | Il numero totale dei processi di lavoro simultanei (richieste) in tutti i database nel pool elastico ha tentato di superare il limite del pool. | Prendere in considerazione l’aumento delle DTU del pool elastico, se possibile, per aumentare il limite dei processi di lavoro o rimuovere i database dal pool elastico. |
| 40844 | EX\_USER | Il database '%ls' sul Server '%ls' è un database versione '%ls' in un pool elastico e non può avere una relazione di copia continua. | nome database, versione database, nome server | Viene emesso un comando StartDatabaseCopy per un database non premium in un pool elastico. | Presto disponibile |
| 40857 | EX\_USER | Pool elastico non trovato per il server: '%ls', nome del pool elastico: '%ls'. | nome del server; nome del pool elastico | Il pool elastico specificato non esiste nel server specificato. | Fornire un nome pool elastico valido. |
| 40858 | EX\_USER | Il pool elastico '%ls' esiste già nel server: '%ls' | nome del pool elastico, nome del server | Il pool elastico specificato esiste già nel server logico specificato. | Fornire un nuovo nome pool elastico. |
| 40859 | EX\_USER | Il pool elastico non supporta il livello di servizio '%ls'. | livello di servizio del pool elastico | Il livello di servizio specificato non è supportato per il provisioning del pool elastico. | Fornire l'edizione corretta oppure lasciare vuoto il livello di servizio per utilizzare il livello di servizio predefinito. |
| 40860 | EX\_USER | La combinazione di pool elastico '%ls' e di obiettivo di servizio '%ls' non è valida. | nome pool elastico; nome obiettivo del livello di servizio | Il pool elastico e l’obiettivo del servizio possono essere specificati insieme solo se l’obiettivo di servizio viene specificato come 'ElasticPool'. | Specificare la combinazione corretta di pool elastico e obiettivo di servizio. |
| 40861 | EX\_USER | L'edizione del database '%.*ls' non può essere diversa dal livello di servizio del pool elastico che è '%.*ls'. | edizione del database, livello di servizio del pool elastico | L'edizione del database è diversa dal livello di servizio del pool elastico. | Non specificare un'edizione del database diversa dal livello di servizio del pool elastico. Si noti che non è necessario specificare l'edizione del database. | 
| 40862 | EX\_USER | Nome del pool elastico deve essere specificato quando viene specificato l'obiettivo del servizio del pool elastico. | Nessuno | L’obiettivo del servizio del pool elastico non identifica in modo univoco un pool elastico. | Specificare il nome del pool elastico se si utilizza l'obiettivo del servizio del pool elastico. | 
| 40864 | EX\_USER | Il numero di DTU per il pool elastico deve essere almeno (%d) DTU per il livello di servizio '%.*ls'. | DTU per il pool elastico; livello di servizio del pool elastico. | Tentativo di impostazione di un numero di DTU per il pool elastico inferiore al limite minimo. | Riprovare impostando le DTU per il pool elastico almeno al limite minimo. | 
| 40865 | EX\_USER | Le DTU per il pool elastico non possono superare (%d) DTU per il livello di servizio '%.*ls'. | DTU per il pool elastico; livello di servizio del pool elastico. | Tentativo di impostazione di un numero di DTU per il pool elastico superiore al limite massimo. | Riprovare impostando un numero di DTU per il pool elastico non superiore al limite massimo. | 
| 40867 | EX\_USER | Il numero massimo di DTU per ogni database deve essere di almeno (%d) per il livello di servizio '%.*ls'. | Numero massimo di DTU per database; livello di servizio del pool elastico | Tentativo di impostare un numero massimo di DTU per ogni database sotto il limite supportato. | È consigliabile utilizzare il livello di servizio del pool elastico che supporta l'impostazione desiderata. | 
| 40868 | EX\_USER | Il numero massimo di DTU per ogni database non può superare (%d) per il livello di servizio '%.*ls'. | Numero massimo di DTU per database; livello di servizio del pool elastico. | Tentativo di impostare un numero massimo di DTU per ogni database oltre il limite supportato. | È consigliabile utilizzare il livello di servizio del pool elastico che supporta l'impostazione desiderata. | 
| 40870 | EX\_USER | Il numero minimo di DTU per ogni database non può superare (%d) per il livello di servizio '%.*ls'. | Numero minimo di DTU per database; livello di servizio del pool elastico. | Tentativo di impostare un numero minimo di DTU per ogni database oltre il limite supportato. | È consigliabile utilizzare il livello di servizio del pool elastico che supporta l'impostazione desiderata. | 
| 40873 | EX\_USER | Il numero di database (%d) e DTU min per ogni database (%d) non può superare le DTU del pool elastico (%d). | Numero di database nel pool elastico; DTU minime per database; DTU del pool elastico. | Tentativo di specificare un numero minimo di DTU per i database nel pool elastico che supera le DTU del pool elastico. | Provare ad aumentare le DTU del pool elastico o a diminuire il numero minimo di DTU per ogni database oppure ridurre il numero di database nel pool elastico. | 
| 40877 | EX\_USER | Impossibile eliminare un pool elastico, a meno che non contenga alcun database. | Nessuno | Il pool elastico contiene uno o più database e pertanto non può essere eliminato. | Rimuovere i database dal pool elastico per eliminarlo. | 
| 40881 | EX\_USER | Il pool elastico '%.* ls' ha raggiunto il numero massimo di database. Il numero massimo di database per il pool elastico non può superare (%d) per un pool elastico con DTU (%d). | Nome del pool elastico; numero massimo di database di pool elastico; DTU per pool di risorse. | Tentativo di creare o aggiungere database al pool elastico quando viene raggiunto il numero massimo di database nel pool elastico. | Provare ad aumentare le DTU del pool elastico se possibile, per aumentare il limite di database o rimuovere i database dal pool elastico. | 
| 40889 | EX\_USER | Il limite di DTU o risorse di archiviazione per il pool elastico '%.*ls' non può essere ridotto dal momento che non si fornisce spazio di archiviazione sufficiente per i database. | Nome del pool elastico. | Tentativo di ridurre il limite di archiviazione del pool elastico sotto il relativo utilizzo dell'archiviazione. | Provare a ridurre l'utilizzo dell'archiviazione dei singoli database nel pool elastico o rimuovere i database dal pool per ridurre le DTU o il limite di archiviazione. | 
| 40891 | EX\_USER | Il numero minimo di DTU per ogni database (%d) non può superare il numero massimo di DTU per ogni database (%d). | Numero minimo di DTU per database; numero massimo di DTU per database. | Tentativo di impostare un numero minimo di DTU per ogni database superiore al numero massimo di DTU per ogni database. | Verificare che il numero minimo di DTU per database non superi il numero massimo di DTU per database. | 
| TBD | EX\_USER | Le dimensioni di archiviazione di un singolo database in un pool elastico non possono superare la dimensione massima consentita per il livello di servizio del pool elastico '%.* ls'. | Livello di servizio del pool elastico | La dimensione massima per il database supera la dimensione massima consentita per il livello di servizio del pool elastico. | Impostare la dimensione massima del database entro i limiti della dimensione massima consentita per il livello di servizio del pool elastico. |


## Errori generali


La tabella seguente elenca tutti gli errori generali che non rientrano nelle categorie precedenti.


|Codice di errore|Gravità|Descrizione|
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
|40638|16|ID sottoscrizione <subscription-id> non valido. La sottoscrizione non esiste.|
|40639|16|Richiesta non conforme allo schema: <schema error>.|
|40640|20|Eccezione imprevista rilevata dal server.|
|40641|16|Il percorso specificato non è valido.|
|40642|17|Server attualmente troppo occupato. Riprovare più tardi.|
|40643|16|Valore dell'intestazione x-ms-version specificato non valido.|
|40644|14|Impossibile autorizzare l'accesso alla sottoscrizione specificata.|
|40645|16|Il nome del server <servername> non può essere vuoto o null. Può essere costituito solo da lettere in minuscolo comprese tra a e z, numeri da 0 a 9 e dal segno meno (-) che non può essere la parte iniziale o finale del nome|
|40646|16|L’ID sottoscrizione non può essere vuoto.|
|40647|16|Server nomeserver non disponibile per la sottoscrizione < id-sottoscrizione.|
|40648|17|Esecuzione di un numero eccessivo di richieste. Riprovare più tardi.|
|40649|16|È stato specificato un tipo di contenuto non valido. È supportato solo il codice XML o l’applicazione.|
|40650|16|La sottoscrizione <subscription-id> non esiste o non è pronta per l'operazione.|
|40651|16|Impossibile creare il server perché la sottoscrizione <subscription-id> è disabilitata.|
|40652|16|Impossibile spostare o creare il server. La sottoscrizione <subscription-id> supera la quota del server.|
|40671|17|Si è verificato un errore di comunicazione tra il gateway e il servizio di gestione. Riprovare più tardi.|
|40852|16|Impossibile aprire il database "%.*ls" nel server "%.*ls" richiesto dall'account di accesso. L'accesso al database è consentito solo tramite una stringa di connessione con sicurezza abilitata. Per accedere al database, modificare le stringhe di connessione in modo che contengano "sicuro" nel FQDN del server - "nome server".database.windows.net deve essere modificato in "nome server".database.`secure`.windows.net.| 
|45168|16|Il sistema SQL Azure è in fase di caricamento e sta fissando un limite superiore per le operazioni simultanee CRUD del database per un singolo server (ad esempio, creare il database). Il server specificato nel messaggio di errore ha superato il numero massimo di connessioni simultanee. Riprovare più tardi.| 
|45169|16|Il sistema SQL Azure è in fase di caricamento e sta fissando un limite superiore per le operazioni simultanee CRUD del server per una singola sottoscrizione (ad esempio, creare il server). La sottoscrizione specificata nel messaggio di errore ha superato il numero massimo di connessioni simultanee e la richiesta è stata negata. Riprovare più tardi.|


## Collegamenti correlati

- [Limitazioni e linee guida generali per il database SQL di Azure](sql-database-general-limitations.md)
- [Limiti delle risorse del database SQL di Azure](sql-database-resource-limits.md)

<!---HONumber=AcomDC_0622_2016-->