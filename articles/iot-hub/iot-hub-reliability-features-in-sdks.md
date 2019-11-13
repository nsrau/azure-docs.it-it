---
title: Gestire la connettività dell'hub Internet & la messaggistica affidabile con SDK per dispositivi
description: Informazioni su come migliorare le funzionalità di connettività e messaggistica dei dispositivi quando si usano gli SDK per dispositivi dell'hub IoT di Azure
services: iot-hub
author: robinsh
ms.author: robinsh
ms.date: 07/07/2018
ms.topic: article
ms.service: iot-hub
ms.openlocfilehash: 8774129b3a1d3c9a1095e7a7c478dd94086b5867
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954487"
---
# <a name="manage-connectivity-and-reliable-messaging-by-using-azure-iot-hub-device-sdks"></a>Gestire le funzionalità di connettività e messaggistica affidabile con gli Azure IoT Hub SDK per dispositivi

Questa guida fornisce indicazioni approfondite per progettare applicazioni per dispositivi più resilienti. Illustra anche come sfruttare i vantaggi offerti dalle funzionalità di connettività e messaggistica affidabile negli Azure IoT SDK per dispositivi. L'obiettivo di questa guida è fornire le informazioni necessarie per gestire gli scenari seguenti:

* Correzione di una connessione di rete interrotta

* Passaggio tra connessioni di rete diverse

* Riconnessione a causa di errori di connessione del servizio temporanei

I dettagli di implementazione possono variare in base alla lingua. Per altre informazioni, vedere la documentazione dell'API o l'SDK specifico:

