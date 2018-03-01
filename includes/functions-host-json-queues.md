```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|Proprietà  |Predefinito | DESCRIZIONE |
|---------|---------|---------| 
|maxPollingInterval|60000|L'intervallo massimo, in millisecondi, tra i polling di coda.| 
|visibilityTimeout|0|L'intervallo di tempo tra i tentativi se l'elaborazione di un messaggio ha esito negativo.| 
|batchSize|16|Il numero di messaggi in coda che il runtime di Funzioni recupera simultaneamente e di processi in parallelo. Quando il numero elaborato viene ridotto a `newBatchThreshold`, il runtime ottiene un altro batch e inizia l'elaborazione dei messaggi. Di conseguenza, il numero massimo di messaggi simultanei elaborati per ogni funzione è `batchSize` più `newBatchThreshold`. Questo limite si applica separatamente a ogni funzione attivata dalla coda. <br><br>Se si vuole evitare l'esecuzione in parallelo per i messaggi ricevuti su una coda, è possibile impostare `batchSize` su 1. Tuttavia, questa impostazione elimina solo la concorrenza se l'app per le funzioni viene eseguita su una singola macchina virtuale (VM). Se l'app per le funzioni scala orizzontalmente più macchine virtuali, ogni macchina virtuale potrebbe eseguire un'istanza di ogni funzione attivata dalla coda.<br><br>Il valore massimo per `batchSize` è 32. | 
|maxDequeueCount|5|Il numero di volte per provare l'elaborazione di un messaggio prima di essere spostato nella coda non elaborabile.| 
|newBatchThreshold|batchSize/2|Ogni volta che il numero di messaggi elaborati simultaneamente viene ridotto a questo numero, il runtime recupera un altro batch.| 



