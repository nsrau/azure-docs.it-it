```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    }
}
```

|Proprietà  |Default | Descrizione |
|---------|---------|---------| 
|routePrefix|api|Il prefisso della route che si applica a tutte le route. Utilizzare una stringa vuota per rimuovere il prefisso predefinito. |
|maxOutstandingRequests|-1|Il numero massimo di richieste in sospeso che verrà mantenuto in un determinato intervallo (-1 indica non associato). Il limite include le richieste che vengono messe in coda ma non hanno avviato l'esecuzione, nonché le esecuzioni in corso. Le richieste in arrivo che superano questo limite vengono rifiutate con la risposta 429 "Occupato". I chiamanti possono utilizzare tale risposta per impiegare strategie di ripetizione basate sul tempo. Questa impostazione controlla solo l’accodamento che si verifica all'interno del percorso di esecuzione dell’host di processo. Altre code, ad esempio la coda di richieste ASP.NET, non sono interessate da questa impostazione. |
|maxConcurrentRequests|-1|Il numero massimo di funzioni HTTP che verrà eseguito in parallelo (-1 indica non associato). Ad esempio, è possibile impostare un limite se le funzioni HTTP utilizzano troppe risorse di sistema quando la concorrenza è elevata. Oppure se le funzioni mandano richieste a un servizio di terze parti, tali chiamate dovrebbero essere a frequenza limitata.|
|dynamicThrottlesEnabled|false|Fa sì che la pipeline di elaborazione delle richieste controlli periodicamente i contatori delle prestazioni del sistema. I contatori includono connessioni, thread, processi, memoria e cpu. Se uno qualsiasi dei contatori si trova oltre una soglia predefinita (80%), le richieste vengono rifiutate con una risposta 429 "Occupato" fino a quando i contatori non tornano a livelli normali.|
