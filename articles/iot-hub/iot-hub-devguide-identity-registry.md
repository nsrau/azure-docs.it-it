---
title: "Informazioni sul registro delle identità dell'hub IoT di Azure | Documentazione Microsoft"
description: "Guida per gli sviluppatori: descrizione del registro delle identità dell'hub IoT e come usarlo per gestire i dispositivi. Include informazioni sull'importazione e sull'esportazione in blocco delle identità dei dispositivi."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 0706eccd-e84c-4ae7-bbd4-2b1a22241147
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/19/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 653c31fb1115c79216f882a52484cd37303e0322
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2018
---
# <a name="understand-the-identity-registry-in-your-iot-hub"></a>Comprendere il registro delle identità nell'hub IoT

Ogni hub IoT ha un registro delle identità in cui sono archiviate le informazioni sui dispositivi a cui è consentito connettersi all'hub IoT. Prima che un dispositivo possa connettersi a un hub IoT, è necessario che sia presente una voce relativa al dispositivo nel registro delle identità dell'hub IoT. Un dispositivo deve autenticarsi anche con l'hub IoT in base alle credenziali archiviate nel registro delle identità.

L'ID dispositivo archiviato nel registro delle identità fa distinzione tra maiuscole e minuscole.

A livello generale, il registro delle identità è una raccolta delle risorse relative alle identità dei dispositivi che supporta REST. Quando si aggiunge una voce nel registro delle identità, l'hub IoT crea un set di risorse per dispositivo, ad esempio una coda contenente messaggi da cloud a dispositivo in elaborazione.

Usare il registro delle identità quando è necessario:

* Il provisioning dei dispositivi che si connettono per l'hub IoT.
* Controllare l'accesso per dispositivo agli endpoint che usano dispositivi dell'hub.

> [!NOTE]
> Il registro delle identità non contiene metadati specifici delle applicazioni.

## <a name="identity-registry-operations"></a>Operazioni del registro delle identità

Il registro delle identità dell'hub IoT espone le operazioni seguenti:

* Creare l'identità del dispositivo
* Aggiornare l'identità del dispositivo
* Recuperare l'identità del dispositivo tramite ID
* Eliminare l'identità del dispositivo
* Elencare al massimo 1000 identità
* Esportare tutte le identità nell'Archiviazione BLOB di Azure
* Importare le identità nell'Archiviazione BLOB di Azure

Tutte queste operazioni possono usare la concorrenza ottimistica, come specificato in [RFC7232][lnk-rfc7232].

> [!IMPORTANT]
> L'unico modo per recuperare tutte le identità nel registro delle identità di un hub IoT consiste nell'usare la funzionalità [Esporta][lnk-export].

Un registro delle identità di un hub IoT:

* Non contiene metadati delle applicazioni.
* È accessibile come un dizionario, usando il valore **deviceId** come chiave.
* Non supporta le query espressive.

Una soluzione IoT include in genere un archivio separato specifico della soluzione che contiene metadati specifici dell'applicazione. Ad esempio, l'archivio specifico della soluzione in una soluzione per un edificio efficiente registra l'ambiente in cui viene distribuito un sensore di temperatura.

> [!IMPORTANT]
> Usare il registro delle identità solo per le operazioni di gestione e provisioning dei dispositivi. Le operazioni a velocità effettiva elevata in fase di esecuzione non devono dipendere dall'esecuzione di operazioni nel registro delle identità. Ad esempio, la verifica dello stato di connessione di un dispositivo prima dell'invio di un comando non è uno schema supportato. Assicurarsi di verificare le [frequenze di limitazione][lnk-quotas] per il registro delle identità dei dispositivi e il modello di [heartbeat del dispositivo][lnk-guidance-heartbeat].

## <a name="disable-devices"></a>Disabilitare i dispositivi

È possibile disabilitare i dispositivi aggiornando la proprietà **status** di un'identità nel registro delle identità. Questa proprietà viene in genere usata in due scenari:

