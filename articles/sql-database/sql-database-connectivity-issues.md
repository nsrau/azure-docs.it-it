---
title: Correggere un errore di connessione SQL temporaneo | Documentazione Microsoft
description: Informazioni su come risolvere, diagnosticare ed evitare un errore di connessione SQL o errore temporaneo nel database SQL di Azure.
keywords: "connessione sql,stringa di connessione,problemi di connettività,errore temporaneo,errore di connessione"
services: sql-database
documentationcenter: 
author: dalechen
manager: cshepard
editor: 
ms.assetid: efb35451-3fed-4264-bf86-72b350f67d50
ms.service: sql-database
ms.custom: develop apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/29/2017
ms.author: daleche
ms.openlocfilehash: 7d393cd08ef5c20ef680e4e1ab3aded191abe932
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="troubleshoot-diagnose-and-prevent-sql-connection-errors-and-transient-errors-for-sql-database"></a>Risolvere, diagnosticare ed evitare gli errori di connessione SQL e gli errori temporanei per il database SQL
Questo articolo illustra come evitare, risolvere, diagnosticare e ridurre gli errori di connessione e gli errori temporanei che si verificano nell'applicazione client durante l'interazione con il database SQL di Azure. Informazioni su come configurare la logica di ripetizione dei tentativi, compilare la stringa di connessione e modificare altre impostazioni di connessione.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Errori temporanei
Un errore temporaneo, noto anche come un errore temporaneo, è una causa principale che non appena si risolve. Una causa occasionale di errori temporanei è costituita dal cambio rapido di risorse hardware da parte del sistema Azure per ottenere un bilanciamento migliore dei diversi carichi di lavoro. La maggior parte di questi eventi di riconfigurazione completate in meno di 60 secondi. Durante questo intervallo di riconfigurazione, potrebbe essere problemi di connettività al Database SQL. Applicazioni che si connettono al Database SQL devono essere compilate in modo da prevedere gli errori temporanei. Per gestirli, implementare la logica di ripetizione nel codice anziché le superfici agli utenti come errori dell'applicazione.

Se il programma client utilizza ADO.NET, il programma verrà comunicato sull'errore temporaneo per la generazione di **SqlException**. Confrontare il **numero** proprietà rispetto all'elenco di errori temporanei che si trovano nella parte superiore dell'articolo [codici di errore SQL per le applicazioni client di Database SQL](sql-database-develop-error-messages.md).

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-vs-command"></a>Connessione e comando
Tentativi di connessione SQL o stabilire nuovamente, a seconda di quanto segue:

* **Si verifica un errore temporaneo durante un tentativo di connessione**: dopo un ritardo di qualche secondo, tentativi di connessione.
* **Si verifica un errore temporaneo durante un comando di query SQL**: non è immediatamente ripetere il comando. Invece, dopo un ritardo, appena stabilire la connessione. Ripetere il comando.


<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## <a name="retry-logic-for-transient-errors"></a>Logica di ripetizione dei tentativi per errori temporanei
I programmi client in cui occasionalmente si verifica un errore temporaneo sono più affidabili se contengono una logica di ripetizione dei tentativi.

Quando il programma comunica con il Database SQL tramite il middleware di terze parti, chiedere al fornitore se il middleware contiene la logica di tentativi per errori temporanei.

<a id="principles-for-retry" name="principles-for-retry"></a>

### <a name="principles-for-retry"></a>Principi per la ripetizione dei tentativi
* Se l'errore è temporaneo, Riprova per aprire una connessione.
* Non ripetere direttamente un'istruzione SQL SELECT che non è riuscita con un errore temporaneo.
  * Stabilire invece una nuova connessione e quindi provare a eseguire di nuovo l'istruzione SELECT.
* Quando un'istruzione SQL UPDATE ha esito negativo con un errore temporaneo, stabilire una nuova connessione prima di ritentare l'aggiornamento.
  * La logica di tentativi deve garantire che transazione intero database è stato completato o eseguire il rollback dell'intera transazione.

