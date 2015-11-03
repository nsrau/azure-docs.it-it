<properties
	pageTitle="Azioni per la risoluzione della perdita temporanea della connessione | Microsoft Azure"
	description="Azioni per evitare, diagnosticare e risolvere gli errori di connessione e altri errori temporanei durante l'interazione con il database SQL di Azure."
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
	ms.topic="get-started-article"
	ms.date="10/26/2015"
	ms.author="genemi"/>


# Azioni per la risoluzione di errori di connessione e di errori temporanei nel database SQL


Questo argomento illustra come evitare, diagnosticare e ridurre gli errori di connessione e gli errori temporanei che si verificano nel programma client durante l'interazione con il database SQL di Azure.


<a id="i-transient-faults" name="i-transient-faults"></a>

## Errori temporanei


Un errore temporaneo è un errore la cui causa sottostante si risolverà automaticamente in modo rapido. Una causa occasionale di errori temporanei è costituita dal cambio rapido di risorse hardware da parte del sistema Azure per ottenere un bilanciamento migliore dei diversi carichi di lavoro. Durante questo intervallo di riconfigurazione è possibile che le connessioni al database SQL di Azure vadano perse.


Se il programma client usa ADO.NET, l'errore temporaneo verrà segnalato al programma tramite la generazione di un'eccezione **SqlException**. È possibile confrontare la proprietà **Number** con l'elenco di errori temporanei disponibili nella parte iniziale dell'argomento [Messaggi di errore per programmi client del database SQL](sql-database-develop-error-messages).


### Confronto tra connessione e comando


Quando si verifica un errore temporaneo durante un tentativo di connessione, è consigliabile provare a connettersi di nuovo dopo alcuni secondi.


Quando si verifica un errore temporaneo durante un comando di query SQL, è consigliabile non provare immediatamente a rieseguire il comando. È invece consigliabile stabilire una nuova connessione dopo un breve intervallo di tempo. Sarà quindi possibile provare a rieseguire il comando.


<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## Logica di ripetizione dei tentativi per errori temporanei


I programmi client che rilevano occasionalmente un errore temporaneo sono più affidabili se contengono una logica di ripetizione dei tentativi.


Quando il programma comunica con il database SQL di Azure tramite middleware di terze parti, chiedere al fornitore se il middleware contiene logica di ripetizione dei tentativi per errori temporanei.


### Principi per la ripetizione dei tentativi


- È consigliabile ripetere un tentativo di apertura di una connessione se l'errore è temporaneo.


- Non devono essere eseguiti direttamente nuovi tentativi di esecuzione di un'istruzione SQL SELECT non riuscita con un errore temporaneo.
 - Stabilire invece una nuova connessione e quindi provare a eseguire di nuovo l'istruzione SELECT.


- Quando un'istruzione SQL UPDATE non riesce con un errore temporaneo, è consigliabile stabilire una nuova connessione prima di provare a eseguire di nuovo l'istruzione UPDATE.
 - La logica di ripetizione dei tentativi deve assicurare il completamento dell'intera transazione di database o il rollback dell'intera transazione.


#### Altre considerazioni per la ripetizione dei tentativi


- Un programma batch avviato automaticamente dopo l'orario di lavoro e con completamento previsto prima del mattino può permettersi di attendere a lungo tra i diversi tentativi.


- Un programma di interfaccia utente deve tenere conto della tendenza degli utenti a desistere dopo un'attesa troppo lunga.
 - La soluzione, tuttavia, non deve prevedere nuovi tentativi con intervalli di pochi secondi, perché un criterio simile può inondare il sistema con un numero eccessivo di richieste.


### Incremento dell'intervallo tra i tentativi


Il programma deve attendere sempre almeno 6-10 secondi prima del primo tentativo. In caso contrario, è possibile che il servizio cloud sia inondato di richieste che non riesce a elaborare.


Se sono necessari più tentativi, è necessario incrementare l'intervallo prima di ogni tentativo successivo, fino a un valore massimo specificato. Sono disponibili queste due strategie alternative:


- Incremento progressivo dell'intervallo. Ad esempio, è possibile aggiungere 5 secondi a ogni intervallo successivo.


- Incremento esponenziale dell'intervallo. Ad esempio, è possibile moltiplicare ogni intervallo successivo per 1,5.


È anche possibile che si voglia impostare un numero massimo di nuovi tentativi prima dell'autoterminazione del programma.


### Esempi di codice con logica di ripetizione dei tentativi


Esempi di codice con logica di ripetizione dei tentativi in diversi linguaggi di programmazione sono disponibili in:

