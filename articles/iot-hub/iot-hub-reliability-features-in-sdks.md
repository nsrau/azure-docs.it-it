---
title: Come gestire le funzionalità di connettività e messaggistica affidabile con gli SDK per dispositivi dell'hub IoT di Azure
description: Informazioni su come migliorare le funzionalità di connettività e messaggistica dei dispositivi quando si usano gli SDK per dispositivi dell'hub IoT di Azure
services: iot-hub
keywords: ''
author: yzhong94
ms.author: yizhon
ms.date: 07/07/2018
ms.topic: article
ms.service: iot-hub
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: d321e82560f7676f77ed9161c1bce0304b7d5914
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397108"
---
# <a name="how-to-manage-connectivity-and-reliable-messaging-using-azure-iot-hub-device-sdks"></a>Come gestire le funzionalità di connettività e messaggistica affidabile con gli SDK per dispositivi dell'hub IoT di Azure

Questa guida fornisce indicazioni approfondite per progettare applicazioni resilienti per dispositivi sfruttando i vantaggi offerti dalle funzionalità di connettività e messaggistica affidabile in Azure IoT SDK per dispositivi. Questo articolo consente di risolvere dubbi e gestire gli scenari seguenti:

- Gestione di una connessione di rete interrotta
- Gestione del passaggio tra connessioni di rete diverse
- Gestione della riconnessione a causa di errori di connessione del servizio temporanei

I dettagli di implementazione possono variare in base al linguaggio usato. Per altre informazioni, vedere la documentazione dell'API o l'SDK specifico.

