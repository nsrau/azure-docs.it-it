<properties
 pageTitle="Argomenti della Guida per sviluppatori per IoT Hub | Microsoft Azure"
 description="Guida per gli sviluppatori dell'hub IoT Azure, con informazioni su endpoint, sicurezza, registro delle identità del dispositivo e messaggistica per l'hub IoT."
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="dobett"/>

# Guida per gli sviluppatori dell'hub IoT di Azure

L'hub IoT di Azure è un servizio completamente gestito che consente comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi IoT e un back-end applicazioni.

L'hub IoT di Azure offre questi vantaggi:

* Comunicazioni protette con credenziali di sicurezza per singoli dispositivi e controllo di accesso.
* Messaggistica affidabile da dispositivo a cloud e da cloud a dispositivo su vasta scala.
* Connettività semplice dei dispositivi con librerie di dispositivi per i linguaggi e le piattaforme più diffusi.

Questo articolo include gli argomenti seguenti:

- [Endpoint](#endpoints). Questa sezione illustra i diversi endpoint esposti da ogni hub IoT per operazioni della fase di esecuzione e di gestione.
- [Registro delle identità dei dispositivi](#device-identity-registry). Questa sezione illustra le informazioni archiviate nel registro delle identità dei dispositivi di ogni hub IoT e spiega come accedere alle informazioni e modificarle.
- [Sicurezza](#security). Questa sezione illustra il modello di sicurezza usato per concedere l'accesso alla funzionalità hub IoT per i dispositivi e i componenti cloud.
- [Messaggistica](#messaging). Questa sezione descrive le funzionalità di messaggistica da dispositivo a cloud e da cloud a dispositivo esposte dall'hub IoT.
- [Quote e limitazione](#throttling). Questa sezione riepiloga le quote applicabili all'uso dell'hub IoT.

## Endpoint <a id="endpoints"></a>

L'hub IoT di Azure è un servizio multi-tenant che espone funzionalità a diversi attori. Il diagramma seguente mostra i vari endpoint esposti dall'hub IoT.

![Endpoint hub IoT][img-endpoints]

Ecco di seguito una descrizione degli endpoint:

* **Provider di risorse**: il provider di risorse dell'hub IoT espone un'interfaccia di [Gestione risorse di Azure][lnk-arm] che consente ai proprietari di sottoscrizioni di Azure di creare ed eliminare hub IoT e di aggiornarne le proprietà. Le proprietà dell'hub IoT determinano i criteri di sicurezza a livello di hub, a differenza del controllo di accesso a livello di dispositivo (vedere [Controllo di accesso](#accesscontrol) di seguito) e delle opzioni funzionali per la messaggistica da cloud a dispositivo e da dispositivo a cloud. Il provider di risorse consente anche di [esportare le identità dei dispositivi](#importexport).
* **Gestione delle identità dei dispositivi**: ogni hub IoT espone un set di endpoint REST HTTP per gestire le identità dei dispositivi, per operazioni di creazione, recupero, aggiornamento ed eliminazione. Le identità dei dispositivi vengono usate per l'autenticazione dei dispositivi e il controllo di accesso. Per altre informazioni, vedere [Registro delle identità dei dispositivi](#device-identity-registry).
* **Endpoint del dispositivo**: per ogni dispositivo di cui è stato effettuato il provisioning nel registro delle identità dei dispositivi, l'hub IoT espone un set di endpoint che il dispositivo può usare per inviare e ricevere messaggi:
    - *Invio di messaggi da dispositivo a cloud*. Usare questo endpoint per inviare messaggi da dispositivo a cloud. Per altre informazioni, vedere [Messaggistica da dispositivo a cloud](#d2c).
    - *Ricezione di messaggi da cloud a dispositivo*. Il dispositivo usa questo endpoint per ricevere messaggi da cloud a dispositivo specifici. Per altre informazioni, vedere [Messaggistica da cloud a dispositivo](#c2d).

    Questi endpoint vengono esposti tramite i protocolli HTTP, [MQTT][lnk-mqtt] e [AMQP][lnk-amqp]. Si noti che AMQP è disponibile anche in [WebSocket][lnk-websockets] sulla porta 443.
* **Endpoint di servizio**: ogni hub IoT espone un set di endpoint che il back-end dell'applicazione può usare per comunicare con i dispositivi. Questi endpoint vengono attualmente esposti solo tramite il protocollo [AMQP][lnk-amqp].
    - *Ricezione di messaggi da dispositivo a cloud*. Questo endpoint è compatibile con gli [hub eventi di Azure][lnk-event-hubs] e può essere usato da un servizio back-end per leggere tutti i messaggi da dispositivo a cloud inviati dai dispositivi. Per altre informazioni, vedere [Messaggistica da dispositivo a cloud](#d2c).
    - *Invio di messaggi da cloud a dispositivo e ricezione di acknowledgement di recapito*. Questi endpoint consentono al back-end dell'applicazione di inviare messaggi affidabili da cloud a dispositivo e di ricevere gli acknowledgment di recapito o di scadenza corrispondenti. Per altre informazioni, vedere [Messaggistica da cloud a dispositivo](#c2d).

L'articolo relativo ad [API e SDK dell'hub IoT][lnk-apis-sdks] illustra le diverse modalità di accesso agli endpoint.

È infine importante notare che tutti gli endpoint dell'hub IoT usano il protocollo [TLS][lnk-tls] e che gli endpoint non vengono mai esposti in canali non crittografati o non sicuri.

### Come leggere dagli endpoint compatibili con l'hub eventi. <a id="eventhubcompatible"></a>

Quando si usa [Azure Service Bus SDK per .NET](https://www.nuget.org/packages/WindowsAzure.ServiceBus) o l'[host processore di eventi di Hub eventi][], è possibile usare qualsiasi stringa di connessione dell'hub IoT con le autorizzazioni appropriate e quindi usare i **messaggi/eventi** come nome dell'hub eventi.

Quando si usano gli SDK o le integrazioni del prodotto non compatibili con l'hub IoT, è necessario recuperare un endpoint compatibile con gli hub eventi e un nome di hub eventi dalle impostazioni dell'hub IoT nel [portale di Azure][]\:

1. Nel pannello dell'hub IoT fare clic su **Impostazioni**, quindi su **Messaggistica**.
2. La sezione **Impostazioni da dispositivo a cloud** include i valori **Endpoint compatibile con l'hub eventi**, **Nome compatibile con l'hub eventi** e **Partizioni**.

    ![][img-eventhubcompatible]

> [AZURE.NOTE] A volte l'SDK richiede un valore per **Nome host** o **Spazio dei nomi**. In tal caso sarà necessario rimuovere lo schema dall'**Endpoint compatibile con l'hub eventi**. Ad esempio, se l'endpoint compatibile con l'hub eventi è **sb://iothub-ns-myiothub-1234.servicebus.windows.net/**, il **Nome host** sarà **iothub-ns-myiothub-1234.servicebus.windows.net** e lo **Spazio dei nomi** sarà **iothub-ns-myiothub-1234**.

È quindi possibile usare qualsiasi tipo di criteri di sicurezza di accesso condiviso con autorizzazioni **ServiceConnect** per la connessione all'hub eventi specificato.

Se si deve compilare una stringa di connessione dell'hub eventi con le informazioni precedenti, è possibile usare il modello seguente:

```
Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}
```

Di seguito è riportato un elenco di SDK e integrazioni che è possibile usare con gli endpoint compatibili con l'hub eventi esposti dall'hub IoT:

* [Client degli hub eventi Java.](https://github.com/hdinsight/eventhubs-client)
* [Spout di Apache Storm](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md). È possibile visualizzare il [codice sorgente dello spout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) su GitHub.
* [Integrazione con Apache Spark.](../hdinsight/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)

## Registro delle identità dei dispositivi

Ogni hub IoT include un registro delle identità dei dispositivi che è possibile usare per creare risorse per i singoli dispositivi nel servizio, ad esempio una coda contenente messaggi da cloud a dispositivo in elaborazione, e per consentire l'accesso agli endpoint per il dispositivo, come illustrato nella sezione [Controllo di accesso](#accesscontrol).

A livello generale, il registro delle identità dei dispositivi è una raccolta delle risorse relative alle identità dei dispositivi che supporta REST. Le sezioni seguenti illustrano in modo dettagliato le proprietà delle risorse relative alle identità dei dispositivi e le operazioni sulle identità consentite dal registro.

> [AZURE.NOTE] Per altre informazioni sul protocollo HTTP e sugli SDK che è possibile usare per l'interazione con il registro delle identità dei dispositivi, vedere l'articolo relativo ad [API e SDK dell'hub IoT][lnk-apis-sdks].

### Proprietà delle identità dei dispositivi <a id="deviceproperties"></a>

Le identità dei dispositivi vengono rappresentate da documenti JSON con le proprietà seguenti.

| Proprietà | Opzioni | Descrizione |
| -------- | ------- | ----------- |
| deviceId | Obbligatoria, di sola lettura negli aggiornamenti | Stringa con distinzione tra maiuscole/minuscole (al massimo 128 caratteri di lunghezza) di caratteri alfanumerici ASCII a 7 bit + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| generationId | Obbligatoria, di sola lettura | Stringa con distinzione tra maiuscole/minuscole generata dall'hub con lunghezza massima di 128 caratteri. Viene usata per distinguere i dispositivi con lo stesso valore **deviceId** in caso di eliminazione e nuova creazione. |
| etag | Obbligatoria, di sola lettura | Stringa che rappresenta un eTag vulnerabile per l'identità del dispositivo, come indicato in [RFC7232][lnk-rfc7232].|
| auth | Facoltativa | Oggetto composito contenente le informazioni di autenticazione e i materiali di sicurezza. |
| auth.symkey | Facoltativa | Oggetto composito contenente una chiave primaria e una chiave secondaria, archiviate in formato Base 64. |
| status | Obbligatoria | Può essere **Enabled** o **Disabled**. Se è **Enabled**, il dispositivo sarà autorizzato alla connessione. Se è **Disabled**, il dispositivo non potrà accedere ad alcun endpoint per il dispositivo. |
| statusReason | Facoltativa | Stringa con 128 caratteri di lunghezza che archivia il motivo dello stato dell'identità del dispositivo. Sono consentiti tutti i caratteri UTF-8. |
| statusUpdateTime | Sola lettura | Data e ora dell'ultimo aggiornamento dello stato. |
| connectionState | Sola lettura | **Connected** o **Disconnected**, rappresenta la visualizzazione Hub IoT dello stato di connessione del dispositivo. **Importante**: questo campo deve essere usato solo per scopi di sviluppo e di debug. Lo stato di connessione viene aggiornato solo per i dispositivi che usano MQTT o AMQP. Si basa anche su ping a livello di protocollo (ping MQTT o ping AMQP) e può avere un ritardo al massimo di 5 minuti. Per questi motivi possono essere presenti falsi positivi, ad esempio dispositivi segnalati come connessi ma che in effetti sono disconnessi. |
| connectionStateUpdatedTime | Sola lettura | Data e ora dell'ultimo aggiornamento dello stato della connessione. |
| lastActivityTime | Sola lettura | Data e ora in cui il dispositivo si è connesso, ha ricevuto o inviato un messaggio per l'ultima volta. |

> [AZURE.NOTE] Lo stato della connessione può rappresentare solo la visualizzazione Hub IoT dello stato della connessione. Gli aggiornamenti dello stato possono essere ritardati a seconda delle condizioni e delle configurazioni della rete.

### Operazioni relative alle identità dei dispositivi

Il registro delle identità dei dispositivi dell'hub IoT espone le operazioni seguenti:

* Creare l'identità del dispositivo
* Aggiornare l'identità del dispositivo
* Recuperare l'identità del dispositivo tramite ID
* Eliminare l'identità del dispositivo
* Elencare al massimo 1000 identità
* Esportare tutte le identità nell'archivio BLOB
* Importare identità dall'archivio BLOB

Tutte queste operazioni consentono l'uso della concorrenza ottimistica, come illustrato in [RFC7232][lnk-rfc7232].

> [AZURE.IMPORTANT] L'unico modo per recuperare tutte le identità nel registro delle identità di un hub consiste nell'usare la funzionalità [Esporta](#importexport).

Un registro delle identità dei dispositivi di un hub IoT:

- Non contiene metadati delle applicazioni.
- È accessibile come dizionario usando il valore **deviceId** come chiave.
- Non supporta le query espressive.

Una soluzione IoT include in genere un archivio separato specifico della soluzione che contiene metadati specifici dell'applicazione. Ad esempio, l'archivio specifico della soluzione in una soluzione per un edificio efficiente registra l'ambiente in cui viene distribuito un sensore di temperatura.

> [AZURE.IMPORTANT] Usare il registro delle identità dei dispositivi solo per le operazioni di gestione e provisioning dei dispositivi. Le operazioni a velocità effettiva elevata in fase di esecuzione non devono dipendere dall'esecuzione di operazioni nel registro delle identità dei dispositivi. Ad esempio, la verifica dello stato di connessione di un dispositivo prima dell'invio di un comando non è uno schema supportato. Assicurarsi di verificare le [ frequenze di limitazione](#throttling) per il registro delle identità dei dispositivi e lo schema di [heartbeat del dispositivo][lnk-guidance-heartbeat].

### Disabilitazione di dispositivi

È possibile disabilitare i dispositivi aggiornando la proprietà **status** di un'identità nel registro. Questo approccio viene in genere usato in due scenari:

- Durante un processo di orchestrazione di provisioning. Per altre informazioni, vedere [Progettare la soluzione - Provisioning di dispositivi][lnk-guidance-provisioning].
- Se, per qualsiasi motivo, si considera un dispositivo compromesso o non più autorizzato.

### Esportare le identità dei dispositivi<a id="importexport"></a>

Le esportazioni sono processi con esecuzione prolungata che usano un contenitore BLOB fornito dal cliente per salvare i dati relativi alle identità letti dal registro delle identità.

È possibile esportare in blocco le identità dei dispositivi dal registro delle identità di un hub IoT usando operazioni asincrone sull'[endpoint del provider di risorse dell'hub IoT](#endpoints).

Ecco di seguito le operazioni consentite nei processi di esportazione:

* Creare un processo di esportazione
* Recuperare lo stato di un processo in esecuzione
* Annullare un processo in esecuzione

> [AZURE.NOTE] Ogni hub può avere solo un processo in esecuzione in un determinato momento.

Per informazioni dettagliate sulle API di importazione e di esportazione, vedere [Hub IoT di Azure - API del provider di risorse][lnk-resource-provider-apis].

Per altre informazioni sull'esecuzione di processi di importazione ed esportazione, vedere [Gestione in blocco delle identità dei dispositivi dell'hub IoT][lnk-bulk-identity]

### Processi di esportazione

Tutti i processi di esportazione hanno le proprietà seguenti:

| Proprietà | Opzioni | Descrizione |
| -------- | ------- | ----------- |
| jobId | Generata dal sistema, ignorata in fase di creazione | |
| creationTime | Generata dal sistema, ignorata in fase di creazione | |
| endOfProcessingTime | Generata dal sistema, ignorata in fase di creazione | |
| type | Sola lettura | **ExportDevices** |
| status | Generata dal sistema, ignorata in fase di creazione | **Accodato**, **Avviato**, **Completato**, **Non riuscito** |
| progress | Generata dal sistema, ignorata in fase di creazione | Valore intero della percentuale di completamento. |
| outputBlobContainerURI | Obbligatoria per tutti i processi | URI di firma di accesso condiviso del BLOB con accesso in scrittura a un contenitore BLOB. Vedere [Creare e usare una firma di accesso condiviso con il servizio BLOB][lnk-createuse-sas]. Viene usata per ottenere l'output dello stato del processo e dei risultati. |
| excludeKeysInExport | Facoltativa | Se **true**, le chiavi vengono incluse nell'output di esportazione; in caso contrario, vengono esportate come **null**. Il valore predefinito è **false**. |
| failureReason | Generata dal sistema, ignorata in fase di creazione | Se lo stato è **Non riuscito**, corrisponde a una stringa contenente il motivo. |

I processi di esportazione accettano come parametro un URI di firma di accesso condiviso BLOB. In questo modo viene concesso l'accesso in scrittura a un contenitore BLOB per abilitare il processo all'output dei risultati.

Il processo scrive i risultati di output nel contenitore BLOB specificato in un file denominato **devices.txt**. Questo file include le identità dei dispositivi serializzate come JSON, come illustrato in [Proprietà delle identità dei dispositivi](#deviceproperties). Il valore di autenticazione è impostato su **null** per ogni dispositivo nel file **devices.txt** se il parametro **excludeKeysInExport** è impostato su **true**.

**Esempio**:

```
{"id":"devA","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}}
{"id":"devB","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}}
{"id":"devC","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}}
```

### Importare le identità dei dispositivi

Le importazioni sono processi con esecuzione prolungata che usano un contenitore BLOB fornito dal cliente per scrivere i dati relativi alle identità nel registro delle identità.

È possibile importare in blocco le identità dei dispositivi in un registro delle identità di un hub IoT usando operazioni asincrone sull'[endpoint del provider di risorse dell'hub IoT](#endpoints).

Ecco di seguito le operazioni consentite nei processi di importazione:

* Creare un processo di importazione
* Recuperare lo stato di un processo in esecuzione
* Annullare un processo in esecuzione

> [AZURE.NOTE] Ogni hub può avere solo un processo in esecuzione in un determinato momento.

Per informazioni dettagliate sulle API di importazione e di esportazione, vedere [Hub IoT di Azure - API del provider di risorse][lnk-resource-provider-apis].

Per altre informazioni sull'esecuzione di processi di importazione ed esportazione, vedere [Gestione in blocco delle identità dei dispositivi dell'hub IoT][lnk-bulk-identity]

### Processi di importazione

Tutti i processi di importazione hanno le proprietà seguenti:

| Proprietà | Opzioni | Descrizione |
| -------- | ------- | ----------- |
| jobId | Generata dal sistema, ignorata in fase di creazione | |
| creationTime | Generata dal sistema, ignorata in fase di creazione | |
| endOfProcessingTime | Generata dal sistema, ignorata in fase di creazione | |
| type | Sola lettura | **ImportDevices** |
| status | Generata dal sistema, ignorata in fase di creazione | **Accodato**, **Avviato**, **Completato**, **Non riuscito** |
| progress | Generata dal sistema, ignorata in fase di creazione | Valore intero della percentuale di completamento. |
| outputBlobContainerURI | Obbligatoria per tutti i processi | URI di firma di accesso condiviso del BLOB con accesso in scrittura a un contenitore BLOB. Vedere [Creare e usare una firma di accesso condiviso con il servizio BLOB][lnk-createuse-sas]. Viene usata per restituire l'output dello stato del processo. |
| inputBlobContainerURI | Obbligatoria | URI di firma di accesso condiviso del BLOB con accesso in lettura a un contenitore BLOB. Vedere [Creare e usare una firma di accesso condiviso con il servizio BLOB][lnk-createuse-sas]. Il processo legge le informazioni sul dispositivo da importare da questo BLOB. |
| failureReason | Generata dal sistema, ignorata in fase di creazione | Se lo stato è **Non riuscito**, corrisponde a una stringa contenente il motivo. |

I processi di importazione accettano come parametri due URI di firma di accesso condiviso del BLOB. Uno concede l'accesso in scrittura a un contenitore BLOB per consentire al processo di restituire il proprio stato, l'altro concede l'accesso in lettura a un contenitore BLOB per consentire al processo di leggere i propri dati di input.

Il processo legge i dati di input dal contenitore BLOB specificato in un file denominato **devices.txt**. Questo file include le identità dei dispositivi serializzate come JSON, come illustrato in [Proprietà delle identità dei dispositivi](#deviceproperties). È possibile eseguire l'override del comportamento di importazione predefinito per ogni dispositivo aggiungendo la proprietà **importMode**. La proprietà può accettare uno dei valori seguenti:

| importMode | Descrizione |
| -------- | ----------- |
| **createOrUpdate** | Se non esiste un dispositivo con l'**ID** specificato, viene registrato di nuovo. <br/>Se il dispositivo esiste già, le informazioni esistenti vengono sovrascritte con i dati di input forniti senza tener conto del valore **ETag**. |
| **create** | Se non esiste un dispositivo con l'**ID** specificato, viene registrato di nuovo. <br/>Se il dispositivo esiste già, viene scritto un errore nel file di log. |
| **update** | Se esiste già un dispositivo con l'**ID** specificato, le informazioni esistenti vengono sovrascritte con i dati di input forniti senza tener conto del valore **ETag**. <br/>Se il dispositivo non esiste, viene scritto un errore nel file di log. |
| **updateIfMatchETag** | Se esiste già un dispositivo con l'**ID** specificato, le informazioni esistenti vengono sovrascritte con i dati di input forniti solo se viene rilevata una corrispondenza con **ETag**. <br/>Se il dispositivo non esiste, viene scritto un errore nel file di log. <br/>In caso di mancata corrispondenza con **ETag**, viene scritto un errore nel file di log. |
| **createOrUpdateIfMatchETag** | Se non esiste un dispositivo con l'**ID** specificato, viene registrato di nuovo. <br/>Se il dispositivo esiste già, le informazioni esistenti vengono sovrascritte con i dati di input forniti solo se viene rilevata una corrispondenza con **ETag**. <br/>In caso di mancata corrispondenza con **ETag**, viene scritto un errore nel file di log. |
| **delete** | Se esiste già un dispositivo con l'**ID** specificato, viene eliminato senza tener conto del valore **ETag**. <br/>Se il dispositivo non esiste, viene scritto un errore nel file di log. |
| **deleteIfMatchETag** | Se esiste già un dispositivo con l'**ID** specificato, viene eliminato solo se viene rilevata una corrispondenza con **ETag**. Se il dispositivo non esiste, viene scritto un errore nel file di log. <br/>In caso di mancata corrispondenza con ETag, viene scritto un errore nel file di log. |

**Esempio**:

```
{"id":"devA","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}, "importMode":"delete"}
{"id":"devB","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}, "importMode":"createOrUpdate"}
{"id":"devC","eTag":"MQ==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"123","secondaryKey":"123"}}, "importMode":"create"}
```

## Sicurezza <a id="security"></a>

Questa sezione illustra le opzioni per la protezione dell'hub IoT di Azure.

### Controllo di accesso <a id="accesscontrol"></a>

L'hub IoT usa il set di *autorizzazioni* seguente per concedere l'accesso agli endpoint di ogni hub IoT. Le autorizzazioni limitano l'accesso a un hub IoT in base alla funzionalità.

* **RegistryRead**. Concede l'accesso in lettura al registro delle identità dei dispositivi. Per altre informazioni, vedere [Registro delle identità dei dispositivi](#device-identity-registry).
* **RegistryReadWrite**. Concede l'accesso in lettura e scrittura al registro delle identità dei dispositivi. Per altre informazioni, vedere [Registro delle identità dei dispositivi](#device-identity-registry).
* **ServiceConnect**. Concede l'accesso alle comunicazioni per il servizio cloud e al monitoraggio degli endpoint. Ad esempio, concede ai servizi cloud back-end l'autorizzazione per la ricezione di messaggi da dispositivo a cloud, l'invio di messaggi da cloud a dispositivo e il recupero degli acknowledgment di recapito corrispondenti.
* **DeviceConnect**. Concede l'accesso agli endpoint di comunicazione per il dispositivo. Ad esempio, concede l'autorizzazione per l'invio di messaggi da dispositivo a cloud e la ricezione di messaggi da cloud a dispositivo. Questa autorizzazione viene usata dai dispositivi.

Per concedere le autorizzazioni è possibile procedere nei modi seguenti:

* **Criteri di accesso condivisi a livello di hub**. I criteri di accesso condiviso possono concedere qualsiasi combinazione delle autorizzazioni elencate nella sezione precedente. È possibile definire i criteri nel [portale di Azure][lnk-management-portal] o a livello di codice mediante le [API del provider di risorse dell'hub IoT di Azure][lnk-resource-provider-apis]. Un hub IoT appena creato ha i criteri predefiniti seguenti:

    - *iothubowner*: criteri con tutte le autorizzazioni
    - *service*: criteri con autorizzazione **ServiceConnect**
    - *device*: criteri con autorizzazione **DeviceConnect**
    - *registryRead*: criteri con autorizzazione **RegistryRead**
    - *registryReadWrite*: criteri con autorizzazioni **RegistryRead** e **RegistryWrite**

* **Credenziali di sicurezza specifiche del dispositivo**. Ogni hub IoT contiene un [registro delle identità dei dispositivi](#device-identity-registry). Per ogni dispositivo in questo registro è possibile configurare credenziali di sicurezza che concedono autorizzazioni **DeviceConnect** con ambito agli endpoint di dispositivo corrispondenti.

**Esempio**. In una tipica soluzione IoT: - Il componente di gestione dei dispositivi usa i criteri *registryReadWrite*. Il componente processore di eventi usa i criteri *service*. - Il componente della logica di business di runtime del dispositivo usa i criteri *service*. - I singoli dispositivi si connettono con le credenziali archiviate nel registro delle identità dell'hub IoT.

Per indicazioni sugli argomenti relativi alla sicurezza per l'hub IoT, vedere la sezione sulla sicurezza in [Progettare la soluzione][lnk-guidance-security].

### Autenticazione

L'hub IoT di Azure concede l'accesso agli endpoint tramite la verifica di un token rispetto ai criteri di accesso condiviso e alle credenziali di sicurezza del registro delle identità dei dispositivi.

Le credenziali di sicurezza, ad esempio le chiavi asimmetriche, non vengono mai trasmesse in rete.

> [AZURE.NOTE] Il provider di risorse dell'hub IoT di Azure viene protetto tramite la sottoscrizione di Azure, analogamente a tutti i provider in [Gestione risorse di Azure][lnk-azure-resource-manager].

#### Formato del token di sicurezza <a id="tokenformat"></a>

Il token di sicurezza ha il formato seguente:

	SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}

Questi sono i valori previsti:

| Valore | Descrizione |
| ----- | ----------- |
| {signature} | Stringa della firma HMAC-SHA256 nel formato: `{URL-encoded-resourceURI} + "\n" + expiry` **Importante**: la chiave viene decodificata dalla codifica Base 64 e usata come chiave per eseguire il calcolo di HMAC-SHA256. |
| {resourceURI} | Prefisso URI (per segmento) degli endpoint a cui è possibile accedere tramite questo token. Ad esempio, `/events` |
| {expiry} | Stringhe UTF8 per il numero di secondi trascorsi dalle 00:00:00 UTC dell'1 gennaio 1970. |
| {URL-encoded-resourceURI} | Codifica URL con lettere minuscole dell'URI della risorsa con lettere minuscole |
| {policyName} | Nome del criterio di accesso condiviso a cui fa riferimento il token. Assente nel caso in cui i token facciano riferimento a credenziali del registro dei dispositivi. |

**Nota sul prefisso**: il prefisso dell'URI viene calcolato in base al segmento e non in base al carattere. Ad esempio `/a/b` è un prefisso per `/a/b/c` ma non per `/a/bc`.

È possibile trovare le implementazioni dell'algoritmo di firma negli IoT SDK per dispositivi e servizi:

* [IoT SDK per servizi per Java](https://github.com/Azure/azure-iot-sdks/tree/master/java/service/iothub-service-sdk/src/main/java/com/microsoft/azure/iot/service/auth)
* [IoT SDK per dispositivi per Java](https://github.com/Azure/azure-iot-sdks/tree/master/java/device/iothub-java-client/src/main/java/com/microsoft/azure/iothub/auth)
* [IoT SDK per dispositivi e servizi per Node. js](https://github.com/Azure/azure-iot-sdks/blob/master/node/common/core/lib/shared_access_signature.js)

#### Specifiche del protocollo

Ogni protocollo supportato, ad esempio AMQP, MQTT e HTTP, trasporta i token in modo diverso.

Il protocollo HTTP implementa l'autenticazione includendo un token valido nell'intestazione della richiesta **Authorization**. Anche un parametro di query denominato **Authorization** può trasportare il token.

Quando si usa [AMQP][lnk-amqp], l'hub IoT supporta [SASL PLAIN][lnk-sasl-plain] e la [sicurezza basata sulle attestazioni AMQP][lnk-cbs].

Nel caso della sicurezza basata sulle attestazioni AMQP, lo standard specifica come trasmettere questi token.

Per SASL PLAIN **username** può essere:

* `{policyName}@sas.root.{iothubName}` nel caso di token a livello di hub.
* `{deviceId}` nel caso di token con ambito relativo al dispositivo.

In entrambi i casi il campo della password contiene il token, come illustrato nella sezione [Formato del token](#tokenformat).

Quando si usa MQTT, il pacchetto CONNECT ha come valore di deviceId ClientId, {iothubhostname}/{deviceId} nel campo Username e un token di firma di accesso condiviso nel campo Password. Il valore di {iothubhostname} deve essere il record CName completo dell'hub IoT, ad esempio contoso.azure-devices.net.

> [AZURE.NOTE] Gli [Azure IoT Hub SDK][lnk-apis-sdks] generano automaticamente i token durante la connessione al servizio. In alcuni casi, gli SDK non supportano tutti i protocolli o tutti i metodi di autenticazione.

#### Confronto tra SASL PLAIN e CBS

Quando si usa SASL PLAIN, un client che si connette a un hub IoT potrà usare un singolo token per ogni connessione TCP. Quando il token scade, la connessione TCP si disconnette dal servizio e attiva una riconnessione. Questo comportamento, anche se non problematico per un componente back-end applicazione, risulta molto dannoso per un'applicazione lato dispositivo, per i motivi seguenti:

*  I gateway si connettono in genere per conto di molti dispositivi. Quando si usa SASL PLAIN, devono creare una connessione TCP distinta per ogni dispositivo che si connette a un hub IoT. Ciò aumenta in modo considerevole il consumo energetico e delle risorse di rete e incrementa la latenza della connessione di ogni dispositivo.
* L'aumento dell'uso delle risorse per la riconnessione dopo la scadenza di ogni token influiscono negativamente sui dispositivi vincolati alle risorse.

### Definizione dell'ambito delle credenziali a livello hub

È possibile definire l'ambito dei criteri di sicurezza a livello hub creando token con URI di risorsa con limitazioni. Ad esempio, l'endpoint per l'invio di messaggi da dispositivo a cloud da un dispositivo è **/devices/{deviceId}/events**. È anche possibile usare criteri di accesso condiviso a livello di hub con autorizzazioni **DeviceConnect** per firmare un token il cui valore resourceURI è **/devices/{deviceId}**, creando un token che può essere usato solo per inviare messaggi per conto del **deviceId** del dispositivo.

Questo meccanismo è simile ai [criteri delle entità di pubblicazione di Hub eventi][lnk-event-hubs-publisher-policy] e consente di implementare metodi di autenticazione personalizzati, come illustrato nella sezione sulla sicurezza in [Progettare la soluzione][lnk-guidance-security].

## Messaggistica

L'hub IoT fornisce le primitive di messaggistica per la comunicazione: - [Da cloud a dispositivo](#c2d): da un back-end dell'applicazione (*servizio* o *cloud*). - [Da dispositivo a cloud](#d2c): da un dispositivo a un back-end dell'applicazione.

Le proprietà di base della funzionalità di messaggistica dell'hub IoT sono l'affidabilità e la durabilità dei messaggi. Ciò consente la resilienza in caso di connettività intermittente sul lato dispositivo e picchi di carico durante l'elaborazione degli eventi sul lato cloud. L'hub IoT implementa *almeno una volta* le garanzie di recapito per la messaggistica da dispositivo a cloud e da cloud a dispositivo.

L'hub IoT supporta più protocolli per il dispositivo, ad esempio AMQP e HTTP/1. Per supportare l'interoperabilità senza problemi tra i protocolli, l'hub IoT definisce un formato di messaggio comune supportato da tutti i protocolli per il dispositivo.

### Formato del messaggio <a id="messageformat"></a>

I messaggi dell'hub IoT sono costituiti da questi elementi:

* Un set di *proprietà del sistema*. Queste sono proprietà impostate o interpretate dall'hub IoT. Il set è predeterminato.
* Un set di *proprietà dell'applicazione*. Si tratta di un dizionario di proprietà stringa che l'applicazione può definire e a cui può accedere senza dover deserializzare il corpo del messaggio. Queste proprietà non vengono mai modificate dall'hub IoT.
* Corpo binario opaco.

Per altre informazioni sul modo in cui il messaggio viene codificato in diversi protocolli, vedere [API e SDK dell'hub IoT][lnk-apis-sdks].

Questo è il set di proprietà del sistema nei messaggi dell'hub IoT.

| Proprietà | Descrizione |
| -------- | ----------- |
| MessageId | Identificatore configurabile dall'utente per il messaggio, usato in genere per modelli di richiesta-risposta. Formato: stringa con distinzione tra maiuscole/minuscole (al massimo 128 caratteri di lunghezza) di caratteri alfanumerici ASCII a 7 bit + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| Numero di sequenza | Numero, univoco per ogni dispositivo-coda, assegnato dall'hub IoT a ogni messaggio da cloud a dispositivo. |
| To | Usata nei messaggi [da cloud a dispositivo](#c2d) per specificare la destinazione. |
| ExpiryTimeUtc | Data e ora della scadenza del messaggio. |
| EnqueuedTime | Data e ora di ricezione del messaggio da parte dell'hub IoT. |
| CorrelationId | Proprietà di stringa in un messaggio di risposta che contiene in genere il valore MessageId della richiesta nei modelli richiesta-risposta. |
| UserId | Usata per specificare l'origine dei messaggi. Quando i messaggi vengono generati dall'hub IoT, viene impostata su `{iot hub name}`. |
| Ack | Usata nei messaggi da cloud a dispositivo per richiedere all'hub IoT di generare messaggi con commenti come risultato dell'utilizzo del messaggio da parte del dispositivo. Di seguito sono indicati i valori possibili: **none** (predefinito): non viene generato alcun messaggio con commenti. **positive**: se il messaggio è stato completato, viene ricevuto un messaggio con commenti. **negative**: se il messaggio è scaduto o se è stato raggiunto il numero massimo di recapiti senza il completamento da parte del dispositivo, viene ricevuto un messaggio con commenti. **full**: esito positivo e negativo. Per altre informazioni, vedere [Commenti sul messaggio](#feedback). |
| ConnectionDeviceId | Impostata dall'hub IoT sui messaggi da dispositivo a cloud. Contiene il valore **deviceId** del dispositivo che ha inviato il messaggio. |
| ConnectionDeviceGenerationId | Impostata dall'hub IoT sui messaggi da dispositivo a cloud. Contiene il valore [generationId](#deviceproperties) (come indicato in **Proprietà delle identità dei dispositivi**) del dispositivo che ha inviato il messaggio. |
| ConnectionAuthMethod | Impostata dall'hub IoT sui messaggi da dispositivo a cloud. Informazioni sul metodo di autenticazione usato per autenticare il dispositivo che invia il messaggio. Per altre informazioni, vedere la sezione relativa all'[anti-spoofing nei messaggi da dispositivo a cloud](#antispoofing).|

### Scelta del protocollo di comunicazione <a id="amqpvshttp"></a>

L'hub IoT supporta i protocolli [AMQP][lnk-amqp], AMQP su WebSockets, MQTT e HTTP/1 per le comunicazioni sul lato dispositivo. Ecco di seguito un elenco di considerazioni relative ai rispettivi usi.

* **Modello da cloud a dispositivo**. HTTP/1 non offre un modo efficiente per implementare il push del server. Di conseguenza, quando si usa HTTP/1 i dispositivi eseguono il polling dell'hub IoT per i messaggi da cloud a dispositivo. Questa procedura è molto inefficiente per il dispositivo e per l'hub IoT. Secondo le linee guida correnti, quando si usa HTTP/1 è opportuno impostare l'esecuzione del polling di ogni dispositivo ogni 25 minuti o più. AMQP e MQTT supportano invece il push del server durante la ricezione di messaggi da cloud a dispositivo e abilitano i push immediati dei messaggi dall'hub IoT al dispositivo. Se la latenza del recapito rappresenta un problema, è consigliabile usare il protocollo AMQP o MQTT. Per i dispositivi raramente connessi è invece possibile usare HTTP/1.
* **Gateway sul campo** Quando si usano HTTP/1 e MQTT, non è possibile connettere più dispositivi, ognuno con le proprie credenziali per dispositivo, con la stessa connessione TLS. Ne consegue che questi protocolli non sono ottimali nel caso di implementazione di [scenari di gateway sul campo][lnk-azure-gateway-guidance], perché richiedono una connessione TLS tra il gateway sul campo e l'hub IoT per ogni dispositivo connesso al gateway sul campo.
* **Dispositivi con risorse ridotte**. Le librerie di MQTT e HTTP/1 hanno un footprint inferiore rispetto alle librerie di AMQP. Se il dispositivo ha poche risorse, ad esempio meno di 1 MB di RAM, questi protocolli potrebbero essere l'unica implementazione di protocollo disponibile.
* **Attraversamento rete**. Lo standard MQTT è in ascolto sulla porta 8883. Ciò potrebbe provocare problemi nelle reti chiuse ai protocolli non HTTP. HTTP e AMQP, su WebSockets, sono entrambi disponibili per l'uso in questo scenario.
* **Dimensioni del payload**. AMQP e MQTT sono protocolli binari significativamente più compatti rispetto a HTTP/1.

In generale, è consigliabile usare AMQP, o AMQP su WebSockets, quando possibile e usare MQTT solo quando i vincoli relativi alle risorse impediscono l'uso di AMQP. È opportuno usare HTTP/1 solo se l'attraversamento e la configurazione della rete impediscono entrambi l'uso di MQTT e AMQP. Quando si usa HTTP/1, ogni dispositivo deve anche eseguire il polling dei messaggi da cloud a dispositivo ogni 25 minuti o più.

> [AZURE.NOTE] Durante lo sviluppo è ovviamente accettabile eseguire il polling con una frequenza maggiore di 25 minuti.

#### Note sul supporto di MQTT
L'hub IoT implementa il protocollo MQTT v3.1.1 con le limitazioni e il comportamento specifico seguenti:

  * **QoS 2 non è supportato**: quando un client del dispositivo pubblica un messaggio con **QoS 2**, l'hub IoT chiude la connessione di rete. Quando un client del dispositivo sottoscrive un argomento con **QoS 2**, l'hub IoT concede il livello QoS 1 massimo nel pacchetto **SUBACK**.
  * **Mantenimento**: se un client del dispositivo pubblica un messaggio con il flag RETAIN impostato su 1, l'hub IoT aggiunge al messaggio la proprietà dell'applicazione **x-opt-retain**. Ciò significa che l'hub IoT non rende persistente il messaggio di mantenimento, ma lo passa invece all'applicazione back-end.

È infine consigliabile vedere [Gateway del protocollo Azure IoT][lnk-azure-protocol-gateway], che descrive come sia possibile distribuire un gateway del protocollo personalizzato a prestazioni elevate che si interfaccia direttamente con l'hub IoT. Il gateway del protocollo Azure IoT consente di personalizzare il protocollo del dispositivo per supportare le distribuzioni di MQTT cosiddette "brownfield" o altri protocolli personalizzati. Questo approccio comporta un compromesso a causa del requisito relativo al self-hosting e alla gestione di un gateway del protocollo personalizzato.

### Dispositivo a cloud <a id="d2c"></a>

Come illustrato nella sezione [Endpoint](#endpoints), i messaggi da dispositivo a cloud vengono inviati tramite un endpoint per il dispositivo (**/devices/{deviceId}/messages/events**) e vengono ricevuti tramite un endpoint per il servizio (**/messages/events**), compatibile con [Hub eventi][lnk-event-hubs]. È quindi possibile usare l'integrazione standard di Hub eventi e gli SDK per ricevere i messaggi da dispositivo a cloud.

L'hub IoT implementa la messaggistica da dispositivo a cloud in modo molto simile agli [hub eventi][lnk-event-hubs]. I messaggi da dispositivo a cloud dell'hub IoT sono molto più simili agli *eventi* degli hub eventi rispetto ai *messaggi* del [bus di servizio][lnk-servicebus].

Questa implementazione presenta le implicazioni seguenti:

* Analogamente agli *eventi* degli hub eventi, i messaggi da dispositivo a cloud sono durevoli e vengono mantenuti in un hub IoT per un massimo di 7 giorni. Vedere [Opzioni di configurazione da dispositivo a cloud](#d2cconfiguration).
* I messaggi da dispositivo a cloud vengono partizionati in un set fisso di partizioni definito in fase di creazione. Vedere la sezione [Opzioni di configurazione da dispositivo a cloud](#d2cconfiguration).
* Analogamente agli hub eventi, i client che leggono i messaggi da dispositivo a cloud devono gestire le partizioni e l'impostazione del checkpoint. Vedere [Hub eventi - Utilizzo di eventi][lnk-event-hubs-consuming-events].
* Analogamente agli eventi degli hub eventi, i messaggi da dispositivo a cloud possono avere dimensioni massime pari a 256 KB e possono essere raggruppati in batch per ottimizzare gli invii. I batch possono avere dimensioni massime di 256 KB e possono includere al massimo 500 messaggi.

Esistono tuttavia alcune differenze tra i messaggi da dispositivo a cloud dell'hub IoT e gli hub eventi:

* Come illustrato nella sezione [Sicurezza](#security), l'hub IoT consente l'autenticazione per i singoli dispositivi e il controllo di accesso.
* L'hub IoT consente milioni di dispositivi connessi simultaneamente (vedere [Quote e limitazione](#throttling)), mentre gli hub eventi sono limitati a 5000 connessioni AMQP per ogni spazio dei nomi.
* L'hub IoT non consente il partizionamento arbitrario con **PartitionKey**. I messaggi da dispositivo a cloud vengono partizionati in base al relativo **deviceId** di origine.
* Il ridimensionamento dell'hub IoT è leggermente diverso rispetto agli hub eventi. Per altre informazioni, vedere [Ridimensionamento dell'hub IoT][lnk-guidance-scale].

Si noti che ciò non significa che sia possibile sostituire Hub eventi con l'hub IoT in tutti gli scenari. Ad esempio, in alcuni calcoli di elaborazione eventi potrebbe essere necessario ripartizionare gli eventi rispetto a una proprietà o un campo diverso prima di analizzare i flussi dei dati. In questo scenario è possibile usare Hub eventi per disaccoppiare due porzioni della pipeline di elaborazione dei flussi. Per altre informazioni, vedere *Partizioni* in [Panoramica di Hub eventi di Azure][lnk-eventhub-partitions].

Per informazioni dettagliate sull'uso della messaggistica da dispositivo a cloud, vedere [API e SDK dell'hub IoT][lnk-apis-sdks].

> [AZURE.NOTE] Quando si usa HTTP per inviare messaggi da dispositivo a cloud, le stringhe seguenti possono contenere solo caratteri ASCII: valori delle proprietà di sistema e nomi e valori delle proprietà delle applicazioni.

#### Traffico non di telemetria

In molti casi, oltre ai punti dati di telemetria, i dispositivi inviano anche messaggi e richieste che devono essere eseguiti e gestiti dal livello della logica di business dell'applicazione. Ad esempio, avvisi critici che devono attivare un'azione specifica nel back-end o risposte a comandi inviati dal back-end.

Per altre informazioni sul modo migliore per elaborare questo tipo di messaggi, vedere [Elaborazione di messaggi da dispositivo a cloud][lnk-guidance-d2c-processing].

#### Opzioni di configurazione da dispositivo a cloud <a id="d2cconfiguration"></a>

Un hub IoT espone le proprietà seguenti per consentire il controllo della messaggistica da dispositivo a cloud.

* **Conteggio partizioni**. Impostare questa proprietà in fase di creazione per definire il numero di partizioni per l'inserimento di eventi da dispositivo a cloud.
* **Periodo di memorizzazione**. Questa proprietà specifica il periodo di memorizzazione per i messaggi da dispositivo a cloud. Il valore predefinito è un giorno, ma può essere aumentato a sette giorni.

Analogamente a Hub eventi, l'hub IoT consente di gestire i Gruppi di consumer nell'endpoint di ricezione da dispositivo a cloud.

È possibile modificare tutte queste proprietà tramite il [portale di Azure][lnk-management-portal] o a livello di codice mediante [Hub IoT di Azure - API del provider di risorse][lnk-resource-provider-apis].

#### Proprietà anti-spoofing <a id="antispoofing"></a>

Per evitare lo spoofing di dispositivi nei messaggi da dispositivo a cloud, l'hub IoT contrassegna tutti i messaggi con le proprietà seguenti:

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

Le prime due contengono le proprietà **deviceId** e **generationId** del dispositivo di origine, come indicato in [Proprietà delle identità dei dispositivi](#deviceproperties).

La proprietà **ConnectionAuthMethod** contiene un oggetto serializzato JSON con le proprietà seguenti:

```
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

### Cloud a dispositivo <a id="c2d"></a>

Come descritto nella sezione [Endpoint](#endpoints), è possibile inviare messaggi da cloud a dispositivo tramite un endpoint per il servizio (**/messages/devicebound**) e il dispositivo può riceverli tramite un endpoint specifico per il dispositivo (**/devices/{deviceId}/messages/devicebound**).

Ogni messaggio da cloud a dispositivo è destinato a un singolo dispositivo, quindi la proprietà **to** sarà impostata su **/devices/{deviceId}/messages/devicebound**.

**Importante**: ogni coda di dispositivo può includere al massimo 50 messaggi da cloud a dispositivo. Se si prova a inviare più messaggi allo stesso dispositivo, si verificherà un errore.

> [AZURE.NOTE] Quando si inviano messaggi da cloud a dispositivo, le stringhe seguenti possono contenere solo caratteri ASCII: valori delle proprietà di sistema e nomi e valori delle proprietà delle applicazioni.

#### Ciclo di vita del messaggio <a id="message lifecycle"></a>

Per implementare la garanzia di recapito di tipo *almeno una volta*, i messaggi da cloud a dispositivo vengono resi persistenti nelle code per dispositivo e i dispositivi devono riconoscere esplicitamente il *completamento* per consentire all'hub IoT di rimuoverli dalla coda. Ciò garantisce la resilienza rispetto a errori di connettività e dei dispositivi.

Il diagramma seguente illustra il grafico sullo stato del ciclo di vita per un messaggio da cloud a dispositivo.

![Ciclo di vita dei messaggi da cloud a dispositivo][img-lifecycle]

Quando il servizio invia un messaggio, questo viene considerato *Accodato*. Quando un dispositivo accetta di *ricevere* un messaggio, l'hub IoT lo *blocca* impostando lo stato su **Invisibile**, per consentire ad altri thread nello stesso dispositivo di iniziare a ricevere altri messaggi. Quando un thread del dispositivo termina l'elaborazione, invia una notifica all'hub IoT *completando* il messaggio.

Un dispositivo può anche: - *Rifiutare* il messaggio e in tal caso l'hub IoT ne imposta lo stato su **Non recapitabile**. - *Abbandonare* il messaggio e in tal caso l'hub IoT lo inserisce nuovamente nella coda e ne imposta lo stato su **Accodato**.

Un thread potrebbe non riuscire a elaborare un messaggio senza inviare una notifica all'hub IoT. In tal caso, i messaggi passano automaticamente di nuovo dallo stato **Invisibile** allo stato **Accodato** dopo un *timeout di visibilità o di blocco* con un valore predefinito di un minuto. Un messaggio può passare dallo stato **Accodato** allo stato **Invisibile** e viceversa per il numero massimo di volte specificato nella proprietà *max delivery count* nell'hub IoT. Dopo tale numero di transizioni, l'hub IoT imposta lo stato del messaggio su **Non recapitabile**. Analogamente, l'hub IoT imposta lo stato del messaggio su **Non recapitabile** dopo la relativa scadenza. Vedere la sezione [Durata (TTL)](#ttl).

Per un'esercitazione sui messaggi da cloud a dispositivo, vedere [Introduzione ai messaggi da cloud a dispositivo dell'hub IoT di Azure][lnk-getstarted-c2d-tutorial]. Per argomenti di riferimento sul modo in cui le diverse API e i vari SDK espongono la funzionalità da cloud a dispositivo, vedere [API e SDK dell'hub IoT][lnk-apis-sdks].

> [AZURE.NOTE] I messaggi da cloud a dispositivo vengono in genere completati quando la perdita del messaggio non influisce sulla logica dell'applicazione. Questa situazione può verificarsi in molti scenari diversi. Ad esempio, il contenuto del messaggio è stato reso persistente nell'archivio locale o un'operazione è stata eseguita correttamente oppure il messaggio include informazioni temporanee la cui perdita non influirebbe sulla funzionalità dell'applicazione. In alcuni casi, per le attività con esecuzione prolungata è possibile completare il messaggio da cloud a dispositivo dopo aver reso persistente la descrizione dell'attività nell'archivio locale e quindi inviare al back-end dell'applicazione una notifica con uno o più messaggi da dispositivo a cloud in diverse fasi di avanzamento dell'attività.

#### Durata (TTL) <a id="ttl"></a>

Ogni messaggio da cloud a dispositivo ha una scadenza. Questa impostazione può essere definita esplicitamente dal servizio nella proprietà **ExpiryTimeUtc** oppure viene configurata dall'hub IoT usando il valore di *durata (TTL)* predefinito specificato come proprietà dell'hub IoT. Vedere [Opzioni di configurazione da cloud a dispositivo](#c2dconfiguration).

#### Commenti sul messaggio <a id="feedback"></a>

Durante l'invio di messaggi da cloud a dispositivo, il servizio può richiedere il recapito di commenti specifici per ogni messaggio in merito allo stato finale del messaggio.

- Se si imposta la proprietà **Ack** su **positive**, l'hub IoT genera un messaggio di commenti se e solo se il messaggio da cloud a dispositivo ha raggiunto lo stato **Completato**.
- Se si imposta la proprietà **Ack** su **negative**, l'hub IoT genera un messaggio di commenti se e solo se il messaggio da cloud a dispositivo ha raggiunto lo stato **Non recapitabile**.
- Se si imposta la proprietà **Ack** su **full**, l'hub IoT genera un messaggio di commenti in entrambi i casi.

> [AZURE.NOTE] Se **Ack** è **full** e non viene ricevuto alcun messaggio di feedback significa che il messaggio di feedback è scaduto e il servizio non può sapere che cosa è successo al messaggio originale. In pratica, un servizio deve garantire che sia possibile elaborare i commenti prima della scadenza. Poiché il periodo di scadenza massimo è di due giorni, è disponibile un tempo sufficiente per garantire il funzionamento del servizio in caso di errore.

Come illustrato nella sezione [Endpoint](#endpoints), l'hub IoT invia commenti tramite un endpoint per il servizio (**/messages/servicebound/feedback**) sotto forma di messaggi. La semantica di ricezione per i commenti è uguale a quella dei messaggi da cloud a dispositivo e ha lo stesso [Ciclo di vita dei messaggi](#ciclo di vita dei messaggi). Quando possibile, i commenti sul messaggio vengono riuniti in un singolo messaggio con il formato seguente.

Ogni messaggio recuperato da un dispositivo dall'endpoint di commenti ha le proprietà seguenti:

| Proprietà | Descrizione |
| -------- | ----------- |
| EnqueuedTime | Timestamp che indica quando è stato creato il messaggio. |
| UserId | `{iot hub name}` |
| ContentType | `application/vnd.microsoft.iothub.feedback.json` |

Il corpo è una matrice serializzata con JSON dei record, ognuno con le proprietà seguenti:

| Proprietà | Descrizione |
| -------- | ----------- |
| EnqueuedTimeUtc | Timestamp che indica quando è stato creato il risultato del messaggio. Ad esempio, il dispositivo ha completato l'operazione o il messaggio è scaduto. |
| OriginalMessageId | **MessageId** del messaggio da cloud a dispositivo correlato a queste informazioni sui commenti. |
| StatusCode | Numero intero obbligatorio. Usato nei messaggi con commenti e suggerimenti generati dall'hub IoT. <br/> 0 = esito positivo <br/> 1 = messaggio scaduto <br/> 2 = conteggio distribuzione massimo superato <br/> 3 = messaggio rifiutato |
| Descrizione | Valori stringa per **StatusCode**. |
| DeviceId | **DeviceId** del messaggio da cloud a dispositivo correlato a queste informazioni sui commenti. |
| DeviceGenerationId | **DeviceGenerationId** del dispositivo di destinazione del messaggio da cloud a dispositivo correlato a queste informazioni sui commenti. |


**Importante**. Il servizio deve specificare un valore **MessageId** per il messaggio da cloud a dispositivo per potere correlare i rispettivi commenti al messaggio originale.

**Esempio**. Ecco un esempio di corpo di un messaggio con commenti.

```
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

#### Opzioni di configurazione da cloud a dispositivo <a id="c2dconfiguration"></a>

Ogni hub IoT espone le opzioni di configurazione seguenti per la messaggistica da cloud a dispositivo.

| Proprietà | Descrizione | Intervallo e valore predefinito |
| -------- | ----------- | ----------------- |
| defaultTtlAsIso8601 | Durata (TTL) predefinita per messaggi da cloud a dispositivo. | Intervallo ISO\_8601 fino a 2D (almeno 1 minuto). Predefinito: 1 ora. |
| maxDeliveryCount | Numero massimo di recapiti per code da cloud a dispositivo per i singoli dispositivi. | Da 1 a 100. Predefinito: 10. |
| feedback.ttlAsIso8601 | Conservazione per messaggi con commenti diretti al servizio. | Intervallo ISO\_8601 fino a 2D (almeno 1 minuto). Predefinito: 1 ora. |
| feedback.maxDeliveryCount | Numero massimo di recapiti per la coda di commenti. | Da 1 a 100. Predefinito: 100. |

Per altre informazioni, vedere [Gestire hub IoT][lnk-manage].

## Quote e limitazione <a id="throttling"></a>

Ogni sottoscrizione di Azure può avere al massimo 10 hub IoT.

Ogni hub IoT viene sottoposto a provisioning con un determinato numero di unità in uno SKU specifico (per altre informazioni, vedere [Prezzi dell'hub IoT di Azure][lnk-pricing].) Lo SKU e il numero di unità determinano la quota giornaliera massima dei messaggi che è possibile inviare.

Lo SKU determina anche le limitazioni che l'hub IoT applica alle operazioni.

### Limitazioni per le operazioni

Le limitazioni per le operazioni sono limitazioni di frequenza applicate con intervalli di minuti e consentono di evitare abusi. L'hub IoT prova a evitare la restituzione di errori, se possibile, ma inizia a restituire eccezioni se la limitazione viene violata troppo a lungo.

Ecco di seguito l'elenco di limitazioni applicate. I valori fanno riferimento a un singolo hub.

| Limitazione | Valore per ogni hub |
| -------- | ------------- |
| Operazioni del registro delle identità (creazione, recupero, elenco, aggiornamento, eliminazione) | 100/min/unità, fino a 5000/min |
| Connessioni del dispositivo | 120/sec/unità (per S2), 12/sec/unità (per S1). <br/>Almeno 100/sec. <br/> Ad esempio, due unità S1 sono 2*12 = 24/sec, ma si otterrà almeno 100/sec tra le unità. Con nove unità S1 si otterrà 108/sec (9*12) tra le unità. |
| Inoltri dal dispositivo al cloud | 120/sec/unità (per S2), 12/sec/unità (per S1). <br/>Almeno 100/sec. <br/> Ad esempio, due unità S1 sono 2*12 = 24/sec, ma si otterrà almeno 100/sec tra le unità. Con nove unità S1 si otterrà 108/sec (9*12) tra le unità. |
| Inoltri dal cloud al dispositivo | 100/min/unità |
| Ricezioni dal cloud al dispositivo | 1000/min/unità |

**Nota**. È possibile incrementare le quote o le limitazioni in qualsiasi momento aumentando il numero di unità sottoposte a provisioning in un hub IoT.

**Importante**: le operazioni del registro delle identità sono destinate all'uso in fase di esecuzione negli scenari di gestione e provisioning di dispositivi. La lettura o l'aggiornamento di un numero elevato di identità dei dispositivi è supportato tramite [processi di importazione ed esportazione](#importexport).

## Passaggi successivi

Al termine di questa panoramica dello sviluppo per l'hub IoT, è possibile usare i collegamenti seguenti per altre informazioni:

- [Introduzione agli hub IoT (esercitazione)][lnk-get-started]
- [Piattaforme del sistema operativo e compatibilità hardware][lnk-compatibility]
- [Centro per sviluppatori Azure IoT][lnk-iotdev]
- [Progettare la soluzione][lnk-guidance]

[host processore di eventi di Hub eventi]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx

[portale di Azure]: https://portal.azure.com

[img-endpoints]: ./media/iot-hub-devguide/endpoints.png
[img-lifecycle]: ./media/iot-hub-devguide/lifecycle.png
[img-eventhubcompatible]: ./media/iot-hub-devguide/eventhubcompatible.png

[lnk-compatibility]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[lnk-apis-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx

[lnk-azure-gateway-guidance]: iot-hub-guidance.md#field-gateways
[lnk-guidance-provisioning]: iot-hub-guidance.md#provisioning
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-guidance-security]: iot-hub-guidance.md#customauth
[lnk-guidance-heartbeat]: iot-hub-guidance.md#heartbeat

[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-guidance]: iot-hub-guidance.md
[lnk-getstarted-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md

[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../resource-group-overview.md
[lnk-azure-resource-manager]: https://azure.microsoft.com/documentation/articles/resource-group-overview/
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-createuse-sas]: ../storage-dotnet-shared-access-signature-part-2/
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-event-hubs-consuming-events]: ../event-hubs/event-hubs-programming-guide.md#event-consumers
[lnk-guidance-d2c-processing]: iot-hub-csharp-csharp-process-d2c.md
[lnk-management-portal]: https://portal.azure.com
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-tls]: https://tools.ietf.org/html/rfc5246
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-eventhub-partitions]: event-hubs-overview.md#partitions
[lnk-manage]: iot-hub-manage-through-portal.md

<!----HONumber=AcomDC_0211_2016-->