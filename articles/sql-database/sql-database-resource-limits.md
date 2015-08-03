<properties 
   pageTitle="Limiti delle risorse del database SQL di Azure"
   description="In questa pagina vengono descritti alcuni limiti di risorse comuni per il Database di SQL Azure."
   services="sql-database"
   documentationCenter="na"
   authors="rothja"
   manager="jeffreyg"
   editor="monicar" />
<tags 
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="07/13/2015"
   ms.author="jroth" />

# Limiti delle risorse del database SQL di Azure

Il Database SQL Azure consente di monitorare l'utilizzo delle risorse condivise, quali il log delle transazioni, I/O e molte altre risorse. In questo modo SQL di Azure mantenere i database nei limiti stabiliti per le risorse. Questo limite o soglia viene chiamato il limite di risorse. Quando l'utilizzo delle risorse dai client supera questi limiti, a un tenant o a livello di nodo fisico, il Database SQL Azure risponde gestendo l'utilizzo delle risorse, con conseguente perdita di connessione o rifiuti di richieste.

> [AZURE.NOTE]Quando i limiti delle risorse impediscono alle query di analizzare i problemi relativi alle prestazioni del database, potrebbe essere necessario utilizzare la connessione amministrativa dedicata (DAC), che è disponibile a partire da V12 Database di SQL Azure. Per ulteriori informazioni sull'utilizzo dell'applicazione livello dati, vedere[connessione di diagnostica per gli amministratori di Database](https://msdn.microsoft.com/library/ms189595.aspx).

## Tabella di riepilogo limiti delle risorse

Nella tabella seguente viene fornito un riepilogo dei limiti per ogni risorsa oltre la quale il Database SQL Azure rifiuta le richieste o termina le connessioni alla risorsa coinvolta e viene restituito un codice di errore. In alcuni casi il livello di servizio (Basic, Standard, Premium) e il livello di prestazioni determina il limite esatto. In questi casi, vedere[livelli di servizio del Database SQL Azure e livelli di prestazioni](https://msdn.microsoft.com/library/azure/dn741336.aspx).

[AZURE.INCLUDE [azure-sql-database-limits](../../includes/azure-sql-database-limits.md)]

## Informazioni sui codici di errore

A volte lo stesso codice di errore viene restituito per più condizioni di limitazione per una risorsa. Tali condizioni sono identificate come indicato nel messaggio di errore. Ad esempio, i seguenti due messaggi di errore vengono visualizzati per la risorsa di lunghezza del Log delle transazioni. Sebbene il testo del messaggio è lo stesso, hanno diversi valori di stato (1 e 2 rispettivamente) in base alle condizioni di limitazione diversi.

Messaggio di errore 1:

	Msg 40552, Level 17, State 1, Line 1
	The session has been terminated because of excessive transaction log space usage.
	Try modifying fewer rows in a single transaction.

Messaggio di errore 2:

	Msg 40552, Level 17, State 2, Line 1
	The session has been terminated because of excessive transaction log space usage.
	Try modifying fewer rows in a single transaction.

La stessa situazione si applica all'ID di risorsa vengono visualizzati alcuni messaggi. L'ID di risorsa viene convertita in una risorsa di sistema in cui si è verificato il limite.

Il resto di questo argomento vengono illustrati i possibili codici di errore in modo più dettagliato, incluse le istanze in cui il valore di stato e l'ID di risorsa fornisce informazioni aggiuntive sull'errore.

## Dimensioni database

| & nbsp; | Ulteriori informazioni |
| :--- | :--- |
| **Condizione** | Quando lo spazio del database allocato al database di un utente è pieno, l'utente riceve un errore di database pieno. |
| **Codice di errore** | **40544**: il database ha raggiunto la quota massima delle dimensioni. Partizionare o eliminare dati, eliminare indici o consultare la documentazione per le possibili soluzioni. |
| **Limite** | Dipende dal [livello di servizio e dal livello di prestazioni](https://msdn.microsoft.com/library/azure/dn741336.aspx). |
| **Tipo di richieste rifiutate** | DML non Selezionato (Inserire, Aggiornare, Inserimenti o Aggiornamenti). |
| **Raccomandazione** | Utilizzare le istruzioni CANCELLA/RIMUOVI per rimuovere i dati dal database finché le dimensioni del database non rientreranno nel limite. |

## Account di accesso

| & nbsp; | Ulteriori informazioni |
| :--- | :--- |
| **Condizione** | Il Database SQL regola il limite dei numeri di accessi simultanei che possono avvenire in un database. Quando viene raggiunto il limite di accessi simultanei di un database, le nuove richieste di accesso al database verranno rifiutate e verrà restituito il codice di errore 10928. |
| **Codice di errore** | **10928**: ID risorsa: 3. Il limite di %s per il database è %d ed è stato raggiunto. Vederehttp://go.microsoft.com/fwlink/?LinkId=267637per assistenza. |
| **Limite** | Dipende dal [livello di servizio e dal livello di prestazioni](https://msdn.microsoft.com/library/azure/dn741336.aspx). |
| **Raccomandazione** | Controllare dm_exec_connections per visualizzare le connessioni utente attualmente attive.<br><br>Interrompere e riprovare l'accesso dopo 10 secondi. |

> [AZURE.NOTE]Il valore di ID di risorsa nel messaggio di errore indica la risorsa per cui è stato raggiunto il limite. Per gli accessi, ID risorsa = 3.

## Utilizzo della memoria

| & nbsp; | Ulteriori informazioni |
| :--- | :--- |
| **Condizione** | Quando sono presenti sessioni in attesa di concessioni di memoria per 20 secondi o più, le sessioni che occupano più di 16 MB di concessione di memoria per più di 20 secondi vengono terminate in ordine decrescente di tempo che sia stata mantenuta la risorsa, in modo che la sessione meno recente venga terminata prima. La chiusura delle sessioni viene arrestata quando diventa disponibile la memoria necessaria. |
| **Codice di errore** | **40553**: la sessione è stata terminata a causa di un utilizzo eccessivo della memoria. Provare a modificare la query per elaborare meno righe. |
| **Limite** | Più di 16 MB di memoria concessi per più di 20 secondi. |
| **Tipo di richieste rifiutate** | Query che utilizzano concessioni di memoria, che includono le query che utilizzano ordinamenti e hash join. |
| **Raccomandazione** | Eseguire l'ottimizzazione delle query per le query che richiedono ordinamenti e/o hash join. |

## Sessioni

| & nbsp; | Ulteriori informazioni |
| :--- | :--- |
| **Condizione** | Il Database SQL regola il limite sul numero di sessioni simultanee che possono essere stabilite su un database. Quando viene raggiunto il limite di sessioni simultanee per un database, verranno rifiutate le nuove richieste di connessioni al database e verrà visualizzato il codice di errore 10928. Tuttavia, le sessioni esistenti del database non verranno terminate. |
| **Codice di errore** | **10928**: ID risorsa: 2. Il limite di %s per il database è %d ed è stato raggiunto. Vederehttp://go.microsoft.com/fwlink/?LinkId=267637per assistenza. |
| **Limite** | Dipende dal [livello di servizio e dal livello di prestazioni](https://msdn.microsoft.com/library/azure/dn741336.aspx). |
| **Raccomandazione** | Controllare dm_exec_requests per visualizzare le richieste utente attualmente in esecuzione. |

> [AZURE.NOTE]Il valore di ID di risorsa nel messaggio di errore indica la risorsa per cui è stato raggiunto il limite. Per le sessioni, ID risorsa = 2.

## Tempdb

| & nbsp; | Ulteriori informazioni |
| :--- | :--- |
| **Condizione** | Le richieste in tempdb possono essere rifiutate per una delle tre condizioni seguenti:<br><br>* * stato 1: * * quando una sessione utilizza più di 5 GB di spazio di tempdb, la sessione viene terminata.<br><br>* * Stato 2: * * le transazioni in tempdb con log di dimensioni superiori a 2 GB vengono troncate. Esempi di operazioni che possono occupare spazio nei log in tempdb: inserire, aggiornare, eliminare, unire, creare indici.<br><br>* * Stato 3: * * le transazioni non salvate in tempdb possono bloccare il troncamento dei file di log. Per evitare questo problema, la distanza tra il numero di sequenza log (LSN) transazione attiva meno recente e la parte finale del log (LSN corrente) nel database tempdb non può superare il 20% delle dimensioni del file di log. Quando viene violata, la transazione problematica in tempdb viene terminata e ripristina di modo che sia possibile troncare il log. |
| **Codice di errore** | **40551**: sessione è stata terminata a causa dell'utilizzo eccessivo di tempdb. Provare a modificare la query per ridurre l'utilizzo dello spazio di tabella temporanea. |
| **Limite** | **Stato 1:**5 GB di spazio di tempdb<br><br>**stato 2:**2 GB per transazione in tempdb<br><br>* * stato 3: * * 20% di spazio totale di log in tempdb |
| **Tipo di richieste rifiutate** | Qualsiasi istruzione DDL o DML in tempdb. |
| **Raccomandazione** | Modificare le query per ridurre l'utilizzo dello spazio di tabella temporaneo, eliminare oggetti temporanei quando essi non sono più necessari, troncare tabelle o rimuovere tabelle inutilizzate. Ridurre le dimensioni dei dati nella transazione in tempdb diminuendo il numero di righe o suddividendo l'operazione in più transazioni. |

## Durata della transazione

| & nbsp; | Ulteriori informazioni |
| :--- | :--- |
| **Condizione** | Le transazioni richiedono blocchi sulle risorse, ad esempio righe, pagine o tabelle, sulle quali dipende la transazione e liberano i blocchi quando non sono più una dipendenza dalle risorse bloccate. Le richieste possono essere rifiutate per una delle due condizioni: Stato 1: se una transazione è stata eseguita per più di 24 ore, viene terminata. Stato 2: Se una transazione blocca una risorsa richiesta da un'attività del sistema sottostante per oltre 20 secondi, viene terminata. |
| **Codice di errore** | **40549**: sessione terminata perché è presente una transazione con esecuzione prolungata. Provare ad abbreviare la transazione. |
| **Limite** | **Stato 1:**24 ore<br><br>* * stato 2: * * 20 secondi se una transazione blocca una risorsa richiesta da un sistema sottostante di attività |
| **Tipo di richieste rifiutate** | Qualsiasi transazione che è stata eseguita per più di 24 ore o qualsiasi istruzione DDL o DML che accetta un blocco, che di conseguenza blocca un’attività di sistema. |
| **Raccomandazione** | Le operazioni nel Database SQL non devono bloccarsi su input dell'utente o avere altre dipendenze che generano transazioni a esecuzione prolungata. |

## Conteggio dei blocchi delle transazioni

| & nbsp; | Ulteriori informazioni |
| :--- | :--- |
| **Condizione** | Le sessioni che utilizzano oltre un milione di blocchi vengono terminate. |
| **Codice di errore** | **40550**: sessione terminata perché ha acquisito troppi blocchi. Provare a leggere o modificare meno righe in una singola transazione. |
| **Limite** | 1 milione di blocchi per transazione |
| **Tipo di richieste rifiutate** | Qualsiasi istruzione DDL o DML. |
| **Raccomandazione** | I Seguenti DMV possono essere utilizzati per monitorare le transazioni:**DM tran_active_transactions****sys.dm_tran_database_transactions****dm_tran_locks**e**DM tran_session_transactions**. A seconda del tipo di applicazione, è possibile utilizzare hint di blocco granularità grossolana, ad esempio**PAGLOCK**o**TABLOCK**per ridurre il numero di blocchi acquisiti in una determinata istruzione/transazione. Si noti che questo può influire negativamente sulle concorrenza di applicazioni. |

## Lunghezza del Log delle transazioni

| & nbsp; | Ulteriori informazioni |
| :--- | :--- |
| **Condizione** | Le richieste potrebbero essere rifiutate per una delle due condizioni seguenti:<br><br>* * stato 1: * * il Database SQL regge transazioni che generano log fino a 2 GB di dimensioni. Le transazioni con log oltre questo limite vengono troncate. Esempi di operazioni che possono occupare spazio nei log con questo volume: inserire, aggiornare, eliminare, unire, creare indici.<br><br>* * Stato 2: * * le transazioni possono impedire il troncamento dei file di log. Per evitare questo problema, la distanza tra il numero di sequenza log (LSN) transazione attiva meno recente e la parte finale del log (LSN corrente) non può superare il 20% delle dimensioni del file di log. Quando viene violata, la transazione problematica viene terminata e viene ripristinata di modo che sia possibile troncare il log. |
| **Codice di errore** | **40552**: La sessione è stata terminata a causa l'utilizzo dello spazio del log delle transazioni in eccesso. Provare a modificare meno righe in una singola transazione. |
| **Limite** | **Stato 1:**2 GB per transazione<br><br>* * stato 2: * * 20% di spazio totale di log |
| **Tipo di richieste rifiutate** | Qualsiasi istruzione DDL o DML. |
| **Raccomandazione** | Per le operazioni di riga, ridurre le dimensioni dei dati della transazione, ad esempio diminuendo il numero di righe o suddividendo l'operazione in più transazioni. Per le operazioni di tabella/indice che richiedono una singola transazione, verificare che venga rispettata la seguente formula: numero di righe interessate nella tabella * (dimensione media del campo aggiornato in byte + 80) < 2 GB (in caso di ricompilazione dell'indice, dimensione media del campo da aggiornare deve essere sostituiti da dimensione media dell'indice). |

## Thread di lavoro (massime richieste simultanee)

| & nbsp; | Ulteriori informazioni |
| :--- | :--- |
| **Condizione** | Il Database SQL regola il limite sul numero di thread di lavoro (richieste simultanee) a un database. A qualsiasi database con oltre il limite di richieste simultanee consentito verrà restituito l'errore 10928 e ulteriori richieste su questo database possono essere rifiutate. |
| **Codici di errore** | **10928**: ID risorsa: 1. Il limite di %s per il database è %d ed è stato raggiunto. Vederehttp://go.microsoft.com/fwlink/?LinkId=267637per assistenza.<br><br>* * 10929 * *: ID risorsa: 1. La %s di garanzia minima è %d, il limite massimo è %d e l'utilizzo corrente per il database è %d. Tuttavia, il server attualmente è troppo occupato per supportare richieste superiori a %d per questo database. Vederehttp://go.microsoft.com/fwlink/?LinkId=267637per assistenza. Altrimenti, riprovare più tardi. |
| **Limite** | Per i livelli Basic, Standard e Premium, dipende dal[livello di prestazioni](https://msdn.microsoft.com/library/azure/dn741336.aspx). Per i database edizione Web/Business precedenti, il limite massimo di richieste simultanee è 180 e può essere inferiore a seconda dell'attività del sistema. |
| **Raccomandazione** | Controllare dm_exec_requests per visualizzare le richieste utente attualmente in esecuzione.<br><br>Ripristinare e ripetere la richiesta dopo 10 secondi. |

> [AZURE.NOTE]Il valore di ID di risorsa nel messaggio di errore indica la risorsa per cui è stato raggiunto il limite. Per i thread di lavoro, ID risorsa = 1.

Errori dovuti alla governance sui thread di lavoro (10928/10929) sostituire l’errore della limitazione del motore originale (40501) per i thread di lavoro. In condizioni normali, gli utenti non devono ricevere errori di limitazione del motore per i thread di lavoro.

In determinati scenari come l'utilizzo della funzionalità di database federati, è possibile premere l'errore di terminazione thread di lavoro (10928) al momento dell'accesso a un database in quanto questa operazione utilizza un thread di lavoro sotto la connessione chiamata aperta. Questo può inserire l'applicazione sopra alla soglia dell’estremità del thread di lavoro Le applicazioni devono includere logica incorporata per gestire questo errore in modo appropriato in questi casi

## Vedere anche

[Gestione delle risorse del database SQL di Azure](https://msdn.microsoft.com/library/azure/dn338083.aspx)

[Messaggi di errore (Database SQL Azure)](https://msdn.microsoft.com/library/azure/ff394106.aspx)

[Database SQL di Azure procedure consigliate per impedire rifiuti di richieste o interruzioni di connessione](https://msdn.microsoft.com/library/azure/dn338082.aspx)

<!---HONumber=July15_HO4-->