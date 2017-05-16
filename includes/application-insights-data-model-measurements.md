Raccolta di misure personalizzate. Usare questa raccolta per segnalare la misura denominata associata all'elemento di telemetria. Casi d'uso tipici sono i seguenti:
- dimensioni del payload della telemetria di dipendenza
- numero di elementi in coda elaborati dalla telemetria delle richieste
- tempo impiegato dal cliente per completare il passaggio nella telemetria degli eventi di completamento dei passaggi della procedura guidata.

È possibile eseguire query sulle [misure personalizzate](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA2WLOw6DMAyGd07hZoLeoRPqyMaGGAL8aiPhGCV2kKoeHsHK%2Bj1myyr8LoiaqfrT%2FkUCzRft4LMl8OUeL3LuLLIx%2BxR%2BIF8%2BtcoiNq2o78vgWuFthQaJ1AeGGxt6UlBwKxa1qQ6EpLhAfQAAAA%3D%3D&timespan=PT24H) in Analisi applicazione:

```
customEvents 
| where customMeasurements != "" 
| summarize avg(todouble(customMeasurements["Completion Time"]) * itemCount)
```

 > [!NOTE]
 > Le misure personalizzate sono associate all'elemento di telemetria a cui appartengono e sono soggette al campionamento con l'elemento di telemetria contenente tali misurazioni. Per rilevare una misura che ha un valore indipendente da altri tipi di telemetria, usare [Dati di telemetria relativi alle metriche](../articles/application-insights/app-insights-api-custom-events-metrics.md#send-metrics).

Lunghezza massima della chiave: 150