- [Esempi di codice di avvio rapido](sql-database-develop-quick-start-client-code-samples.md) 


<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

## Eseguire test sulla logica di ripetizione tentativi


Per testare la logica di ripetizione dei tentativi, è necessario simulare o provocare un errore che può essere corretto mentre il programma è ancora in esecuzione.


### Eseguire il test mediante la disconnessione dalla rete


Uno dei modi per testare la logica di ripetizione dei tentativi consiste nel disconnettere il computer client dalla rete mentre il programma è in esecuzione. Verrà visualizzato un errore analogo al seguente: - **SqlException.Number** = 11001 - Messaggio: "Host sconosciuto".


Come parte del primo tentativo, il programma può correggere l'errore di digitazione e quindi provare a connettersi.


Per semplificare le operazioni, disconnettere il computer dalla rete prima di avviare il programma. Il programma riconoscerà quindi un parametro di runtime che ha le conseguenze seguenti sul programma: 1. Aggiunta temporanea di 11001 al rispettivo elenco di errori da considerare temporanei. 2. Tentativo della prima connessione come di consueto. 3. Dopo il rilevamento dell'errore, rimozione di 11001 dall'elenco. 4. Visualizzazione di un messaggio che richiede all'utente di connettere il computer alla rete. - Sospensione di ulteriori esecuzioni mediante il metodo **Console.ReadLine** o una finestra di dialogo con un pulsante OK. L'utente preme il tasto INVIO dopo la connessione del computer alla rete. 5. Nuovo tentativo di connessione, con esito positivo previsto.


### Eseguire il test mediante la digitazione non corretta del nome del database durante la connessione


Il programma può intenzionalmente digitare in modo errato il nome utente prima del primo tentativo di connessione. L'errore sarà analogo al seguente: - **SqlException.Number** = 18456 - Messaggio: "Accesso non riuscito per l'utente 'WRONG\_MyUserName'."


Come parte del primo tentativo, il programma può correggere l'errore di digitazione e quindi provare a connettersi.


Per semplificare le operazioni, il programma potrebbe riconoscere un parametro di runtime che ha le conseguenze seguenti sul programma: 1. Aggiunta temporanea di 18456 al rispettivo elenco di errori da considerare temporanei. 2. Aggiunta intenzionale di 'WRONG\_' al nome utente. 3. Dopo il rilevamento dell'errore, rimozione di 18456 dall'elenco. 4. Rimozione di 'WRONG\_' dal nome utente. 5. Nuovo tentativo di connessione, con esito positivo previsto.


<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## Connessione: stringa di connessione


