Nella tabella seguente sono elencate le quote e i limiti specifici di Hub eventi di Azure. Per altre informazioni sugli hub eventi, vedere [Prezzi di Hub eventi](https://azure.microsoft.com/pricing/details/event-hubs/). Per informazioni sui prezzi e altre quote del bus di servizio, vedere la panoramica [Prezzi di Azure](https://azure.microsoft.com/pricing/details/service-bus/).

| Limite | Scope | Tipo | Comportamento in caso di superamento | Valore |
|--------------------------------------------------|-------------|--------|------------------------------------------------------------------------------------------------------------------------|----------|
| Numero di hub eventi per ogni spazio dei nomi | Spazio dei nomi | Static | Le successive richieste per la creazione di un nuovo spazio dei nomi verranno rifiutate. | 10 |
| Numero di partizioni per Hub eventi | Entità | Statico | - | 32 |
| Numero di gruppi di consumer per Hub eventi | Entità | Statico | - | 20 |
| Numero di connessioni AMQP per spazio dei nomi | Spazio dei nomi | Statico | Le successive richieste di connessioni aggiuntive verranno rifiutate e verrà restituita un'eccezione dal codice chiamante. | 5.000 |
| Dimensioni massime evento | A livello di sistema | Statico | - | 256 KB |
| Numero di ricevitori fuori dal periodo per gruppo di consumer | Entità | Statico | - | 5 |
| Periodo di memorizzazione massimo dei dati degli eventi | Entità | Statico | - | 1-7 giorni |
| Massimo unità elaborate | Spazio dei nomi | Statico | Se si supera il limite di unità elaborate, i dati verranno limitati e verrà generata un'eccezione **ServerBusyException**. È possibile richiedere un numero maggiore di unità elaborate per un livello Standard compilando una segnalazione. Le unità elaborate aggiuntive sono disponibili in blocchi da 20 in base a un acquisto con impegno. | 20 |

<!---HONumber=AcomDC_0615_2016-->
