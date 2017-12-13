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
|maxOutstandingRequests|-1|Il numero massimo di richieste in sospeso che verrà mantenuto in un determinato intervallo. Questo limite include le richieste che vengono messe in coda ma non hanno avviato l'esecuzione, nonché le esecuzioni in corso. Le richieste in arrivo che superano questo limite vengono rifiutate con la risposta 429 "Occupato". Ciò consente ai chiamanti di usare strategie di ripetizione dei tentativi basate sul tempo e di controllare la latenza massima delle richieste. Questa impostazione controlla solo l'accodamento che si verifica all'interno del percorso di esecuzione dell'host dello script. Altre code, ad esempio la coda di richieste ASP.NET, saranno valide e non interessate da questa impostazione. Il valore predefinito è unbounded.|
|maxConcurrentRequests|-1|Il numero massimo di funzioni HTTP che verrà eseguito in parallelo. Ciò consente di controllare la concorrenza e pertanto di semplificare la gestione dell'uso delle risorse. Ad esempio, potrebbe essere presente una funzione HTTP che usa una quantità di risorse di sistema (memoria/CPU/socket) tale da creare problemi quando la concorrenza è troppo elevata. Oppure potrebbe essere presente una funzione che invia richieste a un servizio di terze parti e tali chiamate devono essere a frequenza limitata. In questi casi potrebbe risultare utile l'applicazione di una limitazione. Il valore predefinito è unbounded.|
|dynamicThrottlesEnabled|false|Quando è abilitata, questa impostazione determina la pipeline di elaborazione della richiesta per il controllo periodico delle prestazioni dei contatori del sistema, ad esempio connessioni/thread/processi/memoria/CPU e così via. Se uno di questi contatori supera una soglia massima predefinita (80%), le richieste verranno rifiutate con una risposta 429 "Occupato" fino a quando i contatori non tornano a livelli normali.|
