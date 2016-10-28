Nella tabella seguente sono elencati i limiti associati ai diversi livelli di servizio (S1, S2, S3, F1). Per informazioni sul costo di ogni *unità* in ogni livello, vedere [Prezzi di IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

| Risorsa | S1 Standard | S2 Standard | S3 Standard | F1 Gratuito |
| -------- | ----------- | ----------- | ----------- | ------- |
| Messaggi al giorno | 400\.000 | 6\.000.000 | 300\.000.000 | 8\.000 |
| Unità massime | 200 | 200 | 200 | 1 |

> [AZURE.NOTE] Se si prevede l'utilizzo di più di 200 unità con un hub di livello S1, S2 o S3, contattare il supporto tecnico Microsoft.

Nella tabella seguente sono elencate le limitazioni che si applicano alle risorse IoT Hub:

| Risorsa | Limite |
| -------- | ----- |
| Numero massimo hub IoT a pagamento per ogni sottoscrizione di Azure | 10 |
| Numero massimo hub IoT gratuiti per ogni sottoscrizione di Azure | 1 |
| Numero massimo di identità del dispositivo<br/> restituito in una singola chiamata | 1000 |
| Conservazione massima nell'hub IoT per i messaggi dal dispositivo al cloud | 7 giorni |
| Dimensione massima del messaggio del dispositivo al cloud | 256 KB |
| Dimensione massima del batch del dispositivo al cloud | 256 KB |
| Messaggi massimi nel batch del dispositivo al cloud | 500 |
| Dimensione massima del messaggio del cloud al dispositivo | 64 KB |
| TTL massima per i messaggi del cloud al dispositivo | 2 giorni |
| Numero massimo di recapiti per i messaggi <br/> del cloud al dispositivo | 100 |
| Numero massimo di recapiti per i messaggi di feedback <br/> in risposta a un messaggio del cloud al dispositivo | 100 |
| TTL massimo per i messaggi di feedback <br/> in risposta a un messaggio del cloud al dispositivo | 2 giorni |

> [AZURE.NOTE] Se sono necessari più di 10 hub IoT a pagamento in una sottoscrizione di Azure, contattare il supporto tecnico Microsoft.

Il servizio IoT Hub limita le richieste quando vengono superate le quote seguenti:

| Limitazione | Valore per ogni hub |
| -------- | ------------- |
| Operazioni del registro delle identità <br/> (creazione, recupero, elenco, aggiornamento, eliminazione), <br/> importazione/esportazione singola o in blocco | 5000/min/unità (per S3) <br/> 100/min/unità (per S1 e S2). |
| Connessioni del dispositivo | 6000/sec/unità (per S3), 120/sec/unità (per S2), 12/sec/unità (per S1). <br/>Minimo di 100/sec. |
| Inoltri dal dispositivo al cloud | 6000/sec/unità (per S3), 120/sec/unità (per S2), 12/sec/unità (per S1). <br/>Minimo di 100/sec. |
| Inoltri dal cloud al dispositivo | 5000/min/unità (per S3), 100/min/unità (per S1 e S2). |
| Ricezioni dal cloud al dispositivo | 50000/min/unità (per S3), 1000/min/unità (per S1 e S2). |
| Operazioni di caricamento file | 5000 notifiche di caricamento file/min/unità (per S3), 100 notifiche di caricamento file/min/unità (per S1 e S2). <br/> 10000 URI di firma di accesso condiviso possono essere generati contemporaneamente per un account di archiviazione.<br/> 10 URI di firma di accesso condiviso/dispositivo possono essere generati contemporaneamente. |

<!---HONumber=AcomDC_0824_2016-->