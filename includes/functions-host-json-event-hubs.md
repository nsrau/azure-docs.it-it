```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|Proprietà  |Default | Descrizione |
|---------|---------|---------| 
|maxBatchSize|64|Il numero massimo degli eventi ricevuto per ogni ciclo di ricezione.|
|prefetchCount|n/d|Il valore predefinito di PrefetchCount che verrà utilizzato dall’EventProcessorHost sottostante.| 
|batchCheckpointFrequency|1|Il numero di batch di eventi da elaborare prima di creare un checkpoint di cursore EventHub.| 
