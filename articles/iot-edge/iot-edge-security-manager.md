---
title: Gestione sicurezza Azure IoT Edge-Azure IoT Edge
description: Gestisce la strategia di sicurezza dei dispositivi IoT Edge e l'integrità dei servizi di sicurezza.
services: iot-edge
keywords: sicurezza, elemento protetto, enclave, TEE, IoT Edge
author: eustacea
manager: philmea
ms.author: eustacea
ms.date: 08/30/2019
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 871f2ec029379f37fc02bcd79847fa04091f0507
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666070"
---
# <a name="azure-iot-edge-security-manager"></a>Gestore sicurezza di Azure IoT Edge

Il gestore sicurezza di Azure IoT Edge è una tecnologia di sicurezza ben delimitata per la protezione del dispositivo IoT Edge e di tutti i relativi componenti tramite l'astrazione dell'hardware del processore di sicurezza. Si tratta del punto focale per la protezione avanzata e fornisce un punto di integrazione della tecnologia per OEM (Original Equipment Manufacturer).

![Gestore sicurezza di Azure IoT Edge](media/edge-security-manager/iot-edge-security-manager.png)

L'obiettivo del gestore sicurezza di IoT Edge è difendere l'integrità del dispositivo IoT Edge e di tutte le operazioni software inerenti. Il gestore della sicurezza esegue il trust dalla radice hardware sottostante dell'hardware di attendibilità (se disponibile) per avviare il runtime di IoT Edge e monitorare le operazioni in corso.  Il gestore sicurezza di IoT Edge è costituito da software che opera in combinazione con l'hardware del processore di sicurezza, se disponibile, per offrire le massime garanzie di sicurezza.  

Le responsabilità del gestore sicurezza di IoT Edge includono, in via esemplificativa:

* Eseguire il bootstrap sicuro e misurato del dispositivo Azure IoT Edge.
* Eseguire il provisioning delle identità del dispositivo e transizione del trust dove applicabile.
* Ospitare e proteggere i componenti del dispositivo di servizi cloud come il servizio Device Provisioning.
* Eseguire il provisioning in modo sicuro dei moduli di IoT Edge con identità univoche.
* Operare come gatekeeper della radice di attendibilità dei dispositivi hardware tramite servizi notary.
* Monitorare l'integrità delle operazioni di IoT Edge in fase di esecuzione.

Il gestore sicurezza di IoT Edge include tre componenti:

* Daemon di sicurezza di IoT Edge.
* Platform Abstraction Layer (PAL) del modulo di protezione hardware.
* Facoltativo ma consigliato, radice di attendibilità dell'hardware del processore o modulo di protezione hardware.

## <a name="the-iot-edge-security-daemon"></a>Daemon di sicurezza di IoT Edge

Il daemon di sicurezza IoT Edge è responsabile delle operazioni logiche di IoT Edge Security Manager. Rappresenta una parte significativa della base di calcolo attendibile del dispositivo IoT Edge. 

### <a name="design-principles"></a>Principi di progettazione

Il daemon di sicurezza di IoT Edge segue due principi fondamentali: ottimizzare l'integrità operativa e ridurre al minimo il software bloat e la varianza.

#### <a name="maximize-operational-integrity"></a>Ottimizzare l'integrità operativa

Il daemon di sicurezza IoT Edge opera con l'integrità più elevata possibile all'interno della funzionalità di difesa di una determinata radice di hardware attendibile. Con l'integrazione corretta, l'hardware della radice di attendibilità misura e monitora il daemon di sicurezza in modo statico e in fase di esecuzione per impedire la manomissione.

L'accesso fisico è sempre una minaccia per i dispositivi IoT. La radice di attendibilità dell'hardware svolge un ruolo importante nella difesa dell'integrità del daemon di sicurezza di IoT Edge.  La radice hardware del trust è presente in due tipi:

* Elementi sicuri per la protezione di informazioni riservate come segreti e chiavi di crittografia.
* Enclave sicuri per la protezione di segreti come le chiavi e di carichi di lavoro sensibili come la misurazione e la fatturazione.

Esistono due tipi di ambienti di esecuzione per utilizzare la radice hardware del trust:

* L'ambiente di esecuzione standard o avanzato (REE) che si basa sull'utilizzo di elementi protetti per proteggere le informazioni riservate.
* L'ambiente di esecuzione attendibile che si basa sull'utilizzo della tecnologia enclave protetta per proteggere le informazioni riservate e offrire protezione all'esecuzione del software.

