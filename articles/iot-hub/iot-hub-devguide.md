<properties
 pageTitle="Guida per gli sviluppatori dell'hub IoT di Azure | Microsoft Azure"
 description="Guida per gli sviluppatori dell'hub IoT Azure, con informazioni su endpoint, sicurezza, registro delle identità del dispositivo e messaggistica per l'hub IoT."
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="09/29/2015"
 ms.author="elioda"/>

# Guida per gli sviluppatori dell'hub IoT di Azure

L'hub IoT di Azure è un servizio completamente gestito che abilita le comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi IoT e un back-end applicazione.

L'hub IoT di Azure offre questi vantaggi:

* Comunicazioni sicure mediante le credenziali di sicurezza per i singoli dispositivi e il controllo di accesso.
* Messaggistica affidabile da dispositivo a cloud e da cloud a dispositivo su scala molto elevata.
* Connettività semplice dei dispositivi con librerie di dispositivi per i linguaggi e le piattaforme più diffusi.

Questo documento esamina le aree seguenti:

- [Endpoint](#endpoints). Questa sezione illustra i diversi endpoint esposti da ogni hub IoT per operazioni della fase di esecuzione e di gestione.
- [Registro delle identità dei dispositivi](#device-identity-registry). Questa sezione illustra le informazioni archiviate nel registro delle identità dei dispositivi di ogni hub IoT e spiega come accedere alle informazioni e modificarle.
- [Sicurezza](#security). Questa sezione illustra il modello di sicurezza per i dispositivi e i componenti cloud usati per concedere l'accesso alla funzionalità hub IoT.
- [Messaggistica](#messaging). Questa sezione descrive le funzionalità di messaggistica da dispositivo a cloud e da cloud a dispositivo esposte dall'hub IoT.
- [Quote e limitazione](#throttling). Questa sezione riepiloga le quote applicabili all'uso dell'hub IoT.

## Endpoint <a id="endpoints"></a>

L'hub IoT di Azure è un servizio multi-tenant ed espone funzionalità a svariati attori. Il diagramma seguente mostra i diversi endpoint esposti dall'hub IoT.

![Endpoint hub IoT][img-endpoints]

Ecco di seguito una descrizione degli endpoint:

* **Provider di risorse**: il provider di risorse dell'hub IoT espone un'interfaccia di [Gestione risorse di Azure][lnk-arm] che consente ai proprietari di sottoscrizioni di Azure di creare hub IoT, aggiornare le proprietà dell'hub IoT ed eliminare hub IoT. Le proprietà dell'hub IoT disciplinano i criteri di sicurezza a livello di hub, al contrario del controllo di accesso a livello di dispositivo. Vedere [Controllo di accesso](#accesscontrol)) e le opzioni funzionali per la messaggistica da cloud a dispositivo e da dispositivo a cloud. Il provider di risorse consente anche di [esportare le identità dei dispositivi](#importexport).
* **Gestione delle identità dei dispositivi**: ogni hub IoT espone un set di endpoint REST HTTP per gestire le identità dei dispositivi, per operazioni di creazione, recupero, aggiornamento ed eliminazione. Le identità dei dispositivi vengono usate per l'autenticazione dei dispositivi e il controllo di accesso. Per altre informazioni, vedere [Registro delle identità dei dispositivi](#device-identity-registry).
* **Endpoint del dispositivo**: per ogni dispositivo sottoposto a provisioning nel registro delle identità dei dispositivi, l'hub IoT espone un set di endpoint usati per comunicazioni verso e dal dispositivo. In HTTP e [AMQP][lnk-amqp] vengono attualmente esposti questi endpoint:
    - *Invio di messaggi da dispositivo a cloud*. Questo endpoint viene usato per inviare messaggi da dispositivo a cloud. Per altre informazioni, vedere [Messaggistica da dispositivo a cloud](#d2c).
    - *Ricezione di messaggi da cloud a dispositivo*. Questo endpoint viene usato dal dispositivo per ricevere messaggi da cloud a dispositivo specifici. Per altre informazioni, vedere [Messaggistica da cloud a dispositivo](#c2d).
* **Endpoint di servizio**: ogni hub IoT espone anche un set di endpoint usati dal (*servizio*) back-end applicazione per comunicare con i dispositivi in uso. Questi endpoint vengono attualmente esposti solo tramite il protocollo [AMQP][lnk-amqp].
    - *Ricezione di messaggi da dispositivo a cloud*. Questo endpoint è compatibile con gli [hub eventi di Azure][lnk-event-hubs] e può essere usato per leggere tutti i messaggi da dispositivo a cloud inviati dai dispositivi. Per altre informazioni, vedere [Messaggistica da dispositivo a cloud](#d2c).
    - *Invio di messaggi da cloud a dispositivo e ricezione di acknowledgement di recapito*. Questi endpoint consentono al back-end dell'applicazione di inviare messaggi affidabili da cloud a dispositivo e di ricevere gli acknowledgement di recapito o di scadenza corrispondenti. Per altre informazioni, vedere [Messaggistica da cloud a dispositivo](#c2d).

L'articolo [API e SDK dell'hub IoT][lnk-apis-sdks] illustra i diversi modi in cui è possibile accedere agli endpoint.

È infine importante notare che tutti gli endpoint dell'hub IoT vengono esposti tramite [TLS][lnk-tls] e nessun endpoint viene mai esposto in canali non crittografati o non sicuri.

### Come leggere dagli endpoint compatibili con l'hub eventi. <a id="eventhubcompatible"></a>

Quando si usa [Azure Service Bus SDK per .NET](https://www.nuget.org/packages/WindowsAzure.ServiceBus) o [Hub eventi - Host processore di eventi][], è possibile usare qualsiasi stringa di connessione dell'hub IoT con le autorizzazioni corrette e quindi usare `messages/events` come nome dell'hub eventi.

Quando si usano gli SDK o le integrazioni del prodotto non compatibili con l'hub IoT, è necessario recuperare un endpoint compatibile con gli hub eventi e un nome di hub eventi dalle impostazioni dell'hub IoT nel [portale di anteprima di Azure][]\:

1. Nel pannello dell'hub IoT fare clic su **Impostazioni**, quindi su **Messaggistica**.
2. La sezione **Impostazioni da dispositivo a cloud** include una casella **Endpoint compatibile con l'hub eventi**, **Nome compatibile con l'hub eventi** e **Partizioni**.

    ![][img-eventhubcompatible]

> [AZURE.NOTE]A volte l'SDK richiede un valore per **Nome host** o **Spazio dei nomi**. Sarà quindi necessario rimuovere lo schema dall'**Endpoint compatibile con l'hub eventi**. Ad esempio, se l'endpoint compatibile con l'hub eventi è `sb://iothub-ns-myiothub-1234.servicebus.windows.net/`, il **Nome host** sarà `iothub-ns-myiothub-1234.servicebus.windows.net` e lo **Spazio dei nomi** sarà `iothub-ns-myiothub-1234`.

È quindi possibile usare qualsiasi criterio di sicurezza di accesso condiviso con autorizzazioni **ServiceConnect** per la connessione all'hub eventi specificato.

Se si deve compilare una stringa di connessione dell'hub eventi con le informazioni precedenti, è possibile usare il modello seguente:

        Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}


Ecco di seguito un elenco di SDK e integrazioni che possono essere usati con l'hub IoT:

* [Client degli hub eventi Java](https://github.com/hdinsight/eventhubs-client).
* [Spout di Apache Storm](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md). Il codice sorgente dello spout è disponibile [qui](https://github.com/apache/storm/tree/master/external/storm-eventhubs).
* [Integrazione con Apache Spark](../hdinsight/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md).

## Registro delle identità dei dispositivi

Ogni hub IoT include un registro delle identità dei dispositivi, che viene usato per creare risorse per i singoli dispositivi nel servizio, ad esempio una coda contenente messaggi da cloud a dispositivo in elaborazione, e consente l'accesso agli endpoint per il dispositivo, come illustrato nella sezione [Controllo di accesso](#accesscontrol).

A livello generale, il registro delle identità dei dispositivi è una raccolta delle risorse relative alle identità dei dispositivi che supporta REST. Le sezioni seguenti illustrano in modo dettagliato le proprietà delle risorse relative alle identità dei dispositivi e le operazioni consentite dal registro sulle identità.

> [AZURE.NOTE]Per altre informazioni sul protocollo HTTP e sugli SDK disponibili per l'interazione con il registro delle identità dei dispositivi, vedere [API e SDK dell'hub IoT][lnk-apis-sdks].

### Proprietà delle identità dei dispositivi <a id="deviceproperties"></a>

Le identità dei dispositivi vengono rappresentate da documenti JSON con le proprietà seguenti.

| Proprietà | Opzioni | Descrizione |
| -------- | ------- | ----------- |
| deviceId | Obbligatoria, di sola lettura negli aggiornamenti | Stringa con distinzione tra maiuscole/minuscole (al massimo 128 caratteri di lunghezza) di caratteri alfanumerici ASCII a 7 bit + `{'-', ':', '.', '+', '&percnt;', '_', '&num;', '&ast;', '?', '!', '(', ')', ',', '=', '&commat;', ';', '&dollar;', '''}`. |
| generationId | Obbligatoria, di sola lettura | Stringa con distinzione tra maiuscole/minuscole generata dall'hub con lunghezza massima di 128 caratteri. Viene usata per distinguere i dispositivi con lo stesso valore **deviceId** in caso di eliminazione e nuova creazione. |
| etag | Obbligatoria, di sola lettura | Stringa che rappresenta un eTag vulnerabile per l'identità del dispositivo, come indicato in [RFC7232][lnk-rfc7232].|
| auth | Facoltativa | Oggetto composito contenente le informazioni di autenticazione e i materiali di sicurezza. |
| auth.symkey | Facoltativa | Oggetto composito contenente una chiave primaria e una chiave secondaria, archiviate in formato Base 64. |
| status | Obbligatoria | Può essere **Enabled** o **Disabled**. Se è **Enabled**, il dispositivo sarà autorizzato alla connessione. Se è **Disabled**, il dispositivo non potrà accedere ad alcun endpoint per il dispositivo. |
| statusReason | Facoltativa | Stringa con 128 caratteri di lunghezza che archivia il motivo dello stato dell'identità del dispositivo. Sono consentiti tutti i caratteri UTF-8. |
| statusUpdateTime | Sola lettura | Data e ora dell'ultimo aggiornamento dello stato. |
| connectionState | Sola lettura | **Connected** o **Disconnected**, rappresenta la visualizzazione Hub IoT dello stato di connessione del dispositivo. |
| connectionStateUpdatedTime | Sola lettura | Data e ora dell'ultimo aggiornamento dello stato della connessione. |
| lastActivityTime | Sola lettura | Data e ora in cui il dispositivo si è connesso, ha ricevuto o inviato un messaggio per l'ultima volta. |

> [AZURE.NOTE]Lo stato della connessione può rappresentare solo la visualizzazione Hub IoT dello stato della connessione. Questo stato può essere ritardato in base alle condizioni e le configurazioni della rete.

### Operazioni relative alle identità dei dispositivi

Il registro delle identità dei dispositivi dell'hub IoT espone le operazioni seguenti:

* Creare l'identità del dispositivo
* Aggiornare l'identità del dispositivo
* Recuperare l'identità del dispositivo tramite ID
* Eliminare l'identità del dispositivo
* Elencare al massimo 1000 identità

Tutte queste operazioni consentono l'uso della concorrenza ottimistica, come illustrato in [RFC7232][lnk-rfc7232].

> [AZURE.IMPORTANT]L'unico modo per recuperare tutte le identità nel registro delle identità di un hub consiste nell'usare la funzionalità [Esporta](#importexport).

Il registro delle identità dei dispositivi di un hub non contiene metadati delle applicazioni, è accessibile come un dizionario tramite il valore **deviceId** come chiave e non supporta le query espressive. Qualsiasi soluzione IoT includerà un archivio specifico per la soluzione, che contiene metadati specifici dell'applicazione, ad esempio l'ambiente in cui è stato distribuito un sensore della temperatura in una soluzione per un edificio efficiente.

### Disabilitazione di dispositivi

È possibile disabilitare i dispositivi aggiornando la proprietà **status** di un'identità nel registro. Questo approccio viene in genere usato in due scenari:

1. Durante un processo di orchestrazione di provisioning. Per altre informazioni, vedere [Indicazioni per l'hub IoT di Azure - Provisioning][lnk-guidance-provisioning].
2. Per qualsiasi motivo, si considera un dispositivo compromesso o temporaneamente non autorizzato.

### Esportare le identità dei dispositivi<a id="importexport"></a>

Le esportazioni sono processi con esecuzione prolungata che usano un contenitore BLOB fornito dal cliente per leggere e scrivere i dati relativi alle identità dei dispositivi.

È possibile esportare in blocco le identità dei dispositivi da un registro delle identità di un hub IoT, tramite operazioni asincrone sull'endpoint del provider di risorse dell'hub IoT ([Endpoint](#endpoints)).

Ecco di seguito le operazioni consentite nei processi di esportazione:

* Creare un processo di esportazione
* Recuperare lo stato di un processo in esecuzione
* Annullare un processo in esecuzione

> [AZURE.NOTE]Ogni hub può avere solo un processo in esecuzione in un determinato momento.

Per informazioni dettagliate sulle API di importazione e di esportazione, vedere [Hub IoT di Azure - API del provider di risorse][lnk-resource-provider-apis].

#### Processi

Tutti i processi di esportazione hanno le proprietà seguenti:

| Proprietà | Opzioni | Descrizione |
| -------- | ------- | ----------- |
| jobId | Generata dal sistema, ignorata in fase di creazione | |
| creationTime | Generata dal sistema, ignorata in fase di creazione | |
| endOfProcessingTime | Generata dal sistema, ignorata in fase di creazione | |
| type | Sola lettura | **Esportazione** |
| status | Generata dal sistema, ignorata in fase di creazione | **Accodato**, **Avviato**, **Completato**, **Non riuscito** |
| progress | Generata dal sistema, ignorata in fase di creazione | Valore intero della percentuale di completamento. |
| outputBlobContainerURI | Obbligatoria per tutti i processi | URI di firma di accesso condiviso BLOB con accesso in scrittura a un contenitore BLOB. Vedere [Creare e usare una firma di accesso condiviso con il servizio BLOB][lnk-createuse-sas] e [Creazione di una firma di accesso condiviso in Java][lnk-sas-java]. Viene usata per ottenere l'output dello stato del processo e dei risultati. |
| includeKeysInExport | Facoltativa | Se **true**, le chiavi verranno incluse nell'output di esportazione; in caso contrario, le chiavi verranno esportate come **Null**. Il valore predefinito è **false**. |
| failureReason | Generata dal sistema, ignorata in fase di creazione | Se lo stato è **Non riuscito**, corrisponde a una stringa contenente il motivo. |

#### Processi di esportazione

I processi di esportazione accettano come parametro un URI di firma di accesso condiviso BLOB. Ciò concede l'accesso in scrittura a un contenitore BLOB e viene usato per restituire il risultato dell'operazione.

I risultati ottenuti vengono scritti nel contenitore BLOB specificato in un file denominato `job_{job_id}_devices.txt`. Questo file include le identità dei dispositivi serializzate come JSON, come illustrato in [Proprietà delle identità dei dispositivi](#deviceproperties). I materiali di sicurezza vengono impostati su **Null** se **includeKeysInExport** è impostato su **false**.

**Esempio**:

    {"deviceId":"devA","auth":{"symKey":{"primaryKey":"123"}},"status":"enabled"}
    {"deviceId":"devB","auth":{"symKey":{"primaryKey":"234"}},"status":"enabled"}
    {"deviceId":"devC","auth":{"symKey":{"primaryKey":"345"}},"status":"enabled"}
    {"deviceId":"devD","auth":{"symKey":{"primaryKey":"456"}},"status":"enabled"}


## Sicurezza <a id="security"></a>

Questa sezione illustra le opzioni per la protezione dell'hub IoT di Azure.

### Controllo di accesso <a id="accesscontrol"></a>

L'hub IoT usa il set di *autorizzazioni* seguente per concedere l'accesso all'endpoint di ogni hub IoT. Le autorizzazioni limitano l'accesso a un hub IoT in base alla funzionalità.

* **RegistryRead**. Questa autorizzazione concede l'accesso in lettura al registro delle identità dei dispositivi. Per altre informazioni, vedere [Registro delle identità dei dispositivi](#device-identity-registry).
* **RegistryReadWrite**. Concede l'accesso in lettura e scrittura al registro delle identità dei dispositivi. Per altre informazioni, vedere [Registro delle identità dei dispositivi](#device-identity-registry).
* **ServiceConnect**. Concede l'accesso alle comunicazioni per il servizio cloud e al monitoraggio degli endpoint. Ad esempio, concede ai servizi cloud l'autorizzazione per la ricezione di messaggi da dispositivo a cloud, l'invio di messaggi da cloud a dispositivo e il recupero degli acknowledgment di recapito corrispondenti.
* **DeviceConnect**. Concede l'accesso agli endpoint di comunicazione per il dispositivo. Ad esempio, concede l'autorizzazione per l'invio di messaggi da dispositivo a cloud e la ricezione di messaggi da cloud a dispositivo. Questa autorizzazione viene usata principalmente dai dispositivi.

Le autorizzazioni vengono concesse nei modi seguenti:

* **Criteri di accesso condivisi a livello di hub**. I *criteri di accesso condiviso* possono concedere qualsiasi combinazione delle autorizzazioni elencate nella sezione precedente. È possibile definire i criteri nel [portale di gestione di Azure][lnk-management-portal] o a livello di codice mediante le [API del provider di risorse dell'hub IoT di Azure][lnk-resource-provider-apis]. Un hub IoT appena creato ha i criteri predefiniti seguenti:

    - *iothubowner*: criteri con tutte le autorizzazioni
    - *service*: criteri con autorizzazione **ServiceConnect**
    - *device*: criteri con autorizzazione **DeviceConnect**
    - *registryRead*: criteri con autorizzazione **RegistryRead**
    - *registryReadWrite*: criteri con autorizzazioni **RegistryRead** e **RegistryWrite**

* **Credenziali di sicurezza specifiche del dispositivo**. Ogni hub IoT contiene un [registro delle identità dei dispositivi](#device-identity-registry). Per ogni dispositivo in questo registro è possibile configurare credenziali di sicurezza che concedono autorizzazioni **DeviceConnect** con ambito agli endpoint di dispositivo corrispondenti.

**Esempio**. Una soluzione IoT include in genere un componente di gestione dei dispositivi che usa i criteri *registryReadWrite*, un componente di elaborazione degli eventi e un componente della logica di business di runtime che usano i criteri *service*. I singoli dispositivi si connettono usando le credenziali archiviate nel registro delle identità dell'hub IoT.

Per indicazioni sugli argomenti relativi alla sicurezza per l'hub IoT, vedere la sezione sulla sicurezza in [Indicazioni per l'hub IoT di Azure][lnk-guidance-security].

### Autenticazione

L'hub IoT di Azure concede l'accesso agli endpoint tramite la verifica di un token rispetto ai criteri di accesso condiviso e alle credenziali di sicurezza del registro delle identità dei dispositivi.

Le credenziali di sicurezza, ad esempio le chiavi asimmetriche, non vengono mai trasmesse in rete.

> [AZURE.NOTE]Il provider di risorse dell'hub IoT di Azure viene protetto tramite la sottoscrizione di Azure, analogamente a tutti i provider in [Gestione risorse di Azure][lnk-azure-resource-manager].

#### Formato del token di sicurezza <a id="tokenformat"></a>

Il token di sicurezza ha il formato seguente:

	SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}

Questi sono i valori previsti:

| Valore | Descrizione |
| ----- | ----------- |
| {signature} | Stringa della firma HMAC-SHA256 nel formato: `{URL-encoded-resourceURI} + "\n" + expiry` |
| {resourceURI} | Prefisso URI (per segmento) degli endpoint a cui è possibile accedere tramite questo token. Ad esempio, `/events` |
| {expiry} | Stringhe UTF8 per il numero di secondi trascorsi dalle 00:00:00 UTC dell'1 gennaio 1970. |
| {URL-encoded-resourceURI} | URI di risorsa con codifica URL (minuscole). |
| {policyName} | Nome del criterio di accesso condiviso a cui fa riferimento il token. Assente nel caso in cui i token facciano riferimento a credenziali del registro dei dispositivi. |

**Nota sul prefisso**: il prefisso dell'URI viene calcolato in base al segmento e non in base al carattere. Ad esempio `/a/b` è un prefisso per `/a/b/c` ma non per `/a/bc`.

#### Specifiche del protocollo

Ogni protocollo supportato, ad esempio AMQP e http, trasporta i token in modi diversi.

L'autenticazione HTTP viene implementata includendo un token valido nell'intestazione della richiesta **Authorization**. Anche un parametro di query denominato **Authorization** può trasportare il token.

Quando si usa [AMQP][lnk-amqp], l'hub IoT supporta [SASL PLAIN][lnk-sasl-plain] e la [sicurezza basata sulle attestazioni AMQP][lnk-cbs].

Nel caso della sicurezza basata sulle attestazioni AMQP, lo standard specifica come trasmettere questi token.

Per SASL PLAIN **username** può essere:

* `{policyName}&commat;sas.root.{iothubName}` nel caso di token a livello di hub.
* `{deviceId}` nel caso di token con ambito relativo al dispositivo.

In entrambi i casi il campo della password contiene il token, come illustrato nella sezione [Formato del token](#tokenformat).

> [AZURE.NOTE]Gli [Azure IoT Hub SDK][lnk-apis-sdks] generano automaticamente i token durante la connessione al servizio. In alcuni casi, gli SDK sono limitati a livello di protocolli supportati o metodi di autenticazione disponibili. Per altre informazioni, vedere la documentazione sugli [Azure IoT Hub SDK][lnk-apis-sdks].

#### Confronto tra SASL PLAIN e CBS

Quando si usa SASL PLAIN, un client che si connette a un hub IoT potrà usare un singolo token per ogni connessione TCP. Quando il token scade, la connessione TCP viene disconnessa dal servizio, attivando una riconnessione. Questo comportamento, anche se non problematico per un componente back-end applicazione, risulta molto dannoso per un'applicazione lato dispositivo, per i motivi seguenti:

*  I gateway si connettono in genere per conto di molti dispositivi. Quando si usa SASL PLAIN, devono creare una connessione TCP distinta per ogni dispositivo che si connette a un hub IoT. Ciò aumenta in modo considerevole il consumo energetico e delle risorse di rete e incrementa la latenza della connessione di ogni dispositivo.
* I dispositivi vincolati alle risorse saranno maggiormente interessati dall'aumento dell'uso delle risorse per la riconnessione dopo la scadenza di ogni token.

### Definizione dell'ambito delle credenziali a livello hub

È possibile definire l'ambito dei criteri di sicurezza a livello hub creando token con URI di risorsa con limitazioni. Ad esempio, l'endpoint per l'invio di messaggi da dispositivo a cloud da un dispositivo è `/devices/{deviceId}/events`. È anche possibile usare criteri di accesso condiviso a livello di hub con autorizzazioni **DeviceConnect** per firmare un token il cui valore resourceURI è `/devices/{deviceId}`, creando un token che può essere usato solo per inviare dispositivi per conto del dispositivo **deviceId**.

Questo meccanismo è simile ai [criteri delle entità di pubblicazione degli hub eventi][lnk-event-hubs-publisher-policy] e consente l'implementazione dei metodi di autenticazione personalizzati, come illustrato nella sezione relativa alla sicurezza delle [Indicazioni per l'hub IoT di Azure][lnk-guidance-security].

## Messaggistica

L'hub IoT consente alle primitive di messaggistica di comunicare da un back-end dell'applicazione (*servizio* o *cloud*) a dispositivi e viceversa. Queste funzionalità sono definite [Dispositivo a cloud](#d2c) e [Cloud a dispositivo](#c2d).

Le proprietà di base della funzionalità di messaggistica dell'hub IoT sono l'affidabilità e la durabilità dei messaggi. Ciò consente la resilienza in caso di connettività intermittente sul lato dispositivo e picchi di carico durante l'elaborazione degli eventi sul lato cloud. L'hub IoT implementa garanzie di recapito di tipo *almeno una volta* per la messaggistica D2C (Device-to-Cloud) e C2D (Cloud-to-Device).

L'hub IoT supporta più protocolli per il dispositivo, ad esempio AMQP e HTTP/1. Per supportare l'interoperabilità senza problemi tra i protocolli, l'hub IoT definisce un formato di messaggio comune supportato da tutti i protocolli per il dispositivo.

### Formato del messaggio <a id="messageformat"></a>

I messaggi dell'hub IoT sono costituiti da questi elementi:

* Un set di *proprietà del sistema*. Si tratta di proprietà interpretate o impostate dall'hub IoT. Il set è predeterminato.
* Un set di *proprietà dell'applicazione*. Si tratta di un dizionario di proprietà di stringa che l'applicazione può definire e a cui può accedere senza dovere deserializzare il corpo. Queste proprietà non vengono mai modificate dall'hub IoT.
* Corpo binario opaco.

Per altre informazioni sul modo in cui il messaggio viene codificato in diversi protocolli, vedere [API e SDK dell'hub IoT][lnk-apis-sdks].

Questo è il set di proprietà del sistema nei messaggi dell'hub IoT.

| Proprietà | Descrizione |
| -------- | ----------- |
| MessageId | Identificatore configurabile dall'utente per il messaggio, usato in genere per modelli di richiesta-risposta. Formato: stringa con distinzione tra maiuscole/minuscole (al massimo 128 caratteri di lunghezza) di caratteri alfanumerici ASCII a 7 bit + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| Numero di sequenza | Numero, univoco per ogni dispositivo-coda, assegnato dall'hub IoT a ogni messaggio C2D. |
| To | Usata in [Cloud a dispositivo](#c2d) per la destinazione del messaggio.|
| ExpiryTimeUtc | Data e ora della scadenza del messaggio. |
| EnqueuedTime | Ora di ricezione del messaggio da parte dell'hub IoT. |
| CorrelationId | Proprietà di stringa che contiene in genere l'ID del messaggio della richiesta nei modelli richiesta-risposta. |
| UserId | Usata per specificare l'origine dei messaggi. Quando i messaggi vengono generati dall'hub IoT, viene impostata su `{iot hub name}`. |
| Ack | Usata nei messaggi C2D per richiedere all'hub IoT di generare messaggi con commenti come risultato dell'utilizzo del messaggio da parte del dispositivo. Di seguito sono indicati i valori possibili: **none** (predefinito): non viene generato alcun messaggio con commenti. **positive**: se il messaggio è stato completato, viene ricevuto un messaggio con commenti. **negative**: se il messaggio è scaduto o se è stato raggiunto il numero massimo di recapiti senza il completamento da parte del dispositivo, viene ricevuto un messaggio con commenti. **full**: esito positivo e negativo. Per altre informazioni, vedere [Commenti sul messaggio](#feedback). |
| ConnectionDeviceId | Impostato dall'hub IoT sui messaggi D2C. Contiene il valore **deviceId** del dispositivo che ha inviato il messaggio. |
| ConnectionDeviceGenerationId | Impostato dall'hub IoT sui messaggi D2C. Contiene il valore [generationId](#deviceproperties) (come indicato in **Proprietà delle identità dei dispositivi**) del dispositivo che ha inviato il messaggio. |
| ConnectionAuthMethod | Impostato dall'hub IoT sui messaggi D2C. Informazioni sul metodo di autenticazione usato per autenticare il dispositivo che invia il messaggio. Per altre informazioni, vedere [Anti-spoofing D2C](#antispoofing).|

### Scelta del protocollo di comunicazione <a id="amqpvshttp"></a>

L'hub IoT supporta i protocolli [AMQP][lnk-amqp] e HTTP/1 per le comunicazioni sul lato dispositivo. Ecco di seguito un elenco di considerazioni relative ai rispettivi usi.

* **Modello da cloud a dispositivo**. HTTP/1 non offre un modo efficiente per implementare il push del server. Di conseguenza, quando si usa HTTP/1 i dispositivi eseguono il polling dell'hub IoT per i messaggi da cloud a dispositivo. Questa procedura è molto inefficiente per il dispositivo e per l'hub IoT. Le indicazioni attuali per l'uso di HTTP/1 prevedono l'impostazione di un intervallo di polling per ogni dispositivo su un valore inferiore a una volta ogni 25 minuti. AMQP supporta invece il push del server durante la ricezione di messaggi da cloud a dispositivo e abilita i push immediati dei messaggi dall'hub IoT al dispositivo. Se la latenza del recapito è un problema, è consigliabile usare il protocollo AMQP. Per i dispositivi raramente connessi è invece possibile usare HTTP/1.
* **Gateway sul campo** Considerate le limitazioni di HTTP/1 relative al push del server, questo protocollo non è appropriato per l'uso con [scenari di gateway sul campo][lnk-azure-gateway-guidance].
* **Dispositivi con risorse ridotte**. Le librerie HTTP/1 sono significativamente più piccole delle librerie AMQP. Se il dispositivo ha poche risorse, ad esempio meno di 1 MB di RAM, è quindi possibile che HTTP/1 sia l'unica implementazione di protocollo disponibile.
* **Attraversamento rete**. Lo standard AMQP rimane in attesa sulla porta 5672. Ciò potrebbe provocare problemi nelle reti chiuse ai protocolli non HTTP.
* **Dimensioni del payload**. AMQP è un protocollo binario ed è quindi significativamente più compatto rispetto a HTTP/1.

In generale, è consigliabile usare AMQP quando possibile e usare HTTP/1 se le risorse del dispositivo o la configurazione della rete non consentono l'uso di AMQP. Quando si usa HTTP/1, inoltre, la frequenza di polling deve essere impostata su un valore inferiore a ogni 25 minuti per ogni dispositivo. Durante lo sviluppo è ovviamente accettabile usare frequenze di polling superiori.

È infine importante vedere [Gateway del protocollo IoT di Azure][lnk-azure-protocol-gateway], che consente di distribuire un gateway MQTT a prestazioni elevate che si interfaccia direttamente con l'hub IoT. MQTT supporta il push del server, consentendo quindi il recapito immediato di messaggi C2D al dispositivo, ed è disponibile per dispositivi con risorse molto ridotte. Lo svantaggio principale di questo approccio consiste nel requisito relativo al self-hosting e alla gestione di un gateway del protocollo.

### Dispositivo a cloud <a id="d2c"></a>

Come illustrato nella sezione [Endpoint](#endpoints), i messaggi da dispositivo a cloud vengono inviati tramite un endpoint per il dispositivo, in particolare `/devices/{deviceId}/messages/events`, e vengono ricevuti tramite un endpoint per il servizio (`/messages/events`), compatibile con gli [hub eventi][lnk-event-hubs]. È quindi possibile usare l'integrazione standard degli hub eventi e gli SDK per ricevere i messaggi.

L'hub IoT implementa la messaggistica da dispositivo a cloud in modo molto simile agli [hub eventi][lnk-event-hubs]. I messaggi da dispositivo a cloud dell'hub IoT sono molto più simili agli *eventi* degli hub eventi rispetto ai *messaggi* del [bus di servizio][lnk-servicebus].

Questa implementazione presenta le implicazioni seguenti:

* Analogamente agli *eventi* degli hub eventi, i messaggi da dispositivo a cloud sono durevoli e vengono mantenuti in un hub IoT per un massimo di 7 giorni. Vedere [Opzioni di configurazione da dispositivo a cloud](#d2cconfiguration).
* I messaggi da dispositivo a cloud vengono partizionati in un set fisso di partizioni definito in fase di creazione. Vedere [Opzioni di configurazione da dispositivo a cloud](#d2cconfiguration).
* Analogamente agli hub eventi, i client che leggono i messaggi da dispositivo a cloud devono gestire le partizioni e l'impostazione del checkpoint. Vedere [Hub eventi - Utilizzo di eventi][lnk-event-hubs-consuming-events].
* Analogamente agli eventi degli hub eventi, i messaggi da dispositivo a cloud possono avere dimensioni massime pari a 256 KB e possono essere raggruppati in batch per ottimizzare gli invii. I batch possono avere dimensioni massime di 256 KB e possono includere al massimo 500 messaggi.

Esistono tuttavia alcune differenze tra i messaggi da dispositivo a cloud dell'hub IoT e gli hub eventi:

* Come illustrato nella sezione [Sicurezza](#security), l'hub IoT consente l'autenticazione per i singoli dispositivi e il controllo di accesso.
* L'hub IoT consente milioni di dispositivi connessi simultaneamente (vedere [Quote e limitazione](#throttling)), mentre gli hub eventi sono limitati a 5000 connessioni AMQP per ogni spazio dei nomi.
* L'hub IoT non consente il partizionamento arbitrario tramite **PartitionKey**. I messaggi da dispositivo a cloud vengono partizionati rispetto al valore **deviceId** di origine.
* Il ridimensionamento dell'hub IoT è leggermente diverso rispetto agli hub eventi. Per altre informazioni, vedere [Ridimensionamento dell'hub IoT][lnk-guidance-scale].

Si noti che ciò non significa che l'hub IoT sostituisca gli hub eventi in tutti gli scenari. Ad esempio, in alcuni calcoli di elaborazione eventi potrebbe essere necessario ripartizionare gli eventi rispetto a una proprietà o un campo diverso prima di analizzare i flussi dei dati. In questo caso è possibile usare un hub eventi per disaccoppiare due porzioni della pipeline di elaborazione dei flussi.

Per informazioni dettagliate sull'uso della messaggistica da dispositivo a cloud, vedere [API e SDK dell'hub IoT][lnk-apis-sdks].

#### Traffico non di telemetria

In molti casi i dispositivi non inviano solo punti dati di telemetria al back-end dell'applicazione, ma anche messaggi *interattivi* e richieste che richiedono l'esecuzione e la gestione dal livello della logica di business dell'applicazione, ad esempio avvisi critici che devono attivare un'azione specifica nel back-end o risposte del dispositivo ai comandi.

Per altre informazioni sul modo migliore per elaborare questo tipo di messaggi, vedere la sezione [Elaborazione da dispositivo a cloud][lnk-guidance-d2c-processing] di Indicazioni per l'hub IoT.

#### Opzioni di configurazione da dispositivo a cloud <a id="d2cconfiguration"></a>

Un hub IoT espone le proprietà seguenti per controllare la messaggistica D2C.

* **Conteggio partizioni**. Impostare questa proprietà in fase di creazione per definire il numero di partizioni per l'inserimento di eventi D2C.
* **Periodo di memorizzazione**. Questa proprietà specifica il periodo di memorizzazione per i messaggi da dispositivo a cloud. Il valore predefinito è 1 giorno, ma è possibile incrementarlo fino a 7.

Analogamente agli hub eventi, l'hub IoT consente la gestione dei Gruppi di consumer nell'endpoint di ricezione da dispositivo a cloud.

È possibile modificare tutte queste proprietà tramite il [portale di Azure][lnk-management-portal] o a livello di codice mediante [Hub IoT di Azure - API del provider di risorse][lnk-resource-provider-apis].

#### Proprietà anti-spoofing <a id="antispoofing"></a>

Per evitare lo spoofing di dispositivi nei messaggi da dispositivo a cloud, l'hub IoT contrassegna tutti i messaggi con le proprietà seguenti:

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

Le prime due contengono le proprietà **deviceId** e **generationId** del dispositivo di origine, come indicato in [Proprietà delle identità dei dispositivi](#deviceproperties).

La proprietà **ConnectionAuthMethod** contiene un oggetto serializzato JSON con le proprietà seguenti:

    {
        "scope": "{ hub | device}",
        "type": "{ symkey | sas}",
        "issuer": "iothub"
    }

### Cloud a dispositivo <a id="c2d"></a>

Come già illustrato nella sezione [Endpoint](#endpoints), i messaggi da cloud a dispositivo vengono inviati tramite un endpoint per il servizio (`/messages/devicebound`) e vengono ricevuti tramite una serie di endpoint per il dispositivo (`/devices/{deviceId}/messages/devicebound`).

Ogni messaggio da cloud a dispositivo è destinato a un singolo dispositivo, quindi la proprietà **to** sarà impostata su `/devices/{deviceId}/messages/devicebound`.

**Importante**: ogni coda di dispositivo può includere al massimo 50 messaggi da cloud a dispositivo. Se si prova a inviare più messaggi allo stesso dispositivo, si verificherà un errore.

#### Ciclo di vita del messaggio <a id="message lifecycle"></a>

Per implementare la garanzia di recapito di tipo *almeno una volta*, i messaggi da cloud a dispositivo vengono resi persistenti nelle code per dispositivo e i dispositivi devono riconoscere esplicitamente il *completamento* per consentire all'hub IoT di rimuoverli dalla coda. Ciò garantisce la resilienza rispetto a errori di connettività e dei dispositivi.

La figura seguente illustra il grafico sullo stato del ciclo di vita per un messaggio da cloud a dispositivo.

![Ciclo di vita dei messaggi da cloud a dispositivo][img-lifecycle]

Quando un messaggio viene inviato dal servizio, viene considerato *Accodato*. Quando un dispositivo accetta di *ricevere* un messaggio, l'hub IoT *blocca* il messaggio (stato **Invisibile**), per consentire ad altri thread nello stesso dispositivo di iniziare a ricevere altri messaggi. Quando un thread del dispositivo ha completato l'elaborazione di un dispositivo, invia una notifica all'hub IoT *completando* il messaggio. Può anche decidere di *rifiutare* il messaggio, impostando lo stato **Non recapitabile** per il messaggio oppure può *abbandonarlo*, inserendo di nuovo il messaggio nella coda con stato **Accodato**.

Poiché è possibile che un thread non riesca a elaborare un messaggio senza inviare una notifica all'hub IoT, i messaggi verranno convertiti automaticamente da **Invisibile** ad **Accodato** dopo un *timeout di visibilità o di blocco*. Valore predefinito: 1 minuto. Un messaggio può passare dallo stato **Accodato** allo stato **Invisibile** e viceversa per un numero massimo di volte specificato nella proprietà *max delivery count* nell'hub IoT. Dopo il numero specificato di transizioni, il messaggio verrà trasferito tra quelli non recapitabili. Analogamente, un messaggio verrà trasferito automaticamente tra quelli non recapitabili dopo la rispettiva scadenza. Vedere [Durata (TTL)](#ttl).

Per un'esercitazione sui messaggi da cloud a dispositivo, vedere [Introduzione ai messaggi da cloud a dispositivo dell'hub IoT di Azure][lnk-getstarted-c2d-tutorial]. Per argomenti di riferimento sul modo in cui le diverse API e i vari SDK espongono la funzionalità da cloud a dispositivo, vedere [API e SDK dell'hub IoT][lnk-apis-sdks].

> [AZURE.NOTE]I messaggi da cloud a dispositivo devono in genere essere completati quando la perdita del messaggio non influisce sulla logica dell'applicazione. Ciò si può verificare in molti scenari diversi, ad esempio, il contenuto del messaggio è stato reso persistente nell'archivio locale o un'operazione è stata eseguita correttamente oppure il messaggio include informazioni temporanee la cui perdita non influirebbe sulla funzionalità dell'applicazione. In alcuni casi, per le attività con esecuzione prolungata, il messaggio da cloud a dispositivo viene in genere completato dopo avere reso persistente la descrizione dell'attività nell'archivio locale e quindi il back-end dell'applicazione riceve una notifica con uno o più messaggi da dispositivo a cloud in diverse fasi di avanzamento dell'attività.

#### Durata (TTL) <a id="ttl"></a>

Ogni messaggio da cloud a dispositivo ha una scadenza. Questa impostazione può essere definita esplicitamente dal servizio nella proprietà **ExpiryTimeUtc** oppure viene configurata dall'hub IoT usando il valore di *durata (TTL)* predefinito specificato come proprietà dell'hub IoT. Vedere [Opzioni di configurazione da cloud a dispositivo](#c2dconfiguration).

#### Commenti sul messaggio <a id="feedback"></a>

Durante l'invio di messaggi da cloud a dispositivo, il servizio può richiedere il recapito di commenti specifici per ogni messaggio in merito allo stato finale del messaggio. Quando si imposta la proprietà **Ack** su **positive**, l'hub IoT genera un messaggio di commenti se e solo se il messaggio da cloud a dispositivo ha raggiunto lo stato **Completato**. Quando si imposta la proprietà **Ack** su **negative**, l'hub IoT genera un messaggio di commenti se e solo se il messaggio da cloud a dispositivo ha raggiunto lo stato **Non recapitabile**. Quando si imposta la proprietà **Ack** su **full**, l'hub IoT genera un messaggio di commenti in entrambi i casi.

Come illustrato in [Endpoint](#endpoints), i commenti vengono recapitati tramite un endpoint per il servizio (`/messages/servicebound/feedback`) come messaggi. La semantica di ricezione per i commenti è uguale a quella dei messaggi da cloud a dispositivo, con lo stesso [Ciclo di vita dei messaggi](#Ciclo di vita dei messaggi). Quando possibile, i commenti sul messaggio vengono riuniti in un singolo messaggio con il formato seguente.

Ogni messaggio recuperato dall'endpoint di commenti ha le proprietà seguenti.

| Proprietà | Descrizione |
| -------- | ----------- |
| EnqueuedTime | Timestamp che indica quando è stato creato il batch. |
| UserId | `{iot hub name}` |
| ContentType | `application/vnd.microsoft.iothub.feedback.json` |

Il corpo è una matrice serializzata con JSON dei record, ognuno con le proprietà seguenti:

| Proprietà | Descrizione |
| -------- | ----------- |
| EnqueuedTime | Timestamp che indica quando è stato creato il risultato del messaggio. Ad esempio, il dispositivo ha completato l'operazione o il messaggio è scaduto. |
| CorrelationId | **MessageId** del messaggio da cloud a dispositivo correlato a queste informazioni sui commenti. |
| StatusCode | **0** se l'esito è positivo, **1** se il messaggio è scaduto, **2** se il numero massimo di recapiti è stato superato, **3** se il messaggio è stato rifiutato. |
| Descrizione | Valori di stringa per i risultati precedenti. |
| DeviceId | **DeviceId** del messaggio da cloud a dispositivo correlato a queste informazioni sui commenti. |
| DeviceGenerationId | **DeviceGenerationId** del dispositivo di destinazione del messaggio da cloud a dispositivo correlato a queste informazioni sui commenti. |

**Importante**. Il servizio deve specificare un valore **MessageId** per il messaggio da cloud a dispositivo per potere correlare i rispettivi commenti al messaggio originale.

**Esempio**. Ecco un esempio di corpo di un messaggio con commenti.

    [
        {
            "CorrelationId": "0987654321",
            "EnqueuedTime": "2015-07-28T16:24:48.789Z",
            "StatusCode": "0",
            "Description": "Success",
            "DeviceId": "123",
            "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
        },
        {
            ...
        },
        ...
    ]

#### Opzioni di configurazione da cloud a dispositivo <a id="c2dconfiguration"></a>

Ogni hub IoT espone le opzioni di configurazione seguenti per la messaggistica da cloud a dispositivo.

| Proprietà | Descrizione | Intervallo e valore predefinito |
| -------- | ----------- | ----------------- |
| defaultTtlAsIso8601 | Durata (TTL) predefinita per messaggi da cloud a dispositivo. | Intervallo ISO\_8601 fino a 2D (almeno 1 minuto). Predefinito: 1 ora. |
| maxDeliveryCount | Numero massimo di recapiti per code da cloud a dispositivo per i singoli dispositivi. | Da 1 a 100. Predefinito: 10. |
| feedback.ttlAsIso8601 | Conservazione per messaggi con commenti diretti al servizio. | Intervallo ISO\_8601 fino a 2D (almeno 1 minuto). Predefinito: 1 ora. |
| feedback.maxDeliveryCount | Numero massimo di recapiti per la coda di commenti. | Da 1 a 100. Predefinito: 100. |

## Quote e limitazione <a id="throttling"></a>

Ogni sottoscrizione di Azure può avere al massimo 10 hub IoT.

Ogni hub IoT viene sottoposto a provisioning con un determinato numero di unità in uno SKU specifico. Per altre informazioni, vedere [Prezzi dell'hub IoT di Azure][lnk-pricing]. Lo SKU e il numero di unità determinano la quota giornaliera massima di messaggi che è possibile inviare e il numero massimo di identità del dispositivo nel registro delle identità. Il numero di dispositivi connessi simultaneamente è limitato dal numero di identità nel registro.

Questo valore determina anche le limitazioni applicate dall'hub IoT alle operazioni.

### Quote del registro delle identità dei dispositivi

L'hub IoT consente al massimo 1100 aggiornamenti del dispositivo (creazione, aggiornamento, eliminazione) per ogni unità al giorno, indipendentemente dallo SKU.

### Limitazioni per le operazioni

Le limitazioni per le operazioni sono limitazioni di frequenza applicate con intervalli di minuti e consentono di evitare abusi. L'hub IoT prova a evitare la restituzione di errori, se possibile, ma inizia a restituire eccezioni se la limitazione viene violata troppo a lungo.

Ecco di seguito l'elenco di limitazioni applicate. I valori fanno riferimento a un singolo hub.

| Limitazione | Valore per ogni hub |
| -------- | ------------- |
| Operazioni del registro delle identità (creazione, recupero, elenco, aggiornamento, eliminazione) importazione/esportazione singola o in blocco | 100/min/unità, fino a 5000/min |
| Connessioni del dispositivo | 100/sec/unità |
| Invii D2C | 2000/min/unità (per S2), 60/min/unità (per S1). Almeno 100/sec. |
| Operazioni C2D (invio, ricezione, commenti) | 100/min/unità |

**Nota**. È possibile incrementare le quote o le limitazioni in qualsiasi momento aumentando il numero di unità sottoposte a provisioning in un hub IoT.

## Passaggi successivi

Al termine di questa panoramica dello sviluppo per l'hub IoT, è possibile usare i collegamenti seguenti per altre informazioni:

- [Introduzione agli hub IoT (esercitazione)][lnk-get-started]
- [Piattaforme del sistema operativo e compatibilità hardware][lnk-compatibility]
- [Pianificare l'implementazione IoT][lnk-guidance]

[Hub eventi - Host processore di eventi]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx

[portale di anteprima di Azure]: https://ms.portal.azure.com

[img-summary]: ./media/iot-hub-devguide/summary.png
[img-endpoints]: ./media/iot-hub-devguide/endpoints.png
[img-lifecycle]: ./media/iot-hub-devguide/lifecycle.png
[img-eventhubcompatible]: ./media/iot-hub-devguide/eventhubcompatible.png

[lnk-compatibility]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[lnk-apis-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-azure-hub-sdks]: https://msdn.microsoft.com/library/mt488521.aspx
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-reference-architecture]: iot-hub-what-is-azure-iot.md

[lnk-azure-gateway-guidance]: iot-hub-guidance.md#fieldgateways
[lnk-guidance-provisioning]: iot-hub-guidance.md#provisioning
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-guidance-security]: iot-hub-guidance.md#customauth

[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-guidance]: iot-hub-guidance.md
[lnk-getstarted-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md

[lnk-amqp]: https://www.amqp.org/
[lnk-arm]: ../resource-group-overview.md
[lnk-azure-resource-manager]: https://azure.microsoft.com/documentation/articles/resource-group-overview/
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-createuse-sas]: ../storage/storage-dotnet-shared-access-signature-part-2/
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-event-hubs]: http://azure.microsoft.com/services/event-hubs/
[lnk-event-hubs-consuming-events]: ../event-hubs/event-hubs-programming-guide.md#event-consumers
[lnk-guidance-d2c-processing]: iot-hub-csharp-csharp-process-d2c.md
[lnk-management-portal]: https://portal.azure.com
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-sas-java]: https://msdn.microsoft.com/library/azure/Hh875756.aspx
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-servicebus]: http://azure.microsoft.com/services/service-bus/
[lnk-tls]: https://tools.ietf.org/html/rfc5246

<!---HONumber=Oct15_HO2-->