```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|Proprietà  |Default | Descrizione |
|---------|---------|---------| 
|maxConcurrentCalls|16|Il numero massimo di chiamate simultanee al callback che il message pump deve avviare. Per impostazione predefinita, il runtime di Funzioni elabora più messaggi contemporaneamente. Per fare in modo che il runtime elabori un solo messaggio della coda o dell'argomento alla volta, impostare `maxConcurrentCalls` su 1. | 
|prefetchCount|n/d|Il valore predefinito di PrefetchCount che verrà utilizzato per il MessageReceiver sottostante.| 
|autoRenewTimeout|00:05:00|La durata massima entro il quale il blocco del messaggio verrà rinnovato automaticamente.| 