Per i dispositivi che usano enclavi sicure come radice hardware di attendibilità, la logica sensibile all'interno IoT Edge daemon di sicurezza deve trovarsi all'interno dell'enclave.  Parti non sensibili del daemon di sicurezza possono essere esterne al TEE.  In ogni caso, il ODM (Original Design Manufacturer) e l'OEM (Original Equipment Manufacturer) devono estendere l'attendibilità dal modulo HSM per misurare e difendere l'integrità del daemon di sicurezza IoT Edge in fase di avvio e di Runtime.

#### <a name="minimize-bloat-and-churn"></a>Ridurre al minimo il software bloat e la varianza

Un altro principio di base per il daemon di sicurezza di IoT Edge è la riduzione della varianza.  Per il massimo livello di attendibilità, il daemon di sicurezza di IoT Edge può essere strettamente associato alla radice di attendibilità dell'hardware del dispositivo e operare come codice nativo.  È comune che questi tipi di realizzazioni aggiornino il software Daemon attraverso la radice hardware dei percorsi di aggiornamento protetti dell'attendibilità (in contrapposizione ai meccanismi di aggiornamento forniti dal sistema operativo), che può risultare difficile in alcuni scenari.  Sebbene sia consigliabile il rinnovo della sicurezza per i dispositivi Internet, i requisiti di aggiornamento eccessivi o i payload di aggiornamenti di grandi dimensioni possono espandere la superficie di minaccia in molti modi.  Gli esempi includono l'omissione di aggiornamenti per ottimizzare la disponibilità operativa o hardware della radice di attendibilità troppo vincolato per elaborare i payload di aggiornamento di grandi dimensioni.  Di conseguenza, la progettazione di IoT Edge daemon di sicurezza è concisa per limitare le dimensioni del footprint e del calcolo attendibile e per ridurre al minimo i requisiti di aggiornamento.

### <a name="architecture-of-iot-edge-security-daemon"></a>Architettura del daemon di sicurezza di IoT Edge

![Daemon di sicurezza di Azure IoT Edge](media/edge-security-manager/iot-edge-security-daemon.png)

Il daemon di sicurezza IoT Edge sfrutta qualsiasi radice hardware disponibile della tecnologia di attendibilità per la protezione avanzata.  Consente inoltre l'esecuzione di un'operazione Split-World tra un ambiente di esecuzione standard o avanzato (REE) e un ambiente di esecuzione attendibile quando le tecnologie hardware offrono ambienti di esecuzione attendibili. Le interfacce specifiche del ruolo consentono ai componenti principali di IoT Edge di garantire l'integrità del dispositivo IoT Edge e delle relative operazioni.

#### <a name="cloud-interface"></a>Interfaccia cloud

L'interfaccia cloud consente al daemon di sicurezza IoT Edge di accedere ai servizi cloud, ad esempio i complimenti del cloud alla sicurezza del dispositivo come il rinnovo della sicurezza.  Ad esempio, il daemon di sicurezza di IoT Edge usa attualmente questa interfaccia per accedere al [servizio Device provisioning](https://docs.microsoft.com/azure/iot-dps/) in hub Azure Azure per la gestione del ciclo di vita delle identità dei dispositivi.  

#### <a name="management-api"></a>API Gestione

IoT Edge daemon Security offre un'API di gestione, chiamata dall'agente IoT Edge durante la creazione, l'avvio, l'arresto e la rimozione di un modulo di IoT Edge. Il daemon di sicurezza archivia le "registrazioni" per tutti i moduli attivi. Queste registrazioni eseguono il mapping dell'identità di un modulo ad alcune proprietà del modulo. Alcuni esempi di queste proprietà sono l'identificatore di processo (PID) del processo in esecuzione nel contenitore o l'hash del contenuto del contenitore Docker.

Queste proprietà vengono usate dall'API del carico di lavoro (descritte di seguito) per verificare che il chiamante sia autorizzato a eseguire un'azione.

L'API di gestione è un'API con privilegi, che può essere chiamata solo dall'agente IoT Edge.  Poiché il daemon di sicurezza di IoT Edge esegue il bootstrap e avvia l'agente di IoT Edge, è in grado di creare una registrazione implicita per l'agente di IoT Edge, dopo aver attestato che l'agente di IoT Edge non è stato manomesso. Lo stesso processo di attestazione usato dall'API del carico di lavoro limita anche l'accesso all'API di gestione solo all'agente IoT Edge.

#### <a name="container-api"></a>API del contenitore

L'API del contenitore interagisce con il sistema di contenitori usato per la gestione dei moduli, ad esempio Moby o docker.

#### <a name="workload-api"></a>API del carico di lavoro

L'API del carico di lavoro è accessibile a tutti i moduli. Fornisce una prova di identità, come un token firmato con radice del modulo di protezione hardware o un certificato X509, e il bundle di trust corrispondente a un modulo. Il bundle di attendibilità contiene i certificati della CA per tutti gli altri server che i moduli devono considerare attendibili.