* Durante un processo di orchestrazione di provisioning. Per altre informazioni, vedere [Provisioning di dispositivi][lnk-guidance-provisioning].
* Se per qualsiasi motivo si ritiene che un dispositivo sia compromesso o non più autorizzato.

## <a name="import-and-export-device-identities"></a>Importare ed esportare le identità dei dispositivi

È possibile esportare in blocco le identità dei dispositivi dal registro delle identità di un hub IoT usando operazioni asincrone sull'[endpoint del provider di risorse dell'hub IoT][lnk-endpoints]. Le esportazioni sono processi a esecuzione prolungata che usano un contenitore BLOB specificato dal cliente per salvare i dati relativi alle identità dei dispositivi letti dal registro delle identità.

È possibile importare in blocco le identità dei dispositivi nel registro delle identità di un hub IoT usando operazioni asincrone sull'[endpoint del provider di risorse dell'hub IoT][lnk-endpoints]. Le importazioni sono processi a esecuzione prolungata che usano un contenitore BLOB specificato dal cliente per scrivere i dati relativi alle identità nel registro delle identità.

Per informazioni dettagliate sulle API di importazione e di esportazione, vedere [IoT Hub resource provider REST APIs][lnk-resource-provider-apis] (API REST del provider di risorse dell'hub IoT). Per altre informazioni sull'esecuzione dei processi di importazione ed esportazione, vedere [Gestione in blocco delle identità dei dispositivi dell'hub IoT][lnk-bulk-identity].

## <a name="device-provisioning"></a>Provisioning di dispositivi

I dati del dispositivo archiviati da una soluzione IoT dipendono dai requisiti specifici di tale soluzione. Tuttavia, come minimo, una soluzione deve archiviare identità di dispositivo e chiavi di autenticazione. L'hub IoT di Azure include un registro delle identità in grado di archiviare i valori per ogni dispositivo, ad esempio ID, chiavi di autenticazione e codici di stato. Una soluzione può usare altri servizi di Azure, ad esempio archiviazione tabelle, Archiviazione BLOB o Cosmos DB per archiviare dati aggiuntivi sui dispositivi.

*Provisioning di dispositivi* è il processo di aggiunta dei dati iniziali dei dispositivi agli archivi nella soluzione. Per abilitare un nuovo dispositivo per la connessione all'hub è necessario aggiungere un ID dispositivo e le relative chiavi al registro delle identità dell'hub IoT. Come parte del processo di provisioning, può essere necessario inizializzare i dati specifici del dispositivo in altri archivi di soluzioni. Inoltre, è possibile utilizzare il servizio Device Provisioning dell'hub IoT di Azure per abilitare il provisioning automatico JIT per uno o più hub IoT senza la necessità dell'intervento umano. Per altre informazioni, vedere la [documentazione del servizio di provisioning][lnk-dps].

## <a name="device-heartbeat"></a>Heartbeat dispositivo

Il registro delle identità dell'hub IoT contiene un campo denominato **connectionState**. Durante le fasi di sviluppo e debug, usare solo il campo **connectionState**. Le soluzioni IoT non devono eseguire query sul campo in fase di esecuzione. Ad esempio, non devono eseguire query sul campo **connectionState** per verificare se un dispositivo è connesso prima di inviare un messaggio da cloud a dispositivo o un SMS.

Se la soluzione IoT deve sapere se un dispositivo è connesso, è necessario implementare il *modello di heartbeat*.

Nel modello di heartbeat il dispositivo invia messaggi da dispositivo a cloud almeno una volta ogni intervallo di tempo stabilito, ad esempio almeno una volta ogni ora. Di conseguenza, anche se in un dispositivo non sono presenti dati da inviare, viene comunque inviato un messaggio vuoto da dispositivo a cloud, in genere con una proprietà che lo identifica come heartbeat. Sul lato servizio, la soluzione gestisce una mappa con l'ultimo heartbeat ricevuto per ogni dispositivo e presuppone che sia presente un problema con il dispositivo se non riceve un messaggio di heartbeat entro il tempo previsto.

Un'implementazione più complessa può includere le informazioni acquisite dal [monitoraggio delle operazioni][lnk-devguide-opmon] per identificare i dispositivi che provano a connettersi o a comunicare ma con esito negativo. Quando si implementa il modello di heartbeat, assicurarsi di controllare [Quote e limitazioni dell'hub IoT][lnk-quotas].

> [!NOTE]
> Se una soluzione IoT usa lo stato di connessione esclusivamente per determinare se inviare i messaggi da cloud a dispositivo e i messaggi non vengono trasmessi a grandi set di dispositivi, è da considerare l'uso del criterio *a breve scadenza* più semplice. Un modello di questo tipo consente di gestire il registro dello stato di connessione del dispositivo in modo analogo a un modello di heartbeat, ma con maggiore efficienza. Se si richiede l'acknowledgement dei messaggi, l'hub IoT può inviare una notifica per indicare quali dispositivi possono ricevere messaggi e quali no.

## <a name="device-lifecycle-notifications"></a>Notifiche del ciclo di vita dei dispositivi

L'hub IoT può inviare una notifica alla soluzione IoT quando un'identità dispositivo viene creata o eliminata inviando notifiche del ciclo di vita dei dispositivi. A questo scopo, la soluzione IoT deve creare una route e impostare l'origine dati su *DeviceLifecycleEvents*. Per impostazione predefinita, non vengono inviate notifiche del ciclo di vita, il che significa che queste route non sono preesistenti. Il messaggio di notifica include le proprietà e il corpo.

Proprietà: le proprietà di sistema del messaggio hanno come prefisso il simbolo `'$'`.

| Nome | Valore |
| --- | --- |
$content-type | application/json |
$iothub-enqueuedtime |  Data e ora in cui è stata inviata la notifica |
$iothub-message-source | deviceLifecycleEvents |
$content-encoding | utf-8 |
opType | **createDeviceIdentity** o **deleteDeviceIdentity** |
hubName | Nome dell'hub IoT |
deviceId | ID del dispositivo |
operationTimestamp | Timestamp ISO8601 dell'operazione |
iothub-message-schema | deviceLifecycleNotification |

Corpo: questa sezione è in formato JSON e rappresenta la copia dell'identità dispositivo creata. Ad esempio,

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "etag":"AAAAAAAAAAE=",
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        }
    }
}
```

## <a name="device-identity-properties"></a>Proprietà delle identità dei dispositivi

Le identità dei dispositivi vengono rappresentate da documenti JSON con le proprietà indicate di seguito.

| Proprietà | Opzioni | Descrizione |
| --- | --- | --- |
| deviceId |Obbligatoria, di sola lettura negli aggiornamenti |Stringa con distinzione tra maiuscole/minuscole (lunghezza massima 128 caratteri) con caratteri alfanumerici ASCII a 7 bit più alcuni caratteri speciali: `- : . + % _ # * ? ! ( ) , = @ ; $ '`. |
| generationId |Obbligatoria, di sola lettura |Stringa con distinzione tra maiuscole/minuscole generata dall'hub IoT con lunghezza massima di 128 caratteri. Tale valore viene usato per distinguere i dispositivi con la stessa proprietà **deviceId** in caso di eliminazione e nuova creazione. |
| etag |Obbligatoria, di sola lettura |Stringa che rappresenta un ETag vulnerabile per l'identità del dispositivo, come indicato in [RFC7232][lnk-rfc7232]. |
| auth |facoltativo |Oggetto composito contenente le informazioni di autenticazione e i materiali di sicurezza. |
| auth.symkey |facoltativo |Oggetto composito contenente una chiave primaria e una chiave secondaria, archiviate in formato Base 64. |
| status |Obbligatoria |Indicatore di accesso. Può essere **Enabled** o **Disabled**. Se è **Enabled**, il dispositivo sarà autorizzato alla connessione. Se è **Disabled**, il dispositivo non potrà accedere ad alcun endpoint per il dispositivo. |
| statusReason |facoltativo |Stringa con 128 caratteri di lunghezza che memorizza il motivo dello stato dell'identità del dispositivo. Sono consentiti tutti i caratteri UTF-8. |
| statusUpdateTime |Sola lettura |Indicatore temporale che mostra la data e l'ora dell'ultimo aggiornamento dello stato. |
| connectionState |Sola lettura |Campo indicante lo stato della connessione: **Connected** o **Disconnected**. Questo campo rappresenta la visualizzazione Hub IoT dello stato di connessione del dispositivo. **Importante**: è consigliabile usare questo campo solo per scopi di sviluppo e di debug. Lo stato di connessione viene aggiornato solo per i dispositivi che usano MQTT o AMQP. Si basa anche su ping a livello di protocollo (ping MQTT o AMQP) e può avere un ritardo massimo di soli 5 minuti. Per questi motivi possono essere presenti falsi positivi, ad esempio dispositivi segnalati come connessi, ma in realtà disconnessi. |
| connectionStateUpdatedTime |Sola lettura |Indicatore temporale che mostra la data e l'ora dell'ultimo aggiornamento dello stato della connessione. |
| lastActivityTime |Sola lettura |Indicatore temporale che mostra la data e l'ora in cui il dispositivo si è connesso oppure ha ricevuto o inviato un messaggio per l'ultima volta. |

