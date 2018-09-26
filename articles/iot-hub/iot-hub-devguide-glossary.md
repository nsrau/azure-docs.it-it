---
title: Glossario dei termini dell'hub IoT di Azure | Documentazione Microsoft
description: Guida per sviluppatori - Glossario dei termini comuni relativi all'hub IoT di Azure.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 575326ee3bd2cb64b2cb65168f23f7fe97dad029
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46954399"
---
# <a name="glossary-of-iot-hub-terms"></a>Glossario dei termini relativi all'hub IoT
Questo articolo elenca alcuni dei termini comuni usati negli articoli su Hub IoT.

## <a name="advanced-message-queueing-protocol"></a>Advanced Message Queueing Protocol
[Advanced Message Queueing Protocol (AMQP)](https://www.amqp.org/) è uno dei protocolli di messaggistica supportati da [Hub IoT](#iot-hub) per la comunicazione con i dispositivi. Per altre informazioni sui protocolli di messaggistica supportati dall'hub IoT, vedere [Inviare e ricevere messaggi con l'hub IoT](iot-hub-devguide-messaging.md).

## <a name="automatic-device-management"></a>Gestione automatica dei dispositivi
La gestione automatica dei dispositivi nell'hub IoT di Azure consente di automatizzare molte attività ripetitive e complesse di gestione di un numero elevato di dispositivi nel loro intero ciclo di vita. La gestione automatica dei dispositivi consente di gestire un insieme di dispositivi in base alle proprietà, definire la configurazione desiderata e permette all'hub IoT di aggiornare i dispositivi quando rientrano nell'ambito.  Si compone delle [configurazioni automatiche del dispositivo](iot-hub-auto-device-config.md) e delle [distribuzioni automatiche di IoT Edge](../iot-edge/how-to-deploy-monitor.md).

## <a name="automatic-device-configuration"></a>Configurazione automatica dei dispositivi
Il back-end della soluzione può usare le [configurazioni automatiche dei dispositivi](iot-hub-auto-device-config.md) per assegnare le proprietà desiderate a un set di [dispositivi gemelli](#device-twin) e segnalare lo stato usando metriche di sistema e metriche personalizzate. 

## <a name="azure-classic-cli"></a>Interfaccia della riga di comando classica di Azure
L'[interfaccia della riga di comando classica di Azure](../cli-install-nodejs.md) è uno strumento di comando multipiattaforma open source basato sulla shell per creare e gestire le risorse in Microsoft Azure. Questa versione dell'interfaccia della riga di comando deve essere usata per le sole distribuzioni classiche.

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
L'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-az-cli2) è uno strumento di comando multipiattaforma open source basato sulla shell per creare e gestire le risorse in Microsoft Azure.


## <a name="azure-iot-device-sdks"></a>SDK dispositivo IoT Azure
Sono disponibili _SDK per dispositivi_ per più linguaggi che consentono di creare [app per dispositivo](#device-app) che interagiscono con un hub IoT. Le esercitazioni su Hub IoT illustrano come usare questi SDK per dispositivi. Il codice sorgente e altre informazioni sugli SDK per dispositivi sono disponibili in questo [repository](https://github.com/Azure/azure-iot-sdks) GitHub.

## <a name="azure-iot-service-sdks"></a>Azure IoT SDK per servizi
Sono disponibili _SDK per servizi_ per più linguaggi che consentono di creare [app back-end](#back-end-app) che interagiscono con un hub IoT. Le esercitazioni su Hub IoT illustrano come usare questi SDK per servizi. Il codice sorgente e altre informazioni sugli SDK per servizi sono disponibili in questo [repository](https://github.com/Azure/azure-iot-sdks) GitHub.

## <a name="azure-iot-toolkit"></a>Azure IoT Toolkit
[Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) è un'utile estensione open source multipiattaforma di Visual Studio Code che consente di gestire i dispositivi e l'hub IoT di Azure in Visual Studio Code. Con Azure IoT Toolkit gli sviluppatori IoT possono sviluppare con facilità un progetto IoT in Visual Studio Code.

## <a name="azure-portal"></a>Portale di Azure
Il [portale di Microsoft Azure](https://portal.azure.com) è una posizione centrale in cui è possibile effettuare il provisioning e gestire le risorse di Azure. Organizza il contenuto usando i _pannelli_.

## <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) è una raccolta di cmdlet che è possibile usare per gestire Azure con Windows PowerShell. È possibile utilizzare i cmdlet per creare, testare, distribuire e gestire soluzioni e servizi offerti tramite la piattaforma Azure.

## <a name="azure-resource-manager"></a>Azure Resource Manager
[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) consente di usare le risorse incluse nella soluzione come un gruppo. È possibile distribuire, aggiornare o eliminare le risorse della soluzione con un'unica operazione coordinata.

## <a name="azure-service-bus"></a>Bus di servizio di Azure
Il [bus di servizio](../service-bus/index.md) offre comunicazione abilitata per il cloud con messaggistica aziendale, nonché comunicazione inoltrata per la connessione delle soluzioni locali con il cloud. Alcune esercitazioni sull'hub IoT usano le [code](../service-bus-messaging/service-bus-messaging-overview.md) del bus di servizio.

## <a name="azure-storage"></a>Archiviazione di Azure
[Archiviazione di Azure](../storage/common/storage-introduction.md) è una soluzione di archiviazione cloud. Include il servizio Archiviazione BLOB che è possibile usare per archiviare dati oggetto non strutturati. Alcune esercitazioni su Hub IoT usano l'archivio BLOB.

## <a name="back-end-app"></a>App back-end
Nell'ambito di [Hub IoT](#iot-hub), un'app back-end è un'app che connette a uno degli endpoint per il servizio in un hub IoT. Un'app back-end, ad esempio, può recuperare messaggi [da dispositivo a cloud](#device-to-cloud) o gestire il [registro delle identità](#identity-registry). Un'app back-end in genere viene eseguita nel cloud, ma in molte esercitazioni le app back-end sono app console in esecuzione nel computer di sviluppo locale.

## <a name="built-in-endpoints"></a>Endpoint predefiniti
Ogni hub IoT include un [endpoint](iot-hub-devguide-endpoints.md) predefinito compatibile con l'Hub eventi. Per leggere i messaggi da dispositivo a cloud da questo endpoint, è possibile usare qualsiasi meccanismo che funzioni con l'Hub eventi.

## <a name="cloud-gateway"></a>Gateway cloud
Un gateway cloud abilita la connettività per i dispositivi che non possono connettersi direttamente a [Hub IoT](#iot-hub). Un gateway cloud, a differenza di un [gateway sul campo](#field-gateway) che viene eseguito in locale nei dispositivi, è ospitato nel cloud. Un caso d'uso tipico per il gateway cloud è l'implementazione della conversione dei protocolli per i dispositivi.

## <a name="cloud-to-device"></a>Da cloud a dispositivo
Si riferisce ai messaggi inviati da un hub IoT a un dispositivo connesso. Spesso, questi messaggi sono comandi che indicano al dispositivo di eseguire un'azione. Per altre informazioni, vedere [Inviare e ricevere messaggi con l'hub IoT](iot-hub-devguide-messaging.md).

## <a name="configuration"></a>Configurazione
Nell'ambito della [configurazione automatica dei dispositivi](iot-hub-auto-device-config.md), una configurazione nell'hub IoT definisce la configurazione desiderata per un set di dispositivi gemelli e fornisce un set di metriche per segnalare lo stato e l'avanzamento.

## <a name="connection-string"></a>Stringa di connessione
Le stringhe di connessione vengono usate nel codice dell'app per incapsulare le informazioni necessarie per connettersi a un endpoint. Una stringa di connessione contiene in genere l'indirizzo dell'endpoint e informazioni di sicurezza, ma il formato della stringa può variare nei diversi servizi. Esistono due tipi di stringa di connessione associato al servizio hub IoT:
- *Stringhe di connessione dispositivo* che consentono ai dispositivi di connettersi agli endpoint che usano dispositivi in un hub IoT.
- *Stringhe di connessione hub IoT* che consentono alle app back-end di connettersi agli endpoint che usano i servizi in un hub IoT.

## <a name="custom-endpoints"></a>Endpoint personalizzati
È possibile creare [endpoint](iot-hub-devguide-endpoints.md) personalizzati su un hub IoT per recapitare i messaggi inviati da una [regola di routing](#routing-rules). Gli endpoint personalizzati connettono direttamente a un Hub eventi, a una coda del Bus di servizio o a un argomento del Bus di servizio.

## <a name="custom-gateway"></a>Gateway personalizzato
Un gateway abilita la connettività per i dispositivi che non possono connettersi direttamente a [Hub IoT](#iot-hub). È possibile usare [Azure IoT Edge](#azure-iot-edge) per compilare gateway personalizzati che implementano la logica personalizzata per gestire i messaggi, le conversioni dei protocolli personalizzati e altre attività di elaborazione su edge.

## <a name="data-point-message"></a>Messaggio di punto dati
Un messaggio di punto dati è un messaggio da [dispositivo a cloud](#device-to-cloud) contenente dati di [telemetria](#telemetry) come temperatura o velocità del vento.

## <a name="desired-configuration"></a>Configurazione desiderata
Nell'ambito di un [dispositivo gemello](iot-hub-devguide-device-twins.md), il termine configurazione desiderata indica il set completo di proprietà e metadati nel dispositivo gemello che deve essere sincronizzato con il dispositivo.

## <a name="desired-properties"></a>Proprietà desiderate
Nell'ambito di un [dispositivo gemello](iot-hub-devguide-device-twins.md), le proprietà desiderate sono un sottoparagrafo del dispositivo gemello usato con le [proprietà segnalate](#reported-properties) per sincronizzare la condizione o la configurazione del dispositivo. Le proprietà desiderate possono essere impostate solo da un'[app back-end](#back-end-app) e possono essere lette dall'[app per dispositivi](#device-app).

## <a name="device-to-cloud"></a>Da dispositivo a cloud
Si riferisce ai messaggi inviati da un dispositivo connesso a [Hub IoT](#iot-hub). Questi messaggi potrebbero essere di [punto dati](#data-point-message) o [interattivi](#interactive-message). Per altre informazioni, vedere [Inviare e ricevere messaggi con l'hub IoT](iot-hub-devguide-messaging.md).

## <a name="device"></a>Dispositivo
Nel contesto di IoT, un dispositivo è in genere un dispositivo di elaborazione autonomo e su scala ridotta, che potrebbe raccogliere dati o controllare altri dispositivi. Ad esempio, un dispositivo potrebbe essere un sistema di monitoraggio ambientale o un controller per i sistemi di irrigazione e ventilazione in una serra. Il [catalogo dei dispositivi](https://catalog.azureiotsuite.com/) contiene un elenco di dispositivi hardware certificati da usare con [hub IoT](#iot-hub).

## <a name="device-app"></a>App per dispositivi
Un'app per dispositivi viene eseguita nel [dispositivo](#device) e gestisce la comunicazione con l'[hub IoT](#iot-hub). In genere si usa uno degli [Azure IoT SDK per dispositivi](#azure-iot-device-sdks) quando si implementa un'app per dispositivi. In diverse esercitazioni su IoT si usa un [dispositivo simulato](#simulated-device) per praticità.

## <a name="device-condition"></a>Condizione del dispositivo
Si riferisce alle informazioni sullo stato del dispositivo, ad esempio il metodo di connettività in uso, come segnalato da un'[app per dispositivi](#device-app). Le [app per dispositivi](#device-app) possono anche segnalare le loro stesse funzionalità. È possibile cercare le informazioni sulla condizione e sulle funzionalità usando i dispositivi gemelli.

## <a name="device-data"></a>Dati del dispositivo
Per dati del dispositivo si intendono i dati di ogni dispositivo archiviati nel [registro delle identità](#identity-registry) di Hub IoT. È possibile importare ed esportare questi dati.

## <a name="device-explorer"></a>Esplora dispositivi
[Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) è uno strumento eseguito in Windows che consente di gestire i dispositivi nel [registro delle identità](#identity-registry), nonché di inviare messaggi dai dispositivi e riceverli.

## <a name="device-identity"></a>Identità del dispositivo
L'identità del dispositivo è l'identificatore univoco assegnato a ogni dispositivo registrato nel [registro delle identità](#identity-registry).

## <a name="module-identity"></a>Identità del modulo
L'identità del modulo è l'identificatore univoco assegnato a ogni modulo appartenente a un dispositivo. L'identità del modulo viene anche inserita nel [registro delle identità](#identity-registry).

## <a name="device-management"></a>Gestione dei dispositivi
La gestione dei dispositivi comprende il ciclo di vita completo associato alla gestione dei dispositivi nella soluzione IoT, inclusi pianificazione, provisioning, configurazione, monitoraggio e ritiro.

## <a name="device-management-patterns"></a>Modelli di gestione dei dispositivi
L'[hub IoT](#iot-hub) abilita modelli di gestione dei dispositivi comuni, inclusi riavvio, esecuzione di ripristini delle impostazioni predefinite ed esecuzione di aggiornamenti del firmware nei dispositivi.

## <a name="device-rest-api"></a>API REST dei dispositivi
È possibile usare l'[API REST dei dispositivi](https://docs.microsoft.com/rest/api/iothub/device) da un dispositivo per l'invio da dispositivo a cloud di messaggi a un hub IoT e per la ricezione da [cloud a dispositivo](#cloud-to-device) di messaggi da un hub IoT. In genere è consigliabile usare uno degli [SDK per dispositivi](#azure-iot-device-sdks) di livello superiore, come illustrato nelle esercitazioni su Hub IoT.

## <a name="device-provisioning"></a>Provisioning di dispositivi
Il [provisioning di dispositivi](#device-data) è il processo di aggiunta dei dati iniziali dei dispositivi agli archivi nella soluzione. Per abilitare un nuovo dispositivo per la connessione all'hub è necessario aggiungere un ID dispositivo e le relative chiavi al [registro delle identità](#identity-registry) di Hub IoT. Come parte del processo di provisioning, può essere necessario inizializzare i dati specifici del dispositivo in altri archivi di soluzioni.

## <a name="device-twin"></a>Dispositivo gemello
Un [dispositivo gemello](iot-hub-devguide-device-twins.md) è un documento JSON nel quali vengono archiviate informazioni sullo stato dei dispositivi, ad esempio metadati, configurazioni e condizioni. [Hub IoT](#iot-hub) rende permanente un dispositivo gemello per ogni dispositivo di cui viene effettuato il provisioning nell'hub IoT. I dispositivi gemelli consentono di sincronizzare le configurazioni e le [condizioni del dispositivo](#device-condition) tra il dispositivo e il back-end della soluzione. È possibile effettuare una query dei dispositivi gemelli per individuare dispositivi specifici ed effettuare una query dello stato delle operazioni a esecuzione prolungata.

## <a name="module-twin"></a>Modulo gemello
Simile a un dispositivo gemello, un modulo gemello è un documento JSON nel quale vengono archiviate informazioni sullo stato dei moduli, ad esempio metadati, configurazioni e condizioni. L'hub IoT rende persistente un modulo gemello per ogni identità di modulo di cui si esegue il provisioning in un'identità di dispositivo nell'hub IoT. I moduli gemelli consentono di sincronizzare le configurazioni e le condizioni del modulo tra il modulo stesso e il back-end della soluzione. È possibile effettuare una query sui moduli gemelli per individuare moduli specifici ed effettuare query sullo stato delle operazioni a esecuzione prolungata.

## <a name="twin-queries"></a>Query dei dispositivi gemelli
Le [query su dispositivi e moduli gemelli](iot-hub-devguide-query-language.md) usano il linguaggio di query simile a SQL dell'hub IoT per recuperare informazioni da dispositivi o moduli gemelli. È possibile usare lo stesso linguaggio di query dell'hub IoT per recuperare informazioni sui [](#job) in esecuzione nell'hub IoT.

## <a name="twin-synchronization"></a>Sincronizzazione dei dispositivi o dei moduli gemelli
La sincronizzazione usa le [proprietà desiderate](#desired-properties) dei dispositivi gemelli o dei moduli gemelli per configurare i dispositivi o i moduli e recuperare le [proprietà segnalate](#reported-properties) da tali elementi per l'archiviazione nel dispositivo o modulo gemello.

## <a name="direct-method"></a>Metodo diretto
Un [metodo diretto](iot-hub-devguide-direct-methods.md) è un modo per attivare un metodo da eseguire su un dispositivo richiamando un'API nell'hub IoT.

## <a name="endpoint"></a>Endpoint
Un hub IoT espone più [endpoint](iot-hub-devguide-endpoints.md) che consentono alle app di connettersi all'hub IoT. Esistono endpoint per il dispositivo che consentono ai dispositivi di eseguire operazioni come l'invio di messaggi [da dispositivo a cloud](#device-to-cloud) e la ricezione di messaggi [da cloud a dispositivo](#cloud-to-device). Esistono endpoint di gestione del servizio che consentono alle [app back-end](#back-end-app) di eseguire operazioni come la gestione delle [identità del dispositivo](#device-identity) e la gestione del dispositivo gemello. Esistono [endpoint incorporati](#built-in-endpoints) del servizio per leggere i messaggi da dispositivo a cloud. È possibile creare [endpoint personalizzati](#custom-endpoints) per ricevere i messaggi da dispositivo a cloud inviati da una [regola di routing](#routing-rules).

## <a name="event-hubs-service"></a>Servizio Hub eventi
[Hub eventi](../event-hubs/event-hubs-what-is-event-hubs.md) è un servizio altamente scalabile di ingresso dei dati tale che può inserire milioni di eventi al secondo. Il servizio consente di elaborare e analizzare le ingenti quantità di dati prodotte da applicazioni e dispositivi connessi. Per un confronto con il servizio Hub IoT, vedere [Confronto tra Hub IoT di Azure e Hub eventi di Azure](iot-hub-compare-event-hubs.md).

## <a name="event-hub-compatible-endpoint"></a>Endpoint compatibile con Hub eventi
Per leggere i messaggi [da dispositivo a cloud](#device-to-cloud) inviati all'hub IoT, è possibile connettersi a un endpoint sull'hub e usare qualsiasi metodo compatibile con Hub eventi. I metodi compatibili con Hub eventi includono l'uso di [SDK di Hub eventi](../event-hubs/event-hubs-programming-guide.md) e [Analisi di flusso di Azure](../stream-analytics/stream-analytics-introduction.md).

## <a name="field-gateway"></a>Gateway sul campo
Un gateway sul campo abilita la connettività per i dispositivi che non possono connettersi direttamente a [Hub IoT](#iot-hub) e vengono generalmente distribuiti in locale con i dispositivi. Per altre informazioni, vedere [Che cos'è l'hub IoT di Azure?](about-iot-hub.md)

## <a name="free-account"></a>Account gratuito
È possibile creare un [account Azure gratuito](https://azure.microsoft.com/pricing/free-trial/) per completare le esercitazioni su Hub IoT e provare a usare il servizio Hub IoT (e altri servizi di Azure).

## <a name="gateway"></a>Gateway
Un gateway abilita la connettività per i dispositivi che non possono connettersi direttamente a [Hub IoT](#iot-hub). Vedere anche [Gateway sul campo](#field-gateway), [Gateway cloud](#cloud-gateway) e [Gateway personalizzato](#custom-gateway).

## <a name="identity-registry"></a>Registro delle identità
Il [registro delle identità](iot-hub-devguide-identity-registry.md) è il componente integrato di un hub IoT che archivia le informazioni sui singoli dispositivi a cui è consentito connettersi a un hub IoT.

## <a name="interactive-message"></a>Messaggio interattivo
Un messaggio interattivo è un messaggio [da cloud a dispositivo](#cloud-to-device) che attiva un'azione immediata nel back-end della soluzione. Ad esempio, un dispositivo potrebbe inviare un allarme relativo a un errore che deve essere registrato automaticamente in un sistema CRM.

[!INCLUDE [azure-iot-hub-edge-glossary-includes](../../includes/azure-iot-hub-edge-glossary-includes.md)]

## <a name="iot-hub"></a>Hub IoT
Hub IoT è un servizio completamente gestito di Azure che consente comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi e un back-end della soluzione. Per altre informazioni, vedere [Che cos'è l'hub IoT di Azure?](about-iot-hub.md) Usando la [sottoscrizione di Azure](#subscription), è possibile creare hub IoT per gestire i carichi di lavoro di messaggistica IoT.

## <a name="iot-hub-metrics"></a>Metriche di Hub IoT
Le [metriche di Hub IoT](iot-hub-metrics.md) forniscono dati sullo stato degli hub IoT nella [sottoscrizione di Azure](#subscription). Le metriche dell'hub IoT consentono di valutare l'integrità complessiva del servizio e dei dispositivi connessi. Le metriche consentono di visualizzare le attività in corso nell'hub IoT e di esaminare la causa radice dei problemi senza la necessità di contattare il supporto di Azure.

## <a name="iot-hub-query-language"></a>Linguaggio di query di Hub IoT
Il [linguaggio di query di Hub IoT](iot-hub-devguide-query-language.md) è un linguaggio simile a SQL che consente di effettuare una query dei [](#job) e dei dispositivi gemelli.

## <a name="iot-hub-resource-rest-api"></a>API REST delle risorse dell'hub IoT
È possibile usare l'[API REST delle risorse dell'hub IoT](https://docs.microsoft.com/rest/api/iothub/iothubresource) per gestire gli hub IoT nella [sottoscrizione di Azure](#subscription) che esegue operazioni come creazione, aggiornamento ed eliminazione di hub.

## <a name="iot-solution-accelerators"></a>Acceleratori di soluzioni IoT
Gli acceleratori di soluzioni Azure IoT raggruppano più servizi di Azure in soluzioni. Tali soluzioni consentono di iniziare a usare rapidamente implementazioni end-to-end di scenari IoT comuni. Per altre informazioni, vedere [Panoramica degli acceleratori di soluzioni di Azure IoT](../iot-accelerators/about-iot-accelerators.md).

## <a name="the-iot-extension-for-azure-cli"></a>L'estensione IoT per l'interfaccia della riga di comando di Azure 
[L'estensione IoT per l'interfaccia della riga di comando di Azure](https://github.com/Azure/azure-iot-cli-extension) è uno strumento multipiattaforma da riga di comando. Lo strumento consente di gestire i dispositivi nel [registro delle identità](#identity-registry), inviare e ricevere messaggi e file dai dispositivi e monitorare le operazioni dell'hub IoT.

## <a name="job"></a>Processo
Il back-end della soluzione può usare i [processi](iot-hub-devguide-jobs.md) per pianificare e tenere traccia delle attività in un set di dispositivi registrati con l'hub IoT. Le attività includono l'aggiornamento delle [proprietà desiderate](#desired-properties) di un dispositivo gemello, l'aggiornamento dei [tag](#tags) di un dispositivo gemello e la chiamata di [metodi diretti](#direct-method). [Hub IoT](#iot-hub) usa anche per eseguire [importazioni ed esportazioni](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) nel [registro delle identità](#identity-registry).

## <a name="mqtt"></a>MQTT
[MQTT](http://mqtt.org/) è uno dei protocolli di messaggistica supportati da [Hub IoT](#iot-hub) per la comunicazione con i dispositivi. Per altre informazioni sui protocolli di messaggistica supportati dall'hub IoT, vedere [Inviare e ricevere messaggi con l'hub IoT](iot-hub-devguide-messaging.md).

## <a name="operations-monitoring"></a>Monitoraggio delle operazioni
Il [monitoraggio delle operazioni](iot-hub-operations-monitoring.md) di Hub IoT consente di monitorare lo stato delle operazioni nel proprio hub IoT in tempo reale. L'[hub IoT](#iot-hub) tiene traccia degli eventi nelle diverse categorie di operazioni. È possibile scegliere di impostare l'invio di eventi da una o più categorie a un endpoint di Hub IoT per l'elaborazione. È possibile monitorare i dati per individuare gli errori o configurare un'elaborazione più complessa in base ai modelli di dati.

## <a name="physical-device"></a>Dispositivo fisico
Un dispositivo fisico è un vero e proprio dispositivo, ad esempio un Raspberry Pi, che si connette a un hub IoT. Per motivi di praticità, diverse esercitazioni su Hub IoT usano [dispositivi simulati](#simulated-device) per consentire di eseguire esempi nel computer locale.

## <a name="primary-and-secondary-keys"></a>Chiavi primarie e secondarie
Quando ci si connette a un endpoint per il dispositivo o per il servizio in un hub IoT, la [stringa di connessione](#connection-string) include la chiave che concede l'accesso. Quando si aggiunge un dispositivo al [registro delle identità](#identity-registry) o si aggiungono [criteri di accesso condiviso](#shared-access-policy) all'hub, il servizio genera una chiave primaria e una secondaria. Avere due chiavi consente di eseguire il rollover da una chiave all'altra quando si aggiorna una chiave, senza perdere l'accesso all'hub IoT.

## <a name="protocol-gateway"></a>Gateway di protocollo
Un gateway di protocollo viene in genere distribuito nel cloud e fornisce servizi di traduzione dei protocolli per i dispositivi che si connettono all'[hub IoT](#iot-hub). Per altre informazioni, vedere [Che cos'è l'hub IoT di Azure?](about-iot-hub.md)

## <a name="quotas-and-throttling"></a>Quote e limitazioni
Esistono diverse [quote](iot-hub-devguide-quotas-throttling.md) che si applicano all'uso di [Hub IoT](#iot-hub). Molte quote sono diverse a seconda del livello dell'hub IoT. [Hub IoT](#iot-hub) applica anche [limitazioni](iot-hub-devguide-quotas-throttling.md) all'uso del servizio in fase di esecuzione.

## <a name="reported-configuration"></a>Configurazione segnalata
Nell'ambito di un [dispositivo gemello](iot-hub-devguide-device-twins.md), il termine configurazione segnalata indica il set completo di proprietà e metadati nel dispositivo gemello che deve essere segnalato al back-end della soluzione.

## <a name="reported-properties"></a>Proprietà segnalate
Nell'ambito di un [dispositivo gemello](iot-hub-devguide-device-twins.md), le proprietà segnalate sono un sottoparagrafo del dispositivo gemello usato con le [proprietà desiderate](#desired-properties) per sincronizzare la condizione o la configurazione del dispositivo. Le proprietà segnalate possono essere impostate solo dall'[app per dispositivo](#device-app) e possono essere lette e sottoposte a query da un'[app back-end](#back-end-app).

## <a name="resource-group"></a>Gruppo di risorse
[Azure Resource Manager](#azure-resource-manager) usa i gruppi di risorse per raggruppare le risorse correlate. È possibile usare un gruppo di risorse per eseguire operazioni su tutte le risorse del gruppo contemporaneamente.

## <a name="retry-policy"></a>Criteri di ripetizione
I criteri di ripetizione si usano per gestire gli [errori temporanei](https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx) quando ci si connette a un servizio cloud.

## <a name="routing-rules"></a>Regole di routing
Configurare le [regole di routing](iot-hub-devguide-messages-read-custom.md) nell'hub IoT per indirizzare i messaggi da dispositivo a cloud su un [endpoint predefinito](#built-in-endpoints) o su un [endpoint personalizzato](#custom-endpoints) per l'elaborazione da parte della soluzione di back-end in uso.

## <a name="sasl-plain"></a>SASL PLAIN
SASL PLAIN è un protocollo usato dal protocollo [AMQP](#advanced-message-queue-protocol) per trasferire i token di sicurezza.

## <a name="service-rest-api"></a>API REST del servizio
È possibile usare l'[API REST del servizio](https://docs.microsoft.com/rest/api/iothub/service) dalla soluzione di back-end per gestire i dispositivi gemelli. L'API consente di recuperare e aggiornare le proprietà del [dispositivo gemello](#device-twin), di invocare [metodi diretti](#direct-method) e pianificare i [processi](#job). In genere è consigliabile usare uno degli [SDK per servizi](#azure-iot-service-sdks) di livello superiore, come illustrato nelle esercitazioni su Hub IoT.

## <a name="shared-access-signature"></a>Firma di accesso condiviso
Le firme di accesso condiviso sono un meccanismo di autenticazione basato su hash sicuri SHA-256 o URI. L'autenticazione della firma di accesso condiviso presenta due componenti: i _criteri di accesso condiviso_ e una _firma di accesso condiviso_, che viene spesso chiamata token. Un dispositivo usa la firma di accesso condiviso per l'autenticazione con un hub IoT. Anche le [app back-end](#back-end-app) usano la firma di accesso condiviso per l'autenticazione con gli endpoint per il servizio in un hub IoT. In genere si include il token di firma di accesso condiviso nella [stringa di connessione](#connection-string) usata da un'app per stabilire una connessione a un hub IoT.

## <a name="shared-access-policy"></a>Criteri di accesso condiviso
Un criterio di accesso condiviso definisce le autorizzazioni concesse a chiunque abbia una [chiave primaria o secondaria](#primary-and-secondary-keys) valida associata a tale criterio. È possibile gestire i criteri di accesso condiviso e le chiavi per l'hub nel [portale](#azure-portal).

## <a name="simulated-device"></a>Dispositivo simulato
Per motivi di praticità, diverse esercitazioni su Hub IoT usano dispositivi simulati per consentire di eseguire esempi nel computer locale. Un [dispositivo fisico](#physical-device) è invece un vero e proprio dispositivo, ad esempio un Raspberry Pi, che si connette a un hub IoT.

## <a name="solution"></a>Soluzione
Una _soluzione_ può essere una soluzione di Visual Studio che include uno o più progetti, ma una _soluzione_ può anche essere una soluzione IoT che include elementi come dispositivi, [app per dispositivo](#device-app), un hub IoT, altri servizi di Azure e [app back-end](#back-end-app).

## <a name="subscription"></a>Sottoscrizione
In una sottoscrizione di Azure viene eseguita la fatturazione. Ogni risorsa di Azure creata o ogni servizio di Azure usato è associato a una singola sottoscrizione. Anche diverse quote si applicano al livello di una sottoscrizione.

## <a name="system-properties"></a>Proprietà di sistema
Nell'ambito di un [dispositivo gemello](iot-hub-devguide-device-twins.md), le proprietà di sistema sono di sola lettura e includono informazioni riguardanti l'uso dei dispositivi, ad esempio lo stato della connessione e l'ora dell'ultima attività.

## <a name="tags"></a>Tag
Nell'ambito di un [dispositivo gemello](iot-hub-devguide-device-twins.md), i tag sono metadati del dispositivo archiviati e recuperati dal back-end della soluzione in forma di documento JSON. I tag non sono visibili alle app in un dispositivo.

## <a name="telemetry"></a>Telemetria
I dispositivi raccolgono dati di telemetria, ad esempio la velocità del vento o la temperatura, e usano i [messaggi dei punti dati](#data-point-messages) per inviare i dati di telemetria a un hub IoT.

## <a name="token-service"></a>Servizio token
È possibile usare un servizio token per implementare un meccanismo di autenticazione per i dispositivi. Usa i [criteri di accesso condiviso](#shared-access-policy) dell'hub IoT con autorizzazioni **DeviceConnect** per creare token *basati sul dispositivo*. Questi token abilitano la connessione di un dispositivo all'hub IoT. Un dispositivo usa un meccanismo di autenticazione personalizzato per l'autenticazione con il servizio token. SE l'autenticazione del dispositivo riesce, il servizio token rilascia un token di firma di accesso condiviso per consentire al dispositivo da usare di accedere all'hub IoT.

## <a name="x509-client-certificate"></a>Certificato client X.509
Un dispositivo può usare un certificato X.509 per autenticarsi con [Hub IoT](#iot-hub). Usare un certificato X.509 è un'alternativa all'uso di un [token di firma di accesso condiviso](#shared-access-signature).