* [SDK C/iOS](https://github.com/azure/azure-iot-sdk-c)

* [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/retrypolicy.md)

* [SDK per Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

* [Python SDK](https://github.com/Azure/azure-iot-sdk-python) (affidabilità non ancora implementata)

## <a name="designing-for-resiliency"></a>Progettazione per la resilienza

I dispositivi IoT si basano spesso su connessioni di rete discontinue e/o instabili, ad esempio GSM o satellitari. Quando i dispositivi interagiscono con servizi basati sul cloud, possono verificarsi errori dovuti a una disponibilità intermittente del servizio ed errori a livello di infrastruttura o errori temporanei. Un'applicazione eseguita in un dispositivo deve gestire i meccanismi per la connessione, la riconnessione e la logica di ripetizione dei tentativi per l'invio e la ricezione di messaggi. I requisiti della strategia di ripetizione dei tentativi, inoltre, dipendono principalmente dallo scenario IoT, dal contesto e dalle funzionalità del dispositivo.

Gli Azure IoT Hub SDK per dispositivi hanno lo scopo di semplificare le connessioni e le comunicazioni da cloud a dispositivo e da dispositivo a cloud. Offrono infatti la possibilità di connettersi all'hub IoT di Azure in modo affidabile e una gamma completa di opzioni per l'invio e la ricezione di messaggi. Gli sviluppatori possono anche modificare l'implementazione esistente per personalizzare la strategia di ripetizione dei tentativi per un determinato scenario.

Nelle sezioni seguenti sono illustrate le funzionalità SDK pertinenti che supportano la connettività e la messaggistica affidabile.

## <a name="connection-and-retry"></a>Connessione e ripetizione dei tentativi

Questa sezione offre una panoramica dei modelli di riconnessione e ripetizione dei tentativi disponibili per la gestione delle connessioni. Offre inoltre indicazioni di implementazione per l'uso dei vari criteri di ripetizione dei tentativi nell'applicazione per dispositivi ed elenca le API pertinenti per gli SDK per dispositivi.

### <a name="error-patterns"></a>Modelli di errore

Gli errori di connessione possono verificarsi a vari livelli:

* Errori di rete: socket disconnesso ed errori di risoluzione dei nomi

* Errori a livello di protocollo per trasporto HTTP, AMQP e MQTT: collegamenti rimossi o sessioni scadute

* Errori a livello di applicazione che derivano da errori locali: credenziali non valide o comportamento anomalo del servizio (ad esempio, superamento delle quote o delle limitazioni del servizio)

Gli SDK per dispositivi sono in grado di rilevare errori in tutti e tre i livelli. Gli errori del sistema operativo e dell'hardware non vengono rilevati e gestiti dagli SDK per dispositivi. La progettazione degli SDK si basa sulle [indicazioni relative alla gestione degli errori temporanei](/azure/architecture/best-practices/transient-faults#general-guidelines) del Centro architetture di Azure.

### <a name="retry-patterns"></a>Modelli di ripetizione dei tentativi

La procedura seguente descrive il processo di ripetizione dei tentativi quando vengono rilevati errori di connessione:

1. L'SDK rileva l'errore e l'errore associato nella rete, nel protocollo o nell'applicazione.

2. L'SDK usa il filtro degli errori per determinare il tipo di errore e determina se è necessaria una ripetizione dei tentativi.

3. Se l'SDK identifica un **errore irreversibile**, operazioni come connessione, invio e ricezione vengono arrestate. L'SDK invia una notifica all'utente. Un errore irreversibile può essere, ad esempio, un errore di autenticazione o un errore di endpoint non valido.

4. Se l'SDK identifica un **errore reversibile**, ripete i tentativi secondo i criteri di ripetizione specificati, fino alla scadenza del timeout definito.  Si noti che l'SDK usa il criterio di ripetizione **Interruzione temporanea esponenziale con instabilità** per impostazione predefinita.
5. Quando scade il timeout definito, l'SDK interrompe i tentativi di connessione o invio e invia una notifica all'utente.

6. L'SDK consente all'utente di associare un callback per ricevere le modifiche relative allo stato della connessione.

Gli SDK prevedono tre criteri di ripetizione dei tentativi:

* **Interruzione temporanea esponenziale con instabilità**: criteri di ripetizione dei tentativi predefiniti che tendono a essere aggressivi all'inizio e rallentano fino a raggiungere il ritardo massimo. La progettazione si basa sulle [indicazioni relative alla ripetizione dei tentativi](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific) del Centro architetture Azure. 

* **Ripetizione dei tentativi personalizzata**: per alcuni linguaggi SDK, è possibile progettare i criteri di ripetizione dei tentativi più adatti al proprio scenario e inserirli in RetryPolicy. La ripetizione dei tentativi personalizzata non è disponibile nell'SDK per il linguaggio C.

* **Nessun tentativo**: è possibile impostare i criteri di ripetizione su "Nessun tentativo", in modo da disabilitare la logica di ripetizione dei tentativi. L'SDK prova a connettersi e a inviare un messaggio una sola volta, supponendo che la connessione sia stata stabilita. Questi criteri vengono solitamente usati nei casi in cui sono presenti problemi relativi alla larghezza di banda o ai costi. Se si sceglie questa opzione, i messaggi non inviati vengono persi e non possono essere recuperati.

### <a name="retry-policy-apis"></a>API dei criteri di ripetizione dei tentativi

   | SDK | Metodo SetRetryPolicy | Implementazioni dei criteri | Indicazioni relative all'implementazione |
   |-----|----------------------|--|--|
   |  C/iOS  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **Predefinita**: [IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Personalizzata**: usare i [criteri di ripetizione dei tentativi](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies) disponibili<BR>**Nessun tentativo**: [IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)  | [Implementazione di C/iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | Java| [SetRetryPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.deviceclientconfig.setretrypolicy?view=azure-java-stable)        | **Predefinita**: [classe ExponentialBackoffWithJitter](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)<BR>**Personalizzata**: implementare l'[interfaccia RetryPolicy](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java)<BR>**Nessun tentativo**: [classe NoRetry](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)  | [Implementazione di Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |
   | .NET| [DeviceClient.SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy?view=azure-dotnet) | **Predefinita**: [classe ExponentialBackoff](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff?view=azure-dotnet)<BR>**Personalizzata**: implementare l'[interfaccia IRetryPolicy](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.iretrypolicy?view=azure-dotnet)<BR>**Nessun tentativo**: [classe NoRetry](/dotnet/api/microsoft.azure.devices.client.noretry?view=azure-dotnet) | [Implementazione di C#](https://github.com/Azure/azure-iot-sdk-csharp) | |
   | Nodo| [setRetryPolicy](/javascript/api/azure-iot-device/client?view=azure-iot-typescript-latest) | **Predefinita**: [classe ExponentialBackoffWithJitter](/javascript/api/azure-iot-common/exponentialbackoffwithjitter?view=azure-iot-typescript-latest)<BR>**Personalizzata**: implementare l'[interfaccia RetryPolicy](/javascript/api/azure-iot-common/retrypolicy?view=azure-iot-typescript-latest)<BR>**Nessun tentativo**: [classe NoRetry](/javascript/api/azure-iot-common/noretry?view=azure-iot-typescript-latest) | [Implementazione di Node](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |
   | Python| Presto disponibile | Presto disponibile | Presto disponibile

Gli esempi di codice seguenti descrivono questo flusso:

#### <a name="net-implementation-guidance"></a>Indicazioni relative all'implementazione di .NET

Gli esempi di codice seguenti illustrano come definire e impostare i criteri di ripetizione dei tentativi predefiniti:

   ```csharp
   // define/set default retry policy
   IRetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

Per evitare un utilizzo elevato della CPU, i tentativi di ripetizione sono limitati se il codice dà immediatamente esito negativo, ad esempio quando non è presente una rete o una route verso la destinazione. Il tempo minimo per eseguire il tentativo successivo è di 1 secondo.

Se il servizio risponde con un errore di limitazione delle richieste, i criteri di ripetizione dei tentativi sono diversi e non possono essere modificati tramite API pubblica:

   ```csharp
   // throttled retry policy
   IRetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), 
     TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5)); SetRetryPolicy(retryPolicy);
   ```

Il meccanismo di ripetizione dei tentativi si interrompe dopo `DefaultOperationTimeoutInMilliseconds`, che è attualmente impostato su 4 minuti.

#### <a name="other-languages-implementation-guidance"></a>Indicazioni relative all'implementazione di altri linguaggi

Per esempi di codice in altri linguaggi, vedere i documenti relativi all'implementazione riportati di seguito. Il repository contiene esempi che illustrano l'uso dell'API dei criteri di ripetizione dei tentativi.

* [SDK C/iOS](https://github.com/azure/azure-iot-sdk-c)

* [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/retrypolicy.md)

* [SDK per Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

* [Python SDK](https://github.com/Azure/azure-iot-sdk-python)

## <a name="next-steps"></a>Passaggi successivi

* [Usare gli SDK di servizi e dispositivi](./iot-hub-devguide-sdks.md)

* [Usare IoT SDK per dispositivi per C](./iot-hub-device-sdk-c-intro.md)

* [Sviluppare per dispositivi vincolati](./iot-hub-devguide-develop-for-constrained-devices.md)

* [Sviluppare per dispositivi mobili](./iot-hub-how-to-develop-for-mobile-devices.md)

* [Risolvere i problemi di disconnessione dei dispositivi](iot-hub-troubleshoot-connectivity.md)
