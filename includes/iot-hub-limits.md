Nella tabella seguente sono elencati i limiti associati ai diversi livelli di servizio (S1, S2, S3, F1). Per informazioni sul costo di ogni *unità* in ogni livello, vedere [Prezzi di IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

| Risorsa | S1 Standard | S2 Standard | S3 Standard | F1 Gratuito |
| --- | --- | --- | --- | --- |
| Messaggi al giorno |400.000 |6.000.000 |300.000.000 |8.000 |
| Unità massime |200 |200 |10 |1 |

> [!NOTE]
> Se si prevede l'uso di più di 200 unità con un hub di livello S1, S2 o con 10 unità con livello S3, contattare il supporto tecnico Microsoft.
> 
> 

Nella tabella seguente sono elencate le limitazioni che si applicano alle risorse IoT Hub:

| Risorsa | Limite |
| --- | --- |
| Numero massimo hub IoT a pagamento per ogni sottoscrizione di Azure |10 |
| Numero massimo hub IoT gratuiti per ogni sottoscrizione di Azure |1 |
| Numero massimo di identità del dispositivo<br/> restituito in una singola chiamata |1000 |
| Conservazione massima nell'hub IoT per i messaggi dal dispositivo al cloud |7 giorni |
| Dimensione massima del messaggio del dispositivo al cloud |256 KB |
| Dimensione massima del batch del dispositivo al cloud |256 KB |
| Messaggi massimi nel batch del dispositivo al cloud |500 |
| Dimensione massima del messaggio del cloud al dispositivo |64 KB |
| TTL massima per i messaggi del cloud al dispositivo |2 giorni |
| Numero massimo di recapiti per i messaggi  <br/> del cloud al dispositivo |100 |
| Numero massimo di recapiti per i messaggi di feedback  <br/> in risposta a un messaggio del cloud al dispositivo |100 |
| TTL massimo per i messaggi di feedback  <br/> in risposta a un messaggio del cloud al dispositivo |2 giorni |
| Dimensione massima del dispositivo gemello <br/> (tag, proprietà segnalate e proprietà desiderate) | 8 KB |
| Dimensione massima del valore della stringa del dispositivo gemello | 512 byte |
| Profondità massima dell'oggetto nel dispositivo gemello | 5 |
| Dimensione massima del payload del metodo diretto | 8 KB |
| Periodo di conservazione massimo della cronologia del processo | 30 giorni |
| Numero massimo di processi simultanei | 10 (per S3), 5 per (S2), 1 ( per S1) |
| Numero massimo di endpoint aggiuntivi | 10 (per S1, S2, S3) |
| Numero massimo di regole di routing dei messaggi | 100 (per S1, S2, S3) |


> [!NOTE]
> Se sono necessari più di 10 hub IoT a pagamento in una sottoscrizione di Azure, contattare il supporto tecnico Microsoft.


> [!NOTE]
> Attualmente, il numero massimo di dispositivi che è possibile connettere a un singolo hub IoT è 500.000. Per aumentare questo limite, contattare il [supporto tecnico Microsoft](https://azure.microsoft.com/en-us/support/options/).

Il servizio IoT Hub limita le richieste quando vengono superate le quote seguenti:

| Limitazione | Valore per ogni hub |
| --- | --- |
| Operazioni del registro delle identità <br/> (creazione, recupero, elenco, aggiornamento, eliminazione), <br/> importazione/esportazione singola o in blocco |5000/min/unità (per S3)  <br/> &100;/min/unità (per S1 e S2). |
| Connessioni del dispositivo |6000/sec/unità (per S3), 120/sec/unità (per S2), 12/sec/unità (per S1). <br/>Minimo di 100/sec. |
| Inoltri dal dispositivo al cloud |6000/sec/unità (per S3), 120/sec/unità (per S2), 12/sec/unità (per S1). <br/>Minimo di 100/sec. |
| Inoltri dal cloud al dispositivo |5000/min/unità (per S3), 100/min/unità (per S1 e S2). |
| Ricezioni dal cloud al dispositivo |50000/min/unità (per S3), 1000/min/unità (per S1 e S2). |
| Operazioni di caricamento file |5000 notifiche di caricamento file/min/unità (per S3), 100 notifiche di caricamento file/min/unità (per S1 e S2). <br/> 10.000 URI di firma di accesso condiviso possono essere generati contemporaneamente per un account di archiviazione di Azure.<br/> &10; URI di firma di accesso condiviso/dispositivo possono essere generati contemporaneamente. |
| Metodi diretti | 1500/sec/unità (per S3), 30/sec/unità (per S2), 10/sec/unità (per S1) |
| Letture del dispositivo gemello | 50/sec/unità (per S3), un massimo di 10/sec o di 1/sec/unità (per S2), 10/sec (per S1) |
| Aggiornamenti dei dispositivi gemelli | 50/sec/unità (per S3), un massimo di 10/sec o di 1/sec/unità (per S2), 10/sec (per S1) |
| Operazioni dei processi <br/> (creazione, aggiornamento, elenco, eliminazione) | 5000/sec/unità (per S3), 100/sec/unità (per S2), 100/min/unità (per S1) |
| Velocità effettiva delle operazioni dei processi per dispositivo | 50/sec/unità (per S3), un massimo di 10/sec o di 1/sec/unità (per S2), 10/sec (per S1) |