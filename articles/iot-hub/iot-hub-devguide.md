---
title: Argomenti della Guida per sviluppatori per IoT Hub | Microsoft Docs
description: Guida per gli sviluppatori dell'hub IoT Azure, con informazioni su endpoint, sicurezza, registro delle identità del dispositivo e messaggistica per l'hub IoT.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/02/2016
ms.author: dobett

---
# Guida per gli sviluppatori dell'hub IoT di Azure
L'hub IoT di Azure è un servizio completamente gestito che consente di abilitare comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi IoT e un back-end applicazioni.

L'hub IoT di Azure offre i vantaggi seguenti:

* Comunicazioni protette con credenziali di sicurezza per singoli dispositivi e controllo di accesso.
* Messaggistica affidabile da dispositivo a cloud e da cloud a dispositivo su vasta scala.
* Connettività semplice dei dispositivi con librerie di dispositivi per i linguaggi e le piattaforme più diffusi.

Questo articolo include gli argomenti seguenti:

* [Endpoint](#endpoints). Questa sezione illustra i diversi endpoint esposti da ogni hub IoT per operazioni della fase di esecuzione e di gestione.
* [Registro delle identità dei dispositivi](#device-identity-registry). Questa sezione illustra le informazioni archiviate nel registro delle identità dei dispositivi di ogni hub IoT e spiega come accedere alle informazioni e modificarle.
* [Sicurezza](#security). Questa sezione illustra il modello di sicurezza usato per concedere l'accesso alla funzionalità hub IoT per i dispositivi e i componenti cloud.
* [Messaggistica](#messaging). Questa sezione descrive le funzionalità di messaggistica da dispositivo a cloud e da cloud a dispositivo esposte dall'hub IoT.
* [Quote e limitazione](#throttling). Questa sezione riepiloga le quote applicabili all'uso dell'hub IoT.

## Endpoint <a id="endpoints"></a>
L'hub IoT di Azure è un servizio multi-tenant che espone le proprie funzionalità a diversi attori. Il diagramma seguente mostra i vari endpoint esposti dall'hub IoT.

![Endpoint hub IoT][img-endpoints]

Ecco di seguito una descrizione degli endpoint:

* **Provider di risorse**. Il provider di risorse dell'hub IoT espone un'interfaccia di [Azure Resource Manager][lnk-arm] che consente ai proprietari di sottoscrizioni di Azure di creare ed eliminare hub IoT e di aggiornarne le proprietà. Le proprietà dell'hub IoT determinano i criteri di sicurezza a livello di hub, a differenza del controllo di accesso a livello di dispositivo (vedere la sezione [Controllo di accesso](#accesscontrol) più avanti in questo articolo) e delle opzioni funzionali per la messaggistica da cloud a dispositivo e da dispositivo a cloud. Il provider di risorse consente anche di [esportare le identità dei dispositivi](#importexport).
* **Gestione delle identità dei dispositivi**. Ogni hub IoT espone un set di endpoint REST HTTP per gestire le identità dei dispositivi (per operazioni di creazione, recupero, aggiornamento ed eliminazione). Le identità dei dispositivi vengono usate per l'autenticazione dei dispositivi e il controllo di accesso. Per altre informazioni, vedere [Registro delle identità dei dispositivi](#device-identity-registry).
* **Endpoint del dispositivo**. Per ogni dispositivo di cui è stato effettuato il provisioning nel registro delle identità dei dispositivi, l'hub IoT espone un set di endpoint che il dispositivo può usare per inviare e ricevere messaggi:
  
  * *Invio di messaggi da dispositivo a cloud*. Usare questo endpoint per inviare messaggi da dispositivo a cloud. Per altre informazioni, vedere [Messaggistica da dispositivo a cloud](#d2c).
  * *Ricezione di messaggi da cloud a dispositivo*. Il dispositivo usa questo endpoint per ricevere messaggi da cloud a dispositivo specifici. Per altre informazioni, vedere [Messaggistica da cloud a dispositivo](#c2d).
  * *Avvio di caricamenti di file*. Il dispositivo usa questo endpoint per ricevere un URI di firma di accesso condiviso di Archiviazione di Azure dall'hub IoT per il caricamento di un file. Per altre informazioni, vedere [Caricamenti di file](#fileupload).
    
    Questi endpoint vengono esposti con i protocolli [MQTT v3.1.1][lnk-mqtt], HTTP 1.1 e [AMQP 1.0][lnk-amqp]. Si noti che AMQP è disponibile anche su [WebSocket][lnk-websockets] sulla porta 443.
* **Endpoint di servizio**. Ogni hub IoT espone un set di endpoint che il back-end dell'applicazione può usare per comunicare con i dispositivi. Questi endpoint vengono attualmente esposti solo tramite il protocollo [AMQP][lnk-amqp].
  * *Ricezione di messaggi da dispositivo a cloud*. Questo endpoint è compatibile con [Hub eventi di Azure][lnk-event-hubs] e può essere usato da un servizio back-end per leggere tutti i messaggi da dispositivo a cloud inviati dai dispositivi. Per altre informazioni, vedere [Messaggistica da dispositivo a cloud](#d2c).
  * *Invio di messaggi da cloud a dispositivo e ricezione di acknowledgement di recapito*. Questi endpoint consentono al back-end dell'applicazione di inviare messaggi affidabili da cloud a dispositivo e di ricevere gli acknowledgment di recapito o di scadenza corrispondenti. Per altre informazioni, vedere [Messaggistica da cloud a dispositivo](#c2d).
  * *Ricezione di notifiche relative ai file*. Questo endpoint di messaggistica consente di ricevere notifiche quando i dispositivi completano il caricamento di un file.

L'articolo [SDK hub IoT][lnk-sdks] descrive le varie modalità di accesso a questi endpoint.

È infine importante notare che tutti gli endpoint dell'hub IoT usano il protocollo [TLS][lnk-tls] e non vengono mai esposti su canali non crittografati o non protetti.

### Come leggere dagli endpoint compatibili con l'hub eventi. <a id="eventhubcompatible"></a>
Quando si usa [Azure Service Bus SDK per .NET][lnk-servicebus-sdk] o [Hub eventi - Host processore di eventi][lnk-eventprocessorhost], è possibile usare qualsiasi stringa di connessione dell'hub IoT con le autorizzazioni corrette. Usare quindi **messaggi/eventi** come nome dell'hub eventi.

Quando si usano SDK (o integrazioni del prodotto) non compatibili con l'hub IoT, è necessario recuperare un endpoint compatibile con Hub eventi e un nome di hub eventi dalle impostazioni dell'hub IoT nel [portale di Azure][lnk-management-portal]\:

1. Nel pannello dell'hub IoT fare clic su **Messaggistica**.
2. La sezione **Impostazioni da dispositivo a cloud** include i valori seguenti: **Endpoint compatibile con l'hub eventi**, **Nome compatibile con l'hub eventi** e **Partizioni**.
   
    ![Impostazioni da dispositivo a cloud][img-eventhubcompatible]

> [!NOTE]
> Se l'SDK richiede un valore **Nome host** o **Spazio dei nomi**, rimuovere lo schema da **Endpoint compatibile con l'hub eventi**. Ad esempio, se l'endpoint compatibile con l'hub eventi è **sb://iothub-ns-myiothub-1234.servicebus.windows.net/**, il **Nome host** sarà **iothub-ns-myiothub-1234.servicebus.windows.net** e **Spazio dei nomi** sarà **iothub-ns-myiothub-1234**.
> 
> 

È quindi possibile usare qualsiasi criterio di sicurezza di accesso condiviso con autorizzazioni **ServiceConnect** per connettersi all'hub eventi specificato.

Se si deve compilare una stringa di connessione dell'hub eventi con le informazioni precedenti, è possibile usare il modello seguente:

```
Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}
```

Di seguito è riportato un elenco di SDK e integrazioni che è possibile usare con gli endpoint compatibili con l'hub eventi esposti dall'hub IoT:

* [Client degli hub eventi Java.](https://github.com/hdinsight/eventhubs-client)
* [Spout di Apache Storm](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md). È possibile visualizzare il [codice sorgente dello spout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) su GitHub.
* [Integrazione con Apache Spark.](../hdinsight/hdinsight-apache-spark-eventhub-streaming.md)

## Registro delle identità dei dispositivi
Ogni hub IoT contiene un registro delle identità dei dispositivi. È possibile usare questo registro per creare risorse per ogni dispositivo nel servizio, ad esempio una coda contenente i messaggi in elaborazione da cloud a dispositivo, ma anche per consentire l'accesso agli endpoint per dispositivo, come illustrato nella sezione [Controllo di accesso](#accesscontrol).

A livello generale, il registro delle identità dei dispositivi è una raccolta delle risorse relative alle identità dei dispositivi che supporta REST. Le sezioni seguenti illustrano in modo dettagliato le proprietà delle risorse relative alle identità dei dispositivi e le operazioni sulle identità consentite dal registro.

> [!NOTE]
> Per altri dettagli sul protocollo HTTP e sugli SDK che è possibile usare per interagire con il registro delle identità dei dispositivi, vedere [SDK hub IoT][lnk-sdks].
> 
> 

### Proprietà delle identità dei dispositivi <a id="deviceproperties"></a>
Le identità dei dispositivi vengono rappresentate da documenti JSON con le proprietà seguenti.

| Proprietà | Opzioni | Descrizione |
| --- | --- | --- |
| deviceId |Obbligatoria, di sola lettura negli aggiornamenti |Stringa con distinzione tra maiuscole e minuscole (con lunghezza massima di 128 caratteri) di caratteri alfanumerici ASCII a 7 bit + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| generationId |Obbligatoria, di sola lettura |Stringa con distinzione tra maiuscole/minuscole generata dall'hub con lunghezza massima di 128 caratteri. Viene usata per distinguere i dispositivi con lo stesso valore **deviceId** in caso di eliminazione e nuova creazione. |
| etag |Obbligatoria, di sola lettura |Stringa che rappresenta un eTag vulnerabile per l'identità del dispositivo, come indicato in [RFC7232][lnk-rfc7232]. |
| auth |Facoltativa |Oggetto composito contenente le informazioni di autenticazione e i materiali di sicurezza. |
| auth.symkey |Facoltativa |Oggetto composito contenente una chiave primaria e una chiave secondaria, archiviate in formato Base 64. |
| status |Obbligatoria |Indicatore di accesso. Può essere **Enabled** o **Disabled**. Se è **Enabled**, il dispositivo sarà autorizzato alla connessione. Se è **Disabled**, il dispositivo non potrà accedere ad alcun endpoint per il dispositivo. |
| statusReason |Facoltativa |Stringa con 128 caratteri di lunghezza che memorizza il motivo dello stato dell'identità del dispositivo. Sono consentiti tutti i caratteri UTF-8. |
| statusUpdateTime |Sola lettura |Indicatore temporale che mostra la data e l'ora dell'ultimo aggiornamento dello stato. |
| connectionState |Sola lettura |Campo indicante lo stato della connessione: **Connected** o **Disconnected**. Questo campo rappresenta la visualizzazione Hub IoT dello stato di connessione del dispositivo. **Importante**: è consigliabile usare questo campo solo per scopi di sviluppo e di debug. Lo stato di connessione viene aggiornato solo per i dispositivi che usano MQTT o AMQP. Si basa anche su ping a livello di protocollo (ping MQTT o AMQP) e può avere un ritardo massimo di soli 5 minuti. Per questi motivi possono essere presenti falsi positivi, ad esempio dispositivi segnalati come connessi ma che in effetti sono disconnessi. |
| connectionStateUpdatedTime |Sola lettura |Indicatore temporale che mostra la data e l'ora dell'ultimo aggiornamento dello stato della connessione. |
| lastActivityTime |Sola lettura |Indicatore temporale che mostra la data e l'ora in cui il dispositivo si è connesso oppure ha ricevuto o inviato un messaggio per l'ultima volta. |

> [!NOTE]
> Lo stato della connessione può rappresentare solo la visualizzazione Hub IoT dello stato della connessione. Gli aggiornamenti dello stato possono essere ritardati a seconda delle condizioni e delle configurazioni della rete.
> 
> 

### Operazioni relative alle identità dei dispositivi
Il registro delle identità dei dispositivi dell'hub IoT espone le operazioni seguenti:

* Creare l'identità del dispositivo
* Aggiornare l'identità del dispositivo
* Recuperare l'identità del dispositivo tramite ID
* Eliminare l'identità del dispositivo
* Elencare al massimo 1000 identità
* Esportare tutte le identità nell'archivio BLOB
* Importare identità dall'archivio BLOB

Tutte queste operazioni consentono l'uso della concorrenza ottimistica, come specificato in [RFC7232][lnk-rfc7232].

> [!IMPORTANT]
> L'unico modo per recuperare tutte le identità nel registro delle identità di un hub consiste nell'usare la funzionalità [Esporta](#importexport).
> 
> 

Un registro delle identità dei dispositivi di un hub IoT:

* Non contiene metadati delle applicazioni.
* È accessibile come un dizionario, usando il valore **deviceId** come chiave.
* Non supporta le query espressive.

Una soluzione IoT include in genere un archivio separato specifico della soluzione che contiene metadati specifici dell'applicazione. Ad esempio, l'archivio specifico della soluzione in una soluzione per un edificio efficiente registra l'ambiente in cui viene distribuito un sensore di temperatura.

> [!IMPORTANT]
> Usare il registro delle identità dei dispositivi solo per le operazioni di gestione e provisioning dei dispositivi. Le operazioni a velocità effettiva elevata in fase di esecuzione non devono dipendere dall'esecuzione di operazioni nel registro delle identità dei dispositivi. Ad esempio, la verifica dello stato di connessione di un dispositivo prima dell'invio di un comando non è uno schema supportato. Assicurarsi di verificare le [frequenze di limitazione](#throttling) per il registro delle identità dei dispositivi e il modello di [heartbeat del dispositivo][lnk-guidance-heartbeat].
> 
> 

### Disabilitazione di dispositivi
È possibile disabilitare i dispositivi aggiornando la proprietà **status** di un'identità nel registro. Questa proprietà viene in genere usata in due scenari:

* Durante un processo di orchestrazione di provisioning. Per altre informazioni, vedere [Progettare la soluzione - Provisioning di dispositivi][lnk-guidance-provisioning].
* Se, per qualsiasi motivo, si considera un dispositivo compromesso o non più autorizzato.

### Importare ed esportare le identità dei dispositivi<a id="importexport"></a>
È possibile esportare in blocco le identità dei dispositivi dal registro delle identità di un hub IoT usando operazioni asincrone sull'[endpoint del provider di risorse dell'hub IoT](#endpoints). Le esportazioni sono processi a esecuzione prolungata che usano un contenitore BLOB specificato dal cliente per salvare i dati relativi alle identità dei dispositivi letti dal registro delle identità.

È possibile importare in blocco le identità dei dispositivi nel registro delle identità di un hub IoT usando operazioni asincrone sull'[endpoint del provider di risorse dell'hub IoT](#endpoints). Le importazioni sono processi a esecuzione prolungata che usano un contenitore BLOB specificato dal cliente per scrivere i dati relativi alle identità nel registro delle identità dei dispositivi.

* Per informazioni dettagliate sulle API di importazione e di esportazione, vedere [Hub IoT di Azure - API del provider di risorse][lnk-resource-provider-apis].
* Per altre informazioni sull'esecuzione dei processi di importazione ed esportazione, vedere [Gestione in blocco delle identità dei dispositivi dell'hub IoT][lnk-bulk-identity].

## Sicurezza <a id="security"></a>
Questa sezione illustra le opzioni per la protezione dell'hub IoT di Azure.

### Controllo di accesso <a id="accesscontrol"></a>
L'hub IoT usa il set di *autorizzazioni* seguente per concedere l'accesso agli endpoint di ogni hub IoT. Le autorizzazioni limitano l'accesso a un hub IoT in base alla funzionalità.

* **RegistryRead**. Concede l'accesso in lettura al registro delle identità dei dispositivi. Per altre informazioni, vedere [Registro delle identità dei dispositivi](#device-identity-registry).
* **RegistryReadWrite**. Concede l'accesso in lettura e scrittura al registro delle identità dei dispositivi. Per altre informazioni, vedere [Registro delle identità dei dispositivi](#device-identity-registry).
* **ServiceConnect**. Concede l'accesso alle comunicazioni per il servizio cloud e al monitoraggio degli endpoint. Ad esempio, concede ai servizi cloud back-end l'autorizzazione per la ricezione di messaggi da dispositivo a cloud, l'invio di messaggi da cloud a dispositivo e il recupero degli acknowledgment di recapito corrispondenti.
* **DeviceConnect**. Concede l'accesso agli endpoint di comunicazione per il dispositivo. Ad esempio, concede l'autorizzazione per l'invio di messaggi da dispositivo a cloud e la ricezione di messaggi da cloud a dispositivo. Questa autorizzazione viene usata dai dispositivi.

Per concedere le autorizzazioni è possibile procedere nei modi seguenti:

* **Criteri di accesso condivisi a livello di hub**. I criteri di accesso condiviso possono concedere qualsiasi combinazione delle autorizzazioni elencate nella sezione precedente. È possibile definire i criteri nel [portale di Azure][lnk-management-portal] o a livello di codice usando le [API del provider di risorse dell'hub IoT di Azure][lnk-resource-provider-apis]. Un hub IoT appena creato ha i criteri predefiniti seguenti:
  
  * **iothubowner**: criteri con tutte le autorizzazioni.
  * **service**: criteri con autorizzazione ServiceConnect.
  * **device**: criteri con autorizzazione DeviceConnect.
  * **registryRead**: criteri con autorizzazione RegistryRead.
  * **registryReadWrite**: criteri con autorizzazioni RegistryRead e RegistryWrite.
* **Credenziali di sicurezza specifiche del dispositivo**. Ogni hub IoT contiene un [registro delle identità dei dispositivi](#device-identity-registry). Per ogni dispositivo in questo registro è possibile configurare credenziali di sicurezza che concedono autorizzazioni **DeviceConnect** con ambito agli endpoint di dispositivo corrispondenti.

Ad esempio, in una soluzione IoT tipica:

* Il componente di gestione dei dispositivi usa i criteri *registryReadWrite*.
* Il componente processore di eventi usa i criteri *service*.
* Il componente della logica di business di runtime del dispositivo usa i criteri *service*.
* I singoli dispositivi si connettono usando le credenziali archiviate nel registro delle identità dell'hub IoT.

Per indicazioni sugli argomenti relativi alla sicurezza dell'hub IoT, vedere la sezione sulla sicurezza dell'articolo [Progettare la soluzione][lnk-guidance-security].

### Autenticazione
L'hub IoT di Azure concede l'accesso agli endpoint tramite la verifica di un token rispetto ai criteri di accesso condiviso e alle credenziali di sicurezza del registro delle identità dei dispositivi.

Le credenziali di sicurezza, ad esempio le chiavi asimmetriche, non vengono mai trasmesse in rete.

> [!NOTE]
> Il provider di risorse dell'hub IoT di Azure viene protetto tramite la sottoscrizione di Azure, analogamente a tutti i provider in [Azure Resource Manager][lnk-azure-resource-manager].
> 
> 

Per altre informazioni sulla creazione e sull'uso di token di sicurezza, vedere [Uso dei token di sicurezza hub IoT][lnk-sas-tokens].

#### Specifiche del protocollo
Ogni protocollo supportato, ad esempio MQTT, AMQP e HTTP, trasporta i token in modo diverso.

Il protocollo HTTP implementa l'autenticazione includendo un token valido nell'intestazione della richiesta **Authorization**.

Quando si usa [AMQP][lnk-amqp], l'hub IoT supporta [SASL PLAIN][lnk-sasl-plain] e la [sicurezza basata sulle attestazioni AMQP][lnk-cbs].

Se si usa la sicurezza basata sulle attestazioni AMQP, lo standard specifica come trasmettere questi token.

Per SASL PLAIN **username** può essere:

* `{policyName}@sas.root.{iothubName}` nel caso di token a livello di hub.
* `{deviceId}` ne caso di token con ambito relativo al dispositivo.

In entrambi i casi, il campo della password contiene il token, come descritto nell'articolo [Uso dei token di sicurezza hub IoT][lnk-sas-tokens].

Quando si usa MQTT, il pacchetto CONNECT ha deviceId come valore di ClientId, {iothubhostname}/{deviceId} nel campo Username e un token di firma di accesso condiviso nel campo Password. Il valore di {iothubhostname} deve essere il record CName completo dell'hub IoT, ad esempio contoso.azure-devices.net.

##### Esempio:
Nome utente (per DeviceId viene fatta distinzione tra maiuscole e minuscole): `iothubname.azure-devices.net/DeviceId`

Password (generare una firma di accesso condiviso con Esplora dispositivi): `SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> Gli [Azure IoT Hub SDK][lnk-sdks] generano automaticamente i token durante la connessione al servizio. In alcuni casi, gli SDK non supportano tutti i protocolli o tutti i metodi di autenticazione.
> 
> 

#### Considerazioni speciali su SASL PLAIN
Quando si usa SASL PLAIN, un client che si connette a un hub IoT potrà usare un singolo token per ogni connessione TCP. Quando il token scade, la connessione TCP si disconnette dal servizio e attiva una riconnessione. Questo comportamento, anche se non problematico per un componente back-end applicazione, risulta molto dannoso per un'applicazione lato dispositivo, per i motivi seguenti:

* I gateway si connettono in genere per conto di molti dispositivi. Quando si usa SASL PLAIN, devono creare una connessione TCP distinta per ogni dispositivo che si connette a un hub IoT. Questo scenario aumenta in modo considerevole il consumo energetico e delle risorse di rete e incrementa la latenza della connessione di ogni dispositivo.
* L'aumento dell'uso delle risorse per la riconnessione dopo la scadenza di ogni token influisce negativamente sui dispositivi vincolati alle risorse.

### Definire l'ambito delle credenziali a livello di hub
È possibile definire l'ambito dei criteri di sicurezza a livello di hub creando token con URI di risorsa con limitazioni. L'endpoint per l'invio di messaggi da dispositivo a cloud da un dispositivo, ad esempio, è **/devices/{deviceId}/messages/events**. È anche possibile usare criteri di accesso condiviso a livello di hub con autorizzazioni **DeviceConnect** per firmare un token il cui valore resourceURI è **/devices/{deviceId}**. Questo approccio crea un token che può essere usato solo per l'invio di dispositivi per conto del dispositivo **deviceId**.

Questo meccanismo è simile ai [criteri dell'entità di pubblicazione di Hub eventi][lnk-event-hubs-publisher-policy] e consente di implementare metodi di autenticazione personalizzati. Per altre informazioni, vedere la sezione sulla sicurezza dell'articolo [Progettare la soluzione][lnk-guidance-security].

## Messaggistica
L'hub IoT fornisce le primitive di messaggistica per comunicare:

* [Da cloud a dispositivo](#c2d), dal back-end di un'applicazione (*di servizio* o *cloud*).
* [Da dispositivo a cloud](#d2c), da un dispositivo al back-end di un'applicazione.
* [Caricamenti di file](#fileupload), da un dispositivo a un account di archiviazione di Azure associato.

Le proprietà di base della funzionalità di messaggistica dell'hub IoT sono l'affidabilità e la durabilità dei messaggi. Queste proprietà consentono la resilienza in caso di connettività intermittente sul lato dispositivo e picchi di carico durante l'elaborazione degli eventi sul lato cloud. L'hub IoT implementa *almeno una volta* le garanzie di recapito per la messaggistica da dispositivo a cloud e da cloud a dispositivo.

L'hub IoT supporta più protocolli per il dispositivo, ad esempio MQTT, AMQP e HTTP. Per supportare l'interoperabilità senza problemi tra i protocolli, l'hub IoT definisce un formato di messaggio comune supportato da tutti i protocolli per il dispositivo.

### Formato del messaggio <a id="messageformat"></a>
I messaggi dell'hub IoT sono costituiti da questi elementi:

* Un set di *proprietà del sistema*. Queste sono proprietà impostate o interpretate dall'hub IoT. Il set è predeterminato.
* Un set di *proprietà dell'applicazione*. Si tratta di un dizionario di proprietà stringa che l'applicazione può definire e a cui può accedere senza dover deserializzare il corpo del messaggio. Queste proprietà non vengono mai modificate dall'hub IoT.
* Corpo binario opaco.

Per altre informazioni sulla codifica del messaggio nei diversi protocolli, vedere [SDK hub IoT][lnk-sdks].

Questo è il set di proprietà del sistema nei messaggi dell'hub IoT.

| Proprietà | Descrizione |
| --- | --- |
| MessageId |Identificatore configurabile dall'utente per il messaggio, usato per modelli di richiesta-risposta. Formato: stringa con distinzione tra maiuscole e minuscole (con lunghezza massima di 128 caratteri) di caratteri alfanumerici ASCII a 7 bit + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| Numero di sequenza |Numero, univoco per ogni dispositivo-coda, assegnato dall'hub IoT a ogni messaggio da cloud a dispositivo. |
| To |Destinazione specificata nei messaggi [da cloud a dispositivo](#c2d). |
| ExpiryTimeUtc |Data e ora della scadenza del messaggio. |
| EnqueuedTime |Data e ora di ricezione del messaggio da parte dell'hub IoT. |
| CorrelationId |Proprietà stringa in un messaggio di risposta che contiene in genere il valore MessageId della richiesta nei modelli richiesta-risposta. |
| UserId |ID usato per specificare l'origine dei messaggi. Quando i messaggi vengono generati dall'hub IoT, viene impostata su `{iot hub name}`. |
| Ack |Generatore di messaggi con commenti. Questa proprietà viene usata nei messaggi da cloud a dispositivo per richiedere all'hub IoT di generare messaggi con commenti come risultato dell'utilizzo del messaggio da parte del dispositivo. I valori possibili sono i seguenti: **none** (predefinito, non viene generato alcun messaggio con commenti), **positive** (viene ricevuto un messaggio con commenti se il messaggio è stato completato), **negative** (viene ricevuto un messaggio con commenti se il messaggio è scaduto o se è stato raggiunto il numero massimo di recapiti senza il completamento da parte del dispositivo) o **full** (esito sia positivo che negativo). Per altre informazioni, vedere [Commenti sul messaggio](#feedback). |
| ConnectionDeviceId |ID impostato dall'hub IoT sui messaggi da dispositivo a cloud. Contiene il valore **deviceId** del dispositivo che ha inviato il messaggio. |
| ConnectionDeviceGenerationId |ID impostato dall'hub IoT sui messaggi da dispositivo a cloud. Contiene il valore [generationId](#deviceproperties) (come indicato in **Proprietà delle identità dei dispositivi**) del dispositivo che ha inviato il messaggio. |
| ConnectionAuthMethod |Metodo di autenticazione impostato dall'hub IoT sui messaggi da dispositivo a cloud. Questa proprietà contiene informazioni sul metodo di autenticazione usato per autenticare il dispositivo che invia il messaggio. Per altre informazioni, vedere la sezione relativa all'[anti-spoofing nei messaggi da dispositivo a cloud](#antispoofing). |

### Scegliere il protocollo di comunicazione <a id="amqpvshttp"></a>
Per le comunicazioni sul lato dispositivo, l'hub IoT supporta i protocolli MQTT, [AMQP][lnk-amqp], AMQP su WebSocket e HTTP/1. Nella tabella seguente vengono fornite le indicazioni generali per la scelta del protocollo:

| Protocol | Quando scegliere questo protocollo |
| --- | --- |
| MQTT |Usare in tutti i dispositivi che non richiedono l'uso di WebSockets. |
| AMQPS |Usare in gateway cloud e sul campo per sfruttare il vantaggio della connessione multiplexing tra dispositivi. <br/> Da usare quando è necessario connettersi tramite la porta 443. |
| HTTPS |Usare per i dispositivi che non supportano altri protocolli. |

Tenere in considerazione quanto segue quando si sceglie il protocollo per le comunicazioni sul dispositivo:

* **Modello da cloud a dispositivo**. HTTP/1 non offre un modo efficiente per implementare il push del server. Di conseguenza, quando si usa HTTP/1 i dispositivi eseguono il polling dell'hub IoT per i messaggi da cloud a dispositivo. Questo approccio è molto inefficiente per il dispositivo e per l'hub IoT. In base alle attuali linee guida di HTTP/1, ogni dispositivo dovrebbe eseguire il polling almeno ogni 25 minuti. D'altra parte, MQTT e AMQP supportano il push del server quando si ricevono messaggi da cloud a dispositivo, consentendo il push immediato dei messaggi dall'hub IoT al dispositivo. Se la latenza del recapito rappresenta un problema, è consigliabile usare il protocollo AMQP o MQTT. Per i dispositivi che si connettono raramente, è possibile usare anche il protocollo HTTP/1.
* **Gateway sul campo** Quando si usano HTTP/1 e MQTT, non è possibile connettere più dispositivi, ognuno con le proprie credenziali per dispositivo, con la stessa connessione TLS. Di conseguenza, questi protocolli non sono ottimali per [scenari di gateway sul campo][lnk-azure-gateway-guidance] perché richiedono una connessione TLS tra il gateway sul campo e l'hub IoT per ogni dispositivo connesso al gateway sul campo.
* **Dispositivi con risorse ridotte**. Le librerie di MQTT e HTTP/1 hanno un footprint inferiore rispetto alle librerie di AMQP. Di conseguenza, se il dispositivo ha risorse limitate, ad esempio meno di 1 MB di RAM, questi protocolli potrebbero costituire l'unica implementazione disponibile.
* **Attraversamento rete**. Lo standard MQTT è in ascolto sulla porta 8883 che potrebbe provocare problemi nelle reti chiuse ai protocolli non HTTP. HTTP e AMQP, su WebSockets, sono entrambi disponibili per l'uso in questo scenario.
* **Dimensioni del payload**. AMQP e MQTT sono protocolli binari, quindi hanno payload significativamente più compatti rispetto a HTTP/1.

> [!NOTE]
> Quando si usa HTTP/1, ogni dispositivo deve eseguire il polling dei messaggi da cloud a dispositivo ogni 25 minuti o più. In fase di sviluppo è comunque accettabile eseguire il polling con una frequenza maggiore di 25 minuti.
> 
> 

<a id="mqtt-support">

#### Note sul supporto di MQTT
L'hub IoT implementa il protocollo MQTT v3.1.1 con le limitazioni e il comportamento specifico seguenti:

* **QoS 2 non è supportato**. Quando un client del dispositivo pubblica un messaggio con **QoS 2**, l'hub IoT chiude la connessione di rete. Quando un client del dispositivo sottoscrive un argomento con **QoS 2**, l'hub IoT concede il livello QoS 1 massimo nel pacchetto **SUBACK**.
* **I messaggi di mantenimento non vengono resi persistenti**. Se un client del dispositivo pubblica un messaggio con il flag RETAIN impostato su 1, l'hub IoT aggiunge al messaggio la proprietà dell'applicazione **x-opt-retain**. In tal caso, l'hub IoT non rende persistente il messaggio di mantenimento, ma lo passa invece all'applicazione back-end.

Per altre informazioni, vedere [Supporto di MQTT nell'hub IoT][lnk-mqtt-support].

È infine consigliabile rivedere la sezione [Gateway del protocollo IoT Azure][lnk-azure-protocol-gateway], che descrive come sia possibile distribuire un gateway del protocollo personalizzato a prestazioni elevate che si interfaccia direttamente con l'hub IoT. Il gateway del protocollo IoT Azure consente di personalizzare il protocollo del dispositivo per supportare le distribuzioni di MQTT cosiddette "brownfield" o altri protocolli personalizzati. Questo approccio richiede tuttavia il self-hosting e la gestione di un gateway di protocollo personalizzato.

### Dispositivo a cloud <a id="d2c"></a>
Come descritto nella sezione [Endpoint](#endpoints), i messaggi da dispositivo a cloud vengono inviati tramite un endpoint per dispositivo (**/devices/{deviceId}/messages/events**). I messaggi vengono ricevuti tramite un endpoint per servizio (**/messages/events**) che è compatibile con [Hub eventi][lnk-event-hubs]. È quindi possibile usare l'integrazione standard di Hub eventi e gli SDK per ricevere i messaggi da dispositivo a cloud.

L'hub IoT implementa la messaggistica da dispositivo a cloud in modo simile ad [Hub eventi][lnk-event-hubs]. I messaggi da dispositivo a cloud dell'hub IoT sono molto più simili agli *eventi* di Hub eventi rispetto ai *messaggi* del [bus di servizio][lnk-servicebus].

Questa implementazione presenta le implicazioni seguenti:

* Analogamente agli eventi di Hub eventi, i messaggi da dispositivo a cloud sono durevoli e vengono mantenuti in un hub IoT per un massimo di sette giorni. Vedere la sezione [Opzioni di configurazione da dispositivo a cloud](#d2cconfiguration).
* I messaggi da dispositivo a cloud vengono suddivisi in un set fisso di partizioni impostato in fase di creazione. Vedere la sezione [Opzioni di configurazione da dispositivo a cloud](#d2cconfiguration).
* Analogamente agli hub eventi, i client che leggono i messaggi da dispositivo a cloud devono gestire le partizioni e l'impostazione del checkpoint. Vedere [Hub eventi - Utilizzo di eventi][lnk-event-hubs-consuming-events].
* Analogamente agli eventi di Hub eventi, i messaggi da dispositivo a cloud possono avere dimensioni massime pari a 256 KB e possono essere raggruppati in batch per ottimizzare gli invii. I batch possono avere dimensioni massime di 256 KB e possono includere al massimo 500 messaggi.

Esistono tuttavia alcune differenze importanti tra i messaggi da dispositivo a cloud dell'hub IoT e Hub eventi:

* Come illustrato nella sezione [Sicurezza](#security), l'hub IoT consente il controllo di accesso e l'autenticazione per singoli dispositivi.
* L'hub IoT consente milioni di dispositivi connessi simultaneamente (vedere [Quote e limitazione](#throttling)), mentre gli hub eventi sono limitati a 5000 connessioni AMQP per ogni spazio dei nomi.
* L'hub IoT non consente il partizionamento arbitrario tramite **PartitionKey**. I messaggi da dispositivo a cloud vengono partizionati in base al valore **deviceId** di origine.
* Il ridimensionamento dell'hub IoT è leggermente diverso rispetto al ridimensionamento dell'hub eventi. Per altre informazioni, vedere [Ridimensionamento dell'hub IoT][lnk-guidance-scale].

Si noti che ciò non significa che sia possibile sostituire Hub eventi con l'hub IoT in tutti gli scenari. Ad esempio, in alcuni calcoli di elaborazione eventi potrebbe essere necessario ripartizionare gli eventi rispetto a una proprietà o un campo diverso prima di analizzare i flussi dei dati. In questo scenario è possibile usare Hub eventi per disaccoppiare due porzioni della pipeline di elaborazione dei flussi. Per altre informazioni, vedere la sezione *Partizioni* in [Panoramica di Hub eventi di Azure][lnk-eventhub-partitions].

Per informazioni dettagliate sull'uso della messaggistica da dispositivo a cloud, vedere [API e SDK dell'hub IoT][lnk-sdks].

> [!NOTE]
> Quando si usa HTTP per inviare messaggi da dispositivo a cloud, i valori e i nomi di proprietà possono contenere solo caratteri ASCII alfanumerici più ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.
> 
> 

#### Traffico non di telemetria
Spesso, oltre ai punti dati di telemetria, i dispositivi inviano anche messaggi e richieste che devono essere eseguiti e gestiti dal livello della logica di business dell'applicazione. Ad esempio, avvisi critici che devono attivare un'azione specifica nel back-end o risposte a comandi inviati dal back-end.

Per altre informazioni sul modo migliore per elaborare questa tipologia di messaggi, vedere [Esercitazione: elaborare messaggi da dispositivo a cloud dell'hub IoT][lnk-guidance-d2c-processing].

#### Opzioni di configurazione da dispositivo a cloud <a id="d2cconfiguration"></a>
Un hub IoT espone le proprietà seguenti per consentire il controllo della messaggistica da dispositivo a cloud.

* **Conteggio partizioni**. Impostare questa proprietà in fase di creazione per definire il numero di partizioni per l'inserimento di eventi da dispositivo a cloud.
* **Periodo di memorizzazione**. Questa proprietà specifica il periodo di memorizzazione per i messaggi da dispositivo a cloud. Il valore predefinito è un giorno, ma può essere aumentato a sette giorni.

Analogamente ad Hub eventi, l'hub IoT consente di gestire i gruppi di consumer nell'endpoint di ricezione da dispositivo a cloud.

È possibile modificare tutte queste proprietà a livello di codice con le [API del provider di risorse dell'hub IoT di Azure][lnk-resource-provider-apis] oppure usando il [portale di Azure][lnk-management-portal].

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
Come descritto nella sezione [Endpoint](#endpoints), è possibile inviare messaggi da cloud a dispositivo tramite un endpoint per servizio (**/messages/devicebound**). Un dispositivo può riceverli tramite un endpoint per dispositivo (**/devices/{deviceId}/messages/devicebound**).

Ogni messaggio da cloud a dispositivo è destinato a un singolo dispositivo, con impostazione della proprietà **to** su **/devices/{deviceId}/messages/devicebound**.

> [!IMPORTANT]
> Ogni coda di dispositivo può includere al massimo 50 messaggi da cloud a dispositivo. Se si prova a inviare più messaggi allo stesso dispositivo, si verificherà un errore.
> 
> [!NOTE]
> Quando si inviano messaggi da cloud a dispositivo, i valori e i nomi di proprietà possono contenere solo caratteri ASCII alfanumerici più ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.
> 
> 

#### Ciclo di vita del messaggio <a id="message lifecycle"></a>
Per implementare la garanzia di almeno un recapito, i messaggi da cloud a dispositivo vengono salvati in modo permanente nelle code per dispositivo. I dispositivi devono confermare esplicitamente il *completamento* per consentire all'hub IoT di rimuoverli dalla coda. Ciò garantisce la resilienza rispetto a errori di connettività e dei dispositivi.

Il diagramma seguente illustra il grafico sullo stato del ciclo di vita per un messaggio da cloud a dispositivo.

![Ciclo di vita dei messaggi da cloud a dispositivo][img-lifecycle]

Quando il servizio invia un messaggio, questo viene considerato *Accodato*. Quando un dispositivo accetta di *ricevere* un messaggio, l'hub IoT *blocca* il messaggio (impostando lo stato su **Invisibile**) per consentire ad altri thread nello stesso dispositivo di iniziare a ricevere altri messaggi. Quando un thread del dispositivo termina l'elaborazione di un messaggio, invia una notifica all'hub IoT *completando* il messaggio.

Un dispositivo può anche:

* *Rifiutare* il messaggio. In questo caso, l'hub IoT ne imposta lo stato su **Non recapitabile**.
* *Abbandonare* il messaggio. In questo caso, l'hub IoT inserisce di nuovo il messaggio nella coda con lo stato impostato su **Accodato**.

Un thread potrebbe non riuscire a elaborare un messaggio senza inviare una notifica all'hub IoT. In questo caso, i messaggi passano automaticamente dallo stato **Invisibile** allo stato **Accodato** dopo un *timeout di visibilità o di blocco*. Il valore predefinito di questo timeout è un minuto.

Un messaggio può passare dallo stato **Accodato** allo stato **Invisibile** e viceversa per il numero massimo di volte specificato nella proprietà **maxDeliveryCount** dell'hub IoT. Dopo il numero di transizioni specificato, l'hub IoT imposta lo stato del messaggio su **Non recapitabile**. Analogamente, l'hub IoT imposta lo stato di un messaggio su **Non recapitabile** dopo la relativa scadenza. Vedere la sezione relativa alla [durata (TTL)](#ttl).

Per un'esercitazione sui messaggi da cloud a dispositivo, vedere [Introduzione ai messaggi da cloud a dispositivo dell'hub IoT di Azure][lnk-getstarted-c2d-tutorial]. Per argomenti di riferimento sul modo in cui le diverse API e i vari SDK espongono la funzionalità da cloud a dispositivo, vedere [API e SDK dell'hub IoT][lnk-sdks].

> [!NOTE]
> I messaggi da cloud a dispositivo vengono in genere completati quando la perdita del messaggio non influisce sulla logica dell'applicazione. Ad esempio, il contenuto del messaggio è stato salvato in modo permanente nell'archivio locale o un'operazione è stata eseguita correttamente. Il messaggio potrebbe anche includere informazioni temporanee la cui perdita non influirebbe sulla funzionalità dell'applicazione. In alcuni casi, per le attività con esecuzione prolungata, è possibile completare il messaggio da cloud a dispositivo dopo aver salvato in modo permanente la descrizione dell'attività nell'archivio locale. È quindi possibile inviare al back-end dell'applicazione una notifica con uno o più messaggi da dispositivo a cloud in diverse fasi di avanzamento dell'attività.
> 
> 

#### Scadenza del messaggio (durata) <a id="ttl"></a>
Ogni messaggio da cloud a dispositivo ha una scadenza. Questa durata può essere impostata esplicitamente dal servizio nella proprietà **ExpiryTimeUtc** oppure viene configurata dall'hub IoT usando il valore di *durata (TTL)* predefinito specificato come proprietà dell'hub IoT. Vedere [Opzioni di configurazione da cloud a dispositivo](#c2dconfiguration).

> [!NOTE]
> Un modo comune per sfruttare i vantaggi della scadenza del messaggio consiste nell'impostare una breve durata dei valori attivi per evitare l'invio di messaggi a dispositivi disconnessi. Questo approccio consente di ottenere lo stesso risultato del mantenimento dello stato di connessione del dispositivo, pur essendo molto più efficiente. Richiedendo gli acknowledgement dei messaggi, è anche possibile ricevere una notifica tramite l'hub IoT indicante i dispositivi in grado di ricevere i messaggi, i dispositivi offline o quelli in stato di errore.
> 
> 

#### Commenti sul messaggio <a id="feedback"></a>
Durante l'invio di messaggi da cloud a dispositivo, il servizio può richiedere il recapito di commenti specifici per ogni messaggio in merito allo stato finale del messaggio.

* Se si imposta la proprietà **Ack** su **positive**, l'hub IoT genera un messaggio con commenti solo se il messaggio da cloud a dispositivo ha raggiunto lo stato **Completato**.
* Se si imposta la proprietà **Ack** su **negative**, l'hub IoT genera un messaggio con commenti solo se il messaggio da cloud a dispositivo raggiunge lo stato **Non recapitabile**.
* Se si imposta la proprietà **Ack** su **full**, l'hub IoT genera un messaggio con commenti in entrambi i casi.

> [!NOTE]
> Se la proprietà **Ack** è impostata su **full** e non si riceve un messaggio con commenti, significa che il messaggio è scaduto. Il servizio non può sapere cosa è successo al messaggio originale. In pratica, un servizio deve garantire che sia possibile elaborare i commenti prima della scadenza. Il periodo di scadenza massimo è di due giorni ed è quindi disponibile un tempo sufficiente per ripristinare il servizio in caso di errore.
> 
> 

Come illustrato nella sezione [Endpoint](#endpoints), l'hub IoT recapita commenti sotto forma di messaggi tramite un endpoint per servizio (**/messages/servicebound/feedback**). La semantica di ricezione per i commenti è uguale a quella dei messaggi da cloud a dispositivo e ha lo stesso [Message lifecycle](#message lifecycle). Quando possibile, i commenti sul messaggio vengono riuniti in un singolo messaggio con il formato seguente.

Ogni messaggio recuperato da un dispositivo dall'endpoint di commenti ha le proprietà seguenti:

| Proprietà | Descrizione |
| --- | --- |
| EnqueuedTime |Timestamp che indica quando è stato creato il messaggio. |
| UserId |`{iot hub name}` |
| ContentType |`application/vnd.microsoft.iothub.feedback.json` |

Il corpo è una matrice serializzata con JSON dei record, ognuno con le proprietà seguenti:

| Proprietà | Descrizione |
| --- | --- |
| EnqueuedTimeUtc |Timestamp che indica quando è stato creato il risultato del messaggio. Ad esempio, il dispositivo ha completato l'operazione o il messaggio è scaduto. |
| OriginalMessageId |**MessageId** del messaggio da cloud a dispositivo correlato a queste informazioni sui commenti. |
| StatusCode |Numero intero obbligatorio. Usato nei messaggi con commenti generati dall'hub IoT. <br/> 0 = esito positivo <br/> 1 = messaggio scaduto <br/> 2 = numero massimo di recapiti superato <br/> 3 = messaggio rifiutato |
| Descrizione |Valori stringa per **StatusCode**. |
| DeviceId |**DeviceId** del messaggio da cloud a dispositivo correlato a queste informazioni sui commenti. |
| DeviceGenerationId |**DeviceGenerationId** del dispositivo di destinazione del messaggio da cloud a dispositivo correlato a queste informazioni sui commenti. |

> [!IMPORTANT]
> Il servizio deve specificare un valore **MessageId** per il messaggio da cloud a dispositivo per poter correlare i commenti al messaggio originale.
> 
> 

L'esempio seguente illustra il corpo di un messaggio con commenti.

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
| --- | --- | --- |
| defaultTtlAsIso8601 |Durata (TTL) predefinita per messaggi da cloud a dispositivo. |Intervallo ISO\_8601 fino a 2D (almeno 1 minuto). Predefinito: 1 ora. |
| maxDeliveryCount |Numero massimo di recapiti per code da cloud a dispositivo per i singoli dispositivi. |Da 1 a 100. Predefinito: 10. |
| feedback.ttlAsIso8601 |Conservazione per messaggi con commenti diretti al servizio. |Intervallo ISO\_8601 fino a 2D (almeno 1 minuto). Predefinito: 1 ora. |
| feedback.maxDeliveryCount |Numero massimo di recapiti per la coda di commenti. |Da 1 a 100. Predefinito: 100. |

Per altre informazioni, vedere [Gestire hub IoT tramite il portale di Azure][lnk-portal].

### Caricamenti di file <a id="fileupload"></a>
Come descritto nella sezione [Endpoint](#endpoints), i dispositivi possono avviare caricamenti di file inviando una notifica tramite un endpoint per dispositivo (**/devices/{deviceId}/files**). Quando un dispositivo notifica all'hub IoT il completamento di un caricamento, l'hub IoT genera notifiche di caricamento file che possono essere ricevute sotto forma di messaggi tramite un endpoint per servizio (**/messages/servicebound/filenotifications**).

I messaggi non vengono negoziati tramite l'hub IoT, che funge invece da strumento di recapito per un account di archiviazione di Azure associato. Un dispositivo richiede dall'hub IoT un token di archiviazione specifico del file che intende caricare. Il dispositivo usa l'URI di firma di accesso condiviso per caricare il file nella risorsa di archiviazione e al termine del caricamento invia una notifica di completamento all'hub IoT. L'hub IoT verifica che il file sia stato caricato e quindi aggiunge una notifica di caricamento file al nuovo endpoint di messaggistica per servizio per le notifiche relative ai file.

#### Associazione di un account di archiviazione di Azure all'hub IoT
Per usare la funzionalità di caricamento file, è prima necessario collegare un account di archiviazione di Azure all'hub IoT. Questa operazione può essere eseguita con il [portale di Azure][lnk-management-portal] oppure a livello di codice con le [API del provider di risorse dell'hub IoT di Azure][lnk-resource-provider-apis]. Dopo l'associazione di un account di archiviazione all'hub IoT, quando un dispositivo avvia una richiesta di caricamento file il servizio restituisce al dispositivo un URI di firma di accesso condiviso.

> [!NOTE]
> Gli [SDK hub IoT][lnk-sdks] di Azure gestiscono automaticamente il recupero dell'URI di firma di accesso condiviso, il caricamento del file e la notifica all'hub IoT del completamento del caricamento.
> 
> 

#### Inizializzazione del caricamento di un file
Per supportare il caricamento di file, l'hub IoT usa due endpoint REST: uno per ottenere l'URI di firma di accesso condiviso per l'archiviazione e uno per la notifica all'hub IoT del completamento del caricamento. Il dispositivo avvia il processo di caricamento file inviato una richiesta GET all'hub IoT in `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`. L'hub restituisce un URI di firma di accesso condiviso specifico del file da caricare e un ID di correlazione da usare al termine del caricamento.

#### Notifica all'hub IoT del completamento del caricamento di un file
Il dispositivo è responsabile del caricamento del file nella risorsa di archiviazione con Azure Storage SDK. Al termine del caricamento, il dispositivo invia una richiesta POST all'hub IoT in `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications/{correlationId}` usando l'ID di correlazione ricevuto dalla richiesta GET iniziale.

#### Notifiche di caricamento file
Quando un dispositivo carica un file e notifica all'hub IoT il completamento del caricamento, il servizio genera facoltativamente un messaggio di notifica contenente il nome e la posizione di archiviazione del file.

Come illustrato nella sezione [Endpoint](#endpoints), l'hub IoT recapita le notifiche di caricamento file sotto forma di messaggi tramite un endpoint per servizio (**/messages/servicebound/fileuploadnotifications**). La semantica di ricezione per le notifiche di caricamento file è uguale a quella dei messaggi da cloud a dispositivo e ha lo stesso [Ciclo di vita dei messaggi](#ciclo di vita dei messaggi). Ogni messaggio recuperato dall'endpoint delle notifiche di caricamento file è un record JSON con le proprietà seguenti.

| Proprietà | Descrizione |
| --- | --- |
| EnqueuedTimeUtc |Timestamp che indica quando è stata creata la notifica. |
| DeviceId |**DeviceId** del dispositivo che ha caricato il file. |
| BlobUri |URI del file caricato. |
| BlobName |Nome del file caricato. |
| LastUpdatedTime |Timestamp che indica quando è stato eseguito l'ultimo aggiornamento del file. |
| BlobSizeInBytes |Dimensioni del file caricato. |

**Esempio**. Esempio del corpo di un messaggio di notifica di caricamento file:

```
{
    "deviceId":"mydevice",
    "blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
    "blobName":"mydevice/myfile.jpg",
    "lastUpdatedTime":"2016-06-01T21:22:41+00:00",
    "blobSizeInBytes":1234,
    "enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

#### Opzioni di configurazione per le notifiche di caricamento file <a id="c2dconfiguration"></a>
Ogni hub IoT espone le opzioni di configurazione seguenti per le notifiche di caricamento file.

| Proprietà | Descrizione | Intervallo e valore predefinito |
| --- | --- | --- |
| **enableFileUploadNotifications** |Controlla se verranno scritte notifiche di caricamento file nell'endpoint per le notifiche relative ai file. |Valore booleano. Predefinito: True. |
| **fileNotifications.ttlAsIso8601** |Durata (TTL) predefinita per le notifiche di caricamento file. |Intervallo ISO\_8601 fino a 48 ore (minimo 1 minuto). Predefinito: 1 ora. |
| **fileNotifications.lockDuration** |Durata del blocco per la coda delle notifiche di caricamento file. |Da 5 a 300 secondi (minimo 5 secondi). Predefinito: 60 secondi. |
| **fileNotifications.maxDeliveryCount** |Numero massimo di recapiti per la coda delle notifiche di caricamento file. |Da 1 a 100. Predefinito: 100. |

Per altre informazioni, vedere [Gestire hub IoT tramite il portale di Azure][lnk-portal].

## Quote e limitazione <a id="throttling"></a>
Ogni sottoscrizione di Azure può avere al massimo 10 hub IoT.

Ogni hub IoT viene sottoposto a provisioning con un determinato numero di unità in uno SKU specifico (per altre informazioni, vedere [Prezzi dell'hub IoT di Azure][lnk-pricing].) Lo SKU e il numero di unità determinano la quota giornaliera massima dei messaggi che è possibile inviare.

Lo SKU determina anche le limitazioni che l'hub IoT applica alle operazioni.

### Limitazioni per le operazioni
Le limitazioni per le operazioni sono limitazioni di frequenza applicate con intervalli di minuti e consentono di evitare abusi. L'hub IoT prova a evitare la restituzione di errori, se possibile, ma inizia a restituire eccezioni se la limitazione viene violata troppo a lungo.

Ecco di seguito l'elenco di limitazioni applicate. I valori fanno riferimento a un singolo hub.

| Limitazione | Valore per ogni hub |
| --- | --- |
| Operazioni del registro delle identità (creazione, recupero, elenco, aggiornamento, eliminazione) |5000/min/unità (per S3),<br/> 100/min/unità (per S1 e S2). |
| Connessioni del dispositivo |6000/sec/unità (per S3), 120/sec/unità (per S2), 12/sec/unità (per S1). <br/>Minimo 100/sec. <br/> Ad esempio, due unità S1 sono 2*12 = 24/sec, ma si hanno almeno 100/sec tra le unità. Con nove unità S1 si otterrà 108/sec (9*12) tra le unità. |
| Inoltri dal dispositivo al cloud |6000/sec/unità (per S3), 120/sec/unità (per S2), 12/sec/unità (per S1). <br/>Minimo 100/sec. <br/> Ad esempio, due unità S1 sono 2*12 = 24/sec, ma si hanno almeno 100/sec tra le unità. Con nove unità S1 si otterrà 108/sec (9*12) tra le unità. |
| Inoltri dal cloud al dispositivo |5000/min/unità (per S3), 100/min/unità (per S1 e S2). |
| Ricezioni dal cloud al dispositivo |50000/min/unità (per S3), 1000/min/unità (per S1 e S2). |
| Operazioni di caricamento file |5000 notifiche di caricamento file/min/unità (per S3), 100 notifiche di caricamento file/min/unità (per S1 e S2). <br/> 10000 URI di firma di accesso condiviso possono essere generati contemporaneamente per un account di archiviazione.<br/> 10 URI di firma di accesso condiviso/dispositivo possono essere generati contemporaneamente. |

È importante chiarire che la limitazione delle *connessioni del dispositivo* determina la frequenza con cui possono essere stabilite nuove connessioni del dispositivo con un hub IoT e non il numero massimo di dispositivi connessi contemporaneamente. La limitazione dipende dal numero di unità di cui viene eseguito il provisioning per l'hub.

Ad esempio, se si acquista una singola unità S1, si ottiene un limite di 100 connessioni al secondo. Ciò significa che per connettere 100.000 dispositivi sono necessari almeno 1000 secondi (circa 16 minuti). Tuttavia, è consentito un numero di dispositivi connessi simultaneamente pari al numero di dispositivi registrati nel registro delle identità dei dispositivi.

Per un'analisi approfondita del comportamento della limitazione dell'hub IoT, vedere il post del blog [IoT Hub throttling and you][lnk-throttle-blog] \(Limitazione dell'hub IoT).

> [!NOTE]
> È possibile incrementare le quote o le limitazioni in qualsiasi momento aumentando il numero di unità sottoposte a provisioning in un hub IoT.
> 
> [!IMPORTANT]
> Le operazioni del registro delle identità sono destinate all'uso in fase di esecuzione negli scenari di gestione e provisioning dei dispositivi. L'operazione di lettura o aggiornamento di un numero elevato di identità dei dispositivi è supportata tramite i [processi di importazione ed esportazione](#importexport).
> 
> 

## Passaggi successivi
Al termine di questa panoramica dello sviluppo per l'hub IoT, è possibile usare i collegamenti seguenti per altre informazioni:

* [Caricare i file dai dispositivi (esercitazione)][lnk-file upload]
* [Creare un hub IoT a livello di codice][lnk-create-hub]
* [Introduzione a C SDK][lnk-c-sdk]
* [SDK hub IoT][lnk-sdks]

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Progettare una soluzione][lnk-design]
* [Esplorare la Gestione dei dispositivi dell'hub IoT di Azure usando l'interfaccia utente di esempio][lnk-dmui]
* [Simulazione di un dispositivo con Gateway SDK][lnk-gateway]
* [Gestire hub IoT tramite il portale di Azure][lnk-portal]
* [Proteggere la soluzione IoT sin dall'inizio][lnk-securing]

[lnk-eventprocessorhost]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx

[img-endpoints]: ./media/iot-hub-devguide/endpoints.png
[img-lifecycle]: ./media/iot-hub-devguide/lifecycle.png
[img-eventhubcompatible]: ./media/iot-hub-devguide/eventhubcompatible.png

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx

[lnk-sas-tokens]: iot-hub-sas-tokens.md
[lnk-azure-gateway-guidance]: iot-hub-guidance.md#field-gateways
[lnk-guidance-provisioning]: iot-hub-guidance.md#provisioning
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-guidance-security]: iot-hub-guidance.md#customauth
[lnk-guidance-heartbeat]: iot-hub-guidance.md#heartbeat

[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-getstarted-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md

[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../resource-group-overview.md
[lnk-azure-resource-manager]: https://azure.microsoft.com/documentation/articles/resource-group-overview/
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-event-hubs-consuming-events]: ../event-hubs/event-hubs-programming-guide.md#event-consumers
[lnk-guidance-d2c-processing]: iot-hub-csharp-csharp-process-d2c.md
[lnk-management-portal]: https://portal.azure.com
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-tls]: https://tools.ietf.org/html/rfc5246
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-eventhub-partitions]: ../event-hubs/event-hubs-overview.md#partitions
[lnk-mqtt-support]: iot-hub-mqtt-support.md
[lnk-throttle-blog]: https://azure.microsoft.com/blog/iot-hub-throttling-and-you/
[lnk-servicebus-sdk]: https://www.nuget.org/packages/WindowsAzure.ServiceBus

[lnk-file upload]: iot-hub-csharp-csharp-file-upload.md
[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-sdks-summary.md

[lnk-design]: iot-hub-guidance.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md
[lnk-securing]: iot-hub-security-ground-up.md

<!---HONumber=AcomDC_0907_2016-->