### <a name="other-considerations-for-retry"></a>Altre considerazioni per la ripetizione dei tentativi
* Un programma batch che viene avviato dopo l'orario di lavoro e termina prima mattina può permettersi di essere molto paziente con intervalli di tempo lunghi tra il numero di tentativi automaticamente.
* Un programma di interfaccia utente deve tenere conto della tendenza degli utenti a desistere dopo un'attesa troppo lunga.
  * La soluzione non deve ripetere ogni pochi secondi, perché tale criterio può riempire il sistema con le richieste.

### <a name="interval-increase-between-retries"></a>Incremento dell'intervallo tra i tentativi
Si consiglia di attendere per 5 secondi prima del primo tentativo. Al primo tentativo con un ritardo inferiore a 5 secondi, si rischia di sovraccaricare il servizio cloud. Per ogni tentativo successivo, il ritardo di aumento delle dimensioni in modo esponenziale, fino a un massimo di 60 secondi.

Per una descrizione del periodo di blocco per i client che usano ADO.NET, vedere [SQL Server pool di connessioni (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

È anche possibile impostare un numero massimo di tentativi prima che il programma Self termina.

### <a name="code-samples-with-retry-logic"></a>Esempi di codice con logica di ripetizione dei tentativi
Esempi di codice di logica di ripetizione dei tentativi sono disponibili in:

- [Connettersi in modo resiliente a SQL con ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
- [Connettersi in modo resiliente a SQL tramite PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

### <a name="test-your-retry-logic"></a>Eseguire test sulla logica di ripetizione tentativi
Per testare la logica di tentativi, è necessario simulare o provocare un errore che può essere corretti, mentre il programma è ancora in esecuzione.

#### <a name="test-by-disconnecting-from-the-network"></a>Eseguire il test mediante la disconnessione dalla rete
Uno dei modi per testare la logica di ripetizione dei tentativi consiste nel disconnettere il computer client dalla rete mentre il programma è in esecuzione. L'errore è:

* **SqlException.Number** = 11001
* Messaggio: "Host sconosciuto"

Come parte del primo tentativo, il programma può correggere l'errore di ortografia e quindi tentare di connettersi.

Per rendere questo test pratici, scollegare il computer dalla rete prima di avviare il programma. Quindi il programma riconosce un parametro runtime che per il programma:

* Aggiunta temporanea di 11001 al rispettivo elenco di errori da considerare temporanei.
* Tentativo della prima connessione come di consueto.
* Dopo il rilevamento dell'errore, rimozione di 11001 dall'elenco.
* Visualizzare un messaggio che informa l'utente di collegare il computer alla rete.
   * Sospensione delle ulteriori esecuzioni con il metodo **Console.ReadLine** o una finestra di dialogo con un pulsante OK. L'utente preme il tasto INVIO dopo che il computer è connesso alla rete.
* Nuovo tentativo di connessione, con esito positivo previsto.

#### <a name="test-by-misspelling-the-database-name-when-connecting"></a>Eseguire il test mediante la digitazione non corretta del nome del database durante la connessione
Il programma può intenzionalmente digitare in modo errato il nome utente prima del primo tentativo di connessione. L'errore è:

* **SqlException.Number** = 18456
* Messaggio: "Accesso non riuscito per l'utente 'WRONG_MyUserName'."

Come parte del primo tentativo, il programma può correggere l'errore di ortografia e quindi tentare di connettersi.

Per rendere questo test pratici, il programma riconosce un parametro runtime che per il programma:

* Aggiunta temporanea di 18456 al rispettivo elenco di errori da considerare temporanei.
* Aggiunta intenzionale di 'WRONG_' al nome utente.
* Dopo il rilevamento dell'errore, rimozione di 18456 dall'elenco.
* Rimozione di 'WRONG_' dal nome utente.
* Nuovo tentativo di connessione, con esito positivo previsto.


<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

## <a name="net-sqlconnection-parameters-for-connection-retry"></a>Parametri di SqlConnection di .NET per nuovi tentativi di connessione
Se il programma client si connette al Database SQL utilizzando la classe di .NET Framework **SqlConnection**, utilizzare .NET 4.6.1 o versioni successive (o .NET Core) in modo che è possibile utilizzare la funzionalità di tentativi di connessione. Per ulteriori informazioni sulla caratteristica, vedere [questa pagina Web](http://go.microsoft.com/fwlink/?linkid=393996).

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->

Quando si compila il [stringa di connessione](http://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) per il **SqlConnection** oggetto, coordinare i valori tra i parametri seguenti:

* **ConnectRetryCount**:&nbsp;&nbsp;valore predefinito è 1. L'intervallo è 0 e 255.
* **ConnectRetryInterval**:&nbsp;&nbsp;il valore predefinito è 1 secondo. Intervallo da 1 a 60.
* **Timeout della connessione**:&nbsp;&nbsp;il valore predefinito è 15 secondi. L'intervallo è 0 e 2147483647.

In particolare, i valori scelti devono rendere vera l'eguaglianza seguente:

Timeout di connessione = ConnectRetryCount * ConnectionRetryInterval

Ad esempio, se il numero è pari a 3 e l'intervallo è uguale a 10 secondi, un timeout di solo 29 secondi non fornisce il sistema di tempo sufficiente per la terza e ultima riprovare a connettersi: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

## <a name="connection-vs-command"></a>Connessione e comando
I parametri **ConnectRetryCount** e **ConnectRetryInterval** consentono all'oggetto **SqlConnection** di ripetere l'operazione di connessione senza interferire con il programma, ad esempio per restituire il controllo al programma. I tentativi possono verificarsi nelle situazioni seguenti:

* Chiamata al metodo mySqlConnection.Open
* Chiamata al metodo mySqlConnection.Execute

È importante sottolineare che, Se si verifica un errore temporaneo durante la *query* è in esecuzione, il **SqlConnection** oggetto non Riprova l'operazione di connessione. Certamente non Riprova la query. Prima di inviare la query per l'esecuzione, tuttavia, **SqlConnection** controlla rapidamente la connessione e, se viene rilevato un problema, **SqlConnection** ritenta l'operazione di connessione. Se il tentativo ha esito positivo, la query viene inviata per l'esecuzione.

### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>Opportunità di combinare ConnectRetryCount con la logica di ripetizione dei tentativi nell'applicazione
Si supponga che l'applicazione disponga di una logica di ripetizione dei tentativi particolarmente avanzata, È possibile ripetere l'operazione di connect quattro volte. Se si aggiunge **ConnectRetryInterval** e **ConnectRetryCount** = 3 alla stringa di connessione, il numero dei tentativi aumenterà a 4 * 3 = 12 tentativi. Un numero così elevato di tentativi potrebbe non essere consigliabile.


<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-sql-database"></a>Connessioni al Database SQL
<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Connessione: stringa di connessione
La stringa di connessione necessarie per connettersi al Database SQL è leggermente diversa rispetto alla stringa utilizzata per connettersi a SQL Server. È possibile copiare la stringa di connessione per il database dal [portale di Azure](https://portal.azure.com/).

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Connessione: indirizzo IP
È necessario configurare il server di database SQL in modo che accetti le comunicazioni dall'indirizzo IP del computer che ospita il programma client. Per impostare questa configurazione, modificare le impostazioni del firewall tramite il [portale di Azure](https://portal.azure.com/).

Se si dimentica di configurare l'indirizzo IP, il programma ha esito negativo con un messaggio di errore utile indicante l'indirizzo IP necessaria.

[!INCLUDE [sql-database-include-ip-address-22-portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]

Per ulteriori informazioni, vedere [configurare le impostazioni del firewall nel Database SQL](sql-database-configure-firewall-settings.md).
<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Connessione: porte
In genere, è necessario assicurarsi che solo la porta 1433 è aperta per le comunicazioni in uscita nel computer che ospita il programma client.

Ad esempio, quando il programma client è ospitato in un computer Windows, è possibile utilizzare Windows Firewall nell'host per aprire la porta 1433.

1. Aprire il pannello di controllo.

2. Selezionare **tutti gli elementi del Pannello di controllo** > **Windows Firewall** > **impostazioni avanzate** > **regole in uscita**   >  **Azioni** > **nuova regola**.

Se il programma client si trova in una macchina virtuale (VM) di Azure, leggere [porte 1433 per il Database di SQL e ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md).

Per informazioni generali sulla configurazione di porte e indirizzi IP, vedere [firewall del Database di SQL Azure](sql-database-firewall-configure.md).

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-461"></a>Connessione: ADO.NET 4.6.1
Se il programma utilizza le classi di ADO.NET come **SqlConnection** per connettersi al Database SQL, è consigliabile utilizzare .NET Framework versione 4.6.1 o versioni successive.

ADO.NET 4.6.1:

* Per il Database SQL, l'affidabilità risulta migliorata quando si apre una connessione utilizzando il **SqlConnection.Open** metodo. Il **aprire** metodo incorpora meccanismi di tentativi di sforzo in risposta a errori temporanei per alcuni errori entro il periodo di timeout di connessione.
* Il pool di connessioni è supportato, che include un'efficiente verifica che l'oggetto di connessione fornisce il programma è in esecuzione.

Quando si utilizza un oggetto connessione da un pool di connessioni, è consigliabile che il programma chiudere temporaneamente la connessione quando non è immediatamente in uso. Non è costoso riaprire una connessione, ma è per creare una nuova connessione.

Se si utilizza ADO.NET 4.0 o versioni precedenti, si consiglia di eseguire l'aggiornamento di ADO.NET più recente. A partire da novembre 2015, è possibile [scaricare ADO.NET 4.6.1](http://blogs.msdn.com/b/dotnet/archive/2015/11/30/net-framework-4-6-1-is-now-available.aspx).

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnostica
<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnostica: verificare se le utilità si possono connettere
Se il programma non riesce a connettersi al Database SQL, un'opzione di diagnostica è tentare di connettersi con un programma di utilità. Idealmente, l'utilità si connette utilizzando la stessa libreria che utilizza il programma.

In qualsiasi computer Windows è possibile provare queste utilità:

* SQL Server Management Studio (ssms.exe), che si connette tramite ADO.NET
* SQLCMD.exe, si connette utilizzando [ODBC](http://msdn.microsoft.com/library/jj730308.aspx)

Dopo avere collegato il programma, verificare se funziona una query SQL SELECT breve.

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnostica: verificare le porte aperte
Se si ritiene che i tentativi di connessione esito negativo a causa di problemi di porta, è possibile eseguire un'utilità che segnala le configurazioni di porte nel computer.

In Linux, le utilità seguenti potrebbero risultare utili:

* `netstat -nap`
* `nmap -sS -O 127.0.0.1`
  * Modificare il valore di esempio per l'indirizzo IP.

In Windows, il [PortQry.exe](http://www.microsoft.com/download/details.aspx?id=17148) può risultare utile. Ecco un'esecuzione di esempio che la situazione porta in un server di Database SQL di query e che è stato eseguito in un computer portatile:

```
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called:
 johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```


<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>Diagnostica: registrare gli errori
La diagnosi di un problema intermittente è spesso agevolata dal rilevamento di uno schema generale nel corso di giorni o settimane.

Il client può supportare l'analisi tramite la registrazione di tutti gli errori rilevati. Potrebbe essere in grado di correlare le voci di log con dati di Database SQL registra internamente stesso errore.

Enterprise Library 6 (EntLib60) offre le classi gestite .NET ai fini della registrazione. Per ulteriori informazioni, vedere [5 - semplice quanto cadere in un log: utilizzare il blocco applicazione per la registrazione](http://msdn.microsoft.com/library/dn440731.aspx).

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnostica: cercare errori nei log di sistema
Ecco alcune istruzioni Transact-SQL SELECT che eseguono query log degli errori e altre informazioni.

| Query di un log | DESCRIZIONE |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |Il [Sys. event_log](http://msdn.microsoft.com/library/dn270018.aspx) Vista offre informazioni sui singoli eventi, che include alcune delle quali può provocare errori temporanei o gli errori di connettività.<br/><br/>Idealmente, è possibile correlare il **start_time** o **end_time** valori con le informazioni quando il programma client si sono verificati problemi.<br/><br/>È necessario connettersi al *master* database per eseguire questa query. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |Il [Sys. database_connection_stats](http://msdn.microsoft.com/library/dn269986.aspx) visualizzazione offre i conteggi aggregati di tipi di eventi per ulteriori operazioni di diagnostica.<br/><br/>È necessario connettersi al *master* database per eseguire questa query. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnostica: cercare eventi relativi a problemi nel log del database SQL
È possibile cercare le voci relative a eventi problema nel log del Database SQL. Provare a eseguire l'istruzione Transact-SQL SELECT seguente nel database *master* :

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


#### <a name="a-few-returned-rows-from-sysfnxetelemetryblobtargetreadfile"></a>Alcune righe restituite da sys.fn_xe_telemetry_blob_target_read_file
Nell'esempio seguente viene illustrato il possibile aspetto una riga restituita. I valori Null mostrati sono spesso non Null in altre righe.

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```


<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Enterprise Library 6
Enterprise Library 6 (EntLib60) è un framework di classi .NET che consente di implementare un client affidabile di servizi cloud, uno dei quali è il servizio di Database SQL. Per individuare gli argomenti dedicati a ogni area in cui è possibile assistere EntLib60, vedere [Enterprise Library 6 - aprile 2013](http://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx).

Logica di riesecuzione per gestire gli errori temporanei è un'area in cui è possibile assistere EntLib60. Per ulteriori informazioni, vedere [4 - Perseverance, segreto del luogo tutti: utilizzare il Transient Fault Handling Application Block](http://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx).

> [!NOTE]
> Il codice sorgente per EntLib60 è disponibile per il download dal [area Download](http://go.microsoft.com/fwlink/p/?LinkID=290898). Microsoft non prevede di fornire altre funzionalità o aggiornamenti di manutenzione per EntLib.
> 
> 

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>Classi di EntLib60 per errori temporanei e ripetizione dei tentativi
Le classi seguenti di EntLib60 sono particolarmente utili per la logica di ripetizione dei tentativi. Tutte queste classi sono disponibili in o nello spazio dei nomi **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**.

Nello spazio dei nomi **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:

* **RetryPolicy**
  
  * **ExecuteAction**
* **ExponentialBackoff**
* **SqlDatabaseTransientErrorDetectionStrategy**
* **ReliableSqlConnection**
  
  * **ExecuteCommand**

Nello spazio dei nomi **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**:

* **AlwaysTransientErrorDetectionStrategy**
* **NeverTransientErrorDetectionStrategy**

Di seguito sono riportati alcuni collegamenti a informazioni EntLib60:

* Download manuale gratuito: [Guida per gli sviluppatori di Microsoft Enterprise Library, 2nd edition](http://www.microsoft.com/download/details.aspx?id=41145).
* Procedure consigliate: [Indicazioni generali per la ripetizione di tentativi](../best-practices-retry-general.md) offre un'eccellente discussione approfondita della logica di ripetizione dei tentativi.
* Download di NuGet: [libreria Enterprise - errore di gestione delle applicazione blocco temporanei versione 6.0](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/).

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: il blocco di registrazione
* Il blocco di registrazione è una soluzione estremamente flessibile e configurabile che consente di:
  
  * Creare e archiviare messaggi di log in diverse posizioni.
  * Classificare e filtrare i messaggi.
  * Raccogliere informazioni contestuali utili per il debug e la traccia, oltre che per i requisiti di controllo e di registrazione generale.
* Il blocco di registrazione estrae la funzionalità di registrazione dalla destinazione di log in modo che il codice dell'applicazione è coerenza, indipendentemente dalla posizione e sul tipo di archivio di registrazione di destinazione.

Per ulteriori informazioni, vedere [5 - semplice quanto cadere in un log: utilizzare il blocco applicazione per la registrazione](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx).

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>Codice sorgente del metodo IsTransient di EntLib60
La classe **SqlDatabaseTransientErrorDetectionStrategy** include anche il codice sorgente C# per il metodo **IsTransient**. Il codice di origine chiarisce gli errori sono stati considerati temporanei e richiedere un nuovo tentativo, a partire da aprile 2013.

```csharp
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


## <a name="next-steps"></a>Passaggi successivi
* Per ulteriori informazioni sulla risoluzione di altri problemi di connessione di Database SQL comuni, vedere [risolvere i problemi di connessione al Database SQL di Azure](sql-database-troubleshoot-common-connection-issues.md).
* [Raccolte di connessioni per database SQL e Server SQL](sql-database-libraries.md)
* [SQL Server pool di connessioni (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)
* [*Nuovo tentativo* è generica della libreria, scritta in Python, nuovo tentativo in corso una licenza di Apache 2.0](https://pypi.python.org/pypi/retrying) per semplificare l'attività di comportamento del nuovo tentativo di aggiunta a qualsiasi tipo.


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php