Il daemon di sicurezza IoT Edge usa un processo di attestazione per proteggere l'API. Quando un modulo chiama questa API, il daemon di sicurezza tenta di trovare una registrazione per l'identità. Se l'operazione riesce, usa le proprietà della registrazione per misurare il modulo. Se il risultato del processo di misurazione corrisponde alla registrazione, viene generata una nuova prova di identità. I certificati della CA (bundle di attendibilità) corrispondenti vengono restituiti al modulo.  Il modulo usa questo certificato per connettersi all'hub IoT o ad altri moduli oppure per avviare un server. Quando il token o il certificato firmato si avvicina alla scadenza, è responsabilità del modulo richiedere un nuovo certificato. 

### <a name="integration-and-maintenance"></a>Integrazione e manutenzione

Microsoft mantiene la base di codice principale per il [daemon di sicurezza di IoT Edge su GitHub](https://github.com/Azure/iotedge/tree/master/edgelet).

#### <a name="installation-and-updates"></a>Installazione e aggiornamenti

L'installazione e gli aggiornamenti del daemon di sicurezza di IoT Edge vengono gestiti tramite il sistema di gestione pacchetti del sistema operativo. I dispositivi IoT Edge con una radice di attendibilità dell'hardware devono fornire protezione avanzata aggiuntiva per l'integrità del daemon eseguendo la gestione del relativo ciclo di vita tramite i sistemi di gestione degli aggiornamenti e dell'avvio protetto. I creatori di dispositivi dovrebbero esplorare questi viali in base alle rispettive funzionalità del dispositivo.

#### <a name="versioning"></a>Controllo delle versioni

Il runtime di IoT Edge registra e restituisce la versione del daemon di sicurezza di IoT Edge. La versione è restituita tramite l'attributo *runtime.platform.version* della proprietà segnalata del modulo dell'agente di IoT Edge.

### <a name="hardware-security-module-platform-abstraction-layer-hsm-pal"></a>Platform Abstraction Layer (PAL) del modulo di protezione hardware

Il Platform Abstraction Layer del modulo di protezione hardware astrae tutto l'hardware della radice di attendibilità per isolare lo sviluppatore o l'utente di IoT Edge dalle relative complessità.  Include una combinazione di procedure di comunicazione Application Programming Interface (API) e Trans-Domain, ad esempio la comunicazione tra un ambiente di esecuzione standard e un'enclave sicura.  L'implementazione effettiva del Platform Abstraction Layer del modulo di protezione hardware dipende dallo specifico hardware sicuro in uso. La sua esistenza consente di usare praticamente qualsiasi hardware di processore sicuro.

## <a name="secure-silicon-root-of-trust-hardware"></a>Hardware della radice di attendibilità per il processore sicuro

Un processore sicuro è necessario per ancorare l'attendibilità nell'hardware del dispositivo IoT Edge.  I processori sicuri possono essere di versi tipi, come Trusted Platform Module (TPM), embedded Secure Element (eSE), ARM TrustZone, Intel SGX e tecnologie per processori sicuri personalizzate.  Si consiglia di usare una radice di trust Silicon sicura nei dispositivi, in base alle minacce associate all'accessibilità fisica dei dispositivi.

## <a name="iot-edge-security-manager-integration-and-maintenance"></a>Integrazione e manutenzione del gestore sicurezza di IoT Edge

Il gestore sicurezza di IoT Edge ha come obiettivo di identificare e isolare i componenti che difendono la sicurezza e l'integrità della piattaforma Azure IoT Edge per la protezione avanzata personalizzata. Le terze parti, come i produttori di dispositivi, devono fare uso delle funzionalità di sicurezza personalizzate disponibili con l'hardware dei relativi dispositivi.  Vedere la sezione Passaggi successivi per collegamenti a esempi su come implementare la protezione avanzata del gestore sicurezza di Azure IoT con TPM (Trusted Platform Module) nelle piattaforme Linux e Windows. Questi esempi usano TPM software o virtuali, ma si applicano direttamente a dispositivi TPM discreti.  

## <a name="next-steps"></a>Passaggi successivi

Leggere il blog [Securing the intelligent edge](https://azure.microsoft.com/blog/securing-the-intelligent-edge/) (Protezione dei dispositivi perimetrali intelligenti).

Creare ed effettuare il provisioning di un [dispositivo IoT Edge con un TPM virtuale in una macchina virtuale Linux](how-to-auto-provision-simulated-device-linux.md).

Creare ed effettuare il provisioning di un [dispositivo IoT Edge con un TPM simulato in Windows](how-to-auto-provision-simulated-device-windows.md).