- [C/Python/iOS SDK](https://github.com/azure/azure-iot-sdk-c)
- [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
- [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)
- [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)


## <a name="designing-for-resiliency"></a>Progettazione per la resilienza

I dispositivi IoT si basano spesso su connessioni di rete discontinue e/o instabili, ad esempio GSM o satellitari. Inoltre, quando si interagisce con servizi basati sul cloud, possono verificarsi errori dovuti a condizioni temporanee, ad esempio disponibilità intermittente del servizio ed errori a livello di infrastruttura (comunemente definiti come errori temporanei). Un'applicazione in esecuzione su un dispositivo deve gestire i meccanismi di connessione e riconnessione, nonché la logica di ripetizione dei tentativi per l'invio e la ricezione dei messaggi. Inoltre, i requisiti della strategia di ripetizione dei tentativi dipendono principalmente dallo scenario IoT di cui fa parte il dispositivo, oltre che dal contesto e dalle funzionalità del dispositivo.

Gli SDK per dispositivi dell'hub IoT di Azure hanno lo scopo di semplificare le connessioni e le comunicazioni da cloud a dispositivo e da dispositivo a cloud, offrendo una modalità affidabile e completa di connessione e invio/ricezione di messaggi da e verso l'hub IoT di Azure. Gli sviluppatori possono anche modificare l'implementazione esistente per sviluppare la corretta strategia di ripetizione dei tentativi per un determinato scenario.

Nelle sezioni seguenti sono illustrate le funzionalità SDK pertinenti che supportano la connettività e la messaggistica affidabile.

## <a name="connection-and-retry"></a>Connessione e ripetizione dei tentativi

Questa sezione fornisce una panoramica dei modelli di riconnessione e ripetizione dei tentativi disponibili quando si gestiscono le connessioni, indicazioni di implementazione per l'uso dei vari criteri di ripetizione dei tentativi nell'applicazione per dispositivi e API pertinenti per gli SDK per dispositivi.

### <a name="error-patterns"></a>Modelli di errore
Gli errori di connessione possono verificarsi a vari livelli:

-  Errori di rete, ad esempio un socket disconnesso ed errori di risoluzione dei nomi
- Errori a livello di protocollo per trasporto HTTP, AMQP e MQTT, ad esempio collegamenti rimossi o sessioni scadute
- Errori a livello di applicazione che derivano da errori locali, ad esempio credenziali non valide o comportamento anomalo del servizio come il superamento delle quote o delle limitazioni del servizio

Gli SDK per dispositivi sono in grado di rilevare errori in tutti e tre i livelli.  Gli errori del sistema operativo e dell'hardware non vengono rilevati e gestiti dagli SDK per dispositivi.  La progettazione si basa sulle [indicazioni relative alla gestione degli errori temporanei](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines) del Centro architetture Azure.

### <a name="retry-patterns"></a>Modelli di ripetizione dei tentativi

Il processo generale di ripetizione dei tentativi quando vengono rilevati errori di connessione è il seguente: 
1. L'SDK rileva l'errore e l'errore associato nella rete, nel protocollo o nell'applicazione.
2. In base al tipo di errore, l'SDK usa il filtro degli errori per determinare se deve essere eseguita la ripetizione dei tentativi.  Se l'SDK identifica un **errore irreversibile**, le operazioni (connessione e invio/ricezione) vengono arrestate e l'SDK invia una notifica all'utente. Un errore irreversibile è un errore che l'SDK può identificare e di cui determina l'impossibilità di risoluzione, ad esempio un errore di autenticazione o di endpoint non valido.
3. Se viene identificato un **errore irreversibile**, l'SDK inizia la ripetizione dei tentativi usando i criteri specificati fino alla scadenza del timeout definito.
4. Quando scade il timeout definito, l'SDK interrompe i tentativi di connessione o invio e quindi invia una notifica all'utente.
5.  L'SDK consente all'utente di associare un callback per ricevere le modifiche relative allo stato della connessione. 

Sono disponibili tre criteri di ripetizione dei tentativi:
- **Interruzione temporanea esponenziale con instabilità**: il criterio applicato per impostazione predefinita.  Tende a essere aggressivo all'inizio, rallenta e quindi causa un ritardo massimo che non viene superato.  La progettazione si basa sulle [indicazioni relative alla ripetizione dei tentativi](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific) del Centro architetture Azure.
- **Ripetizione dei tentativi personalizzata**: è possibile implementare criteri di ripetizione dei tentativi personalizzati e inserirli in RetryPolicy in base al linguaggio usato. È possibile progettare un criterio di ripetizione dei tentativi adatto al proprio scenario.  Questa opzione non è disponibile nell'SDK per il linguaggio C.
- **Nessun tentativo**: è disponibile un'opzione per impostare "Nessun tentativo" come criterio di ripetizione, in modo da disabilitare la logica di ripetizione dei tentativi.  L'SDK prova a connettersi e a inviare un messaggio una sola volta, supponendo che la connessione sia stata stabilita. Questo criterio è in genere usato nei casi in cui sono presenti problemi relativi alla larghezza di banda o ai costi.   Se si sceglie questa opzione, i messaggi non inviati vengono persi e non possono essere recuperati. 

### <a name="retry-policy-apis"></a>API dei criteri di ripetizione dei tentativi

   | SDK | Metodo SetRetryPolicy | Implementazioni dei criteri | Indicazioni relative all'implementazione |
   |-----|----------------------|--|--|
   |  C/Python/iOS  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **Predefinita**: [IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Personalizzata**: usare i [criteri di ripetizione dei tentativi](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies) disponibili<BR>**Nessun tentativo**: [IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)  | [Implementazione di C/Python/iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | Java| [SetRetryPolicy](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.sdk.iot.device._device_client_config.setretrypolicy?view=azure-java-stable)        | **Predefinita**: [classe ExponentialBackoffWithJitter](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)<BR>**Personalizzata**: implementare l'[interfaccia RetryPolicy](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java)<BR>**Nessun tentativo**: [classe NoRetry](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)  | [Implementazione di Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |[.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
   | .NET| [DeviceClient.SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_SetRetryPolicy_Microsoft_Azure_Devices_Client_IRetryPolicy) | **Predefinita**: [classe ExponentialBackoff](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff?view=azure-dotnet)<BR>**Personalizzata**: implementare l'[interfaccia IRetryPolicy](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.iretrypolicy?view=azure-dotnet)<BR>**Nessun tentativo**: [classe NoRetry](/dotnet/api/microsoft.azure.devices.client.noretry?view=azure-dotnet) | [Implementazione di C#]() |
   | Nodo| [setRetryPolicy](/javascript/api/azure-iot-device/client?view=azure-iot-typescript-latest#azure_iot_device_Client_setRetryPolicy) | **Predefinita**: [classe ExponentialBackoffWithJitter](/javascript/api/azure-iot-common/exponentialbackoffwithjitter?view=azure-iot-typescript-latest)<BR>**Personalizzata**: implementare l'[interfaccia RetryPolicy](/javascript/api/azure-iot-common/retrypolicy?view=azure-iot-typescript-latest)<BR>**Nessun tentativo**: [classe NoRetry](/javascript/api/azure-iot-common/noretry?view=azure-iot-typescript-latest) | [Implementazione di Node](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |
   

Di seguito sono illustrati esempi di codice che descrivono questo flusso. 

#### <a name="net-implementation-guidance"></a>Indicazioni relative all'implementazione di .NET

L'esempio di codice seguente illustra come definire e impostare i criteri di ripetizione dei tentativi predefiniti:

   ```csharp
   # define/set default retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

Per evitare un utilizzo elevato della CPU, i tentativi di ripetizione sono limitati se il codice dà immediatamente esito negativo (ad esempio, quando non è presente una rete o una route verso la destinazione), in modo che il tempo minimo per eseguire il tentativo successivo sia di 1 secondo. 

Se il servizio risponde con un errore di limitazione delle richieste, i criteri di ripetizione dei tentativi sono diversi e non possono essere modificati tramite API pubblica:

   ```csharp
   # throttled retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5));
   SetRetryPolicy(retryPolicy);
   ```

Il meccanismo di ripetizione dei tentativi si interromperà dopo `DefaultOperationTimeoutInMilliseconds`, che è attualmente impostato su 4 minuti.

#### <a name="other-languages-implementation-guidance"></a>Indicazioni relative all'implementazione di altri linguaggi
Per altri linguaggi, vedere la documentazione relativa all'implementazione riportata di seguito.  Esempi che illustrano l'uso delle API dei criteri di ripetizione dei tentativi fornite nel repository.
- [C/Python/iOS SDK](https://github.com/azure/azure-iot-sdk-c)
- [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)
- [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)
- [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

## <a name="next-steps"></a>Passaggi successivi
- [Usare gli SDK di servizi e dispositivi](.\iot-hub-devguide-sdks.md)
- [Usare IoT SDK per dispositivi per C](.\iot-hub-device-sdk-c-intro.md)
- [Sviluppare per dispositivi vincolati](.\iot-hub-devguide-develop-for-constrained-devices.md)
- [Sviluppare per dispositivi mobili](.\iot-hub-how-to-develop-for-mobile-devices.md)
- [Risolvere i problemi di disconnessione dei dispositivi](iot-hub-troubleshoot-connectivity.md)
