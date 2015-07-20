Per supportare le funzionalità offline di Servizi mobili è stata usata l'interfaccia `IMobileServiceSyncTable` ed è stato inizializzato `MobileServiceClient.SyncContext` in un archivio locale. In questo caso l'archivio locale era un database SQLite.

Le normali operazioni CRUD per Servizi mobili funzionano come se l'app fosse ancora connessa, ma tutte le operazioni si verificano nell'archivio locale.

Per sincronizzare l'archivio locale con il server sono stati usati i metodi `IMobileServiceSyncTable.PullAsync` e `MobileServiceClient.SyncContext.PushAsync`.

*  Per eseguire il push delle modifiche al server, è stata effettuata la chiamata a `IMobileServiceSyncContext.PushAsync()`. Questo metodo fa parte di `IMobileServicesSyncContext` invece che della tabella di sincronizzazione perché effettuerà il push delle modifiche in tutte le tabelle.

    Solo i record che sono stati in qualche modo modificati localmente (tramite le operazioni CUD) verranno inviati al server.
   
* Per eseguire il pull dei dati da una tabella del server all'app, è stata effettuata la chiamata a `IMobileServiceSyncTable.PullAsync`.

    Un'operazione pull effettua sempre un'operazione push all'inizio. Lo scopo è assicurare che tutte le tabelle nell'archivio locale e le relazioni restino coerenti.

    Sono inoltre presenti overload di `PullAsync()` che consentono di specificare una query in modo da filtrare i dati archiviati nel client. Se non viene passata una query, `PullAsync()` effettuerà il pull di tutte le righe nella tabella (o query) corrispondente. È possibile passare la query per filtrare solo le modifiche necessarie per la sincronizzazione dell'app.

* Per abilitare la sincronizzazione incrementale, passare un ID di query a `PullAsync()`. L'ID di query viene usato per archiviare il timestamp dell'ultimo aggiornamento dai risultati dell'ultima operazione di pull. L'ID di query deve essere una stringa descrittiva univoca per ogni query logica presente nell'app. Se la query include un parametro, è necessario l'ID di query deve includere lo stesso valore di parametro.

    Ad esempio, se si applica un filtro in base all'ID utente, è necessario che questo faccia parte dell'ID di query:

        await PullAsync("todoItems" + userid, syncTable.Where(u => u.UserId = userid));

    Se si intende rifiutare esplicitamente la sincronizzazione incrementale, passare `null` come ID di query. In questo caso, verranno recuperati tutti i record in ogni chiamata a `PullAsync`, potenzialmente inefficace.

* Per rimuovere i record dall'archivio locale del dispositivo quando sono stati eliminati dal database del servizio mobile, è necessario abilitare l'[eliminazione temporanea]. In alternativa, l'app deve periodicamente chiamare `IMobileServiceSyncTable.PurgeAsync()` per ripulire l'archivio locale.

<!---HONumber=July15_HO2-->