La stringa di connessione necessaria per la connessione al database SQL di Azure è leggermente diversa dalla stringa usata per la connessione a Microsoft SQL Server. È possibile copiare la stringa di connessione per il database dal [portale di anteprima di Azure](http://portal.azure.com/).


[AZURE.INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]



#### 30 secondi per il timeout della connessione


La connessione tramite Internet è meno affidabile rispetto a una connessione tramite rete privata. È quindi consigliabile impostare il parametro **Timeout connessione** nella stringa di connessione su **30** secondi, invece di 15 secondi.


<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

## Connessione: indirizzo IP


È necessario configurare il server di database SQL in modo che accetti le comunicazioni dall'indirizzo IP del computer che ospita il programma client. Per eseguire questa operazione, modificare le impostazioni del firewall tramite il [portale di anteprima di Azure](http://portal.azure.com/).


Se si dimentica di configurare l'indirizzo IP, il programma restituirà un messaggio di errore che indica l'indirizzo IP necessario.


[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]


Per altre informazioni, vedere [Procedura: Configurare le impostazioni del firewall nel database SQL](sql-database-configure-firewall-settings.md).


<a id="c-connection-ports" name="c-connection-ports"></a>

## Connessione: porte


È in genere sufficiente assicurarsi che la porta 1433 sia aperta per le comunicazioni in uscita nel computer che ospita il programma client.


Ad esempio, se il programma client è ospitato in un computer Windows, Windows Firewall nell'host consente di aprire la porta 1433:


1. Aprire il Pannello di controllo
2. &gt; Tutti gli elementi del Pannello di controllo
3. &gt; Windows Firewall
4. &gt; Impostazioni avanzate
5. &gt; Regole in uscita
6. &gt; Azioni
7. &gt; Nuova regola


Se il programma client è ospitato in una macchina virtuale (VM) di Azure, vedere <br/>[Porte superiori alla 1433 per ADO.NET 4.5 e il database SQL V12](sql-database-develop-direct-route-ports-adonet-v12.md).


Per informazioni generali sulla configurazione di porte e indirizzi IP, vedere [Firewall del database SQL di Azure](sql-database-firewall-configure.md)


<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

## Connessione: ADO.NET 4.5


Se il programma usa classi ADO.NET come **System.Data.SqlClient.SqlConnection** per la connessione al database SQL di Azure SQL, è consigliabile usare .NET Framework versione 4.5 o versione successiva.


ADO.NET 4.5: - Aggiunge il supporto per il protocollo TDS 7.4, inclusi miglioramenti alla connessione superiori rispetto a quelli della versione 4.0. - Supporta i pool di connessioni, inclusa una verifica efficiente del funzionamento dell'oggetto connessione fornito al programma.


Quando si usa un oggetto connessione da un pool di connessioni, è consigliabile che il programma chiuda temporaneamente la connessione se non deve essere usata immediatamente. La riapertura di una connessione è meno dispendiosa della creazione di una nuova connessione.


Se si usa ADO.NET 4.0 o versioni precedenti, è consigliabile eseguire l'aggiornamento alla versione più recente di ADO.NET. - A partire da luglio 2015 è possibile [scaricare ADO.NET 4.6](http://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-net-framework-4-6.aspx).


<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## Diagnostica: verificare se le utilità si possono connettere


Se il programma non riesce a connettersi al database SQL di Azure, un'opzione di diagnostica consente di provare a connettersi mediante un programma di utilità. Idealmente l'utilità si connette mediante la stessa libreria usata dal programma.


In qualsiasi computer Windows è possibile provare a usare queste utilità: - SQL Server Management Studio (ssms.exe), che si connette mediante ADO.NET. - sqlcmd.exe, che si connette mediante [ODBC](http://msdn.microsoft.com/library/jj730308.aspx).


Dopo la connessione, verificare il funzionamento di una breve query SQL SELECT.


<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

## Diagnostica: verificare le porte aperte


Si supponga che si sospetti che gli errori di connessione siano dovuti a problemi relativi alle porte. Nel computer è possibile eseguire un'utilità che fornisce informazioni sulle configurazioni delle porte.


In Linux potrebbero essere utili le utilità seguenti: - `netstat -nap` - `nmap -sS -O 127.0.0.1` - Sostituire il valore dell'esempio con l'indirizzo IP in uso.


In Windows è possibile usare l'utilità [PortQry.exe](http://www.microsoft.com/download/details.aspx?id=17148). Ecco un'esecuzione di esempio che ha eseguito una query relativa alla situazione delle porte in un server di database SQL di Azure e che è stata eseguita in un computer portatile:


```
[C:\Users\johndoe]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called:
 johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe]
>>
```


<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

## Diagnostica: registrare gli errori


La diagnosi di un problema intermittente è spesso agevolata dal rilevamento di uno schema generale nel corso di giorni o settimane.


Il client può supportare l'analisi tramite la registrazione di tutti gli errori rilevati. È possibile che si riesca a correlare le voci del log con i dati di errore registrati internamente dal database SQL di Azure.


Enterprise Library 6 (EntLib60) offre classi .NET gestite per semplificare la registrazione: - [5 - Più facile che mai: uso del blocco di applicazioni di registrazione](http://msdn.microsoft.com/library/dn440731.aspx)


<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

## Diagnostica: cercare errori nei log di sistema


Ecco alcune istruzioni Transact-SQL SELECT che eseguono query nei log alla ricerca di errori e di altre informazioni.


| Query di un log | Descrizione |
| :-- | :-- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` | La visualizzazione [sys.event\_log](http://msdn.microsoft.com/library/dn270018.aspx) offre informazioni sui singoli eventi, inclusi gli errori di connettività correlati alla riconfigurazione, alla limitazione e all'accumulo eccessivo di risorse.<br/><br/>In teoria è possibile correlare i valori **start\_time** o **end\_time** con le informazioni relative al momento in cui si sono verificati problemi nel programma client.<br/><br/>**SUGGERIMENTO:** è necessario connettersi al database **master** per eseguire questa operazione. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` | La visualizzazione [sys.database\_connection\_stats](http://msdn.microsoft.com/library/dn269986.aspx) offre un conteggio aggregato dei tipi di evento, per consentire operazioni di diagnostica aggiuntive.<br/><br/>**SUGGERIMENTO:** è necessario connettersi al database **master** per eseguire questa operazione. |


### Diagnostica: cercare eventi relativi a problemi nel log del database SQL


È possibile cercare voci relative agli eventi problematici nel log del database SQL di Azure. Provare a eseguire l'istruzione Transact-SQL SELECT seguente nel database **master**:


```
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```


#### Alcune righe restituite da sys.fn\_xe\_telemetry\_blob\_target\_read\_file


Una riga restituita avrà un aspetto analogo al seguente. I valori Null mostrati sono spesso non Null in altre righe.


```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```


<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## Enterprise Library 6


Enterprise Library 6 (EntLib60) è un framework di classi .NET che semplifica l'implementazione di client affidabili dei servizi cloud, ad esempio il servizio database SQL di Azure SQL. Gli argomenti dedicati a ogni area per cui EntLib60 può risultare utile sono disponibili in: - [Enterprise Library 6 - Aprile 2013](http://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx)


La logica di ripetizione dei tentativi per la gestione di errori temporanei è una delle aree per cui EntLib60 può risultare utile: - [4 - Perseveranza, il segreto di ogni successo: uso del blocco applicazioni di gestione degli errori temporanei](http://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx)


Un breve esempio di codice C# che usa EntLib60 nella logica di ripetizione dei tentativi è disponibile in: - [Codice di esempio: logica di ripetizione tentativi di Enterprise Library 6 in C# per la connessione al database SQL](sql-database-develop-entlib-csharp-retry-windows.md)


> [AZURE.NOTE]Il codice sorgente per EntLib60 è disponibile per il [download](http://go.microsoft.com/fwlink/p/?LinkID=290898) pubblico. Microsoft non prevede di fornire altre funzionalità o aggiornamenti di manutenzione per EntLib.


### Classi di EntLib60 per errori temporanei e ripetizione dei tentativi


Le classi seguenti di EntLib60 sono particolarmente utili per la logica di ripetizione dei tentativi. Tutte queste classi sono disponibili nello spazio dei nomi **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling** o nei livelli sottostanti:

*Nello spazio dei nomi **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:*

- Classe **RetryPolicy**
 - Metodo **ExecuteAction**


- Classe **ExponentialBackoff**


- Classe **SqlDatabaseTransientErrorDetectionStrategy**


- Classe **ReliableSqlConnection**
 - Metodo **ExecuteCommand**


Nello spazio dei nomi **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**:

- Classe **AlwaysTransientErrorDetectionStrategy**

- Classe **NeverTransientErrorDetectionStrategy**


Ecco i collegamenti alle informazioni relative a EntLib60:

- [Download gratuito dell'eBook relativo alla Guida per gli sviluppatori di Microsoft Enterprise Library, seconda edizione](http://www.microsoft.com/download/details.aspx?id=41145)

- Procedure consigliate: [Indicazioni generali per la ripetizione di tentativi](best-practices-retry-general.md) offre un'eccellente discussione approfondita della logica di ripetizione dei tentativi.

- Download NuGet del [blocco applicazioni per la gestione di errori temporanei in Enterprise Library 6.0](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/)


### EntLib60: il blocco di registrazione


- Il blocco di registrazione è una soluzione a flessibilità e configurabilità elevata che consente di:
 - Creare e archiviare messaggi di log in diverse posizioni.
 - Classificare e filtrare i messaggi.
 - Raccogliere informazioni contestuali utili per il debug e la traccia, oltre che per i requisiti di controllo e di registrazione generale.


- Il blocco di registrazione astrae la funzionalità di registrazione dalla destinazione di registrazione, in modo che il codice applicazione sia coerente, indipendentemente dalla posizione e dal tipo di archivio di registrazione di destinazione.


Per informazioni dettagliate vedere [5 - Più facile che mai: uso del blocco di applicazioni di registrazione](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx)


### Codice sorgente del metodo IsTransient di EntLib60


La classe **SqlDatabaseTransientErrorDetectionStrategy** include anche il codice sorgente C# per il metodo **IsTransient**. Il codice sorgente chiarisce gli errori considerati temporanei e idonei alla ripetizione dei tentativi, a partire da aprile 2013.

Molte righe **//comment** sono state rimosse da questa copia per migliorarne la leggibilità.


```
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```


## Altre informazioni


- [Pool di connessioni di SQL Server (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx)


- [*Retrying* è una libreria generica Apache 2.0 di ripetizione dei tentativi scritta in **Python** per semplificare l'attività di aggiunta del comportamento di ripetizione dei tentativi a qualsiasi codice.](https://pypi.python.org/pypi/retrying)

<!---HONumber=Nov15_HO1-->