> [!NOTE]
> Lo stato della connessione può rappresentare solo la visualizzazione Hub IoT dello stato della connessione. Gli aggiornamenti dello stato possono essere ritardati a seconda delle condizioni e delle configurazioni della rete.

## <a name="additional-reference-material"></a>Materiale di riferimento

Di seguito sono indicati altri argomenti di riferimento reperibili nella Guida per gli sviluppatori dell'hub IoT:

* [Endpoint dell'hub IoT][lnk-endpoints] illustra i diversi endpoint esposti da ogni hub IoT per operazioni della fase di esecuzione e di gestione.
* [Quote e limitazioni][lnk-quotas] descrive le quote e i comportamenti di limitazione applicabili al servizio hub IoT.
* [Azure IoT SDK per dispositivi e servizi][lnk-sdks] elenca gli SDK nei diversi linguaggi che è possibile usare quando si sviluppano app per dispositivi e servizi che interagiscono con l'hub IoT.
* [Linguaggio di query dell'hub IoT][lnk-query] descrive il linguaggio di query che è possibile usare per recuperare informazioni dall'hub IoT sui dispositivi gemelli e sui processi.
* [Supporto di MQTT nell'hub IoT][lnk-devguide-mqtt] offre altre informazioni sul supporto dell'hub IoT per il protocollo MQTT.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come usare il registro delle identità dell'hub IoT, si potrebbe essere interessati agli argomenti seguenti della Guida per gli sviluppatori dell'hub IoT:

* [Controllare l'accesso all'hub IoT][lnk-devguide-security]
* [Use device twins to synchronize state and configurations][lnk-devguide-device-twins] (Usare dispositivi gemelli per sincronizzare lo stato e le configurazioni)
* [Richiamare un metodo diretto in un dispositivo][lnk-devguide-directmethods]
* [Pianificare processi in più dispositivi][lnk-devguide-jobs]

Per provare alcuni dei concetti descritti in questo articolo, può essere utile l'esercitazione seguente sull'hub IoT:

* [Introduzione all'hub IoT di Azure][lnk-getstarted-tutorial]

Per analizzare l'uso del servizio Device Provisioning dell'hub IoT per abilitare il provisioning automatico senza intervento umano, vedere: 

* [Servizio Device Provisioning in hub IoT di Azure][lnk-dps]


<!-- Links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-guidance-provisioning]: iot-hub-devguide-identity-registry.md#device-provisioning
[lnk-guidance-heartbeat]: iot-hub-devguide-identity-registry.md#device-heartbeat
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-export]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-devguide-opmon]: iot-hub-operations-monitoring.md

[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-dps]: https://azure.microsoft.com/documentation/services/iot-dps
